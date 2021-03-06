---
title: Helyi Windows-jelszó alaphelyzetbe állítása Azure-ügynök nélkül | Microsoft Docs
description: Helyi Windows-felhasználói fiók jelszavának alaphelyzetbe állítása, ha az Azure Guest Agent ügynöke nincs telepítve vagy nem működik egy virtuális gépen
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
ms.openlocfilehash: 4cec8f77cacc5d3492dd6a5f8a8baa060f910763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650596"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure-beli virtuális gép helyi Windows-jelszavának visszaállítása kapcsolat nélküli üzemmódban
Alaphelyzetbe állíthatja az Azure-beli virtuális gép helyi Windows-jelszavát a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , ha telepítve van az Azure Guest Agent ügynök. Ez a módszer az Azure-beli virtuális gépek jelszavának alaphelyzetbe állításának elsődleges módja. Ha az Azure vendég ügynökével kapcsolatos problémákba ütközik, vagy ha az egyéni rendszerkép feltöltése után nem sikerül telepíteni, manuálisan is visszaállíthatja a Windows-jelszót. Ez a cikk részletesen ismerteti a helyi fiók jelszavának alaphelyzetbe állítását úgy, hogy a forrás operációs rendszer virtuális lemezét egy másik virtuális géphez csatolja. A cikkben ismertetett lépések nem vonatkoznak a Windows-tartományvezérlőkre. 

> [!WARNING]
> Ezt a módszert csak végső megoldásként használja. Mindig próbálkozzon a jelszó alaphelyzetbe állításával a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával.

## <a name="overview-of-the-process"></a>A folyamat áttekintése
Az Azure-beli Windows rendszerű virtuális gépek helyi jelszavas alaphelyzetbe állításának alapvető lépései, ha az Azure Guest agenthez nem fér hozzá a következő:

1. Állítsa le az érintett virtuális gépet.
1. Hozzon létre egy pillanatképet a virtuális gép operációsrendszer-lemezéhez.
1. Hozzon létre egy másolatot az operációsrendszer-lemezről a pillanatképből.
1. Csatlakoztassa és csatlakoztassa a másolt operációsrendszer-lemezt egy másik Windows rendszerű virtuális géphez, majd hozzon létre néhány konfigurációs fájlt a lemezen. A fájlok segítségével alaphelyzetbe állíthatja a jelszót.
1. Válassza le és válassza le a másolt operációsrendszer-lemezt a hibaelhárítási virtuális gépről.
1. Cserélje le az érintett virtuális gép operációsrendszer-lemezét.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>A virtuális gép részletes lépései a Resource Manager-alapú üzembe helyezéssel

> [!NOTE]
> A lépések nem vonatkoznak a Windows rendszerű tartományvezérlőkön. Ez csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely tagja egy tartománynak.

A következő lépések elvégzése előtt mindig próbálja meg alaphelyzetbe állítani a jelszót a [Azure Portal vagy Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával. Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik biztonsági másolattal a virtuális gépről.

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről, hozzon létre egy lemezt a pillanatképből, majd csatolja a lemezt egy hibakeresési virtuális géphez. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával a Azure Portal használatával](troubleshoot-recovery-disks-portal-windows.md).
2. Kapcsolódjon a hibaelhárítási virtuális géphez Távoli asztal használatával.
3. Hozza létre a `gpt.ini` `\Windows\System32\GroupPolicy` t a forrás virtuális gép meghajtóján (ha gpt.ini létezik, nevezze át gpt.ini. bak névre):
   
   > [!WARNING]
   > Győződjön meg arról, hogy nem véletlenül hozza létre a következő fájlokat a C:\Windows-ban, a hibaelhárítási virtuális gép operációsrendszer-meghajtóján. Hozza létre a következő fájlokat az operációsrendszer-meghajtón a forrásként szolgáló virtuális gép számára, amely adatlemezként van csatolva.
   
   * Adja hozzá a következő sorokat a `gpt.ini` létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

4. Létrehozás `scripts.ini` a alkalmazásban `\Windows\System32\GroupPolicy\Machine\Scripts\` . Győződjön meg arról, hogy a rejtett mappák láthatók. Szükség esetén hozza létre a `Machine` vagy a `Scripts` mappákat. 
   
   * Adja hozzá a következő sorokat a `scripts.ini` létrehozott fájlhoz:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg." <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
   
    Az új jelszó meghatározásakor meg kell felelnie a virtuális gép konfigurált jelszó-bonyolultsági követelményeinek.

6. A Azure Portalban válassza le a lemezt a hibaelhárítási virtuális gépről.

7. [Módosítsa az érintett virtuális gép operációsrendszer-lemezét](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Az új virtuális gép futása után kapcsolódjon a virtuális géphez Távoli asztal használatával a parancsfájlban megadott új jelszóval `FixAzureVM.cmd` .

9. A távoli munkamenetből az új virtuális gépre, távolítsa el a következő fájlokat a környezet tisztításához:
    
    * %Windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * a FixAzureVM. cmd fájl eltávolítása
    * %Windir%\System32\GroupPolicy\Machine\Scripts
      * scripts.ini eltávolítása
    * %Windir%\System32\GroupPolicy
      * távolítsa el gpt.ini (ha gpt.ini létezett, és átnevezte gpt.ini. bak névre, nevezze át a. bak fájlt gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Klasszikus virtuális gép részletes lépései

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> A lépések nem vonatkoznak a Windows rendszerű tartományvezérlőkön. Ez csak önálló kiszolgálón vagy olyan kiszolgálón működik, amely tagja egy tartománynak.

A következő lépések elvégzése előtt mindig próbálja meg alaphelyzetbe állítani a jelszót a [Azure Portal vagy Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) használatával. Mielőtt elkezdené, győződjön meg róla, hogy rendelkezik biztonsági másolattal a virtuális gépről. 

1. Törölje az érintett virtuális gépet Azure Portal. A virtuális gép törlése csak a metaadatokat, az Azure-ban található virtuális gép hivatkozását törli. A virtuális lemezeket a rendszer a virtuális gép törlésekor megőrzi:
   
   * Válassza ki a virtuális gépet a Azure Portalon, majd kattintson a *Törlés*gombra:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

2. Csatolja a forrás virtuális gép operációsrendszer-lemezét a hibaelhárítási virtuális géphez. A hibaelhárítási virtuális gépnek ugyanabban a régióban kell lennie, mint a forrás virtuális gép operációsrendszer-lemezének (például `West US` ):
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal. Kattintson a *lemezek*  |  *meglévő csatolása*lehetőségre:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
     
   2. Válassza ki a *VHD-fájlt* , majd válassza ki a forrás virtuális gépet tartalmazó Storage-fiókot:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
     
   3. Jelölje be a *klasszikus Storage-fiókok megjelenítése*jelölőnégyzetet, majd válassza ki a forrás-tárolót. A forrás tároló általában *VHD*-k:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
     
   4. Válassza ki a csatolni kívánt operációs rendszert tartalmazó virtuális merevlemezt. A folyamat befejezéséhez kattintson a *kiválasztás* gombra:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

   5. A lemez csatolásához kattintson az OK gombra.

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

3. Kapcsolódjon a hibaelhárítási virtuális géphez Távoli asztal használatával, és győződjön meg arról, hogy a forrás virtuális gép operációsrendszer-lemeze látható:

   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portal, majd kattintson a *kapcsolat*gombra.

   2. Nyissa meg a letöltött RDP-fájlt. Adja meg a hibaelhárítási virtuális gép felhasználónevét és jelszavát.

   3. A Fájlkezelőben keresse meg a csatlakoztatott adatlemezt. Ha a forrás virtuális gép VHD-je az egyetlen, a hibaelhárítási virtuális géphez csatolt adatlemez, az F: meghajtó:
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

4. Létrehozás `gpt.ini` a `\Windows\System32\GroupPolicy` forrás virtuális gép meghajtóján (ha `gpt.ini` létezik, nevezze át `gpt.ini.bak` ):
   
   > [!WARNING]
   > Győződjön meg arról, hogy nem véletlenül hozza létre a következő fájlokat a alkalmazásban `C:\Windows` a hibaelhárítási virtuális gép operációsrendszer-meghajtóján. Hozza létre a következő fájlokat az operációsrendszer-meghajtón a forrásként szolgáló virtuális gép számára, amely adatlemezként van csatolva.
   
   * Adja hozzá a következő sorokat a `gpt.ini` létrehozott fájlhoz:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

5. Létrehozás `scripts.ini` a alkalmazásban `\Windows\System32\GroupPolicy\Machine\Scripts\` . Győződjön meg arról, hogy a rejtett mappák láthatók. Szükség esetén hozza létre a `Machine` vagy a `Scripts` mappákat.
   
   * Adja hozzá a következő sorokat a `scripts.ini` létrehozott fájlhoz:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg." <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
   
    Az új jelszó meghatározásakor meg kell felelnie a virtuális gép konfigurált jelszó-bonyolultsági követelményeinek.

7. A Azure Portal válassza le a lemezt a hibaelhárítási virtuális gépről:
   
   1. Válassza ki a hibaelhárítási virtuális gépet a Azure Portalon, majd kattintson a *lemezek*elemre.
   
   2. Válassza ki a 2. lépésben csatolt adatlemezt, kattintson a **Leválasztás**elemre, majd **az OK**gombra.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

8. Hozzon létre egy virtuális gépet a forrás virtuális gép operációsrendszer-lemezéről:
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Képernyőkép, amely a gpt.ini fájl frissítéseit jeleníti meg.":::

## <a name="complete-the-create-virtual-machine-experience"></a>A virtuális gép létrehozási élményének befejezése

1. Az új virtuális gép futása után kapcsolódjon a virtuális géphez Távoli asztal használatával a parancsfájlban megadott új jelszóval `FixAzureVM.cmd` .

2. A távoli munkamenetből az új virtuális gépre, távolítsa el a következő fájlokat a környezet tisztításához:
    
    * A `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * eltávolítása `FixAzureVM.cmd`
    * A `%windir%\System32\GroupPolicy\Machine\Scripts`
      * eltávolítása `scripts.ini`
    * A `%windir%\System32\GroupPolicy`
      * távolítsa el `gpt.ini` (ha `gpt.ini` korábban létezett, és `gpt.ini.bak` átnevezte a-re), nevezze át a fájlt a (z `.bak` ) névre `gpt.ini` .

## <a name="next-steps"></a>Következő lépések
Ha továbbra sem tud kapcsolatot létesíteni Távoli asztal használatával, tekintse meg az [RDP-hibaelhárítási útmutatót](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Az [RDP-hibaelhárítás részletes útmutatója](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) az egyes lépések helyett a hibaelhárítási módszereket vizsgálja. [Egy Azure-támogatási kérést is megnyithat](https://azure.microsoft.com/support/options/) gyakorlati segítségért.
