---
title: "Az Azure AD alkalmazások elérésének kezelése |} Microsoft Docs"
description: "Ismerteti, hogyan Azure Active Directory lehetővé teszi a szervezetek számára, amely minden felhasználó számára elérhető alkalmazások megadása."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
ms.assetid: b0829f18-9e57-4107-925d-5f0457d81671
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 363478cb1bb2637a8c4f9cf36505817b163df781
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="managing-access-to-apps"></a>Az alkalmazásokhoz való hozzáférés kezelése
A folyamatban lévő kezelési, a használati kiértékelési és a jelentéskészítés továbbra is kihívást követően az alkalmazások integrálva van a szervezet identitásrendszere. Sok esetben a rendszergazdák vagy a segélyszolgálat kell venniük a alkalmazásokhoz való hozzáférés kezelése a folyamatban lévő aktív szerepet. Egyes esetekben hozzárendelés egy általános vagy részlegszintű IT-csoport végzi el. Gyakran, a hozzárendelés döntési célja, hogy az informatikai teszi előtt jóváhagyásra van szükség az üzleti döntéshozó delegálni a hozzárendelés.  Más szervezetek beruházásának egy meglévő automatikus identitások és hozzáférések felügyeleti rendszer, például a szerepköralapú hozzáférés-vezérlést (RBAC) vagy az attribútum-szerepköralapú hozzáférés-vezérlés (ABAC) integrációját. Az integráció és a szabály fejlesztési általában speciális és költséges lehet. Figyelés, vagy a jelentés készítését vagy kezelési módszerrel saját külön költséges és összetett befektetési.

## <a name="how-does-azure-active-directory-help"></a>Hogyan segíti az Azure Active Directory?
 Az Azure AD támogatja a széles körű kezelési konfigurált alkalmazások esetén a megfelelő hozzáférési házirendek automatikus, az attribútum-alapú hozzárendelés (ABAC vagy RBAC forgatókönyvek) kezdve a rendszergazdák kezelése és delegálása könnyedén eléréséhez szervezetek engedélyezése. Az Azure AD könnyen érhető el összetett házirendek egyesítése egyetlen alkalmazás több felügyeleti modellel, és még újra használhatja a portkezelési szabályok az azonos célközönséggel rendelkező alkalmazások között.

* [Felvétel új vagy meglévő alkalmazások](active-directory-enterprise-apps-manage-sso.md)

 Az Azure AD alkalmazás-hozzárendelés két elsődleges hozzárendelés mód koncentrál:

* **Egyes hozzárendelés** egy informatikai rendszergazda a címtár globális rendszergazdai jogosultságokkal rendelkező, válassza ki a felhasználói fiókok egyesével történő, és adja meg számukra hozzáférni az alkalmazáshoz.
* **Csoport-alapú hozzárendelés (fizetős csak az Azure AD)** egy informatikai rendszergazda a címtár globális rendszergazdai jogosultságokkal rendelkező rendelhet hozzá egy csoporthoz az alkalmazást. Adott felhasználók hozzáférést csatlakoznak-e valamilyen a csoport tagjai a időben próbálnak meg hozzáférni az alkalmazáshoz határozza meg. Más szóval rendszergazda hatékonyan hozhat létre egy hozzárendelési szabályt, amely meghatározza, hogy "a hozzárendelt csoport minden tagját hozzáfér az alkalmazás". A hozzárendelési beállításának használata esetén a rendszergazdák is kihasználhatja az Azure AD csoport felügyeleti beállításokat, beleértve a [dinamikus csoportok Attribútumalapú](active-directory-groups-create-azure-portal.md), külső rendszer (például a helyszíni Active Directory vagy a Workday), vagy a rendszergazda által kezelt vagy önálló-üzemeltetési felügyelt csoportokat. Egy különálló csoportot könnyen rendelhet több alkalmazás, győződjön meg arról, hogy hozzárendelés affinitás alkalmazások is használ a hozzárendelési szabályok, így csökkentve a teljes felügyelet összetettségét. Vegye figyelembe, hogy beágyazott csoportok tagságát biztonságicsoport-alapú hozzárendelés alkalmazások jelenleg nem támogatottak.

Rendszergazdák e két hozzárendelési módban érhető el minden kívánatos hozzárendelés felügyeleti módszerrel.

Az Azure ad-vel használatát és a hozzárendelés rendszer teljesen integrált része, lehetővé téve a rendszergazdák könnyen hozzárendelés állapota, a hozzárendelési hibákat és a még akkor is, használati jelentést.

## <a name="complex-application-assignment-with-azure-ad"></a>Összetett alkalmazások hozzárendelése az Azure ad szolgáltatással
Érdemes lehet olyan alkalmazások, mint a Salesforce. Számos szervezetben Salesforce elsősorban a marketing- és értékesítési szervezetek. Gyakran a marketing csapat tagjai magas privilegizált hozzáféréssel rendelkező Salesforce, amíg az értékesítési csapat tagjai csak korlátozott hozzáféréssel rendelkeznek. Sok esetben az információkkal dolgozó szakemberek széleskörű sokaságát korlátozta az alkalmazást. Ezek a szabályok alóli kivételek nehezíti a fontos információk. Általában célszerű a marketing vagy értékesítési vezetői csoportokkal egy felhasználó hozzáférést, vagy módosítsa a szerepkörök függetlenül. a általános szabályok megállapítása.

Az Azure ad-vel alkalmazások, például a Salesforce lehet előre konfigurált az egyszeri bejelentkezés (SSO) és automatizált üzembe helyezést. Ha az alkalmazás van konfigurálva, a rendszergazda létrehozásához, és rendelje hozzá a megfelelő csoportokban egyszeri művelet lehet. Ebben a példában a rendszergazda a következő hozzárendelések hajthatnak végre:

* [Dinamikus csoportok](active-directory-groups-create-azure-portal.md) automatikusan képviselő tulajdonságai, például a részleg vagy szerepkör használatával az értékesítés és marketing csapat összes tagja lehet meghatározni:
  
  * Marketing csoport összes tagja lesz rendelve a Salesforce-ban "marketing" szerepkör
  * Csoportok lesz hozzárendelve a Salesforce-ban "értékesítők" szerepkör értékesítési csoport összes tagja. További pontosítás használhatja, amelyek megfelelnek a különböző Salesforce szerepkörökhöz hozzárendelt regionális értékesítési csoportok több csoporthoz.
* Ahhoz, hogy a kivételképzési mechanizmus, az egyes szerepkörökhöz kell létrehozni egy önkiszolgáló csoportot. Például a "Salesforce marketing kivétel" csoport egy önkiszolgáló csoportkezelési hozhatók létre. A csoport a Salesforce marketing szerepkör lehet hozzárendelni, és a marketing vezetőségi tagja tehető tulajdonosai. Tagjai a marketing vezetőségi tagja volt hozzáadása vagy eltávolítása a, illesztési házirend, vagy még hagyja jóvá vagy megtagadja a hozzáférést az egyes felhasználók kérelmek való csatlakozáshoz. Ez keresztül adatokat feldolgozó megfelelő élményt tulajdonosai vagy a tagok esetében nem szükséges speciális képzési támogatott.

Ebben az esetben az összes hozzárendelt felhasználók volna lehet automatikusan hozzáférést kapnak Salesforce, hozzáadásuk különböző csoportokat a szerepkör-hozzárendelés Salesforce frissíteni kell. Felhasználók felderítése és a Microsoft alkalmazás hozzáférési panelre, Office webes ügyfelekkel, vagy még megnyitásával a szervezeti Salesforce bejelentkezési oldal férhetnek hozzá a Salesforce tudná. A rendszergazdák tudnak egyszerűen az Azure AD jelentéskészítési API használatával használati és a hozzárendelés állapotának megtekintéséhez.

A rendszergazdák is alkalmaz [Azure AD feltételes hozzáférésével](active-directory-conditional-access-azure-portal.md) hozzáférési házirendeket az egyes szerepkörök beállítása. Ezek a házirendek tartalmazhatnak, hogy a hozzáférés a vállalati környezet és még többtényezős hitelesítést vagy az eszköz eléréséhez, az access különböző esetekben kívül engedélyezett.

## <a name="how-can-i-get-started"></a>Hogyan lehet kezdjem el?
Első, ha már nem használja az Azure AD, és egy informatikai rendszergazda:

* [Próbálja ki!](https://azure.microsoft.com/trial/get-started-active-directory/) -Regisztráljon egy ingyenes 30 napos próbaverzió ma, és ezen a hivatkozáson keresztül a 5 perc múlva a első felhőalapú megoldás üzembe helyezése

Az Azure AD-funkciókat, amelyek lehetővé teszik a fiók megosztása a következők:

* [Csoport-hozzárendelés](active-directory-accessmanagement-self-service-group-management.md)
* Az Azure AD-alkalmazások hozzáadása
* Ismerkedés a hozzárendelés
* Alkalmazás-hozzárendelés – gyakori kérdések
* [Alkalmazás használati irányítópult/jelentések](active-directory-passwords-get-insights.md)

## <a name="where-can-i-learn-more"></a>Hol kaphatok további?
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Feltételes hozzáféréssel rendelkező alkalmazások védelme](active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoportkezelési felügyeleti/SSAA](active-directory-accessmanagement-self-service-group-management.md)

