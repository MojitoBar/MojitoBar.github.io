---
title: Accessibility - 번역
author: mojitobar
date: 2023-12-09 15:15:00 +0900
categories: [iOS, Swift, HIG]
tags: [iOS, Swift, HIG, Accessibility, Human Interface Guidelines]
comments: true
image:
  path: /assets/img/foundations-accessibility-intro~dark.png
  alt: A sketch of the Accessibility icon. The image is overlaid with rectangular and circular grid lines and is tinted yellow to subtly reflect the yellow in the original six-color Apple logo.
---

## Accessibility

> An accessible app or game supports accessibility personalizations by design and helps everyone have a great experience, regardless of their capabilities or how they use their devices.

접근성을 고려하여 설계된 앱이나 게임은 개인의 접근성 맞춤 설정을 지원하며, 사용자의 능력이나 기기 사용 방법에 관계없이 모든 사람이 훌륭한 사용 경험을 누릴 수 있도록 돕습니다.

> Approximately one in seven people have a disability that affects the way they interact with the world and their devices. People can experience disabilities at any age, for any duration, and at varying levels of severity. For example, situational disabilities — such as a wrist injury from a fall or voice loss from overuse — can affect the way almost everyone interacts with their devices at various times.

세계 인구 중 약 7분의 1이 기기 사용에 영향을 주는 다양한 장애를 가지고 있으며, 이러한 장애는 나이, 지속 기간, 심각도에 상관없이 나타날 수 있습니다. 예를 들어, 일시적으로 손목을 다치거나 목소리를 잃는 것처럼, 우리 모두의 일상 속에서 발생할 수 있는 다양한 상황이 기기 사용 방식에 영향을 미칠 수 있습니다.

## Best practices

> Design with accessibility in mind. Accessibility is not just about making information available to people with disabilities — it’s about making information available to everyone, regardless of their capabilities or situation. Designing your app with accessibility in mind means prioritizing simplicity and perceivability and examining every design decision to ensure that it doesn’t exclude people with disabilities or who interact with their devices in different ways.

**접근성을 염두에 두고 설계하세요.** 접근성은 장애가 있는 사람들에게만 정보를 제공하는 것이 아니라, 누구나 자신의 능력이나 상황에 구애받지 않고 정보를 이용할 수 있게 하는 것입니다. 접근성을 고려하여 앱을 설계한다는 것은 단순성과 인식 가능성을 중요시하며, 장애가 있는 사람들이나 다른 방식으로 기기와 상호작용하는 사람들을 배제하지 않도록 모든 설계 결정을 꼼꼼히 검토하는 것을 의미합니다.

> Simplicity — Support familiar, consistent interactions that make complex tasks simple and straightforward to perform.

단순성 — 익숙하고 일관된 상호작용을 지원하여 복잡한 작업을 간단하고 명확하게 수행할 수 있게 합니다.

> Perceivability — Make sure that all content can be perceived whether people are using sight, hearing, or touch.

인식 가능성 — 사람들이 시각, 청각, 촉각을 사용하여 모든 내용을 인식할 수 있도록 합니다.

> Support personalization. You already design your experience to adapt to environmental variations — such as device orientation, display size, resolution, color gamut, and split view — because you want people to enjoy it in any context and on all supported devices. With minimal additional effort, you can design your app to support the accessibility features people use to personalize the ways they interact with their devices.

**개인화 지원하기.** 여러분은 이미 기기의 방향, 디스플레이 크기, 해상도, 색상 범위, 분할 뷰 등 환경 변화에 적응하도록 경험을 설계하고 있습니다. 이는 사람들이 어떤 상황에서나 모든 지원 기기에서 즐길 수 있도록 하기 위함입니다. 최소한의 추가 노력으로, 사용자가 자신의 기기와 상호작용하는 방식을 개인화하는 데 사용하는 접근성 기능을 지원하도록 앱을 설계할 수 있습니다.

> When you use standard components to implement your interface, text and controls automatically adapt to several accessibility settings, such as Bold Text, Larger Text, Invert Colors, and Increase Contrast.

인터페이스를 구현할 때 표준 컴포넌트를 사용하면, 텍스트와 컨트롤이 굵은 텍스트, 큰 텍스트, 색상 반전, 대비 증가 등 여러 접근성 설정에 자동으로 적응합니다.

> Audit and test your app or game for accessibility. An audit examines every element in your experience and gives you a comprehensive list of issues to fix. Testing helps you ensure that everyone can complete the most important tasks in your app, no matter how they interact with their devices.

**앱이나 게임을 접근성 관점에서 검사하고 테스트하세요.** 검사는 사용자 경험의 모든 요소를 살펴보고 수정해야 할 문제 목록을 제공합니다. 테스트를 통해 사람들이 자신의 기기와 어떻게 상호작용하든 앱에서 가장 중요한 작업을 완료할 수 있도록 보장합니다.

> When you test important user flows with accessibility features turned on, you gain an appreciation for the challenges of interacting with a device in different ways. You also discover places where your app might fail to deliver a great user experience.

접근성 기능을 켠 상태에서 중요한 사용자 플로우를 테스트할 때, 다양한 방식으로 기기와 상호작용하는 데 따른 도전을 이해하게 됩니다. 또한 앱이 훌륭한 사용자 경험을 제공하지 못하는 부분을 발견할 수도 있습니다.

> For example, a common user flow in a social media app might be “post a response to a comment.” The tasks that make up this flow could include:

예를 들어, 소셜 미디어 앱에서 흔히 볼 수 있는 사용자 플로우는 "댓글에 대한 응답 게시하기"일 수 있습니다. 이 플로우를 구성하는 작업은 다음과 같을 수 있습니다:

> Read posted comments.
>
> Choose a comment for a response.
>
> Open the response view.
>
> Edit the response.
>
> Post the response.

게시된 댓글 읽기.

응답할 댓글 선택하기.

응답 화면 열기.

응답 수정하기.

응답 게시하기.

> For each critical user flow in your app or game, turn on an accessibility feature, such as VoiceOver, Reduce Motion, or Large Text Size, and make sure that you can complete every task in the flow without difficulty. After you fix the problems you uncover, turn on a different accessibility feature and run through the user flow again. To help you audit, test, and fix your app or game, consider using Xcode’s Accessibility Inspector.

앱이나 게임의 각 중요 사용자 플로우에 대해 VoiceOver, 움직임 줄이기, 큰 텍스트 크기와 같은 접근성 기능을 켜고, 플로우의 모든 작업을 어려움 없이 완료할 수 있는지 확인하세요. 발견한 문제를 해결한 후에는 다른 접근성 기능을 켜고 사용자 플로우를 다시 실행하세요. 앱이나 게임을 검사하고, 테스트하며, 문제를 해결하기 위해 Xcode의 접근성 검사기를 사용하는 것을 고려해 보세요.

## Interactions

> Assistive technologies like VoiceOver, Assistive Touch, Pointer Control, and Switch Control expand the ways people can interact with their devices. Because these technologies integrate with system-provided interactions, it’s essential that you support the system interactions correctly in your app.

VoiceOver, Assistive Touch, 포인터 컨트롤, 스위치 컨트롤과 같은 보조 기술은 사람들이 기기와 상호작용하는 방식을 확장합니다. 이러한 기술들이 시스템 제공 상호작용과 통합되기 때문에, 앱에서 시스템 상호작용을 올바르게 지원하는 것이 필수적입니다.

### Gestures

> Don’t override the platform gestures. People expect gestures that target system features — like swiping down to reveal Notification Center — to work regardless of the app they’re using.

플랫폼 제스처를 재정의하지 마세요. 사람들은 시스템 기능을 대상으로 하는 제스처(예: 알림 센터를 표시하기 위해 아래로 스와이프하기)가 사용 중인 앱에 관계없이 작동하기를 기대합니다.

> Prefer simplified gestures for common interactions. Complex gestures such as multifinger or multihand gestures, long presses, or gestures that require repeated movements can be challenging for many people. Using the simplest gestures possible improves the experience for everyone who interacts with your app.

일반적인 상호작용을 위한 간소화된 제스처를 선호하세요. 여러 손가락이나 여러 손을 사용하는 제스처, 긴 누르기, 반복적인 움직임을 필요로 하는 제스처는 많은 사람들에게 도전이 될 수 있습니다. 가능한 가장 단순한 제스처를 사용하는 것이 앱과 상호작용하는 모든 사람의 경험을 향상시킵니다.

> Provide alternative ways to perform gesture-based actions. Include an option for people who may not be able to perform a specific gesture. For example, if people can use a gesture to delete a row in a table, you can also provide a way to delete items through an edit mode or by offering a Delete button in an item detail view.

제스처 기반 동작을 수행할 수 있는 대체 방법을 제공하세요. 특정 제스처를 수행할 수 없는 사람들을 위한 옵션을 포함하세요. 예를 들어, 사람들이 제스처를 사용하여 테이블의 행을 삭제할 수 있다면, 편집 모드를 통해 또는 항목 세부 정보 뷰에서 삭제 버튼을 제공하는 방법으로 항목을 삭제할 수 있게 할 수 있습니다.

<div style="text-align: center">
<img src="https://docs-assets.developer.apple.com/published/fb426fd2e00092ee2888f3610dae626e/tap-to-delete~dark@2x.png" width="250" alt="tap-to-delete">
<img src="https://docs-assets.developer.apple.com/published/f66d4e1598c12b48ad5f9366a0e56afa/swipe-to-delete~dark@2x.png" width="250" alt="swipe-to-delete">
</div>

> When possible, make your app’s core functionality accessible through more than one type of physical interaction. For example, Camera on iPhone and iPad lets people take a photo by tapping the onscreen button or by pressing the device’s volume down button. In addition to making photo-capture more convenient for everyone, these alternative interactions provide options to people who might have limited grip strength or dexterity.

가능한 경우 앱의 핵심 기능을 여러 유형의 물리적 상호작용을 통해 접근할 수 있게 만드세요. 예를 들어, 아이폰과 아이패드의 카메라는 화면의 버튼을 탭하거나 기기의 볼륨 다운 버튼을 눌러 사진을 찍을 수 있습니다. 이러한 대체 상호작용은 모든 사람에게 사진 촬영을 더 편리하게 만들 뿐만 아니라, 손잡이 힘이나 손가락 민첩성이 제한된 사람들에게 옵션을 제공합니다.

> If you define custom gestures, be sure to support assistive technologies that give people alternative ways to interact with your app. For example, with Pointer Control, people can use a wrist, index finger, or head-based pointer; with Dwell Control, they can use only their eyes to select and activate objects. One way to support technologies like VoiceOver, Dwell Control, and Switch Control is to implement custom actions; for developer guidance, see UIAccessibilityCustomAction.

사용자 정의 제스처를 정의할 경우, 앱과 상호작용하는 대체 방법을 제공하는 보조 기술을 지원하는 것이 중요합니다. 예를 들어, 포인터 컨트롤을 사용하면 사람들은 손목, 검지 손가락, 또는 머리 기반 포인터를 사용할 수 있고; 드웰 컨트롤을 사용하면 눈만으로 객체를 선택하고 활성화할 수 있습니다. VoiceOver, 드웰 컨트롤, 스위치 컨트롤과 같은 기술을 지원하는 한 가지 방법은 사용자 정의 액션을 구현하는 것입니다. 개발자 가이드는 [UIAccessibilityCustomAction](https://developer.apple.com/documentation/uikit/uiaccessibilitycustomaction)을 참조하세요.

> Make drag and drop accessible in your iOS or iPadOS app. When you use the accessibility APIs to identify drag sources and drop targets in your app, assistive technologies can help people drag and drop items. For developer guidance, see accessibilityDragSourceDescriptors and accessibilityDropPointDescriptors.

iOS 또는 iPadOS 앱에서 드래그 앤 드롭을 접근 가능하게 만드세요. 접근성 API를 사용하여 앱에서 드래그 소스와 드롭 대상을 식별하면, 보조 기술이 사람들이 항목을 드래그하고 놓는 것을 도와줄 수 있습니다. 개발자 가이드는 [accessibilityDragSourceDescriptors](https://developer.apple.com/documentation/objectivec/nsobject/2891001-accessibilitydragsourcedescripto)와 [accessibilityDropPointDescriptors](https://developer.apple.com/documentation/objectivec/nsobject/2891048-accessibilitydroppointdescriptor)를 참조하세요.

### Buttons and controls

> Give all controls and interactive elements a hit target that’s large enough. For example, on touchscreen devices, a hit target needs to measure at least 44x44 pt; in visionOS, place controls so that their centers are at least 60 pt apart. People with limited mobility need larger hit targets to help them interact with your app. It can be frustrating to interact with too-small controls in any platform, even when people use a pointer.

**모든 컨트롤과 상호작용 요소에 충분한 크기의 히트 타겟을 제공하세요.** 예를 들어, 터치스크린 기기에서 히트 타겟은 최소 44x44 pt의 크기가 필요하며, visionOS에서는 컨트롤의 중심이 최소 60 pt 떨어져 있도록 배치해야 합니다. 이동성이 제한된 사람들은 앱과 상호작용을 돕기 위해 더 큰 히트 타겟이 필요합니다. 포인터를 사용할 때도 너무 작은 컨트롤과 상호작용하는 것은 어떤 플랫폼에서도 실망스러울 수 있습니다.

> Characterize the accessibility of custom elements. You can use system APIs to tell assistive technologies how a component behaves. For example, using button or NSAccessibilityButton to characterize a view as a button means that VoiceOver speaks the view’s description followed by the word button, which tells people that the view behaves like a button.

**사용자 정의 요소의 접근성을 특성화하세요.** 시스템 API를 사용하여 보조 기술에 컴포넌트가 어떻게 작동하는지 알릴 수 있습니다. 예를 들어, 뷰를 버튼으로 특성화하기 위해 button 또는 NSAccessibilityButton을 사용하면 VoiceOver는 뷰의 설명 뒤에 '버튼'이라는 단어를 말해줍니다. 이는 뷰가 버튼처럼 작동한다는 것을 사람들에게 알려줍니다.

> Use a consistent style hierarchy to communicate the relative importance of buttons. In iOS, iPadOS, and tvOS, for example, you can use the visually prominent filled style for the button that performs the most likely action in a view, using less prominent styles — such as gray or plain — for buttons that perform less important actions. (For developer guidance, see UIButton.Configuration.) In visionOS, system-provided buttons generally include a visible background by default. In iOS, iPadOS, visionOS, and for some buttons in macOS, people can also turn on Button Shapes to make it easier to distinguish active buttons from surrounding content.

**버튼의 상대적 중요도를 전달하기 위해 일관된 스타일 계층 구조를 사용하세요.** 예를 들어 iOS, iPadOS 및 tvOS에서는 뷰에서 가장 가능성 있는 동작을 수행하는 버튼에 눈에 띄는 채워진 스타일을 사용하고, 중요도가 낮은 동작을 수행하는 버튼에는 덜 눈에 띄는 스타일(예: 회색 또는 평범한)을 사용할 수 있습니다. (개발자 가이드는 UIButton.Configuration 참조). visionOS에서는 시스템에서 제공하는 버튼에 일반적으로 기본적으로 보이는 배경이 포함되어 있습니다. iOS, iPadOS, visionOS 및 macOS의 일부 버튼에서는 사람들이 주변 콘텐츠와 활성 버튼을 구별하기 쉽도록 버튼 형태를 켤 수도 있습니다.

> Prefer the system-provided switch component. SwiftUI provides a switch that indicates its state by the position of its knob and its fill color. For some people, however, the addition of labels makes it easier to perceive whether a switch is on or off. When you use system-provided switches, iOS, iPadOS, tvOS, visionOS, and watchOS automatically display on/off glyphs within them when people turn on On/Off Labels.

**시스템에서 제공하는 스위치 컴포넌트를 선호하세요.** SwiftUI는 노브의 위치와 채우기 색상으로 상태를 나타내는 스위치를 제공합니다. 그러나 일부 사람들에게는 스위치가 켜져 있는지 꺼져 있는지 더 쉽게 인지할 수 있도록 레이블을 추가하는 것이 도움이 됩니다. 시스템에서 제공하는 스위치를 사용하면 iOS, iPadOS, tvOS, visionOS, watchOS에서 사람들이 켜기/끄기 레이블을 켤 때 스위치 내부에 켜기/끄기 글리프를 자동으로 표시합니다.

<div style="text-align: center">
<img src="https://docs-assets.developer.apple.com/published/5c40107ea0e2e65d88bd194103c5d3c9/switches-without-labels~dark@2x.png" width="200" alt="without on/off label">
<img src="https://docs-assets.developer.apple.com/published/4fcaf17a69147ead56ed2e1f95561748/switches-with-labels~dark@2x.png" width="200" alt="with on/off label">
</div>

> Consider giving links a visual indicator in addition to color, such as an underline. It’s fine to use color to identify a link, but if you use it as the only indicator, people — such as those with color blindness or cognitive or situational attention impairments — may not be able to perceive the distinction.

색상과 함께 밑줄과 같은 시각적 표시를 링크에 제공하는 것을 고려하세요. 링크를 식별하기 위해 색상을 사용하는 것은 괜찮지만, 유일한 표시로 사용하는 경우 색맹이나 인지 또는 상황적 주의력 장애가 있는 사람들은 구별을 인지하지 못할 수 있습니다.

### User input

> Let people input information by speaking instead of typing or gesturing. Adding a dictation button in a text entry field lets people choose speech as their preferred input method. If you create a custom keyboard, be sure to include a microphone key for dictation.

사람들이 타이핑이나 제스처 대신 말로 정보를 입력할 수 있도록 하세요. 텍스트 입력 필드에 음성 입력 버튼을 추가하면 사람들이 선호하는 입력 방법으로 음성을 선택할 수 있습니다. 사용자 정의 키보드를 만드는 경우, 음성 인식을 위한 마이크 키를 포함하는지 확인하세요.

> Support Siri or Shortcuts for performing important tasks by voice alone. To learn more about helping people use Siri interactions in your app, see Siri.

사람들이 음성만으로 중요한 작업을 수행할 수 있도록 Siri 또는 단축어를 지원하세요. 앱에서 Siri 상호작용을 사용하는 사람들을 돕는 방법에 대해 자세히 알아보려면 Siri를 참조하세요.

> When possible, don’t prevent people from selecting plain text. Many people rely on using selected text as input for translations and definitions.

가능한 한, 일반 텍스트의 선택을 방지하지 마세요. 많은 사람들이 번역이나 정의를 위해 선택된 텍스트를 입력으로 사용하는 것에 의존하고 있습니다.

### Haptics

> Support the system-defined haptics where available. Many people rely on haptics to help them interact with apps when they can’t see the display. For example, system apps play haptics to notify people when a task has succeeded or failed or when an event is about to happen. Be sure to use the system-defined haptics consistently in your app so that you don’t confuse people. For guidance, see Playing haptics.

**가능한 경우 시스템에서 정의한 햅틱을 지원하세요.** 많은 사람들이 디스플레이를 볼 수 없을 때 앱과 상호작용하는 데 햅틱을 사용하는 것에 의존하고 있습니다. 예를 들어, 시스템 앱은 작업이 성공하거나 실패했거나 이벤트가 발생할 때 사람들에게 알리기 위해 햅틱을 재생합니다. 앱에서 시스템에서 정의한 햅틱을 일관되게 사용하여 사람들을 혼란스럽게 하지 않도록 주의하세요. 가이드는 [Playing haptics](https://developer.apple.com/design/human-interface-guidelines/playing-haptics)를 참조하세요.

> In platforms that don’t play haptics, use other ways to provide feedback when people interact with custom objects, such as sound.

❕ 햅틱을 지원하지 않는 플랫폼에서는 사람들이 사용자 지정 객체와 상호작용할 때 사운드와 같은 다른 방법을 사용하여 피드백을 제공하세요.

## VoiceOver

> VoiceOver gives audible descriptions of visible content, helping people get information and navigate when they can’t see the display. In visionOS, VoiceOver uses Spatial Audio to help communicate the location of accessible objects.

VoiceOver는 볼 수 있는 콘텐츠에 대한 청각적 설명을 제공하여, 사람들이 디스플레이를 볼 수 없을 때 정보를 얻고 탐색하는 데 도움을 줍니다. visionOS에서 VoiceOver는 공간 오디오를 사용하여 접근 가능한 객체의 위치를 전달하는 데 도움을 줍니다.

> When VoiceOver is on in visionOS, apps that define custom gestures don’t receive hand input by default. Instead, people can perform VoiceOver gestures to explore apps without worrying about an app interpreting their hand input. In VoiceOver’s Direct Gesture mode, VoiceOver doesn’t process its standard gestures, instead letting an app process hand input directly. For developer guidance, see Improving accessibility support in your visionOS app.

⚠️ visionOS에서 VoiceOver가 활성화되어 있을 때, 사용자 정의 제스처를 정의하는 앱은 기본적으로 손 입력을 받지 않습니다. 대신, 사용자는 앱이 손 입력을 해석하는 것에 대해 걱정할 필요 없이 VoiceOver 제스처를 수행하여 앱을 탐색할 수 있습니다. VoiceOver의 직접 제스처 모드에서는 VoiceOver가 표준 제스처를 처리하지 않고, 대신 앱이 손 입력을 직접 처리하도록 합니다. 개발자 가이드는 [visionOS 앱에서 접근성 지원 개선하기](https://developer.apple.com/documentation/visionOS/improving-accessibility-support-in-your-app)를 참조하세요.

### Content descriptions

> Provide alternative descriptions for all images that convey meaning. If you don’t describe the meaningful images in your content, you prevent VoiceOver users from fully experiencing your app. To create a useful description, start by reporting what would be self-explanatory to someone who is able to see the image. Because VoiceOver reads the text surrounding the image and any captions, describe only the information that’s conveyed by the image itself.

**의미 있는 모든 이미지에 대한 대체 설명을 제공하세요.** 컨텐츠의 의미 있는 이미지를 설명하지 않으면, VoiceOver 사용자가 앱을 완전히 경험하는 것을 방해합니다. 유용한 설명을 만들기 위해서는 이미지를 볼 수 있는 사람에게 자명한 내용부터 보고하세요. VoiceOver가 이미지 주변의 텍스트와 캡션을 읽기 때문에, 이미지 자체가 전달하는 정보만 설명하세요.

<div style="text-align: center">
<img src="https://docs-assets.developer.apple.com/published/c6b54e401411a6488486e5b960f05ab5/image-with-alt-text@2x.png" width="250" alt="The alternative description for this element is “Moving: 125 percent; Exercise: zero percent; Standing: 58 percent.”">
</div>

> Make infographics fully accessible. Provide a concise description of the infographic that explains what it conveys. If people can interact with the infographic to get more or different information, you need to make these interactions available to VoiceOver users, too. The accessibility APIs provide ways to represent custom interactive elements so that assistive technologies can help people use them.

**인포그래픽을 완전히 접근 가능하게 만드세요.** 인포그래픽이 전달하는 내용을 설명하는 간결한 설명을 제공하세요. 사람들이 인포그래픽과 상호작용하여 더 많거나 다른 정보를 얻을 수 있다면, 이러한 상호작용을 VoiceOver 사용자에게도 제공해야 합니다. 접근성 API는 보조 기술이 사람들이 사용할 수 있도록 사용자 정의 상호작용 요소를 표현하는 방법을 제공합니다.

> When an image is purely decorative and isn’t intended to communicate anything important, hide it from assistive technologies. Making VoiceOver describe a purely decorative image can waste people’s time and add to their cognitive load without providing any benefit.

**이미지가 순전히 장식적이며 중요한 내용을 전달하지 않는다면, 보조 기술에서 숨기세요.** 순전히 장식적인 이미지에 대해 VoiceOver로 설명하게 하는 것은 사람들의 시간을 낭비하고 인지 부담을 늘릴 수 있으며, 어떠한 이득도 제공하지 않습니다.

> Give each page a unique title and provide headings that identify sections in your information hierarchy. When people arrive on a page, the title is the first piece of information they receive from an assistive technology. To help people understand the structure of your app, create a unique title for each page that succinctly describes its contents or purpose. Similarly, people need accurate section headings to help them build a mental map of the information hierarchy of each page.

**각 페이지에 고유한 제목을 부여하고 정보 계층구조에서 섹션을 식별하는 제목을 제공하세요.** 사람들이 페이지에 도착했을 때, 제목은 보조 기술에서 받는 첫 번째 정보입니다. 앱의 구조를 사람들이 이해할 수 있도록 각 페이지마다 내용이나 목적을 간결하게 설명하는 고유한 제목을 만드세요. 마찬가지로, 사람들이 각 페이지의 정보 계층구조에 대한 정신적 지도를 만드는 데 도움이 되는 정확한 섹션 제목이 필요합니다.

> Help everyone enjoy your video and audio content. When you provide closed captions, audio descriptions, and transcripts, you can help people benefit from audio and video content in ways that work for them.

**모든 사람이 비디오 및 오디오 콘텐츠를 즐길 수 있도록 도와주세요.** 선택 캡션, 오디오 설명, 대본을 제공하면 사람들이 자신에게 맞는 방식으로 오디오 및 비디오 콘텐츠의 이점을 얻을 수 있습니다.

> Closed captions give people a textual equivalent for the audible information in a video. You can also use closed captions to provide multiple translations for the same content, letting the system choose the version that matches the device’s current settings. Because closed captions aren’t always available, it’s important to provide subtitles, too.

**선택 캡션은 비디오에서 청각 정보에 대한 텍스트 상응물을 제공합니다.** 또한 선택 캡션을 사용하여 동일한 콘텐츠에 대해 여러 번역을 제공하고, 시스템이 기기의 현재 설정과 일치하는 버전을 선택하도록 할 수 있습니다. 폐쇄 캡션은 항상 제공되지 않기 때문에 자막도 제공하는 것이 중요합니다.

> Audio descriptions provide a spoken narration of important information that’s presented only visually.

오디오 설명은 시각적으로만 제시된 중요한 정보에 대한 구두 해설을 제공합니다.

> A transcript provides a complete textual description of a video, covering both audible and visual information, so that people can enjoy the video in different ways.

대본은 비디오의 청각 및 시각 정보를 모두 커버하는 완전한 텍스트 설명을 제공하여, 사람들이 다양한 방식으로 비디오를 즐길 수 있도록 합니다.

> For developer guidance, see Selecting Subtitles and Alternative Audio Tracks.

개발자 가이드는 [자막 및 대체 오디오 트랙 선택하기](https://developer.apple.com/documentation/avfoundation/media_playback/selecting_subtitles_and_alternative_audio_tracks)를 참조하세요.

### Navigation

> Make sure VoiceOver users can navigate to every element. VoiceOver uses accessibility information from UI elements to help people understand the location of each element and what it can do. System-provided UI components include this accessibility information by default, but VoiceOver can’t help people discover and use custom elements unless you provide the information. For developer guidance, see Accessibility modifiers.

**VoiceOver 사용자가 모든 요소에 접근할 수 있도록 해야 합니다.** VoiceOver는 UI 요소에서 접근성 정보를 사용하여 사람들이 각 요소의 위치와 기능을 이해하는 데 도움을 줍니다. 시스템에서 제공하는 UI 컴포넌트는 기본적으로 이 접근성 정보를 포함하지만, 사용자 정의 요소에 대한 정보를 제공하지 않는 한 VoiceOver는 사람들이 이러한 요소를 발견하고 사용하는 것을 도울 수 없습니다. 개발자 가이드는 [접근성 수정자](https://developer.apple.com/documentation/SwiftUI/View-Accessibility)를 참조하세요.

> Improve the VoiceOver experience by specifying how elements are grouped, ordered, or linked. Proximity, alignment, and other contextual cues can help sighted people perceive the relationships among visible elements, but these cues don’t work well for VoiceOver users. Examine your app for places where relationships among elements are visual only, and describe these relationships to VoiceOver.

**요소들이 어떻게 그룹화되고, 정렬되며, 연결되는지 명시함으로써 VoiceOver 경험을 개선하세요.** 근접성, 정렬 및 기타 맥락적 단서는 시각이 있는 사람들이 보이는 요소들 간의 관계를 인지하는 데 도움이 될 수 있지만, 이러한 단서는 VoiceOver 사용자에게 잘 작동하지 않습니다. 앱에서 요소들 간의 관계가 시각적으로만 나타나는 곳을 살펴보고, 이러한 관계를 VoiceOver에 설명하세요.

> For example, the layout below relies on proximity and centering to imply that each phrase is a caption for the image above it. However, if you don’t tell VoiceOver that each image needs to be grouped with its phrase, VoiceOver reads, “A large container holding a variety of mangoes. A large container holding many green artichokes. Mangoes come from trees that belong to the genus Mangifera. Artichokes come from a variety of a species of thistle.” This happens because VoiceOver reads elements from top to bottom by default. For developer guidance, see shouldGroupAccessibilityChildren and accessibilityTitleUIElement.

**예를 들어, 아래 레이아웃은 근접성과 중앙 정렬을 사용하여 각 구절이 그 위에 있는 이미지의 캡션임을 시사합니다.** 그러나 VoiceOver에 각 이미지가 해당 구절과 그룹화되어야 한다고 알려주지 않으면, VoiceOver는 "다양한 망고가 들어 있는 큰 용기. 많은 녹색 아티초크가 들어 있는 큰 용기. 망고는 Mangifera 속에 속하는 나무에서 옵니다. 아티초크는 엉겅퀴 종의 다양한 종에서 나옵니다."라고 읽습니다. 이는 VoiceOver가 기본적으로 요소를 위에서 아래로 읽기 때문입니다. 개발자 가이드는 [shouldGroupAccessibilityChildren](https://developer.apple.com/documentation/objectivec/nsobject/1615143-shouldgroupaccessibilitychildren) 및 [accessibilityTitleUIElement](https://developer.apple.com/documentation/appkit/nsaccessibility/1535155-accessibilitytitleuielement)를 참조하세요.

<div style="text-align: center; display: flex;">
<figure>
  <img src="https://docs-assets.developer.apple.com/published/1658b1444c6743131c2cb5513a14eb08/mangoes@2x.png" width="200" alt="without on/off label">
  <figcaption>Mangoes come from trees that belong to the genus Mangifera.</figcaption>
</figure>
<figure>
<img src="https://docs-assets.developer.apple.com/published/5750c7d12cf60f5054ec8c367ec98612/artichokes@2x.png" width="200" alt="with on/off label">
<figcaption>Artichokes come from a variety of a species of thistle.</figcaption>
</figure>
</div>

> Tell VoiceOver when visible content or layout changes. An unexpected change in content or layout can be very confusing to VoiceOver users, because it means that their mental map of the content is no longer accurate. It’s crucial to report visible changes so that VoiceOver and other assistive technologies can help people update their understanding of the content. For developer guidance, see UIAccessibility.Notification (UIKit) or NSAccessibility.Notification (AppKit).

**VoiceOver에 보이는 콘텐츠나 레이아웃의 변경 사항을 알려주세요.** 콘텐츠나 레이아웃의 예기치 않은 변경은 VoiceOver 사용자에게 혼란을 줄 수 있습니다. 이는 그들의 콘텐츠에 대한 정신적 지도가 더 이상 정확하지 않음을 의미하기 때문입니다. 보이는 변경 사항을 보고하는 것은 VoiceOver와 다른 보조 기술이 사람들이 콘텐츠에 대한 이해를 업데이트하는 데 도움을 줄 수 있도록 매우 중요합니다. 개발자 가이드는 [UIAccessibility.Notification](https://developer.apple.com/documentation/uikit/uiaccessibility/notification) (UIKit) 또는 [NSAccessibility.Notification](https://developer.apple.com/documentation/appkit/nsaccessibility/notification) (AppKit)를 참조하세요.

> Help people predict when a control opens a different webpage or app. An unexpected change in context can cause confusion and require people to suddenly rebuild their mental model of the current experience. One way to draw attention to a potential change in context is append an ellipsis to a button’s title. Throughout the system, an ellipsis trailing the title is the standard way for a button to communicate that it opens another window or view in which people can complete the action. For example, Mail in iOS and iPadOS appends an ellipsis to the Move Message button, signaling that a separate view opens, listing the destinations people can choose.

**컨트롤이 다른 웹페이지나 앱을 여는 경우 사람들이 예측할 수 있도록 도와주세요.** 예상치 못한 맥락 변경은 혼란을 일으키고 사람들이 현재 경험에 대한 정신적 모델을 갑자기 재구성해야 할 수 있습니다. 맥락 변경 가능성에 주의를 끄는 한 가지 방법은 버튼 제목에 말줄임표를 추가하는 것입니다. 시스템 전반에 걸쳐, 제목 뒤에 말줄임표가 있는 버튼은 사람들이 작업을 완료할 수 있는 다른 창이나 뷰를 여는 것을 전달하는 표준 방식입니다. 예를 들어, iOS와 iPadOS의 메일 앱은 '메시지 이동' 버튼에 말줄임표를 추가하여 별도의 뷰가 열리고 사람들이 선택할 수 있는 목적지를 나열한다는 것을 알립니다.

> Provide alternative text labels for all important interface elements. Alternative text labels aren’t visible, but they let VoiceOver audibly describe app elements, making navigation easier for people with visual disabilities. System-provided controls have useful labels by default, but you need to create labels for custom elements. For example, if you create an accessibility element that represents a custom rating button, you might supply the label “Rate.”

**모든 중요한 인터페이스 요소에 대체 텍스트 레이블을 제공하세요.** 대체 텍스트 레이블은 보이지 않지만, VoiceOver가 앱 요소를 청각적으로 설명하게 하여 시각 장애가 있는 사람들의 탐색을 용이하게 합니다. 시스템에서 제공하는 컨트롤은 기본적으로 유용한 레이블을 가지고 있지만, 사용자 정의 요소에 대한 레이블을 만들어야 합니다. 예를 들어, 사용자 정의 평가 버튼을 나타내는 접근성 요소를 만든 경우 '평가하기'라는 레이블을 제공할 수 있습니다.

> Support the VoiceOver rotor when necessary. VoiceOver users can use a control called the rotor to navigate a document or webpage by headings, links, or other section types. The rotor can also bring up the braille keyboard. You can help VoiceOver users navigate among related items in your app by identifying these items to the rotor. For developer guidance, see UIAccessibilityCustomRotor and NSAccessibilityCustomRotor.

**필요할 때 VoiceOver 로터를 지원하세요.** VoiceOver 사용자는 로터라는 컨트롤을 사용하여 문서나 웹페이지를 제목, 링크 또는 기타 섹션 유형별로 탐색할 수 있습니다. 로터는 점자 키보드를 불러올 수도 있습니다. 앱에서 관련 항목 간에 탐색을 돕기 위해 이러한 항목을 로터에 식별해주세요. 개발자 가이드는 [UIAccessibilityCustomRotor](https://developer.apple.com/documentation/uikit/uiaccessibilitycustomrotor) 및 [NSAccessibilityCustomRotor](https://developer.apple.com/documentation/appkit/nsaccessibilitycustomrotor)를 참조하세요.

> In iPadOS, macOS, and visionOS, make sure people can use the keyboard to navigate and interact with all components in your app. Ideally, people can turn on Full Keyboard Access and perform every task in your experience using only the keyboard. In addition to accessibility keyboard shortcuts, the system defines a large number of other keyboard shortcuts that many people use all the time. To support everyone, it’s important to avoid overriding any system-defined keyboard shortcuts in your app. For guidance, see Keyboards.

**iPadOS, macOS, visionOS에서는 사람들이 키보드를 사용하여 앱의 모든 구성 요소를 탐색하고 상호 작용할 수 있도록 해야 합니다.** 이상적으로는 사람들이 전체 키보드 접근성을 켜고 키보드만을 사용하여 경험의 모든 작업을 수행할 수 있어야 합니다. [접근성 키보드 단축키](https://support.apple.com/en-us/HT204434) 외에도 시스템은 많은 사람들이 항상 사용하는 다양한 다른 [키보드 단축키](https://support.apple.com/en-us/HT201236)를 정의합니다. 모든 사람을 지원하기 위해 앱에서 시스템이 정의한 키보드 단축키를 재정의하는 것을 피하는 것이 중요합니다. 가이드는 [키보드](https://developer.apple.com/design/human-interface-guidelines/keyboards)를 참조하세요.
