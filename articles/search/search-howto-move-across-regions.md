---
title: A szolgáltatási erőforrás áthelyezése régiók között
titleSuffix: Azure Cognitive Search
description: Ez a cikk bemutatja, hogyan helyezheti át az Azure Cognitive Search erőforrásait egyik régióból a másikba az Azure-felhőben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 03/24/2020
ms.openlocfilehash: 00f16d11f7a9cd276772eda5e91d6e117ada8c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246302"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Az Azure Cognitive Search szolgáltatás áthelyezése egy másik Azure-régióba

Alkalmanként az ügyfelek megkérdezik, hogy áthelyez-e egy keresési szolgáltatást egy másik régióba. Jelenleg nincs beépített mechanizmus vagy eszköz, amely segít a feladatban, de ez a cikk segíthet megérteni az azonos eredmény elérésének manuális lépéseit.

> [!NOTE]
> Az Azure Portalon minden szolgáltatás rendelkezik egy **Exportálási sablon** paranccsal. Az Azure Cognitive Search esetén ez a parancs egy szolgáltatás alapvető definícióját (név, hely, szint, replika és partíciószám) hozza létre, de nem ismeri fel a szolgáltatás tartalmát, és nem viszi át a kulcsokat, szerepköröket vagy naplókat. Bár a parancs létezik, nem javasoljuk, hogy használja a keresési szolgáltatás áthelyezéséhez.

## <a name="guidance-for-moving-a-service"></a>Útmutató a szolgáltatás áthelyezéséhez

1. Azonosítsa a függőségeket és a kapcsolódó szolgáltatásokat, hogy megismerjék a szolgáltatás áthelyezése teljes hatását, ha nem csak az Azure Cognitive Search-et kell áthelyeznie.

   Az Azure Storage naplózásra, tudástároló létrehozására szolgál, és a ai-bővítés és indexelés általánosan használt külső adatforrása. A Cognitive Services függőség a ai gazdagításában. Mind a Cognitive Services, mind a keresési szolgáltatás nak ugyanabban a régióban kell lennie, ha a i-dúsítást használ.

1. Hozzon létre egy leltárt a szolgáltatás összes objektumáról, hogy tudja, mit kell áthelyezni: indexeket, szinonimaleképezéseket, indexelőket, adatforrásokat, skillseteket. Ha engedélyezte a naplózást, hozzon létre és archiválja azon jelentéseket, amelyekre egy korábbi bejegyzéshez szüksége lehet.

1. Ellenőrizze az árak és a rendelkezésre állás az új régióban, hogy biztosítsa az Azure Cognitive Search és az új régió kapcsolódó szolgáltatásainak elérhetőségét. A funkciók többsége minden régióban elérhető, de néhány előzetes verziójú funkció korlátozott annektált.

1. Hozzon létre egy szolgáltatást az új régióban, és tegye közzé újra a forráskódból a meglévő indexeket, szinonimát leképezőket, indexelőket, adatforrásokat és skillseteket. Ne feledje, hogy a szolgáltatásneveknek egyedinek kell lenniük, így a meglévő név nem használható fel újra. Ellenőrizze az egyes skillset, hogy ha a kapcsolatok cognitive Services továbbra is érvényesek az azonos régióban követelmény. Tudástárolók létrehozása esetén ellenőrizze az Azure Storage kapcsolati karakterláncait, ha egy másik szolgáltatást használ.

1. Adott esetben töltse be újra az indexeket és a tudástárolókat. Az alkalmazáskódot használja a JSON-adatok indexbe való lelökéséhez, vagy az indexelők ismételt futtatásával a dokumentumok külső forrásokból történő lekérése. 

1. Engedélyezze a naplózást, és ha használja őket, hozzon létre újra biztonsági szerepköröket.

1. Frissítse az ügyfélalkalmazásokat és a tesztcsomagokat az új szolgáltatásnév és API-kulcsok használatához, és tesztelje az összes alkalmazást.

1. Törölje a régi szolgáltatást, miután az új szolgáltatás teljesen tesztelt és működőképes.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozások segítségével további információkat találhat a fent ismertetett lépések végrehajtásakor.

+ [Az Azure Cognitive Search díjszabása és régiói](https://azure.microsoft.com/pricing/details/search/)
+ [Szint kiválasztása](search-sku-tier.md)
+ [Keresési szolgáltatás létrehozása](search-create-service-portal.md)
+ [Keresési dokumentumok betöltése](search-what-is-data-import.md)
+ [Naplózás engedélyezése](search-monitor-logs.md)


<!-- To move your Azure Cognitive Service account from one region to another, you will create an export template to move your subscription(s). After moving your subscription, you will need to move your data and recreate your service.

In this article, you'll learn how to:

> [!div class="checklist"]
> * Export a template.
> * Modify the template: adding the target region, search and storage account names.
> * Deploy the template to create the new search and storage accounts.
> * Verify your service status in the new region
> * Clean up resources in the source region.

## Prerequisites

- Ensure that the services and features that your account uses are supported in the target region.

- For preview features, ensure that your subscription is whitelisted for the target region. For more information about preview features, see [knowledge stores](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [incremental enrichment](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual), and [private endpoint](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## Assessment and planning

When you move your search service to the new region, you will need to [move your data to the new storage service](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) and then rebuild your indexes, skillsets and knowledge stores. You should record current settings and copy json files to make the rebuilding of your service easier and faster.

## Moving your search service's resources

To start you will export and then modify a Resource Manager template.

### Export a template

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Go to your Resource Group page.

> [!div class="mx-imgBorder"]
> ![Resource Group page example](./media/search-move-resource/export-template-sample.png)

3. Select **All resources**.

3. In the left hand navigation menu select **Export template**.

4. Choose **Download** in the **Export template** page.

5. Locate the .zip file that you downloaded from the portal, and unzip that file to a folder of your choice.

The zip file contains the .json files that comprise the template and scripts to deploy the template.

### Modify the template

You will modify the template by changing the search and storage account names and regions. The names must follow the rules for each service and region naming conventions. 

To obtain region location codes, see [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  The code for a region is the region name with no spaces, **Central US** = **centralus**.

1. In the Azure portal, select **Create a resource**.

2. In **Search the Marketplace**, type **template deployment**, and then press **ENTER**.

3. Select **Template deployment**.

4. Select **Create**.

5. Select **Build your own template in the editor**.

6. Select **Load file**, and then follow the instructions to load the **template.json** file that you downloaded and unzipped in the previous section.

7. In the **template.json** file, name the target search and storage accounts by setting the default value of the search and storage account names. 

8. Edit the **location** property in the **template.json** file to the target region for both your search and storage services. This example sets the target region to `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2020-03-13",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### Deploy the template

1. Save the **template.json** file.

2. Enter or select the property values:

- **Subscription**: Select an Azure subscription.

- **Resource group**: Select **Create new** and give the resource group a name.

- **Location**: Select an Azure location.

3. Click the **I agree to the terms and conditions stated above** checkbox, and then click the **Select Purchase** button.

## Verifying your services' status in new region

To verify the move, open the new resource group and your services will be listed with the new region.

To move your data from your source region to the target region, please see this article's guidelines for [moving your data to the new storage account](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## Clean up resources in your original region

To commit the changes and complete the move of your service account, delete the source service account.

## Next steps

[Create an index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Create a skillset](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Create a knowledge store](https://docs.microsoft.com/azure/search/knowledge-store-create-portal) -->