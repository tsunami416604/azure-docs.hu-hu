---
title: "StorSimple hardverösszetevők és állapot |} Microsoft Docs"
description: "Útmutató a hardverösszetevők, a StorSimple eszközt a StorSimple Manager szolgáltatással."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0d56a2ba-daf0-45ad-9610-8b8712dd5750
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: 93df79c9d349c294b692148a19c9d881c4de4f7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-hardware-components-and-status"></a>A StorSimple Manager szolgáltatással, a figyelő hardverösszetevők és állapota
## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a helyszíni StorSimple eszköz különböző fizikai és logikai összetevők. Azt is bemutatja, hogyan használatával az eszköz összetevő-állapot figyelése a **karbantartási** a StorSimple Manager szolgáltatás lapján. 

A **karbantartási** lap StorSimple eszköz összetevőit a hardver állapotát jeleníti meg. 

A 8100 összetevők listáját nincsenek három szakaszait:

* **A megosztott összetevőit** – ezek nem részét képezik a tartományvezérlők, például a lemezmeghajtók, ház, PCM összetevők PCM hőmérséklet feszültségérzékelő. sor, és aktuális érzékelők sor.
* **A vezérlő 0 összetevők** – az összetevők, amelyek megtalálhatók a vezérlő 0, például a tartományvezérlő, SAS bővítő és összekötő, vezérlő hőmérséklet-érzékelő és a különböző hálózati adapterek.
* **1. vezérlő összetevők** – a vezérlő 1, a vezérlő 0 részletes hasonló alkotó összetevők.

Egy 8600 eszköz regisztrációját, hogy a kibővített álló, lemezcsoport a lemezek (EBOD) ház további összetevőket. Azon összetevők listáját, a szakaszok is vannak öt. Ezek a szakaszok is vannak három, melyek az elsődleges ház összetevői és a 8100 ismertetettekhez azonosak. Nincsenek két további szakaszaiban a a EBOD ház, amelyek ismertetik:

* **EBOD ház megosztott összetevők** – az összetevők a EBOD ház- és a EBOD vezérlő részét nem képező PCM szerepelnek.
* **EBOD vezérlő 0 összetevők** – az összetevők, amelyek megtalálhatók a EBOD ház 0, például a EBOD vezérlő SAS bővítő összekötő és a tartományvezérlő hőmérséklet-érzékelő.
* **EBOD vezérlő 1 összetevők** – a EBOD ház 1, ezek részletes hasonló a EBOD ház 0 alkotó összetevők.

> [!NOTE]
> **A hardver állapotát szakaszban nincs jelen a karbantartási oldalon egy StorSimple virtuális eszköz (1100-as).**
> 
> 

## <a name="monitor-the-hardware-status"></a>A hardver állapotának figyelése
A következő lépésekkel egy eszköz összetevő hardver állapotának megtekintése:

1. Navigáljon a **eszközök**, jelöljön ki egy adott StorSimple eszközt. Nyissa meg a eszközszintű menüben, majd kattintson **karbantartási**. 
2. Keresse meg a **hardverállapot** szakaszt, és válassza ki a rendelkezésre álló összetevői (a fentiekben ismertetett). Egyszerűen kattintson a nyíl megelőző a összetevő címke bontsa ki a listában, és tekintse meg a különböző eszköz összetevők állapotát. Tekintse meg a [részletes összetevő listája az elsődleges ház](#component-list-for-primary-enclosure-of-storsimple-device) és a [részletes összetevő listája a EBOD ház](#component-list-for-ebod-enclosure-of-storsimple-device).
3. Használja a következő kódolási színséma értelmezni az összetevő-állapot:
   
   * **Jelölőnégyzet zöld** – Denotes egy **kifogástalan** vagy **OK** összetevő.
   * **Sárga** – egy összetevő jelöli **figyelmeztetés** állapotát.
   * **Piros felkiáltójel** – Denotes egy összetevő, amely rendelkezik egy **hiba** vagy **figyelmet** állapotát.
   * **A fekete szöveg fehér** – azt jelzi, amely nem található.
4. Ha egy összetevő, amely nem része egy **kifogástalan** állapot, forduljon a Microsoft Support. Ha a riasztás az eszköz engedélyezve van, az e-mail riasztások fog kapni. Ha egy meghibásodott hardverelem cserélni kell, lásd: [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>A StorSimple eszköz elsődleges ház összetevő listája
Az alábbi táblázat ismerteti a helyszíni StorSimple eszköz elsődleges ház található fizikai és logikai összetevőket.

| Összetevő | Modul | Típus | Hely | A mező cserélhető Cisco egységet (FRU)? | Leírás |
| --- | --- | --- | --- | --- | --- |
| [0 – 11] tárolóhelyen lévő meghajtó |Lemezmeghajtó |Fizikai |Közös |Igen |Egy sor számára jelenik meg az egyes az SSD és a HDD meghajtókat, az elsődleges szolgáltatással. |
| Környezeti hőmérséklet-érzékelő |Ház |Fizikai |Közös |Nem |A váz hőmérsékletet méri. |
| Közepes vezérlősík hőmérséklet-érzékelő |Ház |Fizikai |Közös |Nem |A közepes vezérlősík hőmérséklete méri. |
| Hallható |Ház |Fizikai |Közös |Nem |Azt jelzi, hogy a ház hallható alrendszerének működési. |
| Ház |Ház |Fizikai |Közös |Igen |A váz jelenlétét jelzi. |
| Ház beállításai |Ház |Fizikai |Közös |Nem |A váz előlapja hivatkozik. |
| Sor feszültség-érzékelő |PCM |Fizikai |Közös |Nem |Számos sor feszültség-érzékelő állapotukra jelenik meg, amely azt jelzi, hogy a mért feszültség tűréshatáron belül van. |
| Sor aktuális érzékelők |PCM |Fizikai |Közös |Nem |Számos sor aktuális érzékelők állapotukra jelenik meg, amely azt jelzi, hogy a mért jelenlegi tűréshatáron belül van. |
| A PCM hőmérséklet-érzékelő |PCM |Fizikai |Közös |Nem |Számos hőmérséklet-érzékelő például Inlet és interaktív terület érzékelők rendelkezik állapotukra jelenik meg, amely azt jelzi, hogy a mért hőmérséklet tűréshatáron belül vannak. |
| [0-1]. tápegység |PCM |Fizikai |Közös |Igen |Egy sor számára jelenik meg az egyes a áramforrással rendelkezik, az a két PCMs, az eszköz hátoldalán található. |
| Hűtési [0-1] |PCM |Fizikai |Közös |Igen |Egy sor minden a két PCMs szereplő négy hűtőventilátorok áll rendelkezésre. |
| Akkumulátor [0-1] |PCM |Fizikai |Közös |Igen |Egy sor minden rendszer illeszkedik a PCM a biztonsági mentési akkumulátor modulok áll rendelkezésre. |
| Metis |N/A |Logikai |Közös |N/A |Az elemek állapotát jeleníti meg: e azok díjszabási kell, és vannak eléri az élettartam végi. |
| Fürt |N/A |Logikai |Közös |N/A |A fürtöt, létrejön a két integrált vezérlő modulok között állapotát jeleníti meg. |
| Fürtcsomópont |N/A |Logikai |Közös |N/A |A tartományvezérlő a fürt részeként állapotát jelzi. |
| A fürtkvórum |N/A |Logikai | |N/A |A legtöbb lemez tagsági HDD tárolókészletben jelenlétét jelzi. |
| HDD adatterülethez |N/A |Logikai |Közös |N/A |A tárolóhely a merevlemez-meghajtó (HDD) tárolókészletben használt. |
| HDD felügyeleti hely |N/A |Logikai |Közös |N/A |A felügyeleti feladatok HDD a tárolókészletben lefoglalt terület. |
| HDD kvórum terület |N/A |Logikai |Közös |N/A |A fürtkvórum HDD a tárolókészletben lefoglalt terület. |
| HDD helyettesítő terület |N/A |Logikai |Közös |N/A |A vezérlő helyettesítő HDD a tárolókészletben lefoglalt terület. |
| SSD-adatterülethez |N/A |Logikai |Közös |N/A |A tárolókészletben tartós állapotú meghajtót (SSD) adatok felhasznált tárterület. |
| SSD NVRAM terület |N/A |Logikai |Közös |N/A |A tárolóhely, amely az NVRAM logika SSD a tárolókészletben. |
| HDD-tárolókészletben |N/A |Logikai |Közös |N/A |A logikai tárolókészletet, amely az eszköz HDD készül állapotát jeleníti meg. |
| A tárolókészlet SSD |N/A |Logikai |Közös |N/A |A logikai tárolókészlet SSD-k eszközről létrehozott állapotát jeleníti meg. |
| A vezérlő [0-1] [-state] |I/O |Fizikai |Tartományvezérlő |Igen |A vezérlő állapotát jeleníti meg, és hogy a ház belül aktív vagy készenléti üzemmódban van. |
| A vezérlő hőmérséklet-érzékelő |I/O |Fizikai |Tartományvezérlő |Nem |I/o-modul, CPU hőmérséklet, DIMM és PCIe érzékelők például számos hőmérséklet-érzékelő állapotukra jelenik meg, amely azt jelzi, hogy van-e a észlelt hőmérséklet tűréshatáron belül van. |
| SAS bővítő |I/O |Fizikai |Tartományvezérlő |Nem |A soros csatlakoztatott SCSI (SAS) bővítő, amely az integrált tárolási a vezérlőhöz való csatlakozáshoz használt állapotát jelzi. |
| SAS-összekötő [0-1] |I/O |Fizikai |Tartományvezérlő |Nem |Minden SAS-összekötő, amely integrált tárolási kapcsolódni a biztonsági Társítások bővítő állapotát jelzi. |
| SBB közepes vezérlősík memóriamodulok közötti |I/O |Fizikai |Tartományvezérlő |Nem |A közepes vezérlősík összekötő, mely minden vezérlő a közepes vezérlősík való csatlakozáshoz használt állapotát jelzi. |
| Processzormag |I/O |Fizikai |Tartományvezérlő |Nem |A Processzormagok belül minden vezérlő állapotát jelzi. |
| Ház electronics power |I/O |Fizikai |Tartományvezérlő |Nem |Azt jelzi, hogy a ház használt energiagazdálkodási rendszert állapotát. |
| Ház electronics diagnosztika |I/O |Fizikai |Tartományvezérlő |Nem |A diagnosztika alrendszereket, a tartományvezérlő által biztosított állapotát jelzi. |
| Alaplapi felügyeleti vezérlővel (BMC) |I/O |Fizikai |Tartományvezérlő |Nem |Azt jelzi, hogy az alaplapi felügyeleti vezérlő (BMC), amely egy speciális szolgáltatás processzor, amely figyeli a hardvereszköz érzékelők keresztül, és a rendszergazda független kapcsolaton keresztül kommunikál az állapotát. |
| Ethernet |I/O |Fizikai |Tartományvezérlő |Nem |A hálózati adapterek, ez azt jelenti, hogy a felügyeleti és a vezérlőn a megadott adatok portok minden egyes állapotát jelzi. |
| NVRAM |I/O |Fizikai |Tartományvezérlő |Nem |NVRAM, egy biztonsági mentést készített az akkumulátor ellátó fenntartani alkalmazáshoz kritikus információkat áramkimaradás esetén nem felejtő közvetlen elérésű memória állapotát jelzi. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>A StorSimple eszköz EBOD ház összetevő listája
Az alábbi táblázat ismerteti a fizikai és logikai összetevők a EBOD ház a helyszíni StorSimple eszköz szerepel.

| Összetevő | Modul | Típus | Hely | FRU? | Leírás |
| --- | --- | --- | --- | --- | --- |
| [0 – 11] tárolóhelyen lévő meghajtó |Lemezmeghajtó |Fizikai |Közös |Igen |Egy sor elején található a EBOD ház a HDD meghajtókhoz áll rendelkezésre. |
| Környezeti hőmérséklet-érzékelő |Ház |Fizikai |Közös |Nem |A váz hőmérsékletet méri. |
| Közepes vezérlősík hőmérséklet-érzékelő |Ház |Fizikai |Közös |Nem |A közepes vezérlősík hőmérséklete méri. |
| Hallható |Ház |Fizikai |Közös |Nem |Azt jelzi, hogy a ház hallható alrendszerének működési. |
| Ház |Ház |Fizikai |Közös |Igen |A váz jelenlétét jelzi. |
| Ház beállításai |Ház |Fizikai |Közös |Nem |Az OPS vagy a váz előlapja vonatkozik. |
| Sor feszültség-érzékelő |PCM |Fizikai |Közös |Nem |Számos sor feszültség-érzékelő állapotukra jelenik meg, amely azt jelzi, hogy a mért feszültség tűréshatáron belül van. |
| Sor aktuális érzékelők |PCM |Fizikai |Közös |Nem |Számos sor aktuális érzékelők állapotukra jelenik meg, amely azt jelzi, hogy a mért jelenlegi tűréshatáron belül van. |
| A PCM hőmérséklet-érzékelő |PCM |Fizikai |Közös |Nem |Számos hőmérséklet-érzékelő például Inlet és interaktív terület érzékelők rendelkezik állapotukra jelenik meg, amely azt jelzi, hogy a mért hőmérséklet tűréshatáron belül vannak. |
| [0-1]. tápegység |PCM |Fizikai |Közös |Igen |Egy sor számára jelenik meg az egyes a áramforrással rendelkezik, az a két PCMs, az eszköz hátoldalán található. |
| Hűtési [0-1] |PCM |Fizikai |Közös |Igen |Egy sor minden a két PCMs szereplő négy hűtőventilátorok áll rendelkezésre. |
| Helyi tároló [HDD] |N/A |Logikai |Közös |N/A |A logikai tárolókészletet, amely az eszköz HDD készül állapotát jeleníti meg. |
| A vezérlő [0-1] [-state] |I/O |Fizikai |Tartományvezérlő |Igen |A tartományvezérlők állapotát jeleníti meg a EBOD modulban. |
| A EBOD hőmérséklet-érzékelő |I/O |Fizikai |Tartományvezérlő |Nem |Minden tartományvezérlőről számos hőmérséklet-érzékelő állapotukra jelenik meg, amely azt jelzi, hogy az észlelt hőmérséklet tűréshatáron belül van. |
| SAS bővítő |I/O |Fizikai |Tartományvezérlő |Nem |A biztonsági Társítások bővítő, amely az integrált tárolási a vezérlőhöz való csatlakozáshoz használt állapotát jelzi. |
| SAS-összekötő [0-2] |I/O |Fizikai |Tartományvezérlő |Nem |Minden SAS-összekötő, amely integrált tárolási kapcsolódni a biztonsági Társítások bővítő állapotát jelzi. |
| SBB közepes vezérlősík memóriamodulok közötti |I/O |Fizikai |Tartományvezérlő |Nem |A közepes vezérlősík összekötő, mely minden vezérlő a közepes vezérlősík való csatlakozáshoz használt állapotát jelzi. |
| Ház electronics power |I/O |Fizikai |Tartományvezérlő |Nem |Azt jelzi, hogy a ház használt energiagazdálkodási rendszert állapotát. |
| Ház electronics diagnosztika |I/O |Fizikai |Tartományvezérlő |Nem |A diagnosztika alrendszereket, a tartományvezérlő által biztosított állapotát jelzi. |
| Kapcsolat eszköz-vezérlő |I/O |Fizikai |Tartományvezérlő |Nem |A EBOD i/o-modul és az eszköz a tartományvezérlő közötti kapcsolat állapotát jelzi. |

## <a name="next-steps"></a>Következő lépések
* Az eszköz felügyelete a StorSimple Manager szolgáltatás használatához lépjen [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).
* Ha egy eszköz összetevő, amely a csökkentett teljesítményű vagy sikertelen állapotához hibaelhárítása van szüksége, tekintse meg a [ellenőrzési mutatók StorSimple](storsimple-monitoring-indicators.md). 
* Cserélje le egy meghibásodott hardverelem, lásd: [StorSimple hardver összetevő cseréje](storsimple-hardware-component-replacement.md).
* Ha az eszköz problémák is [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).

