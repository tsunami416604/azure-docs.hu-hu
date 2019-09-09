---
title: Hibrid Runbook Worker Azure Automation
description: Ez a cikk a hibrid Runbook-feldolgozók telepítéséről és használatáról nyújt információt, amely a Azure Automation szolgáltatása, amellyel a runbookok a helyi adatközpontban vagy a felhőalapú szolgáltatóban található gépeken is futtathatók.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c10905c283619e6008dbe6ab8c4e721888b8b786
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743803"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Erőforrások automatizálása az adatközpontban vagy a felhőben hibrid Runbook-feldolgozók használatával

Előfordulhat, hogy a Azure Automation runbookok nem fér hozzá más felhőben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure Cloud platformon futnak. A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető számítógépen és a környezetben található erőforrásokon futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation, majd egy vagy több hozzárendelt számítógéphez történik.

A következő ábra szemlélteti ezt a funkciót:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

A hibrid Runbook-feldolgozók az ügynök telepítésekor megadott hibrid Runbook Worker Group tagjai. Egy csoport tartalmazhat egyetlen ügynököt, de a magas rendelkezésre állás érdekében több ügynököt is telepíthet egy csoportba. Mindegyik gép egyetlen Automation-fiókba egyetlen hibrid feldolgozót tud üzemeltetni.

Ha hibrid Runbook-feldolgozón indítja el a runbook, akkor azt a csoportot kell megadnia, amelyen az fut. A csoport minden munkatársa lekérdezi Azure Automation, hogy van-e elérhető feladat. Ha egy feladatot elérhetővé tesz, az első feldolgozónak kell megszereznie a feladatot. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozói hardverprofil és a betöltéstől függ. Nem adhat meg egy adott dolgozót. A hibrid Runbook-feldolgozók nem osztják meg az Azure-beli munkaterületek számos korlátját. Nem rendelkeznek a lemezterülettel, a memóriával vagy a hálózati szoftvercsatornával kapcsolatos korlátozásokkal. A hibrid Runbook-feldolgozók csak a hibrid Runbook dolgozó erőforrásai korlátozzák. Emellett a hibrid Runbook-feldolgozók nem osztják meg az Azure-beli munkaterületek 180 perces, [valós idejű megosztásának](automation-runbook-execution.md#fair-share) korlátját. Ha többet szeretne megtudni az Azure-beli munkaterületek és a hibrid Runbook-feldolgozók szolgáltatási korlátairól, tekintse át a feladatok [korlátai](../azure-subscription-service-limits.md#automation-limits) lapot.

## <a name="install-a-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó telepítése

A hibrid Runbook-feldolgozók telepítésének folyamata az operációs rendszertől függ. A következő táblázat a telepítéshez használható metódusokra mutató hivatkozásokat tartalmaz.

A Windows Hybrid Runbook Worker telepítéséhez és konfigurálásához két módszert használhat. Az ajánlott módszer egy Automation-runbook használata a Windows rendszerű számítógépek konfigurálásának teljes automatizálására. A második módszer egy lépésenkénti eljárás, amellyel manuálisan telepítheti és konfigurálhatja a szerepkört. A Linux rendszerű gépeken egy Python-szkriptet futtathat, amely telepíti az ügynököt a gépre.

|OS  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> A hibrid Runbook-feldolgozói szerepkört támogató kiszolgálók konfigurációjának kezeléséhez a kívánt állapot-konfigurációval (DSC) hozzá kell adnia őket DSC-csomópontként. További információ a DSC-vel való felügyelethez: bevezetési [gépek a Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md).
>
>Ha engedélyezi a [Update Management megoldást](automation-update-management.md), az Azure log Analytics-munkaterülethez csatlakoztatott összes számítógép automatikusan hibrid Runbook-feldolgozóként van konfigurálva a megoldásban foglalt runbookok támogatásához. A számítógép azonban nincs regisztrálva az Automation-fiókban már definiált hibrid feldolgozói csoportokkal. A számítógép hozzáadhatók az Automation-fiókban található hibrid Runbook Worker csoporthoz az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozói csoport tagságához. Ez a funkció a hibrid Runbook-feldolgozók verziójának 7.2.12024.0 lett hozzáadva.

A hibrid Runbook-feldolgozók üzembe helyezésének megkezdése előtt tekintse át a [hálózat megtervezésével kapcsolatos információkat](#network-planning) . A feldolgozó sikeres üzembe helyezése után tekintse át a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md) című témakört, amelyből megtudhatja, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálásához.

A számítógép hozzáadhatók az Automation-fiókban található hibrid Runbook Worker csoporthoz az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozói csoport tagságához. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

## <a name="remove-a-hybrid-runbook-worker"></a>Hibrid Runbook Worker eltávolítása

Egy vagy több hibrid Runbook-feldolgozót eltávolíthat egy csoportból, vagy eltávolíthatja a csoportot a követelményektől függően. A hibrid Runbook-feldolgozó helyszíni számítógépről való eltávolításához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg az Automation-fiókját.
2. A **Fiókbeállítások**területen válassza a **kulcsok** lehetőséget, és jegyezze fel az **URL-cím** és az **elsődleges elérési kulcs**értékét. Ezt az információt a következő lépéshez kell megadnia.

### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsot. Az eltávolítási folyamat részletes naplójához használja a **-verbose** kapcsolót.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Ha el szeretné távolítani az elavult gépeket a hibrid feldolgozói `machineName` csoportból, használja a választható paramétert.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

A hibrid Runbook-feldolgozón a parancs `ls /var/opt/microsoft/omsagent` használatával kérheti le a munkaterület azonosítója. A könyvtárban található egy mappa, amelyben a mappa neve a munkaterület azonosítója.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a kód nem távolítja el a Microsoft monitoring agentet a számítógépről, csak a hibrid Runbook-feldolgozói szerepkör funkcióit és konfigurációját.

## <a name="remove-a-hybrid-worker-group"></a>Hibrid feldolgozói csoport eltávolítása

Egy csoport eltávolításához először el kell távolítania a hibrid Runbook-feldolgozót minden olyan számítógépről, amely tagja a csoportnak a korábban bemutatott eljárás használatával. Ezután a következő lépésekkel távolítsa el a csoportot:

1. Nyissa meg az Automation-fiókot a Azure Portal.
2. A **folyamat automatizálása**területen válassza a **hibrid feldolgozói csoportok**lehetőséget. Válassza ki a törölni kívánt csoportot. Megjelenik az adott csoport tulajdonságok lapja.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. A kiválasztott csoport Tulajdonságok lapján válassza a **Törlés**lehetőséget. Egy üzenet arra kéri, hogy erősítse meg ezt a műveletet. Válassza az **Igen** lehetőséget, ha biztos benne, hogy folytatni kívánja.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ez a folyamat több másodpercig is eltarthat. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="network-planning"></a>A hálózat konfigurálása

### <a name="hybrid-worker-role"></a>Hibrid feldolgozói szerepkör

Ahhoz, hogy a hibrid Runbook-feldolgozó csatlakozhasson a Azure Automationhoz való csatlakozáshoz, és regisztrálja őket, hozzáféréssel kell rendelkeznie a portszámhoz és az ebben a szakaszban ismertetett URL-címekhez. Ez a hozzáférés a Microsoft monitoring Agent Azure Monitor-naplókhoz való csatlakozáshoz [szükséges portokra és URL-címekre](../azure-monitor/platform/agent-windows.md) vonatkozik.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha proxykiszolgálót használ az ügynök és a Azure Automation szolgáltatás közötti kommunikációhoz, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook Worker és az Automation Services kéréseinek időtúllépése 30 másodperc. 3 próbálkozás után a kérelem sikertelen lesz. Ha tűzfal használatával korlátozza az internethez való hozzáférést, a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg róla, hogy a hibrid feldolgozók számára van konfigurálva. Ennek módjával kapcsolatos útmutatásért lásd: [a log Analytics átjáró konfigurálása az Automation hibrid feldolgozók számára](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

A hibrid Runbook-feldolgozói szerepkörnek az Automation szolgáltatással való kommunikációhoz a következő portok és URL-címek szükségesek:

* Port A kimenő internet-hozzáféréshez csak TCP 443 szükséges.
* Global URL: *.azure-automation.net
* US Gov Virginia globális URL-címe: *. azure-automation.us
* Ügynök szolgáltatás: https://\<munkaterület azonosítója\>. agentsvc.Azure-Automation.net

A kivételek meghatározásakor a felsorolt címeket ajánlott használni. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/en-us/download/details.aspx?id=56519). A fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait tartalmazza.

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
| Kelet-Ausztrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

A régiók neveit tartalmazó régió IP-címeinek listáját az Azure- [adatközpont IP-címének](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból töltheti le.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlja felsorolja az Microsoft Azure adatközpontokban használt IP-címtartományt. A fájl a számítási, az SQL-és a tárolási tartományokat tartalmazza.
>
>A frissített fájlok hetente kerülnek közzétételre. A fájl a jelenleg telepített tartományokat és az IP-címtartományok közelgő változásait tükrözi. A fájlban megjelenő új tartományok legalább egy hétig nem használhatók az adatközpontokban.
>
> Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. Az Azure ExpressRoute felhasználói számára fontos megjegyezni, hogy ez a fájl az Azure Space Border Gateway Protocol (BGP) hirdetményének frissítésére szolgál minden hónap első hetében.

### <a name="update-management"></a>Frissítéskezelés

A hibrid Runbook Worker által igényelt szabványos címek és portok alapján a következő címekre van szükség, kifejezetten Update Management. Az ezekkel a címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozók hibáinak elhárítása](troubleshoot/hybrid-runbook-worker.md#general)

