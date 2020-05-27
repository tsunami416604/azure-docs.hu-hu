---
title: Azure Automation Hybrid Runbook Worker – áttekintés
description: Ez a cikk áttekintést nyújt a hibrid Runbook-feldolgozóról, amellyel runbookok futtathatók a helyi adatközpontban vagy a felhőalapú szolgáltatóban található gépeken.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835291"
---
# <a name="hybrid-runbook-worker-overview"></a>Hibrid runbook-feldolgozó – áttekintés

Előfordulhat, hogy a Azure Automation runbookok nem fér hozzá más felhőben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure Cloud platformon futnak. A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több hozzárendelt számítógéphez történik.

A következő ábra szemlélteti ezt a funkciót:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

A hibrid Runbook-feldolgozók a Windows vagy a Linux operációs rendszert is futtathatják. A figyeléshez a Azure Monitor és egy Log Analytics ügynök használatát igényli a támogatott operációs rendszerhez. További információ: [Azure monitor](automation-runbook-execution.md#azure-monitor).

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

Az ajánlott telepítési módszer egy Azure Automation runbook használata a Windows rendszerű számítógépek konfigurálásának teljes automatizálásához. A második módszer egy lépésenkénti eljárás, amellyel manuálisan telepítheti és konfigurálhatja a szerepkört. A Linux rendszerű gépeken egy Python-szkriptet futtathat, amely telepíti az ügynököt a gépre.

## <a name="network-planning"></a><a name="network-planning"></a>Hálózattervezés

Ahhoz, hogy a hibrid Runbook-feldolgozó csatlakozhasson a Azure Automationhoz való csatlakozáshoz, és regisztrálja őket, hozzáféréssel kell rendelkeznie az ebben a részben ismertetett portszámhoz és URL-címekhez. A munkavégzőnek hozzáféréssel kell rendelkeznie a Log Analytics ügynökhöz a Azure Monitor Log Analytics munkaterülethez való csatlakozáshoz [szükséges portokhoz és URL-címekhez](../azure-monitor/platform/agent-windows.md) .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A hibrid Runbook-feldolgozónak a következő portokra és URL-címekre van szüksége:

* Port: csak TCP 443 szükséges a kimenő internet-hozzáféréshez
* Globális URL-cím: *. azure-automation.net
* Az US Gov Virginia globális URL-címe: *.azure-automation.us
* Ügynök szolgáltatás: https:// \< munkaterület azonosítója \> . agentsvc.Azure-Automation.net

Javasoljuk, hogy használja a [kivételek](automation-runbook-execution.md#exceptions)meghatározásakor felsorolt címeket. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/en-us/download/details.aspx?id=56519). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza.

### <a name="dns-records-per-region"></a>DNS-rekordok régiónként

Ha rendelkezik egy adott régióhoz definiált Automation-fiókkal, akkor a hibrid Runbook-feldolgozó kommunikációt korlátozhatja az adott regionális adatközpontra. Az alábbi táblázat az egyes régiók DNS-rekordját tartalmazza.

| **Régió** | **DNS-rekord** |
| --- | --- |
| Ausztrália középső régiója |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Kelet-Ausztrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA nyugati régiója, 2. |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

A régiók neveit tartalmazó régió IP-címeinek listáját az Azure- [adatközpont IP-címének](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból töltheti le. A frissített IP-címet hetente közzétesszük. 

Az IP-cím fájl felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományok listáját. Ez magában foglalja a számítási, az SQL-és a tárolási tartományokat, és tükrözi a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban.

Érdemes minden héten letölteni az új IP-címet. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. 

> [!NOTE]
> Ha az Azure ExpressRoute-t használja, ne feledje, hogy az IP-fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének az egyes hónapok első hetében való frissítésére szolgál.

### <a name="proxy-server-use"></a>Proxykiszolgáló használata

Ha a Azure Automation és a Log Analytics ügynök közötti kommunikációhoz proxykiszolgálót használ, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook Worker és Automation szolgáltatásokból érkező kérések időtúllépése 30 másodperc. Három próbálkozás után egy kérelem meghiúsul. 

### <a name="firewall-use"></a>Tűzfal használata

Ha tűzfal használatával korlátozza az internethez való hozzáférést, a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg arról, hogy a hibrid Runbook-feldolgozók számára van konfigurálva. Lásd: [a log Analytics átjáró konfigurálása az Automation hibrid feldolgozók számára](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Update Management hibrid Runbook-feldolgozón

Ha Azure Automation [Update Management](automation-update-management.md) engedélyezve van, az log Analytics munkaterülethez csatlakoztatott összes számítógép automatikusan hibrid Runbook-feldolgozóként van konfigurálva. Az egyes feldolgozók támogatják az runbookok megcélozni kívánt frissítéseket. 

Az így konfigurált számítógépek nincsenek regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozói csoportokkal. A számítógépet hozzáadhatja egy hibrid Runbook Worker csoporthoz, de ugyanazt a fiókot kell használnia a Update Management és a hibrid Runbook Worker Group tagsághoz is. Ez a funkció a hibrid Runbook-feldolgozók verziójának 7.2.12024.0 lett hozzáadva.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozók Update Management címei

A hibrid Runbook-feldolgozó által igényelt szabványos címek és portok felett Update Management a következő táblázatban szereplő címeket kell megadnia. A címekkel folytatott kommunikáció az 443-es portot használja.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Állapot-konfiguráció Azure Automation hibrid Runbook-feldolgozón

[Azure Automation állapot konfigurációját](automation-dsc-overview.md) futtathatja hibrid Runbook-feldolgozón is. A hibrid Runbook-feldolgozót támogató kiszolgálók konfigurációjának kezeléséhez a kiszolgálókat DSC-csomópontként kell hozzáadnia. Lásd: [a gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbookok hibrid Runbook-feldolgozón

Lehetnek olyan runbookok, amelyek a helyi számítógépen lévő erőforrásokat felügyelik, vagy a helyi környezet erőforrásain futnak, ahol hibrid Runbook-feldolgozót telepítenek. Ebben az esetben dönthet úgy, hogy egy Automation-fiók helyett a hibrid feldolgozón futtatja a runbookok. A hibrid Runbook-feldolgozón futó runbookok az Automation-fiókban futtatott struktúrában azonosak. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hibrid Runbook Worker-feladatok

A hibrid Runbook-feldolgozói feladatok **a helyi** rendszerfiók alatt futnak a Windows rendszeren vagy a Linuxon futó [nxautomation-fiókban](automation-runbook-execution.md#log-analytics-agent-for-linux) . A Azure Automation a hibrid Runbook-feldolgozók feladatait némileg eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Lásd: [Runbook végrehajtási környezet](automation-runbook-execution.md#runbook-execution-environment).

Ha a hibrid Runbook Worker gazdagépe újraindul, a futó Runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a műveletet.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook engedélyek a hibrid Runbook-feldolgozók számára

Mivel a nem Azure-beli erőforrásokhoz férnek hozzá, a hibrid Runbook-feldolgozón futó runbookok nem használhatja az Azure-erőforrásokhoz való runbookok-hitelesítést használó hitelesítési mechanizmust jellemzően. A runbook lehetővé teszi a saját hitelesítését a helyi erőforrásokhoz, vagy a hitelesítést [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)segítségével konfigurálja. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok számára.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](troubleshoot/hybrid-runbook-worker.md#general).