---
title: Az Azure API Management Lapvezérlők |} Microsoft Docs
description: További tudnivalók a Lapvezérlők fejlesztői portál sablonok az Azure API Management használható.
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
ms.openlocfilehash: da68c9b7ebbb1880e35bd60b12db9f920f51e13c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936146"
---
# <a name="azure-api-management-page-controls"></a>Az Azure API Management Lapvezérlők
Az Azure API Management a következő vezérlőelemeket biztosítja a fejlesztői használható portál sablonok.  
  
A vezérlő használatához helyezze el a kívánt helyre a fejlesztői portálon sablonban. Néhány szabályozza, mint például a [alkalmazás-műveletek](#app-actions) kontrolljához paraméterekkel rendelkezik, az alábbi példában látható módon:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 A paraméterek értékeit az adatmodell a sablon részeként átadott a. A legtöbb esetben egyszerűen illessze be a megadott példa minden vezérlőjének, ahhoz, hogy a megfelelő működéséhez. A paraméterértékek további információkért lásd: az adatszakasz modell minden sablon egy vezérlő használja.  
  
 A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Fejlesztői portálsablon Lapvezérlők  
  
-   [alkalmazás-műveletek](#app-actions)  
-   [Basic-bejelentkezés](#basic-signin)  
-   [lapozófájl-vezérlő](#paging-control)  
-   [szolgáltatók](#providers)  
-   [keresési-vezérlő](#search-control)  
-   [-előfizetés](#sign-up)  
-   [előfizetés gomb](#subscribe-button)  
-   [előfizetés-Mégse](#subscription-cancel)  
  
##  <a name="app-actions"></a> alkalmazás-műveletek  
 A `app-actions` vezérlő felhasználói felülete lehetőséget nyújt az alkalmazások a felhasználó oldalon a fejlesztői portálra való interakció.  
  
 ![alkalmazás&#45;műveletek vezérlő](./media/api-management-page-controls/APIM-app-actions-control.png "APIM alkalmazás-műveletek vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|appId|Az alkalmazás azonosítója.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `app-actions` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [Alkalmazások](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic-bejelentkezés  
 A `basic-signin` vezérlő egy felügyeletét biztosítja, a bejelentkezési oldal a fejlesztői portálra felhasználói bejelentkezési adatok gyűjtéséhez.  
  
 ![Alapszintű&#45;signin vezérlő](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-signin vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `basic-signin` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> lapozófájl-vezérlő  
 A `paging-control` lapozás a fejlesztői portálon lapokat biztosít, amelyek elemek listájának megjelenítéséhez.  
  
 ![vezérlő lapozási](./media/api-management-page-controls/APIM-paging-control.png "APIM lapozási vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `paging-control` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Problémák listája](api-management-issue-templates.md#IssueList)  
  
-   [Termékek listáját](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> szolgáltatók  
 A `providers` vezérlő vezérlőt biztosít a bejelentkezési oldalon a fejlesztői portálra hitelesítésszolgáltatókat kiválasztását.  
  
 ![szolgáltatók vezérlő](./media/api-management-page-controls/APIM-providers-control.png "APIM szolgáltatók vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `providers` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> keresési-vezérlő  
 A `search-control` keresési funkciókat biztosítja a fejlesztői portál által felügyelt oldalak felhívásainak elemek listájának megjelenítéséhez.  
  
 ![Keresés vezérlő](./media/api-management-page-controls/APIM-search-control.png "APIM keresési vezérlőelemet")  
  
### <a name="usage"></a>Használat  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `search-control` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Termékek listáját](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> -előfizetés  
 A `sign-up` vezérlő egy felügyeletét biztosítja a fejlesztői portálra a regisztrációs oldalon található felhasználói profillal kapcsolatos információk gyűjtéséhez.  
  
 ![bejelentkezési&#45;vezérlő legfeljebb](./media/api-management-page-controls/APIM-sign-up-control.png "APIM előfizetési vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `sign-up` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [feliratkozni](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> előfizetés gomb  
 A `subscribe-button` vezérlőt biztosít a felhasználót, hogy a termék előfizetés.  
  
 ![előfizetés&#45;gomb vezérlőelem](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM előfizetés gomb vezérlőelem")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `subscribe-button` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [A termék](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> előfizetés-Mégse  
 A `subscription-cancel` vezérlő vezérlőt biztosít a felhasználó oldalon a fejlesztői portálra való előfizetés lemondása.  
  
 ![előfizetés&#45;szakítsa meg a vezérlő](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM előfizetés-Mégse vezérlő")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|subscriptionId|Megszakítja az előfizetés azonosítója.|  
|CancelUrl|Az előfizetés visszavonja az URL-CÍMÉT.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonok  
 A `subscription-cancel` vezérlő a következő fejlesztői portál sablonok használható:  
  
-   [A termék](api-management-product-templates.md#Product)

## <a name="next-steps"></a>További lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).