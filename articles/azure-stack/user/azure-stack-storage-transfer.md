---
title: Eszközök Azure verem tárolás
description: Tudnivalók a verem Azure storage-adatokkal átviteli eszközök
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/25/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: a148f8089dd104933e6ba95f573182e0c1a32ae5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="use-data-transfer-tools-for-azure-stack-storage"></a>Az Azure Storage-verem adatok átvitel eszközök használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A Microsoft Azure verem a tárolási szolgáltatások lemezek, blobok, táblák, üzenetsorok és fiók felügyeleti funkciókat biztosít. Azure Storage-eszközöket is használhatja, ha azt szeretné, kezeléséhez, vagy helyezze át az adatokat, vagy az Azure-verem tárolóból. Ez a cikk áttekintést azokról az eszközökről.

A követelményei határozzák meg, amely a következő eszközök Önnek legjobban:

* [AzCopy](#azcopy)

    Egy tároló-specifikus, parancssori segédprogram, letöltheti a adatok másolása egy objektumot egy másik objektum a tárfiókon belül vagy tárfiókok között.

* [Azure PowerShell](#azure-powershell)

    Egy feladatalapú, parancssori rendszerhéj és programozási nyelv, kifejezetten rendszergazdák számára készült.

* [Azure CLI](#azure-cli)

    Egy nyílt forráskódú, platformok közötti eszköz, amely parancsokat biztosít az Azure és az Azure-verem platformmal működik.

* [Microsoft Tártallózó](#microsoft-azure-storage-explorer)

    Könnyen használható önálló alkalmazás felhasználói felületet.

A tárolási szolgáltatások az Azure és az Azure-verem közötti különbségeket, miatt előfordulhat, egyes konkrét követelmények az egyes eszközök a következő szakaszok ismertetik. Azure verem tárolási és az Azure storage között, lásd: [Azure verem Storage: szempontok és a különbségeket](azure-stack-acs-differences.md).

## <a name="azcopy"></a>AzCopy

AzCopy parancssori segédprogram úgy tervezték, hogy másolja az adatokat a Microsoft Azure-blob és table storage egyszerű parancsokkal optimális teljesítménnyel érkező vagy oda irányuló. Másolhatja adatok egy objektum a másikra a tárfiókon belül vagy tárfiókok között.

### <a name="download-and-install-azcopy"></a>Töltse le és telepítse az AzCopy

Az AzCopy segédprogram két verziója van: a Windows és Linux AzCopy AzCopy.

 - **AzCopy Windowson**
    - Töltse le a támogatott AzCopy Azure verem. Telepítése, és AzCopy használata Azure veremben Azure azonos módon. További tudnivalókért lásd: [Windows AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy).
        - A 1802 frissítés vagy újabb verziók [töltse le az AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417).
        - A korábbi verziókban [töltse le az AzCopy 5.0.0](https://aka.ms/azcopyforazurestack20170417).

 - **AzCopy Linuxon**

    - AzCopy Linux Azure verem 1802 frissítés vagy újabb verzióit támogatja. Telepítése, és AzCopy használata Azure veremben Azure azonos módon. További tudnivalókért lásd: [Linux AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="azcopy-command-examples-for-data-transfer"></a>Adatátvitel az AzCopy parancspéldákban

Az alábbi példák hajtsa végre az adatok másolása és az Azure-verem blobok a jellemző forgatókönyvek. További tudnivalókért lásd: [Windows AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) és [Linux AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux).

### <a name="download-all-blobs-to-a-local-disk"></a>Töltse le az összes BLOB egy helyi lemezre

**Windows**

````AzCopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount.blob.local.azurestack.external/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
````

### <a name="upload-single-file-to-virtual-directory"></a>Egy fájlból töltse fel a virtuális könyvtár

**Windows**

```AzCopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

**Linux**

````AzCopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.local.azurestack.external/mycontainer/vd/abc.txt \
    --dest-key <key>
````

### <a name="move-data-between-azure-and-azure-stack-storage"></a>Adatok áthelyezése az Azure és az Azure Storage-verem között

Azure Storage és Azure verem közötti aszinkron adatforgalom nem támogatott. Meg kell adnia az átvitelt a **/SyncCopy** vagy **--másolatának szinkronizálása** lehetőséget.

**Windows**

````AzCopy
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
````

**Linux**

````AzCopy
azcopy \
    --source https://myaccount1.blob.local.azurestack.external/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --sync-copy
````

### <a name="azcopy-known-issues"></a>Azcopy ismert problémák

 - Szolgáltatásfájl-tároló bármely AzCopy művelet nem érhető el, mert a file storage még nem érhető el, Azure-készletben.
 - Azure Storage és Azure verem közötti aszinkron adatforgalom nem támogatott. Megadhatja az átvitelt a **/SyncCopy** másolja az adatokat.
 - A Linux az Azcopy csak verzióval 1802 frissítés vagy újabb verzió. És az nem támogatja a Table szolgáltatás.

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell modul, amely-parancsmagokat kínál az Azure és az Azure-verem a szolgáltatások kezelésére szolgáló. Egy feladatalapú, parancssori rendszerhéj és programozási nyelv, kifejezetten rendszergazdák számára készült.

### <a name="install-and-configure-powershell-for-azure-stack"></a>PowerShell telepítése és konfigurálása az Azure verem

Azure verem kompatibilis Azure PowerShell-modulok az Azure veremnek megfelelő működéséhez szükségesek. További információkért lásd: [verem Azure PowerShell telepítése](azure-stack-powershell-install.md) és [konfigurálása az Azure-verem felhasználói PowerShell környezet](azure-stack-powershell-configure-user.md) további.

### <a name="powershell-sample-script-for-azure-stack"></a>PowerShell-parancsfájlpélda Azure verem 

Ez a minta során feltételezzük, hogy sikeresen [telepített PowerShell Azure verem](azure-stack-powershell-install.md). Ez a parancsfájl segítségével végezze el a konfigurálását, és kérje meg az Azure-verem bérlő hitelesítő adatokat a fiók hozzáadása a helyi PowerShell környezetben. Majd a parancsfájl fog beállítják az alapértelmezett Azure-előfizetéssel, hozzon létre egy új tárfiókot az Azure-ban, hozzon létre egy új tárolót az új tárfiók és töltse fel a meglévő képfájl (blob) a tároló. Miután a parancsfájl megjeleníti az adott tároló összes BLOB, a helyi számítógépen egy új célkönyvtáron létrehozza, és töltse le a lemezképet.

1. Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).
2. Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).
3. Nyissa meg **Windows PowerShell ISE** és **Futtatás rendszergazdaként**, kattintson a **fájl** > **új** létrehozni egy új parancsfájlt.
4. Másolja az alábbi parancsfájlt, és illessze be az új parancsfájl fájlba.
5. Frissítés a konfigurációs beállítások alapján a parancsfájl-változókat.
   > [!NOTE]
   > Ez a parancsfájl futtatásához a gyökérkönyvtárban a rendelkezik **AzureStack_Tools**.

```PowerShell
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase.
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudience

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Add-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>PowerShell ismert problémák

Kompatibilis Azure PowerShell modul Azure verem nem 1.3.0. Eltér az Azure PowerShell legújabb verzióját. Ezt a különbséget a tárolási szolgáltatások művelet hatással van:

* A visszatérési érték formátuma `Get-AzureRmStorageAccountKey` verziójában 1.3.0 két tulajdonságokkal rendelkezik: `Key1` és `Key2`, amíg a jelenlegi Azure-verzió a fiók kulcsok tartalmazó tömböt ad vissza.

   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```

   További információkért lásd: [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felület található az Azure parancssori Azure-erőforrások kezeléséhez. MacOS, a Linux és a Windows telepítéséhez, és futtassa a parancssorból.

Az Azure CLI megfelelően lett optimalizálva, kezelése és felügyelete az Azure-erőforrások a parancssorból, és automatizálási parancsfájlokat, szemben az Azure Resource Managerrel működő készítéséhez. Nagy része megtalálható a verem Azure portál, beleértve a funkciógazdag adatelérési ugyanazokat a funkciókat biztosít.

Az Azure verem Azure CLI 2.0-s verziója szükséges. Azure CLI-t az Azure veremnek megfelelő konfigurálásával kapcsolatos további információkért lásd: [telepítése és konfigurálása az Azure CLI-verem](azure-stack-version-profiles-azurecli2.md). Több feladatait a Azure verem tárfiókban lévő erőforrások használata az Azure CLI 2.0 használatával kapcsolatos további információkért lásd: [az Azure CLI2.0 az Azure Storage használata](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Az Azure CLI mintaparancsfájl Azure verem

Ha befejezte a parancssori felület telepítése és konfigurálása, megpróbálhatja dolgozhat Azure verem tárolási erőforrások együttműködhet egy kis rendszerhéj parancsfájlt az alábbi lépéseket. A parancsfájl befejezi a következő műveleteket:

* A tárfiók hoz létre egy új tároló.
* A tároló feltölt egy már létező fájlt (a blob).
* Felsorolja az összes BLOB a tárolóban.
* Letölti a fájlt a helyi számítógépen, amely megadja egy célra.

A parancsfájl futtatása előtt győződjön meg arról, hogy akkor sikeresen csatlakozhat, és jelentkezzen be a cél Azure-verem.

1. Nyissa meg a kedvenc szövegszerkesztőjével, majd másolja, és az előző parancsfájl illessze be a szerkesztőt.
2. Frissítse a parancsfájl-változókat megfelelően a konfigurációs beállításokat.
3. A szükséges változók frissítése után mentse a parancsfájlt, és zárja be a szerkesztőt. A következő lépések azt feltételezik, hogy a parancsfájl nevű **my_storage_sample.sh**.
4. Szükség esetén jelölje meg a parancsfájl végrehajtható, mint: `chmod +x my_storage_sample.sh`
5. Hajtsa végre a parancsfájlt. Például a Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

A Microsoft Azure Tártallózó egy különálló alkalmazás, a Microsoft. Ez lehetővé teszi, hogy egyszerűen dolgozhat Azure Storage mind Azure verem Storage-adatokkal Windows, a macOS és a Linux rendszerű számítógépeken. Ha azt szeretné, hogy egyszerűen az Azure Storage-verem adatok kezelésére, fontolja meg a Microsoft Azure Tártallózó használatával.

* Azure verem használható Azure Tártallózó konfigurálásával kapcsolatos további tudnivalókért lásd: [Tártallózó csatlakozni a veremben Azure-előfizetéshez](azure-stack-storage-connect-se.md).
* A Microsoft Azure Tártallózó kapcsolatos további információkért lásd: [Ismerkedés a Tártallózó alkalmazással](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>További lépések
* [Csatlakozás a Tártallózó verem Azure-előfizetéshez](azure-stack-storage-connect-se.md)
* [Ismerkedés a Tártallózó alkalmazással](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure-konzisztens tárolási: különbségek és szempontok](azure-stack-acs-differences.md)
* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
