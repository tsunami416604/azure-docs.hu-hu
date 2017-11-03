---
title: "Azure-ügynök nélkül helyi Windows-jelszó alaphelyzetbe állítása |} Microsoft Docs"
description: "Visszaállítása egy helyi Windows felhasználói fiók jelszavát, amikor az Azure Vendég ügynök nincs telepítve vagy nem működik a virtuális gép"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/07/2017
ms.author: iainfou
ms.openlocfilehash: 880f5e5967298401fc2522124af3746d9906ffa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Helyi Windows-jelszó visszaállítása az Azure virtuális gépekhez
A helyi Windows-jelszó a virtuális gépek az Azure használatával visszaállíthatja a [Azure-portálon vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) megadott Azure Vendég-ügynök telepítve van. Ez a módszer elsődleges módja a jelszó alaphelyzetbe állítása egy Azure virtuális gép. Ha problémák lépnek fel az Azure Vendég ügynöke a vendégügynök nem válaszol, vagy sikertelen egyéni lemezkép feltöltése után telepíteni, manuálisan állítsa vissza a Windows-jelszóval. Ez a cikk részletesen a forrás operációs rendszer virtuális lemez csatolása a másik virtuális gép által a helyi fiók jelszavának visszaállítása. 

> [!WARNING]
> Csak utolsó lehetőségként használja a ezt a folyamatot. Mindig próbálja alaphelyzetbe a jelszót a [Azure-portálon vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) első.
> 
> 

## <a name="overview-of-the-process"></a>A folyamat áttekintése
A core lépések végrehajtásához a helyi jelszót alaphelyzetbe állítani a Windows virtuális gépek Azure-ban, amikor nem érhető el az Azure Vendég ügynöke a következőképpen történik:

* A forrás virtuális gép törlése. A virtuális lemezek jelennek meg.
* A forrás virtuális gép operációsrendszer-lemez csatolása egy másik virtuális gép ugyanazon a helyen belül az Azure-előfizetéshez. Ez a virtuális gép a hibaelhárítás VM nevezzük.
* A hibaelhárítási VM használatával hozhat létre az egyes konfigurációs fájlokat a forrás virtuális gép operációsrendszer-lemez.
* Válassza le a virtuális gép operációsrendszer-lemez a hibaelhárítási virtuális gépről.
* A Resource Manager-sablon használatával hozzon létre egy virtuális Gépet az eredeti virtuális lemez segítségével.
* Amikor az új virtuális gép elindul, a konfigurációs fájlt hoz létre frissítése a szükséges felhasználói jelszavát.

## <a name="detailed-steps"></a>Részletes lépések
Mindig próbálja alaphelyzetbe a jelszót a [Azure-portálon vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) előtt az alábbi lépéseket. Győződjön meg arról, hogy a virtuális gép biztonsági másolata megkezdése előtt. 

1. Törölje a fertőzött virtuális Gépet az Azure portálon. Csak a virtuális gép törlésekor törlődnek a metaadatokat, a hivatkozás a virtuális gép Azure-ban. A virtuális lemezek megőrzi a virtuális gép törlésekor:
   
   * Jelölje ki azt a virtuális Gépet az Azure portálon *törlése*:
     
     ![Meglévő virtuális gép törlése](./media/reset-local-password-without-agent/delete_vm.png)
2. A forrás virtuális gép operációsrendszer-lemez csatolása a hibaelhárítási virtuális Gépet. A hibaelhárítási virtuális gép és a forrás virtuális gép operációsrendszer-lemez ugyanabban a régióban kell lennie (például `West US`):
   
   * Válassza ki a hibaelhárítási virtuális Gépet az Azure portálon. Kattintson a *lemezek* | *Csatolás meglévő*:
     
     ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Válassza ki *VHD-fájl* , és válassza ki a tárfiók, amely tartalmazza a virtuális gép – forrásként:
     
     ![Adattároló fiók kiválasztása](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     A forrás-tároló választása. A forrás tároló van általában *VHD-k*:
     
     ![Válassza ki a tároló](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Válassza ki az operációs rendszer a csatolása. Kattintson a *válasszon* a folyamat befejezéséhez:
     
     ![Válassza ki a forrás virtuális lemez](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Csatlakoztassa a távoli asztali kapcsolattal hibaelhárítási virtuális Gépet, és győződjön meg arról, a forrás virtuális gép operációsrendszer-lemez látható:
   
   * Válassza ki a hibaelhárítási virtuális Gépet az Azure portálon, és kattintson a *Connect*.
   * Nyissa meg az RDP-fájl, amely letölti. Adja meg a felhasználónevet és jelszót a hibaelhárítási virtuális gép.
   * A Fájlkezelőben keresse meg a csatolt adatlemeze. Ha a forrás virtuális gép virtuális merevlemez a hibaelhárítási virtuális Géphez csatlakozik, egyetlen adatlemeze, meg kell a F: meghajtó:
     
     ![Csatolt adatlemez megtekintése](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Hozzon létre `gpt.ini` a `\Windows\System32\GroupPolicy` a forrás virtuális gép meghajtón (ha létezik a gpt.ini, nevezze át gpt.ini.bak):
   
   > [!WARNING]
   > Győződjön meg arról, hogy véletlenül hozzon létre a következő fájlokat a C:\Windows, az operációs rendszer meghajtó a hibaelhárítási virtuális gép. Hozza létre a következő fájlokat a forrás virtuális gép adatok lemezként csatolt OS meghajtóban.
   > 
   > 
   
   * Adja hozzá a következő sorokat a `gpt.ini` létrehozott fájlt:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Gpt.ini létrehozása](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Hozzon létre `scripts.ini` a `\Windows\System32\GroupPolicy\Machine\Scripts`. Győződjön meg arról, rejtett mappákba jelennek meg. Szükség esetén hozzon létre a `Machine` vagy `Scripts` mappák.
   
   * Adja hozzá a következő sorokat a `scripts.ini` létrehozott fájlt:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts.ini létrehozása](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Hozzon létre `FixAzureVM.cmd` a `\Windows\System32` a következő tartalmakat, lecserélve a `<username>` és `<newpassword>` saját értékekkel:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add

    ```

    ![FixAzureVM.cmd létrehozása](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Az új jelszó definiálásakor meg kell felelnie a virtuális géphez beállított összetettségi követelményeknek.
7. Azure-portálon válassza le a lemezt a hibaelhárítási virtuális Gépet:
   
   * Jelölje ki azt a hibaelhárítási virtuális Gépet az Azure portálon *lemezek*.
   * Válassza ki az adatlemezt csatolni 2. lépésben kattintson *leválasztási*:
     
     ![Leválasztja a lemezt](./media/reset-local-password-without-agent/detach_disk.png)
8. Mielőtt létrehozna egy virtuális Gépet, szerezze be az URI-t a forrás operációsrendszer-lemez:
   
   * Válassza ki a tárfiókot az Azure portálon, kattintson a *Blobok*.
   * Jelölje ki a tárolót. A forrás tároló van általában *VHD-k*:
     
     ![Válassza ki a tárolási fiók blob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Válassza ki a forrás virtuális gép operációs rendszer virtuális Merevlemezt, majd kattintson a *másolási* megjelenítő gombra a *URL-cím* nevét:
     
     ![Lemezmásolás URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. A forrás virtuális gép operációsrendszer-lemez a virtuális gép létrehozása:
   
   * Használjon [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) speciális virtuális Merevlemezt a virtuális gép létrehozása. Kattintson a `Deploy to Azure` gombra kattintva nyissa meg az Azure-portálon a sablonalapú adatokkal feltöltve az olyan meg.
   * Ha meg szeretné őrizni a korábbi beállításokat a virtuális gép számára, jelölje be *Szerkesztés sablon* a meglévő virtuális hálózatot, alhálózatot, hálózati adapter vagy nyilvános IP-cím számára.
   * Az a `OSDISKVHDURI` paraméter beviteli mezőt, és beillesztés, az előző lépésben beszerzése a forrás VHD URI:
     
     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Miután az új virtuális gép fut, csatlakoztassa a virtuális Gépet az új jelszóval, amelyet a távoli asztali kapcsolattal a `FixAzureVM.cmd` parancsfájl.
11. Az új virtuális géphez a távoli munkamenetet távolítsa el a környezet karbantartása a következő fájlokat:
    
    * A %windir%\System32
      * Távolítsa el a FixAzureVM.cmd
    * A %windir%\System32\GroupPolicy\Machine\
      * Távolítsa el a scripts.ini
    * A %windir%\System32\GroupPolicy
      * Távolítsa el a gpt.ini (Ha a gpt.ini létezett, és átnevezte gpt.ini.bak, nevezze át a .bak-fájl biztonsági gpt.ini)

## <a name="next-steps"></a>Következő lépések
Ha továbbra is tud kapcsolódni a távoli asztal, tekintse meg a [RDP hibaelhárítási útmutató](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A [részletes hibaelhárítási útmutatója RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ellenőrzi, hogy bizonyos lépésekre helyett módszerek hibaelhárítás. Emellett [az Azure támogatási kérést nyithat](https://azure.microsoft.com/support/options/) gyakorlati segítségért.

