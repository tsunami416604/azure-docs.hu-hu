---
title: A StorSimple ellenőrzési mutatók |} Microsoft Docs
description: Fénykibocsátó dióda (LED) és a StorSimple eszköz állapotának figyelésére használható hallható riasztások mutatja be.
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
ms.openlocfilehash: 9ae0caec211dc1199f0abd2ce9bc0c7ad11c02ec
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
ms.locfileid: "24030357"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>A StorSimple ellenőrzési mutatók segítségével kezelhesse az eszközét


## <a name="overview"></a>Áttekintés
A StorSimple eszköz fénykibocsátó dióda (LED) tartalmaz, és riasztókat használható a modulok és a StorSimple eszköz az általános állapotának figyelésére. A figyelési mutatók található meg az eszköz elsődleges ház és a EBOD ház hardverösszetevők listáját. A figyelési mutatók LED vagy a hallható riasztások lehet.

Három LED állapota azt jelzi, a modul állapota van: zöld, piros-sárga vagy piros-sárga zöld.  

* Zöld LED működési állapotát képviselik.  
* Villogó zöld piros-sárga LED jelentik a nem kritikus feltételek a felhasználói beavatkozást igénylő jelenlétét.  
* Piros-sárga LED jelzi, hogy nincs-e a modulban található kritikus hibát.  

Ez a cikk fennmaradó ismerteti a különböző megfigyelési kijelző LED, a helyek, a StorSimple eszközön, az eszköz állapotát a LED állapotok, és minden kapcsolódó hallható riasztások alapján.

## <a name="front-panel-indicator-leds"></a>Előlapi panel kijelző LED
Az első panel, más néven a *műveletek panel* vagy *ops panel*, a rendszer a modulokat összesített állapotát jeleníti meg. Az első panelen a StorSimple elsődleges és a EBOD ház azonos, és az alábbi ábra szemlélteti.  

   ![Eszköz előlapi panel][1]

Az első panelen a következő mutatók tartalmazza:  

1. Némító gomb
2. Energiagazdálkodási kijelző LED-jét (zöld/piros-sárga)
3. A modul hibát jelző vezetett (a piros-sárga-és kikapcsolása)
4. Tartalék logikai mutató vezetett (a piros sárga KIKAPCSOLÁS
5. Egység azonosító megjelenítése  

A fő különbség a előlapja LED az eszközön, és megfelelnek a EBOD ház között a **rendszer egység azonosítószámot** az LED képernyőjén látható. Az alapértelmezett egység jelenik meg az eszköz azonosítója **00**, mivel az alapértelmezett egység Azonosítóját jelenik meg a EBOD ház **01**. Ez lehetővé teszi, hogy gyorsan különböztetheti meg az eszköz és a EBOD ház az eszköz bekapcsolása. Ha az eszköz ki van kapcsolva, olvassa el a megadott [kapcsolja be az új eszköz](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) EBOD ki az eszköz megkülönböztetéséhez.  

## <a name="front-panel-led-status"></a>Előlapi panel LED állapota
Az alábbi táblázat segítségével azonosíthatja a előlapján az eszköz vagy a EBOD ház LED által jelzett állapotát.  

| Rendszer energiagazdálkodási | A modul hiba | Logikai hiba | Riasztás | status |
| --- | --- | --- | --- | --- |
| Piros-sárga |KIKAPCSOLVA |KIKAPCSOLVA |N/A |A biztonsági mentési power vagy AC power működő elveszett, AC energia- és a modulok távolítottak-e a tartományvezérlő. |
| Zöld |ON |ON |N/A |(5s) panelen bekapcsolás OPS teszt állapota |
| Zöld |KIKAPCSOLVA |KIKAPCSOLVA |N/A |Bekapcsolás jó összes funkciók |
| Zöld |ON |N/A |PCM tartalék LED, ventilátor tartalék LED |Bármely PCM hibája, ventilátor hibája, vagy hőmérsékletű keresztül |
| Zöld |ON |N/A |I/o-modul LED |Bármely tartományvezérlő modul hiba |
| Zöld |ON |N/A |N/A |Ház logika hiba |
| Zöld |Flash |N/A |Modul állapota vezérlő modul vezetett. PCM tartalék LED, ventilátor tartalék LED |Ismeretlen vezérlő modultípus telepítve, I2C bus sikertelen, a tartományvezérlő modul létfontosságú termék data (VPD) konfigurációs hiba |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Energiagazdálkodási modul (PCM) mutató LED hűtési
Energiagazdálkodási hűtési modul (PCM) mutató LED található az elsődleges vagy a EBOD ház hátulján PCM modulokhoz. Ez a témakör ismerteti, hogyan használható a következő LED a StorSimple eszköz állapotának figyelése.  

* Az elsődleges ház PCM LED
* Az a EBOD ház PCM LED

## <a name="pcm-leds-for-the-primary-enclosure"></a>Az elsődleges ház PCM LED
A StorSimple eszköz egy 764W PCM modul egy további akkumulátor rendelkezik. A következő ábrán az eszköz a LED panelen.  

   ![A elsődleges tárolóeszközön PCM LED][2]

Jelmagyarázat LED:

1. AC áramszünet esetén
2. Hiba ventilátor
3. Akkumulátor hiba
4. PCM OK
5. DC hiba
6. Jó akkumulátor  

A PCM állapotának fel van tüntetve a LED panelen. Az eszköz PCM LED panel hat LED rendelkezik. Négy ezek LED a tápegység és a ventilátor állapotüzenet jelenik meg. A többi két LED a biztonsági mentési akkumulátor modulja a PCM állapotát jelzi. Az alábbi táblázatok használatával a PCM állapotának megállapítása.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM kijelző LED tápegység és ventilátor
| status | PCM OK (zöld) | AC sikertelen (sárga) | Ventilátor sikertelen (sárga) | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs AC power (a ház) |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| Nincs AC power (csak ez a PCM) |KIKAPCSOLVA |ON |KIKAPCSOLVA |ON |
| AC jelen PCM ON - OK |ON |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| PCM sikertelen (ventilátor sikertelen) |KIKAPCSOLVA |KIKAPCSOLVA |ON |N/A |
| PCM hiba (keresztül amp feszültség keresztül aktuális keresztül) |KIKAPCSOLVA |ON |ON |ON |
| PCM (ventilátor tolerancia kívül) |ON |KIKAPCSOLVA |KIKAPCSOLVA |ON |
| Készenléti kiszolgálói mód |Villogó |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| PCM belső vezérlőprogram letöltése |KIKAPCSOLVA |Villogó |Villogó |Villogó |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>A biztonsági mentési akkumulátor LED PCM mutatója
| status | Jó akkumulátor (zöld) | Akkumulátor hiba (sárga) |
| --- | --- | --- |
| Nem található akkumulátor |KIKAPCSOLVA |KIKAPCSOLVA |
| Jelen és megterhelni akkumulátor |ON |KIKAPCSOLVA |
| Akkumulátor díjszabási vagy karbantartás lezárása |Villogó |KIKAPCSOLVA |
| Akkumulátor "soft" hiba (helyreállítható) |KIKAPCSOLVA |Villogó |
| Akkumulátor "rögzített" hiba (helyreállíthatatlan) |KIKAPCSOLVA |ON |
| Ártalmatlanított akkumulátor |Villogó |KIKAPCSOLVA |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Az a EBOD ház PCM LED
A EBOD ház egy 580W PCM és további telep nem rendelkezik. A PCM panelje a EBOD ház rendelkezik kijelző LED csak a a áramforrások és a ventilátor. Az alábbi ábrán a LED.

   ![A EBOD tárolóeszközön PCM LED][3] 

Az alábbi táblázat segítségével határozza meg a PCM állapotát.  

| status | PCM OK (zöld) | AC sikertelen (sárga) | Ventilátor sikertelen (sárga) | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs AC power (a ház) |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| Nincs AC power (csak ez a PCM) |KIKAPCSOLVA |ON |KIKAPCSOLVA |ON |
| AC jelen PCM ON – OK |ON |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| PCM sikertelen (ventilátor sikertelen) |KIKAPCSOLVA |KIKAPCSOLVA |ON |X |
| (Keresztül amp keresztül feszültség keresztül aktuális PCM hiba |KIKAPCSOLVA |ON |ON |ON |
| PCM (ventilátor tolerancia kívül) |ON |KIKAPCSOLVA |KIKAPCSOLVA |ON |
| Készenléti modell |Villogó |KIKAPCSOLVA |KIKAPCSOLVA |KIKAPCSOLVA |
| PCM belső vezérlőprogram letöltése |KIKAPCSOLVA |Villogó |Villogó |Villogó |

## <a name="controller-module-indicator-leds"></a>A tartományvezérlő modul kijelző LED
A StorSimple eszköz LED az elsődleges tartományvezérlő és a EBOD vezérlő modulokat tartalmaz.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Az elsődleges tartományvezérlő LED figyelése
Az alábbi ábrán látható könnyebb legyen azonosítani az elsődleges tartományvezérlőn LED. (A összetevőket találhatók, a tájolás segítése érdekében.)  

   ![Figyelési LED - elsődleges tartományvezérlő][4]

Az alábbi táblázat segítségével határozza meg, hogy a vezérlő modul megfelelően működik-e.  

### <a name="controller-indicator-leds"></a>A tartományvezérlő kijelző LED
| LED | Leírás |
| --- | --- |
| Azonosító LED-jét (kék) |Azt jelzi, hogy a modul az azonosított. Ha egy futó tartományvezérlő a kék LED van villogó, majd a tartományvezérlő az aktív vezérlő pedig a másik egy a készenléti vezérlő. További információkért lásd: [azonosítani az eszköz aktív vezérlőjén](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Tartalék LED-jét (sárga) |Azt jelzi, hogy a tartományvezérlő a hibát. |
| OK LED-jét (zöld) |Állandó zöld állapot azt jelzi, hogy a vezérlő OK gombra. A vezérlő VPD konfigurációs hiba zöld jelzi. |
| SAS-tevékenység LED (zöld) |Állandó zöld állapot azt jelzi, hogy nincs aktuális tevékenység kapcsolatot. Zöld azt jelzi, a kapcsolat a folyamatban lévő tevékenységek. |
| Ethernet állapot LED |Jobb oldali hivatkozást és a hálózati tevékenységet jelez: (állandó zöld) hivatkozás aktív, (zöld villogó) hálózati tevékenységet. Bal oldali jelzi a hálózati sebesség: (sárga) 1000 Mb/s (zöld) 100 Mb/s és (KIKAPCSOLT) 10 Mb/s. Az összetevő modelltől függően a ritka villogni előfordulhat, hogy akkor is, ha a hálózati illesztő nem engedélyezett. |
| POST LED |Azt jelzi, a rendszerindító előrehaladását, amikor a vezérlő be van kapcsolva. Ha a StorSimple eszköz rendszerindító nem sikerül, a LED írva az a pont, a rendszerindítási folyamat számára, ahol a hiba történt a Microsoft Support segítségével. |

> [!IMPORTANT]
> A tartalék LED bekapcsolásával van, ha probléma van a vezérlő modullal, előfordulhat, hogy a vezérlő újraindításával orvosolható. Ha a tartományvezérlő újraindítása nem oldja meg a probléma, forduljon a Microsoft Support.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>LED figyelését a EBOD (EBOD ház)
A 6 Gb/s SAS EBOD tartományvezérlők mindegyikén LED állapotát jelző, a következő ábrán látható módon.  

  ![Figyelési LED - EBOD ház][5]

Az alábbi táblázat segítségével határozza meg, hogy a vezérlő EBOD modul megfelelően működik-e.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD vezérlő modul kijelző LED
| status | I/o-modul OK (zöld) | I/o-modul hibát (sárga) | Host port tevékenység (zöld) |
| --- | --- | --- | --- |
| A tartományvezérlő modul OK |ON |KIKAPCSOLVA |- |
| A tartományvezérlő modul hiba |KIKAPCSOLVA |ON |- |
| Külső host port kapcsolat |- |- |KIKAPCSOLVA |
| Külső host port kapcsolat – nincs tevékenység |- |- |ON |
| Külső host port kapcsolat - tevékenység |- |- |Villogó |
| A tartományvezérlő metaadatainak modulhiba |Villogó |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lemezmeghajtó kijelző LED EBOD ház és elsődleges ház
A StorSimple eszköz található, az elsődleges ház- és a EBOD ház meghajtó van. Egyes meghajtókon tartalmaz figyelési kijelző LED, ebben a szakaszban leírtak szerint. 

A merevlemez-meghajtók esetében a meghajtó állapotát jelzi egy zöld LED-jét, és a piros-sárga LED csatlakoztatott meghajtó szolgáltatónként modulokhoz elején. Az alábbi ábrán a LED.

  ![Lemezmeghajtó LED][6]

Az alábbi táblázat segítségével minden meghajtó, amely viszont hatással van a teljes előlapja LED állapot állapotának megállapítása.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>A EBOD ház LED lemezmeghajtó mutatója
| status | Tevékenység OK LED (zöld) | Tartalék LED-jét (piros-sárga) | Ops panel LED társított |
| --- | --- | --- | --- |
| Nincs telepítve meghajtó |KIKAPCSOLVA |KIKAPCSOLVA |None |
| Telepített és működő meghajtó |A tevékenység be-és kikapcsolása villogó |X |None |
| SCSI ház szolgáltatások (SES) eszközt identitás beállítása |ON |A/1 második ki 1 másodperc villogó |None |
| SES eszköz tartalék bit beállítása |ON |ON |Logikai hiba (piros) |
| Áramszünet vezérlő áramkör |KIKAPCSOLVA |ON |A modul hibát (piros) |

## <a name="audible-alarms"></a>Hallható riasztások
A StorSimple eszköz mind az elsődleges ház- és a EBOD ház társított hallható riasztásokat tartalmazza. Hallható figyelmeztető mindkét ház előlapja (más néven az ops panel) található. A hallható riasztás azt jelzi, ha jelen-e a hibaállapot esetén. A következő feltételek aktiválódik a riasztás:  

* Ventilátor hiba vagy probléma
* Engedélyezett tartományon kívül esik feszültségérzékelő
* Felett vagy alatt hőmérséklet feltétel
* Hőmérséklet-túllépés
* Rendszer-hiba
* Logikai hiba
* Hálózati ellátási hiba
* A modul (PCM) hűtési teljesítménye eltávolítása  

A következő táblázat ismerteti a különböző riasztó állapotait.  

### <a name="alarm-states"></a>Riasztó állapotait
| Riasztási állapot | Műveletek | A művelet némító gombokra |
| --- | --- | --- |
| S0 |Normál mód: beavatkozás nélküli |E sípoló kétszer |
| S1 |Tartalék mód: 1 másodperc a/1 második kikapcsolása |S2 vagy S3 átmenet (lásd: a Megjegyzés) |
| S2 |Emlékeztesse mód: időszakos hangjelzés |None |
| S3 |Elfedett mód: beavatkozás nélküli |None |
| S4 |Kritikus hiba mód: folyamatos riasztás |Nem érhető el: csend nem aktív |

> [!NOTE]
> * A riasztás állapota S1 Ha a nem gombra kattint némító 2 percen belül az állapot automatikusan átkerül S2 vagy S3.  
> * Riasztó állapotait S1 S4 való visszatéréshez S0 után a hiba az állapot nincs bejelölve.  
> * Kritikus hiba állapot S4 más állapotból adható meg.  


Az ops panel a némító gomb megnyomásával a hallható riasztás is elnémíthatja. Automatikus némítása megtörténik a két perc után ha némító kapcsoló manuálisan nem működik. Ha a riasztás némított, továbbra is hang rövid időszakos hangjelzés annak jelzésére, hogy a probléma továbbra is létezik. A riasztás csendes lesz, ha az összes probléma adatútvonalai törlődtek.

A következő táblázat ismerteti a különböző riasztási feltételeket.

### <a name="alarm-conditions"></a>Riasztási feltételek
| status | Súlyosság | Riasztás | OPS LED panel |
| --- | --- | --- | --- |
| PCM riasztás – DC áramkimaradás egyetlen PCM |Hiba – a redundancia adatvesztés nélkül |S1 |A modul hiba |
| PCM riasztás – DC áramkimaradás egyetlen PCM |Hiba – a redundancia adatvesztés |S1 |A modul hiba |
| Sikertelen a PCM ventilátor |Hiba – a redundancia adatvesztés |S1 |A modul hiba |
| SBB modul PCM hiba észlelhető |Hiba |S1 |A modul hiba |
| PCM eltávolítása |Hiba történt a konfiguráció |None |A modul hiba |
| Ház konfigurációs hiba |Hiba – kritikus |S1 |A modul hiba |
| Alacsony hőmérséklet vonatkozó figyelmeztetés |Figyelmeztetés |S1 |A modul hiba |
| Magas hőmérséklet vonatkozó figyelmeztetés |Figyelmeztetés |S1 |A modul hiba |
| Hőmérséklet-riasztás keresztül |Hiba – kritikus |S1 |A modul hiba |
| I2C bus hiba |Hiba – a redundancia adatvesztés |S1 |A modul hiba |
| OPS kommunikációs hiba történt (I2C) panelen |Hiba – kritikus |S1 |A modul hiba |
| A tartományvezérlő hiba |Hiba – kritikus |S1 |A modul hiba |
| SBB felület modul hiba |Hiba – kritikus |S1 |A modul hiba |
| SBB felület modul hiba – nem működő modulok fennmaradó |Hiba – kritikus |S4 |A modul hiba |
| SBB felület modul eltávolítása |Figyelmeztetés |None |A modul hiba |
| Meghajtó hálózati vezérlő hiba |Figyelmeztetés – meghajtó power adatvesztés nélkül |S1 |A modul hiba |
| Meghajtó hálózati vezérlő hiba |Hiba – kritikus; meghajtó áramkimaradás |S1 |A modul hiba |
| Eltávolított meghajtó |Figyelmeztetés |None |A modul hiba |
| Nincs elegendő rendelkezésre álló energiagazdálkodási |Figyelmeztetés |Egyik sem |A modul hiba |

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardverösszetevők és állapot](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


