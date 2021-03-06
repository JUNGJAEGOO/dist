title=About
date=2013-09-24
type=page
status=published
big=TCGame
summary=TCGamebaseIosAuth
nation=ko
~~~~~~
## Game > Gamebase > iOS Developer's Guide > Authentication

## Login

*   Gamebase 에서는 기본적으로 guest 로그인을 지원합니다.
*   guest 이외의 Provider에 로그인을 하기 위해서는 해당 Provider AuthAdapter가 필요합니다.
*   AuthAdapter 및 3rd-Party SDK에 대한 설정은 위에 있는 '외부 SDK 다운로드' 링크를 참고하시길 바랍니다.

로그인을 시도하려는 IDP별로, additionalInfo 파라미터를 입력해주어야 하는 경우가 있습니다.
AdditionalInfo에 대한 설명은 하단의 'Gamebase에서 지원 중인 IDP' 항목을 참고합니다.

### Import Header File

로그인을 구현하고자 하는 ViewController에 다음의 헤더 파일을 가져옵니다.

```
#import <Gamebase/Gamebase.h>

```

### Login Flow

*   많은 게임이 타이틀 화면에서 로그인을 구현합니다.
    *   앱을 설치 후 처음 실행했다면 타이틀 화면에서 어떤 IDP로 인증할지 선택할 수 있도록 하여 유저가 선택한 IDP로 인증합니다.
    *   로그인에 한번 성공한 이후에는 IDP 선택화면을 표시하지 않고 이전에 로그인에 성공했던 IDP 타입으로 인증합니다.
*   위에서 설명한 로직을 다음과 같은 순서로 구현할 수 있습니다.

#### 1\. 이전 로그인 타입 받아오기

*   **[TCGBGamebase lastLoggedInProvider]**를 호출합니다.
*   리턴된 값이 존재한다면 **'2\. 이전 로그인 타입으로 인증하기'**를 진행합니다.
*   리턴된 값이 없다면 유저에게 IDP를 선택하도록 한 다음 **'3\. 지정된 IDP로 인증하기'**를 진행합니다.

#### 2\. 이전 로그인 타입으로 인증하기

*   이전에 인증했던 기록이 있다면 ID/PW를 입력받지 않고 인증을 시도합니다.
*   **[TCGBGamebase loginForLastLoggedInProviderWithViewController:completion:]**를 호출합니다.

#### 2-1\. 인증이 성공한 경우

*   축하합니다! 인증에 성공하였습니다.
*   **[TCGBGamebase userID]**로 UserID를 획득하여 게임을 진행하세요.

#### 2-2\. 인증이 실패한 경우

*   네트워크 에러
    *   에러코드가 **TCGB_ERROR_SOCKET_ERROR(110)** 또는 **TCGB_ERROR_SOCKET_RESPONSE_TIMEOUT(101)** 인 경우, 일시적인 네트워크 문제로 인증이 실패한 것이므로 **[TCGBGamebase loginForLastLoggedInProviderWithViewController:completion:]** 를 다시 호출 하거나, 잠시 대기했다가 재시도 하도록 합니다.
*   이용 정지 유저
    *   에러 코드가 **TCGB_ERROR_AUTH_BANNED_MEMBER(3005)** 인 경우, 이용 정지 유저이므로 인증이 실패한 것입니다.
    *   **[TCGBGamebase banInfo]** 로 제재 정보를 확인하여 유저에게 게임을 플레이 할 수 없는 이유를 알려주시기 바랍니다.
    *   Gamebase 초기화시 **[TCGBConfiguration enablePopup:YES]** 및 **[TCGBConfiguration enableBanPopup:YES]**를 호출한다면 Gamebase 가 이용 정지에 관한 팝업을 자동으로 띄워줍니다.
*   그 외의 에러
    *   이전 로그인 타입으로 인증하기가 실패하였습니다. **'3\. 지정된 IDP로 인증하기'**를 진행합니다.

#### 3\. 지정된 IDP로 인증하기

*   IDP 타입을 직접 지정하여 인증을 시도합니다.
    *   인증 가능한 타입은 **TCGBConstants.h** 파일의 **TCGBAuthIDPs**에 선언되어 있습니다.
*   **[TCGBGamebase loginWithType:viewController:completion:]** API를 호출합니다.

#### 3-1\. 인증이 성공한 경우

*   축하합니다! 인증에 성공하였습니다.
*   **[TCGBGamebase userID]** 로 UserID를 획득하여 게임을 진행하세요.

#### 3-2\. 인증이 실패한 경우

*   네트워크 에러
    *   에러코드가 **TCGB_ERROR_SOCKET_ERROR(110)** 또는 **TCGB_ERROR_SOCKET_RESPONSE_TIMEOUT(101)** 인 경우, 일시적인 네트워크 문제로 인증이 실패한 것이므로 **[TCGBGamebase loginWithType:viewController:completion:]** 를 다시 호출 하거나, 잠시 대기했다가 재시도 하도록 합니다.
*   이용 정지 유저
    *   에러 코드가 **TCGB_ERROR_AUTH_BANNED_MEMBER(3005)** 인 경우, 이용 정지 유저이므로 인증이 실패한 것입니다.
    *   **[TCGBGamebase banInfo]** 로 제재 정보를 확인하여 유저에게 게임을 플레이 할 수 없는 이유를 알려주시기 바랍니다.
    *   Gamebase 초기화시 **[TCGBConfiguration enablePopup:YES]** 및 **[TCGBConfiguration enableBanPopup:YES]** 를 호출한다면 Gamebase 가 이용 정지에 관한 팝업을 자동으로 띄워줍니다.
*   그 외의 에러
    *   에러가 발생했다는 것을 유저에게 알리고, 유저가 인증 IDP 타입을 선택할 수 있는 상태(주로 타이틀 화면 또는 로그인 화면)로 되돌아갑니다.

### Login as the Latest Login IDP

특정 IDP에 대한 로그인 버튼을 클릭하였을 때, 다음 로그인 API를 구현합니다.
가장 최근에 로그인한 IDP로의 로그인을 시도합니다. 해당 로그인에 대한 토큰이 만료되었거나, 토큰에 대한 검증 등이 실패하였을 때, 실패를 리턴합니다.
이 때는 해당 IDP에 대한 로그인을 구현해주어야합니다.

```
- (void)automaticLogin {
    // Last Logged In Provider Name
    NSString *lastLoggedInProvider = [TCGBGamebase lastLoggedInProvider];

    [TCGBGamebase loginForLastLoggedInProviderWithViewController:self completion:^(TCGBAuthToken *authToken, TCGBError *error){
        if ([TCGBGamebase isSuccessWithError:error] == YES) {
            NSLog(@"Login is succeeded.");
        }
        else {
            if (error.code == TCGB_ERROR_SOCKET_ERROR || error.code == TCGB_ERROR_SOCKET_RESPONSE_TIMEOUT) {
                NSLog(@"Retry loginForLastLoggedInProviderWithViewController:completion: or Notify to user -\n\terror[%@]", [error description]);
            }
            else {
                NSLog(@"Try to login with loginWithType:viewController:completion:");

                [TCGBGamebase loginWithType:lastLoggedInProvider viewController:topViewController completion:^(TCGBAuthToken *authToken, TCGBError *error) {
                    if ([TCGBGamebase isSuccessWithError:error] == YES) {
                        NSLog(@"Login is succeeded.");
                    }
                    else {
                        NSLog(@"Login is failed.");
                    }
                }];
            }
        }
    }];
}

```

### Login with IDP

특정 IDP 로그인 호출을 위해서 **[TCGBGamebase loginWithType:viewController:completion:]** 메소드를 호출해줍니다.
Gamebase를 통하여 로그인을 처음 시도하거나, 로그인 정보(AccessToken)등이 만료되었다면, 이 API를 사용하여 로그인을 시도해야합니다.
로그인 결과로 **(TCGBError *)error** 객체를 이용해 성공 여부를 판단할 수 있습니다.
또한 **TCGBAuthToken** 객체를 이용하여 userId 등의 사용자 정보 및 토큰 정보를 얻을 수 있습니다.
로그인 성공 시, Gamebase AccessToken이 Local Storage에 저장되며 이후 loginForLastLoggedInProviderWithViewController:completion: 메서드를 사용할 때 이 저장된 AccessToken을 사용하게 됩니다.
하지만 IDP의 AccessToken은 각 IDP가 제공하는 SDK가 관리합니다.

몇몇 IDP의 로그인시에는 필수적으로 들어가야하는 정보가 있습니다.
예를 들어, facebook 로그인을 구현하기 위해서는 scope 등을 설정해주어야합니다.
이러한 필수 정보들을 설정해주기 위해서, **[TCGBGamebase loginWithType:additionalInfo:viewController:completion:]** API를 제공합니다.
파라미터 additionalInfo에 필수 정보들을 Dictionary 형태로 입력하시면 됩니다.
(파라미터 값이 nil일 때는, TOAST Cloud Console에 등록한 additionalInfo 값으로 채워집니다. 파라미터 값이 있을 때는 Console에 등록해놓은 값보다 우선시하여 값을 덮어쓰게 됩니다.)

> [INFO]
> 
> iOS에서 지원하는 IDP는 **TCGBConstants.h**의 TCGBAuthIDPs 영역의 **kTCGBAuthXXXXXX**로 정의되어 있습니다.

```
- (void)loginFacebookButtonClick {
    [TCGBGamebase loginWithType:kTCGBAuthPayco viewController:self completion:^(TCGBAuthToken *authToken, TCGBError *error) {
        if ([TCGBGamebase isSuccessWithError:error] == YES) {
            // To Login Succeeded
            NSString *userId = [authToken.tcgbMember.userId];
        } else {
            // To Login Failed
        }
    }];
}

```

#### Gamebase에서 지원 중인 IDP

#### Guest

#### Facebook

*   AdditionalInfo의 설정이 필요합니다.
    *   **TOAST Cloud Console > Gamebase > App > 인증 정보 > 추가 정보 & Callback URL**의 **추가 정보** 항목에 JSON String 형태의 정보를 설정해야합니다.
    *   Facebook의 경우, OAuth 인증 시도 시, Facebook으로 부터 요청할 정보의 종류를 설정해야 합니다.
    *   예제

```
{ "facebook_permission": [ "public_profile", "email", "user_friends"]}

```

*   Facebook SDK를 사용하기 위한 프로젝트 설정은 다음 링크를 참고합니다.
*   [LINK [Facebook Developer Guide]](https://developers.facebook.com/docs/ios/getting-started)

#### Payco

*   AdditionalInfo의 설정이 필요합니다.
    *   **TOAST Cloud Console > Gamebase > App > 인증 정보 > 추가 정보 & Callback URL**의 **추가 정보** 항목에 JSON String 형태의 정보를 설정해야합니다.
    *   Payco의 경우, PaycoSDK에서 요구하는 **service_code**와 **service_name**의 설정이 필요합니다.
    *   예제

```
{ "service_code": "HANGAME", "service_name": "Your Service Name" }

```

#### GameCenter

TOAST Cloud Console에서의 설정 외에 추가 설정은 없습니다.

### Login with Credential

게임에서 직접 ID Provider에서 제공하는 SDK로 먼저 인증을 하고 발급받은 AccessToken등을 이용하여, Gamebase 로그인을 할 수 있는 인터페이스 입니다.

*   Credential 파라미터의 설정방법

| keyname | a use | 값 종류 |
| --- | --- | --- |
| kTCGBAuthLoginWithCredentialProviderNameKeyname | IDP 타입을 설정 | facebook, payco, iosgamecenter |
| kTCGBAuthLoginWithCredentialAccessTokenKeyname | IDP 로그인 이후 받은 인증정보 (AccessToken)을 설정 |

> [TIP]
> 
> 게임 내에서 외부 서비스(Facebook 등)의 고유기능의 사용이 필요할 때 사용될 수 있습니다.

> <font color="red">[WARNING]</font>
> 
> 외부 SDK에서 지원요구하는 개발사항은 외부SDK의 API를 사용하여 구현해야하며, Gamebase에서는 지원하지 않습니다.

```
#import "TCGBConstants.h"

- (void)auth_login_with_credential {
    NSDictionary *credentialDic = @{ kTCGBAuthLoginWithCredentialProviderNameKeyname: @"facebook", kTCGBAuthLoginWithCredentialAccessTokenKeyname:@"여기에 facebook SDK에서 발급받은 Access Token을 입력하세요" };
    [TCGBGamebase loginWithCredential:credentialDic viewController:parentViewController completion:^(TCGBAuthToken *authToken, TCGBError *error) {
        NSLog([authToken description]);
    }];
}

```

## Logout

#### Import Header File

로그아웃을 구현하고자 하는 ViewController에 다음의 헤더 파일을 가져옵니다.

```
#import <Gamebase/Gamebase.h>

```

#### Logout API

로그인 된 IDP에서 로그아웃을 시도합니다. 주로 게임의 설정 화면에서 로그아웃 버튼을 두고 클릭시 실행되도록 구현하는 경우가 많습니다. 로그아웃이 성공하더라도, 유저 데이터는 유지됩니다. 로그아웃에 성공 하면 해당 IDP로 인증했던 기록을 제거하므로 다음 로그인시 ID/PW 입력창이 노출됩니다.

로그아웃 버튼을 클릭했을 때, 다음과 같이 로그아웃 API를 구현합니다.

```
[TCGBGamebase logoutWithViewController:self completion:^(TCGBError *error) {
    if ([TCGBGamebase isSuccessWithError:error] == YES) {
        // To Logout Succeeded
    } else {
        // To Logout Failed
    }
}];

```

## Withdraw

### Import Header File

탈퇴를 구현하고자 하는 ViewController에 다음의 헤더 파일을 가져옵니다.

```
#import <Gamebase/Gamebase.h>

```

### Widthdraw API

로그인 상태에서 탈퇴를 시도합니다.

*   탈퇴에 성공하면, 로그인 했던 IDP와 연동 되어 있던 유저 데이터는 삭제 됩니다.
*   해당 IDP로 다시 로그인 가능하고 새로운 유저 데이터를 생성합니다.
*   Gamebase 탈퇴를 의미하며, IDP 계정 탈퇴를 의미하지는 않습니다.
*   탈퇴 성공 시 IDP 로그아웃을 시도하게 됩니다.

탈퇴 버튼을 클릭했을 때, 다음과 같이 탈퇴 API를 구현합니다.

```
[TCGBGamebase withdrawWithViewController:self completion:^(TCGBError *error) {
    if ([TCGBGamebase isSuccessWithError:error] == YES) {
        // To Withdrawal Succeeded
    } else {
        // To Withdrawal Failed
    }
}];

```

## Mapping

많은 게임들이 하나의 계정에 여러 IDP를 연동(Mapping)할 수 있도록 하고 있습니다.
Gamebase의 Mapping API를 사용하여 기존에 로그인된 계정에 다른 IDP의 계정을 연동/해제시킬 수 있습니다.

이렇게 하나의 Gamebase UserID에 다양한 IDP 계정을 연동할 수 있습니다.
즉, 연동 중인 IDP 계정으로 로그인을 시도 한다면 항상 동일한 UserID로 로그인 됩니다.

주의할 점은, IDP 마다 하나의 계정씩만 연동이 가능합니다.
예시는 다음과 같습니다.

*   Gamebase UserID : 123bcabca
    *   Google ID : aa
    *   Facebook ID : bb
    *   AppleGameCenter ID : cc
    *   Payco ID : dd
*   Gamebase UserID : 456abcabc
    *   Google ID : ee
    *   Google ID : ff **-> 이미 Google ee 계정이 연동중이므로 Google계정을 추가로 연동할 수 없습니다.**

Mapping 에는 Mapping 추가/해제 API 2개가 있습니다.

### Import Header file into View Controller

Mapping을 구현하고자 하는 ViewController에 다음의 헤더 파일을 가져옵니다.

```
#import <Gamebase/Gamebase.h>

```

### Add Mapping API

특정 IDP에 로그인 된 상태에서 다른 IDP로 Mapping을 시도합니다.
Mapping을 하려는 IDP의 계정이 이미 다른 계정이 연동이 되어있다면,
**TCGB_ERROR_AUTH_ADD_MAPPING_ALREADY_MAPPED_TO_OTHER_MEMBER** 에러를 리턴합니다.

Mapping이 성공 하더라도 '현재 로그인 중인 IDP'가 바뀌지는 않습니다. 즉, Google 계정으로 로그인 한 후, Facebook 계정 Mapping 시도가 성공했다고 해서 '현재 로그인 중인 IDP'가 Google에서 Facebook으로 변경되지는 않습니다. Google 상태로 유지됩니다.
Mapping은 단순히 IDP 연동만 추가 해줍니다.

아래의 예시에서는 facebook에 대해서 Mapping을 시도하고 있습니다.

```
[TCGBGamebase addMappingWithType:@"facebook" viewController:parentViewController completion:^(TCGBAuthToken *authToken, TCGBError *error) {
    if ([TCGBGamebase isSuccessWithError:error] == YES) {
        // To Add Mapping Succeeded
    } else if (error.code == TCGB_ERROR_AUTH_ADD_MAPPING_ALREADY_MAPPED_TO_OTHER_MEMBER) {
        // User Already Mapped Facebook to other account.
    } else {
        // To Add Mapping Failed cause of the error
    }
}
}];

```

### Remove Mapping API

특정 IDP에 대한 연동을 해제합니다.
만약, 해제하고자 하는 IDP가 **유일한 IDP**라면, 실패를 리턴하게 됩니다.
연동 해제후에는 Gamebase 내부에서, 해당 IDP에 대한 로그아웃처리를 해줍니다.

```
[TCGBGamebase removeMappingWithType:@"facebook" viewController:self completion:^(TCGBError *error) {
    if ([TCGBGamebase isSuccessWithError:error] == YES) {
        // To Remove Mapping Succeeded
    } else {
        // To Remove Mapping Failed cause of the error
    }
}
}];

```

### Get IDP Mapping List

현재의 계정이 어떤 IDP들과 매핑되어 있는지 목록을 획득할 수 있습니다.

```
// Obtaining Names of Mapping IDPs
NSArray* authMappingList = [TCGBGamebase authMappingList];

```

## Gamebase User`s Informations

Gamebase를 통하여 인증절차를 진행 후, 앱을 제작할 때 필요한 정보를 획득할 수 있습니다.

### Get Authentication Information for Gamebase

Gamebase에서 발급한 인증 정보를 가져올 수 있습니다.

```
// Obtaining Gamebase UserID
NSString* gamebaseUserID = [TCGBGamebase userID];

// Obtaining Gamebase AccessToken
NSString* gamebaseAccessToken = [TCGBGamebase accessToken];

// Obtaining Last Logged In Provider
NSString* lastProviderName = [TCGBGamebase lastLoggedInProvider];

// Obtaining Ban Information
TCGBBanInfo* banInfo = [TCGBGamebase banInfo];

```

### Get Authentication Information for External IDP

외부 인증 SDK에서 AccessToken, UserId, Profile 등의 인증 정보를 가져올 수 있습니다.

```
// Example for obtaining ID Provider's Authentication Information

// Obtaining Facebook UserID
NSString *userID = [TCGBGamebase authProviderUserIDWithIDPCode:@"facebook"];

// Obtaining Facebook AccessToken
NSString *accessTokenOfIDP = [TCGBGamebase authProviderAccessTokenWithIDPCode:@"facebook"];

// Obtaining Facebook Profile
TCGBAuthProviderProfile *providerProfile = [TCGBGamebase authProviderProfileWithIDPCode:@"facebook"];

```

### Get Banned User Information

Gamebase Console에 제재된 유저로 등록될 경우, 로그인 시도 시, 아래와 같은 이용제한 정보 코드가 노출 될 수 있으며, **[TCGBGamebase banInfo]** 메서드를 이용하여 제재 정보를 확인할 수 있습니다.

*   TCGB_ERROR_AUTH_BANNED_MEMBER

## Error Handling

| Category | Error | Error Code | Notes |
| --- | --- | --- | --- |
| Auth | TCGB_ERROR_AUTH_USER_CANCELED | 3001 | 로그인이 취소되었습니다. |
| |TCGB_ERROR_AUTH_NOT_SUPPORTED_PROVIDER | 3002 | 지원하지 않는 인증 방식입니다. |
| |TCGB_ERROR_AUTH_NOT_EXIST_MEMBER | 3003 | 존재하지 않거나 탈퇴한 회원입니다. |
| |TCGB_ERROR_AUTH_INVALID_MEMBER | 3004 | 잘못된 회원에 대한 요청입니다. |
| |TCGB_ERROR_AUTH_BANNED_MEMBER | 3005 | 제재된 회원입니다. |
| |TCGB_ERROR_AUTH_EXTERNAL_LIBRARY_ERROR | 3009 | 외부 인증 라이브러리 에러입니다. |
| Auth (Login) | TCGB_ERROR_AUTH_TOKEN_LOGIN_FAILED | 3101 | 토큰 로그인에 실패하였습니다. |
| |TCGB_ERROR_AUTH_TOKEN_LOGIN_INVALID_TOKEN_INFO | 3102 | 토큰 정보가 유효하지 않습니다. |
| |TCGB_ERROR_AUTH_TOKEN_LOGIN_INVALID_LAST_LOGGED_IN_IDP | 3103 | 최근에 로그인한 IDP 정보가 없습니다. |
| IDP Login | TCGB_ERROR_AUTH_IDP_LOGIN_FAILED | 3201 | IDP 로그인에 실패하였습니다. |
| |TCGB_ERROR_AUTH_IDP_LOGIN_INVALID_IDP_INFO | 3202 | IDP 정보가 유효하지 않습니다. (Console에 해당 IDP 정보가 없습니다.) |
| Add Mapping | TCGB_ERROR_AUTH_ADD_MAPPING_FAILED | 3301 | 맵핑 추가에 실패하였습니다. |
| |TCGB_ERROR_AUTH_ADD_MAPPING_ALREADY_MAPPED_TO_OTHER_MEMBER | 3302 | 이미 다른 멤버에 맵핑되어있습니다. |
| |TCGB_ERROR_AUTH_ADD_MAPPING_ALREADY_HAS_SAME_IDP | 3303 | 이미 같은 IDP에 맵핑되어있습니다. |
| |TCGB_ERROR_AUTH_ADD_MAPPING_INVALID_IDP_INFO | 3304 | IDP 정보가 유효하지 않습니다. (Console에 해당 IDP 정보가 없습니다.) |
| Remove Mapping | TCGB_ERROR_AUTH_REMOVE_MAPPING_FAILED | 3401 | 맵핑 삭제에 실패하였습니다. |
| |TCGB_ERROR_AUTH_REMOVE_MAPPING_LAST_MAPPED_IDP | 3402 | 마지막에 맵핑된 IDP는 삭제할 수 없습니다. |
| |TCGB_ERROR_AUTH_REMOVE_MAPPING_LOGGED_IN_IDP | 3403 | 현재 로그인되어있는 IDP 입니다. |
| Logout | TCGB_ERROR_AUTH_LOGOUT_FAILED | 3501 | 로그아웃에 실패하였습니다. |
| Withdrawal | TCGB_ERROR_AUTH_WITHDRAW_FAILED | 3601 | 탈퇴에 실패하였습니다. |
| Not Playable | TCGB_ERROR_AUTH_NOT_PLAYABLE | 3701 | 플레이할 수 없는 상태입니다. (점검 또는 서비스 종료 등) |
| Auth(Unknown) | TCGB_ERROR_AUTH_UNKNOWN_ERROR | 3999 | 알수 없는 에러입니다. (정의 되지 않은 에러입니다.) |

*   전체 에러코드 참조 : [LINK [Entire Error Codes]](../error-codes#client-sdk)

**TCGB_ERROR_AUTH_EXTERNAL_LIBRARY_ERROR** _이 에러는 각 IDP의 SDK에서 발생한 에러입니다._ 에러 코드 확인은 다음과 같이 확인하실 수 있습니다.

*   IDP SDK의 에러코드는 각각의 Developer 페이지를 참고바랍니다.

```
TCGBError *tcgbError = error; // TCGBError object via callback
NSError *moduleError = [tcgbError.userInfo objectForKey:NSUnderlyingErrorKey]; // NSError object from external module
NSInteger moduleErrorCode = moduleError.code;
NSString *moduleErrorMessage = moduleError.message;

// If you use **description** method, you can get entire information of this object by JSON Format
NSLog(@"TCGBError: %@", [tcgbError description]);

```