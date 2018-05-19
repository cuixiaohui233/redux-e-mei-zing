### 规范化数据

在应用中避免不了要使用嵌套的数据，例如多级菜单，例如毒博的发帖评论功能，每一条微博都会有评论，但是评论下面还会有其他人评论。再比如我上次做的城市切换组

件，影响页面展示的因素有很多，城市分类，品类分类，下拉刷新等，这个时候数据是相对复杂的，可能存在着嵌套数据，例如下面的微博评论数据：

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
        {
          id : "comment5",
          author : {username : "user3", name : "User 3"},
          comment : ".....",
        }
          ]    
      }
      // and repeat many times
  ]
