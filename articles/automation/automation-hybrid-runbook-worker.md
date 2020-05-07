---
title: A Azure Automation hibrid Runbook-feldolgozójának áttekintése
description: Ez a cikk áttekintést nyújt a hibrid Runbook-feldolgozóról, amely a Azure Automation egyik funkciója, amellyel runbookok futtathatók a helyi adatközpontban vagy a felhőalapú szolgáltatóban található gépeken.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9a5a5c57d6646e46e83794698d2c3d41fd377d1e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787668"
---
# <a name="hybrid-runbook-worker-overview"></a>Hibrid runbook-feldolgozó – áttekintés

Előfordulhat, hogy a Azure Automation runbookok nem fér hozzá más felhőben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure Cloud platformon futnak. A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több hozzárendelt számítógéphez történik.

A következő ábra szemlélteti ezt a funkciót:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

A hibrid Runbook-feldolgozók az ügynök telepítésekor megadott hibrid Runbook Worker Group tagjai. Egy csoport tartalmazhat egyetlen ügynököt, de a magas rendelkezésre állás érdekében több ügynököt is telepíthet egy csoportba. Mindegyik gép egyetlen Automation-fiókba egyetlen hibrid feldolgozót tud üzemeltetni.

Ha hibrid Runbook-feldolgozón indítja el a runbook, akkor azt a csoportot kell megadnia, amelyen az fut. A csoport minden munkatársa lekérdezi Azure Automation, hogy van-e elérhető feladat. Ha egy feladatot elérhetővé tesz, az első feldolgozónak kell megszereznie a feladatot. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozói hardverprofil és a betöltéstől függ. Nem adhat meg egy adott dolgozót. A hibrid Runbook-feldolgozók nem osztják meg az Azure-beli munkaterületek számos korlátját. Nem rendelkeznek a lemezterülettel, a memóriával vagy a hálózati szoftvercsatornával kapcsolatos korlátozásokkal. A hibrid Runbook-feldolgozók csak a hibrid Runbook dolgozó erőforrásai korlátozzák. Emellett a hibrid Runbook-feldolgozók nem osztják meg az Azure-beli munkaterületek 180 perces, [valós idejű megosztásának](automation-runbook-execution.md#fair-share) korlátját. Ha többet szeretne megtudni az Azure-beli és a hibrid Runbook-feldolgozók szolgáltatási korlátairól, tekintse meg a feladatok [korlátait](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

## <a name="installation-of-a-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó telepítése

A hibrid Runbook-feldolgozók telepítésének folyamata az operációs rendszertől függ. Az alábbi táblázat a központi telepítési típusokat határozza meg.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Az ajánlott telepítési módszer egy Automation-runbook használata a Windows rendszerű számítógépek konfigurálásának teljes automatizálására. A második módszer egy lépésenkénti eljárás, amellyel manuálisan telepítheti és konfigurálhatja a szerepkört. A Linux rendszerű gépeken egy Python-szkriptet futtathat, amely telepíti az ügynököt a gépre.

> [!NOTE]
> Az állapot-konfigurációval (DSC) rendelkező hibrid Runbook-feldolgozói szerepkört támogató kiszolgálók konfigurációjának kezeléséhez a kiszolgálókat DSC-csomópontként kell hozzáadnia. További információ a bevezetéssel kapcsolatban: előkészítési [gépek az állapot-konfiguráció (DSC) általi felügyelethez](automation-dsc-onboarding.md).

Ha engedélyezi a [Update Managementt](automation-update-management.md), az Azure log Analytics-munkaterülethez csatlakoztatott összes számítógép automatikusan hibrid Runbook-feldolgozóként van konfigurálva az Update Management szolgáltatáshoz tartozó runbookok támogatásához. A számítógép azonban nincs regisztrálva az Automation-fiókban már definiált hibrid Runbook-feldolgozói csoportokkal. 

Az Automation-fiókban lévő hibrid Runbook Worker-csoporthoz hozzáadhatja a számítógépet, ha a Update Management és a hibrid Runbook munkavégző csoport tagjához is ugyanazt a fiókot használja. Ez a funkció a hibrid Runbook-feldolgozók verziójának 7.2.12024.0 lett hozzáadva.

Ha már ismeri a jelen témakörben található információkat, itt az ideje, hogy üzembe helyezi a hibrid Runbook-feldolgozót a környezetében. A Windows Hybrid Runbook Worker esetében lásd: [Windows Hybrid Runbook Worker telepítése](automation-windows-hrw-install.md). Linux-feldolgozók esetében lásd: [Linux Hybrid Runbook Worker telepítése](automation-linux-hrw-install.md).

## <a name="network-configuration"></a><a name="network-planning"></a>Hálózati konfiguráció

### <a name="hybrid-worker-role"></a>Hibrid feldolgozói szerepkör

Ahhoz, hogy a hibrid Runbook-feldolgozó csatlakozhasson a Azure Automationhoz való csatlakozáshoz, és regisztrálja őket, hozzáféréssel kell rendelkeznie a portszámhoz és az ebben a szakaszban ismertetett URL-címekhez. Ez a hozzáférés a Log Analytics ügynök Azure Monitor naplókhoz való csatlakozáshoz [szükséges portokra és URL-címekre](../azure-monitor/platform/agent-windows.md) vonatkozik.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha proxykiszolgálót használ az ügynök és a Azure Automation szolgáltatás közötti kommunikációhoz, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook Worker és az Automation Services kéréseinek időtúllépése 30 másodperc. 3 próbálkozás után a kérelem sikertelen lesz. Ha tűzfal használatával korlátozza az internethez való hozzáférést, a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg róla, hogy a hibrid feldolgozók számára van konfigurálva. Ennek módjával kapcsolatos útmutatásért lásd: [a log Analytics átjáró konfigurálása az Automation hibrid feldolgozók számára](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

A hibrid Runbook-feldolgozói szerepkörnek az Automation szolgáltatással való kommunikációhoz a következő portok és URL-címek szükségesek:

* Port: a kimenő internet-hozzáféréshez csak TCP 443 szükséges.
* Globális URL-cím: *. azure-automation.net
* Az US Gov Virginia globális URL-címe: *.azure-automation.us
* Ügynök szolgáltatás: https://\<munkaterület azonosítója\>. agentsvc.Azure-Automation.net

A kivételek meghatározásakor a felsorolt címek használatát javasoljuk. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/en-us/download/details.aspx?id=56519). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza.

Ha egy adott régióhoz meghatározott Automation-fiókkal rendelkezik, akkor korlátozhatja a kommunikációt az adott regionális adatközpontra. A következő táblázat az egyes régiók DNS-rekordját tartalmazza:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| USA nyugati régiója, 2. |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Ausztrália középső régiója |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Kelet-Ausztrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

A régiók neveit tartalmazó régió IP-címeinek listáját az Azure- [adatközpont IP-címének](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból töltheti le.

Az Azure Datacenter IP-cím XML-fájlja felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományt. A fájl a számítási, az SQL-és a tárolási tartományokat tartalmazza.

A frissített fájlok hetente kerülnek közzétételre. A fájl a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban.

Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. Az Azure ExpressRoute felhasználói számára fontos megjegyezni, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének frissítésére szolgál minden hónap első hetében.

## <a name="update-management-addresses-for-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozók Update Management címei

A hibrid Runbook Worker által igényelt szabványos címek és portok alapján a következő címekre van szükség, kifejezetten Update Management. Az ezekkel a címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|

## <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbookok futtatása hibrid Runbook-feldolgozón

Lehetnek olyan runbookok, amelyek a helyi számítógépen lévő erőforrásokat felügyelik, vagy a helyi környezet erőforrásain futnak, ahol hibrid Runbook-feldolgozót telepítenek. Ebben az esetben dönthet úgy, hogy egy Automation-fiók helyett a hibrid feldolgozón futtatja a runbookok. A hibrid Runbook-feldolgozón futó runbookok az Automation-fiókban futtatott struktúrában azonosak. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hibrid Runbook Worker-feladatok

A hibrid Runbook-feldolgozói feladatok **a helyi** rendszerfiók alatt futnak a Windows rendszeren vagy a Linuxon futó **nxautomation** -fiókban. A Azure Automation a hibrid Runbook-feldolgozók feladatait némileg eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Az egyik legfontosabb különbség, hogy a runbook-feldolgozók nem korlátozzák a feladatok időtartamát. A runbookok Azure-beli munkaterületeken való futtatása a [méltányos megosztás](automation-runbook-execution.md#fair-share)miatt legfeljebb három órát vesz igénybe.

Ha a hibrid Runbook Worker gazdagépe újraindul, a futó Runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a műveletet.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook engedélyek a hibrid Runbook-feldolgozók számára

Mivel a nem Azure-beli erőforrásokhoz férnek hozzá, a hibrid Runbook-feldolgozón futó runbookok nem használhatja az Azure-erőforrásokhoz való runbookok-hitelesítést használó hitelesítési mechanizmust jellemzően. A runbook lehetővé teszi a saját hitelesítését a helyi erőforrásokhoz, vagy a hitelesítést [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)segítségével konfigurálja. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok számára.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#general).