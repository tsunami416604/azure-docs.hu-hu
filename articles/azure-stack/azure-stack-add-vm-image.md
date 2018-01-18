---
title: "A Virtuálisgép-lemezkép hozzáadása Azure verem |} Microsoft Docs"
description: "A szervezete egyéni Windows vagy Linux virtuális gép lemezképének hozzáadása a bérlők számára."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: mabrigg
ms.openlocfilehash: 3b228452d416bbb2c54243b95292f7e1198af14f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Azure-készletben elérhetővé egy egyéni virtuálisgép-lemezkép

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure-készletben operátorok is elérhetővé egyéni virtuálisgép-rendszerképek a felhasználók számára. Ezeket a lemezképeket hivatkozhat Azure Resource Manager-sablonok, vagy később is hozzáadhatja az Azure piactér felhasználói felület piactér elemként.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>A Virtuálisgép-lemezkép hozzáadása a piactér PowerShell használatával

Futtassa a következő előfeltételek teljesülését, vagy a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) vagy egy Windows-alapú külső ügyfélszámítógépről, ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Telepítse a PowerShell Azure verem](azure-stack-powershell-install.md).  

2. Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

3. Készítse elő a Windows vagy Linux operációs rendszer virtuális merevlemezkép gazdameghajtója VHD-formátumban (ne használjon VHDX formátumú).

   * A Windows lemezképeit, a lemezkép előkészítése kapcsolatos utasításokat lásd [Windows Virtuálisgép-lemezkép feltöltése az Azure Resource Manager üzembe helyezések a](../virtual-machines/windows/upload-generalized-managed.md).

   * Linux-lemezképek, lásd: [központi telepítése Linux virtuális gépek Azure-veremben](azure-stack-linux.md). Hajtsa végre a lemezkép előkészítése, vagy egy meglévő Azure verem Linux kép használni, a cikkben leírtak szerint.    

   Az Azure verem támogatja a rögzített méretű VHD formátummal. A rögzített formátum a logikai lemez lineárisan fájlon belül szerkezet, úgy, hogy a lemez eltolás X blob eltolás X tárolja. A blob végén kis élőláb a VHD-fájl tulajdonságait ismerteti. Győződjön meg róla, ha a lemez fennáll, használja a [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-parancsot.  

   > [!IMPORTANT]
   >  Az Azure verem nem támogatja a dinamikus lemez VHD-k. Egy virtuális géphez csatolt dinamikus lemez átméretezése marad a virtuális gép hibás állapotban. A probléma orvoslása érdekében törölje a virtuális gép a virtuális lemez, a tárfiók VHD blob törlése nélkül. A, a konvertálás a VHD dinamikus lemezről egy rögzített méretű lemezt, majd hozza létre a virtuális gép.

A kép verem Azure piactérről való hozzáadásához kövesse az alábbi lépéseket:

1. A csatlakozás és ComputeAdmin modul importálása:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1
   ```

2. Jelentkezzen be a verem Azure környezetben. Futtassa az alábbi parancsfájlok, attól függően, hogy az Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával telepített Azure verem környezetét. (Cserélje le az Azure AD `tenantName`, `GraphAudience` végpontot, és `ArmEndpoint` beállításazonosítókban környezet konfigurációjától.)

    * **Az Azure Active Directory**. Használja a következő parancsmagot:

      ```PowerShell
      # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "<Resource Manager endpoint for your environment>"

      # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "<GraphAuidence endpoint for your environment>"

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

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID
      ```

   * **Az Active Directory összevonási szolgáltatások**. Használja a következő parancsmagot:

        ```PowerShell
        # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
        $ArmEndpoint = "<Resource Manager endpoint for your environment>"

        # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
        $GraphAudience = "<GraphAuidence endpoint for your environment>"

        # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
        Add-AzureRMEnvironment `
          -Name "AzureStackAdmin" `
          -ArmEndpoint $ArmEndpoint

        Set-AzureRmEnvironment `
          -Name "AzureStackAdmin" `
          -GraphAudience $GraphAudience `
          -EnableAdfsAuthentication:$true

        $TenantID = Get-AzsDirectoryTenantId `
          -ADFS `
          -EnvironmentName AzureStackAdmin

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. A Virtuálisgép-lemezkép adja hozzá a figyelőn a `Add-AzsVMImage` parancsmag. Az a `Add-AzsVMImage` parancsmagot, adja meg `osType` Windows vagy Linux. A közzétevő, az ajánlat, SKU és a VM-lemezkép verziója tartalmazza. Engedélyezett paraméterekkel kapcsolatos további információkért lásd: [paraméterek](#parameters). A paraméterek segítségével Azure Resource Manager-sablonok hivatkoznak a Virtuálisgép-lemezkép. A következő példa a parancsfájl hív meg:

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

A parancs a következőket teszi:

* Hitelesíti az Azure-verem környezetbe.
* A helyi virtuális merevlemez feltölt egy újonnan létrehozott ideiglenes tárfiókot.
* A Virtuálisgép-lemezkép hozzáadása a virtuális gépek lemezképtára.
* A Piactéri elemet hoz létre.

Győződjön meg arról, hogy a parancs sikeresen lefutott, a portálon lépjen a piactéren. Győződjön meg arról, hogy a Virtuálisgép-lemezkép érhető el a **virtuális gépek** kategóriát.

![Sikerült hozzáadni a Virtuálisgép-lemezkép](./media/azure-stack-add-vm-image/image5.PNG)

## <a name="remove-a-vm-image-by-using-powershell"></a>Távolítsa el a Virtuálisgép-lemezkép PowerShell használatával

Ha már nincs szüksége a virtuális gép lemezképére, feltöltött, törölheti a piactérről az alábbi parancsmag segítségével:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Paraméterek

| Paraméter | Leírás |
| --- | --- |
| **publisher** |A közzétevő neve szegmensében a Virtuálisgép-lemezkép felhasználók használni, amikor a lemezképet. Példa: **Microsoft**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben. |
| **offer** |A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép ajánlat neve szegmense. Példa: **WindowsServer**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben. |
| **sku** |A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép SKU neve szegmense. Példa: **Datacenter2016**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben. |
| **version** |A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép verziója. Ebben a formátumban van  *\#.\#. \#*. Példa: **1.0.0**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben. |
| **osType** |A kép osType kell lennie, vagy **Windows** vagy **Linux**. |
| **osDiskLocalPath** |Az operációs rendszer lemezének Azure verem VM lemezképként feltölteni kívánt virtuális merevlemez helyi elérési útja. |
| **dataDiskLocalPaths** |A helyi elérési útja, amely a Virtuálisgép-lemezkép részeként is feltölthetők adatlemezek választható tömbjét. |
| **CreateGalleryItem** |Egy logikai jelző, amely meghatározza, hogy hozzon létre egy elemet a piactér-e. Alapértelmezés szerint érték **igaz**. |
| **cím** |A Piactéri elemet megjelenített neve. Alapértelmezés szerint van beállítva a `Publisher-Offer-Sku` a Virtuálisgép-lemezkép értékét. |
| **description** |A Piactéri elemet leírása. |
| **hely** |A hely, ahol a Virtuálisgép-lemezkép közzé kell tenni. Alapértelmezés szerint ez az érték értéke **helyi**.|
| **osDiskBlobURI** |(Választható) Ezt a parancsfájlt is fogad blobtárolók URI azonosítójához használandó a `osDisk`. |
| **dataDiskBlobURIs** |(Választható) Ezt a parancsfájlt a Blob storage URI-azonosítók tömbjét is fogad az adatok a lemezek hozzáadása a lemezképet. |

## <a name="add-a-vm-image-through-the-portal"></a>A portálon keresztül Virtuálisgép-lemezkép hozzáadása

> [!NOTE]
> Ezzel a módszerrel külön-külön kell létrehozni a Piactéri elemet.

Lemezképek kell tudni blobtárolók URI azonosítójához használandó lehet hivatkozni. Készítse elő a Windows vagy Linux operációs rendszer lemezképének VHD-formátumban (nem VHDX), és majd feltölti a lemezképet az Azure vagy az Azure-verem tárfiókba. Ha a lemezkép már fel van töltve a Blob Storage Azure vagy az Azure-vermet, kihagyhatja az 1. lépés.

1. [Windows Virtuálisgép-lemezkép feltöltése az Azure Resource Manager üzembe helyezések a](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) vagy Linux-lemezképek, kövesse az utasításokat, leírt [központi telepítése Linux virtuális gépek Azure-veremben](azure-stack-linux.md). Mielőtt feltölti a lemezképet, fontos a következő tényezőket kell figyelembe venni:

   * Az Azure verem támogatja a rögzített méretű VHD formátummal. A rögzített formátum a logikai lemez lineárisan fájlon belül szerkezet, úgy, hogy a lemez eltolás X blob eltolás X tárolja. A blob végén kis élőláb a VHD-fájl tulajdonságait ismerteti. Győződjön meg róla, ha a lemez fennáll, használja a [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) PowerShell-parancsot.  

    > [!IMPORTANT]
   >  Az Azure verem nem támogatja a dinamikus lemez VHD-k. Egy virtuális géphez csatolt dinamikus lemez átméretezése marad a virtuális gép hibás állapotban. A probléma orvoslása érdekében törölje a virtuális gép a virtuális lemez, a tárfiók VHD blob törlése nélkül. A, a konvertálás a VHD dinamikus lemezről egy rögzített méretű lemezt, majd hozza létre a virtuális gép.

   * Sokkal hatékonyabban, lemezkép feltölteni az Azure verem Blob storage-mint az Azure Blob storage, mert a lemezképet a verem Azure-lemezképtárba leküldéses kevesebb időt vesz igénybe.

   * Feltöltése a [Windows Virtuálisgép-lemezkép](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), ne felejtse el behelyettesíteni a **Azure bejelentkezési** a lépés a [konfigurálása az Azure-verem operátor PowerShell környezet](azure-stack-powershell-configure-admin.md) lépés.  

   * Jegyezze fel a blobtárolók URI azonosítójához használandó, ha feltölti a lemezképet. A Blob storage URI formátuma a következő:  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd.

   * Ahhoz, hogy a blob névtelenül elérhető, lépjen a tárolóra fiók ahol a Virtuálisgép-lemezkép VHD feltöltése. Válassza ki **Blob**, majd válassza ki **házirend**. Másik lehetőségként létrehozhat helyette egy közös hozzáférésű jogosultságkódot ahhoz a tárolóhoz, és adja hozzá a blob URI részeként.

   ![Ugrás a tárolási fiók blobok](./media/azure-stack-add-vm-image/image1.png)

   ![A nyilvános blob hozzáférés beállítása](./media/azure-stack-add-vm-image/image2.png)

2. Jelentkezzen be Azure verem operátorként. A menüben válasszon ki **további szolgáltatások** > **erőforrás-szolgáltató**. Ezt követően válassza **számítási** > **Virtuálisgép-rendszerképek** > **Hozzáadás**.

3. A **Virtuálisgép-lemezkép hozzáadása**, írja be a közzétevő, az ajánlat, SKU és a virtuálisgép-lemezképek verziószámának. Ezek a szegmensek neve tekintse meg a Resource Manager-sablonok a Virtuálisgép-lemezképet. Ügyeljen arra, hogy válassza ki a **osType** értékének megfelelően. A **OS lemez Blob URI-JÁNAK**, adja meg a Blob URI, ahol a lemezkép feltöltése. Ezt követően válassza **létrehozása** a Virtuálisgép-lemezkép létrehozásának megkezdéséhez.

   ![A lemezkép létrehozásához kezdete](./media/azure-stack-add-vm-image/image4.png)

   A kép sikeres létrehozása után a virtuális gép lemezképének állapota **sikeres**.

4. A virtuálisgép-lemezkép felhasználók által megjeleníthető könnyebben elérhetővé teszi a felhasználói felületen, érdemes nem [létrehozni a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>További lépések

[Virtuális gép kiépítése](azure-stack-provision-vm.md)
