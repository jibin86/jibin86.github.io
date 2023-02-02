---
title:  "[Project] Politics News Portal UI 제작"
# excerpt: "프로젝트 '정치적 편향도를 제공하는 뉴스 포털' 제작 과정을 담아보았다."
categories: [Front-End, Project]
tags: [HTML, Bootstrap, Project]
---
프로젝트 '정치적 편향도를 제공하는 뉴스 포털' UI 제작 과정을 담아보았다.
## 구성 개요

"Honey Politics News" 정치 뉴스 포털의 웹페이지 UI를 부트스트랩과 HTML로 개발했다. 네비게이션바는 "Home", "Group1", "Group2", "Group3", "Group4"로 구성되어 다른 섹션에 대한 링크가 포함되어 있으며, 주요 내용으로는 헤드라인을 보여주는 점보트론과 기사 제목, 기자이름, 날짜, 기사 내용이 포함되어 있다. 또한, Main content 옆에는 편향도 분석 결과와 유사한 기사 추천이 제공된다.

## 개발 과정

### 초본

포털 첫 페이지와 뉴스 기사 페이지 초본을 간단하게 구성했다.
- portal_first.html
    ![portal.html](https://user-images.githubusercontent.com/89712324/216205106-cefb4005-5387-429b-914c-2c321f9f24b3.png)


- news_first.html
    ![news.html](https://user-images.githubusercontent.com/89712324/216205337-c08d7770-c132-4330-9c1f-2428b5b541d3.png)
    

### 코드

#### portal.html - 초본

```html
<!-- portal.html -->
<!DOCTYPE html>
<html lang="en">

<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"
        integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    <title>Political News Portal</title>
</head>

<body>
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container">
            <a class="navbar-brand" href="#">Political News</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav"
                aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav ml-auto">
                    <li class="nav-item">
                        <a class="nav-link" href="#">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#">News</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#">Contact</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>

    <!-- Showcase -->
    <header class="text-center py-5 bg-primary text-white">
        <div class="container">
            <h1>Stay Up to Date with Political News</h1>
            <p class="lead">Stay informed with the latest political news from around the world.</p>
        </div>
    </header>

    <!-- Latest News -->
    <section class="py-5">
        <div class="container">
            <h2 class="text-center mb-4">Latest News</h2>
            <div class="row">
                <div class="col-md-4 mb-4">
                    <div class="card">
                        <img class="card-img-top" src="news-1.jpg" alt="News 1">
                        <div class="card-body">
                            <h5 class="card-title">News Headline 1</h5>
                            <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam viverra
                                euismod odio,</p>
                        </div>
                    </div>
                </div>
                <div class="col-md-4 mb-4">
                    <div class="card">
                        <img class="card-img-top" src="news-1.jpg" alt="News 1">
                        <div class="card-body">
                            <h5 class="card-title">News Headline 1</h5>
                            <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam viverra
                                euismod odio,</p>
                        </div>
                    </div>
                </div>
                <div class="col-md-4 mb-4">
                    <div class="card">
                        <img class="card-img-top" src="news-1.jpg" alt="News 1">
                        <div class="card-body">
                            <h5 class="card-title">News Headline 1</h5>
                            <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam viverra
                                euismod odio,</p>
                        </div>
                    </div>
                </div>
            </div><div class="row">
                <div class="col-md-4 mb-4">
                    <div class="card">
                        <img class="card-img-top" src="news-1.jpg" alt="News 1">
                        <div class="card-body">
                            <h5 class="card-title">News Headline 1</h5>
                            <p class="card-text">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nam viverra
                                euismod odio,</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</body>

</html>
```

#### news.html - 초본

```html
<!-- news.html -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link
      rel="stylesheet"
      href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css"
      integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm"
      crossorigin="anonymous"
    >
    <link rel="stylesheet" href="style.css">

    <title>Politics News Portal</title>
  </head>
  <body>
    <!-- Navigation -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
      <div class="container">
        <a class="navbar-brand" href="#">Politics News Portal</a>
        <button
          class="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav ml-auto">
            <li class="nav-item">
              <a class="nav-link" href="#">Home</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#Type1">Type1</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">Type2</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">Type3</a>
            </li>
            <li class="nav-item">
              <a class="nav-link" href="#">Type4</a>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Jumbotron -->
    <div class="jumbotron jumbotron-fluid bg-primary text-white">
      <div class="container">
        <h1 class="display-4">Politics News Portal</h1>
        <p class="lead">Stay updated with the latest political news and events around the world</p>
      </div>
    </div>

    <!-- Main Content -->
    <div class="container my-5">
      <!-- News Section -->
      <h2 class="text-primary">Latest News</h2>
    </div>
    <div class="container my-5">
      <!-- News Section -->
      <h2 class="text-primary" id="Type1">Type1</h2>
    </div>
  </body>
</html>
```

- 화면이 줄어들었을 때 토글 메뉴가 활성화 되도록 하였다.
    
    ```html
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav"
                    aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
    </button>
    ```
    
    ```html
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js"
            integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN"
            crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js"
            integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q"
            crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js"
            integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl"
            crossorigin="anonymous"></script>
    ```
    

- 위로 가도록하는 버튼을 생성하고 버튼을 누르면 네비게이션바로 향하도록 했다.
    
    ```html
    <button style="position: fixed; bottom: 100px; right: 60px; background-color: #007bff; color: #fff; border: none; padding: 10px 20px; border-radius: 5px; cursor: pointer;" onclick="document.location.href = '#top';">Go Up</button>
    ```
    
    ```html
    <nav class="navbar navbar-expand-lg navbar-light bg-light" id="top" >
    ```
    

- 패딩, 마진, 열 간격, 비율 등을 조정해가며 최적의 위치를 찾았다.
    - Title, 신문사, 기자이름, 날짜 부분
    
    ```html
    <div class="bg-primary text-white pt-5 pb-3 ">
            <div class="container my-4">
                <div class="row" style="float: none; margin: 100 auto;">
                    <div class="col-md-12" style="float: none; margin: 0 auto;">
                        <strong>중앙일보</strong>
                        <h2 class="py-4">“김정은, 트럼프에 ‘로켓맨’은 OK, ‘리틀’은 NO”</h2>
                        <small>배재성 기자 2023-01-24 23:01:00</small>
                    </div>
                </div>
            </div>
        </div>
    ```
    
- 정치 편향도 분석 결과  - 정치 편향도를 나타내는 bar와 텍스트를 구현할 때 정말 애먹었다.. wrap으로 두 번 묶고 텍스트, 바, 텍스트에 대한 너비를 설정했다.
    
    ```html
    <h5 class="card-title">정치 편향도 분석 결과</h5>
    <div style="display: grid;
        grid-template-columns: 70px auto 70px;">
        보수 60%
        <div style="display: grid; height: 20px;
        grid-template-columns: 60% 40%">
            <bar style="background-color: rgb(231, 71, 71); padding: 2px; margin-left: 5px;
                        border-top-left-radius: 20px; border-bottom-left-radius: 20px;">
            </bar>
            <bar style=" background-color: rgb(18, 154, 18); padding: 2px; margin-right: 5px;
                        border-top-right-radius: 20px; border-bottom-right-radius: 20px;">
            </bar>
        </div>진보 40%
    </div>
    ```
    

- 추천 뉴스 - 포털페이지에 있는 개별 뉴스 부분을 따왔다.
    
    ```html
    <h5 class="card-title">추천 뉴스</h5>
    <div class="row">
        <div class="col mb-2">
            <div class="card">
                <div class="card-body">
                    <div class="card-title font-weight-bold">북한, 코로나19 극복 다큐 방영…“80여일만 종식 기적”</div>
                    <p class="card-text small">김정은 북한 국무위원장이 과거 도널드 트럼프 미국 전 대통령이 그에게...</p>
                </div>
            </div>
        </div>
    </div>
    ```

## 결과 UI

![portal.html](https://user-images.githubusercontent.com/89712324/216181248-dfca5b35-b78d-4251-a9c9-964bebef547e.png)<br>
portal.html
<br>
![news.html](https://user-images.githubusercontent.com/89712324/216204568-e9dcaa24-f2f5-44cd-b45d-1f77e28693e8.png)



## 소감

허니 뉴스 포털 프론트를 완성했다. 이전에 javascript, jquery, xml을 사용해서 뉴스 기사, 뉴스 목록을 만들어 본 경험이 있었는데 부트스트랩으로 웹 제작은 이번이 처음이었다. 그리고 디자인 창작에는 정말 감각이 없었지만 열심히 했다.  하루 종일 코딩하고 아침먹고 코딩하고 점심먹고 코딩하고 저녁먹고 바로 글을 쓰는 지금이다. 생각보다 오래걸렸지만 결과가 만족스럽게 나와 뿌듯하다ㅎㅎ

또한 HTML, CSS, javascript 등 기본 지식이 있어서 그런지 생각보다 어렵지 않았다. 오랜만에 section, header를 다뤄봤고 특히 마진과 패딩, 컬럼 간격에 대해서는 정말 끝장을 봤다. 나중에 까먹을 수도 있기에 나를 위해서, 또한 정보가 필요한 사람들을 위해서 까먹기 전에 오늘 작성한 코드에 대한 간략한 설명과 함께 정리를 해보았다.