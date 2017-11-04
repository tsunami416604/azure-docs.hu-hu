---
title: "A StorSimple Manager szolgáltatás irányítópultját |} Microsoft Docs"
description: "Ismerteti a StorSimple Manager szolgáltatás irányítópultját, és ismerteti a StorSimple megoldásban állapotának figyelése céljából."
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-dashboard"></a>A StorSimple Manager szolgáltatás irányítópult
## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás irányítópult-oldalon összegzését jeleníti meg, a StorSimple Manager szolgáltatáshoz, azok a rendszergazda figyelmet igénylő kiemelés csatlakozó eszközöket. Ez az oktatóanyag vezet be az irányítópult-oldalon, ismerteti az irányítópult-tartalom és a függvény, és azokat a feladatokat hajthat végre ezen a lapon.

![Szolgáltatási irányítópult](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

A StorSimple Manager szolgáltatás irányítópultját az alábbi információkat jeleníti meg:

* Az a **diagram** területen láthatja a lényeges mérőszámok diagram az eszközökhöz. Megtekintheti az elsődleges tárolási (helyileg rögzített és Szintezett) használ, amelyek az eszközök, valamint egy meghatározott időtartamra vonatkozóan eszközök által használt felhőbeli tárhelyhez. A diagram jobb felső sarkában a vezérlőkkel adja meg egy 1 hét, hónap 1, 3 hónapos vagy 1 év időskálára.
* A **használatának áttekintése** a kiosztásakor és az összes eszközön viszonyítva a teljes tárterület érhető el minden eszköz által használt elsődleges tárterület megjelenítése. **Kiépített** hivatkozik, amely készen áll a testreszabásra, és használatra, miközben tárolókapacitást **használható** módon tekinthetők a kezdeményezőket, amelyek a megadott eszközökhöz csatlakoztatott kötetek használatát hivatkozik.
* A **riasztások** területen láthatók az összes aktív riasztás pillanatképet minden eszközön, riasztások súlyosság szerint csoportosítva. Milyen súlyossági szintről kattintva megnyílik a **riasztások** lap, ezek a riasztások megjelenítése hatókörébe. Az a **riasztások** lap, kattintson a riasztást, többek között a javasolt művelet kapcsolatos további részletek megtekintéséhez egyéni riasztást. A riasztás is törölheti, ha a probléma megoldódott.
* A **feladatok** terület legutóbbi feladatok pillanatképe lehetővé teszi az összes eszközről, amelyen a szolgáltatáshoz való kapcsolódás. Nincsenek hivatkozásokat tartalmaz, amelyek használatával nézze meg, amelyek jelenleg folyamatban van, azokat, az elmúlt 24 órában nem sikerült, vagy a következő 24 órában ütemezett feladatokat.
* A **gyors áttekintő** terület nyújtanak hasznos információkat szolgáltatás állapota, például az eszközök számától csatlakozik a szolgáltatást, a szolgáltatás helyét, és az előfizetés, a szolgáltatáshoz kapcsolódó részleteit. A műveleti napló mutató hivatkozás is van. Kattintson a hivatkozásra kattintva megtekintheti az összes befejezett StorSimple Manager szolgáltatás műveletek listáját.

A StorSimple Manager szolgáltatás irányítópult-oldalon segítségével kezdeményezheti a következő feladatokat:

* Megtekintheti, és a szolgáltatás regisztrációs kulcsának újragenerálása.
* Módosítsa a szolgáltatásadat-titkosítási kulcs.
* A műveletnaplók megtekintése.

## <a name="view-or-regenerate-the-service-registration-key"></a>Megtekintéséhez vagy a szolgáltatás regisztrációs kulcsának újragenerálása
A szolgáltatás regisztrációs kulcsának egy Microsoft Azure StorSimple-eszköz regisztrálása a StorSimple Manager szolgáltatás szolgál, hogy az eszköz megjelenik a további felügyeleti műveletek a klasszikus Azure portálon. A kulcs az első eszközön létrehozott és megosztott a többi eszközt.

Kattintson a **regisztrációs kulcs** (a lap alján) megnyitja a **Szolgáltatásregisztrációs kulcs** párbeszédpanel, ahol az aktuális Szolgáltatásregisztrációs kulcs másolása a vágólapra, vagy a szolgáltatás regisztrációs kulcsának újragenerálása.

A kulcs újragenerálása nincs hatással a korábban regisztrált eszközökre: csak a után a rendszer újragenerálja a kulcsot a szolgáltatásban regisztrált eszközök hatással van.

Megtekintés, és a szolgáltatás regisztrációs kulcsának létrehozása kapcsolatos további információkért látogasson el [Szolgáltatásregisztrációs kulcs lekérése](storsimple-manage-service.md#get-the-service-registration-key).

## <a name="change-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs módosítása
Szolgáltatás adattitkosítási kulcsokat bizalmas felhasználói adatok, például a tárfiók hitelesítő adatait, a StorSimple eszközt a StorSimple Manager szolgáltatás a küldött titkosításához használt. Ezek a kulcsok rendszeresen módosítható, ha az IT-szervezet egy kulcs Elforgatás szabályzattal rendelkezik a tárolóeszközök kell. A kulcs változási folyamat kis mértékben eltérhet attól függően, hogy van egy egyetlen vagy több eszközön, a StorSimple Manager szolgáltatás kezeli.

A szolgáltatásadat-titkosítási kulcs módosítása egy 3 lépéses folyamat:

1. A klasszikus Azure portál használatával, engedélyezéséhez módosítsa a szolgáltatásadat-titkosítási kulcs az eszköz.
2. A Windows PowerShell használatával a StorSimple, indítsa el a szolgáltatás titkosítási kulcs változását.
3. Ha egynél több StorSimple eszközt, a más eszközökön a szolgáltatásadat-titkosítási kulcs frissítése

Az alábbi lépéseket a szolgáltatásadat-titkosítási kulcs váltása folyamatát ismertetik.

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>A műveletek naplók megtekintése
A műveletnaplók elérhető művelet naplók hivatkozásra kattintva megtekintheti a **gyors áttekintő** az irányítópult panelén. Ekkor megjelenik a felügyeleti szolgáltatások lapra, ahol szűréséhez, és tekintse meg az adott naplókat a StorSimple Manager szolgáltatáshoz.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [hibaelhárítása a StorSimple eszköz](storsimple-troubleshoot-operational-device.md).
* További tudnivalók a [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

