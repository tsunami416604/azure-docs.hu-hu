---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cce78fb6a5472a625e973bb8ca4afdfd6290a26c
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164268"
---
<!--author=alkohli last changed: 07/19/2017-->

#### <a name="to-create-a-volume"></a>Kötet létrehozása
1. Az **Eszközök** panelen szereplő eszközök táblázatos listájából válassza ki az eszközt. Kattintson a **+ Kötet hozzáadása** gombra.

    ![Új kötet hozzáadása](./media/storsimple-8000-create-volume-u2/step5createvol1.png)

2. A **Kötet hozzáadása** panelen:
   
   1. Az **Eszköz kiválasztása** mezőt a rendszer automatikusan kitölti a jelenlegi eszközzel.

   2. A legördülő listából válassza ki a kötettárolót, amelyhez kötetet szeretne adni. 

   3.  Adja meg a kötet **Nevét**. A kötetek nem nevezhetők át a létrehozásuk után.

   4. A legördülő listából adja meg a **Típus** beállítást a kötethez. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **Helyileg rögzített** kötetet. Minden más adathoz válasszon **Rétegzett** kötetet. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** beállítást.
      
       A rétegzett kötetek kiosztása dinamikus, ezért gyorsan létrehozhatók. Archív adatokhoz használt rétegzett kötet esetében a **Kötet használata ritkábban használt archív adatokhoz** beállítás bejelölésével 512 KB-ra módosíthatja a kötet deduplikációs adattömbméretét. Ha ez a mező nincs bejelölve, az érintett rétegzett kötet 64 KB-os adattömbméretet használ. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.
       
       Egy helyileg rögzített kötet kiosztása állandó, és biztosítja, hogy a kötet elsődleges adatai helyben tárolódnak az eszközön, és nem kerülnek a felhőbe.  Ha gyors helyi kötetet hoz létre, az eszköz ellenőrzi a helyi rétegeken elérhető tárhelyet a kívánt méretű kötet kiosztásához. A helyileg rögzített kötet létrehozásához hozzátartozhat az eszközön levő adatok felhőbe küldése is, így a kötet létrehozása hosszú időt vehet igénybe. A teljes időtartam a kiosztott kötet méretétől, az elérhető hálózati sávszélességtől és az eszközön lévő adatoktól függ.

   5. Adja meg a kötethez tartozó **Kiosztott kapacitást**. Jegyezze fel a kiválasztott kötettípus alapján elérhető kapacitást. A megadott kötetméret nem haladhatja meg a rendelkezésre álló területet.
      
       A 8100-as eszközön gyors helyi kötetekhez legfeljebb 8,5 TB, rétegzett kötetekhez legfeljebb 200 TB területet oszthat ki. A nagyobb 8600-as eszközön gyors helyi kötetekhez legfeljebb 22,5 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki. A rétegzett kötetek munkakészletének üzemeltetéséhez helyi terület szükséges az eszközön, ezért a gyors helyi kötetek létrehozása hatással van a rétegzett kötetek kiosztásához rendelkezésre álló területre. Ha tehát helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület lecsökken. Ha rétegzett kötetet hoz létre, akkor ehhez hasonlóan a gyors helyi kötetek létrehozásához rendelkezésre álló terület csökken.
      
       Ha a 8100-as eszközön kioszt egy 8,5 TB-os (vagyis a legnagyobb megengedett méretű) gyors helyi kötetet, azzal elfoglalja az összes rendelkezésre álló helyi területet az eszközön. Innentől kezdve nem hozhat létre további rétegzett köteteket, mivel nincs helyi terület az eszközön, amely üzemeltethetné a rétegzett kötet munkakészletét. A meglévő rétegzett kötetek is hatással vannak a rendelkezésre álló területre. Ha például egy 8100-as eszközhöz már tartozik körülbelül 106 TB rétegzett kötet, akkor csak 4 TB érhető el a gyors helyi kötetekhez.

    6. Kattintson a nyílra a **Csatlakoztatott gazdagépek** mezőben. 

        ![Csatlakoztatott gazdagépek](./media/storsimple-8000-create-volume-u2/step5createvol2.png)

    7. A **Csatlakoztatott gazdagépek** panelen válasszon egy meglévő ACR-t vagy adjon meg egy újat a következő lépések végrehajtásával:

       1. Adja meg az ACR **nevét**.
       2. Az **iSCSI-kezdeményező neve** alatt adja meg a Windows-gazdagép iSCSI minősített nevét (IQN). Ha nem tudja az IQN-t, lépjen a [Windows Server-gazdagép IQN nevének lekérése](#get-the-iqn-of-a-windows-server-host) szakaszhoz.

    9. Kattintson a **Create** (Létrehozás) gombra. Létrejön egy kötet a megadott beállításokkal.

        ![Kattintson a Létrehozás gombra](./media/storsimple-8000-create-volume-u2/step5createvol3.png)

        > [!NOTE]
        > Ügyeljen arra, hogy a létrehozott kötet nem védett. Az ütemezett biztonsági mentésekhez biztonsági mentési házirendeket kell létrehoznia, és társítania kell azokat a kötethez. 

