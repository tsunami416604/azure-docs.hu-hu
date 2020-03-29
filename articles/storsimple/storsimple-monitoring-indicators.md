---
title: StorSimple monitoringmutatók | Microsoft dokumentumok
description: A StorSimple eszköz állapotának ellenőrzésére használt fénykibocsátó diódákat (LED-eket) és hangjelzéseket ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630608"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Az eszköz kezeléséhez storSimple figyelési jelzők használata


## <a name="overview"></a>Áttekintés
A StorSimple eszköz fénykibocsátó diódákat (LED-eket) és riasztásokat tartalmaz, amelyek segítségével figyelheti a modulokat és a StorSimple eszköz általános állapotát. A figyelési mutatók megtalálhatók az eszköz elsődleges házának és az EBOD ház hardverösszetevőin. A monitorozási kijelzők lehetnek LED-ek vagy hallható riasztások.

A modul állapotát három LED-állapot jelzi: zöld, zöldvagy piros borostyánsárga vagy piros-borostyánsárga.  

* A zöld LED-ek egészséges működési állapotot képviselnek.  
* A zöldtől a pirosborossárgáig villogó LED-ek olyan nem kritikus körülmények jelenlétét jelzik, amelyek felhasználói beavatkozást igényelhetnek.  
* A piros-borostyánsárga LED-ek azt jelzik, hogy kritikus hiba van a modulban.  

A cikk további részében a különböző figyelési jelzőLED-ek, azok helye a StorSimple eszközön, az eszköz állapota a LED-állapotok alapján, és a kapcsolódó hallható riasztások.

## <a name="front-panel-indicator-leds"></a>Az előlap jelzőLED-jei
Az előlap, más néven a *műveleti panel* vagy *ops panel*, megjeleníti az összes modul összesített állapotát a rendszerben. Az előlap megegyezik a StorSimple elsődleges és az EBOD ház, és az alábbiakban látható.  

   ![Eszköz előlapján][1]

Az előlap a következő mutatókat tartalmazza:  

1. Elnémítás gomb
2. Teljesítményjelző LED (zöld/piros-sárga)
3. Modulhiba jelző LED (ON red-amber/OFF)
4. Logikai hibajelző LED (ON red-amber/OFF
5. Egységazonosító megjelenítése  

A fő különbség az előlapi LED-ek között az eszköz és az EBOD ház a **rendszer egység azonosító szám** látható a LED kijelzőn. Az eszközön megjelenő alapértelmezett egységazonosító **00**, míg az EBOD házon megjelenő alapértelmezett egységazonosító **01**. Ez lehetővé teszi, hogy gyorsan különbséget az eszköz és az EBOD ház, amikor az eszköz be van kapcsolva. Ha az eszköz ki van kapcsolva, használja az [új eszköz bekapcsolása](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) című részben megadott információkat az eszköz és az EBOD ház megkülönböztetéséhez.  

## <a name="front-panel-led-status"></a>Az előlap LED állapota
Az alábbi táblázat segítségével azonosíthatja az eszköz vagy az EBOD ház előlapján található LED-ek által jelzett állapotot.  

| A rendszer teljesítménye | Modulhiba | Logikai hiba | Riasztás | status |
| --- | --- | --- | --- | --- |
| Vörös borostyán |KI |KI |N/A |A hálózati áram elveszett, működő tartalék teljesítmény, vagy Hálózati áram be van kapcsolva, és a vezérlő modulok at eltávolították. |
| Zöld |ON |ON |N/A |Ops panel bekapcsolása (5s) teszt állapota |
| Zöld |KI |KI |N/A |Kapcsolja be, minden funkció jó |
| Zöld |ON |N/A |PCM hiba LED-ek, ventilátor hiba LED-ek |Bármilyen PCM-hiba, ventilátorhiba, hőmérséklet felett vagy alatt |
| Zöld |ON |N/A |I/O modul LED-ek |Bármilyen vezérlőmodul-hiba |
| Zöld |ON |N/A |N/A |Ház logikai hibája |
| Zöld |Flash |N/A |Modul állapot LED vezérlő modul. PCM hiba LED-ek, ventilátor hiba LED-ek |Ismeretlen vezérlőmodul típusa telepítve, I2C busz hiba, vezérlő modul létfontosságú termékadatok (VPD) konfigurációs hiba |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Energiahűtő modul (PCM) jelző LED-ek
Az energiahűtő modul (PCM) jelző LED-jei az elsődleges ház vagy az EBOD ház hátulján találhatók minden PCM modulon. Ez a témakör ismerteti, hogyan használhatja a következő LED-ek a StorSimple-eszköz állapotának figyeléséhez.  

* PCM LED-ek az elsődleges házhoz
* PCM LED-ek az EBOD házhoz

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LED-ek az elsődleges házhoz
A StorSimple eszköz 764 W-os PCM modullal rendelkezik, amely további akkumulátorral rendelkezik. A következő ábrán a készülék LED-panelje látható.  

   ![PCM LED-ek az elsődleges burkolaton][2]

LED jelmagyarázat:

1. Hálózati áramkimaradás
2. Ventilátor hiba
3. Akkumulátor hiba
4. PCM – rendben
5. Tartományvezérlő hibája
6. Az akkumulátor jó  

A PCM állapota a LED panelen látható. A készülék PCM LED panel hat LED-ek. Ezek közül négy LED a tápegység és a ventilátor állapotát jeleníti meg. A fennmaradó két LED jelzi a tartalék akkumulátormodul állapotát a PCM-ben. Az alábbi táblázatok segítségével meghatározhatja a PCM állapotát.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM jelző LED-ek a tápegységhez és a ventilátorhoz
| status | PCM OK (zöld) | A C sikertelen (sárga) | Ventilátor nem (borostyán) | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs hálózati áram (a burkolathoz) |KI |KI |KI |KI |
| Nincs hálózati áram (csak ez a PCM) |KI |ON |KI |ON |
| AC jelen PCM ON - OK |ON |KI |KI |KI |
| PCM nem (ventilátor nem) |KI |KI |ON |N/A |
| PCM hiba (erősítő, túlfeszültség, áram felett) |KI |ON |ON |ON |
| PCM (a tűréshatáron kívüli ventilátor) |ON |KI |KI |ON |
| Készenléti üzemmód |Villogó |KI |KI |KI |
| PCM firmware letöltése |KI |Villogó |Villogó |Villogó |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>A TARTALÉK akkumulátor PCM-jelző LED-jei
| status | Akkumulátor jó (zöld) | Akkumulátor hiba (sárga) |
| --- | --- | --- |
| Az akkumulátor nincs jelen |KI |KI |
| Akkumulátor jelen és feltöltve |ON |KI |
| Akkumulátor töltése vagy karbantartási kisütése |Villogó |KI |
| Akkumulátor "puha" hiba (hasznosítható) |KI |Villogó |
| Akkumulátor "kemény" hiba (nem hasznosítható) |KI |ON |
| Akkumulátor lefegyverezve |Villogó |KI |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LED-ek az EBOD házhoz
Az EBOD ház 580 W-os PCM-el rendelkezik, és nincs további akkumulátor. Az EBOD ház PCM panelje csak a tápegységek és a ventilátor kijelző LED-jeit használja. A következő ábrán ezek a LED-ek láthatók.

   ![PCM LED-ek az EBOD házon][3] 

Az alábbi táblázat segítségével meghatározhatja a PCM állapotát.  

| status | PCM OK (zöld) | A C sikertelen (sárga) | Ventilátor nem (borostyán) | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs hálózati áram (a burkolathoz) |KI |KI |KI |KI |
| Nincs hálózati áram (csak ez a PCM) |KI |ON |KI |ON |
| AC jelen PCM ON - OK |ON |KI |KI |KI |
| PCM nem (ventilátor nem) |KI |KI |ON |X |
| PCM hiba (erősítő, túlfeszültség, áramerősség |KI |ON |ON |ON |
| PCM (a tűréshatáron kívüli ventilátor) |ON |KI |KI |ON |
| Készenléti modell |Villogó |KI |KI |KI |
| PCM firmware letöltése |KI |Villogó |Villogó |Villogó |

## <a name="controller-module-indicator-leds"></a>Vezérlőmodul jelző LED-ek
A StorSimple eszköz LED-eket tartalmaz az elsődleges vezérlő és az EBOD vezérlő modulokhoz.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Led-ek figyelése az elsődleges vezérlőhöz
A következő ábra segít azonosítani a LED-ek az elsődleges vezérlő. (Az összes összetevő fel van sorolva, hogy segítse a tájékozódás.)  

   ![Monitorozott LED-ek - elsődleges vezérlő][4]

Az alábbi táblázat segítségével megállapíthatja, hogy a vezérlőmodul megfelelően működik-e.  

### <a name="controller-indicator-leds"></a>Vezérlőjelző LED-ek
| Led | Leírás |
| --- | --- |
| LED (kék) |Azt jelzi, hogy a modul azonosítása folyamatban van. Ha a kék LED villog egy futó vezérlőn, akkor a vezérlő az aktív vezérlő, a másik pedig a készenléti vezérlő. További információt [az Aktív vezérlő azonosítása az eszközön](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)című témakörben talál. |
| Hiba LED (sárga) |A vezérlő hibáját jelzi. |
| OK LED (zöld) |Az állandó zöld azt jelzi, hogy a vezérlő rendben van. A zöld villogás a vezérlő VPD konfigurációs hibáját jelzi. |
| SAS tevékenység LED-ek (zöld) |Az állandó zöld azt jelzi, hogy a kapcsolat nem aktuális tevékenység. A zölden villogó zöld azt jelzi, hogy a kapcsolat folyamatos tevékenységet folytat. |
| Ethernet állapotJELZŐ LED-ek |A jobb oldal a kapcsolat/hálózati tevékenységet jelzi: (folyamatos zöld) kapcsolat aktív, (villogó zöld) hálózati tevékenység. A bal oldal a hálózati sebességet jelzi: (sárga) 1000 Mb/s, (zöld) 100 Mb/s és (OFF) 10 Mb/s. Az összetevő modelltől függően ez a jelzőfény akkor is villoghat, ha a hálózati adapter nincs engedélyezve. |
| POST LED-ek |A rendszerindítás folyamatát jelzi, amikor a vezérlő be van kapcsolva. Ha a StorSimple eszköz nem indul el, ez a LED segít a Microsoft-támogatásnak azonosítani a rendszerindítási folyamat azon pontját, ahol a hiba történt. |

> [!IMPORTANT]
> Ha a hiba LED világít, akkor probléma van a vezérlőmodullal, amely a vezérlő újraindításával megoldható. Ha a kontroller újraindítása nem oldja meg a problémát, forduljon a Microsoft támogatási szolgálatához.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>LED-ek figyelése az EBOD (EBOD ház)
A 6 Gb/s SAS EBOD-vezérlő mindegyike rendelkezik OLYAN LED-ekkel, amelyek az alábbi ábrán látható állapotukat jelzik.  

  ![LED-ek figyelése - EBOD ház][5]

Az alábbi táblázat segítségével megállapíthatja, hogy az EBOD vezérlőmodul megfelelően működik-e.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD vezérlőmodul jelző LED-ek
| status | I/O modul OK (zöld) | I/O modulhiba (sárga) | Állomásport-tevékenység (zöld) |
| --- | --- | --- | --- |
| Vezérlőmodul – OK |ON |KI |- |
| Vezérlőmodul hibája |KI |ON |- |
| Nincs külső állomásport-kapcsolat |- |- |KI |
| Külső állomásport-kapcsolat – nincs tevékenység |- |- |ON |
| Külső állomásport-kapcsolat - tevékenység |- |- |Villogó |
| Vezérlőmodul metaadat-hibája |Villogó |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lemezmeghajtó-jelző LED-ek az elsődleges házhoz és az EBOD házhoz
A StorSimple-eszköz lemezmeghajtók találhatók mind az elsődleges ház és az EBOD ház. Minden lemezmeghajtó az ebben a szakaszban leírtak szerint figyelőjelző LED-eket tartalmaz. 

A lemezmeghajtók esetében a meghajtó állapotát egy zöld LED és egy piros sárga LED jelzi, amely minden meghajtóhordozó modul elejére van szerelve. A következő ábrán ezek a LED-ek láthatók.

  ![Lemezmeghajtó LED-ek][6]

Az alábbi táblázat segítségével meghatározhatja az egyes lemezmeghajtók állapotát, ami viszont hatással van az előlap LED-jének általános állapotára.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Az EBOD ház lemezmeghajtó-jelző LED-jei
| status | Tevékenység OK LED (zöld) | Hiba LED (piros-sárga) | Társított ops panel LED |
| --- | --- | --- | --- |
| Nincs telepítve meghajtó |KI |KI |None |
| Telepített és működő meghajtó |Villogó be/ ki tevékenységgel |X |None |
| SCSI-házszolgáltatások (SES) eszközidentitás-készlete |ON |Villogó 1 másodperc be /1 másodperc ki |None |
| SES eszközhibabit beállítása |ON |ON |Logikai hiba (piros) |
| Áramvezérlési áramkör hibája |KI |ON |Modulhiba (piros) |

## <a name="audible-alarms"></a>Hallható riasztások
A StorSimple-eszköz hallható riasztásokat tartalmaz az elsődleges házhoz és az EBOD házhoz társítva. Mindkét ház előlapján (más néven műveleti panelen) hallható riasztás található. A hangjelzés azt jelzi, ha hiba helyzet áll fenn. A következő feltételek kapcsolják be a riasztást:  

* Ventilátor hiba vagy hiba
* Feszültség a hatótávolságon kívül
* Hőmérsékleten felül vagy hőmérsékleten
* Termikus túlcsordulás
* Rendszerhiba
* Logikai hiba
* Áramellátási hiba
* Az energiahűtő modul (PCM) eltávolítása  

Az alábbi táblázat a különböző riasztási állapotokat ismerteti.  

### <a name="alarm-states"></a>Riasztási állapotok
| Riasztás állapota | Műveletek | Művelet az elnémító gomb lenyomásával |
| --- | --- | --- |
| S0 |Normál mód: néma |Hangjelzés kétszer |
| S1 |Hiba üzemmód: 1 másodperc be/1 másodperc ki |Átmenet S2 vagy S3 -ra (lásd a megjegyzéseket) |
| S2 |Emlékeztető mód: szakaszos hangjelzés |None |
| S3 |Némított üzemmód: néma |None |
| S4 |Kritikus hibamód: folyamatos riasztás |Nem érhető el: a némítás nem aktív |

> [!NOTE]
> * S1 riasztási állapotban, ha 2 percen belül nem nyomja meg a némítást, az állapot automatikusan S2 vagy S3 állapotba vált.  
> * A riasztás szerint az S1 és S4 között a hibaállapot törlése után visszatér az S0-ba.  
> * Az S4 kritikus hibaállapot bármely más állapotból megadható.  


A hangjelzést a műveleti panel némításgombjának megnyomásával némíthatja el. Ha a némítókapcsolót nem kézi működtetése után két perc múlva történik, automatikus némítás történik. Amikor a riasztás el van némítva, a hang rövid, szakaszos hangjelzéssel tovább fog hangzani, jelezve, hogy a probléma továbbra is fennáll. A riasztás néma lesz, ha minden probléma törlődik.

Az alábbi táblázat a különböző riasztási feltételeket ismerteti.

### <a name="alarm-conditions"></a>Riasztási körülmények
| status | Severity | Riasztás | Ops panel LED |
| --- | --- | --- | --- |
| PCM riasztás – az egyenáram elvesztése egyetlen PCM-től |Hiba – nincs redundanciaveszteség |S1 |Modulhiba |
| PCM riasztás – az egyenáram elvesztése egyetlen PCM-től |Hiba – a redundancia elvesztése |S1 |Modulhiba |
| A PCM-ventilátor meghibásodik |Hiba – a redundancia elvesztése |S1 |Modulhiba |
| Az SBB modul PCM hibát észlelt |Hiba |S1 |Modulhiba |
| PCM eltávolítva |Konfigurációs hiba |None |Modulhiba |
| Ház konfigurációs hibája |Hiba – kritikus |S1 |Modulhiba |
| Alacsony figyelmeztető hőmérséklet riasztás |Figyelmeztetés |S1 |Modulhiba |
| Magas figyelmeztető hőmérséklet riasztás |Figyelmeztetés |S1 |Modulhiba |
| Túlmelegedés riasztás |Hiba – kritikus |S1 |Modulhiba |
| I2C busz hiba |Hiba – a redundancia elvesztése |S1 |Modulhiba |
| Ops panel kommunikációs hiba (I2C) |Hiba – kritikus |S1 |Modulhiba |
| Vezérlő hiba |Hiba – kritikus |S1 |Modulhiba |
| SBB interfész modul hibája |Hiba – kritikus |S1 |Modulhiba |
| SBB interfész modul hiba – Nem maradt működő modul |Hiba – kritikus |S4 |Modulhiba |
| SBB interfész modul eltávolítva |Figyelmeztetés |None |Modulhiba |
| Meghajtó teljesítményvezérlési hibája |Figyelmeztetés – nincs áramveszteség |S1 |Modulhiba |
| Meghajtó teljesítményvezérlési hibája |Hiba – kritikus; a meghajtó teljesítményének elvesztése |S1 |Modulhiba |
| Meghajtó eltávolítva |Figyelmeztetés |None |Modulhiba |
| Nem áll rendelkezésre elegendő energia |Figyelmeztetés |Nincs |Modulhiba |

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevőkről és állapotról.](storsimple-8000-monitor-hardware-status.md)

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


