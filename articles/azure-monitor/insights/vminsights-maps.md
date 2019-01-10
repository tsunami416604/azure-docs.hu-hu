---
title: A virtuális gépek (előzetes verzió) és az Azure Monitor alkalmazásfüggőségek megtekintése |} A Microsoft Docs
description: Térkép funkciója az Azure monitor-beli virtuális gépek, amelyek automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen hogyan használható a különböző forgatókönyveket.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 73c94b79bb315c317d39ade704e2ee70a241a348
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187745"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>Az Azure Monitor használatával virtuális gépek (előzetes verzió) térkép az alkalmazás-összetevők ismertetése
Megtekintés a detektált alkalmazás-összetevők a környezet figyelhető meg az Azure Monitor szolgáltatással kétféleképpen virtuális gépek esetén közvetlenül a virtuális gépről vagy virtuális gépek az Azure Monitor csoportokhoz Azure-ban futó Windows és Linux rendszerű virtuális gépeken. 

Ez a cikk segít megérteni a felhasználói élményt a két perspektíva adatai és a térkép funkció használata között. A virtuális gépek az Azure Monitor konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Monitor-beli virtuális gépek](vminsights-onboard.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="introduction-to-map-experience"></a>Bemutatása Map élmény
Térkép megtekintése egyetlen virtuális gépet vagy virtuális gépek csoportján ról, előtt fontos kínálunk röviden bemutatja a szolgáltatást, hogyan jelennek meg az adatokat, és a Vizualizációk képviselik tisztában.  

A térkép szolgáltatást közvetlenül a virtuális gép vagy az Azure Monitor válassza ki, hogy egységes környezetet mutat be.  Az egyetlen különbség az Azure Monitor egy többrétegű alkalmazást vagy egy térkép-fürt összes tagja megtekintheti.

A Maps megjeleníti a virtuális gépek függőségeit felderítésével kiszolgálók, a bejövő és kimenő kapcsolati késés és a portok között aktív hálózati kapcsolatokkal rendelkező folyamatok futtatásával bármely TCP-kapcsolattal összekötött architektúrában között megadott időtartomány keresztül.  Folyamat részletei kibontása egy virtuális Gépet jeleníti meg, és csak a virtuális gép kommunikáló folyamatokat látható. Az előtér-, a virtuális géppel csatlakozó ügyfelek száma az ügyfél csoportban jelzi. A háttér-kiszolgálók száma a virtuális gép csatlakozik a kiszolgáló Portcsoportokat a változáskövető. Bontsa ki a kiszolgáló-portok csoportja adott porton keresztül csatlakozó kiszolgálók részletes listájának megtekintéséhez.  

A virtuális gép kattintva a **tulajdonságok** ablaktábla ki van bontva, például a rendszer-információkat az operációs rendszer, az Azure virtuális Gépen, és a egy perec tulajdonságainak által jelentett kijelölt elem tulajdonságainak megjelenítéséhez a jobb oldalon a felderített kapcsolatok összegzése. 

![A számítógép rendszer tulajdonságai](./media/vminsights-maps/properties-pane-01.png)

A jobb oldali ablaktábla, a kattintson a **alkalmazásnapló-események** ikonra kattintva válthat fókuszt az ablaktábla listájának megtekintéséhez, amelyen az adatok gyűjtését a virtuális gép rendelkezik a Log Analytics felé küldött, és érhető el lekérdezés céljából.  A felsorolt erőforrásrekord-típusok közül bármelyik kattint nyílik meg a **naplók** lap használatával jeleníthetők meg, hogy az előre konfigurált lekérdezési típus az eredmények szűrt szemben az adott virtuális géphez.  

![A Tulajdonságok panelen naplófájl-keresési lista](./media/vminsights-maps/properties-pane-logs-01.png)

Zárja be *naplók** és térjen vissza a **tulajdonságai** ablaktáblán, és válassza **riasztások** , riasztások megtekintése, amely riasztást küld a virtuális gép állapotára vonatkozó feltételek a kiváltott. Térkép integrálható az Azure-riasztások a kiválasztott kiszolgálóhoz tartozó aktivált riasztások megjelenítéséhez a kijelölt időtartományban található. A kiszolgáló ikont jelenít meg, ha nincsenek aktuális riasztásokat, és a gép riasztások panel felsorolja a riasztásokat. 

![A Tulajdonságok panelen gép riasztások](./media/vminsights-maps/properties-pane-alerts-01.png)

A kapcsolódó riasztások megjelenítése térképen funkciónak az engedélyezéséhez hozzon létre egy riasztási szabályt, amely akkor aktiválódik, egy adott számítógépen. Megfelelő riasztások létrehozásához:
- Számítógép csoporthoz záradékot tartalmazni (például **számítógép időköze 1 perces**).
- Válasszon riasztást metrikus egység alapján.

További információ az Azure-riasztások és a riasztási szabályok létrehozása: [egyesített riasztások az Azure monitorban](../../azure-monitor/platform/alerts-overview.md)

A **jelmagyarázat** lehetőséget a jobb felső sarkában található szimbólumok és szerepkörök ismerteti a térképen.  A térkép közelebbről a nagyítás, és az informatikai körül, a Nagyítás vezérlők alján az oldal jobb oldalán a nagyítási szint beállítása és az aktuális oldal méretét a laphoz.  

## <a name="connection-metrics"></a>Kapcsolati metrika
A **kapcsolatok** ablaktáblán megjelennek azok a virtuális gép a kiválasztott kapcsolathoz tartozó standard kapcsolati metrikák a TCP-porton keresztül. A mérőszámok közé tartozik a válaszidőt, a kérések száma percenként, a forgalom átviteli sebesség és a hivatkozások.  

![Hálózati kapcsolat diagramok panel például](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Sikertelen kapcsolatok
Sikertelen kapcsolatok jelennek meg a diagramokon a folyamatok és a számítógép, és a egy piros szaggatott vonal jelzi, hogy egy ügyfél rendszer érhetők el egy folyamat és a port nem működik. Sikertelen kapcsolatok függőségi ügynök minden olyan rendszer jelenti, hogy a rendszer egy kísérlet a sikertelen kapcsolódás esetén. Térkép ezt a folyamatot nem sikerült kapcsolatot létesíteni a TCP-szoftvercsatornák megfigyelésével méri. Ez a hiba hatására a tűzfal, melyek a helytelen konfiguráció az ügyfél vagy kiszolgáló vagy egy távoli szolgáltatás nem volt elérhető.

![A térképen a sikertelen kapcsolat – példa](./media/vminsights-maps/map-group-failed-connection-01.png)

Sikertelen kapcsolatok hibaelhárítása, a migrálás ellenőrzési, a biztonsági elemzés segíthet ismertetése, és az általános architektúrát, a szolgáltatás ismertetése. Sikertelen kapcsolatok néha érintetlen, de gyakran közvetlenül a problémát, például egy feladatátvételi környezetet hirtelen váljon nem érhető el, vagy éppen nem lehet kommunikálni egymással a felhőbe történő migrálás után két alkalmazásrétegek mutassanak.

### <a name="client-groups"></a>Ügyfélcsoportok
A térképen ügyfélcsoportok ügyfélgépek, amelyek a csatlakoztatott géppé kapcsolatokat jelölik. Egyetlen ügyfél csoport jelöli az ügyfelek egy egyedi folyamat vagy a gépen.

![Ügyfél csoportokat például a térképen](./media/vminsights-maps/map-group-client-groups-01.png)

A felügyelt ügyfelek és a rendszerek egy ügyfélcsoportba tartozó IP-címek megtekintéséhez válassza ki a csoportot. A csoport alatt jelennek meg a csoport tartalmát.  

![Ügyfél csoport IP cím listája például a térképen](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Ha a csoport a felügyelt és nem felügyelt ügyfeleket tartalmaz, válassza a perecdiagram szűrése az ügyfelek a csoportban a megfelelő szakaszban.

### <a name="server-port-groups"></a>Kiszolgálóport csoportok
Kiszolgálóport csoportok kaphatnak rendelkező kiszolgálók kiszolgáló portjait bejövő kapcsolatok a csatlakoztatott gépen. A csoport tartalmazza a kiszolgáló portja és a kiszolgálót a kapcsolatok erre a portra számát. Válassza ki a csoport egyes kiszolgálók és a kapcsolatok listán. 

![Kiszolgálócsoport-Port a példában a térképen](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Ha a csoport tartalmazza a felügyelt és a figyelés, válassza a perecdiagram szűrése a kiszolgálók a csoport a megfelelő szakaszt.

## <a name="view-map-directly-from-a-virtual-machine"></a>Térkép megtekintése közvetlenül a virtuális gépről 

Hozzáférés az Azure Monitor-beli virtuális gépek közvetlenül a virtuális gépről, hajtsa végre a következő.

1. Az Azure Portalon válassza ki a **virtuális gépek**. 
2. A listából válassza ki a virtuális gép és a a **figyelés** szakaszban válasszon **Insights (előzetes verzió)**.  
3. Válassza ki a **térkép** fülre.

Térkép megjeleníti a virtuális gépek függőségeit, futtató csoportosítja, és a folyamatok aktív hálózati kapcsolatokkal keresztül megadott időtartományt.  Alapértelmezés szerint a térképen jeleníti meg az elmúlt 30 percben.  Használatával a **TimeRange** választó a bal felső sarkában, lekérdezheti múltbéli porttartományok akár egy óráig bemutatják, hogyan függőségek kikeresi az elmúlt (például az incidens alatt vagy előtt történt változás).  

![Közvetlen virtuális gépek – áttekintés](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Az Azure Monitor térkép megtekintése
Az Azure Monitor a térkép funkciót biztosít a virtuális gépek és azok függőségeit tartalmazó globális nézet.  A térkép-funkció eléréséhez az Azure Monitor, hajtsa végre a következő. 

1. Az Azure Portalon válassza ki a **figyelő**. 
2. Válasszon **(előzetes verzió) virtuális gépek** a a **Insights** szakaszban.
3. Válassza ki a **térkép** fülre.

![Az Azure virtuális gépre kiterjedő térkép – áttekintés](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Az a **munkaterület** , az oldal tetején található választóval, ha egynél több Log Analytics-munkaterületen válassza a munkaterület, amely a megoldás engedélyezve van, és virtuális gépek jelent. A **csoport** választóval adja vissza az előfizetések, erőforráscsoportok, [számítógépcsoportok](../../azure-monitor/platform/computer-groups.md), és a Virtuálisgép-méretezési csoportok a kijelölt munkaterülethez kapcsolódó számítógépek. A kijelölt csak a térkép funkció vonatkozik, és nem elterjednek teljesítmény vagy a térképen.

Alapértelmezés szerint a térképen jeleníti meg az elmúlt 30 percben. Használatával a **TimeRange** választó, lekérdezheti, akár egy óráig bemutatják, hogyan függőségek kikeresi az elmúlt (például az incidens alatt vagy előtt módosítás történt) múltbéli címtartományok esetében.   

## <a name="next-steps"></a>További lépések
Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [megtekintése az Azure virtuális gép állapota](vminsights-health.md), vagy szűk keresztmetszetei és a virtuális gépek teljesítményét a teljes kihasználtság azonosításához tekintse meg a [megtekintése az Azure Monitor a virtuális gépek teljesítmény](vminsights-performance.md). 
