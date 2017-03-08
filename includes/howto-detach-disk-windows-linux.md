Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ha leválaszt egy lemezt, azzal eltávolítja azt a virtuális gépről, de nem törli az Azure Storage-fiókból.

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  

> [!NOTE]
> Az operációsrendszer-lemez leválasztásához előbb törölnie kell a virtuális gépet.
>

## <a name="find-the-disk"></a>A lemez megkeresése
Ha nem tudja a lemez nevét, vagy szeretné ellenőrizni a leválasztás előtt, kövesse a következő lépéseket.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Kattintson a **Virtual Machines** lehetőségre, majd válassza ki a megfelelő virtuális gépet.

3. Kattintson a virtuális gép irányítópultjának bal szélén, a **Beállítások** területen a **Lemezek** elemre.

 A virtuális gép irányítópultja felsorolja a csatlakoztatott lemezek nevét és típusát. Ez a képernyő például egy virtuális gépet jelenít meg egy operációsrendszer-lemezzel és egy adatlemezzel:

    ![Adatlemez megkeresése](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>A lemez leválasztása
1. Az Azure Portalon kattintson a **Virtuális gépek** lehetőségre, majd kattintson annak a virtuális gépnek a nevére, amelyen a leválasztani kívánt adatok vannak.

2. Kattintson a virtuális gép irányítópultjának bal szélén, a **Beállítások** területen a **Lemezek** elemre.

3. Kattintson a leválasztani kívánt lemezre.

  ![A leválasztani kívánt lemez azonosítása](./media/howto-detach-disk-windows-linux/disklist.png)

4. A parancssoron kattintson a **Leválasztás** elemre.

  ![A leválasztási parancs megkeresése](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. A megerősítés ablakban kattintson az **Igen** gombra a lemez leválasztásához.

  ![A lemez leválasztásának megerősítése](./media/howto-detach-disk-windows-linux/confirmdetach.png)

A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.
