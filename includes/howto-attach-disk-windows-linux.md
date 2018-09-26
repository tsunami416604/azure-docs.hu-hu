


## <a name="attach-an-empty-disk"></a>Üres lemez csatlakoztatása
Üres lemez csatolása módja egy egyszerű, adatlemez hozzáadása, mert az Azure az Ön hozza létre a .vhd fájlt, és a tárfiókban tárolja azokat.

1. Kattintson a **virtuális gépek (klasszikus)**, majd válassza ki a megfelelő virtuális Gépet.

2. A beállítások menüben kattintson a **lemezek**.

   ![Új üres lemez csatolása](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Kattintson a parancssáv **új Attach**.  
    A **új lemez csatolása** párbeszédpanel jelenik meg.

    ![Új lemez csatolása](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Töltse ki a következő információkat:
    - A **Fájlnév**, fogadja el az alapértelmezett nevet, vagy írjon be egy másikat a .vhd fájlt. Az adatlemez egy automatikusan létrehozott nevet használ, akkor is, ha beírja a .vhd fájlt egy másik nevet.
    - Válassza ki a **típus** az adatokat lemezen. Összes virtuális gépet a standard szintű lemezek támogatásához. Több virtuális gép is támogatja a prémium szintű lemezeket.
    - Válassza ki a **mérete (GB)** az adatokat lemezen.
    - A **gazdagép gyorsítótárazási**, válasszon none, vagy csak olvasható.
    - Kattintson az OK gombra a befejezéshez.

4. A data-lemezek létrehozása és csatlakoztatása után a virtuális lemezek szakaszában szerepel.

   ![Sikeresen csatolta az új és üres adatlemez](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Miután hozzáadott egy adatlemezt, jelentkezzen be a virtuális gép és inicializálja a lemezt, hogy a használat szüksége.

## <a name="how-to-attach-an-existing-disk"></a>Hogyan: meglévő lemez csatlakoztatása
Meglévő lemez csatlakoztatása esetén rendelkeznie kell egy tárfiókban elérhető .vhd-vel. Használja a [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) parancsmaggal töltse fel a .vhd fájlt a storage-fiókba. Miután létrehozott és feltöltött a .vhd fájlt, egy virtuális géphez is csatlakoztatható.

1. Kattintson a **virtuális gépek (klasszikus)**, majd válassza ki a megfelelő virtuális gépet.

2. A beállítások menüben kattintson a **lemezek**.

3. Kattintson a parancssáv **csatolása meglévő**.

    ![Adatlemez csatolása](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Kattintson a **hely**. A rendelkezésre álló tárfiókok jelennek meg. Ezután válassza ki a megfelelő storage-fiók fel van sorolva a.

    ![Adja meg a lemez tárfiókot](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **tárfiók** egy vagy több olyan tárolók, amelyek tartalmazzák a meghajtók (VHD-k) tárolja. Jelöljön ki a megfelelő tárolót fel van sorolva.

    ![Adja meg a virtuális gépek windows-tároló](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. A **VHD-k** panel felsorolja a merevlemez-meghajtók, a tárolóban tárolt. Kattintson az egyik lemezt, és majd a Kiválasztás gombra.

    ![Adja meg a lemezkép virtuális gépek – Windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. A **meglévő lemez csatolása** panel megjeleníti újra, a storage-fiók, tároló és kijelölt merevlemez (vhd) a virtuális gép hozzáadása tartalmazó helyét.

  Állítsa be **gazdagép gyorsítótárazási** None vagy olvasási csak, majd kattintson az OK gombra.

    ![Sikeresen csatolta adatlemez](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
