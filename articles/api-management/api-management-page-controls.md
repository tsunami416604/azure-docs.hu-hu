---
title: Az Azure API Management Lapvezérlők |} A Microsoft Docs
description: További információ a használható az Azure API Management fejlesztői portál sablonjainak Lapvezérlők.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445362"
---
# <a name="azure-api-management-page-controls"></a>Az Azure API Management Lapvezérlők
Az Azure API Management a következő vezérlőelemeket biztosítja használható a fejlesztői portál sablonjainak.  
  
Szeretne használni egy vezérlőelemet, helyezze el a kívánt helyre a fejlesztői portál sablonban. Néhány vezérlőelemet, mint például a [app-műveletek](#app-actions) vezérlőelemet, paraméterekkel rendelkezik, az alábbi példában látható módon:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 A paraméterek értékeit az átadott az adatmodell a sablon részeként. A legtöbb esetben egyszerűen másolhatja a megadott példa minden vezérlőjének, hogy megfelelően működjön. További információ a paraméter értékét az adatszakasz modell, amelyben egy vezérlőelem használható minden sablon láthatja.  
  
 Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Fejlesztői portál sablon Lapvezérlők  
  
-   [App-műveletek](#app-actions)  
-   [Basic-bejelentkezés](#basic-signin)  
-   [lapozófájl-vezérlő](#paging-control)  
-   [Szolgáltatók](#providers)  
-   [Keresés-vezérlő](#search-control)  
-   [Regisztráció](#sign-up)  
-   [előfizetés button](#subscribe-button)  
-   [előfizetés-Mégse](#subscription-cancel)  
  
##  <a name="app-actions"></a> App-műveletek  
 A `app-actions` vezérlőelem egy felhasználói felületet biztosít a felhasználói profil oldalához a fejlesztői portál az alkalmazások használatához.  
  
 ![alkalmazás&#45;műveletek vezérlő](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-műveletek vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|appId|Az alkalmazás azonosítója.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `app-actions` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [Alkalmazások](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic-bejelentkezés  
 A `basic-signin` vezérlőelem egy vezérlőt biztosít a fejlesztői portál bejelentkezési oldala a felhasználói bejelentkezési adatok gyűjtéséhez.  
  
 ![Alapszintű&#45;bejelentkezési vezérlő](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-bejelentkezési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `basic-signin` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> lapozófájl-vezérlő  
 A `paging-control` lapozási funkciókat biztosít a fejlesztői portál oldalainak, amely elemek listájának megjelenítéséhez.  
  
 ![lapozófájl-vezérlő](./media/api-management-page-controls/APIM-paging-control.png "APIM lapozása vezérlés")  
  
### <a name="usage"></a>Használat  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `paging-control` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [API-k listájából](api-management-api-templates.md#APIList)  
  
-   [A probléma listája](api-management-issue-templates.md#IssueList)  
  
-   [Termékek listáját](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> Szolgáltatók  
 A `providers` vezérlőelem egy vezérlőt biztosít a hitelesítésszolgáltatók, a fejlesztői portál bejelentkezési oldala a kiválasztásához.  
  
 ![szolgáltatók vezérlő](./media/api-management-page-controls/APIM-providers-control.png "APIM-szolgáltatók vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `providers` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> Keresés-vezérlő  
 A `search-control` keresési funkciókat biztosít a fejlesztői portál oldalainak, amely elemek listájának megjelenítéséhez.  
  
 ![Keresés vezérlő](./media/api-management-page-controls/APIM-search-control.png "APIM keresési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `search-control` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [API-k listájából](api-management-api-templates.md#APIList)  
  
-   [Termékek listáját](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> Regisztráció  
 A `sign-up` vezérlőelem egy vezérlőt biztosít a fejlesztői portálon a regisztrációs oldalon a felhasználói profil adatainak gyűjtéséhez.  
  
 ![bejelentkezési&#45;vezérlő legfeljebb](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-előfizetési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `sign-up` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [Regisztráció](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> előfizetés button  
 A `subscribe-button` egy vezérlőt biztosít egy felhasználót egy termékre előfizetett.  
  
 ![Feliratkozás&#45;gomb típusú vezérlők](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM előfizetés button vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `subscribe-button` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [A termék](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> előfizetés-Mégse  
 A `subscription-cancel` vezérlőelem egy vezérlőt biztosít a felhasználói profil oldalához a fejlesztői portál egy termék-előfizetés lemondása.  
  
 ![előfizetés&#45;vezérlő Mégse](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM előfizetés-Mégse vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|subscriptionId|Megszakítja az előfizetés azonosítója.|  
|CancelUrl|Az előfizetés megszakítja az URL-CÍMÉT.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjainak  
 A `subscription-cancel` vezérlő a következő fejlesztői portál sablonjainak használni:  
  
-   [A termék](api-management-product-templates.md#Product)

## <a name="next-steps"></a>További lépések
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).