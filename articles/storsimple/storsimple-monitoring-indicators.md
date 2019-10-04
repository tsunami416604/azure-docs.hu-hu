---
title: A StorSimple-ellenőrzési mutatók |} A Microsoft Docs
description: Világító diódákat (LED-ek) és a StorSimple eszköz állapota figyelhető hallható riasztás ismerteti.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60630608"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Az eszköz felügyeletét ellenőrzési mutatók StorSimple használatával


## <a name="overview"></a>Áttekintés
A StorSimple-eszköz világító diódákat (LED-ek) is tartalmaz, és a riasztások, hogy használatával a modulok és a StorSimple-eszköz összesített állapotát figyeli. A figyelési mutatók az eszköz elsődleges ház és a EBOD ház hardverösszetevő is található. A figyelési mutatók LED-ek vagy a hallható riasztás is lehet.

Nincsenek modulok állapotát jelző LED Háromállapotú: zöld, piros-sárga vagy piros-sárga-zöld.  

* Zöld LED-ek egy megfelelő működési állapot képviseli.  
* Villogó zöld – piros-sárga LED-ek jelölik jelenlétét, nem kritikus fontosságú feltételek, amelyek felhasználói beavatkozást igényelnek.  
* Piros-sárga LED-ek jelzi, hogy a kritikus hibát a modulban található.  

Ez a cikk további része ismerteti a különböző monitorozási jelző LED-EK, a StorSimple eszközön, az eszköz állapota alapján a LED állapotok, és minden kapcsolódó hallható riasztás helyükre.

## <a name="front-panel-indicator-leds"></a>Előlapi panel jelző LED-EK
Az első panel, más néven a *műveletek panel* vagy *ops panel*, a rendszer az összes összesített állapotát jeleníti meg. Az első panelen azonos az elsődleges StorSimple és a EBOD ház, és az alábbi ábra szemlélteti.  

   ![Eszköz előlapi panel][1]

Az első panelen a következő mutatók tartalmazza:  

1. Vypnutí gomb
2. Energiagazdálkodási állapotjelző LED (zöld/piros-sárga)
3. A modul tartalék kijelző vezetett (a piros-sárga-és kikapcsolása)
4. Tartalék logikai kijelző vezetett (a piros-sárga és kikapcsolása
5. Egység azonosító megjelenítése  

Az eszköz első panelen LED-EK és a EBOD ház a közötti fő különbség a **rendszer egység azonosítószámot** az LED képernyőjén látható. Az azonosító megjelenik az eszköz alapértelmezett egység **00**, mivel az alapértelmezett egység azonosítója megjelenik a EBOD tárolóeszközön **01**. Ez lehetővé teszi, hogy gyorsan megkülönböztetéséhez az eszköz és a EBOD ház között, amikor az eszköz be van kapcsolva. Ha az eszköz ki van kapcsolva, használja a információk [új eszköz bekapcsolása](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) hogy megkülönböztessük az eszköz a EBOD ház.  

## <a name="front-panel-led-status"></a>Előlapi panel LED állapota
Az alábbi táblázat segítségével azonosíthatja a LED-ek a előlapján, az eszköz vagy a EBOD ház által jelzett állapotát.  

| Rendszer power | A modul hibája | Logikai hiba | Figyelmeztető riasztás | Állapot |
| --- | --- | --- | --- | --- |
| Red-amber |KI |KI |– |Hálózati áramellátás Visszaállt elveszett, a biztonsági mentési power vagy hálózati áramellátás Visszaállt működő és a vezérlő modulok el lettek távolítva. |
| Zöld |ON |ON |– |(5 mp) OPS panel bekapcsolási teszt állapota |
| Zöld |KI |KI |– |Bekapcsolási jó függvények |
| Zöld |ON |– |A PCM tartalék LED-EK, ventilátor tartalék LED-EK |Bármely PCM hibája, ventilátor hibája, felett vagy alatt hőmérséklet |
| Zöld |ON |– |I/o-modul LED-EK |Bármely tartományvezérlő modul hibája |
| Zöld |ON |– |– |A lemezház logikai hiba |
| Zöld |Flash |– |A modul állapota LED vezérlő modul. A PCM tartalék LED-EK, ventilátor tartalék LED-EK |Ismeretlen vezérlő modultípus telepítve, I2C bus hiba, a tartományvezérlő-modul létfontosságú product data (VPD) konfigurációs hiba |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Energiaellátási modul (PCM) jelző LED-ek hűtéssel
Energiagazdálkodási hűtési modul (PCM) jelző LED-ek egyes PCM modulon az elsődleges ház- és EBOD ház hátoldalán található. Ez a témakör ismerteti, hogyan használható a következő LED-ek a StorSimple-eszköz állapotának monitorozásához.  

* Az elsődleges ház a PCM LED-EK
* Az EBOD ház a PCM LED-EK

## <a name="pcm-leds-for-the-primary-enclosure"></a>Az elsődleges ház a PCM LED-EK
A StorSimple-eszköz egy további akkumulátor a PCM 764W modul rendelkezik. Az alábbi ábrán látható az eszköz a LED panelen.  

   ![A elsődleges tárolóeszközön PCM LED-EK][2]

Jelmagyarázat LED:

1. AC áramszünet esetén
2. Ventilátor hibája
3. Tartalék akkumulátor
4. A PCM OK
5. DC-hiba
6. Jó akkumulátor  

A PCM állapotának fel van tüntetve a LED panelen. Az eszköz PCM LED panel hat LED-ek rendelkezik. Négy ezek LED-ek állapotát jelenítik meg az áramforrás és a ventilátor. A fennmaradó két LED a biztonsági mentési akkumulátor a PCM-modulja állapotát jelzi. Az alábbi táblázat segítségével megállapíthatja a PCM állapotát.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>A PCM jelző LED-ek tápegység és ventilátor
| Állapot | A PCM OK (zöld) | AC sikertelen (sárga) | Ventilátor (sárga) fellépő hibák | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs (a ház) áramellátás Visszaállt |KI |KI |KI |KI |
| Nincs áramellátás Visszaállt (csak ez a PCM) |KI |ON |KI |ON |
| AC jelentenek PCM ON - OK |ON |KI |KI |KI |
| A PCM fail (sikertelen ventilátor.) |KI |KI |ON |– |
| A PCM-tartalék (keresztül amp feszültség keresztül jelenlegi keresztül) |KI |ON |ON |ON |
| A PCM (ventilátor tolerancia kívül) |ON |KI |KI |ON |
| Melegtartalék mód |Villogó |KI |KI |KI |
| A PCM belső vezérlőprogram letöltése |KI |Villogó |Villogó |Villogó |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>A biztonsági mentési akkumulátor a PCM jelző LED-EK
| Állapot | Akkumulátor jó (zöld) | Akkumulátor-tartalék (sárga) |
| --- | --- | --- |
| Akkumulátor nincs jelen |KI |KI |
| Telep megtalálható a díjazás? |ON |KI |
| Akkumulátor díjszabási vagy karbantartási lezárása |Villogó |KI |
| "Soft" tartalék akkumulátor (helyreállítható) |KI |Villogó |
| (Nem helyreállítható) "rögzített" tartalék akkumulátor |KI |ON |
| Ártalmatlanított akkumulátor |Villogó |KI |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Az EBOD ház a PCM LED-EK
Az EBOD ház egy 580W PCM és a további telep nem rendelkezik. Az EBOD ház PCM paneljének rendelkezik állapotjelző LED-ek csak az a tápegységek és a ventilátor. A következő ábrán ezek LED-ek.

   ![A EBOD tárolóeszközön PCM LED-EK][3] 

Az alábbi táblázat segítségével megállapíthatja a PCM állapotát.  

| Állapot | A PCM OK (zöld) | AC sikertelen (sárga) | Ventilátor (sárga) fellépő hibák | Tartományvezérlő sikertelen (sárga) |
| --- | --- | --- | --- | --- |
| Nincs (a ház) áramellátás Visszaállt |KI |KI |KI |KI |
| Nincs áramellátás Visszaállt (csak ez a PCM) |KI |ON |KI |ON |
| AC jelentenek PCM-ON – OK |ON |KI |KI |KI |
| A PCM fail (sikertelen ventilátor.) |KI |KI |ON |X |
| A PCM tartalék (keresztül amp keresztül, keresztül jelenlegi feszültség |KI |ON |ON |ON |
| A PCM (ventilátor tolerancia kívül) |ON |KI |KI |ON |
| Készenléti modell |Villogó |KI |KI |KI |
| A PCM belső vezérlőprogram letöltése |KI |Villogó |Villogó |Villogó |

## <a name="controller-module-indicator-leds"></a>Vezérlő modul jelző LED-EK
A StorSimple-eszköz LED-ek az elsődleges vezérlő és az EBOD-vezérlő modulokat tartalmaz.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Figyelési LED-ek az elsődleges vezérlő
Az alábbi ábra segítségével azonosíthatja a LED-ek az elsődleges tartományvezérlőn. (Minden összetevője a szerepelnek, ezzel elősegítve az elrendezés.)  

   ![Figyelési LED-ek - elsődleges vezérlő][4]

Az alábbi táblázat segítségével meghatározhatja, hogy megfelelően működik-e a tartományvezérlő modul.  

### <a name="controller-indicator-leds"></a>Vezérlő jelző LED-EK
| LED | Leírás |
| --- | --- |
| ID LED (kék) |Azt jelzi, hogy a modul azonosítja folyamatban van. Ha a kék LED egy futó tartományvezérlőn van villogó, majd a vezérlő az aktív vezérlő pedig a másik egy a készenléti állapotban lévő vezérlőnek. További információkért lásd: [az eszközön az aktív vezérlő megkeresése](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Tartalék LED (sárga) |Azt jelzi, hogy a vezérlő a hibát. |
| OK-LED (zöld) |Állandó zöld állapot azt jelzi, hogy a vezérlő-e az OK gombra. Zöld jelzi a vezérlő VPD konfigurációs hiba. |
| SAS-tevékenység LED-ek (zöld) |Az állandó zöld jelzi a jelenlegi tevékenység nélküli kapcsolatot. Zöld jelzi a kapcsolat van folyamatban lévő tevékenységbe. |
| Ethernet-állapot LED-EK |Jobb oldali hivatkozást és a hálózati tevékenységet jelez: (állandó zöld) hivatkozás aktív, (villogó zöld) hálózati tevékenységet. Bal oldali azt jelzi, hogy hálózati sebesség: (sárga) 1000 Mb/s (zöld) 100 Mb/s és (kikapcsolva) 10 Mb/s. Az összetevő modelltől függően a világos kurzorvillogás előfordulhat, hogy akkor is, ha a hálózati adapter nincs engedélyezve. |
| POST LED-EK |Azt jelzi, a rendszerindító előrehaladását, amikor a vezérlő be van kapcsolva. A StorSimple-eszköz rendszerindítását nem sikerül, ha a LED segít azonosítani a pont, ahol a hiba történt a betöltési folyamat Support. |

> [!IMPORTANT]
> A tartalék LED megvilágítva, van-e a tartományvezérlő modul, előfordulhat, hogy a vezérlő újraindításával elhárítható a probléma. Ha a vezérlő újraindítása nem oldja meg a probléma, forduljon a Microsoft Support.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Figyelési LED-ek esetében a EBOD (EBOD ház)
6 Gb/s SAS EBOD-vezérlő mindegyike rendelkezik, az alábbi ábrán látható módon az állapotát jelző LED-ek.  

  ![Figyelési LED-ek - EBOD ház][5]

Az alábbi táblázat segítségével meghatározhatja, hogy megfelelően működik-e az EBOD-vezérlő modul.  

### <a name="ebod-controller-module-indicator-leds"></a>Az EBOD-vezérlő modul jelző LED-EK
| Állapot | I/o-modul OK (zöld) | I/o-modul tartalék (sárga) | Host port tevékenység (zöld) |
| --- | --- | --- | --- |
| Vezérlő modul OK |ON |KI |- |
| Vezérlő modul hibája |KI |ON |- |
| Nincsenek külső állomásnév port kapcsolat |- |- |KI |
| Külső host port kapcsolat – nincs tevékenység |- |- |ON |
| Külső host port kapcsolat - tevékenység |- |- |Villogó |
| Hiba a tartományvezérlő modul metaadatok |Villogó |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lemezmeghajtó jelző LED-ek az elsődleges ház és EBOD ház
A StorSimple-eszköz is az elsődleges ház és a EBOD ház meghajtó van. Összes lemezmeghajtót tartalmaz figyelési jelző LED-EK, ebben a szakaszban leírtak szerint. 

A merevlemez-meghajtók esetében a meghajtó állapotát jelöli egy zöld LED és a egy piros-sárga LED csatlakoztatva van, minden meghajtó szolgáltatója modul elején. A következő ábrán ezek LED-ek.

  ![Lemezmeghajtó LED-EK][6]

Az alábbi táblázat segítségével meghatározhatja az egyes meghajtókon, amely viszont hatással van a teljes előlapi panel LED állapot állapotát.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Lemezmeghajtó jelző LED-ek esetében a EBOD ház
| Állapot | Tevékenység OK LED (zöld) | Tartalék LED (piros-sárga) | Az ops panel LED kapcsolódó |
| --- | --- | --- | --- |
| Nincs meghajtó telepítve |KI |KI |None |
| Telepített és működő meghajtó |A tevékenység be-és kikapcsolása villogó |X |None |
| SCSI Enclosure Services (SES) eszközt identitás beállítása |ON |Villogó 1 másodperc, a/1 második kikapcsolása |None |
| SES eszköz tartalék bit beállítva |ON |ON |Logikai hiba (piros) |
| Vezérlő kapcsolatcsoport áramkimaradás |KI |ON |A modul tartalék (piros) |

## <a name="audible-alarms"></a>Hallható riasztás
A StorSimple eszköz mind az elsődleges ház és a EBOD ház társított hallható riasztás tartalmazza. Egy hallható riasztás mindkét házakat előlapján (más néven az ops panel) található. A hallható riasztás azt jelzi, ha jelen-e egy tartalék feltételt. A riasztás akkor aktiválnak, az alábbi feltételek:  

* Ventilátor hibája, vagy hiba
* Engedélyezett tartományon kívül esik feszültségérzékelő
* Felett vagy alatt hőmérséklet feltétel
* Hőmérséklet-túllépés
* Rendszer-hiba
* Logikai hiba
* Ellátási hiba
* A modul (PCM) hűtéssel teljesítménye eltávolítása  

A következő táblázat ismerteti a különböző riasztási állapotok.  

### <a name="alarm-states"></a>Riasztási állapotok
| Riasztás állapota | Műveletek | Vypnutí gombokra művelet |
| --- | --- | --- |
| S0 |Normál üzemmódban: beavatkozás nélküli |Hangjelzés kétszer |
| S1 |Tartalék mód: / 1 1 másodperc, a második kikapcsolva |Átmenet S2 vagy S3 (tekintse meg a megjegyzéseit.) |
| S2 |Emlékeztesse mód: időszakos hangjelzés |None |
| S3 |Elfedett mód: beavatkozás nélküli |None |
| S4 |Kritikus hiba mód: folyamatos figyelmeztető riasztás |Nem érhető el: csend nem aktív |

> [!NOTE]
> * Riasztás állapota S1 nem lenyomja vypnutí 2 percen belül, ha az állapot automatikusan átkerül S2 vagy S3.  
> * Riasztási állapotok S1 szintű, S4 után térjen vissza S0 a tartalék feltétel nincs bejelölve.  
> * Kritikus hiba állapot S4 más állapotból lehet megadni.  


Az ops panel vypnutí gombjának megnyomásával a hallható riasztás is vypnutí. Automatikus némítása fog előfordulni két perc múlva a vypnutí kapcsoló nem manuálisan üzemeltetett. Ha a riasztás némított, továbbra is rövid időszakos hangjelzés jelzi, hogy a probléma továbbra is létezik a hangot. A riasztás csendes lesz, amikor az összes probléma törlődnek.

A következő táblázat ismerteti a különböző riasztási feltételeket.

### <a name="alarm-conditions"></a>Riasztási feltételek
| Állapot | Severity | Figyelmeztető riasztás | Az OPS LED panel |
| --- | --- | --- | --- |
| A PCM riasztás – DC áramkimaradás egyetlen PCM |Hiba – a redundancia adatvesztés nélkül |S1 |A modul hibája |
| A PCM riasztás – DC áramkimaradás egyetlen PCM |Hiba – a redundancia elvesztését |S1 |A modul hibája |
| Sikertelen a PCM ventilátor |Hiba – a redundancia elvesztését |S1 |A modul hibája |
| SBB-modul észlelhető PCM tartalék |Tartalék |S1 |A modul hibája |
| A PCM eltávolítva |Konfigurációs hiba |None |A modul hibája |
| A lemezház konfigurációs hiba |Hiba – kritikus |S1 |A modul hibája |
| Alacsony hőmérséklet figyelmeztetés |Figyelmeztetés |S1 |A modul hibája |
| Magas hőmérséklet figyelmeztetés |Figyelmeztetés |S1 |A modul hibája |
| Hőmérséklet-riasztás keresztül |Hiba – kritikus |S1 |A modul hibája |
| I2C bus hiba |Hiba – a redundancia elvesztését |S1 |A modul hibája |
| Az OPS kommunikációs hiba történt (I2C) panel |Hiba – kritikus |S1 |A modul hibája |
| Vezérlő hiba |Hiba – kritikus |S1 |A modul hibája |
| SBB felület modul hibája |Hiba – kritikus |S1 |A modul hibája |
| SBB felület modul hiba – nem működő modulok van hátra |Hiba – kritikus |S4 |A modul hibája |
| SBB felület modul eltávolítva |Figyelmeztetés |None |A modul hibája |
| Meghajtó ellenőrzési hiba |Figyelmeztetés – a meghajtó power adatvesztés nélkül |S1 |A modul hibája |
| Meghajtó ellenőrzési hiba |Hiba – kritikus; meghajtó áramkimaradás |S1 |A modul hibája |
| Meghajtó eltávolítása |Figyelmeztetés |None |A modul hibája |
| Az akkumulátor töltöttsége érhető el |Figyelmeztetés |Egyik sem |A modul hibája |

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple hardverösszetevők és állapot](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


