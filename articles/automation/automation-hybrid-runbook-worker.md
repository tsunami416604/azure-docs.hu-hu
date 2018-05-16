---
title: Azure Automation szolgáltatásbeli hibrid forgatókönyv-feldolgozók
description: Ez a cikk bemutatja, telepítéséről és használatáról a hibrid forgatókönyv-feldolgozó, amely egy Azure Automation, amely lehetővé teszi runbookok futtatását a helyi adatközpontban, illetve a felhőbeli szolgáltató gépeken funkciója.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0af8806cdc55b89a9ab87a8059808e4fcc9a1730
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>A saját adatközpont vagy a felhőbe a hibrid forgatókönyv-feldolgozó erőforrások automatizálásának

Azure Automation Runbookjai nem lehet hozzáférni az erőforrásokhoz, a többi felhőből vagy a helyszíni környezetben, mert azok Azure felhőben futtatni. A hibrid forgatókönyv-feldolgozó szolgáltatás az Azure Automation lehetővé teszi a runbookok futtatásához, közvetlenül a szerepkört futtató számítógépen és a helyi erőforrások kezelése környezetben erőforrásokon. Runbookok által tárolt és az Azure Automationben kezelt és ezután a felhasználóikhoz kerülnek egy vagy több kijelölt számítógépekre.

Ez a funkció az alábbi ábrán látható:

![Hibrid forgatókönyv-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Hibrid forgatókönyv-feldolgozó csoportok

Minden egyes hibrid forgatókönyv-feldolgozó az ügynök telepítése során a hibrid forgatókönyv-feldolgozó csoport tagja. Egy csoport ügynököt lehetnek, de több ügynök is telepíthető egy magas rendelkezésre állási csoportot.

A hibrid forgatókönyv-feldolgozók a runbook indításakor, a csoport az fut, adja meg. A csoport minden egyes worker kérdezze le az Azure Automation-feladat vannak-e rendelkezésre. Ha egy feladat érhető el majd beolvasni a feladat első worker állítaná. Egy adott munkavégző nem adható meg.

## <a name="installing-a-hybrid-runbook-worker"></a>A hibrid forgatókönyv-feldolgozók telepítése

A folyamat a hibrid forgatókönyv-feldolgozók telepítendő eltér attól függően, hogy az operációs rendszer. A következő táblázat a különböző módszerek használatával telepítse a hibrid forgatókönyv-feldolgozók mutató hivatkozásokat tartalmaz. Telepítése és konfigurálása a Windows hibrid forgatókönyv-feldolgozók, kétféleképpen érhető el. Az ajánlott módszer használ egy Automation-runbook teljesen automatizálja a Windows-számítógép konfigurálásához szükséges. A másik módszer a következő kézi telepítését és konfigurálását a szerepkör lépésenkénti útmutatót. Linux-gépek az ügynök telepítéséhez a számítógépen egy Python-parancsfájl futtatása

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuális](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> A kiszolgálók, a hibrid forgatókönyv-feldolgozói szerepkör a kívánt állapot konfigurációs szolgáltatása (DSC) támogató konfigurációjának kezelésére, szüksége DSC-csomópontként hozzáadni. További információ a bevezetési azokat a-kezeléshez a DSC, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).
>
>Ha engedélyezi a [frissítés felügyeleti megoldás](automation-update-management.md), a Naplóelemzési munkaterület kapcsolódó minden számítógép automatikusan a hibrid forgatókönyv-feldolgozót a megoldásban forgatókönyvek támogatására van konfigurálva. Azonban nincs regisztrálva a hibrid feldolgozó csoportokkal már definiálva van az Automation-fiókban. A számítógép Automation-forgatókönyv támogatásához, mindaddig, amíg az megoldás és a hibrid forgatókönyv-feldolgozó csoport tagsága is ugyanazt a fiókot használ az Automation-fiók hibrid forgatókönyv-feldolgozó csoporthoz lehet hozzáadni. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

Tekintse át a [információkat a hálózat tervezése](#network-planning) mielőtt elkezdené a hibrid forgatókönyv-feldolgozók telepítését. Miután sikeresen telepítette a forgatókönyv-feldolgozók, tekintse át a [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.

## <a name="removing-hybrid-runbook-worker"></a>Hibrid forgatókönyv-feldolgozó eltávolítása

Egy vagy több hibrid forgatókönyv-feldolgozók eltávolítása egy csoportból, vagy távolítsa el a csoport a követelményeitől függően. A helyi számítógépről távolítsa el a hibrid forgatókönyv-feldolgozók, hajtsa végre az alábbi lépéseket:

1. Az Azure-portálon lépjen az Automation-fiók.
2. Az a **beállítások** panelen válassza **kulcsok** meg és jegyezze fel a mező értékei **URL-cím** és **elsődleges elérési kulcsot**. Ezt az információt a következő lépésre van szükség.

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
> Ez nem távolítja el a Microsoft Monitoring Agent a számítógépről, csak a funkciókat és a hibrid forgatókönyv-feldolgozói szerepkör konfigurációja.

## <a name="remove-hybrid-worker-groups"></a>Hibridfeldolgozó-csoport törlése

Távolítsa el a csoportot, először távolítsa el a hibrid forgatókönyv-feldolgozó minden számítógépen, a korábban bemutatott eljárás segítségével csoport tagja, és ezután hajtsa végre az alábbi lépések végrehajtásával távolítsa el a csoportot.

1. Nyissa meg az Automation-fiók az Azure portálon.
1. A **folyamat**, jelölje be **hibrid dolgozó csoportok**. Válassza ki a törölni kívánt csoportot. A megadott csoport kijelölése után a **hibrid feldolgozócsoport** tulajdonságai lap is megjelenik.

   ![Hibrid forgatókönyv-feldolgozó csoport lap](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. A kijelölt csoporthoz tartozó Tulajdonságok lapján kattintson a **törlése**. Válasszon egy üzenet jelenik meg, ez a művelet megerősítését kéri **Igen** Ha biztos benne, hogy a műveletet.

   ![Csoport törlése megerősítő párbeszédpanele](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   A folyamat eltarthat néhány másodpercig, az előrehaladását nyomon követheti az **Értesítések** menüpont alatt.

## <a name="network-planning"></a>A hálózat konfigurálása

### <a name="hybrid-worker-role"></a>Hibrid feldolgozói szerepkör

A hibrid forgatókönyv-feldolgozó csatlakozni, és regisztrálhatja az Naplóelemzési a port számát és az URL-címeket ebben a szakaszban ismertetett elérésére kell legyen. Ez az kívül a [portok és a Microsoft Monitoring Agent szükséges URL-címek](../log-analytics/log-analytics-agent-windows.md) Log Analyticshez való csatlakozáshoz.

Ha az ügynök és a Naplóelemzés szolgáltatás közötti kommunikációhoz proxykiszolgálót használ, ellenőrizze, hogy a megfelelő erőforrásokon elérhető. Ha tűzfal használata az internetről való hozzáférésének korlátozásához, konfigurálnia kell a tűzfalat, hogy lehetővé teszik a hozzáférést.

A következő port és URL-címeket a hibrid forgatókönyv-feldolgozói szerepkör Automation folytatott kommunikációhoz szükségesek:

* Port: Csak a TCP 443-as kimenő internet-hozzáférés szükség.
* Globális URL-címe: *.azure-automation.net
* USA – (kormányzati) Virginia globális URL-címe: *.azure-automation.us
* Ügynök szolgáltatás: https://\<workspaceId\>.agentsvc.azure-automation.net

Ha egy Automation-fiók, amely egy adott területre van definiálva, korlátozhatja, hogy regionális adatközpontok kommunikációt. A következő táblázat a DNS-rekord mindegyik régióhoz.

| **Régió** | **DNS-rekord** |
| --- | --- |
| Egyesült Államok nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-termék-1.azure-automation.net |
| USA középső déli régiója |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Egyesült Államok, keleti régió 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japán, keleti régió |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-termék-1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-termék-1.azure-automation.us |

Régió IP-címek helyett régió nevek listája, töltse le a [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlt a Microsoft Download Center webhelyről.

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl tartalmazza a Microsoft Azure adatközpontjaiban használt IP-címtartományok. Számítás-, SQL és a fájl szerepelnek.
>
>A frissített fáljra hetente visszaküldi. A fájl az aktuálisan telepített tartománya és az IP-címtartományok jövőbeli módosításait tartalmazza. Új tartományok, amelyek szerepelnek a fájlban legalább egy hétig nem szerepel az adatközpontokban.
>
> Célszerű minden héten az új XML-fájl letöltésére. Ezt követően frissítse a helyet megfelelő azonosításához az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy ezt a fájlt a Border Gateway Protocol (BGP) hirdetmény Azure terület minden hónap első hetében frissítéséhez használja.

### <a name="update-management"></a>Frissítéskezelés

A szabványos címek és a hibrid forgatókönyv-feldolgozó által igényelt portokat kívül az alábbi címek kifejezetten a kezeléséhez szükséges. Ezeknél a címeknél kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>Hibaelhárítás

A hibrid forgatókönyv-feldolgozó attól függ, hogy egy ügynök kommunikáljon az Automation-fiók regisztrálása a munkavégző, runbook-feladatok fogadása és jelentse állapotát. A Windows az ügynök a Microsoft Monitoring Agent. Linux esetén Linux OMS-ügynököt. A dolgozó regisztrálása meghiúsul, ha az alábbiakban néhány a hiba lehetséges okai:

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>A hibrid feldolgozó a proxy vagy az tűzfal mögött van

Ellenőrizze, hogy a számítógép *.azure-automation.net kimenő hozzáféréssel rendelkezzen a 443-as porton.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>A hibrid feldolgozó futtató számítógép nem felel meg a minimális hardverkövetelmények

A hibrid forgatókönyv-feldolgozó futtató számítógépeken meg kell felelnie a minimális hardverkövetelményeknek, ez a szolgáltatás futtatásához kijelölése előtt. Ellenkező esetben az attól függően, hogy az erőforrás-használat más háttérfolyamatot és végrehajtása során runbookok által okozott versengés, a számítógép túlterhelt válik, és runbook-feladat késleltetés vagy időtúllépések okozhat.

Győződjön meg arról, a hibrid forgatókönyv-feldolgozó szolgáltatás futtatására kijelölt számítógép megfelel a minimális hardverkövetelményeknek. Ha igen, figyelheti a Processzor- és memóriafelhasználását a hibrid forgatókönyv-feldolgozó folyamat teljesítményét és a Windows között a korrelációs meghatározásához. Ha memória vagy a CPU-terhelés, jelezheti, hogy a szükséges frissítése vagy további processzorok hozzáadásával, vagy növelje a memória erőforrás szűk cím, és hárítsa el a hibát. Azt is megteheti válassza ki a különböző számítási erőforrása, amely támogathatja a minimális követelményeknek és a skála, ha terheléshez növelését szükség.

Egy adott os elhárításával kapcsolatos további információkért lásd: [Linux hibrid forgatókönyv-feldolgozó](automation-linux-hrw-install.md#troubleshooting) vagy [Windows hibrid forgatókönyv-feldolgozó](automation-windows-hrw-install.md#troubleshooting)

## <a name="next-steps"></a>További lépések

Felülvizsgálati [runbookot futtatni a hibrid forgatókönyv-feldolgozó](automation-hrw-run-runbooks.md) megtudhatja, hogyan konfigurálhatja a runbook automatizálása a helyszíni adatközpontját, illetve más felhőalapú környezetben.
