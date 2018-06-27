A Visual Studio Server Explorer használatával hozhat létre virtuális gépeket az Azure-ban.

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>A Server Explorer egy Azure virtuális gép létrehozása
A virtuális gép létrehozásakor is a [Azure felügyeleti portálon](http://go.microsoft.com/fwlink/?LinkID=253103), is létrehozhat egy virtuális gépet az Azure-ban a Server Explorer parancsok használatával. Virtuális gépek használható, például egy közös terhelésű nyilvános végpontot mögé előtér biztosításához.

### <a name="to-create-a-new-virtual-machine"></a>Egy új virtuális gép létrehozása
1. A Server Explorer eszközben nyissa meg a **Azure** csomópontot, és kattintson **virtuális gépek**.
2. A helyi menüben kattintson a **virtuális gép létrehozása**.
   
    A **hozzon létre egy új virtuális gépet** varázsló jelenik meg.
   
    ![A virtuális gép létrehozása parancs](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. Az a **válasszon egy előfizetést** lapon, válassza ki a virtuális gép létrehozásakor használt előfizetés, majd kattintson **következő**.
   
    Ha jelenleg nincs bejelentkezve az Azure-ba, kattintson a **bejelentkezés** való bejelentkezéshez. Ezután válassza az Azure-előfizetéshez a legördülő listában, ha még nincs kiválasztva.
4. Az a **válassza ki a virtuálisgép-lemezkép** lapon, válassza ki a képhez a **képtípussal** legördülő listán, és válassza ki a virtuálisgép-rendszerképek a a **lemezképnév** listamező . Amikor elkészült, kattintson a **következő**.
   
    ![Válassza ki a virtuális gép lap](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    Lehetősége van a következő típusú.
   
   * **Nyilvános képek** sorolja fel az operációs rendszerek és a kiszolgáló szoftvert, például a Windows Server és SQL Server virtuálisgép-lemezképeket.
   * **MSDN-lemezképeihez** sorolja fel az MSDN-előfizetők, például a Visual Studio és a Microsoft Dynamics számára elérhető szoftverek virtuálisgép-lemezképeket.
   * **Saját lemezképek** listák kifejezetten és általánosítva létrehozott virtuális gépek lemezképeivel.
     
     Speciális és általánosított virtuális gépeket, lásd: [Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/). Lásd: [sablon Windows virtuális gép rögzítése](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) olvashat, hogy a virtuális gép egy sablont, amely segítségével gyorsan létrehozhat új, előre konfigurált virtuális gépek számára.
     
     Kattintson a lap jobb oldalán a lemezképére vonatkozó információ megjelenítéséhez a virtuális gép lemezképének nevét.
     
     > [!NOTE]
     > Nem adható hozzá virtuális gépek lemezképét a **nyilvános képek** vagy **MSDN-lemezképeihez** sorolja fel, mert csak olvasható. Minden, az Ön által létrehozott virtuális gépek hozzáadódnak a **saját lemezképek** listája.
     > 
     > 
     
     Ha a Visual Studio-szintű előfizetést az MSDN-előfizető, létrehozhat egy előre létrehozott Azure virtuális gép, amely a Visual Studio, valamint több lemezképet tartalmaz. További információkért lásd: [virtuális gép létrehozása a Visual Studio használatával képek Visual Studio 2013 gyűjtemény kép az MSDN-előfizetők](http://visualstudio2013msdngalleryimage.azurewebsites.net) és [MSDN előfizetések](https://www.visualstudio.com/products/msdn-subscriptions-vs). |}
5. Az a **virtuális gép alapbeállítások** lapon adja meg a számítógép nevét, és adja meg az előírásoknak, a virtuális gép, beleértve a méretet, és egy felhasználónevet és jelszót. Amikor elkészült, kattintson a **következő**.
   
    Jelentkezzen be a géphez távoli asztali kapcsolattal, így érdemes írja le abban az esetben, ha elfelejti a új nevet és jelszót fogja használni. Azure virtuális gép létrehozása a Visual Studio, után módosíthatja annak méretét és az egyéb beállításai a [Azure felügyeleti portálon](http://go.microsoft.com/fwlink/?LinkID=253103).
   
   > [!NOTE]
   > Ha úgy dönt, hogy nagyobb méretek a virtuális géphez, külön költségek merülhetnek fel. Lásd: [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/) további információt.
   > 
   > 
6. A Visual Studio létrehozott virtuális gépeknél szükség van a felhőszolgáltatásban. Az a **felhőalapú szolgáltatás beállításainak** lapon jelölje be a virtuális gép egy felhőalapú szolgáltatás, vagy kattintson a **< hozzon létre új... >** a legördülő listában, ha még nem telepítette a felhőalapú szolgáltatás, vagy egy új használni szeretne. A storage-fiók is szükség, ezért válasszon tárfiókot (vagy hozzon létre egy új tárfiókot) az a **tárfiók** legördülő listában. Lásd: [Microsoft Azure Storage bemutatása](../articles/storage/common/storage-introduction.md) további információt.
7. Ha meg szeretné határozni a virtuális hálózat (nem kötelező), jelölje ki a virtuális hálózati és alhálózati legördülő listák.
   
    Különböző tartalék tartományok telepített virtuális gépek rendelkezésre állási csoportok tagjai. Lásd: [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) további információt.
8. Ha a virtuális gép egy (szintén nem kötelező) rendelkezésre állási csoportba tartozik, jelölje be a **adja meg a rendelkezésre állási csoportok** jelölje be a jelölőnégyzetet, és válassza a rendelkezésre állási készlet a legördülő listában. Amikor elkészült, válassza ki a **következő** gombra.
   
    Az alkalmazás hálózati hibák, helyi lemezhiba hardver és a tervezett leállások közben elérhető marad a virtuális gép hozzáadása a rendelkezésre állási csoportok segítségével. Kell használnia a [Azure felügyeleti portálon](http://go.microsoft.com/fwlink/?LinkID=253103) segítségével hozzon létre virtuális hálózatok, az alhálózatok és a rendelkezésre állási készlet. Lásd: [a virtuális gépek rendelkezésre állásának kezelése](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) további információt.
9. Az a **végpontok** adja meg azokat a nyilvános végpontok, amelyet a felhasználók a virtuális gép számára elérhető. Például előfordulhat, hogy most engedélyezi HTTP (80-as Port) mellett a távoli asztal és a PowerShell-végpontot, amely alapértelmezés szerint engedélyezve vannak. A végpont hozzáadásához válasszon a **Port neve** legördülő listán, és válassza ki a **Hozzáadás** gombra. A végpont eltávolításához válassza ki a piros **X** a végpontok listájában neve mellett.
   
    ![A végpontok oldalon, a virtuális gép varázsló.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    A rendelkezésre álló végpontok a virtuális gép a kiválasztott felhő szolgáltatás függ. Lásd: [Azure Szolgáltatásvégpontok](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) további információt.
   
   > [!NOTE]
   > Nyilvános végpontok engedélyezésére elérhetővé szolgáltatások a virtuális gépen az internethez. Ügyeljen arra, hogy telepítse, és megfelelően konfigurálni a virtuális gépet, a végpontokat és a szolgáltatások, mint például a beállítás hozzáférés-vezérlési lista (ACL) a végpontok. Lásd: [hogyan állítsa be végpontok egy virtuális géphez](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) további információt.
   > 
   > 
10. Miután elkészült a virtuális gép beállításainak konfigurálását, válassza ki a **létrehozása** gombra, a virtuális gép.
    
     Mivel Azure hozza létre a virtuális gép a **Azure tevékenységnapló** mutatja a virtuálisgép-létrehozási művelet előrehaladását.
    
     ![Virtuális gép tevékenységnapló - folyamatban.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     Csak a virtuálisgép-adatok megtekintéséhez válassza a **virtuális gépek** lapra a **Azure tevékenységnapló**.
    
     ![Virtuális gép tevékenységnapló - befejeződött.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     Ha a művelet sikeresen befejeződött, az új virtuális gép megjelenik a **virtuális gépek** a Server Explorer csomópont. Regisztrálhat rá kattintva a **csatlakozzon a távoli asztali kapcsolattal** helyi.
    
     ![A virtuális gép a Server Explorer megjelenne.](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>A virtuális gépek kezelése
Az mellett leállítása, csatlakozás, frissítése és az ellenőrzőpontok felvétele a kiválasztott virtuális gépet, a virtuális gép konfigurációs lapján is megtekintheti vagy módosíthatja a virtuális gép beállításait. A következőket teheti:

* Módosíthatja a virtuális gép méretét.
* Válassza ki a rendelkezésre állási csoportot a virtuális gép használata.
* Hozzáadása, eltávolítása vagy nyilvános végpontok beállításainak módosítása.
* Hozzáadása, eltávolítása vagy virtuálisgép-bővítmények konfigurálása.
* A virtuális géphez tartozó lemez adatainak megtekintése.

### <a name="view-or-change-virtual-machine-settings"></a>Virtuális gép beállításainak megtekintése vagy módosítása
1. A Server Explorer eszközben válassza ki a virtuális gép a **Azure virtuális gépek** csomópont.
2. A helyi menüben válasszon **konfigurálása** a virtuális gép konfigurációs lapnak a megtekintésére.
   
    ![Az Azure virtuális gép konfigurációs lapján](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. A virtuális gép adatainak megtekintéséhez, vagy módosítsa azt.

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>Mentse, vagy a virtuális gép állapotának visszaállítása
Konfigurálja a virtuális gépet, és a szoftverek telepítését, célszerű rendszeresen takaríthat meg a folyamatban lévő virtuális gépek ellenőrzőpontjainak létrehozása. Egy ellenőrzőpont egy pillanatkép, a kép vagy az aktuális állapot, a virtuális gép. Ha probléma merül fel a virtuális gépet, vagy konfigurálja újra a virtuális gép kívánt, időt takaríthat meg állítja vissza egy korábbi ellenőrzőpont állapotának nulláról keresztül helyett.

### <a name="to-create-a-virtual-machine-checkpoint"></a>A virtuálisgép-ellenőrzőpont létrehozása
1. A Server Explorer eszközben válassza ki a virtuális gép a **Azure virtuális gépek** csomópont.
2. A helyi menüben válasszon **konfigurálása** a virtuális gép konfigurációs lapnak a megtekintésére.
3. A konfiguráció lapon válassza ki a **lemezkép rögzítése** gombra.
   
    ![Azure-alapú konfigurációs oldal rögzítése gomb](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    A **virtuális gép rögzítése** párbeszédpanel jelenik meg.
   
    ![Az Azure rögzítési virtuális gép párbeszédpanel](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. Adjon meg egy kép címke és a leírást. Felülírhatja azokat a saját Ha szeretné, de egy alapértelmezett címkét és egy leírást vannak megadva.
5. Ha már futtatta a Sysprep a virtuális gépen, jelölje be a **futtattam Sysprep eszközt a virtuális gépen** mezőbe.
   
    A Sysprep az eszközzel, többek között rendszerek vonatkozó adatokat eltávolítja a Windows, így mások is használt sablon a virtuális gép verziója. Lásd: [sablon Windows virtuális gép rögzítése](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) további információt. Készítsen biztonsági másolatot a virtuális Gépet a Sysprep futtatása előtt.
6. Miután elkészült a rögzítési beállításainak konfigurálása, válassza a **rögzítése** az ellenőrzőpont létrehozása gombra.
   
    Mivel Azure az ellenőrzőpont hozza létre a **Azure tevékenységnapló** a művelet végrehajtási állapotát tartalmazza.
   
    ![A virtuálisgép-ellenőrzőpont rögzítése](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    Az ellenőrzőpont-művelet befejezése után megjelenik a a **Azure tevékenységnapló**.
   
    ![Ellenőrzőpont művelete befejeződött](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>Kezelheti a virtuális gépek ellenőrzőpontjaival kapcsolatban
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>A virtuális gépek visszaállítani egy korábban mentett állapotra
* Című rész lépéseit követve [lépésről lépésre: hajtsa végre felhő állítja vissza a Microsoft Azure virtuális gépek használata a PowerShell - 2. rész](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx).

### <a name="to-delete-a-checkpoint"></a>Ellenőrzőpont törlése
1. Lépjen a [Azure felügyeleti portálján](http://go.microsoft.com/fwlink/?LinkID=253103).
2. A virtuális gép konfigurációs lapján válassza a **képek** fülre az oldal tetején.
3. Válassza ki az ellenőrző pont törlése, és válassza ki a **törlése** gombra az oldal alján.

## <a name="shut-down-your-virtual-machine"></a>A virtuális gép leállítása
1. A Server Explorer eszközben válassza ki a kívánt állítsa le a virtuális gép a **Azure virtuális gépek** csomópont.
2. A helyi menüben, vagy válassza ki a **leállítási** parancsot, vagy válasszon **konfigurálása** megtekintéséhez a virtuális gép konfigurációs lapján, és válassza ki a **leállítási** gombra.

## <a name="next-steps"></a>További lépések
Virtuális gépek létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy virtuális gép futó Linux](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [az Azure betekintő portálon Windows rendszerű virtuális gép létrehozása](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

