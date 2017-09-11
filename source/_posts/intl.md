---
title: 前端国际化
date: 2017-09-10 23:58:34
tags: javascript
category : javascript
---

基于react的前端国际化，当然会用到[yahoo/react-intl](https://github.com/yahoo/react-intl).
<!-- more -->
大体的实现原理和react-redux的实现原理类似，最外层包一个Provider，利用getChildContext，将intlConfigPropTypes存起来，在FormattedMessage、FormattedNumber等组件或者调用injectIntl生成的高阶组件中使用，来完成国际化的。
使用过程也非常简单，和react-redux的使用方法非常类似。

### 创建资源文件

创建一个locales文件夹，里面放置不同语言的语言文件，如

    //en-US.js
    export default {
      edit: 'edit',
      view: 'view',
      delete: 'delete',
      'header.usercenter': 'UserCenter',
    }

    //zh-CN.js
    export default {
      edit: '编辑',
      view: '查看',
      delete: '删除',
      'header.usercenter': '用户中心',
    }

就是一个简单对象，我们使用key来作为取文字的唯一标识。

### 包裹IntlProvider

然后在项目的最外层组件上，包裹IntlProvider。
IntlProvider是支持多重包裹的，可以实现局部国际化的。

    import React, {Component} from 'react';
    import {IntlProvider, addLocaleData} from 'react-intl';
    import en from 'react-intl/locale-data/en';
    import zh from 'react-intl/locale-data/zh';
    import zh_CN from '../../locales/zh-CN.js';
    import en_US from '../../locales/en-US.js';

    addLocaleData([...en, ...zh]);

    export default class App extends Component {
      render() {
        const {language = 'zh'} = localStorage;

        let locale;
        let messages;
        if (language === 'zh') {
          locale = 'zh';
          messages = zh_CN;
        } else if (language === 'en') {
          locale = 'en';
          messages = en_US;
        }

        return (
          <IntlProvider
            locale={locale}
            messages={messages}
          >
            <App/>
          </IntlProvider>
        );
      }
    }

### 组件国际化

我们的文字在使用时一般会分为两种，一种事在jsx中直接使用，另一种是作为state或者props使用，这两种分别有不同的处理方式

第一种可以直接用一个国际化组件实现

    render() {
      return (
        <div>
          你好
        </div>
      );
    }

    import { FormattedMessage } from 'react-intl';
    ...
    render() {
      return (
        <div>
          <FormattedMessage
              id="hello"
              defaultMessage="你好"
            />
        </div>
      );
    }

第二种就需要高阶组件来实现了
    
    render() {
      return (
        <App value='你好' />
      );
    }

    import {injectIntl, defineMessages} from 'react-intl';

    const messages = defineMessages({
      hello: {
        id: 'hello',
        defaultMessage: '你好',
      },
    });

    @injectIntl
    export default class App extends Component {
      render() {
        return (
          <App value={this.props.intl.formatMessage(messages.hello)}/>
        );
      }
    }

### 源码解析

IntlProvider在context中放了一个intl。

    export default class IntlProvider extends Component {
      ......
      static propTypes = {
        ...intlConfigPropTypes,
        children: PropTypes.element.isRequired,
        initialNow: PropTypes.any,
      };
      ......
      getChildContext() {
        const config = this.getConfig();

        // Bind intl factories and current config to the format functions.
        const boundFormatFns = this.getBoundFormatFns(config, this.state);

        const {now, ...formatters} = this.state;

        return {
          intl: {
            ...config,
            ...boundFormatFns,
            formatters,
            now,
          },
        };
      }
    }

injectIntl，经典的高阶组件写法，把this.context.intl传递给了WrappedComponent，WrappedComponent就可以使用this.props.intl来调用了。

    export default function injectIntl(WrappedComponent, options = {}) {
      const {intlPropName = 'intl', withRef = false} = options;

      class InjectIntl extends Component {
        static displayName = `InjectIntl(${getDisplayName(WrappedComponent)})`;

        static contextTypes = {
          intl: intlShape,
        };

        ......

        render() {
          return (
            <WrappedComponent
              {...this.props}
              {...{[intlPropName]: this.context.intl}}
              ref={withRef ? 'wrappedInstance' : null}
            />
          );
        }
      }

      return InjectIntl;
    }

FormattedMessage是利用this.context.intl和传入的id来调用formatMessage得到对应的文字，返回一个Component。
其他的number，data，time等也类似。

    export default class FormattedMessage extends Component {
      render() {
        const {formatMessage, textComponent: Text} = this.context.intl;

        const {
          id,
          description,
          defaultMessage,
          values,
          tagName: Component = Text,
          children,
        } = this.props;

        ......

        let descriptor = {id, description, defaultMessage};
        let formattedMessage = formatMessage(descriptor, tokenizedValues || values);

        let nodes;

        let hasElements = elements && Object.keys(elements).length > 0;
        if (hasElements) {
          // Split the message into parts so the React Element values captured
          // above can be inserted back into the rendered message. This
          // approach allows messages to render with React Elements while
          // keeping React's virtual diffing working properly.
          nodes = formattedMessage
            .split(tokenDelimiter)
            .filter(part => !!part)
            .map(part => elements[part] || part);
        } else {
          nodes = [formattedMessage];
        }

        if (typeof children === 'function') {
          return children(...nodes);
        }

        // Needs to use `createElement()` instead of JSX, otherwise React will
        // warn about a missing `key` prop with rich-text message formatting.
        return createElement(Component, null, ...nodes);
      }
    }

具体的文字转换调用是intl-messageformat这个库。

### 总结

前端国际化，看起来很高大上，其实一点都不复杂，通用的做法都是把文字资源统一管理，在页面中用id来占位，根据语言使用不同的资源去填充。gui开发的国际化都是相似的，例如android也是这种做法，string会放到resources目录下来管理。或者设计一个特殊的注解之类的来区分不同语言的文字部分。