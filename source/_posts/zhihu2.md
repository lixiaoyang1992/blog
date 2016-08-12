---
title: 用react-native开发知乎日报 2.闪屏页面
date: 2016-07-28 07:10:26
tags: react-native
category : javascript
---

本项目源码 [lixiaoyang1992/zhihu](https://github.com/lixiaoyang1992/zhihu)

今天完成了欢迎页面

{% asset_img ScreenShotWelcome.png %}

<!-- more -->

### api调用

欢迎页面需要获取启动图片，对应的api https://news-at.zhihu.com/api/4/start-image/1080*1776

建立js/api/zhihu.js

    export default {

        getStartImage(cb){

            fetch('https://news-at.zhihu.com/api/4/start-image/1080*1776')
                .then((response) => response.json())
                .then((responseJson) => {
                    cb(responseJson);
                })
                .catch((error) => {
                    console.error(error);
                });

        },

    }

在react-native中，只能使用fetch来做网络请求，使用promise来处理

其余的action部分正常调用即可

至此，api的调用方法写好了

### reducer  

和普通的react写法一样。

### WelcomeContainer

获取屏幕的高和宽，显示全屏图片的时候会用到。

    import {
        AppRegistry,
        StyleSheet,
        Text,
        View,
        Image,
        StatusBar
    } from 'react-native';

    var {height, width} = Dimensions.get('window');

    //android要把状态栏的高度减去才是全屏高度
    if (StatusBar.currentHeight)
        height -= StatusBar.currentHeight

react-native中使用js来添加样式，语法类似css稍作变化，如backgroundColor 取代 background-color。

    const styles = StyleSheet.create({
        fullScreen: {
            width: width,
            height: height,
        },
        bottomBar: {
            width: width,
            backgroundColor: '#000000',
        },
        transparentBackgroud: {
            backgroundColor: '#00000000',
        },
        textCentre: {
            textAlign: 'center',
        },
        textLargeWhite: {
            fontSize: 22,
            textAlign: 'left',
            color: 'white',
        },
        textNormalWhite: {
            fontSize: 16,
            textAlign: 'left',
            color: 'white',
        },
    });

view的写法和普通的react没有区别，只是要调用对应的component来绘制试图。

    class Zhihu extends Component {

        componentWillMount() {
            this.props.fetchStartImage();
        }

        render() {
            return (
                <View style={{
                    backgroundColor: '#000000',
                }}>

                    <Image source={{uri: this.props.data.img}}>

                        <View style={[styles.fullScreen, {
                            backgroundColor: '#00000033',
                        }]}>

                            <View style={{
                                position: 'absolute',
                                bottom: 0
                            }}>
                                <Text style={[styles.textCentre, styles.transparentBackgroud, {
                                    fontSize: 16,
                                    margin: 8,
                                    color: 'white',
                                }]}>
                                    {this.props.data.text}
                                </Text>

                                <View style={[styles.bottomBar, {
                                    padding: 20
                                }]}>
                                    <Text style={[styles.textLargeWhite, {}]}>
                                        知乎日报
                                    </Text>
                                    <Text style={[styles.textNormalWhite, {
                                        marginTop: 6,
                                    }]}>
                                        每日三次，每次七分钟
                                    </Text>

                                </View>
                            </View>
                        </View>


                    </Image>

                </View>
            );
        }
    }

    const mapStateToProps = (state) => {
        return {
            data: reducerData(state.welcomeReducer),
        }
    }

    export default connect(
        mapStateToProps,
        {fetchStartImage}
    )(Zhihu)
