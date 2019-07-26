---
title: Helyi Windows-jelszó alaphelyzetbe állítása Azure-ügynök nélkül | Microsoft Docs
description: Helyi Windows-felhasználói fiók jelszavának alaphelyzetbe állítása, ha az Azure Guest Agent ügynöke nincs telepítve vagy nem működik egy virtuális gépen
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 5354ebc8c25125f86a0208382d176c84372cadc1
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369714"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Helyi Windows-jelszó visszaállítása az Azure-beli virtuális géphez offline
Alaphelyzetbe állíthatja az Azure-beli virtuális gép helyi Windows-jelszavát a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , ha telepítve van az Azure Guest Agent ügynök. Ez a módszer az Azure-beli virtuális gépek jelszavának alaphelyzetbe állításának elsődleges módja. Ha az Azure vendég ügynökével kapcsolatos problémákba ütközik, vagy ha az egyéni rendszerkép feltöltése után nem sikerül telepíteni, manuálisan is visszaállíthatja a Windows-jelszót. Ez a cikk részletesen ismerteti a helyi fiók jelszavának alaphelyzetbe állítását úgy, hogy a forrás operációs rendszer virtuális lemezét egy másik virtuális géphez csatolja. A cikkben ismertetett lépések nem vonatkoznak a Windows-tartományvezérlőkre. 

> [!WARNING]
> Ezt a módszert csak végső megoldásként használja. Mindig próbálkozzon a jelszó alaphelyzetbe állításával a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával.

## <a name="overview-of-the-process"></a>A folyamat áttekintése
Az Azure-beli Windows rendszerű virtuális gépek helyi jelszavas alaphelyzetbe állításának alapvető lépései, ha az Azure Guest agenthez nem fér hozzá a következő:

1. Törölje a forrás virtuális gépet. A virtuális lemezek megmaradnak.

2. Csatlakoztassa a forrás virtuális gép operációsrendszer-lemezét egy másik virtuális géphez ugyanazon a helyen az Azure-előfizetésen belül. Ezt a virtuális gépet a hibaelhárítási virtuális gépnek nevezzük.

3. A hibaelhárítási virtuális gép használatával hozzon létre néhány konfigurációs fájlt a forrás virtuális gép operációsrendszer-lemezén.

4. Válassza le a virtuális gép operációsrendszer-lemezét a hibaelhárítási virtuális gépről.

5. Egy Resource Manager-sablon használatával hozzon létre egy virtuális GÉPET az eredeti virtuális lemezzel.

6. Az új virtuális gép indításakor a létrehozott konfigurációs fájlok frissítik a szükséges felhasználó jelszavát.

> [!NOTE]
> A következő folyamatokat automatizálhatja:
>
> - A hibaelhárítási virtuális gép létrehozása
> - Az operációsrendszer-lemez csatolása
> - Az eredeti virtuális gép újbóli létrehozása
> 
> Ehhez használja az [Azure virtuális gépek helyreállítási parancsfájljait](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Ha úgy dönt, hogy az Azure virtuális gép helyreállítási parancsfájljait használja, az alábbi eljárást használhatja a "részletes lépések" szakaszban:
> 1. Az 1. és a 2. lépést kihagyva a parancsfájlok segítségével csatlakoztassa az érintett virtuális gép operációsrendszer-lemezét egy helyreállítási virtuális géphez.
> 2. A mérséklések alkalmazásához kövesse a 3 – 6. lépést.
> 3. A virtuális gép újraépítéséhez a parancsfájlok használatával ugorja át a 7 – 9. lépést.
> 4. Kövesse a 10. és a 11. lépést.

## <a name="detailed-steps-for-resource-manager"></a>A Resource Manager részletes lépései

> [!NOTE]
> A lépések nem vonatkoznak a Windows rendszerű tartományvezérlőkön. Ez csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely tagja egy tartománynak.

A következő lépések elvégzése előtt mindig próbálja meg alaphelyzetbe állítani a jelszót a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával. Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik biztonsági másolattal a virtuális gépről. 

1. Törölje az érintett virtuális gépet Azure Portal. A virtuális gép törlése csak a metaadatokat, az Azure-ban található virtuális gép hivatkozását törli. A virtuális lemezeket a rendszer a virtuális gép törlésekor megőrzi:
   
   * Válassza ki a virtuális gépet a Azure Portalban, majd kattintson a *Törlés*gombra:
     
     ![Meglévő virtuális gép törlése](./media/reset-local-password-without-agent/delete-vm.png)

2. Csatolja a forrás virtuális gép operációsrendszer-lemezét a hibaelhárítási virtuális géphez. A hibaelhárítási virtuális gépnek ugyanabban a régióban kell lennie, mint a forrás virtuális gép operációsrendszer- `West US`lemezének (például):
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal. Kattintson a *lemezek* | *meglévő csatolása*lehetőségre:
     
     ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks-attach-existing.png)
     
   2. Válassza ki a *VHD-fájlt* , majd válassza ki a forrás virtuális gépet tartalmazó Storage-fiókot:
     
     ![Adattároló fiók kiválasztása](./media/reset-local-password-without-agent/disks-select-storage-account.png)
     
   3. Válassza ki a forrás-tárolót. A forrás tároló általában *VHD*-k:
     
     ![Storage-tároló kiválasztása](./media/reset-local-password-without-agent/disks-select-container.png)
     
   4. Válassza ki a csatolni kívánt operációs rendszert tartalmazó virtuális merevlemezt. A folyamat befejezéséhez kattintson a *kiválasztás* gombra:
     
     ![Forrás virtuális lemez kiválasztása](./media/reset-local-password-without-agent/disks-select-source-vhd.png)

3. Kapcsolódjon a hibaelhárítási virtuális géphez Távoli asztal használatával, és győződjön meg arról, hogy a forrás virtuális gép operációsrendszer-lemeze látható:
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal, majd kattintson a *kapcsolat*gombra.

   2. Nyissa meg a letöltött RDP-fájlt. Adja meg a hibaelhárítási virtuális gép felhasználónevét és jelszavát.

   3. A Fájlkezelőben keresse meg a csatlakoztatott adatlemezt. Ha a forrás virtuális gép VHD-je az egyetlen, a hibaelhárítási virtuális géphez csatolt adatlemez, az F: meghajtó:
     
     ![Csatolt adatlemez megtekintése](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer.png)

4. Létrehozás `gpt.ini`a forrás virtuális gép meghajtóján (ha a GPT. ini létezik, nevezze át a GPT. ini. bak névre): `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Győződjön meg arról, hogy nem véletlenül hozza létre a következő fájlokat a C:\Windows-ban, a hibaelhárítási virtuális gép operációsrendszer-meghajtóján. Hozza létre a következő fájlokat az operációsrendszer-meghajtón a forrásként szolgáló virtuális gép számára, amely adatlemezként van csatolva.
   
   * Adja hozzá a következő sorokat a `gpt.ini` létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT. ini létrehozása](./media/reset-local-password-without-agent/create-gpt-ini.png)

5. Létrehozás `scripts.ini` a `\Windows\System32\GroupPolicy\Machines\Scripts\`alkalmazásban. Győződjön meg arról, hogy a rejtett mappák láthatók. Szükség esetén hozza létre a `Machine` vagy `Scripts` a mappákat.
   
   * Adja hozzá a következő sorokat `scripts.ini` a létrehozott fájlhoz:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini létrehozása](./media/reset-local-password-without-agent/create-scripts-ini.png)

6. `FixAzureVM.cmd` Hozzon `\Windows\System32` létre a-ben a következő `<username>` tartalommal, és `<newpassword>` cserélje le a saját értékeit:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Az új jelszó meghatározásakor meg kell felelnie a virtuális gép konfigurált jelszó-bonyolultsági követelményeinek.

7. A Azure Portal válassza le a lemezt a hibaelhárítási virtuális gépről:
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portalon, majd kattintson a *lemezek*elemre.

   2. Válassza ki a 2. lépésben csatolt adatlemezt , majd kattintson a Leválasztás elemre:
     
     ![Lemez leválasztása](./media/reset-local-password-without-agent/detach-disk.png)

8. A virtuális gép létrehozása előtt szerezze be az URI-t a forrás operációsrendszer-lemezre:
   
   1. Válassza ki a Storage-fiókot a Azure Portalban , majd kattintson a Blobok elemre.

   2. Válassza ki a tárolót. A forrás tároló általában *VHD*-k:
     
     ![Storage-fiók blobjának kiválasztása](./media/reset-local-password-without-agent/select-storage-details.png)
     
   3. Válassza ki a forrásként szolgáló virtuális gép operációs rendszerének VHD-jét, és kattintson az *URL-cím* neve melletti *Másolás* gombra:
     
     ![Lemez URI-ja másolása](./media/reset-local-password-without-agent/copy-source-vhd-uri.png)

9. Hozzon létre egy virtuális gépet a forrás virtuális gép operációsrendszer-lemezéről:
   
   1. [Ezzel a Azure Resource Manager sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) létrehozhat egy virtuális gépet egy speciális VHD-ből. `Deploy to Azure` A gombra kattintva megnyithatja a Azure Portal a sablonban megjelenő részletekkel.

   2. Ha meg szeretné őrizni a virtuális gép összes korábbi beállítását, válassza a *Sablon szerkesztése* lehetőséget a meglévő VNet, alhálózat, hálózati adapter vagy nyilvános IP-cím megadásához.

   3. `OSDISKVHDURI` A paraméter szövegmezőbe illessze be a forrás VHD-jét az előző lépésben beolvasott értékre:
     
     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create-new-vm-from-template.png)

10. Az új virtuális gép futása után kapcsolódjon a virtuális géphez távoli asztal használatával a `FixAzureVM.cmd` parancsfájlban megadott új jelszóval.

11. A távoli munkamenetből az új virtuális gépre, távolítsa el a következő fájlokat a környezet tisztításához:
    
    * A%windir%\System32ből
      * remove FixAzureVM.cmd
    * %Windir%\System32\GroupPolicy\Machine\Scripts
      * a scripts. ini fájl eltávolítása
    * %Windir%\System32\GroupPolicy
      * távolítsa el a GPT. ini fájlt (ha a GPT. ini korábban már létezett, és átnevezte a GPT. ini. bak névre, nevezze át a. bak fájlt a GPT. ini fájlba)

## <a name="detailed-steps-for-classic-vm"></a>Klasszikus virtuális gép részletes lépései

> [!NOTE]
> A lépések nem vonatkoznak a Windows rendszerű tartományvezérlőkön. Ez csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely tagja egy tartománynak.

A következő lépések elvégzése előtt mindig próbálja meg alaphelyzetbe állítani a jelszót a [Azure Portal vagy Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) használatával. Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik biztonsági másolattal a virtuális gépről. 

1. Törölje az érintett virtuális gépet Azure Portal. A virtuális gép törlése csak a metaadatokat, az Azure-ban található virtuális gép hivatkozását törli. A virtuális lemezeket a rendszer a virtuális gép törlésekor megőrzi:
   
   * Válassza ki a virtuális gépet a Azure Portalon, majd kattintson a *Törlés*gombra:
     
     ![Meglévő virtuális gép törlése](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Csatolja a forrás virtuális gép operációsrendszer-lemezét a hibaelhárítási virtuális géphez. A hibaelhárítási virtuális gépnek ugyanabban a régióban kell lennie, mint a forrás virtuális gép operációsrendszer- `West US`lemezének (például):
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal. Kattintson a *lemezek* | *meglévő csatolása*lehetőségre:
     
      ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Válassza ki a *VHD-fájlt* , majd válassza ki a forrás virtuális gépet tartalmazó Storage-fiókot:
     
      ![Adattároló fiók kiválasztása](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Jelölje be a *klasszikus Storage-fiókok megjelenítése*jelölőnégyzetet, majd válassza ki a forrás-tárolót. A forrás tároló általában *VHD*-k:
     
      ![Storage-tároló kiválasztása](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Storage-tároló kiválasztása](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Válassza ki a csatolni kívánt operációs rendszert tartalmazó virtuális merevlemezt. A folyamat befejezéséhez kattintson a *kiválasztás* gombra:
     
      ![Forrás virtuális lemez kiválasztása](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. A lemez csatolásához kattintson az OK gombra.

      ![Meglévő lemez csatolása](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Kapcsolódjon a hibaelhárítási virtuális géphez Távoli asztal használatával, és győződjön meg arról, hogy a forrás virtuális gép operációsrendszer-lemeze látható:

   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal, majd kattintson a *kapcsolat*gombra.

   2. Nyissa meg a letöltött RDP-fájlt. Adja meg a hibaelhárítási virtuális gép felhasználónevét és jelszavát.

   3. A Fájlkezelőben keresse meg a csatlakoztatott adatlemezt. Ha a forrás virtuális gép VHD-je az egyetlen, a hibaelhárítási virtuális géphez csatolt adatlemez, az F: meghajtó:
     
      ![Csatolt adatlemez megtekintése](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Létrehozás a forrás virtuális gép meghajtóján ( `gpt.ini.bak`ha `gpt.ini` létezik, nevezze át): `gpt.ini` `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Győződjön meg arról, hogy nem véletlenül hozza létre a következő fájlokat `C:\Windows`a alkalmazásban a hibaelhárítási virtuális gép operációsrendszer-meghajtóján. Hozza létre a következő fájlokat az operációsrendszer-meghajtón a forrásként szolgáló virtuális gép számára, amely adatlemezként van csatolva.
   
   * Adja hozzá a következő sorokat a `gpt.ini` létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT. ini létrehozása](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Létrehozás `scripts.ini` a `\Windows\System32\GroupPolicy\Machines\Scripts\`alkalmazásban. Győződjön meg arról, hogy a rejtett mappák láthatók. Szükség esetén hozza létre a `Machine` vagy `Scripts` a mappákat.
   
   * Adja hozzá a következő sorokat `scripts.ini` a létrehozott fájlhoz:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini létrehozása](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` Hozzon `\Windows\System32` létre a-ben a következő `<username>` tartalommal, és `<newpassword>` cserélje le a saját értékeit:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Az új jelszó meghatározásakor meg kell felelnie a virtuális gép konfigurált jelszó-bonyolultsági követelményeinek.

7. A Azure Portal válassza le a lemezt a hibaelhárítási virtuális gépről:
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portalon, majd kattintson a *lemezek*elemre.
   
   2. Válassza ki a 2. lépésben csatolt adatlemezt, kattintson a *Leválasztás:* , majd *az OK*gombra.

     ![Lemez leválasztása](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Lemez leválasztása](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Hozzon létre egy virtuális gépet a forrás virtuális gép operációsrendszer-lemezéről:
   
     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Virtuális gép létrehozása sablonból](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>A virtuális gép létrehozási élményének befejezése

1. Az új virtuális gép futása után kapcsolódjon a virtuális géphez távoli asztal használatával a `FixAzureVM.cmd` parancsfájlban megadott új jelszóval.

2. A távoli munkamenetből az új virtuális gépre, távolítsa el a következő fájlokat a környezet tisztításához:
    
    * A`%windir%\System32`
      * eltávolítása`FixAzureVM.cmd`
    * A`%windir%\System32\GroupPolicy\Machine\Scripts`
      * eltávolítása`scripts.ini`
    * A`%windir%\System32\GroupPolicy`
      * Távolítsa `gpt.ini` el `gpt.ini` (ha korábban létezett, és `gpt.ini.bak`átnevezte a-re `gpt.ini`), `.bak` nevezze át a fájlt a (z) névre.

## <a name="next-steps"></a>További lépések
Ha továbbra sem tud kapcsolatot létesíteni Távoli asztal használatával, tekintse meg az [RDP-hibaelhárítási útmutatót](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Az [RDP-hibaelhárítás részletes útmutatója](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az egyes lépések helyett a hibaelhárítási módszereket vizsgálja. [Egy Azure-támogatási kérést](https://azure.microsoft.com/support/options/) is megnyithat gyakorlati segítségért.
