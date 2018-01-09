---
title: Mi az Azure Security Center? |} Microsoft Docs
description: "Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 50a54b8d2a73807aa9a0217f7ccf971b8c516494
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center hibrid felhő-munkaterhelések egységes biztonsági kezelése és az advanced threat protection lehetővé teszi. A Security Center biztonsági szabályzatokat alkalmazhat a munkaterhelések, korlátozza a fenyegetéseket, és észleléséhez és válaszol a támadásokkal szemben.

Miért használja a Security Centert?

- **Csoportházirend-kezelés központi** – győződjön meg arról a vállalat megfelelőségi és szabályozási biztonsági követelmények által központilag a hibrid felhő munkaterhelések között a biztonsági házirendek kezelése.
- **A folyamatos biztonsági értékelést** – a biztonsági gépek, a hálózatok, a tárolási szolgáltatásait, és a potenciális biztonsági problémákat felderítéséhez alkalmazások figyeléséhez.
- **Végrehajthatóként javaslatok** – biztonsági rések javítása előtt is kihasználható a támadók a rangsorolt és végrehajthatóként biztonsági javaslatok.
- **Speciális felhő védelmekkel** – fenyegető veszélyek az csak az elérhető felügyeleti portokat és engedélyezése csökkenti a virtuális gépeken futó vezérlő alkalmazásokhoz.
- **Riasztások és incidensek prioritása** -arra utalnak, a kritikus fontosságú fenyegetések először a rangsorolt biztonsági riasztások és incidensek.
- **Integrált biztonsági megoldások** - gyűjtése, keressen, és különböző forrásokból, beleértve az összekapcsolt partnermegoldások biztonsági adatok elemzését.

A **Security Center – áttekintés** révén a biztonsági állapotát az Azure és az-Azure munkaterhelések, így felderítése és a munkaterhelések biztonsági értékeléséhez és azonosítása és a kockázatnak a mérséklése gyors képet. A beépített irányítópult a biztonsági riasztások és biztonsági rések, figyelmet igénylő azonnali betekintést nyújt.

![Áttekintés][1]

## <a name="centralized-policy-management"></a>Központi Csoportházirend kezelése
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. A Security Center házirendek meghatározása és testre is szabhatja azokat a típusú vagy az adatok érzékenységének.

A Security Center-szabályzatok a következő összetevőkből állnak:

- **Adatgyűjtés**: meghatározza, hogy a kiépítési ügynök és a biztonsági [adatgyűjtés](security-center-enable-data-collection.md) beállításait.
- **Biztonsági házirend**: határozzák meg, amely szabályozza a Security Center figyelők és szerkesztésével javasolja a [biztonsági házirend](security-center-policies.md).
- **E-mail értesítések**: meghatározza, hogy a biztonsági névjegyeket és [értesítési e-mail](security-center-provide-security-contact-details.md) beállításait.
- **IP-címek**: szabad határozza meg, vagy Standard [kijelölés árképzési](security-center-pricing.md). A réteg úgy dönt, határozza meg, mely a Security Center funkciók érhetők el az erőforrások hatókörében.

![Biztonsági házirend][2]

Lásd: [biztonsági házirendek – áttekintés](security-center-policies-overview.md) további információt.

## <a name="continuous-security-assessment"></a>A folyamatos biztonsági értékelést
A Security Center elemzi a számítási erőforrásokat, a virtuális hálózatok, a tárolás és a adatszolgáltatások és az alkalmazások biztonsági állapotát. Folyamatos assessment segít felderíteni a potenciális biztonsági problémákat, például a rendszer a hiányzó biztonsági frissítések vagy kitett hálózati portok. Válasszon ki egy csempét a megelőzési szakaszban, ha további információkat, beleértve az erőforrások és a biztonsági rések azonosított listáját.

![Biztonsági állapotfigyelés][3]

Lásd: [biztonsági állapotfigyelés](security-center-monitoring.md) további információt.

## <a name="actionable-recommendations"></a>Végrehajthatóként javaslatok
A Security Center elemzi a potenciális biztonsági hiányosságok azonosítása az Azure és az Azure-erőforrások biztonsági állapotát. Rangsorolt biztonsági javaslatok listája végigvezeti a biztonsági kérdések kielégíteni.

![Javaslatok][4]

Lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md) további információt.

## <a name="just-in-time-vm-access"></a>Virtuális gépek igény szerinti elérése
A hálózati támadási felületének csökkentése JIT, Azure virtuális gépeken, ami rendkívüli mértékben csökkenti a találgatásos és más hálózati támadásoknak való kitettség kezelőportjai ellenőrzött elérésére.

![Virtuális gépek igény szerinti elérése][5]

Adja meg, hogyan felhasználók csatlakozhatnak virtuális gépek szabályait. Szükség esetén hozzáférést igényelhet a Security Center vagy a PowerShell segítségével. Mindaddig, amíg a kérelem megfelel a szabályoknak, automatikusan hozzáférés kért alkalommal.

Lásd: [JIT-virtuális gép hozzáférés kezelése](security-center-just-in-time.md) további információt.

## <a name="adaptive-application-controls"></a>Adaptív alkalmazásvezérlők
Kártevő szoftverek és más nemkívánatos alkalmazások blokkolása a bizonyos Azure munkaterhelések igazodó és gépi tanulási technológiával engedélyezett javaslatok alkalmazásával.

![Adaptív alkalmazásvezérlők][6]

Tekintse át, és kattintson a Security Center által generált ajánlott alkalmazás engedélyezett szabályok vonatkoznak, vagy már be van állítva szabályok szerkesztése.

Lásd: [adaptív alkalmazás vezérlők](security-center-adaptive-application.md) további információt.

## <a name="prioritized-alerts-and-incidents"></a>Rangsorolt riasztásokat és incidensek
A Security Center speciális elemzés és globális fenyegetésfelderítési adataival használja a bejövő támadások észlelésére és utáni megsértik a tevékenység. Riasztások prioritása és csoportosítva incidensek, segítve a koncentrálhat, a kritikus fontosságú fenyegetések először. Létrehozhat saját egyéni biztonsági riasztásokat is.

![Rangsorolt riasztásokat és incidensek][7]

Gyorsan hatókör és vizuális, interaktív vizsgálat nyújthassunk támadás hatásának értékelése, és használja az előre definiált vagy alkalmi lekérdezések mélyebb feltárási biztonsági adatok.

Lásd: [kezelése és a biztonsági riasztásokra való reagálásról](security-center-managing-and-responding-alerts.md) további információt.

## <a name="integrate-your-security-solutions"></a>A biztonsági megoldások integrálása
Gyűjtése, keressen, és különböző forrásokból, összekapcsolt partneri megoldások, például a hálózati tűzfalak és más Microsoft-szolgáltatásokban, beleértve a Security Center biztonsági adatok elemzését.

![Biztonsági megoldások integrálása][8]

Lásd: [integrálhatja biztonsági megoldásokat](security-center-partner-integration.md) további információt.

## <a name="next-steps"></a>További lépések

- A kezdéshez a Security Center szüksége van a Microsoft Azure-előfizetéssel. Ha nem rendelkezik előfizetéssel, regisztrálhat az egy [ingyenes próbaverzió](https://azure.microsoft.com/free/).
- A Security Center az ingyenes tarifacsomag engedélyezve van Azure-előfizetéséhez. A speciális biztonsági felügyeleti és a fenyegetések az észlelési képességek kihasználásához, frissítenie kell a Standard tarifacsomag. A standard szint 60 napig ingyenesen használható. Tekintse meg a [árképzést ismertető oldalra a Security Center](https://azure.microsoft.com/pricing/details/security-center/) további információt.
- Ha készen áll a Security Center szabványos engedélyezése, a [gyors üzembe helyezés: bevezetni a Security Center Standard Azure-előfizetése](security-center-get-started.md) végigvezeti a lépéseken.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
