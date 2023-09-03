---
weight: 1
title: "架構與技術說明"
date: 2023-08-23T07:00:00+08:00
lastmod: 2023-08-31T07:00:00+08:00
draft: false
---
#### 專案介紹
本專案主要使用Go語言撰寫的Web應用：
[[**ChatGPT Shared Chat**]](../shared-form/)

可以管理ChatGPT導出的分享連結，讓使用者可以方便的管理和分享這些ChatGPT生成的連結。

#### 主要特色
1. **前後端分離架構**: 前端使用Hugo框架來管理網站的內容，而後端則使用Gin框架來處理CRUD(建立、讀取、更新、刪除)資料API。這種分離的架構讓前端的呈現變得更流暢，而後端的資料處理也更可靠和高效。整體而言，這樣的設計可以提升網站的效能、可維護性，並且也更容易進行擴展和迭代。
2. **自動化單元測試**: 透過GitHub Actions在每次push時自動執行backend的單元測試，這可以確保每次的更動都不會對已經存在的功能產生不期望的影響。
3. **自動化部署**: 使用GitHub Actions自動進行HUGO的發版和部署，降低手動發版的可能錯誤，並且提升了部署的效率。

### 後端使用技術
[後端專案github repository: [**YuanData/SharedBoard**]](https://github.com/YuanData/SharedBoard)
1. [Gin-gonic](https://github.com/gin-gonic/gin): Gin是用Go語言編寫的web框架，它是高效且靈活的。
2. [sqlc](https://github.com/sqlc-dev/sqlc): 是用Go實作之SQL Compiler，可以使用SQL語句來生成類型安全的Go語言程式碼的工具。
3. [postgres](https://www.postgresql.org/): 使用postgres作為資料庫系統，它是功能強大且穩定的開源資料庫系統。
4. [go migrate](https://github.com/golang-migrate/migrate): 是用Go語言撰寫的資料庫遷移工具，可以方便的進行資料庫的版本控制。
5. [go mockgen](https://github.com/golang/mock): 是用於生成Go語言的mock物件的工具，可以方便的進行單元測試。
6. [go test](https://pkg.go.dev/testing): Go語言的內建測試工具。

### 前端使用技術
[前端專案github repository: [**YuanData/SharedForm**]](https://github.com/YuanData/SharedForm)
1. [HUGO](https://gohugo.io/): HUGO是用Go語言撰寫的靜態網站生成器，這裡使用HUGO的template系統來進行版面的元件控制。
2. **AJAX**: 透過原生Javascript語法控制互動介面，對接API進行非同步的資料存取。

### DevOps使用技術
1. [Docker](https://www.docker.com/): 利用容器技術，可以方便的進行應用程式的擴展和部署，這裡使用docker-compose up命令來完成整個架構的部署。
2. [GitHub Actions](https://github.com/features/actions): 這是一個CI/CD(持續整合/持續部署)工具，可以自動執行單元測試，並且自動部署到GitHub Pages site。

### 架構圖
{{< mermaid >}}

graph LR

  B1[fa:fa-database sqlc]
  B2[fa:fa-database postgres]
  B3[fa:fa-exchange go migrate]
  B4[fa:fa-cogs go mockgen]
  B5[fa:fa-check go test]

  F1[fa:fa-code HUGO]
  F2[fa:fa-refresh AJAX]

  D1[fa:fa-ship Docker]
  D2[fa:fa-file GitHub Actions]

  C[fa:fa-user Client]
  S[fa:fa-server Gin API Server]

  S --- B1
  S --- B2
  S --- B3
  S --- B4
  S --- B5

  C --- F1
  F1 --- F2
  F2 --- S

  S --- D1
  D1 --- D2

{{< /mermaid >}}
