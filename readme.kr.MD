![ARKit + CoreLocation](https://github.com/ProjectDent/ARKit-CoreLocation/blob/master/arkit.png)

주) 영어에서 번역한 것이며, 그 내용이 최신이 아닌 경우도 있습니다. 최신 정보는 원본 영어 버전을 참조하십시오.

**ARKit**: 카메라와 모션 데이터를 사용하여 이동할 때 정밀하게 근처의 세계를 비춥니다.

**CoreLocation**: Wifi 및 GPS를 사용하여 낮은 정확도로 위치를 측정합니다.

**ARKit + CoreLocation**: AR의 높은 정확도와 GPS 스케일을 결합합니다.

![Points of interest demo](https://github.com/ProjectDent/ARKit-CoreLocation/blob/master/giphy-1.gif) ![Navigation demo](https://github.com/ProjectDent/ARKit- CoreLocation/blob/master/giphy-2.gif)

이러한 기술의 조합은 매우 큰 잠재력을 가지고 있으며 다양한 분야에 걸쳐 많은 잠재적인 응용 프로그램이 있습니다. 이 라이브러리에는 두 가지 중요한 기능이 있습니다.

- 현실 세계의 좌표를 사용하여 AR의 세계에 아이템을 넣을 수 있는 기능
- AR 세계를 통한 움직임 지식을 결합한 최근의 위치 데이터를 이용한 극적으로 개선된 위치 정밀도

개선된 위치 정확도는 현재 "실험적" 단계에 있지만 가장 중요한 요소가 될 수 있습니다.

해야 할 일이 많고 다른 분야에서도 그렇기 때문에 Github의 Issue에서 우리가 하는 것보다 이 프로젝트는 개방적인 커뮤니티에서 제공하는 것이 가장 좋습니다.
그래서 이 라이브러리와 개선, 자신들의 일에 대해 논의를 하기 위해 누구나 참가할 수 있는 Slack 그룹을 열려고 합니다.

** [Slack 커뮤니티에 가입하십시오]

## 요구 사항
ARKit은 iOS 11이 필요하며 다음 단말기가 지원합니다.
- iPhone 6S and upwards
- iPhone SE
- iPad (2017)
- All iPad Pro models

iOS 11은 Apple’s Developer 웹사이트에서 다운로드할 수 있습니다.

## 사용 방법
이 라이브러리는 ARKit과 CoreLocation framework를 포함하고 있으며, [Demo 1] (https://twitter.com/AndrewProjDent/status/886916872683343872)와 비슷한 데모 앱과 유사합니다.

[True North calibration 섹션을 반드시 읽으십시오. ](#true-north-calibration)

### Build 설정 :

 ``bash
swift build \
        -Xswiftc "-sdk" -Xswiftc "`xcrun --sdk iphonesimulator --show-sdk-path`"\
        -Xswiftc "-target" -Xswiftc "x86_64-apple-ios12.1-simulator"
``

### Swift Package Manager에서 설정

### CocoaPods에서 설정
1. Podfile에 다음을 추가:

`pod 'ARCL'`

2. 터미널에서 프로젝트 폴더로 이동하고 다음을 입력하십시오.

`pod update`

`pod install`

3. `NSCameraUsageDescription`과 `NSLocationWhenInUseUsageDescription`을 간단한 설명을 넣어 plist에 추가 (예는 데모를 참조하십시오)

### 수동으로 설정
1.`ARKit + CoreLocation / Source`의 모든 파일을 프로젝트에 추가하십시오.
2. ARKit, SceneKit, CoreLocation, MapKit 가져오기
3. `NSCameraUsageDescription`과 `NSLocationWhenInUseUsageDescription`을 간단한 설명을 넣어 plist에 추가 (예는 데모를 참조하십시오)

### 클릭 시작 가이드
예를 들어 런던의 Canary Wharf와 같은 빌딩 위에 핀을 놓으려면 ARCL이 구축하는 메인 클래스를 사용합니다. - `SceneLocationView`

먼저 ARCL과 CoreLocation을 가져옵니다. 그런 다음 SceneLocationView를 속성으로 선언합니다.

``swift
import ARCL
import CoreLocation

class ViewController: UIViewController {
var sceneLocationView = SceneLocationView()
}
``

초점이 맞을 때는 언제라도 `sceneLocationView.run()` 를 호출해, 다른 뷰로 이동하거나 앱을 닫거나 하는 등으로 중단하는 경우는 `sceneLocationView.pause()` 를 호출해야 합니다.

``swift
override func viewDidLoad() {
super.viewDidLoad()

sceneLocationView.run()
view.addSubview(sceneLocationView)
}

override func viewDidLayoutSubviews() {
super.viewDidLayoutSubviews()

sceneLocationView.frame=view.bounds
}
``

`run()`을 호출하면 좌표를 추가할 수 있습니다. ARCL은 `LocationNode`라는 3D 장면의 객체로 현실 세계의 위치를 ​​가지며 3D 세계 안에 적절하게 표시 할 수있게하는 몇 가지 다른 속성이있는 클래스가 있습니다.
`LocationNode` 는 SceneKit 의 `SCNNode` 의 서브 클래스로, 한층 더 서브 클래스화할 수 있습니다. 예를 들어, `LocationAnnotationNode`라는 서브 클래스를 사용합니다. 이것은 3D 세계에 2차원 이미지를 표시하는 데 사용되지만 항상 사용됩니다.

``swift
let coordinate = CLLocationCoordinate2D(latitude: 51.504571, longitude: -0.019717)
let location = CLLocation(coordinate: coordinate, altitude: 300)
let image = UIImage(named: "pin")!

let annotationNode = LocationAnnotationNode(location: location, image: image)
``

UIView를 사용하여 `LocationAnnotationNode`를 초기화하는 것도 가능합니다.
내부에서는 UIImage 로 변환되기 때문에, 내용을 동적으로 갱신할 수 없습니다만, UIImage 를 이용하는 것보다 간단하게 복잡한 레이아웃을 지정할 수 있습니다.

``swift
let coordinate = CLLocationCoordinate2D(latitude: 51.504571, longitude: -0.019717)
let location = CLLocation(coordinate: coordinate, altitude: 300)
let view = UIView() // or a custom UIView subclass

let annotationNode = LocationAnnotationNode(location: location, view: view)
``

또, CALayer 를 사용해 `LocationAnnotationNode`를 초기화하는 것도 가능합니다.
콘텐츠를 동적으로 업데이트하고 싶은 경우에 이용하면 좋을 것입니다.

``swift
let coordinate = CLLocationCoordinate2D(latitude: 51.504571, longitude: -0.019717)
let layer = CALayer() // or a custom CALayer subclass

let annotationNode = LocationAnnotationNode(location: location, layer: layer)
``

기본적으로 설치한 이미지는 항상 주어진 크기로 표시되어야 합니다. 예를 들어, 100x100 이미지를 주었다면 화면에 100x100으로 표시됩니다.
멀리 있는 주석 노드는 항상 가까이 되는 것과 같은 크기로 보입니다.
만약 거리에 따라 축소와 확대를 하는 것이 좋다면,LocationAnnotationNode의 `scaleRelativeToDistance`를 `true`로 설정할 수 있습니다.

``swift
sceneLocationView.addLocationNodeWithConfirmedLocation(locationNode: annotationNode)
``

장면에 위치 노드를 추가하는 방법에는 두 가지가 있습니다. 그것은 `addLocationNodeWithConfirmedLocation`과 `addLocationNodeForCurrentPosition`을 사용하는 방법입니다. 터미널과 같은 위치에 위치 노드를 3D 세계에 배치하고 좌표를 제공합니다.
sceneLocationView의 frame을 설정하면 Canary Wharf 위에 핀이 떠있는 것을 볼 수 있습니다.

노드가 `sceneLocationView`에 의해 터치되었을 때, 통지를 받기 위해서는 ViewController 클래스의 `LNTouchDelegate`를 따라야합니다. `locationNodeTouched(node:AnnotationNode)`는 화면에 닿은 노드에 대한 액세스를 제공합니다. `AnnotationNode`는 SCNNode의 서브 클래스이며, `image:UIImage? `와` view:UIView? `의 두 가지 추가 속성이 있습니다. 이러한 property는 어느쪽이나 `LocationAnnotationNode`가 어떻게 초기화되었는지에 근거해 정해집니다 (UIImage 또는 UIView 어느 쪽의 constructor 을 사용했는지에 의해 정해집니다).

``swift
class ViewController: UIViewController, LNTouchDelegate {
    override func viewDidLoad() {
        super.viewDidLoad()
        //...
        self.sceneLocationView.locationNodeTouchDelegate = self
        //...
    }
    func locationNodeTouched(node: AnnotationNode) {
        // Do stuffs with the node instance

        // node could have either node.view or node.image
        if let nodeView = node.view {
            // Do stuffs with the nodeView
            // ...
        }
        if let nodeImage=node.image{
            // Do stuffs with the nodeImage
            // ...
        }
    }
}
``

## 추가 기능
라이브러리와 데모는 설정을 위한 추가 기능을 제공합니다. 반드시 나열할 수 있도록 완전히 문서화되어 있습니다.

SceneLocationView는 ARSCNView의 서브 클래스입니다. 다른 방법으로 완전히 ARSCNView를 사용할 수 있도록 해주는 반면, 다른 클래스에 delegate를 설정하면 안됩니다. delegate의 기능이 필요하면 서브 클래스의`SceneLocationView`를 사용하십시오.

## True North calibration

나는이 문제가 다양한 AR 기술에 의해 극복 될 것이라고 확신합니다. 그것은 공동으로 혜택을 받을 수 있다고 생각하는 한 영역입니다.

현재 이것을 개선하기 위해, 나는 북점을 조정할 수있는 몇 가지 기능을 라이브러리에 추가했습니다.
`sceneLocationView.useTrueNorth`를 `false`로 설정하여 이것을 사용합니다. 그런 다음 터미널을 북쪽의 일반적인 방향으로 향하면 합리적으로 접근 할 수 있습니다. `UseTrueNorth`를 true(기본값)로 설정하면 북쪽을 잘 감지할 수 있게 되면서 조정을 계속합니다.

데모 앱 내에서는 `adjustNorthByTappingSidesOfScreen`이라는 사용할 수없는 속성이 있습니다. 이것은 위의 기능에 액세스 할 수 있지만, 한 번 사용할 수있게하면 화면의 왼쪽과 오른쪽을 탭하여 장면의 진행 방향을 조정할 수 있습니다.

자신의 위치로부터 직접 True North가 되어 있는 근처의 표시를 정확하게 해, 좌표를 사용해 거기에 물체를 설치해, 그 물체가 장소에 나란히 표시될 때까지 씬을 조정한다 `moveSceneHeading` 사용합니다.

CoreLocation은 1~15초마다 어디서나 위치의 갱신을 전달해 주지만, 정밀도는 150m에서 4m까지 변화합니다. 부정확한 위치를 읽으러 가기 전에 가끔 4m나 8m처럼 더 정확한 위치를 얻을 수 있습니다. 동시에 AR은 모션 및 카메라 데이터를 사용하여 AR 세계에서 지도를 만듭니다.

사용자는 4m까지 정확한 위치를 읽을 수 있습니다. 그런 다음 사용자는 10m 북쪽으로 걸어 65m까지 정확하게 읽은 다른 위치를 얻습니다. 이 65m에서의 정확한 판독은 CoreLication이 제공할 수 있는 최고의 것입니다만, 4m를 읽을 때에 AR내에서의 유저의 위치를 ​​파악해, 거기로부터 AR를 10m북쪽으로 걸으면, 약 4m의 정밀도로 새로운 좌표를 주는 데이터로 변환할 수 있습니다. 이것은 100m까지 정확합니다.

[자세한 정보는 위키에 있습니다. ] (https://github.com/ProjectDent/ARKit-CoreLocation/wiki/Current-Location-Accuracy).

### 문제
이것은 실험적이라고 말했지만, 현재는 사용자가 AR로 걸을 때 ARKit이 때때로 혼란스러워지고 사용자의 위치가 부정확해질 수 있습니다. 이 문제는 "오일러 각도"또는 터미널 방향 정보에 영향을 미치는 것처럼 보입니다. 그러므로, 조금 거리를 가고 나면, 당신이 다른 방향으로 걷고 있다고 ARKit은 생각할지도 모릅니다.

애플이 그 중 ARKit을 개선하는 한편, 이러한 문제를 피하기 위해 우리가 할 수있는 개선이 있다고 생각합니다. 예를 들어 문제가 발생한 것을 인식하고 수정하는 등. 이것은 위치 데이터를 예상한 위치와 비교하여, 예상한 범위를 넘어 이동했는지 어떤지를 판정하는 것으로 실현할 수 있습니다.

### 위치의 알고리즘의 개선
사용자의 위치를 ​​결정하기 위해 추가 최적화를 할 수 있습니다.

예를 들면, 최근의 위치 데이터를 보고, 그 후의 유저의 이동을 근거로 각 데이터 포인트를 변환해, 유저가 그러한 위치를 보다 엄격하게 판정하기 위해서 그 데이터 포인트의 중첩을 사용하는 테크닉입니다.

[자세한 정보는 위키에 있습니다. ] (https://github.com/ProjectDent/ARKit-CoreLocation/wiki/Current-Location-Accuracy).

## 앞으로

우리는 몇 가지 이정표와 위와 관련된 문제가 있습니다. 논의하거나 공헌하는 것은 누구나 환영합니다. 마음껏 풀 요청을 보내주십시오. 새로운 Issue를 추가하거나 [the Slack community](https://join.slack.com/t/arcl-dev/shared_invite/MjE4NTQ3NzE3MzgxLTE1MDExNTAzMTUtMTIyMmNlMTkyYg)로 새로운 기능/확장/버그에 대해 논의

## Thanks
[@AndrewProjDent](https://twitter.com/andrewprojdent)가 라이브러리를 만들었지만 커뮤니티의 노력은 여기에서입니다.

[MIT License](http://opensource.org/licenses/MIT)의 조건으로 오픈 소스로 이용할 수 있습니다.
