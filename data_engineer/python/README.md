# Задание программирование

## Python

Необходимо:
1. Получить данные по комментариям и постам с ресурса http://jsonplaceholder.typicode.com/
    * http://jsonplaceholder.typicode.com/posts
    * http://jsonplaceholder.typicode.com/comments

2. Посчитать среднее количество комментариев к посту каждого
   пользователя, результатом должен быть словарь формата:
    * user_id
    * average_comments_per_post
   
3. Результат вывести в stdout (например `print`).

## ____________________________________________________________________________
```python
import requests
import json
import pandas as pd


try:
    posts = requests.get('http://jsonplaceholder.typicode.com/posts')
    comments = requests.get('http://jsonplaceholder.typicode.com/comments')
except:
    raise Exception("Posts or Comments did/'t load.")

# Create dataframe for Posts and Comments
df_posts = pd.DataFrame.from_dict(posts.json())
df_comments = pd.DataFrame.from_dict(comments.json())

# Join Posts and Comments on Posts.id=Comments.postId
joined_dfs = pd.merge(df_posts, df_comments, left_on='id', right_on='postId', how='left')
# Group on userId, Posts.id field and counting Comments
grop_jd = joined_dfs.groupby(['userId', 'id_x'])['id_y'].size()
# Calculation average value comments per post for each users
avg_com_per_post = grop_jd.drop(columns=["id_x"]).groupby("userId").mean()
# Convert to dictionary
result = avg_com_per_post.to_dict()
print(result)
```
