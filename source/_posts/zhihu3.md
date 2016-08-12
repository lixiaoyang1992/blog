---
title: 用react-native开发知乎日报 3.首页列表
date: 2016-08-12 19:51:02
tags: react-native
category : javascript
---

本项目源码[lixiaoyang1992/zhihu](https://github.com/lixiaoyang1992/zhihu)

今天完成了首页列表

{% asset_img listview.png %}

<!-- more -->

### 列表

要用到ListView，有两个必不可少的参数：dataSource数据源，renderRow每一行的渲染函数。

    render() {
            return (
                <View style={styles.container}>
                    {
                        this.props.data.stories && this.props.data.stories.length > 0 ?
                            <ListView
                                dataSource={this.state.dataSource}
                                renderRow={this.renderListItem}
                            /> :
                            <Text>
                                加载中
                            </Text>
                    }
                </View>
            );
        }

获取数据的要从服务器后台获取数据。
每一次数据改变的时候都需要cloneWithRows。


    constructor(props) {
        super(props);
        const ds = new ListView.DataSource({rowHasChanged: (r1, r2) => r1 !== r2});
        this.state = {
            ds: ds,
            dataSource: ds.cloneWithRows([])
        };
    }

    componentWillMount() {
        this.props.fetchLatestNews();
    }

    componentWillReceiveProps(nextProps) {
        if (nextProps.data.stories) {
            let dataSource = this.state.ds.cloneWithRows(nextProps.data.stories);
            this.setState({
                dataSource
            });
        }
    }

行渲染，只需要把文字和图片显示出来即可。

    renderListItem(rowData) {
        return <View style={{
            width: width,
            backgroundColor: '#E9E9E9'
        }}>
            <View style={styles.row}>
                <Text style={styles.text}>
                    {rowData.title}
                </Text>
                {rowData.images ? <Image style={styles.thumb} source={{uri: rowData.images[0]}}/> : null}
            </View>
        </View>
    }

就这样，没什么难点。
