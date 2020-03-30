---
title: Azure Automation – Hibrid runbook-feldolgozó
description: Ez a cikk a hibrid Runbook-feldolgozó telepítésével és használatával kapcsolatban nyújt tájékoztatást, amely az Azure Automation egyik szolgáltatása, amellyel runbookok futtatására használható a helyi adatközpontban vagy felhőszolgáltatóban lévő gépeken.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: cb1444261a2ba4810f4fddb3d7aa3bc172f09654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278869"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó használatával

Előfordulhat, hogy az Azure Automationben a Runbookok nem férnek hozzá más felhőkben vagy a helyszíni környezetben lévő erőforrásokhoz, mert azok az Azure felhőplatformon futnak. Az Azure Automation hibrid Runbook-feldolgozó szolgáltatásával runbookokat futtathat közvetlenül a szerepkört üzemeltető számítógépen és a környezetben lévő erőforrásokkal a helyi erőforrások kezeléséhez. A Runbookok tárolása és kezelése az Azure Automationben van tárolva, majd egy vagy több hozzárendelt számítógépre kézbesítve.

Az alábbi képen ez a funkció látható:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

Minden hibrid Runbook-feldolgozó egy hibrid Runbook-feldolgozó csoport tagja, amelyet az ügynök telepítésekor megadott. Egy csoport tartalmazhat egyetlen ügynököt, de a magas rendelkezésre állás érdekében több ügynököt is telepíthet egy csoportban. Minden gép egy hibrid feldolgozójelentést üzemeltethet egy Automation-fióknak.

Amikor egy hibrid Runbook-feldolgozó runbookot indít el, megadhatja azt a csoportot, amelyen fut. A csoport minden egyes dolgozója lekérdezi az Azure Automationt, hogy lássa, van-e elérhető feladat. Ha egy feladat elérhető, az első dolgozó, aki megkapja a munkát veszi azt. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozó hardverprofiljától és terhelésátótól függ. Egy adott dolgozót nem adhat meg. A hibrid Runbook-feldolgozók nem osztoznak az Azure sandbox-ok által meghatározott korlátokon. A lemezterületre, a memóriára vagy a hálózati szoftvercsatornákra vonatkozóan nem ugyanazok a korlátok vonatkoznak. Hibrid Runbook-feldolgozók csak korlátozza az erőforrásokat a hibrid Runbook-feldolgozó maga. Emellett a hibrid Runbook-feldolgozók nem osztják meg a 180 perces [méltányos megosztási](automation-runbook-execution.md#fair-share) időkorlátot, amelyet az Azure sandboxes csinál. Ha többet szeretne megtudni az Azure sandboxes és a hibrid Runbook-feldolgozók szolgáltatáskorlátairól, tekintse meg a [feladatkorlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) lapot.

## <a name="install-a-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó telepítése

A hibrid Runbook-feldolgozó telepítésének folyamata az operációs rendszertől függ. Az alábbi táblázat a telepítéshez használható módszerekre mutató hivatkozásokat tartalmaz.

A Windows hibrid runbook-feldolgozó telepítéséhez és konfigurálásához két módszert használhat. Az ajánlott módszer egy Automation runbook használatával teljesen automatizálja a Windows-számítógép konfigurálásának folyamatát. A második módszer a szerepkör manuális telepítésének és konfigurálásának lépésről lépésre történik. Linux-gépek, futtatja a Python-parancsfájlt az ügynök telepítéséhez a gépen.

|Operációs rendszer  |Telepítési típusok  |
|---------|---------|
|Windows     | [Powershell](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> A hibrid runbook-feldolgozószerepkört a kívánt állapotkonfigurációval (DSC) támogató kiszolgálók konfigurációjának kezeléséhez hozzá kell adnia őket DSC-csomópontként. A DSC-vel való felügyelethez való bevezetésükről további információt a Bevezetési gépek az [Azure Automation DSC általi felügyelethez](automation-dsc-onboarding.md)című témakörben talál.
>
>Ha engedélyezi az [Update Management megoldás,](automation-update-management.md)minden olyan számítógép, amely csatlakozik az Azure Log Analytics-munkaterület automatikusan konfigurálva van, mint egy hibrid Runbook feldolgozó támogatja runbookok ebben a megoldásban. A számítógép azonban nincs regisztrálva az Automation-fiókban már definiált hibrid feldolgozói csoportokkal. A számítógép hozzáadható egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation runbookok támogatásához, feltéve, hogy ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozó csoporttagsághoz. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziója lett hozzáadva.

Tekintse át a [hálózat megtervezéséhez szükséges információkat,](#network-planning) mielőtt elkezdené telepíteni a hibrid Runbook-feldolgozót. Miután sikeresen üzembe helyezte a dolgozót, tekintse át a [Runbookok futtatásának futtatását egy hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md) című útmutatót, amelyből megtudhatja, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőalapú környezetben.

A számítógép hozzáadható egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation runbookok támogatásához, feltéve, hogy ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozó csoporttagsághoz. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

## <a name="remove-a-hybrid-runbook-worker"></a>Hibrid Runbook-feldolgozó eltávolítása

Eltávolíthat egy vagy több hibrid Runbook-feldolgozót egy csoportból, vagy eltávolíthatja a csoportot, a követelményektől függően. Hibrid Runbook-feldolgozó helyszíni számítógépről való eltávolításához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg az Automation-fiók.
2. A **Fiókbeállítások csoportban**válassza a **Kulcsok** lehetőséget, és jegyezze fel az **URL-** és **elsődleges hozzáférési kulcs értékeit.** Erre az információra van szüksége a következő lépéshez.

### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-munkamenetet Rendszergazda módban, és futtassa a következő parancsot. Az eltávolítási folyamat részletes naplójának részletes naplójával használja a **-Verbose** kapcsolót.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Az elavult gépek eltávolítása a hibrid feldolgozó `machineName` csoport, használja a választható paraméter.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Használhatja a `ls /var/opt/microsoft/omsagent` parancsot a hibrid Runbook feldolgozó a munkaterület lekért. Van egy mappa a könyvtárban, amelyben a mappa neve a munkaterület-azonosító.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a kód nem távolítja el a Microsoft Monitoring Agent a számítógépről, csak a funkcionalitás és a konfiguráció a hibrid Runbook feldolgozó szerepkör.

## <a name="remove-a-hybrid-worker-group"></a>Hibridfeldolgozó-csoport eltávolítása

Egy csoport eltávolításához először el kell távolítania a hibrid Runbook-feldolgozót minden olyan számítógépről, amely a csoport tagja a korábban bemutatott eljárással. Ezután az alábbi lépésekkel távolíthatja el a csoportot:

1. Nyissa meg az Automation-fiókot az Azure Portalon.
2. A **Folyamatautomatizálás**csoportban válassza a **Hibrid munkavégző csoportok lehetőséget.** Jelölje ki a törölni kívánt csoportot. Megjelenik a csoport tulajdonságlapja.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. A kijelölt csoport tulajdonságlapján válassza a **Törlés**lehetőséget. Egy üzenet arra kéri, hogy erősítse meg ezt a műveletet. Válassza az **Igen** lehetőséget, ha biztos benne, hogy folytatni szeretné.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ez a folyamat több másodpercig is eltarthat. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="configure-your-network"></a><a name="network-planning"></a>A hálózat konfigurálása

### <a name="hybrid-worker-role"></a>Hibrid feldolgozószerepkör

Ahhoz, hogy a hibrid Runbook-feldolgozó csatlakozzon az Azure Automationhez, és regisztráljon vele, hozzáféréssel kell rendelkeznie a portszámhoz és az ebben a szakaszban ismertetett URL-címekhez. Ez a hozzáférés a tetején a [portok és URL-címek szükséges Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) az Azure Monitor naplók hoz való csatlakozáshoz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ha az ügynök és az Azure Automation szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook-feldolgozó és az Automation-szolgáltatások kéréseinek időtúlóra-időpontja 30 másodperc. 3 próbálkozás után a kérés sikertelen lesz. Ha tűzfalat használ az internet-hozzáférés korlátozására, konfigurálnia kell a tűzfalat, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg arról, hogy hibrid dolgozók számára van konfigurálva. Ennek módjáról a Log [Analytics-átjáró konfigurálása az Automatizálás hibrid feldolgozóiszámára](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)című témakörben talál.

A hibrid runbook-feldolgozó szerepkörhöz a következő port és URL-címek szükségesek az Automation-szel való kommunikációhoz:

* Port: A kimenő internet-hozzáféréshez csak TCP 443 szükséges.
* Globális URL: *.azure-automation.net
* Az US Gov Virginia globális URL-címe: *.azure-automation.us
* Ügynökszolgáltatás:\<https:// munkaterület-azonosító\>.agentsvc.azure-automation.net

A kivételek definiálásakor ajánlott a felsorolt címek használata. IP-címek esetén letöltheti a [Microsoft Azure Datacenter IP-tartományait.](https://www.microsoft.com/en-us/download/details.aspx?id=56519) Ez a fájl hetente frissül, és rendelkezik az aktuálisan telepített tartományokkal és az IP-tartományok közelgő módosításokkal.

Ha egy adott régióhoz definiált Automation-fiókkal rendelkezik, korlátozhatja a kommunikációt az adott regionális adatközponttal. Az alábbi táblázat az egyes régiók DNS-rekordját tartalmazza:

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

A régió IP-címek listájához a régiónevek helyett töltse le az [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlját a Microsoft letöltőközpontból.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl felsorolja a Microsoft Azure adatközpontokban használt IP-címtartományokat. A fájl számítási, SQL és tárolási tartományokat tartalmaz.
>
>A frissített fájl felkerül hetente. A fájl az aktuálisan telepített tartományokat és az IP-tartományok közelgő módosításait tükrözi. A fájlban megjelenő új tartományoklegalább egy hétig nem használatosak az adatközpontokban.
>
> Érdemes minden héten letölteni az új XML-fájlt. Ezután frissítse a webhelyet az Azure-ban futó szolgáltatások megfelelő azonosításához. Az Azure ExpressRoute-felhasználóknak meg kell jegyezniük, hogy ez a fájl az Azure-tárhely Border Gateway Protocol (BGP) hirdetésének frissítésére szolgál minden hónap első hetében.

### <a name="update-management"></a>Frissítéskezelés

A hibrid Runbook-feldolgozó által igényelt szabványos címek és portok mellett a következő címek szükségesek kifejezetten az Update Management hez. Az ezekhez a címekhez való kommunikáció a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>További lépések

* Ha tudni szeretné, hogyan konfigurálhatja a runbookokat a folyamatok automatizálására a helyszíni adatközpontban vagy más felhőbeli környezetben, olvassa [el a Runbookok futtatása hibrid runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.
* A hibrid runbook-feldolgozók hibaelhárításáról a [Hibrid Runbook-dolgozók hibaelhárítása című](troubleshoot/hybrid-runbook-worker.md#general) témakörben olvashat.

