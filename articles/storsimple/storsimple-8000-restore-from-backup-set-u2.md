---
title: Kötet visszaállítása biztonsági mentésből egy StorSimple 8000 sorozaton | Microsoft dokumentumok
description: A storSimple Eszközkezelő szolgáltatás biztonsági másolata katalógus használatával hogyan állítható vissza egy StorSimple-kötet egy biztonságimásolat-készletből.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723390"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>StorSimple-kötet helyreállítása biztonságimásolat-készletből

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a StorSimple 8000 sorozatú eszközön egy meglévő biztonságimásolat-készlet használatával végrehajtott visszaállítási műveletet ismerteti. A **Biztonsági másolat katalógus** panel segítségével visszaállíthatja a kötetet egy helyi vagy felhőbeli biztonsági mentésből. A **Biztonsági másolat katalógus** panel megjeleníti az összes biztonsági mentési készletek, amelyek akkor jönnek létre, amikor a manuális vagy automatikus biztonsági mentések készülnek. A biztonságimásolat-készletből történő visszaállítási művelet azonnal online állapotba hozza a kötetet, miközben az adatok a háttérben töltődnek le.

A visszaállítás elindításának másik módja az **Eszközök > [Az eszköz] > kötetek**. A **Kötetek** panelen jelöljön ki egy kötetet, kattintson a jobb gombbal a helyi menü meghívásához, majd válassza a **Visszaállítás parancsot.**

## <a name="before-you-restore"></a>A visszaállítás előtt

A visszaállítás megkezdése előtt tekintse át a következő kikötéseket:

* A visszaállítási művelet megkezdése előtt **offline állapotba kell helyeznie a kötetet** – A kötetet mind a gazdagépen, mind az eszközön offline állapotba kell helyeznie. Bár a visszaállítási művelet automatikusan online állapotba hozza a kötetet az eszközön, manuálisan kell online állapotba hoznia az eszközt a gazdagépen. A kötetet online állapotba hozhatja a gazdagépen, amint a kötet online állapotba kerül az eszközön. (Nem kell megvárnia, amíg a visszaállítási művelet befejeződik.) Az eljárásokért nyissa meg [a Kötet offline állapotba helyezése (Kötet offline állapotba helyezése) (Kötet offline állapotba helyezése) (Kötet offline állapotba helyezése)](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)

* **Kötettípus visszaállítás után** – A törölt kötetek visszaállítása a pillanatképben megadott típus alapján; ez azt, hogy a helyileg rögzített kötetek visszaállítása helyileg rögzített kötetek és a rétegzett kötetek rétegzett kötetek visszaállítása rétegzett kötetekként lesz visszaállítva.

    Meglévő kötetek esetén a kötet aktuális használati típusa felülbírálja a pillanatképben tárolt típust. Ha például egy kötetet egy olyan pillanatképből állít vissza, amely a kötettípus rétegzett rétegezése kor készült, és amely a kötettípus most helyileg rögzített (egy végrehajtott konverziós művelet miatt), akkor a kötet helyileg rögzített kötetként lesz visszaállítva. Hasonlóképpen, ha egy meglévő helyileg rögzített kötet ki lett bontva, és ezt követően visszaállították egy régebbi pillanatképből, amely et a kötet kisebb volt, a visszaállított kötet megtartja az aktuális kibontott méretet.

    A kötet nem konvertálható rétegzett kötetből helyileg rögzített kötetté vagy helyileg rögzített kötetből rétegzett kötetre a kötet visszaállítása közben. Várjon, amíg a visszaállítási művelet befejeződik, majd a kötetet más típusra konvertálhatja. A kötetek konvertálásával kapcsolatban a Kötettípus módosítása című részről a [>, a Kötettípus módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)című részre talál. 

* **A kötet mérete tükröződik a visszaállított kötet** – Ez fontos szempont, ha egy helyben rögzített kötet, amely törölt (mert a helyileg rögzített kötetek teljesen kiépített). Győződjön meg arról, hogy elegendő hely áll rendelkezésre, mielőtt megpróbálna visszaállítani egy korábban törölt, helyileg rögzített kötetet.

* **A kötet visszaállítása közben nem bontható ki** – A kötet kibontása előtt várja meg, amíg a visszaállítási művelet befejeződik. A kötetek bővítéséről a [Kötet módosítása](storsimple-8000-manage-volumes-u2.md#modify-a-volume)című részről a Módosítás című részről talál.

* **A biztonsági mentést a helyi kötet visszaállítása közben is elvégezheti** – Az eljárásokhoz az [AStorSimple Eszközkezelő szolgáltatás használata biztonsági mentési házirendek kezelésére](storsimple-8000-manage-backup-policies-u2.md)című részre léphet.

* **A visszaállítási művelet megszakítása** – Ha megszakítja a visszaállítási feladatot, akkor a kötet visszaáll a visszaállítási művelet megkezdése előtti állapotba. Az eljárásokért nyissa meg [a Feladat törlése (Feladat törlése) (Feladat törlése) (Feladat törlése) (Feladat törlése) ( Feladat törlése) lehetőség](storsimple-8000-manage-jobs-u2.md#cancel-a-job)

## <a name="how-does-restore-work"></a>Hogyan működik a visszaállítás?

A 4-es vagy újabb frissítést futtató eszközök esetében egy hőtérkép-alapú visszaállítás van megvalósítva. Ahogy az állomás kéri az adatok eléréséhez az eszköz eléréséhez, ezeket a kérelmeket nyomon követi, és egy hőtérkép jön létre. A magas kérelemsebesség nagyobb hővel rendelkező adattömböket eredményez, míg az alacsonyabb kérelemsebesség alacsonyabb hővel rendelkező adattömbökké válik. Legalább kétszer kell hozzáférnie az adatokhoz ahhoz, hogy forróként legyen _megjelölve._ A módosított fájlok is _forróként_vannak megjelölve. Miután elindította a visszaállítást, az adatok proaktív hidratálása a hőtérkép alapján történik. A 4.

A heatmap-alapú visszaállításokra a következő kikötések vonatkoznak:

* A hőtérkép-követés csak rétegzett kötetek esetén engedélyezett, és a helyileg rögzített kötetek nem támogatottak.

* A heatmap-alapú visszaállítás nem támogatott, ha egy kötetet másik eszközre állít. 

* Ha van egy helyi visszaállítás, és a helyi pillanatkép a kötet vissza kell állítani létezik az eszközön, akkor nem rehidratálja (az adatok már elérhető helyileg). 

* Alapértelmezés szerint visszaállításkor a rehidratálási feladatok kezdeményeződnek, amelyek proaktív módon rehidratálják az adatokat a hőtérkép alapján. 

A 4-es frissítésben a Windows PowerShell-parancsmagok segítségével lekérdezheti a rehidratálási feladatokat, megszakíthatja a rehidratálási feladatot, és lekaphatja a rehidratálási feladat állapotát.

* `Get-HcsRehydrationJob`- Ez a parancsmag megkapja a rehidratációs feladat állapotát. Egyetlen rehidratálási feladat egy kötethez aktiválódik.

* `Set-HcsRehydrationJob`- Ez a parancsmag lehetővé teszi a rehidratálási feladat szüneteltetését, leállítását, folytatását, amikor a rehidratálás folyamatban van.

A rehidratálási parancsmagokról a [Windows PowerShell-parancsmag storSimple-hez való hivatkozási at.](https://technet.microsoft.com/library/dn688168.aspx)

Automatikus rehidratálás, általában magasabb átmeneti olvasási teljesítmény várható. A fejlesztések tényleges nagysága különböző tényezőktől függ, például a hozzáférési mintát, az adatforgalomtól és az adattípustól. 

A rehidratálási feladat megszakításához használhatja a PowerShell-parancsmag. Ha véglegesen le szeretné tiltani a rehidratálási feladatokat az összes jövőbeli visszaállításhoz, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)

## <a name="how-to-use-the-backup-catalog"></a>A biztonsági másolat katalógusának használata

A **Biztonsági másolat katalógus** panel egy lekérdezést biztosít, amely segít a biztonságimásolat-készlet kiválasztásának szűkítése. A beolvasott biztonságimásolat-készletek et a következő paraméterek alapján szűrheti:

* **Időtartomány** – A biztonsági másolat készlet létrehozásának dátum- és időtartománya.
* **Eszköz** – Az az eszköz, amelyen a biztonsági másolat készlet készült.
* **Biztonsági mentési házirend** vagy **kötet** – A biztonsági mentési házirend vagy a biztonsági másolathoz társított kötet.

A szűrt biztonsági másolat-készletek táblázatba foglalása a következő attribútumok alapján lesznek táblázatba foglalva:

* **Név** – A biztonsági másolat házirendjének vagy kötetének neve a biztonságimásolat-készlethez társítva.
* **Típus** – Biztonsági másolat készletek lehetnek helyi pillanatképek vagy felhőbeli pillanatképek. A helyi pillanatkép az eszközön helyileg tárolt összes kötetadat biztonsági másolata, míg a felhőbeli pillanatkép a felhőben található kötetadatok biztonsági mentésére vonatkozik. A helyi pillanatképek gyorsabb hozzáférést biztosítanak, míg a felhőbeli pillanatképek az adatok rugalmasságához vannak kiválasztva.
* **Méret** – A biztonságimásolat-készlet tényleges mérete.
* **Létrehozva** – A biztonsági mentések létrehozásának dátuma és időpontja. 
* **Kötetek** – A biztonságimásolat-készlethez társított kötetek száma.
* **Kezdeményezett** – A biztonsági mentések automatikusan kezdeményezhetők ütemezés szerint vagy manuálisan a felhasználó által. (Biztonsági mentési házirend del ütemezheti a biztonsági mentéseket. Másik lehetőségként használhatja a **Biztonsági mentés készítése** lehetőséget interaktív vagy igény szerinti biztonsági mentés készítéséhez.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple kötet visszaállítása biztonsági másolatból

A Katalógus **biztonsági mentése** panel segítségével visszaállíthatja a StorSimple kötet egy adott biztonsági mentés. Ne feledje azonban, hogy a kötet visszaállítása visszaállítja a kötetet arra az állapotra, amelyben a biztonsági mentés készítéseke során volt. A biztonsági mentési művelet után hozzáadott adatok elvesznek.

> [!WARNING]
> A biztonsági másolatból történő visszaállítás felülírja a meglévő köteteket a biztonsági mentésből. Ez a biztonsági mentés után írt adatok elvesztését okozhatja.


### <a name="to-restore-your-volume"></a>A kötet visszaállítása
1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Katalógus biztonsági másolata**gombra.

2. Válasszon ki egy biztonságimentési készletet az alábbiak szerint:
   
   1. Adja meg az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. A legördülő listában válassza ki a kijelölni kívánt biztonsági másolat kötet- vagy biztonsági mentési házirendet.
   4. A lekérdezés végrehajtásához kattintson az **Alkalmaz** gombra.

      A kijelölt kötethez vagy biztonsági mentési házirendhez társított biztonsági másolatnak meg kell jelennie a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet lista](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezeket a köteteket a visszaállítás előtt offline állapotba kell helyezni a gazdagépen és az eszközön. Férjen hozzá az eszköz Kötetek paneljén lévő **kötetekhez,** majd kövesse a [Kötet offline állapotba helyezése](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) című rész lépéseit, és offline állapotba hozhatja őket.
   
   > [!IMPORTANT]
   > Mielőtt a köteteket offline állapotba helyezte volna az eszközön, győződjön meg arról, hogy a köteteket offline állapotba helyezte az állomáson. Ha nem helyezi offline állapotba a köteteket az állomáson, az adatsérüléshez vezethet.
   
4. Lépjen vissza a **Katalógus biztonsági másolata** lapra, és jelöljön ki egy biztonságimásolat-készletet. Kattintson a jobb gombbal, majd a helyi menüben válassza a **Visszaállítás parancsot.**

    ![Biztonságimásolat-készlet lista](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. A rendszer a művelet megerősítését kéri. Tekintse át a visszaállítási adatokat, majd jelölje be a megerősítést kérő jelölőnégyzetet.
   
    ![Megerősítő oldal](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kattintson **a Visszaállítás gombra.** Ezzel olyan visszaállítási feladatot kezdeményez, amelyet a **Feladatok** lap elérésével tekinthet meg.

   ![Megerősítő oldal](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. A visszaállítás befejezése után ellenőrizze, hogy a kötetek tartalmát a biztonsági másolatkötetek cserélik-e.


## <a name="if-the-restore-fails"></a>Ha a visszaállítás sikertelen

Riasztást fog kapni, ha a visszaállítási művelet bármilyen okból sikertelen. Ebben az esetben frissítse a biztonsági másolat listát, és ellenőrizze, hogy a biztonsági másolat továbbra is érvényes-e. Ha a biztonsági mentés érvényes, és a felhőből állítja vissza a szolgáltatást, akkor a kapcsolódási problémák okozhatják a problémát.

A visszaállítási művelet befejezéséhez állítsa offline állapotba a kötetet az állomáson, majd próbálkozzon újra a visszaállítási művelettel. Vegye figyelembe, hogy a visszaállítási folyamat során végrehajtott kötetadatok módosításai elvesznek.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple kötetek kezeléséről.](storsimple-8000-manage-volumes-u2.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

