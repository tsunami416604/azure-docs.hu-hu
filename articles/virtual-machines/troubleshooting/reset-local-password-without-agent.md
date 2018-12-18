---
title: Azure-ügynök nélkül egy helyi Windows-jelszó alaphelyzetbe állítása |} A Microsoft Docs
description: Ha az Azure-vendégügynök nincs telepítve vagy nem működik a virtuális gép alaphelyzetbe állítása a helyi Windows felhasználói fiók jelszava
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 31e675b101d903af5dd4a07fee3bc56fbc3353d9
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412788"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Kapcsolat nélküli az Azure virtuális gép helyi Windows-jelszó visszaállítása
A virtuális gépek az Azure-ban a helyi Windows-jelszó alaphelyzetbe állíthatja a [az Azure portal vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) biztosított az Azure Vendég ügynök van telepítve. Ez a módszer az elsődleges módja egy Azure virtuális gép a jelszó alaphelyzetbe állítása. Ha problémák merülnek fel a az Azure Vendég ügynök nem válaszol, vagy egyéni kép feltöltése után telepítése meghiúsul, manuálisan egy Windows-jelszó alaphelyzetbe állítása. Ez a cikk részletesen bemutatja egy helyi fiók jelszavának alaphelyzetbe a forrás operációs rendszer virtuális lemez egy másik virtuális géphez való csatlakoztatásával. A jelen cikkben ismertetett lépések Windows rendszerű tartományvezérlők nem vonatkoznak. 

> [!WARNING]
> Csak végső megoldásként használja a ezt a folyamatot. Mindig próbálja meg alaphelyzetbe a jelszót a [az Azure portal vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) első.
> 
> 

## <a name="overview-of-the-process"></a>A folyamat áttekintése
A core lépések végrehajtásához a helyi jelszó-visszaállításra egy Windows virtuális gép az Azure-ban nem lehet hozzáférni az Azure-vendégügynök, amikor a következőképpen történik:

* A forrásoldali virtuális gép törlése. A virtuális lemezek jelennek meg.
* A forrás virtuális gép operációsrendszer-lemez csatolása az ugyanazon a helyen, az Azure-előfizetésen belül egy másik virtuális géphez. Ez a virtuális gép a hibaelhárító virtuális Géphez nevezzük.
* Hozzon létre néhány konfigurációs fájlokat a forrás virtuális gép operációsrendszer-lemezt a hibaelhárító virtuális Géphez segítségével.
* Válassza le a virtuális gép operációsrendszer-lemez a hibaelhárító virtuális gépről.
* A Resource Manager-sablon használatával hozzon létre egy virtuális Gépet az eredeti virtuális lemez segítségével.
* Az új virtuális gép elindul, amikor a létrehozott konfigurációs fájl frissítése a szükséges felhasználói jelszót.

## <a name="detailed-steps"></a>Részletes lépések

> [!NOTE]
> A lépések Windows rendszerű tartományvezérlők nem vonatkoznak. Csak önálló kiszolgáló vagy a kiszolgálót, amely tagja egy tartománynak működik.
> 
> 

Mindig próbálja meg alaphelyzetbe a jelszót a [az Azure portal vagy az Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) előtt az alábbi lépéseket. Ellenőrizze, hogy rendelkezik a virtuális gép biztonsági másolatának megkezdése előtt. 

1. Törölje az érintett virtuális gépek az Azure Portalon. Csak a virtuális gép törlésével törli a metaadatok, a hivatkozás a virtuális gép Azure-ban. A virtuális lemezek megmaradnak a virtuális gép törlésekor:
   
   * Válassza ki a virtuális gép az Azure Portalon, kattintson *törlése*:
     
     ![Meglévő virtuális gép törlése](./media/reset-local-password-without-agent/delete_vm.png)
2. Csatlakoztassa a forrás virtuális gép operációsrendszer-lemezt a hibaelhárító virtuális géphez. A hibaelhárító virtuális Géphez a forrás virtuális gép operációsrendszer-lemez ugyanabban a régióban kell lennie (például `West US`):
   
   * Válassza ki a hibaelhárító virtuális Géphez az Azure Portalon. Kattintson a *lemezek* | *csatolása meglévő*:
     
     ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Válassza ki *VHD-fájl* , és válassza ki a tárfiókot, amely tartalmazza a forrás virtuális gép:
     
     ![Adattároló fiók kiválasztása](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Válassza ki a forrás-tárolót. A forrás zásobník általában *VHD-k*:
     
     ![Válassza ki a storage-tároló](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Válassza ki a rendszert tartalmazó virtuális merevlemezt csatolni. Kattintson a *kiválasztása* a folyamat befejezéséhez:
     
     ![Válassza ki a forrás virtuális lemez](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. A hibaelhárító virtuális Géphez a távoli asztal használatával csatlakozhat, és győződjön meg arról, a forrás virtuális gép operációsrendszer-lemezének mérete látható:
   
   * Válassza ki a hibaelhárító virtuális Géphez az Azure Portalon, és kattintson a *Connect*.
   * Nyissa meg az RDP-fájlt, amely letölti. Adja meg a felhasználónevet és jelszót, a hibaelhárító virtuális Géphez.
   * A Fájlkezelőben keresse meg a csatlakoztatott adatlemez. Ha a forrásoldali virtuális gép VHD-t a csak a hibaelhárító virtuális Géphez csatlakoztatott lemez, az egyes hatásszintekhez meghajtót kell:
     
     ![Csatlakoztatott adatlemez megtekintése](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Hozzon létre `gpt.ini` a `\Windows\System32\GroupPolicy` a forrás virtuális gép meghajtón (ha létezik a gpt.ini, nevezze át gpt.ini.bak):
   
   > [!WARNING]
   > Győződjön meg arról, hogy ne véletlenül hozzon létre a következő fájlokat a C:\Windows, az operációs rendszer meghajtójának a hibaelhárító virtuális géphez. Hozza létre a következő fájlokat a forrás virtuális gép adatlemezként csatlakoztatott operációsrendszer-meghajtón lévő.
   > 
   > 
   
   * Adja hozzá a következő sorokat, a `gpt.ini` létrehozott fájlba:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Hozzon létre a gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Hozzon létre `scripts.ini` a `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Győződjön meg arról, rejtett mappákba jelennek meg. Szükség esetén hozzon létre a `Machine` vagy `Scripts` mappákat.
   
   * Adja hozzá a következő sorokat a `scripts.ini` létrehozott fájlba:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts.ini létrehozása](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Hozzon létre `FixAzureVM.cmd` a `\Windows\System32` a következő tartalmakat, és cserélje le a `<username>` és `<newpassword>` a saját értékeire:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd létrehozása](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    A virtuális géphez konfigurált összetettségi követelményeknek kell megfelelnie, az új jelszó meghatározásakor.
7. Az Azure Portalon válassza le a lemezt a hibaelhárító virtuális gépről:
   
   * Válassza ki a hibaelhárító virtuális Géphez az Azure Portalon, kattintson *lemezek*.
   * Válassza ki az adatlemezt csatolni a 2. lépésben kattintson *leválasztási*:
     
     ![Lemez leválasztása](./media/reset-local-password-without-agent/detach_disk.png)
8. Mielőtt létrehozna egy virtuális Gépet, szerezze be az URI-t a forrás operációsrendszer-lemez:
   
   * Az Azure Portalon válassza ki a tárfiókot, kattintson a *Blobok*.
   * Válassza ki a tárolót. A forrás zásobník általában *VHD-k*:
     
     ![Válassza ki a naplótárolásifiók-blob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Válassza ki a forrás virtuális gép rendszert tartalmazó virtuális Merevlemezt, majd kattintson a *másolási* megjelenítő gombra a *URL-cím* neve:
     
     ![Lemez másolása URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Virtuális gép létrehozása a forrás virtuális gép operációsrendszer-lemez alapján:
   
   Használata [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) virtuális gép létrehozása speciális virtuális merevlemezből. Kattintson a `Deploy to Azure` gombra kattintva nyissa meg az Azure Portalon a sablonalapú adatokkal feltöltve az Ön számára.
   * Ha meg szeretné őrizni a korábbi beállításokat a virtuális gép, jelölje be *szerkesztési sablon* biztosít a meglévő virtuális hálózat, alhálózat, hálózati adapter vagy nyilvános IP-cím.
   * Az a `OSDISKVHDURI` paraméter beviteli mező, illessze be az előző lépésben beszerzése a forrás virtuális merevlemez URI azonosítója:
     
     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Miután az új virtuális gép fut, csatlakozzon a virtuális Géphez a távoli asztal használata a megadott új jelszó a `FixAzureVM.cmd` parancsfájlt.
11. Az új virtuális gépre a távoli munkamenet távolítsa el a következő fájlokat a környezet törlése:
    
    * A %windir%\System32
      * Távolítsa el a FixAzureVM.cmd
    * A %windir%\System32\GroupPolicy\Machine\
      * Távolítsa el a scripts.ini
    * A %windir%\System32\GroupPolicy
      * Távolítsa el a gpt.ini (Ha a gpt.ini létezett, és átnevezte gpt.ini.bak, nevezze át a gpt.ini vissza a .bak-fájl)

## <a name="next-steps"></a>További lépések
Ha továbbra is tud kapcsolódni a távoli asztal használatával, tekintse meg a [RDP – hibaelhárítási útmutató](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A [részletes hibaelhárítási útmutató RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) megvizsgálja a hibaelhárítás lépéseit helyett módszereket. Emellett [nyisson meg egy Azure-támogatáskérést](https://azure.microsoft.com/support/options/) gyakorlati segítségért.

