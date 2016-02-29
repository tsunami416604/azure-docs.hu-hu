<properties
  pageTitle="Mobile Apps 和行動服務中的用戶端和伺服器 SDK 版本控制 | Azure App Service"
  description="列出適用於行動服務和 Azure Mobile Apps 的用戶端 SDK 和與伺服器 SDK 版本的相容性"
  services="app-service\mobile"
  documentationCenter=""
  authors="lindydonna" 
  manager="dwrede"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="12/15/2015"
  ms.author="donnam"/>

# Mobile Apps 和行動服務中的用戶端和伺服器版本控制

Azure 行動服務的最新版本是 **行動應用程式** Azure 應用程式服務功能。 

<!-- Azure App Service offers a number of platform benefits over Mobile Services, including continuous integration and deployment, staging lots, and VNET support.
 -->

行動應用程式的用戶端與伺服器 Sdk 會原本基礎行動服務中，但它們是 *不* 彼此相容。 也就是說，您必須使用 *行動應用程式* 用戶端 SDK 與 *行動應用程式* 伺服器 SDK，同樣地， *行動電話服務*。 您可以透過用戶端和伺服器 SDK 所使用的特殊標頭值 `ZUMO-API-VERSION` 來強制執行。

注意: 當本文件是指 *行動電話服務* 後端，它不一定需要行動服務代管。 您就可以移轉不變更任何程式碼的情況下執行應用程式服務的行動服務，但服務仍會使用 *行動電話服務*  SDK 版本。

若要深入了解移轉至應用程式服務不需要變更任何程式碼，請參閱文章 [Migrate a Mobile Service to Azure App Service]。

## 標頭規格

鍵值 `ZUMO-API-VERSION` 可以在 HTTP 標頭或查詢字串中指定。 值是在表單中的版本字串 **x.y.z**。

例如：

取得 https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## 選擇不進行版本檢查

您可以選擇不要將值設定為檢查版本 **true** 應用程式設定 **MS_SkipVersionCheck**。 在 web.config 中或在 Azure 入口網站的 [應用程式設定] 區段中都可指定這項設定。

> [AZURE.NOTE] 有一些行動服務與行動應用程式，特別是在離線同步處理、 驗證和推播通知的區域之間的行為變更。 您應該在完成測試之後才選擇不要進行版本檢查，確保這些行為的變更不會影響您的 app 功能。

## 所有版本的相容性摘要

下圖顯示所有的用戶端和伺服器類型之間的相容性。 後端是做為其中一個行動 classfied **服務** 或 Mobile **應用程式** server SDK，它會使用為基礎。

|                           | **行動服務** Node.js 或.NET | **行動應用程式** Node.js 或.NET |
| ----------                | -----------------------             |   ----------------              |
| [行動服務用戶端] | 確定                                  | 發生錯誤時 *                         |
| [Mobile Apps 用戶端]     | 發生錯誤時 *                             | 確定                              |

可以藉由指定控制 \*This **MS_SkipVersionCheck**。


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. --> 

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>行動服務用戶端和伺服器

下表中的用戶端 Sdk 會相容於 **行動電話服務**。

注意: 行動服務用戶端 Sdk *不* 傳送標頭值 `ZUMO-API-VERSION`。 如果服務收到此標頭或查詢字串值，將會傳回錯誤，除非您如上面所述明確選擇不要進行檢查。

### <a name="MobileServicesClients"></a> 行動 *服務* 用戶端 Sdk

| 用戶端平台                   | 版本                                                                   | 版本標頭值 |
| -------------------               | ------------------------                                                  | -------------------  |
| 受管理的用戶端 (Windows、Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/a                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/a                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/a                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/a     |

### 行動 *服務* server Sdk

| 伺服器平台  | 版本                                                                                                        | 接受的版本標頭 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* 版本 1.0.x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | * * 沒有版本標頭 * * |
| Node.js          | (即將推出)                        | **任何版本標頭** |

<!-- TODO: add Node npm version -->

### 行動服務後端的行為

| ZUMO-API-VERSION | MS_SkipVersionCheck 的值 | Response |
| ---------------- | ---------------------------- | -------- |
| 未指定    | 任意                          | 200 - 確定 |
| 任何值        | True                         | 200 - 確定 |
| 任何值        | False/未指定          | 400 - 不正確的要求 | 

## <a name="2.0.0"></a>Azure 行動應用程式用戶端和伺服器

### <a name="MobileAppsClients"></a> 行動 *應用程式* 用戶端 Sdk

版本檢查引進了從下列版本的用戶端 SDK 的 **Azure Mobile Apps**:

| 用戶端平台                   | 版本                   | 版本標頭值 |
| -------------------               | ------------------------  | -----------------    |
| 受管理的用戶端 (Windows、Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### 行動 *應用程式* server Sdk

下列伺服器 SDK 版本包含版本檢查：

| 伺服器平台  | SDK                                                                                                        | 接受的版本標頭 |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node.js          | [azure-mobile-apps 版本 1.0-beta1 (或更新版本)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### Mobile Apps 後端的行為

| ZUMO-API-VERSION | MS_SkipVersionCheck 的值 | Response |
| ---------------- | ---------------------------- | -------- |
| x.y.z 或 Null    | True                         | 200 - 確定 |
| Null             | False/未指定          | 400 - 不正確的要求 |
| 1.x.y            | False/未指定          | 400 - 不正確的要求 |
| 2.0.0-2.x.y      | False/未指定          | 200 - 確定 |
| 3.0.0-3.x.y      | False/未指定          | 400 - 不正確的要求 |


## 後續步驟

- [將行動服務移轉至 Azure App Service]


[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Migrate a Mobile Service to Azure App Service]: app-service-mobile-migrating-from-mobile-services.md


