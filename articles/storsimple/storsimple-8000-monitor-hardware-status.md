---
title: A StorSimple 8000 sorozat hardverösszetevők és állapot |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti a hardverösszetevők, a StorSimple eszközt a StorSimple-Eszközkezelő szolgáltatással.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 58007eea9ce25423bc3a9c2847de42db04be43eb
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060830"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>A StorSimple-Eszközkezelő szolgáltatás figyelő hardverösszetevők és állapot használata

## <a name="overview"></a>Áttekintés
Ez a cikk a helyszíni StorSimple 8000 sorozatú eszköz különböző fizikai és logikai összetevőit ismerteti. Emellett ismerteti, hogyan az eszköz összetevő állapotát figyelemmel követhető a **állapot és hardverállapot** a StorSimple-Eszközkezelő szolgáltatás paneljén.

A **állapot és hardverállapot** panelen a StorSimple eszköz összetevők hardverek állapotát jeleníti meg.

A 8100-as összetevők listáját nincsenek leíró három szakaszra osztható:

* **Megosztott összetevők** – ezek nem részei a vezérlők, például meghajtók, a ház, a teljesítmény- és hűtési modul (PCM) összetevők és a PCM hőmérséklet feszültségérzékelő. sor, és aktuális érzékelők. sor.
* **0. vezérlő összetevői** – az összetevőket, amelyek megtalálhatók a vezérlő 0, például a vezérlő, SAS-bővítő és összekötő, adatkezelő hőmérséklet-érzékelő és a különböző hálózati adapterek.
* **1. vezérlő összetevői** – a vezérlő 1, 0. vezérlő részletes hasonló alkotó összetevők.

A 8600-as eszközön további összetevők, amelyek megfelelnek a kiterjesztett Bunch-lemezek (EBOD) ház rendelkezik. Az összetevők listájában alatt vannak öt szakaszokat. Ezek nincsenek három szakaszra osztható, amely tartalmazza az elsődleges lemezház összetevői és a 8100-as leírtaktól megegyeznek. Nincsenek két további szakaszokban a EBOD ház, amelyek ismertetik:

* **EBOD 0. vezérlő összetevői** – az összetevőket, amelyek megtalálhatók a EBOD ház 0, például az EBOD-vezérlő SAS bővítő és az összekötő, és a tartományvezérlő hőmérséklet-érzékelő.
* **Az EBOD-vezérlő 1 összetevők** – az összetevőket, amelyek jelentenek az EBOD ház 1, ezek részletes hasonló az EBOD ház 0.
* **EBOD ház megosztott összetevők** – az összetevők jelentenek az EBOD ház és PCM, amelyek nem részei az EBOD-vezérlő.

> [!NOTE]
> **A hardver állapotát a StorSimple Cloud Appliance (8010/8020) nem érhető el.**


## <a name="monitor-the-hardware-status"></a>A hardver állapotának figyelése
Hajtsa végre az alábbi lépéseket egy eszköz összetevő hardver állapotának megtekintése:

1. Navigáljon a **eszközök**, jelöljön ki egy adott StorSimple eszközt. Lépjen a **figyelő > hardverállapot**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Keresse meg a **hardverösszetevők** szakaszt, és az elérhető összetevők közül választhat. Egyszerűen kattintson az összetevő-címkére, bontsa ki a listát, és megtekintheti az eszköz különféle összetevőinek állapotát. Tekintse meg a [részletes összetevő listája az elsődleges ház](#component-list-for-primary-enclosure-of-storsimple-device) és a [részletes összetevő listája a EBOD ház](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Használja a következő kódolási színsémát értelmezni az összetevő-állapot:
   
   * **Ellenőrzés zöld** – azt jelzi, hogy a megfelelő állapotú összetevő **OK** állapotát.
   * **Sárga** – azt jelzi, hogy egy csökkentett teljesítményű összetevő **figyelmeztetés** állapota.
   * **Piros felkiáltójel** – Denotes a meghibásodott összetevőhöz, amely rendelkezik egy **hiba** állapotát.
   * **A fekete szöveg fehér** – azt jelzi, hogy egy összetevő, amely nem található.
   
   Az alábbi képernyőfelvételen a összetevőkkel rendelkező eszköz **OK**, **figyelmeztetés**, és **hiba** állapota.
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Kibontás a **közös összetevők listájában**, láthatjuk, hogy a NVRAM és a fürt csökkent.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Kibontás a **vezérlő 1 összetevők** listában, láthatjuk, hogy a fürtcsomópont nem sikerült.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Ha egy összetevő, amely nem része egy **kifogástalan** állapotban van, forduljon a Microsoft Support. Ha a riasztás engedélyezve van az eszközön, kapni fog riasztást kapjon. Ha a sikertelen hardverkomponensek cseréje van szüksége, tekintse meg [StorSimple összetevő hardvercseréhez](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>A StorSimple-eszköz elsődleges ház összetevő lista
Az alábbi táblázat ismerteti a fizikai és logikai összetevők, a helyszíni StorSimple eszköz (8100-as és 8600-as is elérhető) elsődleges ház található.

| Összetevő | Modul | Típus | Hely | Telepen cserélhető egység (FRU)? | Leírás |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a [0, 11] bővítőhelyen |Lemezmeghajtók |Fizikai |Közös |Igen |Az SSD vagy HDD meghajtókat a elsődleges ház az egysoros jelennek meg. |
| Környezeti hőmérséklet-érzékelő |Rendszerház |Fizikai |Közös |Nem |A váz a hőmérsékletet méri. |
| Közepes adatsík hőmérséklet-érzékelő |Rendszerház |Fizikai |Közös |Nem |A hőmérsékletet, a közepes sík méri. |
| Hallható riasztás |Rendszerház |Fizikai |Közös |Nem |Azt jelzi, hogy a hallható riasztás alrendszer a váz belül működik. |
| Rendszerház |Rendszerház |Fizikai |Közös |Igen |Jelzi, hogy a vázat. |
| Házbeállítások |Rendszerház |Fizikai |Közös |Nem |A váz előlapja hivatkozik. |
| Sor feszültség-érzékelő |A PCM |Fizikai |Közös |Nem |Számos sor feszültség-érzékelő állapotuk jelenik meg, amely azt jelzi, hogy a mért feszültség tűréshatáron belül van. |
| Sor aktuális érzékelők |A PCM |Fizikai |Közös |Nem |Számos sor aktuális érzékelők állapotuk jelenik meg, amely azt jelzi, hogy a mért jelenlegi tűréshatáron belül van. |
| Hőmérséklet-érzékelő a PCM-ben |A PCM |Fizikai |Közös |Nem |Számos például bemeneti hőmérséklet-érzékelő- és elérési pont érzékelők rendelkezik saját jelző állapot. jelenik meg, hogy tolerancia belül van-e a mért hőmérséklet. |
| [0-1] tápegység |A PCM |Fizikai |Közös |Igen |Egy sor minden a tápegységek, az a két PCMs, az eszköz hátoldalán található jelennek meg. |
| Hűtés a [0-1] |A PCM |Fizikai |Közös |Igen |Egy sor minden a két PCMs szereplő négy hűtőventilátorok jelennek meg. |
| Akkumulátor [0-1] |A PCM |Fizikai |Közös |Igen |Egy sor minden a a biztonsági mentési akkumulátor a PCM a rendszer illeszkedik modulok jelennek meg. |
| Metis |– |Logikai |Közös |– |Az elemek állapotát jeleníti meg: e azok díjszabási kell, és hamarosan eléri teljes életciklusa. |
| Fürt |– |Logikai |Közös |– |A fürtökön létrehozott két integrált vezérlő moduljai közötti állapotát jeleníti meg. |
| Fürtcsomópont |– |Logikai |Közös |– |Azt jelzi, hogy a fürt részeként a vezérlő állapota. |
| Fürtök kvóruma számára |– |Logikai | |– |Jelzi, hogy a legtöbb lemezen tagság a HDD-tárolókészlet. |
| HDD-adatterület |– |Logikai |Közös |– |A merevlemez-meghajtó (HDD) tárolókészletben levő adatok használt lemezterület. |
| Felügyeleti HDD-terület |– |Logikai |Közös |– |A felügyeleti feladatokhoz HDD a tárolókészletben lefoglalt terület. |
| HDD-kvórumterület |– |Logikai |Közös |– |A HDD-tárolókészlet fürtök kvóruma számára lefoglalt terület. |
| HDD-bővítőhely |– |Logikai |Közös |– |A HDD vezérlő cseréje a tárolókészletben lefoglalt terület. |
| SSD-adatterület |– |Logikai |Közös |– |A felhasznált lemezterület a tárolókészletben szemben – tartós állapotú meghajtót (SSD) adatok. |
| SSD NVRAM-terület |– |Logikai |Közös |– |A tárolókészlet SSD NVRAM logikai kijelölt tárolóhely. |
| HDD-tárolókészlet |– |Logikai |Közös |– |Az eszközről HDD-k létrehozása logikai a tárolókészlet állapotát jeleníti meg. |
| SSD-tárolókészlet |– |Logikai |Közös |– |Az eszközről SSD-k létrehozása logikai a tárolókészlet állapotát jeleníti meg. |
| Vezérlő [0-1] [állapot] |I/O |Fizikai |Vezérlő |Igen |A vezérlő állapotát jeleníti meg, és hogy a váz belül aktív vagy készenléti üzemmódban van. |
| Hőmérséklet-érzékelő a tartományvezérlő |I/O |Fizikai |Vezérlő |Nem |Például i/o-modul, CPU hőmérséklet, DIMM és PCIe érzékelők számos hőmérséklet-érzékelő állapotuk jelenik meg, amely azt jelzi-e a hőmérséklet észlelt tűréshatáron belül van. |
| SAS-bővítő |I/O |Fizikai |Vezérlő |Nem |A soros csatlakoztatott SCSI (SAS) bővítő, az integrált tárolási csatlakozni a vezérlő használt állapotát jelzi. |
| SAS-csatlakozó [0-1] |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy minden egyes SAS-csatlakozó, integrált tárolási csatlakozni a SAS-bővítő használt állapotát. |
| SBB közepes adatsík összekötöje |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy a közepes adatsík-összekötőt, amely minden vezérlő kapcsolódhat a közepes síknak állapotát. |
| Processzormag |I/O |Fizikai |Vezérlő |Nem |A Processzormagok belül minden vezérlő állapotát jelzi. |
| Házelektronikai tápegység |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy az a ház által használt energiaellátási rendszer állapotát. |
| Házelektronikai diagnosztika |I/O |Fizikai |Vezérlő |Nem |A diagnosztika alrendszerek a vezérlő által biztosított állapotát jelzi. |
| Alaplapi felügyeleti vezérlőnek (BMC) |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy az alaplapi felügyeleti vezérlőnek (BMC), amely egy speciális szolgáltatás processzor, amely figyeli a hardvereszköz érzékelők keresztül, és a rendszergazda független kapcsolaton keresztül kommunikál a állapotát. |
| Ethernet |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy a hálózati adapterek, amelyek a felügyeleti és a vezérlőn megadott adatok portok állapotát. |
| NVRAM |I/O |Fizikai |Vezérlő |Nem |NVRAM, egy biztonsági másolatot készít a telep fenntartani áramkimaradás esetén az alkalmazás kritikus fontosságú információkat ellátó nem felejtő közvetlen elérésű memória állapotát jelzi. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>A StorSimple-eszköz EBOD ház összetevő lista
Az alábbi táblázat ismerteti a fizikai és logikai összetevők, a helyszíni StorSimple eszköz (csak jelen 8600-as modell) EBOD ház található.

| Összetevő | Modul | Típus | Hely | FRU? | Leírás |
| --- | --- | --- | --- | --- | --- |
| Meghajtó a [0, 11] bővítőhelyen |Lemezmeghajtók |Fizikai |Közös |Igen |Egy sor minden a HDD meghajtókon található a EBOD ház jelennek meg. |
| Környezeti hőmérséklet-érzékelő |Rendszerház |Fizikai |Közös |Nem |A váz a hőmérsékletet méri. |
| Közepes adatsík hőmérséklet-érzékelő |Rendszerház |Fizikai |Közös |Nem |A hőmérsékletet, a közepes sík méri. |
| Hallható riasztás |Rendszerház |Fizikai |Közös |Nem |Azt jelzi, hogy a hallható riasztás alrendszer a váz belül működik. |
| Rendszerház |Rendszerház |Fizikai |Közös |Igen |Jelzi, hogy a vázat. |
| Házbeállítások |Rendszerház |Fizikai |Közös |Nem |Az OPS vagy a váz előlapja vonatkozik. |
| Sor feszültség-érzékelő |A PCM |Fizikai |Közös |Nem |Számos sor feszültség-érzékelő állapotuk jelenik meg, amely azt jelzi, hogy a mért feszültség tűréshatáron belül van. |
| Sor aktuális érzékelők |A PCM |Fizikai |Közös |Nem |Számos sor aktuális érzékelők állapotuk jelenik meg, amely azt jelzi, hogy a mért jelenlegi tűréshatáron belül van. |
| Hőmérséklet-érzékelő a PCM-ben |A PCM |Fizikai |Közös |Nem |Számos például bemeneti hőmérséklet-érzékelő- és elérési pont érzékelők rendelkezik az állapotuk jelenik meg, amely azt jelzi, hogy a mért hőmérséklet tűréshatáron belül vannak. |
| [0-1] tápegység |A PCM |Fizikai |Közös |Igen |Egy sor minden a tápegységek, az a két PCMs, az eszköz hátoldalán található jelennek meg. |
| Hűtés a [0-1] |A PCM |Fizikai |Közös |Igen |Egy sor minden a két PCMs szereplő négy hűtőventilátorok jelennek meg. |
| Helyi tároló [HDD] |– |Logikai |Közös |– |Az eszközről HDD-k létrehozása logikai a tárolókészlet állapotát jeleníti meg. |
| Vezérlő [0-1] [állapot] |I/O |Fizikai |Vezérlő |Igen |Az EBOD-modul a tartományvezérlők állapotát jeleníti meg. |
| Az EBOD hőmérséklet-érzékelő |I/O |Fizikai |Vezérlő |Nem |Minden egyes vezérlőről számos hőmérséklet-érzékelő állapotuk jelenik meg, amely azt jelzi, hogy a hőmérséklet észlelt tűréshatáron belül van. |
| SAS-bővítő |I/O |Fizikai |Vezérlő |Nem |A SAS-bővítő, az integrált tárolási csatlakozni a vezérlő használt állapotát jelzi. |
| SAS-csatlakozó [0, 2] |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy minden egyes SAS-csatlakozó, integrált tárolási csatlakozni a SAS-bővítő használt állapotát. |
| SBB közepes adatsík összekötöje |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy a közepes adatsík-összekötőt, amely minden vezérlő kapcsolódhat a közepes síknak állapotát. |
| Házelektronikai tápegység |I/O |Fizikai |Vezérlő |Nem |Azt jelzi, hogy az a ház által használt energiaellátási rendszer állapotát. |
| Házelektronikai diagnosztika |I/O |Fizikai |Vezérlő |Nem |A diagnosztika alrendszerek a vezérlő által biztosított állapotát jelzi. |
| Az eszközvezérlő kapcsolat |I/O |Fizikai |Vezérlő |Nem |Az EBOD i/o-modulja, és az eszközvezérlő közötti kapcsolat állapotát jelzi. |

## <a name="next-steps"></a>További lépések
* Az eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás használatához lépjen a [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).
* Ha egy eszköz összetevő csökkentett teljesítményű vagy sikertelen állapotú hibaelhárítása van szüksége, tekintse meg [ellenőrzési mutatók StorSimple](storsimple-monitoring-indicators.md).
* Sikertelen hardverkomponensek cseréje, lásd: [StorSimple összetevő hardvercseréhez](storsimple-hardware-component-replacement.md).
* Ha továbbra is eszköz problémákba [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

