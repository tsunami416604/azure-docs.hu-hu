<!--author=SharS last changed: 9/17/15-->

#### <a name="to-create-a-volume-container"></a>Kötettároló létrehozása
1. Az eszköz **Gyors üzembe helyezés** oldalán kattintson a**Kötettároló hozzáadása** lehetőségre. Megjelenik a **Kötettároló létrehozása** párbeszédablak.
   
    ![Kötettároló létrehozása](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)
2. A **Kötettároló létrehozása** párbeszédablakban:
   
   1. Adja meg a kötettároló **nevét**. A név 3–32 karakter hosszúságú lehet.
   2. Válasszon egy **tárfiókot**, amelyet ehhez a kötettárolóhoz kíván társítani. Kiválaszthat egy alapértelmezett fiókot, amely a szolgáltatás létrehozásakor jön létre. Használhatja az **Új hozzáadása** lehetőséget is. Ezzel megadhat egy olyan tárfiókot, amely nincs ehhez a szolgáltatás-előfizetéshez kapcsolva.
   3. A **Felhőalapú tárolás titkosításának engedélyezése** lehetőséggel engedélyezheti az eszközről a felhőbe küldött adatok titkosítását.
   4. Adja meg a **Felhőalapú tárolás titkosítási kulcsát**, amely 8–32 karakter hosszúságú lehet. Ezt a kulcsot az eszköz a titkosított adatok hozzáféréséhez használja.
   5. A **Sávszélesség megadása** legördülő listában válassza a **Korlátlan** lehetőséget, ha az összes rendelkezésre álló sávszélességet fel kívánja használni. Ehhez a beállításhoz megadhatja az **Egyéni** értéket is, ha sávszélesség-korlátozást szeretne alkalmazni. 1 és 1000 Mb/s közötti értéket adhat meg. 
      Ha hozzáfér a sávszélesség-használati adatokhoz, lehetősége van a sávszélesség ütemezett lefoglalására a **Sávszélességsablon kiválasztása** beállítás megadásával. Az eljárás pontos lépéseit a [Sávszélességsablon hozzáadása](../articles/storsimple/storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template) szakaszban találja.
   6. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) a kötettároló mentéséhez és a varázslóból való kilépéshez. 
   
   Az újonnan létrehozott kötettároló megjelenik a **Kötettárolók** oldalon lévő listán.

![Videó elérhető](./media/storsimple-create-volume-container/Video_icon.png)**Videó elérhető**

A kötettároló a StorSimple megoldásban való létrehozását bemutató videó megtekintéséhez kattintson [ide](https://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/).

