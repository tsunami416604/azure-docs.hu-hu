---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: ecbafe0d3f39b1bd6f7c494695ea17e067f0c79e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129278"
---
## <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek eléréséhez általánosítva kell lennie az operációs rendszer virtuális merevlemezének, egy olyan műveletnek, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver illesztőprogramjait egy virtuális gépről.

### <a name="for-windows"></a>Windows esetén

A Windows operációsrendszer-lemezek általánosítva vannak a [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) eszközzel. Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a Sysprep programot.

> [!WARNING]
> A Sysprep futtatása után kapcsolja ki a virtuális gépet az üzembe helyezésig, mert a frissítések automatikusan futtathatók. Ez a Leállítás megakadályozza, hogy a későbbi frissítések az operációs rendszer vagy a telepített szolgáltatások példány-specifikus módosításait is elvégezzék. A Sysprep futtatásával kapcsolatos további információkért tekintse [meg a virtuális merevlemez általánosítása című témakör lépéseit](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Linux esetén

A következő folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre. Részletekért lásd: [virtuális gépek vagy VHD-k rendszerképének létrehozása](../../virtual-machines/linux/capture-image.md). Ha eléri a "virtuális gép létrehozása a rögzített lemezképből" szakaszt, akkor leállíthatja.

1. Távolítsa el az Azure Linux-ügynököt.
    1. Csatlakozhat a linuxos virtuális géphez egy SSH-ügyfél használatával.
    2. Az SSH ablakban adja meg a következő parancsot: `sudo waagent –deprovision+user` .
    3. A folytatáshoz írja be az Y betűt (a-Force paramétert hozzáadhatja az előző parancshoz, így elkerülhető a megerősítő lépés).
    4. A parancs befejezése után a **Kilépés** gombra kattintva zárja be az SSH-ügyfelet.
2. Állítsa le a virtuális gépet.
    1. A Azure Portal válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalását.
    2. A virtuális gép már általánosítva van, és létrehozhat egy új virtuális gépet a virtuálisgép-lemez használatával.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Pillanatkép készítése a virtuális gép lemezéről

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. A bal felső sarokban válassza az **erőforrás létrehozása** elemet, majd keresse meg és válassza a **Pillanatkép** lehetőséget.
3. A pillanatkép panelen válassza a  **Létrehozás** lehetőséget.
4. Adja meg a pillanatkép **nevét** .
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adja meg egy új csoport nevét.
6. A **forrásoldali lemez** lapon válassza ki a felügyelt lemezt a pillanatképhez.
7. Válassza ki a pillanatkép tárolására használni kívánt **fiókot** . A **standard HDD** csak akkor használja, ha nagy teljesítményű SSD-meghajtón tárolja.
8. Válassza a **Létrehozás** lehetőséget.

#### <a name="extract-the-vhd"></a>A VHD kibontása

A következő parancsfájl használatával exportálja a pillanatképet egy virtuális merevlemezre a Storage-fiókjában.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $ snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy pillanatkép SAS URI-kódjának létrehozásához, és a mögöttes VHD-t egy Storage-fiókba másolja a SAS URI használatával. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
| --- | --- |
| az disk grant-access | Létrehoz egy írásvédett SAS-t, amelynek használatával a mögöttes VHD-fájl átmásolható egy tárfiókba, vagy letölthető a helyszíni rendszerre
| az storage blob copy start | Aszinkron módon másol egy blobot az egyik Storage-fiókból a másikba. Az `az storage blob show` új blob állapotának vizsgálatára használható. |
|