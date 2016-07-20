<!--author=SharS last changed: 02/29/2016-->

#### Kötet létrehozása

1. A **Gyors üzembe helyezés** oldalon kattintson a **Kötet hozzáadása** lehetőségre. Ezzel elindítja a Kötet hozzáadása varázslót.

2. A Kötet hozzáadása varázslóban az **Alapbeállítások** területen:

    4. Adja meg a kötet **Nevét**.
    5. A legördülő listából adja meg a **Használat típusa** beállítást a kötethez. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **Helyileg rögzített** kötetet. Minden más adathoz válasszon **Rétegzett** kötetet. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** beállítást. 
    
        Egy helyileg rögzített kötet kiosztása állandó, és biztosítja, hogy a kötet elsődleges adatai helyben tárolódnak az eszközön, és nem kerülnek a felhőbe.  Ha helyileg rögzített kötetet hoz létre, az eszköz ellenőrzi a helyi rétegeken elérhető tárhelyet a kívánt méretű kötet kiosztásához. A helyileg rögzített kötet létrehozásához hozzátartozhat az eszközön levő adatok felhőbe küldése is, így a kötet létrehozása hosszú időt vehet igénybe. A teljes időtartam a kiosztott kötet méretétől, az elérhető hálózati sávszélességtől és az eszközön lévő adatoktól függ. 

        A rétegzett kötetek kiosztása dinamikus, és ezek nagyon gyorsan létrehozhatók. Ha a rétegzett kötetet archív adatokhoz használja, a **Kötet használata ritkábban használt archív adatokhoz** beállítás bejelölésével a kötet deduplikációs adattömbméretét 512 KB-ra módosíthatja. Ha ez a mező nincs bejelölve, a megfelelő rétegzett kötet 64 KB adattömbméretet használ. Egy nagyobb deduplikációs adattömbméret lehetővé teszi az eszköz számára a nagy mennyiségű archiválási adatok továbbítását a felhőbe.

    3. Adja meg a kötethez tartozó **Kiosztott kapacitást**. Jegyezze fel a kiválasztott kötettípus alapján elérhető kapacitást. A megadott kötetméret nem haladhatja meg a rendelkezésre álló területet.

        A 8100-as eszközön helyileg rögzített kötetekhez legfeljebb 8 TB, rétegzett kötetekhez legfeljebb 200 TB területet oszthat ki. A nagyobb 8600-as eszközön helyileg rögzített kötetekhez legfeljebb 20 TB, rétegzett kötetekhez legfeljebb 500 TB területet oszthat ki. Mivel a rétegzett kötetek munkakészletének üzemeltetéséhez helyi terület szükséges az eszközön, a helyileg rögzített kötetek létrehozása hatással van a rétegzett kötetek kiosztásához rendelkezésre álló területre. Ha tehát helyileg rögzített kötetet hoz létre, a rétegzett kötetek létrehozásához rendelkezésre álló terület csökken. Hasonlóképpen ha rétegzett kötetet hoz létre, a helyileg rögzített kötetek létrehozásához rendelkezésre álló terület csökken. 

        Ha a 8100-as eszközön kioszt egy 8 TB-os (ez a legnagyobb megengedett méret) helyileg rögzített kötetet, azzal elfoglalja az összes rendelkezésre álló helyi területet az eszközön. Innentől kezdve nem hozhat létre további rétegzett köteteket, mivel nincs helyi terület az eszközön, amely üzemeltethetné a rétegzett kötet munkakészletét. A meglévő rétegzett kötetek is hatással vannak a rendelkezésre álló területre. Ha például egy 8100-as eszközhöz már tartozik 100 TB rétegzett kötet, akkor már csak 4 TB érhető el a helyileg rögzített kötetekhez.

        Az alábbi képen egy helyileg rögzített kötet **Alapbeállítások** párbeszédablaka látható.

         ![Helyi kötet hozzáadása](./media/storsimple-create-volume-u2/add-local-volume-include.png)

        Az alábbi képen egy rétegzett kötet **Alapbeállítások** párbeszédablaka látható.

         ![Helyi kötet hozzáadása](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)

   4. Kattintson a nyíl ikonra ![nyíl ikon](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) a következő oldalra lépéshez.


3. A **További beállítások** párbeszédpanelen adjon hozzá egy új hozzáférés-vezérlési rekordot (ACR):

    1. Adja meg az ACR **nevét**.
    2. Az **iSCSI-kezdeményező neve** alatt adja meg a Windows-gazdagép iSCSI minősített nevét (IQN). Ha nem tudja az IQN-t, lépjen a [Windows Server-gazdagép IQN nevének lekérése](#get-the-iqn-of-a-windows-server-host) szakaszhoz.
    3. A **Alapértelmezett a biztonsági mentés ehhez a kötethez?** területen jelölje be az **Engedélyezés** jelölőnégyzetet. Az alapértelmezett biztonsági mentés létrehoz egy házirendet, amelyet a rendszer mindennap 22:30-kor (az eszköz ideje szerint) futtat, és létrehoz egy felhő-pillanatfelvételt a kötetről.
     
     > [AZURE.NOTE] Miután itt engedélyezte a biztonsági mentést, a beállítás nem állítható vissza. A beállítás módosításához a kötetet kell szerkesztenie.

     ![Kötet hozzáadása](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)

4. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Létrejön egy kötet a megadott beállításokkal.





<!--HONumber=Jun16_HO2-->


