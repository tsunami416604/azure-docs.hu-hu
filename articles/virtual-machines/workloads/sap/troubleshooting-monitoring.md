---
title: "Hibaelhárítás és megfigyelése az Azure (nagy példányok) SAP HANA |} Microsoft Docs"
description: "Hibaelhárítás, és figyelje az SAP HANA egy Azure (nagy példány)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583f3d1949614dbba4d2f91d72e4ac6b4d03d1c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>És figyelheti az SAP HANA (nagy példány) az Azure-on


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Az SAP HANA Azure (nagy példányok) figyelése

Az Azure (nagy példányok) SAP HANA ugyanolyan helyzetet teremt, az egyéb IaaS-telepítésből – kell figyelnie, az operációs rendszer és az alkalmazás tevékenységétől, és hogyan ezek erőforrást a következő:

- CPU
- Memory (Memória)
- Hálózati sávszélesség
- Lemezterület

Mi a teendő, hogy a fent megnevezett szoftverre erőforrás osztályok-e elegendő, és hogy ezek beolvasása elfogytak van Azure virtuális gépekkel. Íme további információkhoz juthat az egyes a különböző osztályú:

**Processzor-erőforrás-felhasználás:** SAP HANA elleni bizonyos munkaterhelések meghatározott aránya érvénybe van léptetve, győződjön meg arról, hogy az elegendő Processzor-erőforrások a memóriában tárolt adatok keresztül elérhető legyen. Mindazonáltal néhány esetben előfordulhat HANA igényel, ahol nagy mennyiségű Processzor végrehajtott lekérdezések hiányzó indexek vagy hasonló hibák miatt. Ez azt jelenti, célszerű figyelemmel kísérni a HANA nagy példány egység, valamint a Processzor-erőforrások, az adott HANA szolgáltatások által használt CPU hálózatierőforrás-fogyasztás.

**Memória-felhasználás:** fontos, hogy a egységen HANA belül, valamint HANA kívül a figyelheti. Belül HANA hogy az adatok nem használ-e HANA lefoglalt memória ahhoz, hogy az SAP szükséges méretezési útmutatást belül maradnak figyelése. Szeretné a nagy példány szintjén, győződjön meg arról, hogy további telepített nem-HANA szoftver nem túl sok memóriát használ, és ezért versenyeznek HANA-memória memória-felhasználás figyelése.

**Hálózati sávszélességet:** az Azure virtuális hálózatot átjáró korlátozott sávszélesség az adatok áthelyezése az Azure VNet, akkor célszerű egy Vnetet, hogy milyen közel vannak a kiválasztott Termékváltozat Azure átjáró keretein belül az összes Azure VMs által fogadott adatok figyelésére. A nagy példány HANA egységen akkor értelme bejövő és kimenő hálózati forgalom figyelését is, és a kötetek adott idő alatt végrehajtott nyomon követéséhez.

**Szabad lemezterület:** lemezterület-felhasználást általában növekszik adott idő alatt. Ennek számos oka lehet, de a legtöbb összes: adatmennyiség növekszik, a tranzakciónapló biztonsági mentései, nyomkövetési fájlokat tárolja, és a storage-pillanatfelvételekkel végrehajtása végrehajtását. Ezért fontos, a felhasznált lemezterület felügyeletét és kezelését a HANA nagy példány egységhez társított lemezterület.

Az a **típus II termékváltozatok** HANA nagy példánya, a kiszolgáló tartalmaz az előre betöltött diagnosztikai eszközei. A diagnosztikai eszközök az állapot-ellenőrzés végrehajtásához használhatja. A következő parancsot a állapotának ellenőrzése naplófájlt /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Használatakor a Microsoft Support csoport a problémákat, előfordulhat, hogy is kell kérni a naplófájlok a diagnosztikai eszköz használatával. A fájl a következő paranccsal is zip.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Figyelés és hibaelhárítás céljából HANA oldaláról

Ahhoz, hogy hatékonyan elemezheti Azure (nagy példányok) SAP HANA kapcsolatos problémák, célszerű a probléma okának szűkítéséhez. SAP dokumentációja segít nagy mennyiségű tett közzé.

SAP HANA-teljesítménnyel kapcsolatos vonatkozó gyakori kérdések a következő SAP megjegyzések találhatók:

- [SAP Megjegyzés #2222200 – gyakran ismételt kérdések: SAP HANA-hálózat](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Megjegyzés #2100040 – gyakran ismételt kérdések: SAP HANA Processzor](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Megjegyzés #199997 – gyakran ismételt kérdések: SAP HANA memória](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Megjegyzés #200000 – gyakran ismételt kérdések: SAP HANA teljesítmény optimalizálása](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Megjegyzés #199930 – gyakran ismételt kérdések: SAP HANA i/o-elemzés](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Megjegyzés #2177064 – gyakran ismételt kérdések: SAP HANA-szolgáltatás újraindítása és összeomlik](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA riasztások**

Első lépésként a naplófájlokban aktuális SAP HANA riasztások. Az SAP HANA Studio eszközben navigáljon **felügyeleti konzol: riasztások: megjelenítése: az összes riasztás**. Ezen a lapon adott értékekre (szabad fizikai memória, a processzorkihasználtság stb.) a set minimális és maximális küszöbértékek kívül eső összes SAP HANA riasztások jelennek meg. Alapértelmezés szerint legyenek ellenőrzések automatikus frissítése 15 percenként.

![Az SAP HANA Studio, nyissa meg a felügyeleti konzol: riasztások: megjelenítése: az összes riasztás](./media/troubleshooting-monitoring/image1-show-alerts.png)

**PROCESSZOR**

Küszöbérték helytelen beállítás miatt indított riasztást megoldást, hogy visszaállítása az alapértelmezett érték vagy egyéb ésszerű küszöbértéket.

![Az alapértelmezett érték vagy egyéb ésszerű küszöbértéket](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Az alábbi riasztások utalhat a Processzor-erőforrás problémák:

- Gazdagép CPU-használat (riasztás 5)
- Legutóbbi mentésipont-művelet (riasztás 28)
- Mentési pont időtartama (riasztás 54)

Azt tapasztalhatja, hogy magas CPU-felhasználás az SAP HANA-adatbázishoz a következők egyikét:

- Riasztási 5 (gazdagép CPU-használat) jelenik meg, az aktuális és korábbi CPU-használat
- A megjelenített CPU-használat a az Áttekintés képernyő

![CPU-használat jelenik meg az Áttekintés képernyő](./media/troubleshooting-monitoring/image3-cpu-usage.png)

A betöltési graph magas CPU-felhasználás, vagy a fogyasztás magas lehet, hogy megjelenítése a múltban:

![A betöltési graph előfordulhat, hogy megjelenítése magas CPU-felhasználás, vagy magas fogyasztás az elmúlt](./media/troubleshooting-monitoring/image4-load-graph.png)

Lehetséges okok miatt magas fokú Processzorhasználatot tapasztalható kiváltott riasztást több oka is, de nem kizárólagosan: bizonyos tranzakciókat, az adatok betöltése, a feladatok sokáig futnak az SQL-utasításokat vagy hibás lekérdezések teljesítményét (például a HANA kockák a BW) függő végrehajtását.

Tekintse meg a [SAP HANA-hibaelhárítás: CPU kapcsolódó okoz, és a megoldások](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) helyen részletes hibaelhárítási lépéseket.

**Operációs rendszer**

A legfontosabb ellenőrzések SAP Hana Linux egyik győződjön meg arról, hogy van-e tiltva átlátszó túl nagy lapok [SAP Megjegyzés #2131662 – átlátszó túl nagy lapok (THP) SAP HANA-kiszolgálókon](https://launchpad.support.sap.com/#/notes/2131662).

- Ha átlátszó túl nagy lapok engedélyezve vannak-e a következő Linux paranccsal ellenőrizheti: **/sys/kernel/mm/transparent macskaeledel\_hugepage/engedélyezve**
- Ha _mindig_ szimpla zárójelek között, az alábbi, az azt jelenti, hogy engedélyezve vannak-e a transzparens túl nagy lapok: [mindig] madvise soha nem; Ha _soha nem_ kapcsos zárójelek között, az alábbi, az azt jelenti, hogy van-e tiltva a transzparens túl nagy lapok: mindig madvise [soha nem]

A következő Linux parancs semmi sem kell visszaadnia: **rpm - qa |} grep ulimit.** Ha úgy tűnik, _ulimit_ van telepítve, távolítsa el azonnal.

**Memória**

Azt is láthatja, hogy a SAP HANA-adatbázisból által lefoglalt memória mérete nagyobb, mint a várt. Az alábbi riasztások a magas memóriahasználatban kapcsolatos hibákat jelzik:

- Állomás fizikai memória használata (riasztás 1.)
- A névkiszolgáló (riasztás 12) memóriahasználata
- Teljes memóriahasználatát oszlop tárolási táblák (riasztás 40)
- Memóriahasználat (riasztás 43) szolgáltatások
- Memóriahasználat fő tárolási oszlop tárolási táblák (riasztás 45)
- Futásidejű memóriaképek (riasztás 46)

Tekintse meg a [SAP HANA-hibaelhárítás: memóriával kapcsolatos problémák](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) helyen részletes hibaelhárítási lépéseket.

**Hálózat**

Tekintse meg [SAP Megjegyzés #2081065 – hibaelhárítás SAP HANA hálózati](https://launchpad.support.sap.com/#/notes/2081065) , és végezze el a hibaelhárítási SAP Megjegyzés vehető fel a hálózaton.

1. Kiszolgáló és az ügyfél közötti üzenetváltások idő elemzése.
  A. Az SQL-parancsfájl futtatása [ _HANA\_hálózati\_ügyfelek_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Elemezze a fürtök csomóponton belüli kommunikációjához kommunikációt.
  A. SQL-parancsfájl futtatása [ _HANA\_hálózati\_szolgáltatások_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Linux parancs **ifconfig** (a kimeneti látható, ha a csomag veszteségeiért is megjelenhetnek).
4. Linux parancs **tcpdump parancsot**.

Használja továbbá a nyílt forráskódú [IPERF](https://iperf.fr/) eszköz (vagy hasonlót) alkalmazás valós hálózati teljesítmény mérését.

Tekintse meg a [SAP HANA-hibaelhárítás: hálózati teljesítmény és a kapcsolódási problémái vannak](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) helyen részletes hibaelhárítási lépéseket.

**Tárolás**

Végfelhasználói szempontból egy alkalmazás (vagy a rendszer egészének) nehézkesen futtatja, nem válaszol, vagy még is úgy tűnik, hogy lefagy, ha problémák i/o-teljesítményét. Az a **kötetek** lapon SAP HANA Studio, a megtekintheti, milyen kötetek minden szolgáltatás által használt, és a kapcsolódó kötetek.

![A SAP HANA Studio kötetek lapján megtekintheti, milyen kötetek minden szolgáltatás által használt, és a kapcsolódó kötetek](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Csatlakoztatott kötetek a képernyő alsó részén látható a kötetek, fájlok és az i/o-statisztikák például részleteit.

![Csatlakoztatott kötetek a képernyő alsó részén látható a kötetek, fájlok és az i/o-statisztikák például részleteit](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Tekintse meg a [SAP HANA-hibaelhárítás: i/o kapcsolódó okait és megoldásait](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) és [SAP HANA-hibaelhárítás: lemez kapcsolódó okait és megoldásait](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) helyen részletes hibaelhárítási lépéseket.

**Diagnosztikai eszközök**

Hajtsa végre egy SAP HANA állapot-ellenőrzéssel keresztül HANA\_konfigurációs\_Minichecks. Ez az eszköz, amely kell már merült fel az SAP HANA Studio riasztásként elvégzésével kritikus technikai problémák adja vissza.

Tekintse meg [SAP Megjegyzés #1969700 – SQL utasítás gyűjtemény SAP Hana](https://launchpad.support.sap.com/#/notes/1969700) és töltse le az SQL Statements.zip fájlt, hogy a megjegyzés csatolva. A .zip fájlt a helyi merevlemez-meghajtón tárolja.

Az SAP HANA Studio a a **Rendszerinformáció** lapra, kattintson a jobb gombbal a a **neve** oszlop, és válassza ki **importálási SQL-utasítások**.

![Az SAP HANA Studio, a rendszer adatok lapon kattintson a jobb gombbal a neve oszlopban, és válassza ki a importálási SQL-utasítások](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Válassza ki a helyileg tárolt SQL Statements.zip fájlt, és importálja a megfelelő SQL-utasítások egy mappa. Ezen a ponton a sok különböző diagnosztikai ellenőrzések futtatható ezen az SQL-utasítások.

Például SAP HANA rendszer replikáció sávszélesség-követelményekkel teszteléséhez kattintson a jobb gombbal a **sávszélesség** utasítás alapján **replikációs: sávszélesség** válassza ki **nyitott** SQL-konzolon.

A teljes SQL-utasítás megnyílik, lehetővé téve a bemeneti paramétereket (módosítása szakaszát) megváltozott, és akkor hajtja végre.

![A teljes SQL-utasítás megnyitása (módosítása szakaszát) megváltozott, és akkor hajtja végre a bemeneti paraméterek engedélyezése](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Az utasítások alapján a jobb gombbal kattint egy másik példa: **replikációs: áttekintés**. Válassza ki **Execute** a helyi menüben:

![Jobb gombbal kattint, a replikációs csoportban utasítások egy másik példa: áttekintése. A helyi menüből válassza ki a végrehajtás](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Ennek eredményeként a hibaelhárítási információkat:

![Ennek eredményeképpen az adatokat, amelyek segítenek a hibaelhárításban](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Tegye meg ugyanezt a HANA\_konfigurációs\_Minichecks és az összes ellenőrzés _X_ megjelöli a a _C_ (kritikus) oszlopban.

A minta kimenete:

**HANA\_konfigurációs\_MiniChecks\_Rev102.01 + 1** az általános SAP HANA ellenőrzi.

![HANA\_konfigurációs\_MiniChecks\_Rev102.01 + 1. a általános SAP HANA-ellenőrzése](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_szolgáltatások\_áttekintése** mi SAP HANA futó szolgáltatások áttekintését.

![HANA\_szolgáltatások\_megtudhatja, mi SAP HANA futó szolgáltatások – áttekintés](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_szolgáltatások\_statisztika** SAP Hana szolgáltatás információkat (Processzor, memória, stb.).

![HANA\_szolgáltatások\_SAP Hana statisztika szolgáltatás információkat ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfigurációs\_áttekintése\_Rev110 +** általános tudnivalók az SAP HANA-példányon.

![HANA\_konfigurációs\_áttekintése\_Rev110 + általános tudnivalók az SAP HANA-példányon](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfigurációs\_paraméterek\_Rev70 +** SAP HANA-paraméterekhez kereséséhez.

![HANA\_konfigurációs\_paraméterek\_Rev70 + SAP HANA-paraméterekhez ellenőrzése](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

