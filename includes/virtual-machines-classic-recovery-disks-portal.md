Ha az Azure-ban valamely virtuális gép (VM) indítási vagy lemezhibát észlel, előfordulhat, hogy magán a virtuális merevlemezen kell elvégeznie a hibaelhárítást. Egy gyakori példa erre, amikor egy sikertelen alkalmazásfrissítés megakadályozza, hogy a virtuális gép sikeresen elinduljon. A cikk leírja, hogyan csatlakoztathatja virtuális merevlemezét egy másik virtuális géphez az Azure Portal használatával a hibák javítása és az eredeti virtuális gép ismételt létrehozása érdekében.


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a meghibásodott virtuális gépet, de a virtuális merevlemezeket ne.
2. Csatlakoztassa a virtuális merevlemezt egy másik virtuális géphez hibaelhárítás céljából.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. A fájlok szerkesztésével vagy eszközök futtatásával javítsa a hibákat az eredeti virtuális merevlemezen.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális gépet az eredeti virtuális merevlemez használatával.

## <a name="delete-the-original-vm"></a>Az eredeti virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemezen található az operációs rendszer, az alkalmazások, valamint a konfigurációk. A virtuális gép ezzel szemben csak egy metaadathalmaz, amely a méretet vagy a helyet határozza meg, illetve amely az erőforrásokra, például a virtuális merevlemezre vagy a virtuális hálózati kártyára (NIC) hivatkozik. A rendszer minden egyes virtuális merevlemezhez hozzárendel egy bérletet, amikor az adott merevlemez egy virtuális géphez lesz csatlakoztatva. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is hozzárendeli az operációs rendszer merevlemezét a virtuális gépekhez, még ha az adott virtuális gép leállított vagy felszabadított állapotban van is.

A virtuális gép helyreállításának első lépése magának a virtuális gép erőforrásnak a törlése. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemezt csatlakoztathatja egy másik virtuális géphez a hibák megkeresése és elhárítása érdekében. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A bal oldali menüben kattintson a **Virtuális gépek (klasszikus)** elemre.
3. Válassza ki a hibás virtuális gépet, kattintson a **Lemezek** elemre, majd azonosítsa a virtuális merevlemez nevét. 
4. Válassza ki az operációs rendszer merevlemezét, és a **Hely** mezőben azonosítsa a virtuális merevlemezt tároló tárfiók nevét. Az alábbi példában a közvetlenül a „.blob.core.windows.net” előtt álló karakterlánc a tárfiók neve.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![A virtuális gép helyét bemutató kép](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Kattintson jobb gombbal a virtuális gépre, majd válassza a **Törlés** lehetőséget. Győződjön meg róla, hogy a lemezek nincsenek kijelölve, amikor törli a virtuális gépet.
6. Hozzon létre egy új helyreállítási virtuális gépet. Ennek a virtuális gépnek ugyanabban a régióban és erőforráscsoportban (felhőszolgáltatás) kell lennie, mint a hibás virtuális gépnek.
7. Jelölje ki a helyreállítási virtuális gépet, majd válassza a **Lemezek** > **Meglévő csatolása** lehetőséget.
8. A meglévő virtuális merevlemez kiválasztásához kattintson a **VHD-fájl** lehetőségre:

    ![Meglévő VHD keresése](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Jelölje ki a tárfiókot > a VHD-tárolót > és a virtuális merevlemezt, majd kattintson a **Kiválasztás** gombra a kijelölés megerősítéséhez.

    ![Meglévő VHD kiválasztása](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Miután már kiválasztotta a VHD-t, kattintson az **OK** gombra a meglévő virtuális merevlemez csatlakoztatásához.
11. Néhány másodperc elteltével a virtuális gép **Lemezek** panelje csatlakoztatott adatlemezként jeleníti meg a meglévő virtuális merevlemezt:

    ![Adatlemezként csatlakoztatott meglévő virtuális merevlemez](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Hibák javítása az eredeti virtuális merevlemezen
Miután csatlakoztatta a meglévő virtuális merevlemezt, végrehajthatja a szükséges karbantartási és hibaelhárítási lépéseket. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez leválasztása
Miután a hibákat kijavította, válassza le a meglévő virtuális merevlemezt a hibaelhárító virtuális gépről. A virtuális merevlemezt nem használhatja más virtuális géppel, amíg a virtuális merevlemezt a hibaelhárító virtuális géphez társító bérlet nincs feloldva.  

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). 
2. A bal oldali menüben válassza a **Virtuális gépek (klasszikus)** elemet.
3. Keresse meg a helyreállítási virtuális gépet. Válassza a Lemezek lehetőséget, kattintson a jobb gombbal a lemezre, majd válassza a **Leválasztás** parancsot.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezről

Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [Azure-portálon](https://portal.azure.com).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A portálon, válassza a bal felső at **hozzon létre egy erőforrást** > **számítási** > **virtuális gép** > **a Gyűjteményelem**.
3. A **Kép kiválasztása** szakaszban válassza a **Saját lemezek** lehetőséget, majd válassza ki az eredeti virtuális merevlemezt. Ellenőrizze a helyadatokat. Ez az a régió, ahol a virtuális gépet üzembe kell helyezni. Válassza a Tovább gombot.
4. A **Virtuális gép konfigurációja** szakaszban írja be a virtuális gép nevét, majd válasszon méretet a virtuális gép számára.
