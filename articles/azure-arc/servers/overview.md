---
title: Az Azure arc használatára képes kiszolgálók áttekintése
description: Ismerje meg, hogyan használhatja az Azure arc-kompatibilis kiszolgálókat az Azure-on kívül üzemeltetett kiszolgálók (például Azure-erőforrások) kezelésére.
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid
ms.date: 09/16/2020
ms.topic: overview
ms.openlocfilehash: 9fcac35f943eefb15a200e9b148b22ce67cc3941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90887533"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Mi azok az Azure Arc-kompatibilis kiszolgálók?

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-on kívül üzemeltetett Windows-és Linux-gépek felügyeletét a vállalati hálózaton vagy más felhőalapú szolgáltatón, hasonlóan a natív Azure-beli virtuális gépek kezeléséhez. Ha egy hibrid gép csatlakozik az Azure-hoz, az egy csatlakoztatott gép lesz, és erőforrásként van kezelve az Azure-ban. Minden csatlakoztatott számítógép rendelkezik erőforrás-AZONOSÍTÓval, egy előfizetéshez tartozó erőforráscsoport részeként, valamint a szabványos Azure-konstrukciók, például a Azure Policy és a címkék alkalmazásának előnyei. Az ügyfél helyszíni infrastruktúráját kezelő szolgáltatók a hibrid gépeket ugyanúgy kezelhetik, mint a natív Azure-erőforrásokkal, több ügyfél-környezetben, az [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) és az Azure arc használatával.

Ahhoz, hogy ezt a szolgáltatást az Azure-on kívül üzemeltetett hibrid gépekkel is el lehessen juttatni, az Azure-hoz csatlakoztatott számítógép-ügynököt minden olyan gépre telepíteni kell, amelyet az Azure-hoz való csatlakozásra tervez. Ez az ügynök nem nyújt semmilyen egyéb funkciót, és nem helyettesíti az Azure [log Analytics Agent ügynököt](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-intro.md).

## <a name="supported-scenarios"></a>Támogatott esetek

Ha a gépet az Azure arc-kompatibilis kiszolgálókhoz kapcsolja, lehetővé teszi a következő konfigurálási és figyelési feladatok végrehajtását:

- Az Azure Virtual Machines szolgáltatáshoz tartozó szabályzat-hozzárendeléssel megegyező módon rendeljen hozzá [Azure Policy vendég konfigurációkat](../../governance/policy/concepts/guest-configuration.md) .

- A felügyelt kiszolgálókon a telepített szoftverekkel, a Microsoft-szolgáltatásokkal, a Windows-beállításjegyzékkel és-fájlokkal, valamint a Linux-démonokkal kapcsolatos konfigurációs változások a Azure Automation [change Tracking és a leltár](../../automation/change-tracking.md)használatával.

- Figyelheti a csatlakoztatott számítógép vendég operációs rendszerének teljesítményét, és felderítheti az alkalmazás-összetevőket, hogy a folyamatait és függőségeit a [Azure monitor for VMS](../../azure-monitor/insights/vminsights-overview.md)használatával kommunikáló más erőforrásokkal figyelje.

- Egyszerűsítse az üzembe helyezést más Azure-szolgáltatásokkal, például az Azure Automation [állapot-konfigurációval](../../automation/automation-dsc-overview.md) és Azure monitor log Analytics munkaterülettel a nem Azure-beli Windows-vagy Linux-alapú Azure virtuálisgép- [bővítmények](manage-vm-extensions.md) használatával. Ez magában foglalja az üzembe helyezés utáni konfiguráció vagy a Szoftvertelepítés egyéni parancsfájl-bővítmény használatával végzett telepítését is.

- Windows-és Linux-kiszolgálók operációs rendszerének frissítéseinek kezeléséhez használja a Azure Automation [Update Management](../../automation/update-management/update-mgmt-overview.md) . Először telepítse a [hibrid Runbook-feldolgozói](../../automation/automation-hybrid-runbook-worker.md) szerepkört, majd kövesse a lépéseket, hogy [engedélyezze Update Management](../../automation/update-management/update-mgmt-enable-portal.md) a nem Azure-beli Windows-vagy Linux-gépen.

- Vegye fel a nem Azure-kiszolgálókat a fenyegetések észlelésére, és proaktívan figyelje az esetleges biztonsági fenyegetéseket a [Azure Security Center](../../security-center/security-center-intro.md)használatával.

A hibrid gépről Log Analytics munkaterületen gyűjtött és tárolt adatok naplózása mostantól a géppel kapcsolatos tulajdonságokat is tartalmaz, például egy erőforrás-azonosítót. Ezt az [erőforrás-környezet](../../azure-monitor/platform/design-logs-deployment.md#access-mode) naplózási hozzáférésének támogatásához használhatja.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók

Az Azure arc-kompatibilis kiszolgálókkal rendelkező támogatott régiók végleges listáját az Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) oldalon tekintheti meg.

A legtöbb esetben a telepítési parancsfájl létrehozásakor kiválasztott helyen kell lennie az Azure-régiónak, amelynek földrajzilag legközelebb kell lennie a számítógép helyéhez. A nyugalmi állapotban lévő adatok tárolása az Ön által megadott régiót tartalmazó Azure földrajzon belül történik, amely az Ön által választott régióra is hatással lehet, ha rendelkezik az adatok tartózkodási feltételeivel. Ha az Azure-régiót, amelyhez a gép csatlakoztatva van, a rendszer áramszünetet érint, a csatlakoztatott gép nem lesz hatással, de az Azure-t használó felügyeleti műveletek nem lehetnek teljesek. Regionális kimaradás esetén, ha több, földrajzilag redundáns szolgáltatást támogató hellyel rendelkezik, a legjobb megoldás, ha a gépeket egy másik Azure-régióhoz köti.

### <a name="agent-status"></a>Ügynök állapota

A csatlakoztatott gépi ügynök egy normál szívverésű üzenetet küld a szolgáltatásnak 5 percenként. Ha a szolgáltatás nem tudja fogadni ezeket a szívverési üzeneteket a gépről, a gép offline állapotba kerül, és a rendszer 15 – 30 percen belül automatikusan **megszakítja a kapcsolatot** a portálon. Amikor egy későbbi szívverési üzenetet kap a csatlakoztatott számítógép ügynökéről, az állapota automatikusan **csatlakoztatva**lesz.

## <a name="next-steps"></a>További lépések

Az arc-kompatibilis kiszolgálók több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények megismeréséhez, az ügynök műszaki részleteihez és a telepítési módszerekhez.
