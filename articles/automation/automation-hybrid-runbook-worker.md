---
title: Az Azure Automation hibrid Runbook-feldolgozó
description: Ez a cikk nyújt információt telepítésével és a hibrid Runbook-feldolgozó, amely egy szolgáltatás, amellyel a helyi adatközpontban vagy a felhőbeli szolgáltató gépeken futtatná az Azure Automation használatával.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 694327cf7f7331a35a7e18cb68c566932c6231fc
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363503"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizálhatja az erőforrások az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó

Az Azure Automation Runbookjai nem lehet erőforrásokhoz való hozzáférés a helyszíni környezetben vagy más felhőkben, mert az Azure felhőalapú platformon futnak. Az Azure Automation hibrid Runbook-feldolgozó szolgáltatása segítségével a runbookok futtatása, közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése a környezetben az erőforrásokon. Runbookok tárolt és kezelt az Azure Automation és egy vagy több hozzárendelt számítógépekre kézbesítését.

Az alábbi ábrán ez a funkció:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

Minden egyes hibrid Runbook-feldolgozó, amely az ügynök telepítésekor adja meg egy hibrid Runbook-feldolgozó csoport tagja. Egy csoport egy adott ügynök is tartalmazhat, de több ügynök is telepíthető egy csoportot a magas rendelkezésre állás érdekében.

Amikor elindít egy runbookot a hibrid Runbook-feldolgozón, adja meg a csoportot, amely a futtatott. A csoport minden egyes feldolgozóhoz kérdezze le az Azure Automation-feladat elérhető megtekintéséhez. Ha egy feladat érhető el, az első worker a feladatot veszi. Egy adott feldolgozó nem adható meg. Hibrid Runbook-feldolgozók ne ossza meg, amelyek az Azure próbakörnyezetbe lefordítja a korlátokat számos. A hely a lemezen, a memória vagy a hálózati szoftvercsatorna nem kell ugyanazon korlátait. Hibrid Runbook-feldolgozók kizárólag az erőforrásokat a hibrid Runbook-feldolgozón maga is jelent. Emellett a hibrid Runbook-feldolgozók ne ossza meg a 180 perc [igazságos elosztás](automation-runbook-execution.md#fair-share) , amelyeket az Azure próbakörnyezetbe lefordítja a határidő. A szolgáltatásra vonatkozó korlátozások az Azure próbakörnyezetbe lefordítja a és a hibrid Runbook-feldolgozók kapcsolatos további információkért tekintse meg a feladat [korlátok](../azure-subscription-service-limits.md#automation-limits) lapot.

## <a name="install-a-hybrid-runbook-worker"></a>A hibrid Runbook Worker telepítése

A folyamat egy hibrid Runbook Worker telepítése az operációs rendszer függ. Az alábbi táblázat a módszereket, a telepítéshez használható hivatkozásokat tartalmaz.

Telepítése és konfigurálása a Windows hibrid Runbook-feldolgozók, két módszert használhatja. Az ajánlott módszer az Automation-runbook segítségével teljesen automatizálja a Windows-számítógép konfigurálásának folyamatán. A második módszer által követett kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót. A Linux rendszerű gépek futtassa az ügynök telepítéséhez a számítógépen egy Python-szkriptet.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuális](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> A kiszolgálók, amelyek támogatják a hibrid forgatókönyv-feldolgozói szerepkör Desired State Configuration (DSC) rétegen-konfigurációjának kezeléséhez, hozzá kell őket DSC-csomópontok. További információ a bevezetési őket felügyeletre való DSC, lásd: [gépek előkészítése kezelésre, amelyet az Azure Automation DSC](automation-dsc-onboarding.md).
>
>Ha engedélyezi a [Update Management megoldás](automation-update-management.md), minden olyan számítógép, amely az Azure Log Analytics-munkaterülethez csatlakozik automatikusan van beállítva egy hibrid Runbook-feldolgozó az ebben a megoldásban lévő runbookok támogatása érdekében. Azonban a számítógép nincs regisztrálva az bármely Hibridfeldolgozó-csoportok az Automation-fiók már definiálva. A számítógép egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation-runbookok támogatása, mindaddig, amíg használja ugyanazt a fiókot a megoldás és a hibrid Runbook-feldolgozó csoport tagsága is hozzáadhat. Ez a funkció a hibrid Runbook-feldolgozó 7.2.12024.0-s verziójától bővült.

Tekintse át a [információk a hálózat tervezése](#network-planning) egy hibrid Runbook-feldolgozó üzembe helyezése előtt. Miután a feldolgozó sikeresen telepített, tekintse át a [runbookok futtatása hibrid Runbook-feldolgozók](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a helyszíni adatközpont vagy egyéb felhőalapú környezetben lévő folyamatok automatizálásához a runbookokban.

## <a name="remove-a-hybrid-runbook-worker"></a>Távolítsa el a hibrid Runbook-feldolgozók

Egy vagy több hibrid forgatókönyv-feldolgozók eltávolíthatja a csoportból, vagy eltávolíthatja a csoport a követelményektől függően. A helyi számítógépről egy hibrid Runbook-feldolgozó eltávolításához használja az alábbi lépéseket:

1. Az Azure Portalon nyissa meg az Automation-fiók.
2. Alatt **beállítások**, jelölje be **kulcsok** , és jegyezze fel a tartozó értékeket **URL-cím** és **elsődleges elérési kulcs**. Ezt az információt a következő lépésre van szükség.

### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsot. Használja a **-Verbose** váltani, az eltávolítási folyamat részletes naplók.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

A hibrid feldolgozócsoport elavult gépek eltávolításához használja az opcionális `machineName` paraméter.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ez a kód nem távolítja el a Microsoft Monitoring Agent a számítógépről, csak a funkciók és a hibrid forgatókönyv-feldolgozói szerepkör konfigurációja.

## <a name="remove-a-hybrid-worker-group"></a>A hibrid feldolgozócsoport törlése

Eltávolítja a csoportot, akkor először távolítsa el a hibrid Runbook-feldolgozó minden olyan számítógépről, amely tagja a csoportnak a korábban bemutatott eljárás használatával. Ezután a következő lépések segítségével távolítsa el a csoportot:

1. Nyissa meg az Automation-fiókot az Azure Portalon.
1. A **Folyamatautomatizálás**válassza **hibrid feldolgozócsoportok**. Válassza ki a törölni kívánt csoportot. A csoport tulajdonságok lapján jelenik meg.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. A kiválasztott csoport tulajdonságai lapon válassza ki a **törlése**. A rendszer megkérdezi, hogy erősítse meg a műveletet. Válassza ki **Igen** Ha biztos abban, hogy szeretné-e továbbra is.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   A folyamat eltarthat néhány másodpercig befejezéséhez. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="network-planning"></a>A hálózat konfigurálása

### <a name="hybrid-worker-role"></a>A hibrid feldolgozói szerepkör

A hibrid Runbook-feldolgozó kapcsolódni és regisztrálni a Log Analytics-szel, az azt a portszámot és az ebben a szakaszban leírt URL-hozzáféréssel kell rendelkeznie. Ez az elérés felül, akkor a [portokat és a Microsoft Monitoring Agent számára a szükséges URL-címek](../log-analytics/log-analytics-agent-windows.md) szeretne csatlakozni a Log Analytics.

Ha az ügynök és a Log Analytics szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, ügyeljen arra, hogy elérhetők-e a megfelelő erőforrásokat. Ha tűzfal használatával korlátozza az internethez való hozzáférést, konfigurálnia kell a tűzfalat, hogy engedélyezze a hozzáférést. Ha a proxyként az OMS-átjárót használ, győződjön meg arról, hibrid feldolgozók van konfigurálva. Ehhez az utasításokért lásd: [az OMS-átjáró konfigurálása az Automation hibrid feldolgozók](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway#configure-for-automation-hybrid-workers).

A következő portot és az URL-címek a hibrid Runbook-feldolgozó szerepkör, Automation folytatott kommunikációhoz szükségesek:

* Port: Csak a TCP 443-as kimenő internet-hozzáférés szükség.
* Globális URL: *.azure-automation.net
* USA-beli államigazgatás – Virginia, globális URL: *.azure-automation.us
* Az ügynök szolgáltatás: https://\<munkaterület azonosítója\>.agentsvc.azure-automation.net

Javasoljuk, hogy a címek kivételek meghatározásakor. Az IP-címeket, letöltheti a [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ez a fájl hetente frissül, és a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat.

Ha egy Automation-fiókot, amely egy adott régióban van definiálva, korlátozhatja a kommunikációt a regionális adatközpontjának. A következő táblázat tartalmazza a DNS-rekord minden olyan régió esetében:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice – éles-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice – éles-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice – éles-1.azure-automation.us |

Régió IP-címek helyett régiónevek listáját, töltse le a [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) tartalmazó XML-fájlt a Microsoft Download Center.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájlt a Microsoft Azure-adatközpontok az által használt IP-címtartományok listája. A fájl a compute, SQL és storage tartományokat tartalmaz.
>
>A frissített hetente tesznek közzé. A fájl a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat tükrözi. A fájlban megjelenő új tartományokat legalább egy hétig nem használják az adatközpontokban.
>
> Célszerű letölteni az új XML-fájlt minden héten. Ezután frissítse a helyet, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy ez a fájl minden hónap első hetében Azure címterületek a Border Gateway Protocol (BGP) hirdetés frissítésére használatos.

### <a name="update-management"></a>Frissítéskezelés

Felül a standard szintű címek és a hibrid Runbook-feldolgozó által igényelt portokat a következő címekre szükség, kifejezetten az Update Management. Ezek a címek kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Hibaelhárítás

Hogyan háríthatók el a hibrid Runbook-feldolgozók kapcsolatban lásd: [hibrid Runbook-feldolgozók hibáinak elhárítása](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>További lépések

A runbookok automatizálhatja a folyamatokat a helyszíni adatközpont vagy egyéb felhőalapú környezetbe történő konfigurálásával kapcsolatban lásd: [runbookok futtatása hibrid Runbook-feldolgozók](automation-hrw-run-runbooks.md).
