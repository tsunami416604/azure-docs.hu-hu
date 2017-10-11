1. Jelentkezzen be a [klasszikus Azure portálra](http://manage.windowsazure.com).  
2. A parancssávon az ablak alján kattintson **új**.
3. A **számítási**, kattintson a **virtuális gép**, és kattintson a **a gyűjtemény**.
   
    ![Új virtuális gép létrehozása][Image1]
4. Az a **SUSE** csoportban, OpenSUSE virtuálisgép-lemezkép válassza ki, majd kattintson a nyílra, a folytatáshoz.
5. Az első **virtuálisgép-konfiguráció** lap:
   
   * Adjon meg egy **virtuális gép neve**, például a "testlinuxvm". A név kell csak 3 és 15 karakter közötti lehet, is tartalmazhat, csak betűket, számokat és kötőjeleket tartalmazhat, és kell betűvel kezdődhet és betűvel vagy számmal végződhet.
   * Ellenőrizze a **réteg** , és válasszon egy **mérete**. A réteg meghatározza, hogy a méretek közül választhat. A mérete befolyásolja költségét, valamint csatolhat a konfigurációs beállítások, például hogy hány adatok lemezek. További információkért lásd: [virtuális gépek méretei](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Adjon meg egy **új felhasználónevet**, vagy fogadja el az alapértelmezett **azureuser**. Ez a név a Sudoers listáját tartalmazó fájl kerül.
   * Döntse el, hogy milyen típusú **hitelesítési** használatára. Általános jelszó útmutatást lásd: [erős jelszavak](http://msdn.microsoft.com/library/ms161962.aspx).
6. A következő **virtuálisgép-konfiguráció** lap:
   
   * Használja az alapértelmezett **hozzon létre egy új felhőalapú szolgáltatás**.
   * Az a **DNS-név** mezőbe írja be a címet, például "testlinuxvm" része egy egyedi DNS-név.
   * Az a **régió/affinitás csoport/virtuális hálózati** mezőben válasszon ki egy régiót, ahol a virtuális lemezképet fog üzemelni.
   * A **végpontok**, tartsa meg az SSH-végpontot. Mások, vegye fel vagy hozzáadása, módosítása, vagy törölje azokat, a virtuális gép létrehozása után.
     
     > [!NOTE]
     > Ha azt szeretné, hogy a virtuális gép egy virtuális hálózatot, akkor **kell** adja meg a virtuális hálózatot, a virtuális gép létrehozásakor. Nem adható hozzá a virtuális gép virtuális hálózathoz, a virtuális gép létrehozása után. További információkért lásd: [virtuális hálózat áttekintése](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. Utolsó **virtuálisgép-konfiguráció** lapon. az alapértelmezett beállítások megtartásához, majd kattintson a pipa jelre a Befejezés gombra.

A portál megjeleníti az új virtuális gépek **virtuális gépek**. Amíg az állapota **(kiépítése)**, a virtuális gép beállítása folyamatban van. Ha az állapot az elvártnak megfelelően **futtató**, a következő lépéssel továbbléphet.

## <a name="connect-to-the-virtual-machine"></a>Csatlakozzon a virtuális géphez
SSH vagy PuTTY fogja használni a virtuális gép, attól függően, hogy az operációs rendszer azon a számítógépen történő csatlakozás fog csatlakozni:

* Linux rendszerű számítógépeken az SSH használata. A parancssorba írja be:
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Írja be a jelszót.
* Windows rendszerű számítógépeken a PuTTY használata. Ha nincs telepítve, töltse le a [PuTTY letöltési oldalát][PuTTYDownload].
  
    Mentés **putty.exe** könyvtárra a számítógépen. Nyisson meg egy parancssort, keresse meg azt a mappát, és futtassa **putty.exe**.
  
    Írja be a gazdagép nevét, például a "testlinuxvm.cloudapp.net", és írja be a "22" a **Port**.
  
    ![PuTTY képernyő][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Frissítse a virtuális gép (nem kötelező)
1. Miután a virtuális gép csatlakozik, rendszer frissítések és javítások igény szerint telepítheti. A frissítés futtatásához írja be:
   
    `$ sudo zypper update`
2. Válassza ki **szoftver**, majd **Online frissítés** rendelkezésre álló frissítések listáját. Válassza ki **elfogadás** a telepítés megkezdéséhez és javításokat minden új érhető el (kivéve a választható megfelelően).
3. Telepítés után válassza ki a **Befejezés**.  A rendszer már naprakészek legyenek.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
