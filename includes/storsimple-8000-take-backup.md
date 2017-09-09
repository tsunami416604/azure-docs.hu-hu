<!--author=alkohli last changed: 01/12/17-->

### <a name="to-take-a-backup"></a>Biztonsági mentés végrehajtása

1. Nyissa meg a StorSimple-eszközkezelő szolgáltatást. Az eszközök táblázatos listájából válassza ki az eszközt és kattintson rá, majd kattintson az **Összes beállítás** elemre. A **Beállítások** panelen lépjen a **Beállítások > Kezelés > Biztonsági mentési szabályzat** részhez.

    ![Biztonsági mentési házirend hozzáadása](./media/storsimple-8000-take-backup/step8takebu1.png)

2. A **Biztonsági mentési szabályzat** panelen kattintson a **+ Szabályzat hozzáadása** gombra.

    ![Biztonsági mentési házirend hozzáadása](./media/storsimple-8000-take-backup/step8takebu2.png)

3. A **Biztonsági mentési szabályzat létrehozása** panelen adjon egy 3–150 karakter hosszúságú nevet a biztonsági mentési szabályzatnak.

4. Válassza ki a köteteket, amelyekről biztonsági mentést kíván készíteni. Ha egynél több kötetet választ ki, ezeket a köteteket a rendszer egy összeomlásbiztos biztonsági mentésbe csoportosítja.

    ![Biztonsági mentési házirend hozzáadása](./media/storsimple-8000-take-backup/step8takebu4.png)

5. Az **Első ütemezés hozzáadása** panelen:

    1. Válassza ki a biztonsági mentés típusát. A gyorsabb visszaállítás érdekében válassza a **Helyi** pillanatfelvételt. Az adatrugalmasság érdekében válassza a **Felhőbeli** pillanatfelvételt.
    2. Adja meg a biztonsági mentés gyakoriságát percben, órában, napban vagy hétben kifejezve.
    3. Adjon meg egy megőrzési időtartamot. A megőrzési lehetőségek a biztonsági mentés gyakoriságától függenek. Napi házirend esetén például a megtartás hetekben adható meg, míg havi házirend esetén hónapokban.
    4. Válassza ki a biztonsági házirend indítási időpontját és dátumát.
    5. A biztonsági mentési szabályzat létrehozásához kattintson az **OK** gombra.

        ![Biztonsági mentési házirend hozzáadása](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. A biztonsági mentési szabályzat létrehozásának megkezdéséhez kattintson a **Létrehozás** gombra. A biztonsági mentési szabályzat sikeres létrehozásáról értesítést kap. A biztonsági mentési szabályzatok listája is frissítve lett.
      
      ![Biztonsági mentési házirend hozzáadása](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Mostantól a biztonsági mentési szabályzat ütemezett biztonsági mentéseket hoz létre a kötetadatokról.




