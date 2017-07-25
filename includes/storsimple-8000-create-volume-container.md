<!--author=alkohli last changed: 06/22/17-->

#### <a name="to-create-a-volume-container"></a>Kötettároló létrehozása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt, majd kattintson rá. 

    ![Kötettároló panel](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Az eszköz irányítópultján kattintson a **+ Kötettároló hozzáadása** gombra.

    ![Kötettároló panel](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. A **Kötettároló hozzáadása** panelen:
   
   1. Az eszköz automatikusan ki lesz választva.
   2. Adja meg a kötettároló **nevét**. A név 3–32 karakter hosszúságú lehet. A kötettárolók nem nevezhetők át a létrehozásuk után.
   3. A **Felhőalapú tárolás titkosításának engedélyezése** lehetőséggel engedélyezheti az eszközről a felhőbe küldött adatok titkosítását.
   4. Adja meg a **Felhőalapú tárolás titkosítási kulcsát**, amely 8–32 karakter hosszúságú lehet. Ezt a kulcsot az eszköz a titkosított adatok hozzáféréséhez használja.
   5. Válasszon egy **tárfiókot**, amelyet ehhez a kötettárolóhoz kíván társítani. Választhat egy meglévő tárfiókot vagy kiválaszthatja az alapértelmezett fiókot is, amely a szolgáltatás létrehozásakor jön létre. Használhatja az **Új hozzáadása** lehetőséget is. Ezzel megadhat egy olyan tárfiókot, amely nincs ehhez a szolgáltatás-előfizetéshez kapcsolva.
   6. A **Sávszélesség megadása** legördülő listában válassza a **Korlátlan** lehetőséget, ha az összes rendelkezésre álló sávszélességet fel kívánja használni. Ehhez a beállításhoz megadhatja az **Egyéni** értéket is, ha sávszélesség-korlátozást szeretne alkalmazni. 1 MB/s és 1000 Mb/s közötti értéket adhat meg.
      Ha hozzáfér a sávszélesség-használati adatokhoz, lehetősége van a sávszélesség ütemezett lefoglalására a **Sávszélességsablon kiválasztása** beállítás megadásával. Az eljárás pontos lépéseit a [Sávszélességsablon hozzáadása](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template) szakaszban találja.

      ![Kötettároló panel](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Kattintson a **Létrehozás** gombra.

        ![Kötettároló panel](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       A kötettároló sikeres létrehozásáról értesítést kap.

       ![Kötettároló létrehozási értesítése](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Az újonnan létrehozott kötettároló megjelenik az eszköz kötettárolóinak listájában.

   ![Kötettároló hozzáadása panel](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


