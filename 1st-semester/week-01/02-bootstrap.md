# Bootstrap  
## Bootstrap(부트스트랩)이란?  
- 프론트엔드 개발을 빠르고 쉽게 할 수 있는 프레임워크  
- 웹 페이지에서 많이 사용되는 요소를 거의 전부 제공하고 있고, 반응형 웹 디자인을 지원합니다.  
- 오픈소스이며 상업적으로 사용이 가능합니다.  

## 공식 사이트  
[https://getbootstrap.com/](https://getbootstrap.com/)  

## Bootstrap을 HTML에 적용하는 방법
`CDN`을 이용하면 됩니다.  
### CSS
```html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
```
위 코드를 `<head>` 태그 안, 다른 `stylesheet` 위쪽에 넣어주세요.  

### JS  
`Bootstrap`의 많은 component는 `JavaScript` 사용을 요구합니다.  
특히, `jQuery`, `Popper.js`, 그리고 `Bootstrap` 자체 플러그인이 필요합니다.  
```html
<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>
```
위 코드를 닫는 `</body>` 태그 바로 앞에 넣어주세요.  
`jQuery`가 첫번째로, 그 다음에 `Popper.js`, 그리고 `Bootstrap`의 JavaScript 플러그인이 와야 합니다.  
위 코드에서는 `jQuery`의 slim build를 사용하지만, full version 사용도 지원합니다.  
