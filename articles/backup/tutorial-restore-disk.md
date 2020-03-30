---
title: Oktatóanyag – Virtuálisgép-lemez visszaállítása az Azure Backup segítségével
description: Megtudhatja, hogyan állíthatja vissza a lemezt, valamint hogyan hozhat létre és állíthat helyre egy virtuális gépet az Azure-ban a Backup és a Recovery Services használatával.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238733"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Lemez visszaállítása és helyreállított virtuális gép létrehozása az Azure-ban

Az Azure Backup georedundáns helyreállítási tárolókban tárolt helyreállítási pontokat hoz létre. Helyreállítási pontról történő visszaállításkor visszaállíthatja a teljes virtuális gépet, vagy csak egyes fájlokat. Ez a cikk a teljes virtuális gép parancssori felülettel való visszaállításának módját ismerteti. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

További információért a PowerShell a lemez visszaállításhoz és egy helyreállított virtuális gép létrehozásához való használatáról, tekintse meg a [Azure-beli virtuális gépek biztonsági mentése és visszaállítása a PowerShell-lel](backup-azure-vms-automation.md#restore-an-azure-vm) című részt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.18-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz olyan Linux rendszerű virtuális gépre van szükség, amelyet az Azure Backup véd. A virtuális gép véletlen törlésének és a helyreállítási folyamatnak a szimulálásához hozzon létre egy virtuális gépet a helyreállítási pont egyik lemezéről. Ha szüksége van egy linuxos virtuális gépre, amelyet az Azure Backup véd, lásd: [Virtuális gép biztonsági mentése az Azure-ban a parancssori felület használatával](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Amikor az Azure biztonsági mentést kezdeményez, a virtuális gépen futó biztonsági mentési bővítmény időponthoz kötött pillanatképet készít. A biztonsági mentési bővítmény az első biztonsági mentés kérésekor települ a virtuális gépre. Az Azure Backup akkor is tud pillanatképet készíteni az alapul szolgáló tárolóról, ha a virtuális gép a biztonsági mentés közben nem fut.

Alapértelmezés szerint az Azure Backup a fájlrendszerrel konzisztens biztonsági másolatot készít. Amikor az Azure Backup elkészítette a pillanatképet, az adatok átkerülnek a helyreállítási tárba. A maximális hatékonyság érdekében az Azure Backup csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az előző biztonsági mentés óta változtak.

Ha az adatátvitel befejeződött, a rendszer eltávolítja a pillanatképet, és létrehoz egy helyreállítási pontot.

## <a name="list-available-recovery-points"></a>Rendelkezésre álló helyreállítási pontok listázása

A lemez visszaállításához ki kell választania egy helyreállítási pontot a helyreállítási adatok forrásaként. Az alapértelmezett szabályzat minden nap létrehoz egy-egy helyreállítási pontot, és mindegyiket 30 napig tartja meg. Így fenntartható egy helyreállítási pontokból álló készlet, amelyből kiválaszthat egy adott időpontot a helyreállításhoz.

Az elérhető helyreállítási pontok listájának megjelenítéséhez használja az [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsot. A lemezek a helyreállítási pont **nevével** állíthatók helyre. Ebben az oktatóanyagban a legutóbbi rendelkezésre álló helyreállítási pontot használjuk. A `--query [0].name` paraméter az alábbiak szerint választja ki a legutóbbi helyreállítási pont nevét:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Virtuálisgép-lemez visszaállítása

> [!IMPORTANT]
> Nagyon ajánlott az Az CLI 2.0.74-es vagy újabb verziójának használata a gyors visszaállítás előnyeinek kihasználásához, beleértve a felügyelt lemezvisszaállítást is. A legjobb, ha a felhasználó mindig a legújabb verziót használja.

### <a name="managed-disk-restore"></a>Felügyelt lemez visszaállítása

Ha a biztonsági mentési virtuális gép felügyelt lemezekkel rendelkezik, és ha a szándék a felügyelt lemezek visszaállítása a helyreállítási pontról, először egy Azure storage-fiókot. Ez a tárfiók a virtuális gép konfigurációjának és a központi telepítési sablon tárolására szolgál, amely később a virtuális gép központi telepítéséhez használható a visszaállított lemezekről. Ezután egy célerőforráscsoportot is biztosít a felügyelt lemezek visszaállításához.

1. A tárfiók létrehozásához használja az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myRecoveryPointName-t* az előző biztonsági [mentéshelyreállítási helyreállításipont-lista](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancsának kimenetében kapott helyreállításipont nevére. ***Adja meg azt a célerőforrás-csoportot is, amelyre a felügyelt lemezek et visszaállítják.***

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Ha a cél-erőforrás-csoport nem érhető el, majd a felügyelt lemezek visszalesznek állítva nem felügyelt lemezekként az adott tárfiókba. Ez jelentős következményekkel jár a visszaállítási idő, mivel a lemezek visszaállítása szükséges idő teljes mértékben függ az adott tárfiók.

### <a name="unmanaged-disks-restore"></a>Nem felügyelt lemezek visszaállítása

Ha a biztonsági mentési virtuális gép nem felügyelt lemezekkel rendelkezik, és ha a szándék a lemezek visszaállítása a helyreállítási pontról, először egy Azure storage-fiókot. Ez a tárfiók a virtuális gép konfigurációjának és a központi telepítési sablon tárolására szolgál, amely később a virtuális gép központi telepítéséhez használható a visszaállított lemezekről. Alapértelmezés szerint a nem felügyelt lemezek visszaállnak az eredeti tárfiókokba. Ha a felhasználó egyetlen helyre kívánja visszaállítani az összes nem felügyelt lemezt, akkor az adott tárfiók is használható a lemezek átmeneti helyeként.

A további lépésekben a visszaállított lemezzel létrehoz egy virtuális gépet.

1. A tárfiók létrehozásához használja az [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) parancsot. A tárfiók nevének globálisan egyedinek kell lennie, és csak kisbetűkből állhat. Cserélje le *mystorageaccount* kifejezést egy saját egyedi névre:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Állítsa vissza a lemezt a helyreállítási pontról az [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) paranccsal. Cserélje le *mystorageaccount* kifejezést az előző paranccsal létrehozott tárfiók nevére. Cserélje le a *myRecoveryPointName* kifejezést az előzőleg használt [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) parancs kimenetéből származó helyreállításipont-névre:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Mint már említettük, a nem felügyelt lemezek visszaáll nak az eredeti tárfiókba. Ez biztosítja a legjobb visszaállítási teljesítményt. De ha az összes nem felügyelt lemezt vissza kell állítani az adott tárfiókba, akkor használja a megfelelő jelzőt az alábbi módon.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

A kimenet a következő példához hasonló, amelyben látható, hogy a visszaállítási feladat *InProgress* (Folyamatban) állapotban van:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Amikor a visszaállítási feladat jelentései *befejeződött,* a szükséges információkat (vm konfiguráció és a központi telepítési sablon) vissza lett állítva a tárfiókba. *Status*

## <a name="create-a-vm-from-the-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről

Az utolsó lépés az, hogy hozzon létre egy virtuális gép a visszaállított lemezekről. Használhatja a központi telepítési sablon ta- tárfiókba letöltött a virtuális gép létrehozásához.

### <a name="fetch-the-job-details"></a>A feladat részleteinek beolvasása

Az eredményül bejön a feladat részletei a sablon URI-ját adják meg, amely lekérdezhető és üzembe helyezhető. A feladatmegjelenítések paranccsal további részleteket kaphat az aktivált visszaállított feladatról.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

A lekérdezés kimenete minden részletet megad, de csak a tárfiók tartalma érdekel minket. Az Azure CLI [lekérdezési képességével](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) letudjuk kérni a megfelelő részleteket

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>A központi telepítési sablon beolvasása

A sablon nem érhető el közvetlenül, mivel az ügyfél tárfiók és az adott tároló alatt található. A sablon eléréséhez a teljes URL-címre van szükség (egy ideiglenes SAS-jogkivonattal együtt).

Először bontsa ki a sablonblob Uri-t a feladat részleteiből

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

A sablon blob Uri lesz ebben a formátumban, és bontsa ki a sablon nevét

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Így a sablon neve a fenti ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` példában lesz, és a tároló neve```myVM-daa1931199fd4a22ae601f46d8812276```

Most kap a SAS token ehhez a tárolóhoz és sablonrészletes [itt](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>A sablon üzembe helyezése a virtuális gép létrehozásához

Most telepítse a sablont a virtuális gép létrehozásához az [itt](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)leírtak szerint.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Ha ellenőrizni szeretné, hogy létrejött-e a virtuális gép a helyreállított lemezről, listázza az erőforráscsoportban szereplő virtuális gépeket az [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) paranccsal az alábbiak szerint:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban visszaállított egy lemezt a helyreállítási pontról, és létrehozott egy virtuális gépet a lemezről. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Helyreállítási pontok listázása és kiválasztása
> * Lemez visszaállítása egy helyreállítási pontból
> * Virtuális gép létrehozása a visszaállított lemezről

Folytassa a következő oktatóanyaggal, amely egy helyreállítási pont egyes fájljainak visszaállítását ismerteti.

> [!div class="nextstepaction"]
> [Fájlok visszaállítása Azure-beli virtuális gépekre](tutorial-restore-files.md)
