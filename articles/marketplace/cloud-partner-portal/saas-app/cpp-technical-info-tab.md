---
title: Azure SaaS alkalmazás ajánlat műszaki konfigurálása |} A Microsoft Docs
description: Technikai információ konfigurálja a SaaS-alkalmazás ajánlat az Azure Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: e247cedb732bb1290631ada4c6d423f1b3ed0dbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197112"
---
# <a name="saas-application-technical-info-tab"></a>SaaS-alkalmazás technikai információ lapon

A technikai információ lapon találhatók a műszaki konfigurációs képernyő. Ezen a képernyőn válassza ki a SaaS-alkalmazás (alkalmazás) hoz létre, és hogyan biztosított az ügyfelek számára az alkalmazás konfigurálása.

![Technikai konfigurációs űrlapján](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>Technikai konfigurációs űrlapján

Az űrlap 2 mezőt tartalmaz: Termék- és a művelet meghívásához.

### <a name="product-field"></a>Termék mező

A következő kirakattípus mindkét megadhat egy SaaS-alkalmazás:
- Kiválasztásával üzleti felhasználók a **ajánlati** lehetőséget.
- Egy informatikai rendszergazda felhasználó kiválasztásával **értékesítsen Rajuk keresztül a Microsoft**.
Segítségével eldöntheti, hogy milyen típusú SaaS-alkalmazásokat fejleszt, olvassa el [kirakat kijelölés ismertetése](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).

#### <a name="sell-through-microsoft"></a>Értékesítsen rajuk keresztül a Microsoft
A felhasználói élményt hozhat létre, konfigurálnia kell az alábbi adatokra:

- Az SaaS-szolgáltatás webhelyen csatlakozzon a Microsoft SaaS API-kkal. A [SaaS eladása, Azure-ban – API-k segítségével](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) a cikk azt ismerteti, hogyan hozhat létre a kapcsolatot.
- Eladása, Cloud Partner portálra szóló Azure-on keresztül engedélyezi a műszaki konfigurációs képernyőn, és adja meg a szükséges információkat. Ez a számlázási modell, és hogyan megvalósított kapcsolatos további információkért lásd: [SaaS – értékesítheti Azure-on keresztül](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions#overview).

 ![Értékesítsen rajuk keresztül Microsoft űrlap](./media/saas-techinfo-sellthrough-ms.png)

A következő táblázat ismerteti a kötelező mezőket a Microsoft értékesítési.

|  **Mező neve**   |  **Leírás**  |
|  ---------------  |  ---------------  |
|    Előfizetés azonosítókat előzetes verzió               |    Minden Azure-előfizetési azonosítók tesztelésére előzetes verzióban érhető el az ajánlatot, előtt a nyilvánosan elérhető.               |
|     Első lépések útmutató              |   Az ügyfelekkel, amelyekkel csatlakozhat a SaaS-alkalmazás megosztásához irányban. Alapszintű HTML-címkéket engedélyezettek, például: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;stb.                |
|    Kezdőlap URL-címe  |   A webhely URL-cím lesz ügyfelei számára, hogy az Azure Portalról beszerzése után megjelenni irányítja. Az URL-cím is a végpontot, amely fog kapni a kapcsolat API-k a Microsoft kereskedelmi megkönnyítése érdekében.                |
|  Kapcsolat Webhook    |  Az összes aszinkron esemény, amelyet a Microsoft küld Önnek a vásárló nevében (például: Azure-előfizetés felment érvénytelen), kérjük, adja meg a kapcsolat webhook. Ha még nem rendelkezik egy webhook rendszer helyben, a legegyszerűbb konfiguráció, hogy egy HTTP végpont logikai alkalmazást, amely minden, az csökkentheti a közzétett események figyelésére és majd őket megfelelően kezelni. További információkért lásd: <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">hívása, eseményindító, vagy HTTP-végpontokat, a logic apps-munkafolyamatok beágyazása</a>                |
|  Az Azure AD-bérlő és alkalmazás-azonosító      |   Az Azure-portálon belül szükség van egy Active Directory-alkalmazást hoz létre, hogy mi is a kapcsolat ellenőrzése a kettő közötti szolgáltatások egy hitelesített kommunikáció mögött helyezkedik el. Az alábbi mezők AD-alkalmazás létrehozása, és illessze be a megfelelő bérlői azonosító és az alkalmazásazonosító megadása kötelező.               |


Végül ha **értékesítsen Rajuk keresztül a Microsoft**, van egy másik új ajánlat eseménykifejezést **csomagok**. 

A [csomagok lapján](./cpp-plans-tab.md) felsorolja az adott csomag és az ezekre vonatkozó árak, amelyek a SaaS-alkalmazás támogatja. Mai lehetővé tesszük a havi díjszabás, és lehetővé teszi, hogy az 1 - vagy a 3-hónapnyi ingyenes hozzáférést. Ezek a csomagok és árak egyeznie kell a pontos terveket és az árak, amelyek a saját SaaS-alkalmazás helyen van.

>[!NOTE] 
>Csomagok csak akkor van szükség, ha úgy dönt, hogy a Microsoft értékesítési.

### <a name="call-to-action-field"></a>Hívás művelet mező

Műveletmező hívása lehetővé teszi az üzenetet, amely akkor jelenik meg az ajánlat beszerzési gombon. A következő lehetőségek közül választhat:

- Ingyenes – Ha ezt a lehetőséget választja, meg kell adnia egy próba URL-címet, ahol ügyfelek révén ingyenesen juthatnak az SaaS-alkalmazáshoz. Például:https://contoso.com/trial
- Ingyenes próbaverzió – Ha kiválasztjuk az ezt a beállítást kéri, adja meg egy próba URL-címet, ahol ügyfelek révén ingyenesen juthatnak az SaaS-alkalmazáshoz. Például:https://contoso.com/trial
- Megkeresést kérek

Művelet beállításainak hívása kapcsolatos további információkért lásd: válassza a közzététel lehetőséget.

## <a name="next-steps"></a>További lépések

- [Csomagok lapon (nem kötelező)](./cpp-plans-tab.md)
- [Csatorna adatai lap](./cpp-channel-info-tab.md)
