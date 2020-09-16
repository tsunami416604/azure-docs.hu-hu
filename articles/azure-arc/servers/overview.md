---
title: Azure arc-kompatibilis kiszolgálók (előzetes verzió) – áttekintés
description: Ismerje meg, hogyan használhatja az Azure arc-kompatibilis kiszolgálókat (előzetes verzió) az Azure-on kívül üzemeltetett kiszolgálók (például Azure-erőforrások) kezelésére.
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: c5779eecddcc9d7d2bd0b405a9bdadc88309fb35
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604716"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Mi az Azure arc-kompatibilis kiszolgálók (előzetes verzió)?

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) lehetővé teszik az Azure-on kívül üzemeltetett Windows-és Linux-gépek felügyeletét a vállalati hálózaton vagy más felhőalapú szolgáltatón, hasonlóan a natív Azure-beli virtuális gépek kezeléséhez. Ha egy hibrid gép csatlakozik az Azure-hoz, az egy csatlakoztatott gép lesz, és erőforrásként van kezelve az Azure-ban. Minden csatlakoztatott számítógép rendelkezik erőforrás-AZONOSÍTÓval, egy előfizetéshez tartozó erőforráscsoport részeként, valamint a szabványos Azure-konstrukciók, például a Azure Policy és a címkék alkalmazásának előnyei. Az ügyfél helyszíni infrastruktúráját kezelő szolgáltatók a hibrid gépeket ugyanúgy kezelhetik, mint a natív Azure-erőforrásokkal, több ügyfél-környezetben, az [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) és az Azure arc használatával.

Ahhoz, hogy ezt a szolgáltatást az Azure-on kívül üzemeltetett hibrid gépekkel is el lehessen juttatni, az Azure-hoz csatlakoztatott számítógép-ügynököt minden olyan gépre telepíteni kell, amelyet az Azure-hoz való csatlakozásra tervez. Ez az ügynök nem nyújt semmilyen egyéb funkciót, és nem helyettesíti az Azure [log Analytics Agent ügynököt](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Ez az előzetes kiadás a kiértékelés céljára szolgál, és javasoljuk, hogy ne kezelje a kritikus üzemi gépeket.
>

## <a name="supported-scenarios"></a>Támogatott esetek

Ha a gépet az Azure arc-kompatibilis kiszolgálókhoz (előzetes verzió) kapcsolja, lehetővé teszi a következő konfigurációs felügyeleti feladatok végrehajtását:

- Az Azure Virtual Machines szolgáltatáshoz tartozó szabályzat-hozzárendeléssel megegyező módon rendeljen hozzá [Azure Policy vendég konfigurációkat](../../governance/policy/concepts/guest-configuration.md) .

- Figyelheti a csatlakoztatott számítógép vendég operációs rendszerének teljesítményét, és felderítheti az alkalmazás-összetevőket, hogy a folyamatait és függőségeit a [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)használatával kommunikáló más erőforrásokkal figyelje.

- Egyszerűsítse az üzembe helyezést más Azure-szolgáltatásokkal, például az Azure Automation állapot-konfigurációval és Azure Monitor Log Analytics munkaterülettel a nem Azure-beli Windows-vagy Linux-alapú gépek támogatott Azure virtuálisgép- [bővítményeivel](manage-vm-extensions.md) Ez magában foglalja az üzembe helyezés utáni konfiguráció vagy a Szoftvertelepítés egyéni parancsfájl-bővítmény használatával végzett telepítését is.

A hibrid gépről Log Analytics munkaterületen gyűjtött és tárolt adatok naplózása mostantól a géppel kapcsolatos tulajdonságokat is tartalmaz, például egy erőforrás-azonosítót. Ezt az [erőforrás-környezet](../../azure-monitor/platform/design-logs-deployment.md#access-mode) naplózási hozzáférésének támogatásához használhatja.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók

Az Azure arc-kompatibilis kiszolgálók (előzetes verzió) használata esetén csak bizonyos régiók támogatottak:

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

A legtöbb esetben a telepítési parancsfájl létrehozásakor kiválasztott helyen kell lennie az Azure-régiónak, amelynek földrajzilag legközelebb kell lennie a számítógép helyéhez. A nyugalmi állapotban lévő adatok tárolása az Ön által megadott régiót tartalmazó Azure földrajzon belül történik, amely az Ön által választott régióra is hatással lehet, ha rendelkezik az adatok tartózkodási feltételeivel. Ha az Azure-régiót, amelyhez a gép csatlakoztatva van, a rendszer áramszünetet érint, a csatlakoztatott gép nem lesz hatással, de az Azure-t használó felügyeleti műveletek nem lehetnek teljesek. Regionális kimaradás esetén, ha több, földrajzilag redundáns szolgáltatást támogató hellyel rendelkezik, a legjobb megoldás, ha a gépeket egy másik Azure-régióhoz köti.

### <a name="agent-status"></a>Ügynök állapota

A csatlakoztatott gépi ügynök egy normál szívverésű üzenetet küld a szolgáltatásnak 5 percenként. Ha a szolgáltatás nem tudja fogadni ezeket a szívverési üzeneteket a gépről, a gép offline állapotba kerül, és a rendszer 15 – 30 percen belül automatikusan **megszakítja a kapcsolatot** a portálon. Amikor egy későbbi szívverési üzenetet kap a csatlakoztatott számítógép ügynökéről, az állapota automatikusan **csatlakoztatva**lesz.

## <a name="next-steps"></a>További lépések

Az arc-kompatibilis kiszolgálók (előzetes verzió) több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények, az ügynök műszaki adatai és a telepítési módszerek megismeréséhez.
