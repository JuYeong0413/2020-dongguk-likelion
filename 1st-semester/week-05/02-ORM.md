# ORM  
## ORM이란?  
객체(Object)의 관계(Relational)를 연결(Mapper)해주는 것을 의미합니다.  
객체 간의 관계를 바탕으로 SQL을 자동으로 생성해서 쿼리문 없이도 데이터베이스의 데이터를 쉽게 조작할 수 있습니다.  
우리가 만든 `Post` 모델에 한번 `ORM`을 사용해 봅시다.  

우선 `Python` shell에 들어갈게요.  
```bash
$ python manage.py shell
```
이제 `Django`의 interactive console에 들어오게 되었습니다.  

## 객체 생성하기  
객체를 생성하기 전에, 우리가 사용할 `Post` 모델을 가져와야 합니다.  
```bash
>>> from posts.models import Post
```
그럼 이제 새로운 게시글을 만들어 봅시다.  
```bash
>>> Post.objects.create(title="dongguk", content="likelion")
```
콘솔에 `<Post: Post object (1)>`이라고 나오는데요, `1`이라는 id 값을 가진 `Post` 객체라는 것을 의미합니다. 
id는 모델을 만들 때 자동적으로 생성되는 필드입니다.  
하나의 모델에 동일한 id를 가지는 객체는 없습니다. 고유함을 나타내는 기본키(primary key)라고 생각해 주세요.  

객체를 하나 더 만들어 봅시다.  
```bash
>>> Post.objects.create(title="hello", content="django")
```
이번에는 `<Post: Post object (2)>`라고 나오죠.  
`id`값은 설정해주지 않아도 알아서 부여됩니다.  

## 모든 객체 불러오기  
앞서 만든 게시글 객체를 모두 불러와볼게요.  
```bash
>>> Post.objects.all()
```
`Post` 테이블의 모든 객체를 불러오는 명령어입니다.  
그런데 이 명령어로는 `QuerySet`만 알 수 있고, 객체의 값까지는 알 수가 없습니다.  

각 객체의 값도 확인하려면  
```bash
>>> Post.objects.values()
```
를 이용하면 됩니다.  

## 객체의 속성 확인하기  
```bash
>>> p = Post.objects.get(id=1)
```
`get()`을 이용해 `id`값이 1인 객체를 `p`라는 변수에 담아주었습니다.  
```bash
>>> p
```
확인해 보면 `Post` 객체가 잘 담긴 것을 알 수 있죠.  
객체의 속성을 확인하기 위해선 `.`을 이용합니다.  
먼저 `p`의 제목을 확인해볼게요.  
```bash
>>> p.title
```
첫 번째 객체를 만들 때 제목으로 설정한 `dongguk`이 출력됩니다.  

이번에는 `p`의 내용을 확인해볼게요.  
```bash
>>> p.content
```
마찬가지로 내용이 잘 출력됩니다.  

`p`의 조회수는  
```bash
>>> p.view_count
```

`p`가 생성된 시간은  
```bash
>>> p.created_at
```
으로 확인할 수 있습니다.  

## 객체 수정하기  
앞서 `p`라는 변수에 저장해 둔 첫 번째 `Post` 객체의 제목을 한번 수정해보겠습니다.  
마찬가지로 `.`를 이용하면 됩니다.  
```bash
>>> p.title = 'dongguk university'
```
`p`의 `title` 속성을 새롭게 지정해 주었습니다.  
```bash
>>> p.title
```
제목을 다시 확인해보면 조금 전 입력한 `dongguk university`로 나오는 것을 확인할 수 있습니다.  
```bash
>>> Post.objects.values()
```
그런데 모든 객체의 값을 조회해 보면, 수정하기 전 값이 출력되는 것을 알 수 있습니다.  
바로 우리가 `p`의 제목을 수정했지만, 수정한 것을 저장하지 않았기 때문이에요.  
```bash
>>> p.save()
```
`save()`로 객체를 저장해주고, 다시 확인해보면 이번에는 제대로 출력됩니다.  

## 객체 삭제하기  
이번에는 객체를 삭제해 보도록 하겠습니다.  
```bash
>>> p.delete()
```
`delete()`로 객체를 삭제할 수 있습니다.  
```bash
>>> Post.objects.all()
```
모든 객체를 조회해 보면 우리가 조금 전에 삭제한 1번 객체는 없고, 2번 객체만 있는 것을 확인할 수 있습니다.  

이외에도 다양한 메서드가 존재하는데요, [documentation](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#methods-that-return-new-querysets)에서 확인이 가능합니다.  
