---
title: "Állítsa vissza egy kötetet biztonsági másolatból a StorSimple 8000 Series |} Microsoft Docs"
description: "A StorSimple Device Manager szolgáltatás biztonságimásolat-katalógus a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet használatát ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>A StorSimple-kötet visszaállítása egy biztonságimásolat-készlet

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja a visszaállítási műveletet végrehajtani a StorSimple 8000 series eszközt egy meglévő biztonságimásolat-készletből. Használja a **biztonságimásolat-katalógus** panel visszaállítási helyi kötet vagy a felhőbeli biztonsági mentését. A **biztonságimásolat-katalógus** csempe megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. A biztonságimásolat-készletből való visszaállítás során a kötetet az azonnal, amíg az adatok letöltődik a háttérben.

Alternatív módszert elindítani a visszaállítási, hogy lépjen **eszközök > [az eszköz] > kötetek**. Az a **kötetek** panelen válasszon ki egy kötetet, kattintson a jobb gombbal el a helyi menüt, és válassza ki a **visszaállítása**.

## <a name="before-you-restore"></a>Mielőtt vissza tudná állítani

A helyreállítás megkezdése előtt tekintse át az alábbi korlátozásokkal:

* **A kötet kell helyezze offline állapotba** – a kötet offline állapotba a gazdagép és az eszköz is igénybe vehet, még a visszaállítás elindítása előtt. Bár a visszaállítási művelet automatikusan kiválasztja a kötetet az eszközön, manuálisan kell hoznia az eszköz online a gazdagépen. Helyezheti a kötetet a gazdagépen, amint a kötet az eszköz online állapotban. (Nem kell várnia, amíg a visszaállítási művelet nem fejeződött.) Az eljárások, nyissa meg a [kötet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Visszaállítás után kötettípus** – törölt kötetek visszaállítása a pillanatkép típusa alapján; ez azt jelenti, hogy volt helyileg rögzített kötetek visszaállítását végzi el a helyileg rögzített kötetekhez, és volt rétegzett kötetek visszaállítását végzi el a rétegzett kötetek.

    Meglévő kötetek a kötet aktuális használati típusú felülírja a pillanatkép tárolt típusát. Például ha egy köteten, ha a kötet típusa történt rétegzett, és (miatt egy átalakítási műveletet végeznie) mostantól helyileg rögzített kötet típusa történt egy pillanatképből, majd a kötet visszaállítja egy helyileg rögzített kötet. Hasonló módon ha meglévő helyileg rögzített kötet kibontva, ezt követően a hajt végre, amikor kisebb volt a kötet egy korábbi pillanatképből állítottak vissza, a helyreállított kötet megőrzik az aktuális kiterjesztett méretét.

    Nem konvertálható a kötet egy helyileg rögzített kötet rétegzett kötet, illetve a rétegzett kötetek helyileg rögzített kötet közben a kötet visszaállítása folyamatban van. Várjon, amíg a visszaállítás befejeződött, majd egy másik átválthat a kötetet. A kötet konvertálása kapcsolatos információkért látogasson el [kötet típusának módosítása](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **A kötet mérete megjelenik a visszaállított kötet** – Ez akkor fontos szempont, ha egy helyileg rögzített kötet (mert helyileg rögzített kötetekhez teljesen kiépített) törölt visszaállítását végzi. Győződjön meg arról, hogy van elegendő hely, mielőtt megkísérli visszaállítani egy korábban törölt helyileg rögzített kötet.

* **A kötet nem terjeszthető ki, miközben visszaállítása folyamatban van** – Várjon, amíg a visszaállítási művelet befejezése előtt bontsa ki a kötetet. A kötet kiterjesztését kapcsolatos információkért látogasson el [kötet módosítása](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Végezheti el a biztonsági mentés során egy kötet visszaállítása** – az eljárások Ugrás [a StorSimple Device Manager szolgáltatás segítségével kezelheti a biztonsági mentési házirendeket](storsimple-8000-manage-backup-policies-u2.md).

* **A visszaállítási művelet megszakíthatja** – Ha a kötet vissza lesz állítva a visszaállítási műveletet kezdeményezett előtti állapotba, majd szakítsa meg a visszaállítási feladat. Az eljárások, nyissa meg a [feladatok megszakítása](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hogyan állítsa vissza a munkahelyi

4 vagy újabb frissítés rendszerű eszközöket a heatmap-alapú visszaállítási van megvalósítva. Mivel a gazdagép kéri, hogy érheti el az adatait elérni az eszközön, ezeket a kérelmeket a rendszer nyomon, és egy heatmap jön létre. Magas kérelmek aránya magasabb tűz az adattömbök eredményez, mivel alacsonyabb kérelmek aránya az eszköz az alsó tűz adattömböket. Az adatok legalább kétszer állapotúként kell megjelölni a kell elérnie _működés közbeni_. A fájl módosított is van megjelölve _működés közbeni_. Miután elindította a visszaállítás, az adatok proaktív hidratálásával történik a heatmap alapján. Verziók Update 4-nél korábbi adatok letöltődik alapuló hozzáférés csak visszaállítás során.

Az alábbi korlátozásokkal heatmap alapú visszaállítások vonatkoznak:

* Csak a rétegzett kötetek engedélyezve van a változáskövetés Heatmap, és helyileg rögzített kötetek nem támogatottak.

* Heatmap-alapú visszaállítási nem támogatott, ha egy másik eszközre kötet klónozása. 

* Ha egy helyi visszaállítási, és egy helyi pillanatfelvétel állítani a kötet létezik-e az eszközön, majd azt nem rehydrate (mivel az adatok már helyileg nem érhető el). 

* Alapértelmezés szerint történő visszaállításához, a rendszer kezdeményezett a rehidratálása feladatok proaktív rehydrate az adatokat a heatmap alapján. 

Az Update 4, Windows PowerShell-parancsmagok használhatók futó rehidratálása feladat lekérdezése rehidratálása feladatok megszakítása, és a rehidratálása feladat állapotának beolvasása.

* `Get-HcsRehydrationJob`– Ez a parancsmag a rehidratálása feladat állapotának beolvasása. Egy kötet egy egyetlen rehidratálása feladat elindul.

* `Set-HcsRehydrationJob`– Ez a parancsmag lehetővé teszi szüneteltetése, állítsa le, a rehidratálása feladat folytatódik, amint a rehidratálása folyamatban van.

Rehidratálása parancsmagok további információkért látogasson el [Windows PowerShell parancsmag-referencia a StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Az automatikus rehdyration általában magasabb átmeneti olvasási teljesítmény várható. A tényleges magniutde fejlesztései számos tényező befolyásolja, például a hozzáférési mintázatát, adatmennyiség és adattípus függ. 

Szakítsa meg a rehidratálása feladatot, a PowerShell-parancsmagot is használhat. Ha végleg letiltja az összes jövőbeli visszaállítások rehidratálása feladataihoz [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>A biztonsági mentési katalógus használata

A **biztonságimásolat-katalógus** panel biztosít egy lekérdezést, amely segít a megadásával szűkíthető, a biztonsági másolat beállítása. A biztonságimásolat-készletek, amelyek a rendszer beolvassa a következő paraméterek alapján szűrheti:

* **Időtartománynak** – a dátum és idő tartomány, a biztonságimásolat-készlet létrehozásakor.
* **Eszköz** – az eszköz, amelyre a biztonságimásolat-készlet létrehozása.
* **Biztonsági mentési házirend** vagy **kötet** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társítva.

A szűrt biztonsági mentés majd megjelennének a következő attribútumok alapján:

* **Név** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Típus** – biztonsági másolat beállítása helyi pillanatképeket és felhőalapú pillanatfelvételek. Egy helyi pillanatfelvétel minden kötet adatait az eszközön, helyileg tárolt biztonsági másolatot, mivel egy felhőalapú pillanatfelvétel hivatkozik a felhőben tárolt kötet adatok biztonsági mentését. Helyi pillanatképeket gyorsabb hozzáférést biztosít, mivel felhőalapú pillanatfelvételek az adatrugalmasság közül választ.
* **Méret** – a biztonságimásolat-készlet tényleges méretének.
* **A létrehozott** – a dátum és idő, amikor a biztonsági mentés készült. 
* **Kötetek** -a biztonságimásolat-készlet társított kötetek számát.
* **Kezdeményezett** – a biztonsági mentések kezdeményezheti automatikusan ütemezés szerint vagy manuálisan egy felhasználó által. (Használhatja a biztonsági mentési házirend ütemezett biztonsági mentéseket. Másik lehetőségként használhatja a **biztonsági másolatok készítéséhez** egy interaktív vagy igény szerinti biztonsági mentést a lehetőség.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple-kötet visszaállítása egy biztonsági másolatból

Használhatja a **biztonságimásolat-katalógus** panelt, és a StorSimple-kötet visszaállítása egy adott biztonsági másolatból. Tartsa azonban szem előtt, kötet visszaállítása visszaállítja a kötet állapotát, mint amikor a biztonsági mentés készült. Bármely vettek fel a biztonsági mentési művelet után adatok elvesznek.

> [!WARNING]
> Egy biztonsági másolatból történő visszaállítását a rendszer lecseréli a meglévő kötetet a biztonsági mentésből. Ez után a biztonsági mentés készült írt adatok elvesztésével járhat.


### <a name="to-restore-your-volume"></a>A kötet visszaállítása
1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és kattintson a **biztonságimásolat-katalógus**.

2. Válassza ki a biztonságimásolat-készletet a következőképpen:
   
   1. Adja meg azt az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.

    A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
   
    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezek a kötetek offline állapotba kell helyezni a gazdagépet és az eszköz visszaállítani őket. A kötetek elérését a a **kötetek** panelen található az eszközre, majd kövesse a lépéseket [kötet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) offline állapotba.
   
   > [!IMPORTANT]
   > Győződjön meg arról, hogy elvégezte a kötetek offline állapotba a gazdagépen, mielőtt az eszköz a kötetek offline állapotba. Ha a gazdagép nem hajtja végre a kötetek offline, potenciálisan előfordulhat, hogy sérült adatokat.
   
4. Lépjen vissza a **biztonságimásolat-katalógus** lapot, és válasszon egy biztonságimásolat-készlet. Kattintson a jobb gombbal, és a helyi menüből válassza **visszaállítása**.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Megerősítést kér bekéri. Tekintse át a helyreállítási adatokat, és jelölje be a megerősítő jelölőnégyzetet.
   
    ![Jóváhagyás lap](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Kattintson a **visszaállítása**. Ez elindít egy visszaállítási feladatot, amely elérésével megtekintheti a **feladatok** lap.

    ![Jóváhagyás lap](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. A visszaállítás befejezése után ellenőrizze, hogy a köteteken a biztonsági mentésből kötetek helyett.


## <a name="if-the-restore-fails"></a>Ha a visszaállítás sikertelen

A visszaállítási művelet bármilyen okból nem sikerül riasztást kap. Ha ez történik, frissítse a biztonságimásolat-lista annak ellenőrzéséhez, hogy a biztonsági mentés továbbra is érvényes. Ha a biztonsági mentés érvényes, és a felhőből állítja vissza, majd kapcsolódási problémák előfordulhat, hogy okozza a problémát.

A visszaállítási művelethez a kötet offline állapotba a gazdagépen, és próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a kötet adatait a visszaállítási folyamat során végrehajtott módosításai elvesznek.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [kezelése StorSimple-köteteket](storsimple-8000-manage-volumes-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

