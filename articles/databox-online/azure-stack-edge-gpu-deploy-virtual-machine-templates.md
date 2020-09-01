---
title: Virtuális gépek üzembe helyezése a Azure Stack peremhálózati eszközön sablonok használatával
description: Ismerteti, hogyan lehet virtuális gépeket (VM-ket) létrehozni és felügyelni egy Azure Stack peremhálózati eszközön sablonok használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 330186b12efcc31e9f99d7c4bdbff3e081311c23
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084148"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-templates"></a>Virtuális gépek üzembe helyezése a Azure Stack peremhálózati eszközön sablonok használatával

Ez az oktatóanyag leírja, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack peremhálózati eszközön sablonok használatával. Ezek a sablonok JavaScript Object Notation (JSON) fájlok, amelyek meghatározzák a virtuális gép infrastruktúráját és konfigurációját. Ezekben a sablonokban megadhatja a telepítendő erőforrásokat és az erőforrások tulajdonságait.

A sablonok különböző környezetekben rugalmasak, mivel a paramétereket bemenetként, fájlból is elvégezhetik. A szabványos elnevezési struktúra a `TemplateName.json` sablonhoz és `TemplateName.parameters.json` a Parameters fájlhoz szükséges. Az ARM-sablonokkal kapcsolatos további információkért [tekintse meg a mi a Azure Resource Manager-sablonok?](../azure-resource-manager/templates/overview.md)című témakört.

Ebben az oktatóanyagban az előre megírt minta sablonokat fogjuk használni az erőforrások létrehozásához. Nem kell szerkesztenie a sablonfájlt, és csak a `.parameters.json` fájlokat módosíthatja, hogy testreszabja a központi telepítést a gépen. 

## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Azure Stack peremhálózati virtuális gépek számos eszközön való üzembe helyezéséhez egyetlen Sysprep használatával létrehozott virtuális merevlemezt használhat a teljes flottához, ugyanezt a sablont az üzembe helyezéshez, és csak kisebb módosításokat hajthat végre a sablonban az egyes telepítési helyekhez (ezeket a módosításokat lehet kézzel elvégezni, ahogy itt vagy programozott módon).) 

A sablonok használatával történő üzembe helyezési munkafolyamat magas szintű összefoglalása a következő:

1. **Előfeltételek konfigurálása** – az előfeltételek 3 típusa létezik; eszköz, ügyfél és a virtuális gép számára.

    1. **Eszköz előfeltételei**

        1. Azure Resource Manager csatlakoztatása az eszközön.
        2. A számítás engedélyezése a helyi felhasználói felületen keresztül.

    2. **Ügyfél előfeltételei**

        1. Töltse le a virtuálisgép-sablonokat és a kapcsolódó fájlokat az ügyfélen.
        1. A TLS 1,2 opcionálisan konfigurálható az ügyfélen.
        1. [Töltse le és telepítse a Microsoft Azure Storage Explorert](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) az ügyfélen.

    3. **A virtuális gépek előfeltételei**

        1. Hozzon létre egy erőforráscsoportot az eszköz helyén, amely az összes virtuálisgép-erőforrást tartalmazni fogja.
        1. Hozzon létre egy Storage-fiókot a virtuálisgép-rendszerkép létrehozásához használt virtuális merevlemez feltöltéséhez.
        1. Adja hozzá a helyi Storage-fiók URI-JÁT a DNS-hez vagy a Hosts fájlhoz az eszközt elérő ügyfélen.
        1. Telepítse a blob Storage-tanúsítványt az eszközre, valamint az eszközt elérő helyi ügyfélre. Szükség esetén telepítse a blob Storage-tanúsítványt a Storage Explorer.
        1. Hozzon létre és töltsön fel egy virtuális merevlemezt a korábban létrehozott Storage-fiókba.

2. **Virtuális gép létrehozása sablonokból**

    1. Hozzon létre egy virtuálisgép-rendszerképet és egy VNet a `CreateImageAndVnet.parameters.json` Parameters fájl és a `CreateImageAndVnet.json` telepítési sablon használatával.
    1. Hozzon létre egy virtuális gépet a korábban létrehozott erőforrásokkal `CreateVM.parameters.json` Paraméterek fájl és  `CreateVM.json` központi telepítési sablon használatával.

## <a name="device-prerequisites"></a>Eszköz előfeltételei

Konfigurálja ezeket az előfeltételeket az Azure Stack Edge-eszközön.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Ügyfél előfeltételei

Konfigurálja ezeket az előfeltételeket az ügyfélen, amely az Azure Stack Edge-eszköz elérésére szolgál majd.

1. [Töltse le Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) , ha a virtuális merevlemez feltöltésére használja. Azt is megteheti, hogy letölti a AzCopy a virtuális merevlemez feltöltéséhez. Előfordulhat, hogy konfigurálnia kell a TLS 1,2-et az ügyfélszámítógépen, ha a AzCopy régebbi verzióját futtatja. 
1. [Töltse le a virtuálisgép-sablonokat és a paraméterek fájljait](https://aka.ms/ase-vm-templates) az ügyfélszámítógépre. Bontsa ki a munkakönyvtárként használni kívánt könyvtárba.


## <a name="vm-prerequisites"></a>A virtuális gépek előfeltételei

Konfigurálja ezeket az előfeltételeket olyan erőforrások létrehozásához, amelyekre szükség lesz a virtuális gépek létrehozásához. 

    
### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat, például a Storage-fiókot, a lemezt, a felügyelt lemezt.

> [!IMPORTANT]
> Az összes erőforrás ugyanabban a helyen jön létre, mint az eszköz, és a hely a **DBELocal**értékre van állítva.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Az alábbiakban egy példa látható a kimenetre.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy új Storage-fiókot az előző lépésben létrehozott erőforráscsoport használatával. Ez egy **helyi Storage-fiók** , amely a virtuális gép virtuális lemezének lemezképének feltöltésére szolgál majd.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> A Azure Resource Manager használatával csak a helyi Storage-fiókok, például a helyileg redundáns tárolás (Standard_LRS vagy Premium_LRS) hozhatók létre. A többplatformos tárolási fiókok létrehozásához tekintse [meg a Hozzáadás, kapcsolódás a Storage-fiókokhoz a Azure stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md)-ben című témakör lépéseit.

Az alábbiakban egy példa látható a kimenetre.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

A Storage-fiók kulcsainak lekéréséhez futtassa a `Get-AzureRmStorageAccountKey` parancsot. Itt látható a parancs mintájának kimenete.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>BLOB URI hozzáadása a Hosts fájlhoz

Győződjön meg arról, hogy már hozzáadta a blob URI-t a Hosts fájlban a blob Storage-hoz való kapcsolódáshoz használt ügyfélhez. **Futtassa rendszergazdaként a Jegyzettömböt** , és adja hozzá a következő bejegyzést a blob URI-hoz `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Egy tipikus környezetben a DNS-t úgy kell konfigurálni, hogy az összes Storage-fiók az Azure Stack Edge-eszközre mutasson egy `*.blob.devicename.domainname.com` bejegyzéssel.

### <a name="optional-install-certificates"></a>Választható Tanúsítványok telepítése

Ugorja át ezt a lépést, ha Storage Explorer *http*-n keresztül fog kapcsolatot létesíteni. Ha *https*-t használ, akkor telepítenie kell a megfelelő tanúsítványokat a Storage Explorer. Ebben az esetben telepítse a blob Endpoint-tanúsítványt. További információ: tanúsítványok létrehozása és feltöltése a [tanúsítványok kezelése](azure-stack-edge-j-series-manage-certificates.md)szolgáltatásban. 

### <a name="create-and-upload-a-vhd"></a>Virtuális merevlemez létrehozása és feltöltése

Győződjön meg arról, hogy rendelkezik egy virtuális lemezzel, amelyet a későbbi lépésben való feltöltéshez használhat. Kövesse a [virtuális gép rendszerképének létrehozása](azure-stack-edge-j-series-create-virtual-machine-image.md)című témakör lépéseit. 

Másolja a korábbi lépésekben létrehozott helyi Storage-fiókban a lapok blobba felhasználható lemezes lemezképeit. A [virtuális merevlemezt a](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) korábbi lépések során létrehozott Storage-fiókba a [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a AzCopy eszközzel töltheti fel. 

### <a name="use-storage-explorer-for-upload"></a>Feltöltés Storage Explorer használata

1. Nyissa meg a Storage Explorert. Válassza a **Szerkesztés** lehetőséget, és győződjön meg arról, hogy az alkalmazás a **Target Azure stack API**-kra van beállítva.

    ![Cél beállítása Azure Stack API-kra](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Telepítse az ügyféltanúsítványt PEM formátumban. Válassza az **> SSL-tanúsítványok szerkesztése > tanúsítványok importálása**lehetőséget. Mutasson az ügyféltanúsítványt.

    ![BLOB Storage-végpont tanúsítványának importálása](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Ha eszköz által generált tanúsítványokat használ, töltse le és alakítsa át a blob Storage-végpont `.cer` tanúsítványát `.pem` formátumra. Futtassa az alábbi parancsot: 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Ha saját tanúsítványt hoz, használja az aláíró lánc főtanúsítványát `.pem` formátumban.

3. A tanúsítvány importálása után indítsa újra Storage Explorer a módosítások érvénybe léptetéséhez.

    ![Újraindítás Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. A bal oldali ablaktáblán kattintson a jobb gombbal a **Storage-fiókok** elemre, és válassza a **Kapcsolódás az Azure Storage-hoz**lehetőséget. 

    ![Kapcsolódás az Azure Storage-hoz 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Válassza a **Tárfióknév és -kulcs használata** lehetőséget. Kattintson a **Tovább** gombra.

    ![Kapcsolódás az Azure Storage-hoz 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. A **kapcsolat neve és kulcsa mezőben**adja **meg a megjelenítendő nevet**, a **Storage-fiók nevét**, az Azure Storage- **fiók kulcsát**. Válassza a **másik** tárolási tartomány lehetőséget, majd adja meg a `<device name>.<DNS domain>` kapcsolatok karakterláncát. Ha nem telepített tanúsítványt a Storage Explorerban, ellenőrizze a **use http** beállítást. Kattintson a **Tovább** gombra.

    ![Kapcsolat a névvel és a kulccsal](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Tekintse át a **kapcsolat összegzését** , és válassza a **Csatlakoztatás**lehetőséget.

8. A Storage-fiók megjelenik a bal oldali ablaktáblán. Válassza ki és bontsa ki a Storage-fiókot. Válassza a **blob-tárolók**lehetőséget, kattintson a jobb gombbal, majd válassza a **blob-tároló létrehozása**lehetőséget. Adja meg a blob-tároló nevét.

9. Válassza ki az imént létrehozott tárolót, és a jobb oldali ablaktáblában válassza a **feltöltés > a fájlok feltöltése**lehetőséget. 

    ![1. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Tallózással keresse meg a fájlt, és mutasson arra a virtuális merevlemezre, amelyet fel szeretne tölteni a **kiválasztott fájlokban**. Válassza ki a **blob típusát** **lap blobként** , és válassza a **feltöltés**lehetőséget.

    ![A 2. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Miután betöltötte a VHD-t a blob-tárolóba, válassza ki a virtuális merevlemezt, kattintson a jobb gombbal, majd válassza a **Tulajdonságok parancsot**. 

    ![3. VHD-fájl feltöltése](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Másolja ki és mentse az **URI** -t, amelyet a későbbi lépésekben fog használni.

    ![URI másolása](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Rendszerkép-és VNet létrehozása a virtuális géphez

Ha lemezképet és virtuális hálózatot szeretne létrehozni a virtuális GÉPHEZ, szerkesztenie kell a `CreateImageAndVnet.parameters.json` paramétereket tartalmazó fájlt, majd telepítenie kell `CreateImageAndVnet.json` azt a sablont, amely ezt a paramétert használja.


### <a name="edit-parameters-file"></a>Paraméterek szerkesztése fájl

A fájl `CreateImageAndVnet.parameters.json` a következő paramétereket veszi figyelembe: 

```json
"parameters": {
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
      "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Szerkessze a fájlt `CreateImageAndVnet.parameters.json` , hogy a következőt tartalmazza a Azure stack Edge-eszközhöz:

1. Adja meg a feltölteni kívánt virtuális merevlemezhez tartozó operációsrendszer-típust. Az operációs rendszer típusa Windows vagy Linux lehet.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Módosítsa a rendszerkép URI-ját az előző lépésben feltöltött rendszerkép URI-azonosítójához:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Ha a *http* -t használja a Storage Explorer, módosítsa ezt egy *https* URI-ra.

3. Módosítsa a `addressPrefix` és a `subnetPrefix` . Az eszköz helyi felhasználói felületén nyissa meg a **hálózat** lapot. Keresse meg a számításhoz engedélyezett portot. Szerezze be az alaphálózat IP-címét, és adja hozzá az alhálózati maszkot a CIDR-jelölés létrehozásához. Ha standard 255.255.255.0-alhálózattal rendelkezik, tegye a következőt az IP-cím utolsó számának a 0 értékkel való lecserélése és a/24 végpont hozzáadásával. Így a 10.126.68.0 egy 255.255.255.0 alhálózati maszkmal 10.126.68.0/24 lesz. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Adja meg a paraméterek egyedi rendszerképét, a VNet nevét és az alhálózat nevét.

    Itt látható a cikkben használt JSON-minta.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Mentse a paramétereket tartalmazó fájlt.


### <a name="deploy-template"></a>Sablon üzembe helyezése 

A sablon üzembe helyezése `CreateImageAndVnet.json` . Ez a sablon telepíti a VNet és a képerőforrásokat, amelyeket a rendszer a későbbi lépésben a virtuális gépek létrehozásához használ majd.

> [!NOTE]
> Ha hitelesítési hibaüzenetet kap a sablon telepítésekor, előfordulhat, hogy a munkamenet Azure-beli hitelesítő adatai lejártak. Futtassa `login-AzureRM` újra a parancsot az Azure stack Edge-eszközön lévő Azure Resource Managerhoz való kapcsolódáshoz.

1. Futtassa a következő parancsot: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Ellenőrizze, hogy a rendszerkép és a VNet erőforrások üzembe helyezése sikeres volt-e. Itt látható a sikeres létrehozott rendszerkép-és VNet kimenete.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Virtuális gép létrehozása

### <a name="edit-parameters-file-to-create-vm"></a>Paraméterek szerkesztése virtuális gép létrehozásához
 
Virtuális gép létrehozásához használja a `CreateVM.parameters.json` paramétert. A következő paramétereket veszi igénybe.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Rendelje hozzá a megfelelő paramétereket az `CreateVM.parameters.json` Azure stack Edge-eszközhöz.

1. Adjon meg egy egyedi nevet, a hálózati csatoló nevét és az ipconfig nevet. 
1. Adja meg a felhasználónevet, a jelszót és a virtuális gép támogatott méretét.
1. Adja meg ugyanazt a nevet a **VnetName**, a **SubnetName**és a **imagename** számára a paraméterekben megadott módon `CreateImageAndVnet.parameters.json` . Ha például a VnetName, a subnetName és a ImageName a **vnet1**, a **subnet1**és a **image1**értéket adta meg, akkor ezeket az értékeket is megtarthatja a sablon paramétereinek megfelelően.
1. Most szüksége lesz egy statikus IP-címére, amelyet a fent definiált alhálózati hálózaton lévő virtuális géphez szeretne rendelni. Cserélje le a **privateipaddress tulajdonságot** erre a címnek a paraméter fájljában. Ha azt szeretné, hogy a virtuális gép IP-címet kapjon a helyi DHCP-kiszolgálóról, hagyja `privateIPAddress` üresen az értéket.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Mentse a paramétereket tartalmazó fájlt.

    Itt látható a cikkben használt JSON-minta.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Sablon üzembe helyezése virtuális gép létrehozásához

Telepítse a virtuális gép létrehozási sablonját `CreateVM.json` . Ez a sablon egy hálózati adaptert hoz létre a meglévő VNet, és virtuális gépet hoz létre a központilag telepített rendszerképből.

1. Futtassa a következő parancsot: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "RG1"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    A virtuális gép létrehozása 15-20 percet is igénybe vehet. Az alábbi példa egy sikeresen létrehozott virtuális gép kimenetét jeleníti meg.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
 
7. Ellenőrizze, hogy a virtuális gép üzembe helyezése sikeres volt-e. Futtassa a következő parancsot:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Kapcsolódás virtuális géphez

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Nem támogatott VM-műveletek és-parancsmagok

A bővítmények, a méretezési csoportok, a rendelkezésre állási csoportok, a pillanatképek nem támogatottak.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge device.

On the client used to access your Azure Stack Edge device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Következő lépések

[Azure Resource Manager-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
