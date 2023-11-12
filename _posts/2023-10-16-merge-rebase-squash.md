---
title: merge, rebase, squash 차이점
author: mojitobar
date: 2023-10-14 22:52:00 +0900
categories: [iOS, Swift]
tags: [iOS, Swift, merge, rebase, squash, git]
comments: true
---

## 개요

깃에는 커밋을 합칠 수 있는 여러 키워드들이 있어요.

이번에는 가장 자주 사용되는 merge, rebase, squash 키워드와 그 차이들에 대해 알아봤어요.

## Merge

두 개의 branch를 합치는 과정이에요.

만약 `feature` branch에서 작업을 완료하고 이를 `main` branch에 합치려고 한다면 `merge`를 사용하게 돼요. merge를 할 때, `feature`와 `main` branch의 변경 사항들을 합치고, 그 결과를 새로운 커밋으로 저장해요.

![merge](https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/6117c62d-5648-411e-926a-4c940697e531)

그림을 보면 `test`의 변경 사항들을 `main`으로 합치면서 새로운 `C6` 커밋이 생긴 것을 볼 수 있어요.

```swift
// 현재 브랜치에 {test} 브랜치를 합치겠다는 뜻
git merge test
```

## Rebase

한 branch의 변경 사항을 다른 branch 위에 재배치하는 과정이에요.

이는 커밋 히스토리를 깔끔하게 한 줄로 유지하는 데 도움이 돼요.

![rebase](https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/4db20f6f-d335-41cc-ad20-5c730b37108f)

그림을 보면 `test`의 변경 사항들을 그대로 `main` 아래에 옮긴 것을 볼 수 있어요.

```swift
// 현재 브랜치의 변경 사항을 {main} 브랜치 밑으로 합치겠다는 뜻
git rebase main

// 이후에 {main} 브랜치에서 merge를 하면 fast-forward가 적용되어,
// test와 같은 노드를 가르킬 수 있다.
git checkout main
git merge test
```

## Squash

여러 커밋을 하나의 커밋으로 합치는 과정이에요. git merge의 옵션으로 `—squash`를 이용하여 사용할 수 있어요.

원래 merge의 명령어의 경우 합치고자하는 브랜치에 기록된 모든 커밋을 가져오지만 `—squash`의 경우 여러 커밋을 합친 새로운 커밋을 현재 브랜치에 반영해요. rebase에서도 squash를 사용할 수 있어요.

```swift
// merge --squash 예제
git checkout main
git merge --squash test

// 합친 커밋이 반영될 메시지를 작성
git commit -m "commit 0~3"

// rebase & squash 예제
// HEAD 기준으로 최근 커밋 3개를 rebase 하겠다는 뜻
git rebase -i HEAD~3
```

`git merge --squash test`는 현재 브랜치와 비교하여 test의 모든 변경사항을 하나의 커밋으로 합치겠다는 뜻이에요.

그래서 이후에 커밋 메시지를 `commit 0~3` 와 같은 방식으로 작성하는 거죠.

<div>
<img alt="squash_image1" src="https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/f4db3770-1c43-4fcc-9b8f-c2761e22461b" width="360" >
<img alt="squash_image2" src="https://github.com/MojitoBar/MojitoBar.github.io/assets/16567811/939e45b0-4c98-43c3-b876-5abb5205b09d" width="360" >
</div>

반면 `git rebase -i HEAD~3` 명령어를 입력하면 위 사진과 같은 창이 떠요.

왼쪽처럼 창이 뜨면 앞에 `pick` 부분을 원하는 명령어로 변경한 후 저장하면 돼요.

오른쪽은 `squash`로 변경하고 저장했을 때 뜨는 화면이에요. 여기서 합칠 커밋 메시지를 작성하면 돼요.

## 어떨 때 사용하면 좋을까요?

- merge: 간단하게 브랜치를 합치고 그 과정을 명시적으로 남기고 싶을 때 사용해요.
- rebase: 커밋 히스토리를 한 줄로 깔끔하게 유지하고 싶을 때 사용해요.
- squash: 여러 변경 사항을 하나의 의미 있는 커밋으로 합칠 때 사용해요.

## 마무리

개인적으로는 squash가 매력적이었어요.

브랜치를 따서 작업하다보면 의미있는 커밋만 하려해도 굳이 전부 반영될 필요가 없는 커밋이 있는 경우가 있었어요. 또는 main에 반영될 것을 생각해서 의미있는 커밋만을 하려다보니 너무 잘게 커밋하게 되거나 한번에 diff가 큰 커밋을 하게 되는 경우도 있었죠.

이럴 때 squash를 이용해서 main에 변경사항을 훨씬 깔끔하고 단위도 어느정도 일정하게 나눠서 반영할 수 있을 것 같았어요.
