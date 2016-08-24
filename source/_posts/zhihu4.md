---
title: 用react-native开发知乎日报 4.点击事件
date: 2016-08-23 09:21:03
tags: react-native
category : javascript
---

本项目源码 [lixiaoyang1992/zhihu](https://github.com/lixiaoyang1992/zhihu)

列表中的项，点击后可以跳转到对应的详情页。点击事件用到TouchableHighlight。

    renderListItem(rowData) {
            return <View style={{
                width: width,
                backgroundColor: '#E9E9E9'
            }}>
                <TouchableHighlight onPress={()=> {
                    this.onPress(rowData);
                }}>
                    <View style={styles.row}>
                        <Text style={styles.text}>
                            {rowData.title}
                        </Text>
                        {rowData.images ? <Image style={styles.thumb} source={{uri: rowData.images[0]}}/> : null}
                    </View>

                </TouchableHighlight>
            </View>
        }

    onPress(rowData) {
        this.props.navigator.push({
            name: 'detail',
            id: rowData.id
        });
    }

onPress方法中使用页面导航。
