---
title: Figyelés és hibaelhárítás hana oldalról az SAP HANA-n az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Figyelés és hibaelhárítás hana oldalról az SAP HANA egy Azure-beli (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617071"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA-oldali monitorozás és hibaelhárítás

Az Azure-beli SAP HANA -val kapcsolatos problémák hatékony elemzéséhez hasznos leszűkíteni a probléma kiváltó okát. Az SAP nagy mennyiségű dokumentációt tett közzé, amelyek segítenek Önnek.

Az SAP HANA teljesítményével kapcsolatos vonatkozó gyakori kérdések a következő SAP-megjegyzésekben találhatók:

- [SAP Note #2222200 – gyakori kérdések: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Megjegyzés #2100040 – gyakori kérdések: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – gyakori kérdések: SAP HANA memória](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – gyakori kérdések: SAP HANA teljesítményoptimalizálás](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – gyakori kérdések: SAP HANA I/O elemzés](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – gyakori kérdések: AZ SAP HANA szolgáltatás újraindítása és összeomlása](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA riasztások

Első lépésként ellenőrizze az aktuális SAP HANA riasztási naplók. Az SAP HANA Studio,go to **Administration Console: Alerts: Show: all alerts**. Ez a lap a beállított minimális és maximális küszöbértékeken kívül esik adott értékekre (szabad fizikai memória, CPU-kihasználtság stb.) vonatkozó összes SAP HANA-riasztást jeleníti meg. Alapértelmezés szerint az ellenőrzések 15 percenként automatikusan frissülnek.

![Az SAP HANA Studio-ban nyissa meg a Felügyeleti konzol: Riasztások: Megjelenítés: az összes riasztást](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

A nem megfelelő küszöbérték-beállítás miatt kiváltott riasztás esetén a megoldás az alapértelmezett értékre vagy egy ésszerűbb küszöbértékre való visszaállítás.

![Visszaállítás az alapértelmezett értékre vagy egy ésszerűbb küszöbértékre](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

A következő riasztások jelezhetik a CPU-erőforrások problémáit:

- Állomás CPU-használat (5. riasztás)
- Legutóbbi mentésipont-művelet (28. riasztás)
- A mentési pont időtartama (54. riasztás)

Az SAP HANA-adatbázis magas processzorfogyasztását tapasztalhatja az alábbi eszközök egyikén:

- Az 5-ös riasztás (host CPU-használat) a jelenlegi vagy a korábbi CPU-használatra
- A megjelenített CPU-használat az áttekintő képernyőn

![Cpu-használat jelenik meg az áttekintő képernyőn](./media/troubleshooting-monitoring/image3-cpu-usage.png)

A Terhelés grafikon magas processzorfogyasztást vagy a múltban magas fogyasztást mutathat:

![A Terhelés grafikon magas cpu-fogyasztást vagy a múltban magas fogyasztást mutathat](./media/troubleshooting-monitoring/image4-load-graph.png)

A magas CPU-kihasználtság miatt kiváltott riasztást több ok is okozhatja, többek között, de nem kizárólagosan: bizonyos tranzakciók végrehajtása, adatbetöltés, nem válaszoló feladatok, hosszú ideig futó SQL-utasítások és rossz lekérdezési teljesítmény (például a BW a HANA kocka).

Tekintse meg az [SAP HANA hibaelhárítás: CPU kapcsolatos okok és megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="operating-system"></a>Operációs rendszer

Az SAP HANA egyik legfontosabb ellenőrzése Linuxon, hogy megbizonyosodjon arról, hogy az átlátszó hatalmas oldalak le vannak tiltva, lásd [az SAP Note #2131662 - Transparent Huge Pages (THP) az SAP HANA szervereken.](https://launchpad.support.sap.com/#/notes/2131662)

- Ellenőrizheti, hogy az Átlátszó Hatalmas oldalak engedélyezve vannak-e a következő Linux paranccsal: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Ha _mindig_ zárójelben van, az azt jelenti, hogy az Átlátszó hatalmas oldalak engedélyezve vannak: [mindig] madvise soha; ha _soha nem_ van mellékelve zárójelben, mint az alábbiakban, ez azt jelenti, hogy az Átlátszó Hatalmas oldalak le vannak tiltva: mindig madvise [soha]

A következő Linux parancs nem ad vissza semmit: **rpm -qa | grep ulimit.** Ha úgy tűnik, _ulimit_ telepítve van, távolítsa el azonnal.

## <a name="memory"></a>Memory (Memória)

Előfordulhat, hogy az SAP HANA-adatbázis által lefoglalt memória mennyisége a vártnál magasabb. A következő riasztások a magas memóriahasználattal kapcsolatos problémákat jelzik:

- Gazdafizikai memória használata (1. riasztás)
- Névkiszolgáló memóriahasználata (12. riasztás)
- Oszloptároló-táblák teljes memóriahasználata (40. riasztás)
- Szolgáltatások memóriahasználata (43. riasztás)
- Az Oszloptároló-táblák fő tárolójának memóriahasználata (45. riasztás)
- Futásidejű memóriaképfájlok (46. riasztás)

Tekintse meg az [SAP HANA hibaelhárítás: Memóriaproblémák](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="network"></a>Network (Hálózat)

Tekintse meg az [SAP Note #2081065 – Sap HANA hálózat hibaelhárítása,](https://launchpad.support.sap.com/#/notes/2081065) és hajtsa végre a hálózati hibaelhárítási lépéseket ebben az SAP Megjegyzésben.

1. A kiszolgáló és az ügyfél közötti oda-vissza idő elemzése.
  A. Futtassa a [_HANA\_\_hálózati ügyfelek SQL-parancsfájlt._](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Internode kommunikáció elemzése.
  A. Futtassa a [_HANA\_\_Network Services SQL-parancsfájlt._](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Futtassa a Linux parancsot **ifconfig** (a kimenet azt mutatja, ha bármilyen csomagveszteség fordul elő).
4. Futtassa a Linux **tcpdump parancsot.**

Emellett a nyílt forráskódú [IPERF](https://iperf.fr/) eszközzel (vagy hasonlóval) mérheti a valós alkalmazáshálózati teljesítményt.

Tekintse meg az [SAP HANA hibaelhárítás: Hálózati teljesítmény és kapcsolódási problémák](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="storage"></a>Storage

A végfelhasználó szempontjából egy alkalmazás (vagy a rendszer egésze) lassan fut, nem válaszol, vagy akár úgy tűnik, hogy nem válaszol, ha problémák merülnek fel az I/O teljesítményével kapcsolatban. Az SAP HANA Studio **Kötetek** lapján láthatja a csatolt köteteket, és az egyes szolgáltatások által használt köteteket.

![Az SAP HANA Studio Kötetek lapján láthatja a csatolt köteteket, és az egyes szolgáltatások által használt köteteket](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

A képernyő alsó részén csatolt kötetek a kötetek részleteit, például a fájlokat és az I/O-statisztikákat láthatja.

![A képernyő alsó részén csatolt kötetek a kötetek részleteit, például a fájlokat és az I/O-statisztikákat láthatja.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Tekintse meg az [SAP HANA hibaelhárítás: I/O kapcsolódó kiváltó okok és megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) és az SAP [HANA hibaelhárítás: Lemezzel kapcsolatos kiváltó okok és megoldások](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) hely részletes hibaelhárítási lépéseket.

## <a name="diagnostic-tools"></a>Diagnosztikai eszközök

SAP HANA állapot-ellenőrzés\_hana\_konfigurációs miniellenőrzések en keresztül. Ez az eszköz olyan potenciálisan kritikus technikai problémákat ad vissza, amelyeket már riasztásként kellett volna felvetni az SAP HANA Studio-ban.

Tekintse meg az [SAP Note #1969700 – SQL utasítás gyűjtemény az SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) és töltse le az SQL Statements.zip fájlt csatolt, hogy a megjegyzés. Tárolja ezt a .zip fájlt a helyi merevlemezen.

Az SAP HANA Studio **Rendszerinformáció** lapján kattintson a jobb gombbal a **Név** oszlopra, és válassza az SQL **utasítások importálása parancsot.**

![Az SAP HANA Studio Rendszerinformáció lapján kattintson a jobb gombbal a Név oszlopra, és válassza az SQL-utasítások importálása parancsot.](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Jelölje ki a helyileg tárolt SQL Statements.zip fájlt, és a program importálja a megfelelő SQL-utasításokat tartalmazó mappát. Ezen a ponton a számos különböző diagnosztikai ellenőrzések futtathatók ezekkel az SQL utasításokkal.

Például az SAP HANA rendszer replikációs sávszélesség-követelményeinek teszteléséhez kattintson a jobb gombbal a **Sávszélesség** utasításra a **Replikáció: Sávszélesség** csoportban, és válassza a **Megnyitás** az SQL Konzolban parancsot.

A teljes SQL utasítás megnyílik, amely lehetővé teszi a bemeneti paraméterek (módosítási szakasz) módosítását, majd végrehajtását.

![Megnyílik a teljes SQL utasítás, amely lehetővé teszi a bemeneti paraméterek (módosítási szakasz) módosítását, majd végrehajtását](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Egy másik példa a jobb gombbal a **replikáció: Áttekintés**. Válassza a helyi menü **Végrehajtás** parancsát:

![Egy másik példa a jobb gombbal a replikáció: áttekintés. Válassza a Helyi menü Végrehajtás parancsát](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Ez olyan információkat eredményez, amelyek segítenek a hibaelhárításban:

![Ez olyan információkat eredményez, amelyek segítenek a hibaelhárításban](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Tegye ugyanezt a\_\_HANA konfigurációs miniellenőrzésekesetében, és ellenőrizze a _C_ (Kritikus) oszlopban lévő _X_ jeleket.

Mintakimenetek:

**HANA\_\_konfigurációs\_miniellenőrzések Rev102.01+1** általános SAP HANA-ellenőrzések.

![HANA\_\_konfigurációs\_miniellenőrzések Rev102.01+1 általános SAP HANA ellenőrzések](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**\_\_HANA-szolgáltatások áttekintése** az SAP HANA-szolgáltatások jelenlegi futtatásának áttekintéséhez.

![HANA-szolgáltatások\_\_– áttekintés az SAP HANA-szolgáltatások jelenlegi működéséről](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_\_szolgáltatások statisztikája** az SAP HANA szolgáltatás információihoz (CPU, memória stb.).

![HANA\_\_szolgáltatások statisztikája az SAP HANA szolgáltatás adataihoz](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_\_konfigurációáttekintése\_Rev110+** az SAP HANA-példány általános információiért.

![HANA\_\_konfigurációáttekintése\_Rev110+ az SAP HANA-példányáltalános információihoz](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_\_konfigurációs\_paraméterek Rev70+** az SAP HANA-paraméterek ellenőrzéséhez.

![Hana\_\_konfigurációs\_paraméterek Rev70+ az SAP HANA paramétereinek ellenőrzéséhez](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**További lépések**

- A [SUSE-ban beállított magas rendelkezésre állás a STONITH használatával jelenik meg.](ha-setup-with-stonith.md)