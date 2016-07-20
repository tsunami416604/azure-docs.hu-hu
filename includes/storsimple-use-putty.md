<!--author=SharS last changed: 9/17/15-->

#### Csatlakozás soros konzolon keresztül

1. Csatlakoztassa a soros kábelt az eszközhöz (közvetlenül vagy egy USB–soros adapteren keresztül).

2. Nyissa meg a **Vezérlőpultot**, majd az **Eszközkezelőt**.

3. Keresse meg a COM-portot az alábbi ábrának megfelelően.

     ![Csatlakozás soros konzolon keresztül](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)

4. Indítsa el a PuTTY alkalmazást. 

5. A jobb oldali panelen állítsa a **Kapcsolat típusa** beállítást **Soros** értékre.

6. A jobb oldali panelen írja be a megfelelő COM-portot. Ügyeljen arra, hogy a soros konfiguráció paraméterei az alábbiaknak megfelelők legyenek:
  - Sebesség: 115 200
  - Adatbitek: 8
  - Stopbitek: 1
  - Paritás: Nincs
  - Folyamatvezérlés: Nincs.

    Ezek a beállítások az alábbi ábrán láthatók.

     ![PuTTY-beállítások](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 

    > [AZURE.NOTE] Ha az alapértelmezett folyamatvezérlési beállítás nem működik, próbálja meg átállítani XON/XOFF értékre.

7. Soros munkamenet indításához kattintson a **Megnyitás** elemre.
 


<!--HONumber=Jun16_HO2-->


