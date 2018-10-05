---
title: SaaS - értékesítheti Azure-on keresztül |} A Microsoft Docs
description: Az előfizetés számlázási modell SaaS-alkalmazásokhoz és implementálásáról ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810665"
---
<a name="saas---sell-through-azure"></a>SaaS - értékesítheti Azure-on keresztül
=========================

Ez a cikk ismerteti az előfizetés számlázási modell, SaaS-alkalmazásokhoz és a Cloud Partner portálra az implementálásáról.


<a name="overview"></a>Áttekintés
--------

Az Azure piactér lehetővé teszi, hogy tehet közzé és értékesítheti az SaaS-ajánlatok az Azure Marketplace-en keresztül. Az ügyfeleknek akkor is terhelve keresztül az Azure közvetlenül a számlázási, és csak kapnak egy számlázási az Azure-ból az összes erőforrások és szolgáltatások azok aktiválta. A közzététel a SaaS-előfizetések rendelkezik a következő előnyökkel jár:

-   **Közzétételi egységes élményt** – Ha már SaaS-ajánlatok közzététele, vagy az Azure piactéren, a közzétételi élmény semmilyen egyéb ajánlattal ugyanazon a közzétételi portálon keresztül.
-   **Új kirakat felderítés** --közzé minden ajánlat mind az Azure Marketplace külső megjelennie, valamint az Azure Marketplace-en bővítmény alapjait az Azure Portalon látható lesz.
-   **Integrált számlázás** – mostantól minden olyan régióban, ahol az Azure ad el értékesítésére, és Azure gondoskodik az Ön számára a számlázás.
-   **Érdeklődők vonzását integrált** – most már automatikusan fogadhat érdeklődőket a CRM, a választott Azure-ból, amikor feliratkozik egy Azure-felhasználó az SaaS-szolgáltatás használata az Azure Marketplace-en.
-   **A Microsoft-értékesítők közös értékesítési** – megosztásához, a kiemelt katalógusmegjelenéshez, kétirányú érdeklődő megszerezheti, és a lehetőséget, és zárja be a közös egymás melletti Microsoft-értékesítőkkel foglalkozik.

<a name="billing-models"></a>Számlázási modellek
--------------

A jelenleg támogatott csak számlázási modell a szoftverszolgáltatások háttéreseményeinek előfizetésenként a havi fix keretösszegek díjat kell fizetni.

**Megjegyzés:** egy későbbi időpontban további számlázási modellek elérhető lehet.

Egyes SaaS-ajánlatok rendelkezhet egy vagy több csomag (például alapszintű vagy prémium). Minden csomag rendelkezik néhány alapvető metaadatokat társítva, a díj a tervhez társított együtt.

Terv meghatározásának teljes hozzáféréssel rendelkezik. Például, mi nem egy alapszintű terv jelent? A csomag határozza meg, és megadhatja a szükséges szöveg írja le a csomag közzététele részeként.

A szolgáltatás a tervhez társított díja a havi fix keretösszegek díj, amely az Azure-felhasználók fizet a szolgáltatás használatához.

### <a name="what-is-not-supported-today"></a>Mi nem támogatott jelenleg?

-   Számlázás alapján egyéni egységekre – például egy áron vehetők igénybe a kérelmek száma alapján.
-   Számlázási alapuló munkaállomás – például lehetővé teszik a felhasználók száma alapján licenceket vásárolhat Azure-felhasználók.

<a name="end-to-end-flow"></a>Teljes körű folyamatot
---------------

Az SaaS-előfizetés kínálnak a folyamat a végfelhasználói szempontból először

**Megjegyzés:** a folyamat leírásának azt feltételezi, hogy az Azure Marketplace-en "Contoso bemutató SaaS" nevű SaaS ajánlata közzétételét.

![SaaS-előfizetés felhasználói folyamat](media/saas-offer-subscription/saas-subscription-user-flow.png)

Azure-felhasználó is rendelkezhet, és az SaaS-szolgáltatás közötti kapcsolatok a következő:

-   Fedezze fel az SaaS-szolgáltatás az Azure Marketplace-en
-   Az SaaS-szolgáltatás az Azure-ban
-   Az Azure Portalról nyissa meg a SaaS-szolgáltatás
-   Egy fiók létrehozásához a SaaS-szolgáltatás és (módosítása terv és törlése) a az SaaS-fiók kezelése
-   Leiratkozás a SaaS-szolgáltatás Azure Portal használatával

<a name="discover-your-saas-service-in-azure-marketplace"></a>Fedezze fel az SaaS-szolgáltatás az Azure Marketplace-en
-----------------------------------------------

Amikor a felhasználók az Azure Portalon indítsa el az Azure Marketplace-en, akkor fogja látni "Szoftverszolgáltatás (SaaS)" nevű kategória.

![Fedezze fel az SaaS kategória menü használatával](media/saas-offer-subscription/saas-category-menu.png)

Felhasználók is kereshet az SaaS-szolgáltatás.

![SaaS-keresési felderítése](media/saas-offer-subscription/saas-cpp-search.png)

Felhasználók lehet majd tekinteni a szolgáltatást, és kattintson a **létrehozás**.

![Egy SaaS-előfizetés létrehozása](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>SaaS-szolgáltatás az Azure-ban

A felhasználó lehet majd fizessen elő az SaaS-szolgáltatás az Azure-ból.

![Feliratkozik egy SaaS-szolgáltatás](media/saas-offer-subscription/saas-subscribe-signup.png)

Amikor a felhasználó feliratkozik az SaaS-szolgáltatás, a felhasználó a következő adatokat tartalmazza.

-   --A név, hogy a felhasználók felderítéséhez vagy a SaaS-előfizetésre, az Azure-ban kezelheti.
-   Előfizetés – Az előfizetési környezetet, amelyeket be szeretne társítani az SaaS-szolgáltatás díjszabása.
-   Díjcsomag – Az SaaS service-csomag, amely kívánják-e előfizetni.

A használati feltételek dokumentum kiadásakor az ajánlat keretében nyújtott is megjelenik a felhasználó előtt a felhasználó feliratkozik az SaaS-szolgáltatás.

A felhasználó mostantól előfizethetnek a szolgáltatás kattintva **előfizetés**.
Az Azure értesítést küld a portálon az előfizetés befejeződése után.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Az Azure Portalról nyissa meg a SaaS-szolgáltatás

Majd kattintson a felhasználók **erőforrás megnyitása** megtekintéséhez és kezeléséhez az SaaS-előfizetésre.

![Megtekintése és kezelése az SaaS-példány](media/saas-offer-subscription/saas-go-to-resource.png)

A felhasználó értesítést kap, hogy azok a fiókot konfigurálni kell a Szolgáltatottszoftver-szolgáltatásban először. A számlázás akkor kezdődik, miután az SaaS-szolgáltatás értesíti az Azure számlázását, amely akkor, ha a felhasználó létrehoz egy fiókot a SaaS-szolgáltatás webhelyen elindításához.

![Az SaaS-példány konfigurálása](media/saas-offer-subscription/saas-configure-account.png)

Amikor a felhasználó kattint **fiók konfigurálása**, akkor a rendszer átirányítja az SaaS-szolgáltatás végponthoz. Az átirányítás során egy jogkivonat átadott mentén lekérdezési paramétert. Példa:

![SaaS-token-fiók](media/saas-offer-subscription/saas-account-token.png)

Jegyezze fel a token értékét. Ezt a jogkivonatot egy rövid életű, és a egy előfizetés-azonosító beszerzése az Azure-ban való fel kell oldani.

<a name="creating-a-saas-offer-subscription"></a>Létrehoz egy SaaS-ajánlat
----------------------------------

A felhasználói élményt hozhat létre, a következők két darab munka, amelyek szükségesek:

-   Csatlakozás SaaS-szolgáltatás webhelyét a Microsoft\'s SaaS API-k. Ez a dokumentum [SaaS eladása, Azure - API-k segítségével](./cloud-partner-portal-saas-subscription-apis.md) azt ismerteti, hogyan hozhat létre a kapcsolatot.  
-   Engedélyezése **értékesítheti Azure-on keresztül** a Cloud Partner portálra szóló a **technikai információ** szakaszt, és adja meg az összes konfigurációs adatait.

![SaaS új ajánlat műszaki adatok](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Az alábbi, az összes kötelező mezőt a magyarázatát a **technikai információ** szakaszban:

|  **Az ajánlat mezők**                 |  **Leírás**                                   |
|  ----------------                 |  -------------------------------------             |
| Előfizetés azonosítókat előzetes verzió          | Minden Azure-előfizetési azonosítók tesztelésére előzetes verzióban érhető el az ajánlatot, előtt a nyilvánosan elérhető. |
| Első lépések útmutató      | Az ügyfelekkel, amelyekkel csatlakozhat a SaaS-alkalmazás megosztásához irányban. Engedélyezett HTML alapvető használatát, ilyen-címkék `<p>`, `<h1>`, `<li>`stb.  |
| Kezdőlap URL-címe                  | A webhely URL-cím lesz ügyfelei számára, hogy az Azure Portalról beszerzése után megjelenni irányítja. Az URL-cím is a végpontot, amely fog kapni a kapcsolat API-k a Microsoft kereskedelmi megkönnyítése érdekében.  |
| Kapcsolat Webhook                | Az összes aszinkron esemény, amelyet a Microsoft az ügyfél nevében dolgozunk fel, küldéséhez (Példa: Azure-előfizetés érvénytelen volt), kérjük, adja meg a kapcsolat webhook. Ha még nem rendelkezik egy webhook rendszer helyben, a legegyszerűbb konfiguráció, hogy egy HTTP végpont logikai alkalmazást, amely minden, az csökkentheti a közzétett események figyelésére és majd őket megfelelően kezelni.  További információkért lásd: [hívása, eseményindító, vagy HTTP-végpontokat, a logic apps-munkafolyamatok beágyazása](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| Az Azure AD-bérlő és alkalmazás-azonosító     | Az Azure-portálon belül szükség van egy Active Directory-alkalmazást hoz létre, hogy mi is a kapcsolat ellenőrzése a kettő közötti szolgáltatások egy hitelesített kommunikáció mögött helyezkedik el. Az alábbi mezők AD-alkalmazás létrehozása, és illessze be a megfelelő bérlői azonosító és az alkalmazásazonosító megadása kötelező. |
|  |  |


Végül ha **értékesítheti Azure-on keresztül**, van egy hozzáadott című szakaszában **csomagok**. Csomagok csak akkor van szükség, ha eladása, Azure-on keresztül van kijelölve. Ez a szakasz felsorolja az adott csomagokat, és az ezekre vonatkozó árak, amely támogatja a SaaS-alkalmazását. Mai lehetővé tesszük a havi díjszabás, és lehetővé teszi, hogy az 1 - vagy a 3-hónapnyi ingyenes hozzáférést. Ezek a csomagok és árak egyeznie kell a pontos terveket és az árak, amelyek a saját SaaS-alkalmazás helyen van.
