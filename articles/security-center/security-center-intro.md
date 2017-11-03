---
title: "Azure Security Center bemutatása |} Microsoft Docs"
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
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Az Azure Security Center bemutatása
Információk az Azure Security Centerről, annak főbb funkcióiról és működéséről.

## <a name="what-is-azure-security-center"></a>Mi az az Azure Security Center?
Az Azure Security Center egységes biztonsági kezelése és az advanced threat protection az Azure, a helyszíni és a többi felhőből futó számítási feladatok biztosít.  Hibrid felhő-munkaterhelések, aktív védelmet, amely csökkenti a fenyegetéseket, és intelligens észlelési segítséget nyújtanak a gyorsan fejlődő cyberattacks tartani át és szabályozhatja biztosítja.

A Security Center áttekintése révén a biztonsági állapotát az Azure és az-Azure munkaterhelések, gyors képet, így felderítése és a munkaterhelések biztonsági értékeléséhez és azonosítása és a kockázatnak a mérséklése.

![Áttekintés](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>Miért használja a Security Centert?

**Egyesített figyelemmel kísérelhetik és vezérelhetik**

- **Biztonsági állapotát megértéséhez hibrid munkaterhelések között**. Kezeli a biztonságot az összes a hibrid felhő-munkaterhelések – helyszíni Azure és más felhőplatformokkal – egyetlen konzolon. Beépített irányítópultok betekintést azonnali figyelmet igénylő biztonsági problémákat.
- **Felhő-munkaterhelések láthatósága**. Lépést tartani a sűrűn módosuló felhő-munkaterhelések. Automatikus észlelését, és a bevezetni új erőforrások létrehozott Azure-előfizetését.
- **Csoportházirend-kezelés központi**. Központilag a hibrid felhő munkaterhelések között a biztonsági házirendek kezelése biztosíthatja a vállalat megfelelőségi vagy jogszabályi biztonsági követelményeinek.
- **Biztonsági adatok számos más forrásból**. Gyűjtése, keresése és különböző forrásokból, beleértve az összekapcsolt partneri megoldások, például a hálózati tűzfalak és más Microsoft-szolgáltatásokban biztonsági adatok elemzését. 
- **Integráció a meglévő biztonsági munkafolyamatok**. Hozzáférési integrálása és REST API-k használata a meglévő eszközeivel és eljárásaival csatlakozáshoz biztonsági adatainak elemzése.
- **Megfelelőségi jelentések készítéséhez**. Biztonsági adatok és insights segítségével megfelelőségi bemutatása, és könnyedén generálhatnak bizonyító adatok ellenőrök számára alkalmas.

**Adaptív fenyegetés megelőzése**

- **A folyamatos biztonsági értékelést**. Gépek, hálózatokon és az Azure-szolgáltatások beépített biztonsági vizsgálatok során több száz használatával biztonsági figyelése, vagy hozza létre a sajátját. Azonosítsa a szoftverek és konfigurációk, amelyek a támadásokkal szemben.
- **Végrehajthatóként javaslatok**. Biztonsági rések kijavítani, mielőtt is kihasználható a támadók a rangsorolt, végrehajthatóként biztonsági javaslatok és a beépített automatizálást playbooks.
- **Adaptív alkalmazás vezérlők**. Kártevő szoftverek és más nemkívánatos alkalmazások blokkolása a bizonyos Azure munkaterhelések igazodó és gépi tanulási technológiával engedélyezett javaslatok alkalmazásával. 
- **Hálózati hozzáférés-biztonságot**. A hálózati támadási felület csökkentése just-in-time, ellenőrzött hozzáféréssel rendelkező felügyeleti portokhoz Azure virtuális gépeken, ami rendkívüli mértékben csökkenti a találgatásos és egyéb hálózati támadásoktól.

**Intelligens fenyegetések észlelése és a válasz**

- **A fenyegetés eszközintelligencia iparági legteljesebb**. Koppintson a Microsoft intelligens biztonsági diagramot, használja a Microsoft-szolgáltatások és a világ minden táján rendszerek jelek trillions új fenyegető veszélyek azonosításához azokat.
- **A fenyegetésészlelés speciális**. Használja a beépített viselkedéselemzés és a gépi tanulás támadások és nulla napos biztonsági rések azonosítása. Figyelheti a bejövő támadásokat és az incidens utáni tevékenységeket a hálózatokon, a gépeken és a felhőszolgáltatásokon.
- **Riasztások és incidensek prioritása**. A legfontosabb fenyegetések először a rangsorolt biztonsági riasztások és incidensek, amelyek különböző típusú riasztások az egyetlen támadás kampány összpontosítanak. Hozzon létre a saját egyéni biztonsági riasztásokat is.
- **Zökkenőmentes vizsgálat**. Gyorsan felmérheti a hatókör és vizuális, interaktív nyújthassunk támadás hatását. Használja az előre definiált vagy alkalmi lekérdezések mélyebb feltárási biztonsági adatok. 
- **Környezetfüggő fenyegetésfelderítési adataival**. A forrás egy interaktív globális térképen támadások megjelenítése. Betekintést értékes a technikák és az ismert rosszindulatú szereplője célkitűzéseinek a beépített fenyegetés az eszközintelligencia jelentései.

## <a name="get-started"></a>Bevezetés
A kezdéshez a Security Center szüksége van a Microsoft Azure-előfizetéssel. A Security Center engedélyezve van Azure- előfizetéséhez. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/). 

[Ismerkedés az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) gyorsan megismerheti a Security Center biztonsági biztonságfelügyeleti és szabályzat-kezelési összetevőit. 


## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban megismerhette a Security Centert, annak főbb funkcióit és használatának első lépéseit. További tudnivalókért lásd a következőket:

* [Az Azure Security Center tervezésével és műveletek útmutatója](security-center-planning-and-operations-guide.md) – megtudhatja, hogyan optimalizálható a Security Center alapján a vállalat biztonsági igényeinek és felhőfelügyeleti modelljének használatára.
* [Biztonsági szabályzatok beállítása](https://docs.microsoft.com/azure/security-center/security-center-policies) – útmutató az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése](https://docs.microsoft.com/azure/security-center/security-center-recommendations) – megtudhatja, miként könnyítik meg a javaslatok az Azure és az-Azure erőforrások védelmében.
* [Biztonsági állapotfigyelés](https://docs.microsoft.com/azure/security-center/security-center-monitoring) – útmutató az Azure és az-Azure erőforrások állapotának figyelésére.
* [Kezelése és válaszadás a biztonsági riasztások](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partnermegoldások figyelése](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions) – megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Security Center: GYIK](https://docs.microsoft.com/azure/security-center/security-center-faq) – gyakori kérdések a Security Center használatáról.


