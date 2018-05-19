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
