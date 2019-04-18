---
title: Felügyelt identitás a Data Factory |} A Microsoft Docs
description: További információk a felügyelt identitás az Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59260748"
---
# <a name="managed-identity-for-data-factory"></a>Felügyelt identitás Data Factoryhoz

Ez a cikk segít megérteni a felügyelt identitás Mi a Data Factory (korábbi nevén ismert, Felügyeltszolgáltatás-identitás/MSI) és működését.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Amikor egy adat-előállítót hoz létre, egy felügyelt identitás gyári létrehozása mellett is létrehozható. A felügyelt identitás egy felügyelt alkalmazást, regisztrálni az Azure Active Directory, és az adott adat-előállító jelöli.

A Data Factory felügyelt identitás számos előnyt biztosít, a következő funkciókat:

- [Hitelesítő adatok Store az Azure Key Vaultban](store-credentials-in-key-vault.md), ebben az esetben a data factory által felügyelt identitás az Azure Key Vault hitelesítési szolgál.
- Összekötők, többek között [Azure Blob storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md), és [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webes tevékenység](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Hozzon létre felügyelt identitás

A Data Factory felügyelt identitás jön létre a következő:

- A data factory segítségével létrehozásakor **az Azure portal vagy a PowerShell**, felügyelt identitás mindig automatikusan létrejönnek.
- Keresztül adat-előállító létrehozása során **SDK**, felügyelt identitás hozható létre, csak akkor, ha a megadott "Identity új FactoryIdentity() =" az előállító objektum létrehozásához. A példa [.NET – rövid útmutató – adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- A data factory segítségével létrehozásakor **REST API-val**, felügyelt identitás hozható létre, csak akkor, ha a "identity" szakaszban adja meg a kérelem törzsében. A példa [REST-rövid útmutató – adat-előállító létrehozása](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Ha látja az adat-előállító nem rendelkezik egy felügyelt identitás kapcsolódó következő [lekérése a felügyelt identitás](#retrieve-managed-identity) utasítást, explicit módon létrehozhat egyet a data factory programozott módon az identitás kezdeményező frissítésével:

- [Hozzon létre felügyelt identitás PowerShell-lel](#generate-managed-identity-using-powershell)
- [A REST API használatával felügyelt identitás létrehozása](#generate-managed-identity-using-rest-api)
- [Hozzon létre egy Azure Resource Manager-sablon használatával felügyelt identitás](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Hozzon létre felügyelt identitás SDK-val](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Felügyelt identitás nem módosítható. Egy adat-előállítót, amely már rendelkezik egy felügyelt identitás frissítése nem lesz hatással, a felügyelt identitás változatlan marad.
>- Ha frissít egy adat-előállítót, amely már rendelkezik egy felügyelt identitás, a példánygyár objektumot a "identity" paraméter megadása nélkül, vagy a REST-kérés törzse "identity" szakasz megadása nélkül, megjelenik egy hibaüzenet.
>- Ha töröl egy adat-előállító, a kapcsolódó felügyelt identitás mentén törlődni fog.

### <a name="generate-managed-identity-using-powershell"></a>Hozzon létre felügyelt identitás PowerShell-lel

Hívás **Set-AzDataFactoryV2** parancsot újra, majd "Identity" az újonnan létrehozott mezők:

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

### <a name="generate-managed-identity-using-rest-api"></a>A REST API használatával felügyelt identitás létrehozása

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

**Válasz**: felügyelt identitás automatikusan létrejön, és a "identity" szakasz ennek megfelelően fel van töltve.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Hozzon létre egy Azure Resource Manager-sablon használatával felügyelt identitás

**Sablon**: hozzáadása "identity": {"type": "SystemAssigned"}.

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

### <a name="generate-managed-identity-using-sdk"></a>Hozzon létre felügyelt identitás SDK-val

A data factory create_or_update függvény identitással = új FactoryIdentity(). A mintakód .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Felügyelt identitás beolvasása

A felügyelt identitás, az Azure Portalon vagy programozott módon kérheti le. A következő szakaszok bemutatják az egyes minták.

>[!TIP]
> Ha nem látja a felügyelt identitás [készítése felügyelt identitás](#generate-managed-identity) az előállító frissítésével.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Az Azure portal használatával felügyelt identitás beolvasása

A felügyelt azonosító adatok az Azure portal -> annak a data factory -> Tulajdonságok:

- Felügyelt identitás objektumazonosítója
- Felügyelt identitás bérlője
- **Felügyelt identitás Alkalmazásazonosítója** > másolja ezt az értéket

![Felügyelt identitás beolvasása](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell-lel felügyelt identitás beolvasása

A felügyelt identitás résztvevő-Azonosítóját és bérlőazonosítóját vissza kell adni, ha egy adott adat-előállítót a következő:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

A résztvevő-azonosító másolja, majd futtassa az Azure Active Directory-parancshoz alább résztvevő-azonosító paraméter használatával beolvas, a **ApplicationId**, amellyel hozzáférést:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő témakörök, amelyek vezeti be, mikor és hogyan használja a data factory által felügyelt identitás:

- [Hitelesítő adatok Store az Azure Key Vaultban](store-credentials-in-key-vault.md)
- [Másolja az adatokat, és az Azure Data Lake Store az Azure-erőforrások hitelesítési felügyelt identitások használatával](connector-azure-data-lake-store.md)

Lásd: [felügyelt identitások Azure-erőforrások áttekintő](/azure/active-directory/managed-identities-azure-resources/overview) típusán alapuló további háttérben az Azure-erőforrások, mely a data factory által felügyelt identitás felügyelt identitások számára. 
