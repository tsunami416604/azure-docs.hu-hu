---
title: A virtuális gépek (előzetes verzió) és az Azure Monitor alkalmazásfüggőségek megtekintése |} A Microsoft Docs
description: Térkép az Azure Monitor-beli virtuális gépek egy funkciója. Automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk ismerteti a különböző forgatókönyvekben a térkép funkció használatához.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206757"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>A térkép funkció segítségével az Azure Monitor-beli virtuális gépek (előzetes verzió) alkalmazás-összetevők ismertetése
Azure Monitor-beli virtuális gépek megtekintheti a Windows és Linux rendszerű futtató virtuális gépeket (VM) az Azure-ban vagy a környezet a detektált alkalmazás-összetevők. Figyelheti, hogy a virtuális gépeket kétféle módon. Közvetlenül a virtuális gépek a leképezés megjelenítéséhez, vagy az Azure Monitor az összetevők megtekintéséhez a csoportok a virtuális gépek közötti leképezés megjelenítéséhez. A cikknek a segítségével megismerheti a megtekintésre két módszer közül, és a térkép funkció használata. 

A virtuális gépek az Azure Monitor konfigurálásával kapcsolatos további információkért lásd: [engedélyezése az Azure Monitor-beli virtuális gépek](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>A térkép élményt bemutatása
Mielőtt belevágna a térkép tapasztalatokat, tisztában kell lennie a módját, megadja elérhetővé, és információkat. A térkép szolgáltatást közvetlenül a virtuális gép vagy az Azure Monitor válassza ki, hogy a térkép funkció megjelenít egy egységes felhasználói élményt. Az egyetlen különbség, hogy az Azure monitorral egy térképen jeleníti meg a többrétegű alkalmazások vagy a fürt összes tagja.

A térkép funkció megjeleníti a virtuális gép függőségeit rendelkező folyamatok futtatásával derítse fel: 

- Kiszolgálók közötti aktív hálózati kapcsolatokat.
- Bejövő és kimenő kapcsolat késleltetése.
- Portok között, bármely TCP-kapcsolattal összekötött architektúrában megadott időtartomány keresztül.  
 
Bontsa ki a folyamat részletei, és csak a virtuális gép kommunikáló folyamatok megjelenítése egy virtuális Gépet. Az ügyfélcsoport jeleníti meg. a virtuális géppel előtér-ügyfelek. A kiszolgálóport csoportok száma, a virtuális gép csatlakozik a háttér-kiszolgálók megjelenítése. Bontsa ki a kiszolgálóport csoportot adott porton keresztül csatlakozó kiszolgálók részletes listájának megtekintéséhez.  

A virtuális gép kiválasztásakor a **tulajdonságok** a jobb oldali panelen látható a virtuális gép tulajdonságait. Tulajdonságok között az operációs rendszer, az Azure virtuális Gépen, és a egy perecdiagramot, amely összefoglalja a felderített kapcsolatok tulajdonságainak által jelentett rendszer-információkat. 

![A Tulajdonságok panelen](./media/vminsights-maps/properties-pane-01.png)

A panel jobb oldalán válassza ki a **alkalmazásnapló-események** megjelenítéséhez, amelyek a virtuális gép az Azure monitornak küldött adatok listája. Ezek az adatok lekérdezéséhez érhető el.  Minden rekordtípus megnyitásához válassza a **naplók** lap, ahol adott rekordtípushoz az eredményeket látja. A virtuális gép alapján szűrt előre konfigurált lekérdezés is megjelenik.  

![A naplózási események ablak](./media/vminsights-maps/properties-pane-logs-01.png)

Zárja be a **naplók** lapon, és térjen vissza a **tulajdonságok** ablaktáblán. Itt kattintson **riasztások** a virtuális gép állapota – feltételek riasztások megtekintése érdekében. A térkép szolgáltatás integrálható az Azure-riasztások a kiválasztott kiszolgálóhoz tartozó riasztások megjelenítéséhez a kijelölt időtartományban található. A kiszolgáló aktuális riasztások ikont jelenít meg, és a **gép riasztások** panel felsorolja a riasztásokat. 

![A riasztások panelen](./media/vminsights-maps/properties-pane-alerts-01.png)

Ahhoz, hogy a kapcsolódó riasztások megjelenítése térképen funkció, hozzon létre egy riasztási szabályt, amely egy adott számítógép vonatkozik:

- A csoport riasztásokra záradékot tartalmazni a számítógép által (például **számítógép időköze 1 perces**).
- A riasztás alapja egy metrikát.

További információ az Azure-riasztások és a riasztási szabályok létrehozása: [az Azure monitorban riasztásokat egyesített](../../azure-monitor/platform/alerts-overview.md).

A jobb felső sarokban a **jelmagyarázat** beállítást ismerteti a szimbólumok és a szerepkörök a térképen. Közelebbről a térképen, és át körül használja a Nagyítás vezérlők jobb alsó sarokban. A nagyítás szintjének beállítása, és az oldal méretét a térképet méretéhez igazíthatja.  

## <a name="connection-metrics"></a>Kapcsolati metrika
A **kapcsolatok** ablaktáblán megjelennek azok a virtuális gép a kiválasztott kapcsolathoz tartozó standard mérőszámok a TCP-porton keresztül. A mérőszámok közé tartozik a válaszidőt, a kérések száma percenként, a forgalom átviteli sebesség és a hivatkozások.  

![A Connections ablaktáblában a hálózati kapcsolat diagramok](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Sikertelen kapcsolatok
A map megmutatja a sikertelen kapcsolatokat folyamatok és a számítógépek számára. Piros szaggatott vonal jelzi, hogy ügyfélrendszer érhetők el egy folyamat és a port nem működik. A függőségi ügynököt használó rendszerek esetén az ügynök jelentéseket küld a sikertelen csatlakozási kísérletek. A térkép szolgáltatást nem sikerült kapcsolatot létesíteni a TCP-szoftvercsatornák betartásával egy folyamat figyeli. Ez a hiba, tűzfal, melyek a helytelen konfiguráció az ügyfél vagy kiszolgáló vagy egy nem érhető el távoli szolgáltatás vezethet.

![A sikertelen csatlakozást a térképen](./media/vminsights-maps/map-group-failed-connection-01.png)

Sikertelen kapcsolatok segítségével ismertetése hibaelhárítása, áttelepítés ellenőrzése, elemezheti a biztonsági és megismerheti az általános architektúrát, a szolgáltatás. Sikertelen kapcsolatok néha érintetlen, de egy probléma milyen gyakran mutassanak. Kapcsolatok meghiúsulhat például, ha egy feladatátvételi környezetet hirtelen elérhetetlenné válik, vagy ha a két alkalmazás szinten nem kommunikálnak egymással felhőbe történő migrálás után.

### <a name="client-groups"></a>Ügyfélcsoport
A térképen az ügyfél csoportok kaphatnak az ügyfélgépek, amelyek a csatlakoztatott gép csatlakoznak. Egy adott ügyfélcsoportból jelöli az ügyfelek egy egyedi folyamat vagy a gépen.

![A térképen az ügyfélcsoport](./media/vminsights-maps/map-group-client-groups-01.png)

A felügyelt ügyfelek és a rendszerek egy ügyfélcsoportba tartozó IP-címek megtekintéséhez válassza ki a csoportot. A csoport tartalmát alatt jelennek meg.  

![A térképen IP-címek egy ügyfél csoport listája](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Ha a csoport tartalmazza a felügyelt és nem figyelt ügyfelek számára, válassza a csoport perecdiagram szűrése az ügyfelek számára a megfelelő szakaszban.

### <a name="server-port-groups"></a>Kiszolgálóport csoportok
Kiszolgálóport csoportok kaphatnak rendelkező kiszolgálók port bejövő kapcsolatok a csatlakoztatott gépen. A csoport tartalmazza a kiszolgáló portja és a kiszolgálók, amelyek erre a portra kapcsolatok száma számát. Válassza ki a csoport egyes kiszolgálók és a kapcsolatokat. 

![A térképen egy kiszolgáló-portok csoportja](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Ha a csoport a felügyelt és nem figyelt kiszolgálók is tartalmaz, kiválaszthatja a megfelelő szakaszban szűrése a kiszolgálók a csoport perecdiagram.

## <a name="view-a-map-from-a-vm"></a>Virtuális gép leképezés megjelenítéséhez 

Az Azure Monitor elérése közvetlenül a virtuális gép virtuális gépekhez:

1. Az Azure Portalon válassza ki a **virtuális gépek**. 
2. Válassza ki egy virtuális Gépet a listából. Az a **figyelés** válassza **Insights (előzetes verzió)** .  
3. Válassza ki a **térkép** fülre.

A térkép megjeleníti a virtuális gép függőségeit felderítésével futó folyamat csoportok és folyamatokat, amelyek a megadott időtartomány aktív hálózati kapcsolatokkal rendelkeznek.  

Alapértelmezés szerint a térképen jeleníti meg az elmúlt 30 percben. Ha azt szeretné, hogy hogyan függőségek kikeresi az elmúlt, akár egy óráig múltbéli porttartományok lekérdezheti. A lekérdezés futtatásához használja a **TimeRange** választó a bal felső sarokban. A lekérdezés futhat, például az incidens alatt vagy a változás előtti állapot megtekintéséhez.  

![Közvetlen virtuális gépek – áttekintés](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot a leképezés megjelenítéséhez

Az Azure Monitor-beli virtuális gépek közvetlenül elérhetők egy virtuálisgép-méretezési csoportot:

1. Az Azure Portalon válassza ki a **a Virtual machine scale sets**.
2. Válassza ki egy virtuális Gépet a listából. Ezt a a **figyelés** válassza **Insights (előzetes verzió)** .  
3. Válassza ki a **térkép** fülre.

A térkép összes példányt a méretezési csoport csomópontot a csoport függőségeivel együtt elérhetővé. A kibontott csomópontok felsorolja a méretezési csoportban található példányokhoz. Ezek a példányok 10 görgetheti egyszerre. 

A térkép egy adott példány betöltése, válassza ki annak a példánynak a térképen. Válassza ki a **három** jobbra látható gombra (…), majd **Kiszolgálótérkép betöltése**. A megjelenő térképen megjelenik a folyamat csoportok és folyamatokat, amelyek a megadott időtartomány aktív hálózati kapcsolatokkal rendelkeznek. 

Alapértelmezés szerint a térképen jeleníti meg az elmúlt 30 percben. Ha azt szeretné, hogy hogyan függőségek kikeresi az elmúlt, akár egy óráig múltbéli porttartományok lekérdezheti. A lekérdezés futtatásához használja a **TimeRange** választó. A lekérdezés futhat, például az incidens alatt vagy a változás előtti állapot megtekintéséhez.

![Közvetlen virtuális gépek – áttekintés](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>A térkép egy adott példány esetében is elérhető a **példányok** nézet a virtuális gép méretezési csoporthoz. Az a **beállítások** nyissa meg a szakaszban **példányok** > **Insights (előzetes verzió)** .

## <a name="view-a-map-from-azure-monitor"></a>Az Azure Monitor leképezés megjelenítéséhez
Az Azure monitorban a térkép funkciót biztosít a virtuális gépek és azok függőségeit tartalmazó globális nézet. A térkép-funkció az Azure monitorban elérése:

1. Az Azure Portalon válassza ki a **figyelő**. 
2. Az a **Insights** válassza **(előzetes verzió) virtuális gépek**.
3. Válassza ki a **térkép** fülre.

   ![Az Azure Monitor áttekintés több virtuális gépből](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Válassza ki a munkaterület használatával a **munkaterület** választó, amely a lap tetején. Ha egynél több Log Analytics-munkaterületen, válassza ki a munkaterületet, a megoldás engedélyezve van, és rendelkezik az annak jelentő virtuális gépeket. 

A **csoport** választóval adja vissza az előfizetések, erőforráscsoportok, [számítógépcsoportok](../../azure-monitor/platform/computer-groups.md), és virtuálisgép-méretezési csoportok a kijelölt munkaterülethez kapcsolódó számítógépek. A kijelölt csak azokra a térkép-funkció, és nem jelenik meg teljesítmény vagy egészségügyi.

Alapértelmezés szerint a térképen jeleníti meg az elmúlt 30 percben. Ha azt szeretné, hogy hogyan függőségek kikeresi az elmúlt, akár egy óráig múltbéli porttartományok lekérdezheti. A lekérdezés futtatásához használja a **TimeRange** választó. A lekérdezés futhat, például az incidens alatt vagy a változás előtti állapot megtekintéséhez.  

## <a name="next-steps"></a>További lépések
- Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [megtekintése az Azure virtuális gép állapota](vminsights-health.md). 
- A szűk keresztmetszetek azonosítása, ellenőrizze a teljesítményt, és megismerheti a virtuális gépek teljes kihasználtság, lásd: [teljesítmény állapotának megtekintése az Azure Monitor-beli virtuális gépek](vminsights-performance.md). 
