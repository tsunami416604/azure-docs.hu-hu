---
title: Azure Automation szolgáltatásbeli hibrid forgatókönyv-feldolgozó
description: Ez a cikk bemutatja, telepítéséről és használatáról a hibrid forgatókönyv-feldolgozó, amely egy Azure Automation, amelyek segítségével a runbookok futtatásához a helyi adatközpontban, illetve a felhőbeli szolgáltató gépeken funkciója.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 54b2cab2ad6b1a22d35fcf0755f257063573e58b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128622"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>A datacenter vagy a felhőben lévő erőforrások automatizálásának hibrid forgatókönyv-feldolgozók segítségével

Azure Automation Runbookjai nem lehet hozzáférni az erőforrásokhoz, a többi felhőből vagy a helyszíni környezetben, mivel az Azure felhőalapú platform számítógépen futnak. A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation segítségével közvetlenül a számítógépen, amelyen a szerepkör és a helyi erőforrások kezelése környezetben erőforrásokon a runbookok futtatásához. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre.

Az alábbi ábrán ez a funkció:

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

Minden egyes hibrid forgatókönyv-feldolgozó az ügynök telepítése során a hibrid forgatókönyv-feldolgozó csoport tagja. Egy csoport ügynököt lehetnek, de több ügynök is telepíthető egy magas rendelkezésre állási csoportot.

A hibrid forgatókönyv-feldolgozók a runbook indításakor, a csoport az fut, adja meg. A csoport minden egyes worker kérdezze le az Azure Automation-feladat vannak-e rendelkezésre. Ha egy feladat érhető el, a feladat eléréséhez első worker állítaná. Egy adott munkavégző nem adható meg.

## <a name="install-a-hybrid-runbook-worker"></a>A hibrid forgatókönyv-feldolgozók telepítése

A folyamat a hibrid forgatókönyv-feldolgozók telepíteni az operációs rendszer függ. A következő táblázat a módszereket, amelyek a telepítés mutató hivatkozásokat tartalmaz. 

Telepítse és konfigurálja a Windows hibrid forgatókönyv-feldolgozók, két módszert is használhat. Az ajánlott módszer az Automation-forgatókönyv használatával történő teljesen automatizálja a Windows-számítógép konfigurálásának lépésein. A másik módszer a következő kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót. Linux-gépek a Python-parancsfájl az ügynök telepítéséhez a számítógépen futtatja.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuális](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Felügyelje a konfigurációt a kiszolgálók, amelyek támogatják a hibrid forgatókönyv-feldolgozói szerepkör a kívánt állapot konfigurációs szolgáltatása (DSC), szüksége DSC-csomópontként hozzáadni. További információ a bevezetési azokat a-kezeléshez a DSC, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).
>
>Ha engedélyezi a [frissítés felügyeleti megoldás](automation-update-management.md), csatlakozik-e az Azure Log Analytics-munkaterület számítógépek automatikusan egy hibrid forgatókönyv-feldolgozót a megoldásban forgatókönyvek támogatására van konfigurálva. Azonban a számítógép nincs regisztrálva az Automation-fiók már definiálva Hibridfeldolgozó csoportok. A számítógép az Automation-fiókban Automation-forgatókönyv támogatásához, mindaddig, amíg ugyanazt a fiókot használja a megoldás és a hibrid forgatókönyv-feldolgozó csoport tagsága is a hibrid forgatókönyv-feldolgozó csoporthoz lehet hozzáadni. Ez a funkció a hibrid forgatókönyv-feldolgozó 7.2.12024.0 verziójára bővült.

Tekintse át a [információkat a hálózat tervezése](#network-planning) mielőtt elkezdené a hibrid forgatókönyv-feldolgozók telepítését. Miután sikeresen telepítette a munkavégző, tekintse át a [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.

## <a name="remove-a-hybrid-runbook-worker"></a>Távolítsa el a hibrid forgatókönyv-feldolgozók

Egy vagy több hibrid forgatókönyv-feldolgozók eltávolítása egy csoportból, vagy távolítsa el a csoport a követelményeitől függően. A helyi számítógépről távolítsa el a hibrid forgatókönyv-feldolgozók, hajtsa végre az alábbi lépéseket:

1. Az Azure portálon lépjen az Automation-fiók.
2. A **beállítások**, jelölje be **kulcsok** meg és jegyezze fel a értékeinek **URL-cím** és **elsődleges elérési kulcsot**. Ezt az információt a következő lépésre van szükség.

### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-munkamenetet rendszergazdai módban, és futtassa a következő parancsot. Használja a **-Verbose** részletes napló, az eltávolítási folyamat váltani.

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
> Ezt a kódot nem a Microsoft Monitoring Agent eltávolítása a számítógépről, csak a funkciókat és a hibrid forgatókönyv-feldolgozói szerepkör konfigurációja.

## <a name="remove-a-hybrid-worker-group"></a>A hibrid feldolgozócsoport törlése

Egy csoport eltávolításához először távolítsa el a hibrid forgatókönyv-feldolgozó minden számítógép, amely tagja a csoportnak a korábban bemutatott eljárás használatával. Ezután hajtsa végre az alábbi lépések végrehajtásával távolítsa el a csoportot:

1. Nyissa meg az Automation-fiók az Azure portálon.
1. A **folyamat**, jelölje be **hibrid dolgozó csoportok**. Válassza ki a törölni kívánt csoportot. A csoport tulajdonságai lapon jelenik meg.

   ![Tulajdonságok lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. A kijelölt csoporthoz tartozó Tulajdonságok lapján jelölje ki a **törlése**. A rendszer megkérdezi, hogy erősítse meg a műveletet. Válassza ki **Igen** Ha biztos abban, hogy szeretné-e folytatni.

   ![Megerősítő üzenet](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   A folyamat eltarthat néhány másodpercig befejezéséhez. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="network-planning"></a>A hálózat konfigurálása

### <a name="hybrid-worker-role"></a>Hibrid feldolgozói szerepkör

A hibrid forgatókönyv-feldolgozó csatlakozni, és regisztrálhatja az Naplóelemzési a port számát és az URL-címeket ebben a szakaszban ismertetett elérésére kell legyen. A hozzáférés kívül van a [portok és a Microsoft Monitoring Agent szükséges URL-címek](../log-analytics/log-analytics-agent-windows.md) Log Analyticshez való csatlakozáshoz. 

Ha az ügynök és a Naplóelemzés szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, ellenőrizze, hogy a megfelelő erőforrásokon elérhető. Ha tűzfal használata az internetről való hozzáférésének korlátozásához, konfigurálnia kell a tűzfalat, hogy lehetővé teszik a hozzáférést.

A következő port és URL-címeket a hibrid forgatókönyv-feldolgozói szerepkör Automation folytatott kommunikációhoz szükségesek:

* Port: Csak a TCP 443-as kimenő internet-hozzáférés szükség.
* Globális URL-címe: *.azure-automation.net
* USA – (kormányzati) Virginia globális URL-címe: *.azure-automation.us
* Ügynök szolgáltatás: https://\<workspaceId\>.agentsvc.azure-automation.net

Javasoljuk, hogy a felsorolt kivételeket definiálásakor címek használatához. Az IP-címeket is letöltheti a [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ez a fájl hetente frissítve van, és az aktuálisan telepített tartománya és az IP-címtartományok jövőbeli módosításait tartalmazza.

Ha egy Automation-fiók, amely egy adott területre van definiálva, korlátozhatja, hogy regionális adatközpontok kommunikációt. A következő táblázat a DNS-rekord mindegyik régióhoz:

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-termék-1.azure-automation.net |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-termék-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-termék-1.azure-automation.us |

Régió IP-címek helyett régió nevek listája, töltse le a [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlt a Microsoft Download Center webhelyről.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl tartalmazza a Microsoft Azure adatközpontjaiban használt IP-címtartományok. A fájl tartalmazza a tárolási, számítási és SQL-tartományokat.
>
>A frissített fáljra hetente visszaküldi. A fájl az aktuálisan telepített tartománya és az IP-címtartományok jövőbeli módosításait tartalmazza. Új tartományok, amelyek szerepelnek a fájlban legalább egy hétig nem szerepel az adatközpontokban.
>
> Célszerű minden héten az új XML-fájl letöltésére. Ezt követően frissítse a helyet megfelelő azonosításához az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy a fájl a Border Gateway Protocol (BGP) hirdetés Azure terület minden hónap első hetében frissítésére szolgál.

### <a name="update-management"></a>Frissítéskezelés

A szabványos címek és a hibrid forgatókönyv-feldolgozó által igényelt portokat kívül az alábbi címek kifejezetten a kezeléséhez szükséges. Ezeknél a címeknél kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Hibaelhárítás

A hibrid forgatókönyv-feldolgozók hibaelhárítása kapcsolatban [hibrid forgatókönyv-feldolgozók hibaelhárítása](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>További lépések

A runbookok automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezet konfigurálása, lásd: [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md).