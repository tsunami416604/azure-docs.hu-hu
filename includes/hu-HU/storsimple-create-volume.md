<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>Kötet létrehozása
1. A **Gyors üzembe helyezés** oldalon kattintson a **Kötet hozzáadása** lehetőségre. Ezzel elindítja a Kötet hozzáadása varázslót.
2. A Kötet hozzáadása varázslóban az **Alapbeállítások** területen tegye a következőket:
   
   1. Adjon **nevet** a kötetnek.
   2. Adja meg a kötethez tartozó **Kiosztott kapacitást** GB-ban vagy TB-ban. A kötet kapacitása egy 1 GB és 64 TB közötti érték lehet fizikai eszközök esetében.
   3. A legördülő listából adja meg a **Használat típusa** beállítást a kötethez. 
   4. Ha archív adatokhoz használja ezt a kötetet, jelölje be a **Kötet használata ritkábban használt archív adatokhoz** jelölőnégyzetet. Minden más használat esetén egyszerűen válassza a **Rétegzett kötet** lehetőséget. (A rétegzett kötetek neve korábban elsődleges kötet volt.)
      
        ![Kötet hozzáadása](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Kattintson a nyíl ikonra ![nyíl ikon](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) a következő oldalra lépéshez.
3. A **További beállítások** párbeszédpanelen adjon hozzá egy új hozzáférés-vezérlési rekordot (ACR):
   
   1. Adja meg az ACR **nevét**.
   2. Az **iSCSI-kezdeményező neve** alatt adja meg a Windows-gazdagép iSCSI minősített nevét (IQN). Ha nem tudja az IQN-t, lépjen a [Windows Server-gazdagép IQN nevének lekérése](#get-the-iqn-of-a-windows-server-host) szakaszhoz.
   3. Javasoljuk, hogy engedélyezze az alapértelmezett biztonsági mentést az **Alapértelmezett biztonsági mentés engedélyezése ehhez a kötethez** jelölőnégyzet bejelölésével. Az alapértelmezett biztonsági mentés létrehoz egy házirendet, amelyet a rendszer mindennap 22:30-kor (az eszköz ideje szerint) futtat, és létrehoz egy felhő-pillanatfelvételt a kötetről.
      
      > [!NOTE]
      > Miután itt engedélyezte a biztonsági mentést, a beállítás nem állítható vissza. A beállítás módosításához a kötetet kell szerkesztenie.
      > 
      > 
      
        ![Kötet hozzáadása](./media/storsimple-create-volume/AddVolume2-include.png)
4. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Létrejön egy kötet a megadott beállításokkal.

![Videó elérhető](./media/storsimple-create-volume/Video_icon.png)**Videó elérhető**

A StorSimple-kötet létrehozását bemutató videó megtekintéséhez kattintson [ide](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).

