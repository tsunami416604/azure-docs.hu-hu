---
title: Az Azure API Management lap vezérlői | Microsoft dokumentumok
description: Ismerje meg az Azure API Management fejlesztői portálsablonjaiban használható lapvezérlőket.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244016"
---
# <a name="azure-api-management-page-controls"></a>Az Azure API Management lap vezérlői
Az Azure API Management a következő vezérlőket biztosítja a fejlesztői portál sablonjaiban való használatra.  
  
Vezérlő használatához helyezze a kívánt helyre a fejlesztői portál sablonban. Egyes vezérlők, például az [alkalmazásműveletek](#app-actions) vezérlő, paraméterekkel rendelkeznek, ahogy az a következő példában látható:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
A paraméterek értékeit a sablon adatmodelljének részeként adják át. A legtöbb esetben egyszerűen beillesztheti a megadott példát az egyes vezérlőkhöz, hogy megfelelően működjön. A paraméterértékekről további információt az adatmodell szakaszban talál minden olyan sablonhoz, amelyben vezérlőt használhat.  

A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Fejlesztői portál sablonlap-vezérlői  
  
-   [alkalmazás-műveletek](#app-actions)  
-   [alap-bejelentkezés](#basic-signin)  
-   [lapozás-vezérlés](#paging-control)  
-   [Szolgáltatók](#providers)  
-   [keresés-ellenőrzés](#search-control)  
-   [Feliratkozás](#sign-up)  
-   [feliratkozás-gomb](#subscribe-button)  
-   [előfizetés-lemondás](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>alkalmazás-műveletek  
 A `app-actions` vezérlő felhasználói felületet biztosít a fejlesztői portál felhasználói profillapján található alkalmazásokkal való interakcióhoz.  
  
 ![alkalmazás&#45;műveletek vezérlő](./media/api-management-page-controls/APIM-app-actions-control.png "APIM-alkalmazásműveletek vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|appId|Az alkalmazás azonosítója.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `app-actions` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [Alkalmazások](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>alap-bejelentkezés  
 A `basic-signin` vezérlő a felhasználói bejelentkezési adatok gyűjtésére szolgáló vezérlőt biztosít a fejlesztői portál bejelentkezési lapján.  
  
 ![alapvető&#45;bejelentkezésvezérlés](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM alapszintű bejelentkezésvezérlés")  
  
### <a name="usage"></a>Használat  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `basic-signin` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [Bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>lapozás-vezérlés  
 A `paging-control` lapozási funkciót biztosít a fejlesztői portál oldalain, amelyek en elemek listáját jelenítik meg.  
  
 ![lapozás vezérlő](./media/api-management-page-controls/APIM-paging-control.png "APIM-lapozásvezérlés")  
  
### <a name="usage"></a>Használat  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `paging-control` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Problémalista](api-management-issue-templates.md#IssueList)  
  
-   [Terméklista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Szolgáltatók  
 A `providers` vezérlő a hitelesítésszolgáltatók kiválasztásának vezérlője a fejlesztői portál bejelentkezési lapján.  
  
 ![szolgáltatók ellenőrzése](./media/api-management-page-controls/APIM-providers-control.png "APIM-szolgáltatók ellenőrzése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `providers` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [Bejelentkezés](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>keresés-ellenőrzés  
 A `search-control` keresési funkciót a fejlesztői portál oldalain biztosít, amelyek az elemek listáját jelenítik meg.  
  
 ![keresés vezérlő](./media/api-management-page-controls/APIM-search-control.png "APIM-keresés vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `search-control` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Terméklista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Feliratkozás  
 A `sign-up` vezérlő a felhasználói profil adatainak a fejlesztői portál regisztrációs lapján történő gyűjtésére szolgáló vezérlőt biztosít.  
  
 ![regisztráció&#45;ellenőrzés](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-regisztráció vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `sign-up` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [feliratkozni](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>feliratkozás-gomb  
 Az `subscribe-button` a vezérlő a felhasználó termékre való feliratkozásához.  
  
 ![feliratkozás&#45;gomb vezérlő](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM előhívás-gomb vezérlése")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Paraméterek  
 Nincs.  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `subscribe-button` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [Termék](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>előfizetés-lemondás  
 A `subscription-cancel` vezérlő a fejlesztői portál felhasználói profillapján egy termékre szóló előfizetés lemondására szolgáló vezérlőt biztosít.  
  
 ![előfizetés&#45;a vezérlés lemondása](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-előfizetés-lemondás vezérlés")  
  
### <a name="usage"></a>Használat  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Paraméterek  
  
|Paraméter|Leírás|  
|---------------|-----------------|  
|subscriptionId|A lemondani hozandó előfizetés azonosítója.|  
|cancelUrl|Az előfizetés törli az URL-címet.|  
  
### <a name="developer-portal-templates"></a>Fejlesztői portál sablonjai  
 A `subscription-cancel` vezérlő a következő fejlesztői portálsablonokban használható:  
  
-   [Termék](api-management-product-templates.md#Product)

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.
