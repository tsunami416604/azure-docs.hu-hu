---
title: Az Azure Data Factory szolgáltatásidentitás |} Microsoft Docs
description: További információk a data factory szolgáltatásidentitás Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: 40c2cd4ae808ad3f82028f1ddb7a8e0006676e6e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619286"
---
# <a name="azure-data-factory-service-identity"></a>Az Azure Data Factory-szolgáltatási identitás

Ez a cikk segít megérteni a data factory szolgáltatásidentitás Újdonságok és annak működéséről.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [adat-előállító version1 dokumentációja](v1/data-factory-introduction.md).

## <a name="overview"></a>Áttekintés

Egy adat-előállító létrehozásakor szolgáltatásidentitás létrehozása mellett hozhatók létre. A szolgáltatásidentitás egy kezelt alkalmazást, regisztrálni Azure tevékenység könyvtárba, és adott data factory jelöli.

Data factory szolgáltatásidentitás a következő két szolgáltatás előnyökkel jár:

- [Hitelesítő adatok tárolása az Azure Key Vault](store-credentials-in-key-vault.md), ebben az esetben data factory szolgáltatásidentitást az Azure Key Vault-hitelesítésre használják.
- [Másolja az adatokat, vagy az Azure Data Lake Store](connector-azure-data-lake-store.md), ebben az esetben a data factory szolgáltatásidentitás használható-e a Data Lake Store támogatott hitelesítési típusok egyikét.

## <a name="generate-service-identity"></a>Szolgáltatásidentitás létrehozása

Data factory szolgáltatásidentitás jön létre az alábbiak szerint:

- Adat-előállító keresztül létrehozásakor **az Azure portál vagy PowerShell**, szolgáltatásidentitás mindig automatikusan létrejön az ADF V2 nyilvános előzetes verzió óta.
- Adat-előállító keresztül létrehozásakor **SDK**, szolgáltatásidentitás hozható létre, csak akkor, ha megadja "identitás új FactoryIdentity() =" a gyári objektum létrehozásához. A következő példa [.NET gyors üzembe helyezés – adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Adat-előállító keresztül létrehozásakor **REST API**, szolgáltatásidentitás hozható létre, csak akkor, ha a kérés törzsében megadott "identity" szakasz. A következő példa [REST-gyors üzembe helyezés – adat-előállító létrehozása](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Ha megtalálta a data factory nem rendelkezik a következő társított szolgáltatásidentitás [szolgáltatásidentitás beolvasása](#retrieve-service-identity) utasítás, explicit módon létrehozhat egy adat-előállító programozott módon való identitás kezdeményező frissítése:

- [PowerShell-lel szolgáltatásidentitás létrehozása](#generate-service-identity-using-powershell)
- [REST API használatával szolgáltatásidentitás létrehozása](#generate-service-identity-using-rest-api)
- [SDK használatával szolgáltatásidentitás létrehozása](#generate-service-identity-using-sdk)

>[!NOTE]
>- Szolgáltatásidentitás nem lehet módosítani. Egy adat-előállítót, amelyhez már tartozik szolgáltatásidentitás frissítése nem kell gyakorolt hatás, a szolgáltatásidentitás változatlan marad.
>- Ha frissíti egy adat-előállítót, amelyeknek már szolgáltatásidentitás a gyári objektumban "identity" paraméter megadása nélkül, vagy "identity" szakasz többi kérés törzsében megadása nélkül, elérhetővé válik a hibát.
>- Ha töröl egy adat-előállítót, a társított szolgáltatás identitásának mentén törlődni fog.

### <a name="generate-service-identity-using-powershell"></a>PowerShell-lel szolgáltatásidentitás létrehozása

Hívás **Set-AzureRmDataFactoryV2** parancs újra, a "Identity" mezők rendszer újonnan létrehozott látja majd:

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

### <a name="generate-service-identity-using-rest-api"></a>REST API használatával szolgáltatásidentitás létrehozása

Alább API-t hívja meg a kérelem törzsében szereplő "identity" szakasz:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**A kérelem törzsében**: "identity" hozzáadása: {"type": "SystemAssigned"}.

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

**Válasz**: szolgáltatásidentitás automatikusan létrejön, és a "identity" szakasz ennek megfelelően fel van töltve.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>SDK használatával szolgáltatásidentitás létrehozása

Hívja meg a data factory create_or_update függvényt identitású új FactoryIdentity() =. A mintakód .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Szolgáltatásidentitás beolvasása

A szolgáltatásidentitás Azure-portálon vagy programozottan kérheti le. Az alábbi szakaszok néhány minta látható.

>[!TIP]
> Ha nem látja a szolgáltatásidentitás [szolgáltatásidentitás létrehozása](#generate-service-identity) a gyári frissítésével.

### <a name="retrieve-service-identity-using-azure-portal"></a>Azure-portál használatával szolgáltatásidentitás beolvasása

A szolgáltatás azonosító adatok Azure-portálon -> megtalálhatja a data factory -> Beállítások -> tulajdonságai:

- SZOLGÁLTATÁS IDENTITÁSÁNAK AZONOSÍTÓJA
- SZOLGÁLTATÁS IDENTITÁSÁNAK BÉRLŐI
- **SZOLGÁLTATÁS IDENTITÁSÁNAK Alkalmazásazonosító** > Ez az érték másolása

![Szolgáltatásidentitás beolvasása](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>PowerShell-lel szolgáltatásidentitás beolvasása

A szolgáltatásidentitás résztvevő-azonosító és a bérlő azonosítója visszatér, amikor egy adott adat-előállítót az alábbiak szerint:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

A résztvevő-azonosító másolja, majd a futtató alatt az Azure Active Directory-parancshoz egyszerű ID paraméter használatával beolvassa a **ApplicationId**, amelyek segítségével engedélyezheti a hozzáférést:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>További lépések
Az alábbi témakörökben bevezető mikor és hogyan használható a data factory identitás:

- [Hitelesítő adatok tárolása az Azure Key Vault](store-credentials-in-key-vault.md)
- [Adatok másolása az/Azure Data Lake Store felügyelt identitás-hitelesítéssel](connector-azure-data-lake-store.md)

Lásd: [MSI áttekintése](~/articles/active-directory/msi-overview.md) További háttérinformációk a felügyelt Szolgáltatásidentitás, mely data factory szolgáltatásidentitás alapul. 
