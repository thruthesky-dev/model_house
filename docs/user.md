# 사용자


## 설치

## 초기화

현재 사용자가 로그인 및 정보 관리를 위해서는 `UserService.instance.init()` 을 통해서 초기화를 해야 한다. 그렇지 않으면 현재 사용자의 로그인 상태나 기타 기능을 사용 할 수 없다. 다만, 로그인 사용자가 아닌, 다른 사용자 정보를 참조할 때에는 초기화가 필요없다.

초기화가 되면, 사용자 문서의 `User` 모델 객체가 `UserService.instance.user` 에 보관되고 실시간 업데이트된다.




## 헬퍼 함수

`Iam` 클래스는 단순히 `UserService.instance` 를 짧게 쓰기 위한 alias 이다.


`my` 변수는 글로벌 변수로 `UserService.instance.user` 를 짧게 쓰기 위한 alias 이다.


## 개인정보

예를 들면, 이름, 생년월일, 성별 등은 민감한 개인 정보로 개발자의 선택에 따라 비공개로 할 수 있다. 비공개로 하기 위해서는 Firestore 의 `/users` 컬렉션에 정보를 보관하면 안되고, `/users-private` 컬렉션에 따로 정보를 보관해야 한다. 참고로 email 과 phoneNumber 필드는 `하우스`가 내부적으로 처리를 비공개 처리를 해 주며, 그 외의 필드는 직접 `/user-private` 컬렉션에 저장해야하며, 이와 관련된 라이브러리가 제공된다.

어떤 필드를 비 공개로 할지 초기화 과정에서 지정을 해 주면 된다.

에제

```dart
my.private.email;
my.private.phoneNumber;
my.private.set('field', 'value');
```



## 사용자 위젯

### MyDoc

로그인한 사용자의 정보를 액세스 할 수 있다. 즉, 나의 이름이나 나이, 성별 등의 정보를 읽고 수정 할 수 있다.





`UserService.instance.user` 는 DB 의 사용자 문서 값을 모델로 가지고 있는 변수이다. 짧게 `my` 로 쓸 수 있도록 해 놓았다. DB 의 값이 변경되면 실시간으로 이 변수의 값도 업데이트(sync)된다. 그래서 DB 에 값을 변경 한 다음, (약간 쉬었다) `my` 변수로 올바로 값이 저장되었는지 확인 할 수 있다. 예를 들면, form field 값 변경 즉시 저장하고, submit 버튼을 누르면 확인을 할 수 있다.

로그인한 사용자(나)의 정보를 참조하기 위해서는 `MyDoc` 를 사용하면 된다. 물론, `UserDoc` 를 사용해도 되지만, `MyDoc` 를 사용하는 것이 더 효과적이다. To reference the information of the logged-in user (yourself), you can use MyDoc. While using UserDoc is acceptable, using MyDoc is more effective.

Fireflutter 은 `UserService.instance.myDataChanges` 를 통해서 로그인 한 사용자의 데이터가 변경 될 때 마다, 자동으로 BehaviorSubject 인 `myDataChanges` 이벤트 시키는데 그 이벤트를 받아서 `MyDoc` 위젯이 동작한다. 그래서 추가적으로 DB 액세스를 하지 않아도 되는 것이다. Fireflutter uses UserService.instance.myDataChanges to automatically trigger the BehaviorSubject myDataChanges event whenever the data of the logged-in user changes. MyDoc widgets respond to this event, eliminating the need for additional DB access.

```dart
MyDoc(builder: (my) => Text("isAdmin: ${my?.isAdmin}"))
```

관리자이면 위젯을 표시하는 예. An example of displaying a widget if the user is an administrator:

```dart
MyDoc(builder: (my) => isAdmin ? Text('I am admin') : Text('I am not admin'))
```

If you are going to watch(listen) a value of a field, then you can use `MyDoc.field`.

```dart
MyDoc.field('${Field.blocks}/$uid', builder: (v) {
  return Text(v == null ? T.block.tr : T.unblock.tr);
})
```

### UserDoc