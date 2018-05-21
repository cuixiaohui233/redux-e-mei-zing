### 规范化数据

在应用中避免不了要使用嵌套的数据，例如多级菜单，例如毒博的发帖评论功能，每一条微博都会有评论，但是评论下面还会有其他人评论。再比如我上次做的城市切换组件，影响页面展示的因素有很多，城市分类，品类分类，下拉刷新等，这个时候数据是相对复杂的，可能存在着嵌套数据，例如下面的微博评论数据：

    const blogPosts = [
    {
        id : "post1",
        author : {username : "user1", name : "User 1"},
        body : "......",
        comments : [
		{
			id : "comment1",
			author : {username : "user2", name : "User 2"},
			comment : ".....",
		},
		{
			id : "comment2",
			author : {username : "user3", name : "User 3"},
			comment : ".....",
		}
	]    
    },
    {
          id : "post2",
          author : {username : "user2", name : "User 2"},
          body : "......",
          comments : [
		{
			id : "comment3",
			author : {username : "user3", name : "User 3"},
			comment : ".....",
		},
		{
			id : "comment4",
			author : {username : "user1", name : "User 1"},
			comment : ".....",
		},
			id : "comment5",
			author : {username : "user3", name : "User 3"},
			comment : ".....",
		}
          ]    
      }
      // and repeat many times
    ]
    
此时数据很复杂,如果数据重复，那么更新数据难度变大，而且redux数据不可变的规则要求复制和更新state中的所有数据，而且在引用新的对象时，连接的UI组件会重新呈现，因此对嵌套数据的更新会导致完全不相干的UI组件也随之重新呈现，这样就不太好了。

### 设计一个归一化的数据结构

规范化的数据规范：

1.每一种数据类型就有一个自己的表，例如，评论人、ID、评论的内容等

2.每一个数据表应当将这个表的各个项目存储在一个对象中，其中项目的ID作为键，这个项目本身作为值。

3.任何对该项目的引用都应该存储该项目的ID来完成

4.用项目的ID的数组来表示排序

上面的微博数据写成规范化数据，以下：

		{
			posts : {
				byId : {
					"post1" : {
						id : "post1",
						author : "user1",
						body : "......",
						comments : ["comment1", "comment2"]    
					},
					"post2" : {
						id : "post2",
						author : "user2",
						body : "......",
						comments : ["comment3", "comment4", "comment5"]    
					}
				},
				allIds : ["post1", "post2"]
			},
			comments : {
				byId : {
					"comment1" : {
						id : "comment1",
						author : "user2",
						comment : ".....",
					},
					"comment2" : {
						id : "comment2",
						author : "user3",
						comment : ".....",
					},
					"comment3" : {
						id : "comment3",
						author : "user3",
						comment : ".....",
					},
					"comment4" : {
						id : "comment4",
						author : "user1",
						comment : ".....",
					},
					"comment5" : {
						id : "comment5",
						author : "user3",
						comment : ".....",
					},
				},
				allIds : ["comment1", "comment2", "comment3", "commment4", "comment5"]
			},
			users : {
				byId : {
					"user1" : {
						username : "user1",
						name : "User 1",
					}
					"user2" : {
						username : "user2",
						name : "User 2",
					}
					"user3" : {
						username : "user3",
						name : "User 3",
				}
				},
				allIds : ["user1", "user2", "user3"]
			}
		}

关于规范化的文档：https://github.com/reduxjs/redux/blob/master/docs/recipes/reducers/NormalizingStateShape.md

用于规范化数据的库：https://github.com/paularmstrong/normalizr

### 关于redux-saga,这个和redux-thunk差不多

作用：

具有可读和可预测的异步流程

连接两个分离的组件

api:

·put(): 在saga中dispatch一个action

·take(): 暂停我们的saga,直到有一个action触发

·select(): 获取State中的一部分数据，

·call(): 使用其余参数调用作为第一个参数传递的函数。

[相关连接-中文](https://redux-saga-in-chinese.js.org/)

### 关于类型检查 prop-types

作用：

记录或检测传给组件的值得类型，在类型不正确时给出警告。

安装

	npm install --save prop-types
	
用法：

	import React from 'react';
	import PropTypes from 'prop-types';

	class MyComponent extends React.Component {
	  render() {
	    // ... do things with the props
	  }
	}
	
	MyComponent.proptypes = {
		// ES5/ES6的类型们
		optionalArray: PropTypes.array,
		optionalBool: PropTypes.bool,
		optionalFunc: PropTypes.func,
		optionalNumber: PropTypes.number,
		optionalObject: PropTypes.object,
		optionalString: PropTypes.string,
		optionalSymbol: PropTypes.symbol,
		
		// 这个是...
		optionalNode: PropTypes.node,
		
		// 还可以是一个 react element
		optionalElement: PropTypes.element,
		
		//  亦可以检查某个类的实例，使用js中用 instanceOf 运算符
		optionalMessage: PropTypes.instanceOf(Message),
		
		// // 你可以通过将它作为枚举来确保你的prop被限制到特定的值。
		optionalEnum: React.PropTypes.oneOf(['News', 'Photos']),

		// 可以是许多类型之一的对象
		optionalUnion: React.PropTypes.oneOfType([
			React.PropTypes.string,
			React.PropTypes.number,
			React.PropTypes.instanceOf(Message)
		]),

		// 某种类型的数组
		optionalArrayOf: React.PropTypes.arrayOf(React.PropTypes.number),

		// 具有某种类型的属性值的对象
		optionalObjectOf: React.PropTypes.objectOf(React.PropTypes.number),

		// 采取特定样式的对象
		optionalObjectWithShape: React.PropTypes.shape({
			color: React.PropTypes.string,
			fontSize: React.PropTypes.number
		}),

		// 你可以用`isRequired`来连接到上面的任何一个类型，以确保如果没有提供props的话会显示一个警告。
		requiredFunc: React.PropTypes.func.isRequired,

		// 任何数据类型
		requiredAny: React.PropTypes.any.isRequired,

		// 您还可以指定自定义类型检查器。 如果检查失败，它应该返回一个Error对象。 不要`console.warn`或throw，因为这不会在`oneOfType`内工作。
		customProp: function(props, propName, componentName) {
		if (!/matchme/.test(props[propName])) {
				return new Error(
				'Invalid prop `' + propName + '` supplied to' +
				' `' + componentName + '`. Validation failed.'
				);
			}
		},

		// 您还可以为`arrayOf`和`objectOf`提供自定义类型检查器。 如果检查失败，它应该返回一个Error对象。 
		// 检查器将为数组或对象中的每个键调用验证函数。 
		// 检查器有两个参数，第一个参数是数组或对象本身，第二个是当前项的键。
		customArrayProp: React.PropTypes.arrayOf(function(propValue, key, componentName, location, propFullName) {
		if (!/matchme/.test(propValue[key])) {
				return new Error(
				'Invalid prop `' + propFullName + '` supplied to' +
				' `' + componentName + '`. Validation failed.'
				);
			}
		})
	}

嘻嘻嘻，全是康V抽C康抽V的，用的多的就是检查个类型，符合规范罢。

### 关于 redux 官方推荐的符合不可变数据标准的到底何方神圣这么大的头衔的 Immutable,酱酱~闪亮登场

文档贼多，[官方文档-有时抽风打不开](http://facebook.github.io/immutable-js/docs/#/)

突然困了，撒den力~

这个文档是贼拉多，突然性弃坑。

捡几个重要的搞一下：

immutable 就是一旦创建就不可以更改的数据，这正好符合redux数据不可变的规范。每当immutable对象进行修改的时候，就会返回一个新的immutable 对象，以此来保证数据的不可变性，[相关连接-炒鸡详细](https://github.com/camsong/blog/issues/3)

三种最重要的数据结构：

·Map(),键值对集合，无序索引集，对应js中的object.

·List(),有序索引集，相当于Array.

·Set(),无序且不可重复的列表.

上面的链接炒鸡详细了...

先挖几个坑...

### 关于redux中的select

### 如何使用 devTools

### 关于时间旅行的小栗子，Undo/Redo，Copy/Paste

### webpack 解决css全局变量问题到底怎么配置？

### 利用 shouldComponentUpdate 提高性能的方法



