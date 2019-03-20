---
title: A kötet visszaállítása biztonsági másolatból egy StorSimple 8000 Series |} A Microsoft Docs
description: Ismerteti a StorSimple-kötet visszaállítása a biztonságimásolat-készlet a StorSimple-Eszközkezelő szolgáltatás biztonságimásolat-katalógus használatával.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082806"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>StorSimple-kötet visszaállítása a biztonságimásolat-készlet

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja a visszaállítási műveletet végrehajtani a StorSimple 8000 sorozatú eszköz egy meglévő biztonságimásolat-készlet használatával. Használja a **biztonságimásolat-katalógus** visszaállítási egy kötet egy helyi vagy felhőbeli biztonsági mentés panelen. A **biztonságimásolat-katalógus** panel jeleníti meg az összes biztonságimásolat-készletek, amelyek jönnek létre, ha a manuális vagy automatikus biztonsági másolatokat készít. A visszaállítási művelet biztonságimásolat-készlet számos lehetőséget kínál a kötet online állapotú, azonnal, miközben az adatok letöltése van a háttérben.

A helyreállítás megkezdéséhez alternatív módszert, hogy lépjen **eszközök > [az eszköz] > kötetek**. Az a **kötetek** panelen válassza ki azt a kötetet, kattintson a jobb gombbal indítja el a helyi menüt, és válassza ki **visszaállítása**.

## <a name="before-you-restore"></a>Mielőtt vissza tudná állítani

A helyreállítás megkezdése előtt tekintse át az alábbi korlátozásokkal:

* **A kötet kell offline állapotba** – a kötet offline a gazdagép és az eszközt is igénybe vehet, a visszaállítási művelet elindítása előtt. Bár a visszaállítási művelet automatikusan kezelni a kötet online állapotú, az eszközön, manuálisan kell csatlakoztassa az eszközt a gazdagépen. Amint a kötetet az eszköz online állapotban, akkor a kötet online elérhetővé tenni a gazdagépen. (Nem kell várnia, amíg a visszaállítás befejeződött.) Az eljárások ismertetéséért Ugrás [egy kötetet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Visszaállítás után kötettípus** – törölt kötetek visszaállítása a pillanatkép típusa alapján; amelyek helyileg rögzített kötetek visszaállítását végzi a gyors helyi kötetek és rétegzett kötetek is rétegzett kötetek visszaállítását végzi.

    A meglévő köteteket az aktuális használati típus, a kötet felülbírálja a pillanatképben tárolt típusát. Például ha került sor, ha a kötet típusának történt rétegzett, és (miatt végrehajtott konverzió művelet) most már helyileg rögzített kötet típusa pillanatképből visszaállítani egy kötetet, majd a kötet lesz visszaállítva, egy helyileg rögzített kötet. Hasonlóképpen ha meglévő helyileg rögzített kötet volt kibontva, és ezt követően visszaállítása egy korábbi pillanatképből elvégezni, ha a kötet kisebb volt, a helyreállított kötet megőrzi a jelenlegi kibontott mérete.

    Nem konvertálható a kötet egy helyileg rögzített kötet a rétegzett kötet vagy egy helyileg rögzített kötet a rétegzett kötetek közben a kötet kerül. Várjon, amíg a visszaállítás befejeződött, majd alakíthatja át a kötet egy másik típusa. A kötet konvertálása kapcsolatos információkért lépjen [a kötet típusának módosításához](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **A kötet méretét a helyreállított kötet színnel** – Ez egy fontos szempont az, ha a visszaállítani kívánt törölt (mivel a gyors helyi kötetek teljes felhasznált) helyileg rögzített kötetet. Győződjön meg arról, hogy mielőtt megkísérli visszaállítani egy korábban törölt helyileg rögzített kötet áll rendelkezésre elegendő lemezterület.

* **Nem terjeszthet ki egy kötetet, amíg visszaállítása folyamatban van** – várja meg a visszaállítási művelet befejeződik, mielőtt megkísérli a kötet bontsa ki. Egy kötet bővítésében kapcsolatos információkért lépjen [a kötet módosításának](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Biztonsági másolat hajthat végre, amíg egy helyi kötet visszaállítása** – az eljárások Ugrás [biztonsági mentési házirendek kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manage-backup-policies-u2.md).

* **A visszaállítási művelet megszakíthatja** – Ha a visszaállítási feladat megszakítása, akkor a kötet vissza lesz állítva, hogy kezdeményezte a visszaállítás előtti állapotra. Az eljárások ismertetéséért Ugrás [feladatok megszakítása](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hogyan munkahelyi visszaállítása

A 4-es vagy újabb frissítést futtató eszközök esetében a intenzitástérkép-alapú helyreállítás van megvalósítva. Ahogy arra kéri a gazdagép érheti el adatait az eszköz eléréséhez, ezeket a kérelmeket a rendszer nyomon követi, és egy intenzitástérkép jön létre. Magas kérelmek arányának magasabb megadott hőtérképrészlet az adattömbök eredményez, mivel alacsonyabb kérelmek arányának a rendszer lefordítja arra az alacsonyabb megadott hőtérképrészlet adattömböket. Az adatok legalább kétszer kell elérnie megjelölni _gyakori elérésű_. Módosított egy fájlt is van megjelölve _gyakori elérésű_. Miután elindította a visszaállítás, az adatok proaktív hidratálási történik az intenzitástérkép alapján. Verziók Update 4-nél régebbi az adatokat a rendszer letölti alapuló hozzáférés csak visszaállítás során.

Az alábbi korlátozásokkal intenzitástérkép-alapú visszaállítások vonatkoznak:

* Intenzitástérkép engedélyezve van a rétegzett kötetek esetében csak, és a gyors helyi kötetek nem támogatottak.

* Intenzitástérkép-alapú helyreállítás nem támogatott, ha egy másik eszközre kötet klónozása. 

* Ha egy helyi visszaállítási, és vissza kell állítani a kötet helyi pillanatkép létezik-e az eszközön, majd azt nem rehidratálási (mivel az adatok már elérhető helyileg). 

* Alapértelmezés szerint állítja vissza, ha a rehidratálás feladatok kezdeményezett, amely proaktív módon rehidratálási az intenzitástérkép alapján. 

Update 4, a Windows PowerShell-parancsmagok rehidratálás futó feladatok lekérdezése, rehidratálás feladatok megszakítása és a rehidratálás feladat állapotának lekérése is használható.

* `Get-HcsRehydrationJob` – Ez a parancsmag beolvassa a rehidratálás feladat állapotát. Egy kötet egy egyetlen rehidratálás feladat elindul.

* `Set-HcsRehydrationJob` – Ez a parancsmag lehetővé teszi a szüneteltetés, állítsa le, a rehidratálás feladat folytatása folyamatban van a rehidratálás során.

Parancsmagok rehidratálása további információért látogasson el [storsimple-höz készült Windows PowerShell-parancsmagok leírása](https://technet.microsoft.com/library/dn688168.aspx).

Az automatikus rehidratálás általában magasabb átmeneti olvasási teljesítmény várható. Fejlesztések tényleges mértékétől függ számos tényező befolyásolja, például a hozzáférési mintájában, adatváltozás és adattípust. 

A rehidratálás feladat megszakítása, használhatja a PowerShell-parancsmagot. Ha véglegesen letiltja az összes jövőbeli visszaállítás érdekében rehidratálás feladatok [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>A biztonságimásolat-katalógus használata

A **biztonságimásolat-katalógus** panel biztosít egy lekérdezést, amely segítséget nyújt a biztonsági mentés szűkítéséhez beállítása. A biztonságimásolat-készleteket, hogy olvassa be a következő paraméterek alapján szűrheti:

* **Időtartomány** – a dátum- és időtartományt a biztonságimásolat-készlet létrehozásakor.
* **Eszköz** – az eszköz, amelyen a biztonságimásolat-készletet létrehozták.
* **Biztonsági mentési szabályzat** vagy **kötet** – a biztonsági mentési szabályzat vagy a kötet a biztonságimásolat-készlet társítva.

A szűrt biztonságimásolat-készleteket majd megjelennének attribútumok közül a következők alapján:

* **Név** – a biztonsági mentési szabályzat vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Típus** – Backup sets lehet a helyi pillanatképek vagy felhőalapú pillanatfelvételek. Helyi pillanatképet az összes kötet az eszközön, a helyileg tárolt biztonsági, mivel a felhőbeli pillanatképet a biztonsági mentés, a felhőben levő kötetadatokról hivatkozik. A helyi pillanatképeket gyorsabb engedélyt biztosít, mivel a felhőbeli pillanatképekkel adatrugalmasság közül választ.
* **Méret** – a biztonságimásolat-készlet aktuális méretének.
* **A létrehozott** – a dátum és idő, amikor a biztonsági mentések létrejöttek. 
* **Kötetek** – a biztonságimásolat-készlet társított kötetek számát.
* **Kezdeményezett** – a biztonsági mentések kezdeményezhetők automatikusan egy ütemezés szerint vagy manuálisan felhasználója. (Használhatja egy biztonsági mentési szabályzatot a biztonsági mentések ütemezéséhez. Másik lehetőségként használhatja a **biztonsági mentés** egy interaktív vagy igény szerinti biztonsági mentés lehetőséget.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple-kötet visszaállítása egy biztonsági másolatból

Használhatja a **biztonságimásolat-katalógus** a StorSimple-kötet biztonsági másolatból történő visszaállítását egy adott panelen. Azonban tartsa szem előtt, visszaállítani egy kötetet visszaáll a kötetet a állapotba, amelyben a biztonsági mentés időpontjában volt. Bármilyen adatokat vettek fel a biztonsági mentési művelet után el fog veszni.

> [!WARNING]
> Egy biztonsági másolatból történő visszaállítását a rendszer lecseréli a meglévő kötetek a biztonsági mentésből. Emiatt előfordulhat, hogy az adatvesztés minden arról, hogy miután a biztonsági mentéshez.


### <a name="to-restore-your-volume"></a>A kötet visszaállítása
1. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, és kattintson a **biztonságimásolat-katalógus**.

2. Válassza ki a biztonsági csoportot az alábbiak szerint:
   
   1. Megadhatja az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. A legördülő listában válassza ki a biztonsági mentés, amelyet szeretne, válassza ki a kötet vagy a biztonsági mentési szabályzat.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.

      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési szabályzat meg kell jelennie a biztonságimásolat-készletek listájában.
   
      ![Biztonságimásolat-készlet listája](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezeket a köteteket kell tenni a gazdagép és az eszköz offline állapotú, mielőtt azokat helyreállíthatja. A kötetek elérését a a **kötetek** paneljén látható az eszközt, és kövesse a lépéseket a [egy kötetet offline állapotba](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) offline állapotba.
   
   > [!IMPORTANT]
   > Győződjön meg arról, hogy elvégezte a kötetek offline a gazdagépen, mielőtt az eszköz a kötetek offline. A gazdagép nem lépnek a kötetek offline állapotba, ha azt vezethet az adatok sérülésének.
   
4. Lépjen vissza a **biztonságimásolat-katalógus** lapra, és válassza ki a biztonságimásolat-készletből. Kattintson a jobb gombbal, és a helyi menüből válassza ki **visszaállítása**.

    ![Biztonságimásolat-készlet listája](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Meg kell adnia megerősítést. Tekintse át a visszaállítási adatokat, és válassza ki a megerősítő jelölőnégyzetet.
   
    ![Megerősítő oldal](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kattintson a **visszaállítása**. Ez elindít egy visszaállítási feladat, amely elérésével megtekintheti a **feladatok** lapot.

   ![Megerősítő oldal](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. A visszaállítás befejezése után ellenőrizze, hogy a köteteken helyébe kötetek a biztonsági mentésből.


## <a name="if-the-restore-fails"></a>Ha a visszaállítás sikertelen

Riasztást fog kapni, ha valamilyen okból meghiúsul, a visszaállítási műveletet. Ha ez történik, frissítse a biztonsági mentési listában ellenőrizze, hogy a biztonsági másolat még érvényes. Ha a biztonsági másolat érvényes, és a felhőből állítja vissza, majd kapcsolódási problémák előfordulhat, hogy okozza a problémát.

A visszaállítási művelethez a kötet offline állapotba a gazdagépen, és próbálja megismételni a visszaállítási műveletet. Vegye figyelembe, hogy a kötet adatait a visszaállítás során végzett módosításai elvesznek.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelése a StorSimple-kötetek](storsimple-8000-manage-volumes-u2.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

