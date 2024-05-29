# Covi iOS SDK 연동 가이드

## 목차
- [연동 프로세스](#연동-프로세스)

- [요구 사양](#요구-사양)

- [COVI SDK 설정](#covi-sdk-설정)

- [COVI 광고 삽입](#covi-광고-삽입)
    - [COVI SDK 사용](#covi-sdk-사용)
    - [COVI 플레이어 생성](#covi-플레이어-생성)
	    - [Code(programmatic) 방식으로 COVI 플레이어 삽입](#codeprogrammatic-방식으로-covi-플레이어-생성)
	    - [Storyboard 방식으로 COVI 플레이어 삽입](#storyboard-방식으로-covi-플레이어-생성)
	- [옵션 설정](#옵션-설정)
        - [옵션 설명](#옵션-설명)
    - [이벤트 핸들러 설정](#이벤트-핸들러-설정)
    - [COVI 플레이어 로드](#covi-플레이어-로드)

- [오류코드](#오류코드)

<br/>

# 연동 프로세스
<img width="770" alt="image" src="https://cnp-file.dev.covi.co.kr/docs/covi-ad-sdk/iOS/process.webp">

<br/>

# 요구 사양
- iOS version: 12.0 이상

<br/>

# COVI SDK 설정
- COVI SDK를 적용할 프로젝트의 `TARGETS` -> `General` -> `Frameworks, Libraries, and Embedded Content` 항목에 진입합니다.
- `covisdk.framework`를 추가합니다.
- 필요 시 covisdk.framework에서 사용하는 SwiftyXMLParser, Alamofire, SDWebImage, Player framework를 추가합니다.

<br/>

# COVI 광고 삽입

## COVI SDK 사용
- COVI 플레이어를 삽입할 ViewController가 위치한 파일 상단에 covisdk를 import 합니다.
``` swift
import covisdk
```

<br/>

## COVI 플레이어 생성

### `Code(programmatic)` 방식으로 COVI 플레이어 생성

- COVI 플레이어를 삽입할 View의 ViewController에 coviPlayer 인스턴스를 적당한 크기로 생성합니다.
``` swift
let coviPlayer = CoviPlayer(frame: CGRect(x: 0, y: 0, width: 400, height: 200))
```

<br/>


### `Storyboard` 방식으로 COVI 플레이어 생성
1) .Storyboard에 UIView를 생성합니다.

2) 생성한 UIView의 Custom Class 항목에서 Class를 CoviPlayer로 설정합니다.

3) GUI로 COVI 플레이어를 삽입할 View의 하위에 CoviPlayer를 위치 시킵니다.

4) GUI로 CoviPlayer를 COVI 플레이어를 삽입할 View의 ViewController와 연결합니다.

<br/>

## 옵션 설정
- COVI 광고 요청을 위한 옵션 값을 할당합니다.

``` swift
func setCoviOpts () {

    // 필수
    coviPlayer.type = "dev"
    coviPlayer.pcode = "매체사 코드"
    coviPlayer.category = "매체사 카테고리"
        
    // 권장
    coviPlayer.age = 20
    coviPlayer.gender = "M"
    coviPlayer.idfa = "test-idfa-value"
        
    // 선택
    coviPlayer.playType = .auto_play
}
```

<br/>

### 옵션 설명

| 이름     | 타입    | 설명                                                         | 필수여부              |
| -------- | ------- | ------------------------------------------------------------ | --------------------- |
| type     | String  | 개발버전, 상용버전 구분<br><font color="dodgerblue">dev</font>: 개발버전 (기본값)<br><font color="orange">prod</font>: 상용버전<br> * 개발버전에서 테스트 완료 후 승인 절차를 걸쳐 승인 완료 후 사용해야 합니다. | <strong>필수</strong> |
| pcode    | String  | 매체사 코드 | <strong>필수</strong> |
| category | String  | 매체사 카테고리                      | <strong>필수</strong> |
| age      | Int | 연령                       | 권장                  |
| gender   | String  | 성별                       | 권장                  |
| idfa     | String  | iOS 광고 ID            | 권장                  |
| playtype | Enum  | 비디오 재생 방법<br> .auto_play: 자동 재생 (기본값)<br>.click_to_play: 클릭 재생 | 선택                  |

<br/>

## 이벤트 핸들러 설정
- COVI 플레이어의 이벤트 핸들러 함수를 설정합니다.
``` swift
func coviEventHandler(event:String) {
    switch (event) {
    case "VAST_LOAD_SUCCESS":
        print("[covi] VAST_LOAD_SUCCESS")

    case "VAST_LOAD_FAIL":
        print("[covi] VAST_LOAD_FAIL")

    case "SDK_NO_ADS":
        print("[covi] no ads");
            
    case "PLAYER_VIDEO_PLAY":
            
    case "PLAYER_VIDEO_PAUSE":
            
    case "PLAYER_VIDEO_REPLAY":
                
    case "PLAYER_VIDEO_ENDED":
        break;
            
    default:
        break;
    }
}
```

<br/>

## COVI 플레이어 로드
- `viewDidLoad`에서 COVI 옵션을 설정한 뒤 `viewDidLoad`나 `viewDidAppear`에서 COVI 플레이어를 로드합니다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    // COVI 옵션 설정
    setCoviOpts()
    
    // COVI 플레이어 로드
    coviPlayer.loadContent(coviEventHandler: coviEventHandler)
}
```

<br>

```swift
override func viewDidAppear() {
    super.viewDidAppear()

    // COVI 플레이어 로드
    coviPlayer.loadContent(coviEventHandler: coviEventHandler)
}
```

<br/>

# 오류코드
		
Code|Message|해결방법
---|---|---
E001|type 값이 정의되지 않았습니다.|type 정보 추가
E002|pcode 값이 정의되지 않았습니다.|pcode 정보 추가
E003|매체사 정보가 올바르지 않습니다. 담당자에게 문의해 보세요.|담당자에게 문의해서 매체사 코드 확인 후 pcode에 적용
E004|category 값이 정의되지 않았습니다.|매체사 category 정보 추가
E100|현재 진행중인 광고가 없습니다.|담당자에게 문의
---
