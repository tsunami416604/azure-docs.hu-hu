---
title: Azure Automation Hybrid Runbook Worker – áttekintés
description: Ez a cikk áttekintést nyújt a hibrid Runbook-feldolgozóról, amellyel runbookok futtathatók a helyi adatközpontban vagy a felhőalapú szolgáltatóban található gépeken.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: d921ecc390ae9361c9b36b4738e73a499aa2e8a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361812"
---
# <a name="hybrid-runbook-worker-overview"></a>Hibrid runbook-feldolgozó – áttekintés

Előfordulhat, hogy a Azure Automation runbookok nem fér hozzá más felhőben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure Cloud platformon futnak. A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető gépen és a környezet erőforrásain keresztül futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation történik, majd egy vagy több hozzárendelt géphez érkezik.

A következő ábra szemlélteti ezt a funkciót:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

A hibrid Runbook-feldolgozók a Windows vagy a Linux operációs rendszeren is futtathatók. Ez a [log Analytics ügynöktől](../azure-monitor/platform/log-analytics-agent.md) függ, amely egy Azure monitor [log Analytics munkaterületre](../azure-monitor/platform/design-logs-deployment.md)jelentett. A munkaterület nem csak a támogatott operációs rendszerhez tartozó gép figyelésére használható, hanem a hibrid Runbook-feldolgozóhoz szükséges összetevők letöltésére is.

A hibrid Runbook-feldolgozók az ügynök telepítésekor megadott hibrid Runbook Worker Group tagjai. Egy csoport tartalmazhat egyetlen ügynököt, de a magas rendelkezésre állás érdekében több ügynököt is telepíthet egy csoportba. Mindegyik gép egyetlen Automation-fiókba egyetlen hibrid feldolgozót tud üzemeltetni.

Ha hibrid Runbook-feldolgozón indítja el a runbook, akkor azt a csoportot kell megadnia, amelyen az fut. A csoport minden munkatársa lekérdezi Azure Automation, hogy van-e elérhető feladat. Ha egy feladatot elérhetővé tesz, az első feldolgozónak kell megszereznie a feladatot. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozói hardverprofil és a betöltéstől függ. Nem adhat meg egy adott dolgozót.

Azure-beli [homokozó](automation-runbook-execution.md#runbook-execution-environment) helyett hibrid Runbook-feldolgozót használhat, mert nem rendelkezik a lemezterület, a memória vagy a hálózati szoftvercsatornák nagy részének [korlátozásával](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) . A hibrid feldolgozók korlátai csak a dolgozó saját erőforrásaihoz kapcsolódnak.

> [!NOTE]
> A hibrid Runbook-feldolgozók nem korlátozzák az Azure-beli munkaterületek [valós idejű megosztásának](automation-runbook-execution.md#fair-share) korlátját.

## <a name="hybrid-runbook-worker-installation"></a>Hibrid Runbook Worker telepítése

A hibrid Runbook-feldolgozók telepítésének folyamata az operációs rendszertől függ. Az alábbi táblázat a központi telepítési típusokat határozza meg.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [Automatizált](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Az ajánlott telepítési módszer egy Azure Automation runbook használata a Windows rendszerű gépek konfigurálásának teljes automatizálásához. Ha ez nem valósítható meg, a szerepkör manuális telepítéséhez és konfigurálásához hajtsa végre a lépésenkénti útmutatót. A Linux rendszerű gépeken egy Python-szkriptet futtathat, amely telepíti az ügynököt a gépre.

## <a name="network-planning"></a><a name="network-planning"></a>Hálózattervezés

Ahhoz, hogy a hibrid Runbook-feldolgozó csatlakozhasson a Azure Automationhoz való csatlakozáshoz, és regisztrálja őket, hozzáféréssel kell rendelkeznie az ebben a részben ismertetett portszámhoz és URL-címekhez. A munkavégzőnek hozzáféréssel kell rendelkeznie a Log Analytics ügynökhöz a Azure Monitor Log Analytics munkaterülethez való csatlakozáshoz [szükséges portokhoz és URL-címekhez](../azure-monitor/platform/agent-windows.md) .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A hibrid Runbook-feldolgozónak a következő portokra és URL-címekre van szüksége:

* Port: csak TCP 443 szükséges a kimenő internet-hozzáféréshez
* Globális URL-cím:`*.azure-automation.net`
* US Gov Virginia globális URL-címe:`*.azure-automation.us`
* Ügynök szolgáltatás:`https://<workspaceId>.agentsvc.azure-automation.net`

Ha rendelkezik egy adott régióhoz definiált Automation-fiókkal, akkor a hibrid Runbook-feldolgozó kommunikációt korlátozhatja az adott regionális adatközpontra. Tekintse át a [Azure Automation által használt DNS-rekordokat](how-to/automation-region-dns-records.md) a szükséges DNS-rekordokhoz.

### <a name="proxy-server-use"></a>Proxykiszolgáló használata

Ha a Azure Automation és a Log Analytics ügynököt futtató gépek közötti kommunikációhoz proxykiszolgálót használ, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook Worker és Automation szolgáltatásokból érkező kérések időtúllépése 30 másodperc. Három próbálkozás után egy kérelem meghiúsul.

### <a name="firewall-use"></a>Tűzfal használata

Ha tűzfal használatával korlátozza az internethez való hozzáférést, a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg arról, hogy a hibrid Runbook-feldolgozók számára van konfigurálva. Lásd: [a log Analytics átjáró konfigurálása az Automation hibrid feldolgozók számára](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

### <a name="service-tags"></a>Szolgáltatáscímkék

A Azure Automation támogatja az Azure Virtual Network szolgáltatás címkéit, a szolgáltatási címke [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)kezdve. A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](../virtual-network/security-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. Ha a szolgáltatási címke nevét **GuestAndHybridManagement** adja meg egy szabály megfelelő forrás vagy cél mezőjében, engedélyezheti vagy megtagadhatja az Automation szolgáltatás forgalmát. Ez a szolgáltatási címke nem támogatja az IP-címtartományok egy adott régióra való korlátozásával a részletesebb szabályozás engedélyezését.

A Azure Automation szolgáltatáshoz tartozó szolgáltatás címkéje csak a következő forgatókönyvekhez használt IP-címeket biztosítja:

* Webhookok indítása a virtuális hálózaton belülről
* A hibrid Runbook-feldolgozók vagy állami konfigurációs ügynökök engedélyezése a VNet az Automation szolgáltatással való kommunikációhoz

>[!NOTE]
>A **GuestAndHybridManagement** szolgáltatás jelenleg nem támogatja a runbook-feladatok végrehajtását egy Azure-beli homokozóban, csak egy hibrid runbook-feldolgozón.

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management hibrid Runbook-feldolgozón

Ha Azure Automation [Update Management](automation-update-management.md) engedélyezve van, az log Analytics munkaterülethez csatlakoztatott összes gép automatikusan hibrid Runbook-feldolgozóként van konfigurálva. Az egyes feldolgozók támogatják az runbookok megcélozni kívánt frissítéseket.

Az ily módon konfigurált gépek nincsenek regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozói csoportokkal. Felveheti a gépet egy hibrid Runbook Worker-csoportba, de ugyanazt a fiókot kell használnia a Update Management és a hibrid Runbook Worker csoport tagságához is. Ez a funkció a hibrid Runbook-feldolgozók verziójának 7.2.12024.0 lett hozzáadva.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozók Update Management címei

A hibrid Runbook-feldolgozó által igényelt szabványos címek és portok felett Update Management a következő táblázatban szereplő címeket kell megadnia. A címekkel folytatott kommunikáció az 443-es portot használja.

|Azure Public  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`     | `*.ods.opinsights.azure.us`         |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Állapot-konfiguráció Azure Automation hibrid Runbook-feldolgozón

[Azure Automation állapot konfigurációját](automation-dsc-overview.md) futtathatja hibrid Runbook-feldolgozón is. A hibrid Runbook-feldolgozót támogató kiszolgálók konfigurációjának kezeléséhez a kiszolgálókat DSC-csomópontként kell hozzáadnia. Lásd: [a gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbookok hibrid Runbook-feldolgozón

Lehetnek olyan runbookok, amelyek a helyi gépen lévő erőforrásokat felügyelik, vagy a helyi környezet erőforrásain futnak, ahol hibrid Runbook-feldolgozót telepítenek. Ebben az esetben dönthet úgy, hogy egy Automation-fiók helyett a hibrid feldolgozón futtatja a runbookok. A hibrid Runbook-feldolgozón futó runbookok az Automation-fiókban futtatott struktúrában azonosak. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hibrid Runbook Worker-feladatok

A hibrid Runbook-feldolgozói feladatok **a helyi** rendszerfiók alatt futnak a Windows rendszeren vagy a Linuxon futó [nxautomation-fiókban](automation-runbook-execution.md#log-analytics-agent-for-linux) . A Azure Automation a hibrid Runbook-feldolgozók feladatait némileg eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Lásd: [Runbook végrehajtási környezet](automation-runbook-execution.md#runbook-execution-environment).

Ha a hibrid Runbook Worker gazdagépe újraindul, a futó Runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a műveletet.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook engedélyek a hibrid Runbook-feldolgozók számára

Mivel a nem Azure-beli erőforrásokhoz férnek hozzá, a hibrid Runbook-feldolgozón futó runbookok nem használhatja az Azure-erőforrásokhoz való runbookok-hitelesítést használó hitelesítési mechanizmust jellemzően. A runbook lehetővé teszi a saját hitelesítését a helyi erőforrásokhoz, vagy a hitelesítést [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)segítségével konfigurálja. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok számára.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](troubleshoot/hybrid-runbook-worker.md#general).