---
title: jekyll gitblog chirpy 테마 사이드바 이미지 배경 넣기
description: (2025.03 기준)
author: 박성호
date: 2025-03-21 11:08:00 +09:00
categories: [Study, etc]
tags: [scss, jekyll, chirpy]
---

chirpy theme를 fork하여 깃블로그를 커스터마이징하면서, 사이드바의 배경을 light 모드와 다크 모드에서 다르게 설정하고 싶었습니다.
인터넷에서 방법을 찾아봐도, 최근 chirpy theme의 디렉토리가 바뀌었는지 \_sass 폴더 내에 사이드바에 대한 내용이 어느 scss파일에 있는지 찾는 데에 어려움을 겪어서 이에 대해 포스팅해보려 합니다.

## 사이드바 관련 scss 파일

```
/_sass/layout/_sidebar.scss
/_sass/themes/_light.scss
/_sass/themes/_dark.scss
```

## 사이드바 단일 이미지 배경(라이트/다크 모드에서 동일한 배경)

먼저, `/_sass/layout/_sidebar.scss` 파일에서 다음 부분을 찾습니다.

```scss
/* 수정 전 */
#sidebar {
  @include mx.pl-pr(0);

  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  overflow-y: auto;
  width: v.$sidebar-width;
  background: var(--sidebar-bg);
  border-right: 1px solid var(--sidebar-border-color);

  /* Hide scrollbar for IE, Edge and Firefox */
  -ms-overflow-style: none; /* IE and Edge */
  scrollbar-width: none; /* Firefox */

  /* Hide scrollbar for Chrome, Safari and Opera */
  &::-webkit-scrollbar {
    display: none;
  }
```

여기서 `background: var(--sidebar-bg);` 부분을 주석 처리( `/* 내용 */`)한 후 다음 코드를 추가합니다.

```scss
background-size: cover;
background-position: center;
background-repeat: no-repeat;
background-image: url("/assets/img/사이드바 이미지.jpg"); /*사용할 이미지 경로*/
```

## 사이드바 배경 이미지 모드별 설정(라이트/다크 모드에서 다른 배경)

앞선 과정과 비슷합니다. `/_sass/layout/_sidebar.scss` 파일에서 `#sidebar`부분을 찾고, 이번에는 주석 처리 없이 다음 코드만을 추가합니다.

```scss
background-size: cover;
background-position: center;
background-repeat: no-repeat;
```

다음으로 `/_sass/themes/_light.scss`와 `/_sass/themes/_dark.scss`에 들어가서 sidebar에 관한 부분을 찾습니다.

```scss
/* Sidebar */
--site-title-color: #717070;
--site-subtitle-color: #868686;
--sidebar-bg: #1e1e1e;
--sidebar-border-color: #292929;
--sidebar-muted-color: #868686;
--sidebar-active-color: rgb(255, 255, 255, 0.95);
--sidebar-hover-bg: #262626;
--sidebar-btn-bg: #232328;
--sidebar-btn-color: #787878;
--avatar-border-color: rgb(206, 206, 206, 0.9);
```

`--sidebar-bg: #1e1e1e;`를 `--sidebar-bg: url('/assets/img/다크모드 배경.jpg');`로 수정합니다. 파일 경로는 사용할 이미지 경로로 작성해야 하고, 깃허브와 연결된 디렉토리 내에 있어야 합니다. 이런 식으로 다크 모드와 라이트 모드에서 각각 배경을 설정해주면 모드별로 다른 배경 이미지가 나타납니다.

## 배경 색만 변경

배경 이미지가 아니라 모드별로 다른 배경색을 나타내길 원하시면 단순히 `--sidebar-bg: #1e1e1e;`에서 컬러 코드만 수정하면 됩니다. 또한, `/_sass/layout/_sidebar.scss` 파일을 수정할 필요도 없습니다.

수정 후 터미널에 bundle exec jekyll serve 명령어를 입력하여 로컬 링크에서 수정된 배경을 확인할 수 있습니다.

<!-- markdownlint-capture -->
<!-- markdownlint-disable -->

## 참고한 링크

[블로그 커스터마이징 하기 - Dodev](https://wlqmffl0102.github.io/posts/Customizing-Blogs/)
