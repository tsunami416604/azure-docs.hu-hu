---
title: "A StorSimple-kötet visszaállítása biztonsági másolatból |} Microsoft Docs"
description: "A StorSimple Manager szolgáltatás biztonságimásolat-katalógus lapján a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet használatát ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/22/2017
ms.author: alkohli
ms.openlocfilehash: 99b76e3bc2939c65654cbf606fda6f8a45e0c44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>A StorSimple-kötet visszaállítása egy biztonságimásolat-készlet (2. frissítés)
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Áttekintés
A **biztonságimásolat-katalógus** lap megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. Ezen a lapon a listán, és biztonsági mentéseit, visszaállítani a biztonságimásolat-készletből, vagy a klónozáshoz kötet.

 ![Biztonságimásolat-katalógus lap](./media/storsimple-restore-from-backup-set-u2/restore.png)

Ez az oktatóanyag bemutatja, hogyan használja a **biztonságimásolat-katalógus** lapon az eszköz visszaállítása egy biztonságimásolat-készlet.

Visszaállíthatja egy helyi kötet vagy a felhőbeli biztonsági mentését. Mindkét esetben a visszaállítási művelet számos lehetőséget kínál a kötet online azonnal, amíg az adatok letöltődik a háttérben. 

## <a name="before-you-restore"></a>Mielőtt vissza tudná állítani
Még a visszaállítás elindítása előtt meg kell ügyelnie, az alábbi korlátozásokkal:

* **A kötet offline állapotba** – a kötet offline állapotba a gazdagép és az eszköz is igénybe vehet, még a visszaállítás elindítása előtt. Bár a visszaállítási művelet automatikusan kiválasztja a kötetet az eszközön, manuálisan kell hoznia az eszköz online a gazdagépen. Ha a kötet online állapotú, az eszközön, akkor a kötet online elérhetővé tenni a gazdagépen. (Nem kell várnia, amíg a visszaállítási művelet nem fejeződött.) Az eljárások, nyissa meg a [kötet offline állapotba](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Visszaállítás után kötettípus** – törölt kötetek visszaállítása a pillanatkép típusa alapján. Volt helyileg rögzített kötetek visszaállítását végzi el a helyileg rögzített kötetekhez, és volt rétegzett kötetek visszaállítását végzi el a rétegzett kötetek.
  
    Meglévő kötetek a kötet aktuális használati típusú felülírja a pillanatkép tárolt típusát. Például ha egy köteten, ha a kötet típusa történt rétegzett, és (miatt egy átalakítási műveletet) mostantól helyileg rögzített kötet típusa történt egy pillanatképből, majd a kötet visszaállítja egy helyileg rögzített kötet. Hasonlóképpen meglévő helyileg rögzített kötet kibontva, és ezt követően a hajt végre, amikor kisebb volt a kötet egy korábbi pillanatképből állítottak vissza, ha a visszaállított kötet megtartja az aktuális kiterjesztett méretét.
  
    Nem lehet konvertálni a kötet egy rétegzett kötet egy helyileg rögzített kötet vagy _viszont_ közben a kötet visszaállítása folyamatban van. Várjon, amíg a visszaállítás befejeződött, majd egy másik átválthat a kötetet. A kötet konvertálása kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **A kötet mérete megjelenik a visszaállított kötet** – Ez akkor fontos szempont, ha egy helyileg rögzített kötet (mert helyileg rögzített kötetekhez teljesen kiépített) törölt visszaállítását végzi. Győződjön meg arról, hogy van elegendő hely, mielőtt megkísérli visszaállítani egy korábban törölt helyileg rögzített kötet. 
* **A kötet nem terjeszthető ki, miközben visszaállítása folyamatban van** – Várjon, amíg a visszaállítási művelet befejezése előtt bontsa ki a kötetet. A kötet kiterjesztését kapcsolatos információkért látogasson el [kötet módosítása](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Végezheti el a biztonsági mentés közben állítja vissza egy kötet** – az eljárások Ugrás [a StorSimple Manager szolgáltatás segítségével kezelheti a biztonsági mentési házirendeket](storsimple-manage-backup-policies.md).
* **A visszaállítási művelet megszakíthatja** – Ha a kötet vissza lesz állítva indította el, a visszaállítás előtti állapotba, majd szakítsa meg a visszaállítási feladat. Az eljárások, nyissa meg a [feladatok megszakítása](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hogyan állítsa vissza a munkahelyi
4 vagy újabb frissítés rendszerű eszközöket a heatmap-alapú visszaállítási van megvalósítva. Mivel a gazdagép kéri, hogy érheti el az adatait elérni az eszközön, ezeket a kérelmeket a rendszer nyomon, és egy heatmap jön létre. Magas kérelmek aránya magasabb tűz az adattömbök eredményez, mivel alacsonyabb kérelmek aránya az eszköz az alsó tűz adattömböket. Az adatok legalább kétszer állapotúként kell megjelölni a kell elérnie _működés közbeni_. A fájl módosított is van megjelölve _működés közbeni_. Miután elindította a visszaállítás, az adatok proaktív hidratálásával történik a heatmap alapján. Verziók Update 4-nél korábbi adatok letöltött alapuló hozzáférés csak visszaállítás során. 

Heatmap-alapú engedélyezve van a változáskövetés csak rétegzett kötet, és helyileg rögzített kötetek nem támogatottak. Heatmap-alapú visszaállítási nem is támogatott, ha egy másik eszközre kötet klónozása. Ha egy helyi visszaállítási, és egy helyi pillanatfelvétel állítani a kötet létezik-e az eszközön, majd azt nem rehydrate (mivel az adatok már helyileg nem érhető el). Alapértelmezés szerint történő visszaállításához, a rendszer kezdeményezett a rehidratálása feladatok proaktív rehydrate az adatokat a heatmap alapján. Az Update 4, Windows PowerShell-parancsmagok használhatók futó rehidratálása feladat lekérdezése rehidratálása feladatok megszakítása, és a rehidratálása feladat állapotának beolvasása.

* `Get-HcsRehydrationJob`– Ez a parancsmag a rehidratálása feladat állapotának beolvasása. Egy kötet egy egyetlen rehidratálása feladat elindul.
* `Set-HcsRehydrationJob`– Ez a parancsmag lehetővé teszi szüneteltetése, állítsa le, a rehidratálása feladat folytatódik, amint a rehidratálása folyamatban van. 

Rehidratálása parancsmagok további információkért látogasson el [Windows PowerShell parancsmag-referencia a StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Az automatikus rehdyration általában magasabb átmeneti olvasási teljesítmény várható. A tényleges magniutde fejlesztései számos tényező befolyásolja, például a hozzáférési mintázatát, adatmennyiség és adattípus függ. Szakítsa meg a rehidratálása feladatot, a PowerShell-parancsmagot is használhat. Ha végleg tiltsa le a jövőbeli visszaállítások rehidratálása feladataihoz, forduljon a Microsoft Support.

## <a name="how-to-use-the-backup-catalog"></a>A biztonsági mentési katalógus használata
A **biztonságimásolat-katalógus** lap biztosít egy lekérdezést, amely segít a megadásával szűkíthető, a biztonsági másolat beállítása. A biztonságimásolat-készletek, amelyek a rendszer beolvassa a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyre a biztonságimásolat-készlet létrehozása.
* **Biztonsági mentési házirend** vagy **kötet** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társítva.
* **A** és **való** – a dátum és idő tartomány, a biztonságimásolat-készlet létrehozásakor.

A szűrt biztonsági mentés majd megjelennének a következő attribútumok alapján:

* **Név** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Méret** – a biztonságimásolat-készlet tényleges méretének.
* **A létrehozott** – a dátum és idő, amikor a biztonsági mentés készült. 
* **Típus** – biztonsági másolat beállítása helyi pillanatképeket és felhőalapú pillanatfelvételek. Egy helyi pillanatfelvétel az összes kötet adatait az eszközön helyileg tárolt biztonsági másolatot. Egy felhőalapú pillanatfelvétel hivatkozik a felhőben tárolt kötet adatok biztonsági mentését. Helyi pillanatképeket gyorsabb hozzáférést biztosít, mivel felhőalapú pillanatfelvételek az adatrugalmasság közül választ.
* **Által kezdeményezett** – a biztonsági mentések kezdeményezhetők automatikusan ütemezés szerint vagy manuálisan is. (Használhatja a biztonsági mentési házirend ütemezett biztonsági mentéseket. Másik lehetőségként használhatja a **biztonsági másolatok készítéséhez** egy interaktív biztonsági mentést a lehetőség.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple-kötet visszaállítása egy biztonsági másolatból
Használhatja a **biztonságimásolat-katalógus** a StorSimple-kötet biztonsági másolatból történő visszaállítását egy adott oldal. Tartsa szem előtt, azonban, a kötet visszaállítása helyreállítja a kötet állapotát, mint amikor a biztonsági mentés készült. A biztonsági mentési művelet után hozzáadott bármely adat elvész.

> [!WARNING]
> Egy biztonsági másolatból történő visszaállítását lecseréli a meglévő kötetet a biztonsági mentésből. Ez után a biztonsági mentés készült írt adatok elvesztésével járhat.
> 
> 

### <a name="to-restore-your-volume"></a>A kötet visszaállítása
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** fülre.
   
    ![Biztonságimásolat-katalógus](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Válassza ki a biztonságimásolat-készletet a következőképpen:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   3. Adja meg azt az időtartományt.
   4. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezek a kötetek offline állapotba kell helyezni a gazdagépet és az eszköz visszaállítani őket. A kötetek elérését a a **Kötettárolók** lapon, és kövesse a lépéseket [kötet offline állapotba](storsimple-manage-volumes-u2.md#take-a-volume-offline) offline állapotba.
   
   > [!IMPORTANT]
   > Győződjön meg arról, hogy elvégezte a kötetek offline állapotba a gazdagépen, mielőtt az eszköz a kötetek offline állapotba. Ha a gazdagép nem hajtja végre a kötetek offline, potenciálisan előfordulhat, hogy sérült adatokat.
   > 
   > 
4. Lépjen vissza a **biztonságimásolat-katalógus** lapot, és válasszon egy biztonságimásolat-készlet.
5. Kattintson a **visszaállítása** az oldal alján.
6. Kell megerősítést kérni. Tekintse át a helyreállítási adatokat, és jelölje be a megerősítő jelölőnégyzetet.
   
    ![Jóváhagyás lap](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). A visszaállítási feladat kezdődik. A feladat megtekintéséhez fér hozzá a **feladatok** lap. 
8. A visszaállítás befejezése után ellenőrizheti, hogy a köteteken a biztonsági mentésből kötetek helyett.

![Videó elérhető](./media/storsimple-restore-from-backup-set-u2/Video_icon.png)**Videó elérhető**

Bemutató videó bemutatja, hogyan használhatja a Klónozás és szolgáltatások a StorSimple törölt fájlok visszaállítása, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Ha a visszaállítás sikertelen
Riasztást kapjon, ha a visszaállítási művelet bármilyen okból nem sikerül. Ha ez történik, frissítse a biztonságimásolat-lista annak ellenőrzéséhez, hogy a biztonsági mentés továbbra is érvényes. Ha a biztonsági mentés érvényes, és a felhőből állítja vissza, majd kapcsolódási problémák előfordulhat, hogy okozza a problémát. 

A visszaállítási művelethez a kötet offline állapotba a gazdagépen, és próbálja megismételni a visszaállítási műveletet. A kötet adatait a visszaállítási folyamat során végrehajtott módosításai elvesznek.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [kezelése StorSimple-köteteket](storsimple-manage-volumes-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

