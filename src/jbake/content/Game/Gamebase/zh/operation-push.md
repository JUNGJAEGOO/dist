title=About
date=2013-09-24
type=page
status=published
big=TCGame
summary=TCGamebaseOpPush
nation=ko
~~~~~~
## Game > Gamebase > Operator Guide > Push

앱 이용자에게 푸시 알림을 전송합니다.
Gamebase 내부적으로 TOAST Cloud PUSH 상품을 사용하고 있습니다.

## Push

![image alt](http://static.toastoven.net/prod_gamebase/Operators_Guide/Console_Push1_1.1.png)

Gamebase를 통해 발송한 푸시 내역 및 발송 예정 내역을 조회할 수 있습니다.
발송 예정 내역에 있는 리스트들은 상세조회를 통해 예약 전송을 취소할 수 있습니다.

### Detail Push

![image alt](http://static.toastoven.net/prod_gamebase/Operators_Guide/Console_Push2_1.1.png) 전체 리스트에서 푸시를 선택하면 전송된 푸시의 상세 내역을 조회할 수 있습니다.
예약발송 상세화면에서는 푸시의 예상 발송시간을 확인할 수 있습니다. 예약발송은 현재는 취소만 가능하며 예약발송 메시지에 대한 수정기능은 추후 제공 될 예정입니다.

### Register Push

![image alt](http://static.toastoven.net/prod_gamebase/Operators_Guide/Console_Push3_1.1.png)

#### (1) 메시지 타입

> [INFO]
> 한국 정보통신망보호법 준수를 위하여 제공해 드리는 기능입니다.
> 푸시 발송시 정보성이 아닌 경우에는 '(광고)'로 메시지가 시작해야 하며 메시지에 연락처와 수신철회방법이 포함되어 전송되어야 합니다.

*   정보성 : 입력된 메시지만 푸시로 발송됩니다. 한국이 아닌 유저에게는 정보성으로 전송하시면 됩니다.
*   홍보성 : 입력된 메시지에 '(광고)' 머리말이 붙고 추가 입력된 연락처와 수신철회방법이 함께 발송됩니다. 전체 발송과 같은 광고성 푸시의 경우 국내에서는 정보통신망보호법 준수를 위하여 꼭 '홍보성'으로 발송하셔야 합니다.

> <font color="red">[WARNING]</font>
> 홍보성 선택 후 입력 메시지에 '(광고)'문구를 입력하시면 중복으로 입력되므로 주의하세요.

#### (2) 발송 대상

푸시 메시지를 발송할 대상을 선택합니다.

*   전체발송 : OS별로 선택이 가능합니다. 선택한 OS를 사용하는 사용자는 모두 푸시를 수신하게 됩니다.
*   특정회원 발송 : 특정회원에게만 푸시메시지를 전송하고자 할 때 선택합니다. 입력된 User ID로 푸시토큰을 등록한 단말기에 푸시를 발송합니다.
*   그룹 발송 : 메시지 발송을 원하는 User 리스트를 파일로 등록합니다. 그룹발송은 1회 최대 10,000명까지 가능합니다.

#### (3) 발송 타입

발송 주기를 선택합니다.

*   즉시 발송 : 등록 즉시 푸시 메시지를 발송합니다.
*   예약 발송 : 예약한 시간에 푸시 메시지를 발송합니다.

추후 반복발송(daily, weekly, monthly)과 Loacl Time 기준 발송등의 기능이 추가로 제공될 예정입니다.

#### (4) 대상 국가

푸시 메시지 발송할 국가를 선택합니다.

*   전체 국가 : 모든 사용자에게 발송
*   일부 국가 : 선택한 국가의 사용자에게만 발송.
    추가하고자 하는 국가코드를 입력하면 자동으로 완성되어 입력됩니다. 입력하고자 하는 국가코드가 없는 경우 관리자에게 문의바랍니다.

> [INFO]
> 국가 판단 기준
> 사용자의 **USIM 국가코드** 기준으로 판단하며 USIM이 없을 경우 **Device**에 설정되어 있는 국가를 기준으로 푸시메시지가 노출됩니다.

#### (5) 발송 메시지

사용자에게 노출할 푸시 메시지를 입력합니다.
다국어 지원을 위해 여러개의 언어로 등록 가능하며, 입력한 언어 이외의 언어를 사용하는 사용자에게는 기본 언어로 선택된 언어가 노출됩니다. 오른쪽의 **'+'**버튼을 클릭하면 언어 추가가 가능하며 원하는 언어가 없는 경우 담당자에게 연락을 주시면 새로운 언어 추가가 가능합니다.

> <font color="blue">[TIP]</font>
> 푸시 메시지를 발송했는데 단말기에 오지 않아요.
> 대부분의 경우 사용자의 PUSH Token을 등록하지 않은 경우입니다. 사용자의 PUSH Token이 등록되었는지 확인해주세요.
> - [LINK [AOS > Register PUSH]](../aos-push/#2-register-push)
> - [LINK [iOS > Register PUSH]](../ios-push/#2-register-push)
> - [LINK [Unity > Register PUSH]](../unity-push/#2-register-push)