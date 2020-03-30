---
title: Az alkalmazások függőségének megtekintése a virtuális gépekHez tartozó Azure Monitor szolgáltatással
description: A Map az Azure Monitor virtuális gépekhez szolgáltatása. Automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ez a cikk a Térkép szolgáltatás különböző esetekben való használatának részleteit ismerteti.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283854"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Az Azure Monitor szolgáltatás a virtuális gépekhez szolgáltatás használatával az alkalmazás-összetevők megértéséhez
A virtuális gépek hez az Azure-figyelőben megtekintheti az Azure-ban vagy a környezetben futó Windows és Linux virtuális gépeken (VM-ek). A virtuális gépeket kétféleképpen figyelheti meg. Tekintse meg a térképet közvetlenül a virtuális gépről, vagy tekintse meg a térképet az Azure Monitor az összetevők a virtuális gépek csoportok közötti megtekintéséhez. Ez a cikk segít megérteni ezt a két megtekintési módszert és a Térkép funkció használatát. 

Az Azure Monitor virtuális gépekhez való konfigurálásáról az [Azure Monitor engedélyezése a virtuális gépekhez](vminsights-enable-overview.md)című témakörben talál.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="introduction-to-the-map-experience"></a>Bevezetés a térképélménybe
Mielőtt belemerülne a Térkép élménybe, meg kell értenie, hogyan mutatja be és vizualizálja az információkat. Akár közvetlenül a virtuális gépről, akár az Azure Monitorról választja a Map funkciót, a Map funkció egységes élményt nyújt. Az egyetlen különbség az, hogy az Azure Monitor, egy térkép egy többrétegű alkalmazás vagy fürt összes tagját jeleníti meg.

A Térkép szolgáltatás vizualizálja a virtuális gép függőségeit a következő futó folyamatok felfedezésével: 

- Aktív hálózati kapcsolatok a kiszolgálók között.
- Bejövő és kimenő kapcsolat késése.
- Portok bármely TCP-hez kapcsolódó architektúrában egy megadott időtartományban.  
 
Bontsa ki a virtuális gép a folyamat részleteinek megjelenítéséhez, és csak azokat a folyamatokat, amelyek kommunikálnak a virtuális gép. Az ügyfélcsoport a virtuális géphez csatlakozó előtér-ügyfelek számát jeleníti meg. A kiszolgáló-port csoportok a virtuális gép által csatlakozott háttérkiszolgálók számát jelenítik meg. Bontsa ki a kiszolgáló-port csoportot az adott porton keresztül csatlakozó kiszolgálók részletes listájának megtekintéséhez.  

Ha kiválasztja a virtuális gép, a **tulajdonságok** ablaktábla a jobb oldalon a virtuális gép tulajdonságait jeleníti meg. A tulajdonságok közé tartozik az operációs rendszer által jelentett rendszeradatok, az Azure virtuális gép tulajdonságai és egy perecdiagram, amely összefoglalja a felderített kapcsolatokat. 

![A Tulajdonságok ablaktábla](./media/vminsights-maps/properties-pane-01.png)

Az ablaktábla jobb oldalán válassza **az Események naplózása** lehetőséget a virtuális gép által az Azure Monitornak küldött adatok listájának megjelenítéséhez. Ezek az adatok lekérdezésre is rendelkezésre állnak.  Bármelyik bejegyzéstípust kijelölve nyissa meg a Naplók lapot, ahol az adott bejegyzéstípus eredményeit **láthatja.** A virtuális gép re szűrt előre konfigurált lekérdezésis megjelenik.  

![A Naplóesemények ablaktábla](./media/vminsights-maps/properties-pane-logs-01.png)

Zárja be a **Naplók** lapot, és térjen vissza a **Tulajdonságok** ablaktáblára. Itt válassza **a Riasztások lehetőséget** a virtuális gép állapotfeltétel-riasztási nézeteinek megtekintéséhez. A Map funkció integrálható az Azure Alerts a kiválasztott kiszolgáló riasztások megjelenítése a kiválasztott időtartományban. A kiszolgáló ikont jelenít meg az aktuális riasztásokhoz, és a **Gépriasztások** ablaktábla felsorolja a riasztásokat. 

![A Riasztások ablaktábla](./media/vminsights-maps/properties-pane-alerts-01.png)

Ha azt szeretné, hogy a Térkép szolgáltatás releváns riasztásokat jelenítsen meg, hozzon létre egy riasztási szabályt, amely egy adott számítógépre vonatkozik:

- Adjon meg egy záradékot a riasztások számítógépenként történő csoportosításához (például **1 perces számítógép-intervallum szerint).**
- A riasztás alapja egy metrika.

Az Azure-riasztásokról és a riasztási szabályok létrehozásáról az Egyesített riasztások az [Azure Monitorban](../../azure-monitor/platform/alerts-overview.md)című témakörben talál további információt.

A jobb felső sarokban a **Jelmagyarázat** beállítás a térképen lévő szimbólumokat és szerepköröket írja le. Ha közelebbről is meg szeretné tekinteni a térképet, és szeretné mozgatni, használja a jobb alsó sarokban található nagyítási vezérlőket. Beállíthatja a nagyítási szintet, és a térképet az oldal méretéhez igazíthatja.  

## <a name="connection-metrics"></a>Csatlakozási mutatók
A **Kapcsolatok** ablaktábla a kijelölt kapcsolat szabványos metrikákat jelenít meg a virtuális gépről a TCP-porton keresztül. A metrikák tartalmazzák a válaszidőt, a percenkénti kérelmeket, a forgalmi átviteli forgalmat és a hivatkozásokat.  

![Hálózati kapcsolatdiagramok a Kapcsolatok ablaktáblán](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Sikertelen kapcsolatok
A térkép a folyamatok és számítógépek sikertelen kapcsolatait mutatja. A szaggatott piros vonal azt jelzi, hogy az ügyfélrendszer nem ér el egy folyamatot vagy portot. A függőségi ügynököt használó rendszerek esetében az ügynök sikertelen csatlakozási kísérleteket jelent. A Térkép szolgáltatás egy folyamatot figyel meg, ha megfigyeli azokat a TCP-szoftvercsatornákat, amelyek nem hoznak létre kapcsolatot. Ez a hiba a tűzfal, az ügyfél vagy a kiszolgáló helytelen konfigurációja vagy egy nem elérhető távoli szolgáltatás eredménye.

![Sikertelen kapcsolat a térképen](./media/vminsights-maps/map-group-failed-connection-01.png)

A sikertelen kapcsolatok ismertetése segíthet a hibaelhárításban, az áttelepítés érvényesítésében, a biztonság elemzésében és a szolgáltatás általános architektúrájának megértésében. A sikertelen kapcsolatok néha ártalmatlanok, de gyakran egy problémára mutatnak. A kapcsolatok például sikertelenek lehetnek, ha egy feladatátvételi környezet hirtelen elérhetetlenné válik, vagy ha két alkalmazásszint nem tud kommunikálni egymással egy felhőbe való migrálás után.

### <a name="client-groups"></a>Ügyfélcsoportok
A térképen az ügyfélcsoportok olyan ügyfélgépeket jelölnek, amelyek a leképezett géphez csatlakoznak. Egyetlen ügyfélcsoport képviseli az ügyfelek egy adott folyamat vagy gép.

![Ügyfélcsoport a térképen](./media/vminsights-maps/map-group-client-groups-01.png)

A figyelt ügyfelek és az ügyfélcsoport rendszereinek IP-címei megtekintéséhez jelölje ki a csoportot. A csoport tartalma az alábbiakban jelenik meg.  

![Egy ügyfélcsoport IP-címeinek listája a térképen](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Ha a csoport figyelt és nem figyelt ügyfeleket tartalmaz, kiválaszthatja a csoport perecdiagramjának megfelelő szakaszát az ügyfelek szűréséhez.

### <a name="server-port-groups"></a>Kiszolgálóport-csoportok
A kiszolgálóport-csoportok olyan kiszolgálók portjait jelölik, amelyek bejövő kapcsolattal rendelkeznek a leképezett gépről. A csoport tartalmazza a kiszolgálóportot és az adott porthoz kapcsolattal létesített kiszolgálók számát. Válassza ki a csoportot az egyes kiszolgálók és kapcsolatok megtekintéséhez. 

![Kiszolgáló-port csoport a térképen](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Ha a csoport figyelt és nem figyelt kiszolgálókat tartalmaz, a kiszolgálók szűréséhez kiválaszthatja a csoport perecdiagramjának megfelelő szakaszát.

## <a name="view-a-map-from-a-vm"></a>Térkép megtekintése virtuális gépről 

Az Azure Monitor virtuális gépekhez való hozzáférés e közvetlenül a virtuális gép:

1. Az Azure Portalon válassza a **Virtuális gépek**lehetőséget. 
2. A listából válasszon egy virtuális gép. A **Figyelés** csoportban válassza az **Insights**lehetőséget.  
3. Válassza a **Térkép** lapot.

A térkép vizualizálja a virtuális gép függőségeit azáltal, hogy felkötteti azokat a futó folyamatcsoportokat és folyamatokat, amelyek egy adott időtartományon belül aktív hálózati kapcsolattal rendelkeznek.  

Alapértelmezés szerint a térkép az utolsó 30 percet jeleníti meg. Ha szeretné látni, hogyan függőségek nézett ki a múltban, lekérdezheti a korábbi időtartományok legfeljebb egy óra. A lekérdezés futtatásához használja a **TimeRange** választógombot a bal felső sarokban. Futtathat például egy lekérdezést egy incidens során, vagy megtekintheti az állapotot a módosítás előtt.  

![Közvetlen virtuális gép térkép – áttekintés](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Térkép megtekintése virtuálisgép-méretezési csoportból

Az Azure Monitor virtuális gépekhez való hozzáférés e közvetlenül a virtuális gép méretezési csoport:

1. Az Azure Portalon válassza a **Virtuálisgép-méretezési csoportok lehetőséget.**
2. A listából válasszon egy virtuális gép. Ezután a **Figyelés** csoportban válassza az **Insights**lehetőséget.  
3. Válassza a **Térkép** lapot.

A térkép a méretezési csoport összes példányát a csoportcsomópontként, valamint a csoport függőségeit vizualizálja. A kibontott csomópont felsorolja a méretezési csoport példányait. Ezeken a példányokon egyszerre 10-et görgethet. 

Egy adott példány térképének betöltéséhez először jelölje ki azt a példányt a térképen. Ezután válassza a jobb oldali **három pont** gombot (...), és válassza a **Kiszolgálóleképezés betöltése**parancsot . A megjelenő térképen olyan folyamatcsoportok és folyamatok láthatók, amelyek egy adott időtartományon keresztül aktív hálózati kapcsolattal rendelkeznek. 

Alapértelmezés szerint a térkép az utolsó 30 percet jeleníti meg. Ha szeretné látni, hogyan függőségek nézett ki a múltban, lekérdezheti a korábbi időtartományok legfeljebb egy óra. A lekérdezés futtatásához használja a **TimeRange** választót. Futtathat például egy lekérdezést egy incidens során, vagy megtekintheti az állapotot a módosítás előtt.

![Közvetlen virtuális gép térkép – áttekintés](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Egy adott példány térképét is elérheti a **virtuálisgép-méretezési** csoport Példányok nézetéből. A **Beállítások** szakaszban nyissa meg **a Példányok elemzési** > **adatait.**

## <a name="view-a-map-from-azure-monitor"></a>Térkép megtekintése az Azure Monitorból

Az Azure Monitorban a Map funkció globális képet nyújt a virtuális gépekről és azok függőségeiről. A Map funkció elérése az Azure Monitorban:

1. Az Azure Portalon válassza a **Figyelő**lehetőséget. 
2. Az **Insights (Insights)** csoportban válassza a **Virtuális gépek**lehetőséget.
3. Válassza a **Térkép** lapot.

   ![Több virtuális gép Azure Monitorának áttekintő térképe](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Válasszon munkaterületet a **Workspace** lap tetején található Munkaterület-választóval. Ha egynél több Log Analytics-munkaterülettel rendelkezik, válassza ki azt a munkaterületet, amely engedélyezve van a megoldással, és amelyhez virtuális gépek jelentenek. 

A **Csoportválasztó** a kijelölt munkaterülethez kapcsolódó előfizetéseket, erőforráscsoportokat, [számítógépcsoportokat](../../azure-monitor/platform/computer-groups.md)és virtuálisgép-méretezési csoportokat ad vissza. A kijelölés csak a Térkép funkcióra vonatkozik, és nem viszi át a Teljesítmény vagy az Egészség szolgáltatásba.

Alapértelmezés szerint a térkép az utolsó 30 percet jeleníti meg. Ha szeretné látni, hogyan függőségek nézett ki a múltban, lekérdezheti a korábbi időtartományok legfeljebb egy óra. A lekérdezés futtatásához használja a **TimeRange** választót. Futtathat például egy lekérdezést egy incidens során, vagy megtekintheti az állapotot a módosítás előtt.  

## <a name="next-steps"></a>További lépések

A szűk keresztmetszetek azonosításához, a teljesítmény ellenőrzéséhez és a virtuális gépek általános kihasználásának megismeréséhez olvassa [el a Virtuális gépek teljesítményállapotának megtekintése](vminsights-performance.md)című témakört. 
