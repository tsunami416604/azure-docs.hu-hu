


Rendelkezésre állási készlet nyújt segítséget, mint tartani a virtuális gépek állásidő, során rendelkezésre álló karbantartás során. Két vagy több hasonló módon konfigurált virtuális gépek elhelyezése egy rendelkezésre állási csoportot hoz létre a redundancia csökkentése érdekében az alkalmazások vagy a virtuális gépen futó szolgáltatások rendelkezésre állását fenntartásához szükséges. Ez működésével kapcsolatos részletekért lásd: [virtuális gépek rendelkezésre állásának kezelése][Manage the availability of virtual machines].

Ajánlott eljárás segítségével rendelkezésre állási készletek és a terheléselosztás végpontok győződjön meg arról, hogy az alkalmazás mindig elérhető, és fut. hatékonyan. További információk az elosztott terhelésű végpont: [Azure infrastruktúra-szolgáltatásokat a terheléselosztás][Load balancing for Azure infrastructure services].

Klasszikus virtuális gépeket adhat hozzá a rendelkezésre állási készlet két lehetőség egyikét használva:

* [1. lehetőség: Hozzon létre egy virtuális gép és a rendelkezésre állási készlet egyszerre][Option 1: Create a virtual machine and an availability set at the same time]. Adja hozzá az új virtuális gépek a készlethez azon virtuális gépek létrehozásakor.
* [2. lehetőség: Hozzáadása egy meglévő virtuális gép rendelkezésre állási csoportok][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> A klasszikus modellben el szeretné helyezni az azonos rendelkezésre állási csoport a virtuális gépek azonos a felhőszolgáltatáshoz kell tartoznia.
> 
> 

## <a id="createset"> </a>1. lehetőség: Hozzon létre egy virtuális gép és a rendelkezésre állási készlet egyszerre
Ehhez használhatja az Azure portálon vagy az Azure PowerShell-parancsokat.

Az Azure portál használata:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **hozzon létre egy erőforrást** > **számítási**.
3. Válassza ki a használni kívánt piactér virtuálisgép-lemezkép. Ha szeretné, hozzon létre egy Linux vagy a Windows virtuális gépet.
4. A kijelölt virtuális gép esetében ellenőrizze, hogy a telepítési modell **klasszikus** majd **létrehozása**
   
    ![Kép helyettesítő szövege](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Adja meg a virtuális gép nevét, felhasználónevet és jelszót (a Windows gépek) vagy (a Linux-gépek) nyilvános SSH-kulcsot. 
6. Válassza ki a virtuális gép méretét, és kattintson a **válasszon** a folytatáshoz.
7. Válasszon **opcionális konfigurációs > rendelkezésre állási csoport**, és válassza ki a rendelkezésre állási csoportban, akkor a virtuális gép hozzá kívánja.
   
    ![Kép helyettesítő szövege](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Tekintse át a konfigurációs beállításokat. Amikor elkészült, kattintson a **létrehozása**.
9. Amíg az Azure létrehozza a virtuális gép, nyomon követheti a folyamat állapotát **virtuális gépek** a központ menüben.

Azure PowerShell-parancsok használatával hozzon létre egy Azure virtuális gépen, és adja hozzá egy új vagy meglévő rendelkezésre állási csoport, lásd: [hozhat létre és konfigurálja a Windows-alapú virtuális gépek előre Azure PowerShell használata](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"> </a>2. lehetőség: Hozzáadása egy meglévő virtuális gép rendelkezésre állási csoportok
Az Azure-portálon adhat hozzá meglévő klasszikus virtuális gépek rendelkezésre állási beállítása, vagy hozzon létre egy újat a számukra. (Ne feledje, hogy az azonos rendelkezésre állási csoport a virtuális gépek azonos a felhőszolgáltatáshoz kell tartoznia.) A lépései megegyeznek szinte. Az Azure PowerShell a virtuális gép adhat hozzá egy meglévő rendelkezésre állási csoportot.

1. Ha még nem tette meg, jelentkezzen be a [Azure-portálon](https://portal.azure.com).
2. Kattintson a bal oldali menü **virtuális gépek (klasszikus)**.
   
    ![Kép helyettesítő szövege](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Virtuális gépek listájából válassza ki a virtuális gép hozzá a kívánt nevét.
4. Válasszon **rendelkezésre állási csoport** a virtuális gépről **beállítások**.
   
    ![Kép helyettesítő szövege](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Válassza ki a rendelkezésre állási csoport a virtuális gép hozzá kívánja. A virtuális gép az ugyanabban a rendelkezésre állási csoport felhőszolgáltatásra kell tartoznia.
   
    ![Kép helyettesítő szövege](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Kattintson a **Save** (Mentés) gombra.

Az Azure PowerShell-parancsokkal, nyisson meg egy rendszergazdai Azure PowerShell-munkamenetet, és futtassa a következő parancsot. A helyőrzőket a (például &lt;VmCloudServiceName&gt;), cserélje le a mindent, ami az ajánlatokat, beleértve a < és > karakter, helyes nevét.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> A virtuális gép kell hozzáadni a rendelkezésre állási csoport befejezéséhez újra kell indítani.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

