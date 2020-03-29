---
title: Helyi Windows-jelszó alaphelyzetbe állítása Azure-ügynök nélkül | Microsoft dokumentumok
description: A helyi Windows felhasználói fiók jelszavának alaphelyzetbe állítása, ha az Azure vendégügynök nincs telepítve vagy nem működik a virtuális számítógépen
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921623"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure-beli virtuális gép helyi Windows-jelszavának visszaállítása kapcsolat nélküli üzemmódban
Az Azure Portalon vagy az Azure PowerShellben egy virtuális gép helyi Windows-jelszavát alaphelyzetbe állíthatja az [Azure-portálon vagy](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure PowerShellben, feltéve, hogy az Azure vendégügynök telepítve van. Ez a módszer az elsődleges módja az Azure-beli virtuális gépek jelszavának alaphelyzetbe állítása. Ha problémákat tapasztal az Azure vendégügynök nem válaszol, vagy nem telepíti az egyéni lemezkép feltöltése után, manuálisan alaphelyzetbe állíthatja a Windows jelszavát. Ez a cikk részletezi, hogyan lehet alaphelyzetbe állítani a helyi fiók jelszavát a forrás operációsrendszer virtuális lemezének egy másik virtuális géphez csatolásával. A cikkben ismertetett lépések nem vonatkoznak a Windows tartományvezérlőkre. 

> [!WARNING]
> Ezt a módszert csak végső megoldásként használja. Mindig próbálja meg alaphelyzetbe állítani a jelszót az [Azure Portalon vagy](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az Azure PowerShell először.

## <a name="overview-of-the-process"></a>A folyamat áttekintése
Az Azure-beli vendégügynök helyi jelszó-alaphelyzetbe állításának alapvető lépései az Azure-beli vendégügynök höz való hozzáférés tekintetében a következők:

1. Állítsa le az érintett virtuális gép.
1. Hozzon létre egy pillanatképet a virtuális gép operációs rendszer lemezéhez.
1. Hozzon létre egy másolatot az operációs rendszer lemezéről a pillanatképből.
1. Csatolja és csatolja a másolt operációsrendszer-lemezt egy másik Windows virtuális géphez, majd hozzon létre néhány konfigurációs fájlt a lemezen. A fájlok segítenek a jelszó visszaállításában.
1. Válassza le és válassza le a másolt operációsrendszer-lemezt a hibaelhárítási virtuális gépről.
1. Cserélje ki az operációs rendszer lemezét az érintett virtuális gépre.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>A virtuális gép részletes lépései az Erőforrás-kezelő telepítésével

> [!NOTE]
> A lépések nem vonatkoznak a Windows tartományvezérlőkre. Csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely egy tartomány tagja.

Mindig próbálja meg alaphelyzetbe állítani a jelszót az Azure Portalon vagy az [Azure PowerShellben,](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) mielőtt megpróbálna a következő lépéseket. Győződjön meg arról, hogy a virtuális gép a kezdés előtt egy biztonsági másolatot.

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibaelhárítási virtuális géphez. További információt a [Windows-virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure Portalon keresztül idomuló virtuális géphez való csatolásával](troubleshoot-recovery-disks-portal-windows.md)című témakörben talál.
2. Csatlakozzon a hibaelhárítási virtuális géphez a Távoli asztal használatával.
3. `\Windows\System32\GroupPolicy` Hozzon létre `gpt.ini` a forrás virtuális gép meghajtóján (ha gpt.ini létezik, nevezze át gpt.ini.bak) értékre):
   
   > [!WARNING]
   > Győződjön meg arról, hogy véletlenül nem hozza létre a következő fájlokat a C:\Windows, az operációs rendszer meghajtója a hibaelhárítás virtuális gép. Hozza létre a következő fájlokat az operációs rendszer meghajtóján a forrás virtuális gép, amely adatlemezként van csatolva.
   
   * Adja hozzá a `gpt.ini` következő sorokat a létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Gpt.ini létrehozása](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. `scripts.ini` Létrehozás `\Windows\System32\GroupPolicy\Machines\Scripts\`a területen Győződjön meg arról, hogy a rejtett mappák megjelennek. Szükség esetén hozza `Machine` `Scripts` létre a vagy mappákat.
   
   * Adja hozzá a `scripts.ini` létrehozott fájlt a következő sorokhoz:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Parancsfájlok létrehozása.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `FixAzureVM.cmd` Hozzon `\Windows\System32` létre a következő `<username>` `<newpassword>` tartalommal, helyettesítve és a saját értékeivel:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd létrehozása](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Az új jelszó megadásakor meg kell felelnie a virtuális gép konfigurált jelszó-összetettségi követelményeinek.

6. Az Azure Portalon válassza le a lemezt a hibaelhárítási virtuális gép.

7. [Módosítsa az érintett virtuális gép operációsrendszer-lemezét.](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)

8. Az új virtuális gép futása után csatlakozzon a virtuális géphez a `FixAzureVM.cmd` Távoli asztal használatával a parancsfájlban megadott új jelszóval.

9. A távoli munkamenetből az új virtuális gépre távolítsa el a következő fájlokat a környezet megtisztításához:
    
    * A következőwebhelyről: %windir%\System32
      * FixAzureVM.cmd eltávolítása
    * A következőből: %windir%\System32\GroupPolicy\Machine\Scripts
      * parancsfájlok eltávolítása.ini
    * A következőwebhelyről: %windir%\System32\GroupPolicy
      * eltávolítás gpt.ini (ha gpt.ini létezett korábban, és átnevezte gpt.ini.bak, nevezze át a .bak fájlt vissza gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>A klasszikus virtuális gép részletes lépései

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> A lépések nem vonatkoznak a Windows tartományvezérlőkre. Csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely egy tartomány tagja.

Mindig próbálja meg alaphelyzetbe állítani a jelszót az Azure Portalon vagy az [Azure PowerShellben,](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) mielőtt megpróbálna a következő lépéseket. Győződjön meg arról, hogy a virtuális gép a kezdés előtt egy biztonsági másolatot. 

1. Törölje az érintett virtuális gép az Azure Portalon. A virtuális gép törlése csak törli a metaadatokat, a virtuális gép hivatkozását az Azure-on belül. A virtuális lemezek megmaradnak a virtuális gép törlésekor:
   
   * Válassza ki a virtuális gép az Azure Portalon, majd kattintson *a Törlés*:
     
     ![Meglévő virtuális gép törlése](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Csatlakoztassa a forrás virtuális gép operációs rendszer lemezét a hibaelhárítási virtuális géphez. A hibaelhárítási virtuális gépnek ugyanabban a régióban kell lennie, `West US`mint a forrásvirtuális gép operációsrendszer-lemezének (például):
   
   1. Válassza ki a hibaelhárítási virtuális gép az Azure Portalon. Kattintson *a Meglévő lemezek* | *csatolása*elemre:
     
      ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Válassza a *VHD-fájlt,* majd válassza ki a forrásvirtuális gépét tartalmazó tárfiókot:
     
      ![Adattároló fiók kiválasztása](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Jelölje be a *Klasszikus tárfiókok megjelenítése jelölőnégyzetet,* majd jelölje ki a forrástárolót. A forrástároló általában *vhds:*
     
      ![Tárolótároló kiválasztása](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Tárolótároló kiválasztása](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Válassza ki a csatolni kívánt operációs rendszer vhd.Select the OS vhd to attach. A folyamat befejezéséhez kattintson a *Kijelölés* gombra:
     
      ![Forrásvirtuális lemez kiválasztása](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. A lemez csatolásához kattintson az Ok gombra

      ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Csatlakozzon a hibaelhárítási virtuális géphez a Távoli asztal használatával, és győződjön meg arról, hogy a forrásvirtuális gép operációsrendszer-lemeze látható:

   1. Válassza ki a hibaelhárítási virtuális gép az Azure Portalon, és kattintson a *Csatlakozás gombra.*

   2. Nyissa meg a letöltést tartalmazó RDP-fájlt. Adja meg a hibaelhárítási virtuális gép felhasználónevét és jelszavát.

   3. A Fájlkezelőben keresse meg a csatolt adatlemezt. Ha a forrás virtuális gép virtuális merevlemeze az egyetlen adatlemez, amely a hibaelhárítási virtuális géphez van csatlakoztatva, akkor az F: meghajtónak kell lennie:
     
      ![Csatolt adatlemez megtekintése](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. `gpt.ini` Hozzon `\Windows\System32\GroupPolicy` létre a forrás virtuális gép `gpt.ini` meghajtóján (ha létezik, nevezze át `gpt.ini.bak`a):
   
   > [!WARNING]
   > Győződjön meg arról, hogy véletlenül nem hozza létre a következő fájlokat a rendszerben, `C:\Windows`az operációs rendszer meghajtóját a hibaelhárítási virtuális gép. Hozza létre a következő fájlokat az operációs rendszer meghajtóján a forrás virtuális gép, amely adatlemezként van csatolva.
   
   * Adja hozzá a `gpt.ini` következő sorokat a létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Gpt.ini létrehozása](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. `scripts.ini` Létrehozás `\Windows\System32\GroupPolicy\Machines\Scripts\`a területen Győződjön meg arról, hogy a rejtett mappák megjelennek. Szükség esetén hozza `Machine` `Scripts` létre a vagy mappákat.
   
   * Adja hozzá a `scripts.ini` létrehozott fájlt a következő sorokhoz:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Parancsfájlok létrehozása.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` Hozzon `\Windows\System32` létre a következő `<username>` `<newpassword>` tartalommal, helyettesítve és a saját értékeivel:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![FixAzureVM.cmd létrehozása](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Az új jelszó megadásakor meg kell felelnie a virtuális gép konfigurált jelszó-összetettségi követelményeinek.

7. Az Azure Portalon válassza le a lemezt a hibaelhárítási virtuális gépről:
   
   1. Válassza ki a hibaelhárítási virtuális gép az Azure Portalon, kattintson *a Lemezek gombra.*
   
   2. A **2.** **Detach**

     ![Lemez leválasztása](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Lemez leválasztása](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Hozzon létre egy virtuális gép a forrás virtuális gép operációs rendszer lemezéről:
   
     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>A virtuális gép létrehozása élmény befejezése

1. Az új virtuális gép futása után csatlakozzon a virtuális géphez a `FixAzureVM.cmd` Távoli asztal használatával a parancsfájlban megadott új jelszóval.

2. A távoli munkamenetből az új virtuális gépre távolítsa el a következő fájlokat a környezet megtisztításához:
    
    * Honnan`%windir%\System32`
      * Eltávolít`FixAzureVM.cmd`
    * Honnan`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Eltávolít`scripts.ini`
    * Honnan`%windir%\System32\GroupPolicy`
      * eltávolítás `gpt.ini` (ha `gpt.ini` korábban már létezett, és `.bak` átnevezte `gpt.ini` `gpt.ini.bak`, nevezze át a fájlt vissza)

## <a name="next-steps"></a>További lépések
Ha továbbra sem tud csatlakozni a Távoli asztal használatával, olvassa el az [RDP hibaelhárítási útmutatóját.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) A [részletes RDP hibaelhárítási útmutató](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a hibaelhárítási módszereket vizsgálja, nem pedig a konkrét lépéseket. Azure-támogatási kérelmet is [megnyithat](https://azure.microsoft.com/support/options/) gyakorlati segítségnyújtásra.
