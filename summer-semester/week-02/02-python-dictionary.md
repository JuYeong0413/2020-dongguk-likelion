# Python 자료형-딕셔너리  
`Python`에는 여러가지의 자료형이 있습니다. 정수, 실수, 불(bool), 문자열, 리스트, 튜플, 딕셔너리 등등...  
이번에는 `Django`를 사용하면서 여러분이 자주 접하게 될 딕셔너리(dictionary)라는 자료형에 대해 간단하게 알아볼거에요.  

딕셔너리는 `{key: value}`의 형태로 값을 저장합니다.  
`key`와 `value`의 쌍 여러 개가 중괄호(`{}`)로 둘러싸여 있는데요, 각각의 쌍은 쉼표(`,`)를 이용해 구분합니다.  

```python
>>> dic = {'name': 'dgulion', 'phone': '01000000000', 'age': 8}
```
`dic`이라는 이름의 딕셔너리 자료형을 하나 만들어 보았습니다.  
위 예시에서 `key`는 각각 `name`, `phone`, `age`이고, 각각의 `key`에 해당하는 `value`는 `dgulion`, `01000000000`, `8`이 됩니다.  

## 딕셔너리에 새로운 값 추가하기  
앞서 생성한 `dic`에 새로운 값을 추가하는 방법에 대해 알아봅시다.  
`변수이름[key] = value`의 형태를 이용하면 됩니다.  

`dic`에 `birthday`를 추가해 봅시다.  
```python
>>> dic['birthday'] = '0403'
```
을 실행해보고
```python
>>> print(dic)
```
으로 `dic`에 들어있는 값을 출력해보면  
```python
{'name': 'dgulion', 'phone': '01000000000', 'age': 8, 'birthday': '0403'}
```
이 출력되는 걸 확인할 수 있습니다.  

## 딕셔너리에 있는 값 수정하기  
수정하는 것은 새로운 값을 추가하는 방법과 유사합니다.  
`변수이름[수정할 key] = value`의 형태입니다.  

`dic`의 `name` 값을 `likelion`으로 수정해봅시다.  
```python
>>> dic['name'] = 'likelion'
```
를 실행해보고  
```python
>>> print(dic)
```
으로 `dic`에 들어있는 값을 출력해보면  
```python
{'name': 'likelion', 'phone': "01000000000', 'age': 8, 'birthday': '0403'}
```
이 출력되는 걸 확인할 수 있습니다.  

## 딕셔너리에 있는 값 삭제하기  
`del` 키워드를 사용해 딕셔너리에 있는 값을 삭제할 수 있습니다.  

`dic`의 `age`라는 값을 한번 삭제해봅시다.  
```python
>>> del dic['age']
```
를 실행해보고  
```python
>>> print(dic)
```
으로 `dic`에 들어있는 값을 출력해보면  
```python
{'name': 'likelion', 'phone': '01000000000', 'birthday': '0403'}
```
이 출력되는 걸 확인할 수 있습니다.  

## 딕셔너리 자료형을 사용할 때 주의해야 할 것  
1. `key`는 `value`를 찾기 위한 유일한 값이기 때문에 중복으로 사용할 수 없습니다.  
2. `key`에 리스트(list) 자료형은 사용할 수 없습니다.  
3. `value`에는 모든 자료형을 사용할 수 있습니다. 리스트 또한 `value`가 될 수 있습니다.  

## 딕셔너리 함수  
딕셔너리 자료형에 이용할 수 있는 여러가지 함수를 알아봅시다.  
### 1. `keys()`  
딕셔너리의 `key`만 모아 `dict_keys(['key1', 'key2', ...])`의 형태로 반환해줍니다.  
```python
>>> dic.keys()
dict_keys(['name', 'phone', 'birthday'])
```
### 2. `values()`  
딕셔너리의 `value`만 모아 `dict_values(['value1', 'value2', ...])의 형태로 반환해줍니다.  
```python
>>> dic.values()
dict_values(['likelion', '01000000000', '0403'])
```
### 3. `items()`  
딕셔너리의 `key`와 `value`를 튜플(tuple)로 묶어 `dict_items([('key1', 'value1'), ('key2', 'value2'), ...])`의 형태로 반환해줍니다.  
```python
>>> dic.items()
dict_items([('name', 'likelion'), ('phone', '01000000000'), ('birthday', '0403')])
```
### 4. `clear()`  
딕셔너리의 모든 값을 삭제하여 빈 딕셔너리로 만듭니다.  
```python
>>> dic.clear()
>>> print(dic)
{}
```
### 5. `get(key)`  
`key`에 해당하는 `value`를 반환해줍니다. `변수명[key]`와 동일한 역할을 합니다.  
```python
>>> dic.get('name')
'likelion'
```
```python
>>> dic['birthday']
'0403'
```
만약 `key`가 존재하지 않는다면 `None`을 반환합니다.  
이 경우에 `None`이 아닌 다른 값이 출력되기를 원하신다면 `변수명.get(key, 출력하고 싶은 값)`의 형태를 이용하면 됩니다.  
```python
>>> dic.get('age')
>>> dic.get('age', 'age 값이 없습니다.')
'age 값이 없습니다.'
```
### 6. `key in 변수`  
`key`값이 변수에 존재하면 `True`를, 존재하지 않는다면 `False`를 반환합니다.  
```python
>>> 'name' in dic
True
>>> 'age' in dic
False
```

