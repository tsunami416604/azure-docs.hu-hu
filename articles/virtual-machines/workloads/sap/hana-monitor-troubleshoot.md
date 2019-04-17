---
title: Az SAP HANA az Azure-ban (nagyméretű példányok) oldalán figyelése és hibaelhárítása a HANA-ból |} A Microsoft Docs
description: Figyelés és hibaelhárítás a HANA oldalán, az SAP HANA az Azure-beli (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71970a74817665c97a9522fbc9a68dd3834252b9
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59616356"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA-oldali monitorozás és hibaelhárítás

Annak érdekében, hogy hatékonyan elemezheti az Azure-ban (nagyméretű példányok) SAP Hana-val kapcsolatos problémák, hasznos lehet a probléma alapvető okának szűkítéséhez. SAP dokumentáció párosításával segítenek nagy mennyiségű tett közzé.

Az SAP HANA-teljesítményt kapcsolatos vonatkozó gyakori kérdések az alábbi SAP-megjegyzések található:

- [SAP-Jegyzetnek #2222200 – gyakori kérdések: Az SAP HANA-hálózat](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP-Jegyzetnek #2100040 – gyakori kérdések: AZ SAP HANA PROCESSZOR](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP-Jegyzetnek #199997 – gyakori kérdések: Az SAP HANA memória](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP-Jegyzetnek #200000 – gyakori kérdések: Az SAP HANA-teljesítmény optimalizálása](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP-Jegyzetnek #199930 – gyakori kérdések: Az SAP HANA i/o-elemzés](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP-Jegyzetnek #2177064 – gyakori kérdések: SAP HANA-szolgáltatás újraindítása, és összeomlik](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Az SAP HANA-riasztások

Első lépésként tekintse meg a jelenlegi riasztási SAP HANA-naplókat. Az SAP HANA Studio, lépjen a **felügyeleti konzol: Riasztások: Megjelenítés: az összes riasztás**. Ezen a lapon adott értékekre (szabad fizikai memória, CPU-kihasználtság, stb.) a állítsa be a minimális és maximális küszöbértékét kívül eső összes SAP HANA-riasztások jelennek meg. Alapértelmezés szerint ellenőrzi a automatikusan frissülnek minden 15 percben.

![Az SAP HANA Studio nyissa meg a felügyeleti konzol: Riasztások: Megjelenítése: az összes riasztás](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Küszöbérték helytelen beállítás miatt aktivált riasztás megoldását, hogy állítsa vissza az alapértelmezett érték vagy egy további ésszerű küszöbértéket.

![Állítsa vissza az alapértelmezett érték vagy egy további ésszerű küszöbértéket](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Az alábbi riasztásokat utalhat a Processzor-erőforrás problémák:

- Gazdagép CPU-használat (riasztás 5)
- Legutóbbi mentésipont-művelet (riasztás 28)
- Mentési pont időtartama (riasztás 54)

Azt tapasztalhatja, hogy magas CPU-felhasználás az SAP HANA Database, az alábbi lehetőségek közül:

- 5. riasztási (gazdagép CPU-használat) jelenik meg, a jelenlegi és volt ügyfeleinktől CPU-használat
- A megjelenített CPU-használat az Áttekintés képernyő

![CPU-használat megjelenik az áttekintő képernyő](./media/troubleshooting-monitoring/image3-cpu-usage.png)

A gráf betöltése előfordulhat, hogy magas CPU-felhasználás, vagy nagy használat az elmúlt megjelenítése:

![A gráf betöltése előfordulhat, hogy megjelenítése nagy CPU-felhasználás, vagy magas felhasználás az elmúlt](./media/troubleshooting-monitoring/image4-load-graph.png)

Több oka is, de nem kizárólagosan miatt magas CPU-kihasználtság aktivált riasztás oka: bizonyos tranzakciók, az adatok betöltése, függő feladatok, mennyi ideig fut az SQL-utasításokkal, és a hibás lekérdezési teljesítmény (például az SAP BW on HANA végrehajtása a kockák).

Tekintse meg a [SAP HANA-hibáinak elhárítása: CPU kapcsolódó okoz, és a megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="operating-system"></a>Operációs rendszer

A legfontosabb ellenőrzi az SAP Hana Linux rendszeren egyik, győződjön meg arról, hogy a transzparens hatalmas lapok le vannak tiltva, hogy [SAP Megjegyzés #2131662 – transzparens hatalmas lapok (THP) SAP HANA-kiszolgálókon](https://launchpad.support.sap.com/#/notes/2131662).

- Ha transzparens hatalmas oldalak engedélyezve vannak-e a következő Linux parancs használatával ellenőrizheti: **/sys/kernel/mm/transparent macskakép\_hugepage/engedélyezve**
- Ha _mindig_ zárt szögletes zárójeleket az alábbi, az azt jelenti, hogy engedélyezve vannak-e a transzparens hatalmas lapok: [mindig] madvise soha nem; Ha _soha nem_ zárt szögletes zárójeleket az alábbi, az azt jelenti, hogy az átlátszó hatalmas Lapok le vannak tiltva: mindig madvise [soha nem]

A következő Linux-parancsot kell semmit nem adott vissza: **rpm - qa |} grep ulimit.** Ha a _ulimit_ van telepítve, távolítsa el azonnal.

## <a name="memory"></a>Memory (Memória)

Akkor lehet, hogy ellenőrizze, hogy az SAP HANA-adatbázis által lefoglalt memória szintje a vártnál magasabb. Az alábbi riasztások magas memóriahasználat kapcsolatos hibákat jelzik:

- Gazdagép fizikai memória kihasználtsága (riasztás 1.)
- A névkiszolgáló (riasztás 12) memóriahasználata
- A teljes memóriahasználat oszlop Store táblák (riasztás 40)
- Memóriahasználat (riasztás 43) szolgáltatások
- Oszlop Store táblák (riasztás 45) fő tárolási memóriahasználata
- Futásidejű memóriaképeket (riasztás 46)

Tekintse meg a [SAP HANA-hibáinak elhárítása: A memóriával kapcsolatos problémák](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="network"></a>Network (Hálózat)

Tekintse meg [SAP Megjegyzés #2081065 – SAP HANA hálózati hibaelhárítási](https://launchpad.support.sap.com/#/notes/2081065) , és végezze el a hibaelhárítási lépések az SAP-Jegyzetnek a hálózaton.

1. Kiszolgáló és az ügyfél közötti üzenetváltás idő elemzéséhez.
  A. Az SQL-parancsfájl futtatása [ _HANA\_hálózati\_ügyfelek_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Csomópontok kommunikációs elemzése.
  A. SQL-parancsfájl futtatása [ _HANA\_hálózati\_szolgáltatások_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Linux-parancs futtatása **ifconfig** (a kimenet mutatja, ha a csomag veszteségeiért is megjelenhetnek).
4. Linux-parancs futtatása **tcpdump**.

Ezenkívül használhatja a nyílt forráskódú [IPERF](https://iperf.fr/) eszköz (vagy hasonlót) alkalmazás valós hálózati teljesítmény mérésére.

Tekintse meg a [SAP HANA-hibáinak elhárítása: Hálózati teljesítmény és a kapcsolódási problémák](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="storage"></a>Storage

A végfelhasználói szempontból egy alkalmazás (vagy a rendszer egészének) nehézkesen fut, nem válaszol vagy nem válaszol, ha probléma adódik az i/o-teljesítmény akár tűnhet. Az a **kötetek** fülre az SAP HANA Studio, a csatlakoztatott köteteket, és milyen kötetek minden egyes szolgáltatás által használt láthatja.

![Az SAP HANA Studio kötetek lapján látható a csatlakoztatott köteteket, és milyen kötetek minden egyes szolgáltatás által használt](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Csatlakoztatott kötetek a képernyő alsó részén láthatja a kötetekről, például fájlok és i/o-statisztikai adatait.

![Csatlakoztatott kötetek a képernyő alsó részén láthatja a kötetekről, például fájlok és i/o-statisztikák részletei](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Tekintse meg a [SAP HANA-hibáinak elhárítása: I/o kapcsolatos alapvető okait és megoldásait](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) és [SAP HANA-hibáinak elhárítása: Kapcsolódó alapvető okok és-megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="diagnostic-tools"></a>A diagnosztikai eszközök

Hajtsa végre az SAP HANA állapot-ellenőrzés keresztül HANA\_konfigurációs\_Minichecks. Ezzel az eszközzel kell már merült fel az SAP HANA Studio riasztásai potenciálisan kritikus technikai problémák adja vissza.

Tekintse meg [SAP Megjegyzés #1969700 – SAP HANA SQL-utasítás gyűjtemény](https://launchpad.support.sap.com/#/notes/1969700) , töltse le a jegyzet csatolva SQL Statements.zip. A .zip-fájlt a helyi merevlemezen Store.

Az SAP HANA Studio a a **rendszer-információkat** lapra, kattintson a jobb gombbal a **neve** oszlopra, és válassza **importálása SQL-utasítások**.

![Az SAP HANA Studio, a rendszer-információ lapon kattintson a jobb gombbal az oszlop, és válassza ki az importálás SQL-utasítások](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Válassza ki a helyben tárolt SQL Statements.zip fájlt, és importálja a megfelelő SQL-utasítások nevű mappa. Ezen a ponton a számos különböző diagnosztikai ellenőrzéseket is futtatható a következő SQL-utasításokat.

Például, ha tesztelni szeretné az SAP HANA-Rendszerreplikálást sávszélességre van szükség, kattintson a jobb gombbal a **sávszélesség** utasítás alatt **replikációs: A sávszélesség** válassza **nyílt** SQL-konzolon.

A teljes SQL-utasítás nyílik meg, így a bemeneti paraméterek (módosításának szakasz) módosult, és akkor hajtja végre.

![A teljes SQL-utasítás nyílik meg, így a bemeneti paraméterek (módosításának szakasz) módosult, és akkor hajtja végre](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Egy másik példa a jobb gombbal kattint a utasítások alapján a **replikációs: Áttekintés**. Válassza ki **Execute** és a helyi menüben:

![A következő replikációs csoportban az utasításokat a jobb gombbal kattint egy másik példa: Áttekintése. A helyi menüből válassza ki a végrehajtás](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Ez azt eredményezi, amely segít a hibaelhárítási információkat:

![Ennek eredményeképpen az információkat, amelyek segítenek a hibaelhárításban](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Tegye meg ugyanezt a HANA\_konfigurációs\_Minichecks, és ellenőrizze a _X_ megjelöli a a _C_ (kritikus) oszlopban.

Minta kimenete:

**HANA\_konfigurációs\_MiniChecks\_Rev102.01 + 1** az általános SAP HANA ellenőrzi.

![HANA\_konfigurációs\_MiniChecks\_Rev102.01 + 1 a SAP HANA általános ellenőrzések](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_szolgáltatások\_áttekintése** mi SAP HANA futó szolgáltatások áttekintését.

![HANA\_szolgáltatások\_áttekintése, mi az SAP HANA futó szolgáltatások áttekintése](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_szolgáltatások\_statisztika** az SAP Hana szolgáltatás az adatai (CPU, memória, stb.).

![HANA\_szolgáltatások\_statisztikáit az SAP Hana-szolgáltatás adatai](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfigurációs\_áttekintése\_Rev110 +** általános információk a SAP HANA-példányon.

![HANA\_konfigurációs\_áttekintése\_Rev110 + az SAP HANA-példány általános információk](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfigurációs\_paraméterek\_Rev70 +** SAP HANA-paramétereket.

![HANA\_konfigurációs\_paraméterek\_Rev70 + SAP HANA-paramétereket](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Következő lépések**

- Tekintse meg [magas rendelkezésre állás beállítása a STONITH használatával SUSE](ha-setup-with-stonith.md).