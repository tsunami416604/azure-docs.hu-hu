---
title: Hozzáadhat és eltávolíthat egy Virtuálisgép-lemezkép Azure verem |} Microsoft Docs
description: Adja hozzá, vagy távolítsa el a szervezete egyéni Windows vagy Linux rendszerű Virtuálisgép-lemezképet bérlők számára.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/27/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 8dd77dd3431f1be2b8edd8b51929c21b1d5bcd88
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081350"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>A virtuálisgép-lemezkép elérhetővé Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure-készletben tehet virtuálisgép-rendszerképek elérhetővé a felhasználóknak. Ezeket a lemezképeket hivatkozhat Azure Resource Manager-sablonok, vagy később is hozzáadhatja az Azure piactér felhasználói felület piactér elemként. Vagy egy kép az űrlapot használja a globális Azure piactérről, vagy adjon hozzá a saját egyéni lemezképet. Hozzáadhat egy virtuális Gépet, a portál vagy a Windows PowerShell használatával.

## <a name="add-a-vm-image-through-the-portal"></a>A portálon keresztül Virtuálisgép-lemezkép hozzáadása

> [!NOTE]
> Ezzel a módszerrel külön-külön kell létrehozni a Piactéri elemet.

Lemezképek kell tudni blobtárolók URI azonosítójához használandó lehet hivatkozni. Készítse elő a Windows vagy Linux operációs rendszer lemezképének VHD-formátumban (nem VHDX), és majd feltölti a lemezképet az Azure vagy az Azure-verem tárfiókba. Ha a lemezkép már fel van töltve a blob Storage Azure vagy az Azure-vermet, kihagyhatja az 1. lépés.

1. [Windows Virtuálisgép-lemezkép feltöltése az Azure Resource Manager üzembe helyezések a](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) vagy Linux-lemezképek, kövesse az utasításokat, leírt [központi telepítése Linux virtuális gépek Azure-veremben](azure-stack-linux.md). Mielőtt feltölti a lemezképet, fontos a következő tényezőket kell figyelembe venni:

   - Az Azure verem támogatja a rögzített méretű VHD formátummal. A rögzített formátum a logikai lemez lineárisan fájlon belül szerkezet, úgy, hogy a lemez eltolás X blob eltolás X tárolja. A blob végén kis élőláb a VHD-fájl tulajdonságait ismerteti. Győződjön meg róla, ha a lemez fennáll, használja a [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-parancsot.  

    > [!IMPORTANT]
    >  Az Azure verem nem támogatja a dinamikus lemez VHD-k. Egy virtuális géphez csatolt dinamikus lemez átméretezése marad a virtuális gép hibás állapotban. A probléma orvoslása érdekében törölje a virtuális gép a virtuális lemez, a tárfiók VHD blob törlése nélkül. A, a konvertálás a VHD dinamikus lemezről egy rögzített méretű lemezt, majd hozza létre a virtuális gép.

   * Hatékonyabb, ha a lemezkép feltöltése a verem Azure blob Storage Azure blob storage mert leküldéses a lemezképet a verem Azure-lemezképtárba kevesebb időt vesz igénybe, mint.

   * Feltöltése a [Windows Virtuálisgép-lemezkép](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), ne felejtse el behelyettesíteni a **Azure bejelentkezési** a lépés a [konfigurálása az Azure-verem operátor PowerShell környezet](azure-stack-powershell-configure-admin.md) lépés.  

   * Jegyezze fel a blobtárolók URI azonosítójához használandó, ha feltölti a lemezképet. A blob storage URI formátuma a következő: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd.

   * Ahhoz, hogy a blob névtelenül elérhető, lépjen a tárolóra fiók ahol a Virtuálisgép-lemezkép VHD feltöltése. Válassza ki **Blob**, majd válassza ki **házirend**. Másik lehetőségként létrehozhat helyette egy közös hozzáférésű jogosultságkódot ahhoz a tárolóhoz, és adja hozzá a blob URI részeként.

   ![Ugrás a tárolási fiók blobok](./media/azure-stack-add-vm-image/image1.png)

   ![A nyilvános blob hozzáférés beállítása](./media/azure-stack-add-vm-image/image2.png)

2. Jelentkezzen be Azure verem operátorként. A menüben válasszon ki **további szolgáltatások**. Ezt követően válassza **számítási** > **Virtuálisgép-rendszerképek** > **Hozzáadás**.

3. A **Virtuálisgép-lemezkép hozzáadása**, írja be a közzétevő, az ajánlat, SKU és a virtuálisgép-lemezképek verziószámának. Ezek a szegmensek neve tekintse meg a Resource Manager-sablonok a Virtuálisgép-lemezképet. Ügyeljen arra, hogy válassza ki a **osType** értékének megfelelően. A **OS lemez Blob URI-JÁNAK**, adja meg a Blob URI, ahol a lemezkép feltöltése. Ezt követően válassza **létrehozása** a Virtuálisgép-lemezkép létrehozásának megkezdéséhez.

   ![A lemezkép létrehozásához kezdete](./media/azure-stack-add-vm-image/image4.png)

   A kép sikeres létrehozása után a virtuális gép lemezképének állapota **sikeres**.

4. A virtuálisgép-lemezkép felhasználók által megjeleníthető könnyebben elérhetővé teszi a felhasználói felületen, érdemes nem [létrehozni a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Távolítsa el a Virtuálisgép-lemezkép a portálon keresztül

1. Nyissa meg a felügyeleti portálon, a [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Válassza ki **piactér felügyeleti**, majd válassza ki a törölni kívánt virtuális Gépet.

3. Kattintson a **Törlés** gombra.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>A Virtuálisgép-lemezkép hozzáadása a piactér PowerShell használatával

1. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).  

2. Jelentkezzen be Azure verem operátor. Útmutatásért lásd: [jelentkezzen be Azure verem kezelőként](azure-stack-powershell-configure-admin.md).

3. PowerShell nyisson meg egy rendszergazdai jogú parancssorba, majd futtassa:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  A **Add-AzsPlatformimage** parancsmag hivatkoznak a Virtuálisgép-lemezkép az Azure Resource Manager-sablonok segítségével értékeket adja meg. Az értékek a következők:
  - **publisher**  
    Például:`Canonical`  
    A közzétevő neve szegmensében a Virtuálisgép-lemezkép felhasználók használni, amikor a lemezképet. Példa: **Microsoft**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **offer**  
    Például:`UbuntuServer`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép ajánlat neve szegmense. Példa: **WindowsServer**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **sku**  
    Például:`14.04.3-LTS`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép SKU neve szegmense. Példa: **Datacenter2016**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **Verzió**  
    Például:`1.0.0`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép verziója. Ebben a formátumban van  *\#.\#. \#*. Példa: **1.0.0**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **osType**  
    Például:`Linux`  
    A kép osType kell lennie, vagy **Windows** vagy **Linux**.  
  - **OSUri**  
    Például:`https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    A blobtárolók URI azonosítójához használandó is megadhat az egy `osDisk`.  

    További információkért lásd: a PowerShell-referencia az a [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) parancsmag és a [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) parancsmag.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Egy egyéni Virtuálisgép-lemezkép hozzáadása a piactér a PowerShell használatával

1. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Ha használ **Active Directory összevonási szolgáltatások**, használja a következő parancsmagot:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Jelentkezzen be Azure verem operátor. Útmutatásért lásd: [jelentkezzen be Azure verem kezelőként](azure-stack-powershell-configure-admin.md).

4. Hozzon létre egy tárfiókot a globális Azure vagy Azure verem az egyéni VM-lemezkép mentéséhez. Útmutatásért lásd: [gyors üzembe helyezés: feltöltés, letölthetők és az Azure portál használatával lista blobok](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. A VHD-formátumban (nem VHDX) Windows vagy Linux operációs rendszer lemezképének előkészítése, feltölti a lemezképet a tárfiókhoz és az URI, ahol a Virtuálisgép-lemezkép kérhetnek le PowerShell beolvasása.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Opcionális) Feltöltheti a Virtuálisgép-lemezkép részeként adatlemezek tömbjét. Az adatlemezek, a New-DataDiskObject parancsmaggal hozzon létre. Nyissa meg a Powershellt egy rendszergazda jogú parancssorból, és futtassa:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. PowerShell nyisson meg egy rendszergazdai jogú parancssorba, majd futtassa:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Az Add-AzsPlatformimage parancsmag és a New-DataDiskObject parancsmag kapcsolatos további információkért tekintse meg a Microsoft PowerShell [Azure verem operátor modul dokumentáció](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Távolítsa el a Virtuálisgép-lemezkép PowerShell használatával

Ha már nincs szüksége a virtuális gép lemezképére, feltöltött, törölheti a piactérről az alábbi parancsmag segítségével:

1. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).

2. Jelentkezzen be Azure verem operátor.

3. PowerShell nyisson meg egy rendszergazdai jogú parancssorba, majd futtassa:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  A **Remove-AzsPlatformImage** parancsmag hivatkoznak a Virtuálisgép-lemezkép az Azure Resource Manager-sablonok segítségével értékeket adja meg. Az értékek a következők:
  - **publisher**  
    Például:`Canonical`  
    A közzétevő neve szegmensében a Virtuálisgép-lemezkép felhasználók használni, amikor a lemezképet. Példa: **Microsoft**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **offer**  
    Például:`UbuntuServer`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép ajánlat neve szegmense. Példa: **WindowsServer**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **sku**  
    Például:`14.04.3-LTS`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép SKU neve szegmense. Példa: **Datacenter2016**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **Verzió**  
    Például:`1.0.0`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép verziója. Ebben a formátumban van  *\#.\#. \#*. Példa: **1.0.0**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
    
    A Remove-AzsPlatformImage parancsmaggal kapcsolatban további információért tekintse meg a Microsoft PowerShell [Azure verem operátor modul dokumentáció](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>További lépések

[Virtuális gép kiépítése](azure-stack-provision-vm.md)
