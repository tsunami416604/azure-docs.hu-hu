---
title: Felügyelt identitás Data Factoryhoz
description: Ismerje meg az Azure Data Factory felügyelt identitását.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261124"
---
# <a name="managed-identity-for-data-factory"></a>Felügyelt identitás Data Factoryhoz

Ez a cikk segít megérteni, hogy mi a data factory (korábbi nevén felügyelt szolgáltatásidentitás/MSI) felügyelt identitása, és hogyan működik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Adat-előállító létrehozásakor felügyelt identitás hozható létre a gyár létrehozásával együtt. A felügyelt identitás az Azure Active Directory regisztrált felügyelt alkalmazás, és képviseli ezt az adott adat-előállító.

A Data Factory felügyelt identitása a következő funkciókat szolgálja:

- [Tárolja a hitelesítő adatokat az Azure Key Vaultban,](store-credentials-in-key-vault.md)ebben az esetben az adatok által felügyelt identitást használja az Azure Key Vault-hitelesítéshez.
- Összekötők, beleértve [az Azure Blob storage,](connector-azure-blob-storage.md) [Az Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [az Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [az Azure SQL Database](connector-azure-sql-database.md)és az Azure SQL Data [Warehouse.](connector-azure-sql-data-warehouse.md)
- [Webes tevékenység](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Felügyelt identitás létrehozása

A Data Factory felügyelt identitása a következőképpen jön létre:

- Amikor az Azure Portalon vagy a **PowerShellen**keresztül hoz létre adatgyárat, a felügyelt identitás mindig automatikusan létrejön.
- Adatgyár **SDK-n**keresztüli létrehozásakor a felügyelt identitás csak akkor jön létre, ha a gyári objektumban megadja az "Identity = new FactoryIdentity()" értéket a gyári objektumban a létrehozáshoz. Lásd például a [.NET rövid útmutató - hozzon létre adat-gyári](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Adat-előállító rest **API-n**keresztül történő létrehozásakor a felügyelt identitás csak akkor jön létre, ha megadja az "identitás" szakaszt a kérelemtörzsben. Lásd például a [REST rövid útmutató – hozzon létre adat-gyári](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Ha úgy találja, hogy az adat-előállító nem rendelkezik felügyelt identitással a [felügyelt identitáslekérési](#retrieve-managed-identity) utasítás után, explicit módon létrehozhat egyet az adat-előállító rendszerének az identitáskezdeményezővel való frissítésével programozott módon:

- [Felügyelt identitás létrehozása a PowerShell használatával](#generate-managed-identity-using-powershell)
- [Felügyelt identitás létrehozása rest api-val](#generate-managed-identity-using-rest-api)
- [Felügyelt identitás létrehozása Azure Resource Manager-sablon használatával](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Felügyelt identitás létrehozása SDK használatával](#generate-managed-identity-using-sdk)

>[!NOTE]
>- A felügyelt identitás nem módosítható. Egy olyan adat-előállító frissítése, amely már rendelkezik felügyelt identitással, nem lesz hatással, a felügyelt identitás változatlan marad.
>- Ha olyan adat-előállítót frissít, amely már rendelkezik felügyelt identitással anélkül, hogy megadnák az "identitás" paramétert a gyári objektumban, vagy anélkül, hogy megadnák az "identitás" szakaszt a REST kéréstörzsében, hibaüzenetet kap.
>- Adateléntálya törlésekor a társított felügyelt identitás is törlődik.

### <a name="generate-managed-identity-using-powershell"></a>Felügyelt identitás létrehozása a PowerShell használatával

Hívja meg újra **a Set-AzDataFactoryV2** parancsot, majd megjelenik az "Identitás" mezők újonnan létrehozása:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Felügyelt identitás létrehozása rest api-val

Az API alatti hívás a kérelem törzsében az "identitás" szakaszban található:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Kérelem törzse**: adja hozzá az "identitás" értéket: { "type": "SystemAssigned" }.

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

**Válasz**: a felügyelt identitás automatikusan létrejön, és az "identitás" szakasz ennek megfelelően kerül feltöltésre.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Felügyelt identitás létrehozása Azure Resource Manager-sablon használatával

**Sablon**: add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="generate-managed-identity-using-sdk"></a>Felügyelt identitás létrehozása SDK használatával

Hívja meg az adatgyárat create_or_update függvényt az Identity=new FactoryIdentity() függvényben. Mintakód a .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Felügyelt identitás beolvasása

A felügyelt identitás lekérheti az Azure Portalon vagy programozott módon. A következő szakaszok néhány mintát mutatnak be.

>[!TIP]
> Ha nem látja a felügyelt identitást, [hozzon létre felügyelt identitást](#generate-managed-identity) a gyár frissítésével.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Felügyelt identitás lekérése az Azure Portal használatával

A felügyelt identitásadatokat az Azure Portalon –> az adatgyár -> tulajdonságok.

- Felügyelt identitásobjektum-azonosító
- Felügyelt identitás bérlője
- Felügyelt identitásalkalmazás azonosítója

A felügyelt identitásadatok akkor is megjelennek, amikor olyan összekapcsolt szolgáltatást hoz létre, amely támogatja a felügyelt identitáshitelesítést, például az Azure Blobot, az Azure Data Lake Storage-t, az Azure Key Vaultot stb.

Az engedély megadásakor használja az objektumazonosítót vagy az adatgyár nevét (felügyelt identitásnévként) az identitás megkereséséhez.

### <a name="retrieve-managed-identity-using-powershell"></a>Felügyelt identitás lekérése a PowerShell használatával

A felügyelt identitás főazonosítója és a bérlői azonosító t ad vissza, ha egy adott adat-előállító az alábbiak szerint. A **PrincipalId** segítségével adjon hozzáférést:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Az alkalmazásazonosítót a fenti egyszerű azonosító másolásával, majd az Azure Active Directory parancs alatt futó fő azonosító paraméterként.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi témaköröket, amelyek bemutatják, hogy mikor és hogyan kell használni az adat-üzemi kezelt identitást:

- [Hitelesítő adatok tárolása az Azure Key Vaultban](store-credentials-in-key-vault.md)
- [Adatok másolása az Azure Data Lake Store-ból/az Azure Data Lake Store-ba felügyelt identitások használatával az Azure-erőforrások hitelesítéséhez](connector-azure-data-lake-store.md)

Tekintse [meg az Azure-erőforrások felügyelt identitások áttekintése](/azure/active-directory/managed-identities-azure-resources/overview) további háttér az Azure-erőforrások felügyelt identitások, amely az adatok-előde felügyelt identitás alapul. 
