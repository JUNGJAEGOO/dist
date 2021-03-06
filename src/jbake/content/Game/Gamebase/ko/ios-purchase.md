title=About
date=2013-09-24
type=page
status=published
big=TCGame
summary=TCGamebaseIosPurchase
nation=ko
~~~~~~
## Game > Gamebase > iOS Developer's Guide > Purchase

## Purchase

### Settings

#### apple iTunes-Connect

1.  테스트용 앱 빌드를 업로드
2.  In-App Purchases 상품을 등록 및 승인
3.  Sandbox Tester 계정등록
4.  Detail Guide for iTunes-Connect : [LINK [Apple Guide]](https://help.apple.com/itunes-connect/developer/#/devb57be10e7)

#### Toast Cloud Console 등록

ToastCloud Console에서 설정해야하는 내용입니다.

1.  **'Gamebase > Purchase(IAP) > 앱'**에서 이용할 스토어를 등록합니다.
    *   스토어: **App Store**를 선택합니다.
2.  **'Gamebase > Purchase(IAP) > 아이템'**에서 아이템을 등록합니다.
    *   스토어: **App Store**를 선택합니다.
    *   스토어 아이템 ID: iTunes-Connect에 등록한 Product ID를 입력합니다.
3.  아이템을 설정했다면, 저장을 누릅니다.

#### Xcode Project 설정

1.  **'Targets > Capabilities > In-App Purchase'**를 **ON**으로 설정합니다.
2.  **'Targets > General > Identity'**의 Bundle Identifier, Version, Build 의 값을 알맞게 설정합니다.

#### Import Header File

구매 API를 구현하고자 하는 ViewController에 다음의 헤더 파일을 가져옵니다.

```
#import <Gamebase/Gamebase.h>

```

### Purchase Flow

아이템 구매는 다음과 같은 순서로 구현하시기 바랍니다.

1.  **requestPurchaseWithItemSeq:viewController:completion:** 를 호출하여 결제를 시도합니다.
2.  결제가 성공하였다면 **requestItemListOfNotConsumedWithCompletion:** 를 호출하여 미소비 결제내역을 확인합니다.
3.  리턴된 미소비 결제내역 리스트에 값이 존재한다면 게임 클라이언트가 게임 서버에 Consume 을 요청합니다.
4.  게임 서버는 IAP서버에 서버 API를 통해 Consume 을 요청합니다.
5.  IAP서버에서 Consume이 성공하였다면 게임 서버가 게임 클라이언트에 아이템을 지급합니다.

스토어 결제는 성공하였으나 에러가 발생하여 정상 종료되지 못하는 경우가 있습니다. 로그인 완료 후 다음 두 API를 각각 호출하여 재처리 로직을 구현하시기 바랍니다.

1.  미처리 아이템 배송 요청
    *   로그인이 성공하면 **requestItemListOfNotConsumedWithCompletion:** 를 호출하여 미소비 결제내역을 확인합니다.
    *   리턴된 미소비 결제내역 리스트에 값이 존재한다면 게임 클라이언트가 게임 서버에 Consume을 요청하여 아이템 지급 처리를 합니다.
2.  결제 오류 재처리 시도
    *   로그인이 성공하면 **requestRetryTransactionWithCompletion:** 을 호출하여 미처리 내역에 대한 자동 재처리를 시도합니다.
    *   리턴된 successList 에 값이 존재한다면 게임 클라이언트가 게임 서버에 Consume을 요청하여 아이템 지급 처리를 합니다.
    *   리턴된 failList 에 값이 존재한다면 해당 값을 게임 서버나 Log&Crash 등을 통해 전송하여 데이터를 확보하고, 빌링 개발팀에 재처리 실패 원인을 문의합니다.

### Purchase Item

구매하고자 하는 아이템의 itemSeq를 이용해 다음의 API를 호출하여 구매요청을 합니다.

```
- (void)purchasingItem:(long)itemSeq {
    [TCGBPurchase requestPurchaseWithItemSeq:itemSeq viewController:self completion:^(TCGBPurchasableReceipt *purchasableReceipt, TCGBError *error) {
        if ([TCGBGamebase isSuccessWithError:error] == YES) {
            // To Purchase Item Succeeded
        } else if (error.code == TCGB_ERROR_PURCHASE_USER_CANCELED) {
            // User Canceled Purchasing Item
        } else if (error) {
            // To Purchase Item Failed cause of the error
        }
    }];
}

```

### Get a List of Purchasable Items

아이템 목록을 조회하기 위하여 다음의 API를 호출합니다. 콜백으로 리턴되는 Array 안에는 각 아이템들에 대한 정보가 담겨 있습니다.

```
- (void)viewDidLoad {
    [TCGBPurchase requestItemListPurchasableWithCompletion:^(NSArray *purchasableItemArray, TCGBError *error) {
        if (error != nil) {
            // To Request Item List Failed cause of the error
            return;
        }

        NSMutableArray *itemArrayMutable = [[NSMutableArray alloc] init];
        [purchasableItemArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            TCGBPurchasableItem *item = (TCGBPurchasableItem *)obj;

            [itemArrayMutable addObject:item];
        }];
    }];
}

```

### Get a List of Non-Consumed Items

아이템을 구매는 하였지만, 정상적으로 아이템이 소비(배송, 지급)되었지 않은 **미소비 결제내역**을 요청합니다.
해당 내역을 받은 경우에는 게임서버(아이템 서버)에 요청을 하여, 아이템을 배송(지급)하도록 처리하여야합니다.

*   다음 두가지 상황에서 호출해 주세요.
    1.  결제 성공 후 아이템 Consume 처리 전 최종 확인을 위하여 호출.
    2.  로그인 성공 후 Consume하지 못한 아이템이 남아있지는 않은지 확인 하기 위하여 호출.

```
- (void)viewDidLoad {
    [TCGBPurchase requestItemListOfNotConsumedWithCompletion:^(NSArray<TCGBPurchasableReceipt *> *purchasableReceiptArray, TCGBError *error) {
        if (error != nil) {
            // To Requesting Non-consumed Item List Failed cause of the error
            return;
        }

        // Should Deal With This Non-consumed Items.
        // Send this item list to the game(item) server for consuming item
    }];
}

```

### Reprocess Failed Purchase Transaction

스토어 결제는 정상적으로 이루어졌지만, ToastCloud IAP 서버 검증 실패 등으로 인해 정상적으로 결제가 이뤄지지 않은 경우에, 해당 API를 이용하여 재처리를 시도합니다.
최종적으로 결제가 성공한 내역을 바탕으로, 아이템 배송(지급)등의 API를 호출하여 처리를 해주어야합니다.

```
- (void)viewDidLoad {
    [TCGBPurchase requestRetryTransactionWithCompletion:^(TCGBPurchasableRetryTransactionResult *transactionResult, TCGBError *error) {
        if (error != nil) {
            // To Retry Failed Purchasing Transaction Failed cause of the error
            return;
        }

        // Should Deal With This Retry Transaction Result.
        // You may send result to your gameserver and add item to user.
    }];
}

```

### Error Handling

| Error | Error Code | Notes |
| --- | --- | --- |
| TCGB_ERROR_NOT_SUPPORTED | 10 | GamebaseAdapter가 포함되지 않았습니다.<br>Error 객체의 도메인이 "TCGB.Gamebase.TCGBPurchase" 인 경우, PurchaseAdapter의 존재 유무를 확인해주시길 바랍니다. |
| TCGB_ERROR_PURCHASE_NOT_INITIALIZED | 4001 | Gamebase PurchaseAdapter가 초기화되지 않았습니다. |
| TCGB_ERROR_PURCHASE_USER_CANCELED | 4002 | 구매가 취소되었습니다. |
| TCGB_ERROR_PURCHASE_NOT_FINISHED_PREVIOUS_PURCHASING | 4003 | 이전 구매가 완료되지 않았습니다. |
| TCGB_ERROR_PURCHASE_NOT_ENOUGH_CASH | 4004 | 해당 스토어의 캐쉬가 부족하여 결제할 수 없습니다. |
| TCGB_ERROR_PURCHASE_NOT_SUPPORTED_MARKET | 4010 | 지원하지 않는 스토어입니다. iOS의 지원가능한 스토어는 "AS" 입니다. |
| TCGB_ERROR_PURCHASE_EXTERNAL_LIBRARY_ERROR | 4201 | IAP 라이브러리 에러입니다.
error.message 를 확인하세요. |
| TCGB_ERROR_PURCHASE_UNKNOWN_ERROR | 4999 | 정의되지 않은 구매 에러입니다.
전체 로그를 Gamebase 개발팀에 전달하여 에러상황을 문의해 주세요. |

*   전체 에러코드 참조 : [LINK [Entire Error Codes]](../error-codes#client-sdk)

**TCGB_ERROR_PURCHASE_EXTERNAL_LIBRARY_ERROR**

*   이 에러는 IAP 모듈에서 발생한 에러입니다.
*   에러 코드 확인은 다음과 같이 확인하실 수 있습니다.

```
TCGBError *tcgbError = error; // TCGBError object via callback
NSError *moduleError = [tcgbError.userInfo objectForKey:NSUnderlyingErrorKey]; // NSError object from external module
NSInteger moduleErrorCode = moduleError.code;
NSString *moduleErrorMessage = moduleError.message;

// If you use **description** method, you can get entire information of this object by JSON Format
NSLog(@"TCGBError: %@", [tcgbError description]);

```

*   IAP 에러코드는 다음 문서를 참고하시기 바랍니다.
*   [LINK [IAP > Error Code Guide > Client API 에러 타입]](http://docs.cloud.toast.com/ko/Common/IAP/ko/Error%20Code/#client-api)