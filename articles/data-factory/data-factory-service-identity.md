---
title: Az Azure Data Factory-szolgáltatásidentitás |} A Microsoft Docs
description: Tudnivalók a data factory-szolgáltatásidentitás az Azure Data Factoryban.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: 7937836daad5ad299f3e5b7b6b7994ae40a833fd
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446886"
---
# <a name="azure-data-factory-service-identity"></a>Az Azure Data Factory-szolgáltatásidentitás

Ez a cikk segítségével megismerheti, mi a data factory-szolgáltatásidentitás és működését.

## <a name="overview"></a>Áttekintés

Amikor egy adat-előállítót hoz létre, a felügyeltszolgáltatás-identitás gyári létrehozása mellett is létrehozható. A felügyeltszolgáltatás-identitását egy felügyelt alkalmazást, regisztrálni az Azure Active Directory, és az adott adat-előállító jelöli.

Data factory-szolgáltatásidentitás számos előnyt biztosít, a következő funkciókat:

- [Hitelesítő adatok Store az Azure Key Vaultban](store-credentials-in-key-vault.md), ebben az esetben a data factory-szolgáltatásidentitás Azure Key Vault hitelesítési szolgál.
- Összekötők, többek között [Azure Blob storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md), és [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webes tevékenység](control-flow-web-activity.md).

## <a name="generate-service-identity"></a>Felügyeltszolgáltatás-identitás létrehozása

Data factory-szolgáltatásidentitás jön létre a következő:

- A data factory segítségével létrehozásakor **az Azure portal vagy a PowerShell**, a felügyeltszolgáltatás-identitást a rendszer mindig automatikusan létrehozza.
- A data factory segítségével létrehozásakor **SDK**, felügyeltszolgáltatás-identitás hozható létre, csak akkor, ha megad "identitás új FactoryIdentity() =" az előállító objektum létrehozásához. A példa [.NET – rövid útmutató – adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- A data factory segítségével létrehozásakor **REST API-t**, csak akkor, ha a kérelem törzsében megadott "identity" szakasz létrehozza a felügyeltszolgáltatás-identitást. A példa [REST-rövid útmutató – adat-előállító létrehozása](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Ha látja az adat-előállító nem rendelkezik a következő tartozó szolgáltatásidentitás [beolvasni a felügyeltszolgáltatás-identitás](#retrieve-service-identity) utasítást, explicit módon létrehozhat egyet a data factory programozott módon az identitás kezdeményező frissítésével:

- [PowerShell-lel felügyeltszolgáltatás-identitás létrehozása](#generate-service-identity-using-powershell)
- [A REST API használatával felügyeltszolgáltatás-identitás létrehozása](#generate-service-identity-using-rest-api)
- Hozzon létre egy Azure Resource Manager-sablonnal felügyeltszolgáltatás-identitás
- [SDK-val felügyeltszolgáltatás-identitás létrehozása](#generate-service-identity-using-sdk)

>[!NOTE]
>- Felügyeltszolgáltatás-identitás nem módosítható. Egy adat-előállítót, amely már rendelkezik egy felügyeltszolgáltatás-identitás frissítése nem lesz hatással, a felügyeltszolgáltatás-identitás változatlan marad.
>- Ha frissít egy adat-előállítót, amely már rendelkezik egy felügyeltszolgáltatás-identitás, a példánygyár objektumot a "identity" paraméter megadása nélkül, vagy a REST-kérés törzse "identity" szakasz megadása nélkül, megjelenik egy hibaüzenet.
>- Ha töröl egy adat-előállítót, társított szolgáltatásidentitás mentén törlődni fog.

### <a name="generate-service-identity-using-powershell"></a>PowerShell-lel felügyeltszolgáltatás-identitás létrehozása

Hívás **Set-AzureRmDataFactoryV2** parancsot újra, majd "Identity" az újonnan létrehozott mezők:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>A REST API használatával felügyeltszolgáltatás-identitás létrehozása

Alább API-t hívja meg a kérelem törzsében szereplő "identity" szakaszban:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Kérelem törzse**: hozzáadása "identity": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Válasz**: felügyeltszolgáltatás-identitást a rendszer automatikusan létrehozza, és a "identity" szakasz ennek megfelelően fel van töltve.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-service-identity-using-an-azure-resource-manager-template"></a>Hozzon létre egy Azure Resource Manager-sablonnal felügyeltszolgáltatás-identitás

**Sablon**: hozzáadása "identity": {"type": "SystemAssigned"}.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-service-identity-using-sdk"></a>SDK-val felügyeltszolgáltatás-identitás létrehozása

A data factory create_or_update függvény identitással = új FactoryIdentity(). A mintakód .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Felügyeltszolgáltatás-identitás beolvasása

A felügyeltszolgáltatás-identitás az Azure Portalon vagy programozott módon kérheti le. A következő szakaszok bemutatják az egyes minták.

>[!TIP]
> Ha nem látja a felügyeltszolgáltatás-identitás [felügyeltszolgáltatás-identitás létrehozása](#generate-service-identity) az előállító frissítésével.

### <a name="retrieve-service-identity-using-azure-portal"></a>Beolvasni a felügyeltszolgáltatás-identitás az Azure portal használatával

Annak a identitás adatait az Azure portal -> a data factory -> Beállítások -> Tulajdonságok:

- SZOLGÁLTATÁSIDENTITÁS AZONOSÍTÓJA
- SZOLGÁLTATÁSIDENTITÁS BÉRLŐJE
- **SZOLGÁLTATÁSIDENTITÁS Alkalmazásazonosítója** > másolja ezt az értéket

![Felügyeltszolgáltatás-identitás beolvasása](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>PowerShell-lel felügyeltszolgáltatás-identitás beolvasása

A felügyeltszolgáltatás-identitás résztvevő-Azonosítóját és bérlőazonosítóját vissza kell adni, ha egy adott adat-előállítót a következő:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

A résztvevő-azonosító másolja, majd futtassa az Azure Active Directory-parancshoz alább résztvevő-azonosító paraméter használatával beolvas, a **ApplicationId**, amellyel hozzáférést:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő témakörök, amelyek vezeti be, mikor és hogyan használhatja a data factory-szolgáltatásidentitás:

- [Hitelesítő adatok Store az Azure Key Vaultban](store-credentials-in-key-vault.md)
- [Másolja az adatokat, és az Azure Data Lake Store az Azure-erőforrások hitelesítési felügyelt identitások használatával](connector-azure-data-lake-store.md)

Lásd: [felügyelt identitások Azure-erőforrások áttekintő](/azure/active-directory/managed-identities-azure-resources/overview) ismertető háttér-felügyelt identitások Azure-erőforrások számára, mely data factory-szolgáltatásidentitás alapul. 
