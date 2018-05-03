


## <a name="attach-an-empty-disk"></a>Üres lemez csatlakoztatása
Üres lemez csatolása módja a egyszerű hozzá adatlemezt, mert az Azure létrehozza a .vhd fájlt, és azt a tárfiók tárolja.

1. Kattintson a **virtuális gépek (klasszikus)**, majd válassza ki a megfelelő virtuális gép.

2. A beállítások menüben kattintson a **lemezek**.

   ![Egy új üres lemez csatolása](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. A parancssávon kattintson **új Attach**.  
    A **új lemez csatolása** párbeszédpanel jelenik meg.

    ![Új lemez csatolása](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Írja be a következő adatokat:
    - A **Fájlnév**, fogadja el az alapértelmezett nevet, vagy írja be a .vhd fájl egy másikat. Az adatok lemez egy automatikusan létrehozott nevet használ, akkor is, ha beírja a .vhd fájlt egy másik nevet.
    - Válassza ki a **típus** az adatok lemez. Az összes virtuális gép támogatja a normál lemezeket. Több virtuális gép is támogatja a premium lemezek.
    - Válassza ki a **méret (GB)** az adatok lemez.
    - A **állomás-gyorsítótárazása**, válasszon none, vagy csak olvasható.
    - Kattintson az OK gombra a befejezéshez.

4. Miután a adatlemez létrehozták, és a kapcsolódó, a virtuális lemezek szakaszában szerepel.

   ![Sikeresen csatolta az új és üres adatlemez](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Adatlemez hozzáadása után kell jelentkezzen be a virtuális Gépet, és végezze el a lemez inicializálását, hogy használható.

## <a name="how-to-attach-an-existing-disk"></a>Hogyan: meglévő lemez csatolása
Meglévő lemez csatlakoztatása esetén rendelkeznie kell egy tárfiókban elérhető .vhd-vel. Használja a [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) parancsmagot, hogy a .vhd fájl feltöltése a tárfiókba. Létrehozott, és a .vhd fájl feltöltése után hozzácsatolhat egy virtuális géphez.

1. Kattintson a **virtuális gépek (klasszikus)**, majd válassza ki a megfelelő virtuális gép.

2. A beállítások menüben kattintson a **lemezek**.

3. A parancssávon kattintson **Csatolás meglévő**.

    ![Adatlemez csatolása](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Kattintson a **hely**. A rendelkezésre álló tár fiókokat jeleníti meg. Ezután válassza ki a megfelelő tárolási fiók felsorolt.

    ![Adja meg a storage-fiók](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **tárfiók** rendelkezik egy vagy több olyan tárolók, amelyek tartalmazzák a meghajtók (VHD-k). Válassza ki a megfelelő tárolót felsorolt.

    ![Adja meg a virtuális gépek windows tároló](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. A **VHD-k** panel a tárolóban tárolt a merevlemez-meghajtók listája. Kattintson egy lemezt, majd válassza ki.

    ![Adja meg a lemezképet a virtuális gépek windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. A **meglévő lemez csatolása** panel megjeleníti újra, a helyen lévő tartalmazó a tárfiókot, a tároló és a kijelölt merevlemez (vhd) a virtuális géphez való hozzáadásához.

  Állítsa be **állomás-gyorsítótárazása** None vagy olvasási csak, majd kattintson az OK gombra.

    ![Sikeresen csatolta adatlemez](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
