---
title: Azure parancssori felület használata Azure Data Lake Analytics kezelése
description: Ez a cikk ismerteti, hogyan kezelheti a Data Lake Analytics-feladatok, a adatforrások és a felhasználók az Azure CLI használatával.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fa7d46d45c350435c0ffba8f3755ad8bea651c3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088757"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Az Azure parancssori felület (CLI) használatával az Azure Data Lake Analytics kezelése

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ismerje meg, hogyan kezelheti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok az Azure CLI használatával. Más eszközök használatával kapcsolatos témakörök megtekintéséhez kattintson a fenti lapválasztóra.


**Előfeltételek**

Ez az oktatóanyag elkezdéséhez a következőket kell rendelkeznie:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Lásd: [Install and configure Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (Az Azure parancssori felület telepítése és konfigurálása).

   * Töltse le és telepítse az **előzetes kiadású** [Azure parancssori felületi eszközöket](https://github.com/MicrosoftBigData/AzureDataLake/releases) a bemutató elvégzéséhez.

* Hitelesítést végezni a `az login` parancsot, és válassza ki a használni kívánt előfizetést. További információ a munkahelyi vagy iskolai fiók segítségével történő hitelesítésről: [Connect to an Azure subscription from the Azure CLI](/cli/azure/authenticate-azure-cli) (Csatlakozás Azure-előfizetéshez az Azure parancssori felületről).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Most már elérheti a Data Lake Analytics és Data Lake Store parancsokat. Futtassa a következő parancsot a Data Lake Store és Data Lake Analytics parancsok listázásához:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Fiókok kezelése

A Data Lake Analytics-feladatok futtatásához egy Data Lake Analytics-fiókkal kell rendelkeznie. Az Azure HDInsight, eltérően nem kell fizetnie egy Analytics-fiók, amikor nem fut egy feladat. Csak az idő, amikor fut egy feladat kell fizetnie.  További információkért lásd: [Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Fiókok létrehozása

Futtassa a következő parancsot a Data Lake-fiók létrehozása 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Fiókok frissítése

A következő parancsot egy meglévő Data Lake Analytics-fiók tulajdonságainak frissítése

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Fiókok listázása

Egy adott erőforráscsoporton belül listája a Data Lake Analytics-fiókok

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Egy fiók részleteinek beolvasása

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>-Fiók törlése

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics jelenleg a következő két adatforrások használatát támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Analytics-fiók létrehozásakor ki kell jelölnie egy Azure Data Lake tárfiókot kell az alapértelmezett tárfiók. Az alapértelmezett Data Lake tárfiókot feladat metaadatok és a feladat a vizsgálati naplók tárolására szolgál. Miután létrehozott egy Analytics-fiók, hozzáadhat további Data Lake Storage-fiókok és/vagy az Azure Storage-fiókot. 

### <a name="find-the-default-data-lake-store-account"></a>Keresse meg az alapértelmezett Data Lake Store-fiók

Az alapértelmezett Data Lake Store-fiókot használt futtatásával megtekintheti a `az dla account show` parancsot. Alapértelmezett fiók neve a defaultDataLakeStoreAccount tulajdonság alatt jelenik meg.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>További Blob storage-fiókok hozzáadása

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Csak a Blob storage rövid nevek használata támogatott. Ne használjon teljes Tartománynevet, például a "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>További Data Lake Store-fiókok hozzáadása

A következő parancs frissíti a megadott Data Lake Analytics-fiók egy további Data Lake Store-fiók:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Meglévő adatforrás frissítése

Egy meglévő Blob storage-fiókkulcs frissítése:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Adatforrások listája:

A Data Lake Store-fiók megjelenítése:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

A Blob storage-fiók megjelenítése:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![A Data Lake Analytics-lista adatforrás](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Adatforrás törlése:
Egy Data Lake Store-fiók törlése:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Blob storage-fiók törlése:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Feladatok kezelése
Data Lake Analytics-fiók egy feladat létrehozása előtt kell rendelkeznie.  További információkért lásd: [kezelheti a Data Lake Analytics-fiókok](#manage-accounts).

### <a name="list-jobs"></a>Feladatok listája

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![A Data Lake Analytics-lista adatforrás](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Feladatok részleteinek beolvasása

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Feladatok elküldése

> [!NOTE]
> Az alapértelmezett prioritásának 1000, és a egy feladat párhuzamosságai alapértelmezett mértéke: 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Feladatok megszakítása
A list parancs használatával keresse meg a feladat azonosítója, és majd a Mégse gombra a Mégse gombra a feladat.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Folyamatok és ismétlődések

**Folyamatok és ismétlődések adatainak lekérése**

A korábban elküldött feladatok folyamatadatait az `az dla job pipeline` paranccsal tekintheti meg.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

A korábban elküldött feladatok ismétlődési adatait az `az dla job recurrence` paranccsal tekintheti meg.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Lásd még
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

