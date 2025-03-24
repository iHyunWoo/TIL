# RN Navigation - Stack Navigator

[Stack Navigator 공식 문서](https://reactnavigation.org/docs/stack-navigator)
기준 버전 React Navigation 7.x
## Installation & Setup
- React Navigation 공통 setup
```
npm install @react-navigation/native
yarn add @react-navigation/native

// RN CLI
npm install react-native-screens react-native-safe-area-context
yarn add react-native-screens react-native-safe-area-context

// Expo
npx expo install react-native-screens react-native-safe-area-context

npx pod-install ios

```
- Android setup
```kotlin
// MainActivity.kt
import android.os.Bundle;

override fun onCreate(savedInstanceState: Bundle?) {  
	super.onCreate(null)  
}
```
- Stack Navigator setup
```
npm install @react-navigation/stack
yarn add @react-navigation/stack

// RN CLI
npm install react-native-gesture-handler
yarn add react-native-gesture-handler

// Expo
npx expo install react-native-gesture-handler

// RN CLI
npm install @react-native-masked-view/masked-view
yarn add @react-native-masked-view/masked-view

// Expo
npx expo install @react-native-masked-view/masked-view

npx pod-install ios
```
- gesture-handler setup
```js
// index.js 최상단에 추가
import 'react-native-gesture-handler';
```
> Web도 지원을 하는 프로젝트라면 문서에서 설정 방법 참고 [공식 문서](https://reactnavigation.org/docs/stack-navigator)



## Basic Usage
1. App.tsx
	- NavigationContainer로 감싸기
```ts
function App(): React.JSX.Element {
  return (
    <NavigationContainer>
      <RootNavigator />
    </NavigationContainer>
  );
}
```
2. RootNavigator.tsx
	- 로그인 되어있다면 MainScreen을 아니라면 AuthStackNavigator를 보여주도록
```ts
function RootNavigator() {  
  const isLogin = false;  
  
  return <>{isLogin ? <MainScreen/> : <AuthStackNavigator/>}</>  
}
```
3. navigation.ts
	- AuthStackNavigator에서 쓸 화면 목록을 따로 상수로 만들기
```ts
const authNavigations = {  
  AUTH_HOME: 'AuthHome',  
  LOGIN: 'Login',  
  SIGNUP: 'Signup',  
} as const;
```
4. AuthStackNavigator.tsx
	- AuthStack에서 사용할 파라미터를 정의
	- createStackNavigator로 Stack을 생성
```ts
export type AuthStackParamList = {  
  [authNavigations.AUTH_HOME]: undefined;  
  [authNavigations.LOGIN]: {email: string, password: string};  
  [authNavigations.SIGNUP]: undefined;  
};  
  
const Stack = createStackNavigator<AuthStackParamList>();  
  
function AuthStackNavigator() {  
  return (  
    <Stack.Navigator initialRouteName={authNavigations.AUTH_HOME}>  
      <Stack.Screen        
        name={authNavigations.AUTH_HOME}  
        component={AuthHomeScreen}  
      />  
      <Stack.Screen name={authNavigations.LOGIN} component={LoginScreen} />  
      <Stack.Screen name={authNavigations.SIGNUP} component={SignupScreen} />  
    </Stack.Navigator>  
  );  
}
```
5. AuthHomeScreen.tsx
	- AuthHomeScreen에서 props로 받을 route와 navigation을 type으로 만듦
```ts
type AuthHomeScreenProps = StackScreenProps<  
  AuthStackParamList,  
  typeof authNavigations.AUTH_HOME  


function AuthHomeScreen({route, navigation}: AuthHomeScreenProps) {  
  return (  
    <SafeAreaView>  
      <Pressable        
	      onPress={() => {  
		    navigation.navigate(authNavigations.LOGIN, {  
			  email: 'test@test.com',  
			  password: '123',  
		    });  
          }}>
// ... 이후 생략
```
6. LoginScreen.tsx
	- route의 params에서 가져온 값을 꺼내옴
```ts
type LoginScreenProps = StackScreenProps<  
  AuthStackParamList,  
  typeof authNavigations.LOGIN  
>  
  
function LoginScreen({route, navigation}: LoginScreenProps) {  
  const { email, password } = route.params;  
  return (  
    <SafeAreaView>  
      <Text>{email}</Text>  
      <Text>{password}</Text>  
      <Pressable onPress={() => {navigation.pop()}}>
```


## Route Object
- 현재 route에 대한 정보를 담고 있음
- `key` - 스크린의 유니크한 키. 스크린으로 네비게이션 할 때 자동 생성
- `name` - 스크린 네임
- `path` - (Optional) 딥링크 사용 시 스크린을 연 path
- `params` - 네비게이션 시 전달한 param
- ex)
```
key: "Login-TV9ljv9bsbExJ4JMRxCGG"
name: "Login"
params: {email: 'test@test.com', password: '123'}
path: undefined
```

## Navigation Object
- navigation과 관련된 함수들을 담고 있음
- `navigate` - 주어진 화면으로 네비게이션
- `goBack` - 뒤로 가기(스택에선 pop)
- `reset` - 주어진 상태로 네비게이션 초기화
- `preload` - 네비게이션 하기 전에 새로운 화면을 미리 로드
- `setParams` - 현재 라우트의 파람에 새로운 파람을 합침
- `dispatch` - navigation state 업데이트
	- ex)` e.preventDefault()`로 동작을 막고 확인 클릭시 `navigation.dispatch(e.data.action)`으로  디스패치
- `setOptions` - 스크린 옵션 업데이트
- `isFocused` - 현재 화면이 활성화 됐는지
- `canGoBack` - 뒤로 가기가 가능한지
- `getState` - 네비게이션 상태 가져오기
- `getParent` - 부모가 있다면 부모의 네비게이션 객체 가져오기
- `addListener` - 스크린에서 발생하는 이벤트 리스너 등록
	- Core Event
		- focus: 화면이 활성화 될 때
		- blur: 화면이 비활성화 될 때
		- state: 네비게이션 상태가 변경될 때
		- beforeRemove: 화면이 스택에서 제거될 때(뒤로가기 시 경고 하는 경우 등 사용)
	- Stack Navigation Event
		- gestureStart
		- gestureEnd
		- gestureCancel
		- transitionStart
		- transitionEnd
- `removeListener` - 스크린 이벤트 구독 해제
- Stack Navgiation Func
	- `replace` - 현재 화면으로 새로운 화면으로 교체
	- `push` - 새로운 화면 push
	- `pop` - 뒤로가기
	- `popTo` - 스택의 특정 화면으로 뒤로가기
	- `popToTop` - 스택의 제일 처음으로 뒤로가기


## Advanced Usage
1. CompositeNavigationProp
	- 여러개의 Navigation에 묶여 있는 경우가 있을 수 있음
	- 예를 들어, Drawer과 현재 화면에서의 StackNavigation에 묶여있는 경우
```ts
type MapHomeScreenProps = {
  navigation: CompositeNavigationProp<
    StackNavigationProp<MapStackParamList>,
    DrawerNavigationProp<MainDrawerParamList>
  >
}

function MapHomeScreen({navigation}: MapHomeScreenProps) {
```
2. 뒤로가기 시 alert 띄우기
```ts
useEffect(() => {  
  const beforeRemoveListener = navigation.addListener('beforeRemove', (e) => {  
    e.preventDefault(); // 기본 동작 막기  
  
    Alert.alert(  
      '뒤로 가기',  
      '정말로 나가시겠습니까?',  
      [  
        { text: '취소', style: 'cancel', onPress: () => {} },  
        { text: '확인', onPress: () => {navigation.dispatch(e.data.action)} },  
      ]  
    );  
  });  
  return () => beforeRemoveListener();  
}, [navigation]);
```