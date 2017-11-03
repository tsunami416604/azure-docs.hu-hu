---
title: "Hitelesítő adatok tárolása az Azure Key Vault |} Microsoft Docs"
description: "Útmutató adatok tárolóinak egy, az Azure Data Factory automatikusan, futásidőben kérheti le az Azure key vault használt hitelesítő adatok tárolására."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 193d7c77f01384106b3e0d932d02ba6cdff9e750
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása az Azure Key Vault

Az adattároló hitelesítő adatait tárolhatja egy [Azure Key Vault](../key-vault/key-vault-whatis.md). Az Azure Data Factory olvassa be a hitelesítő adatokat, a tárolót használó tevékenység végrehajtása közben. Jelenleg csak [Dynamics összekötő](connector-dynamics-crm-office-365.md) támogatja ezt a szolgáltatást.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [a Data Factory version1 documenttion](v1/data-factory-introduction.md).

## <a name="steps"></a>Lépések

Egy adat-előállító létrehozásakor szolgáltatásidentitás létrehozása mellett hozhatók létre. A szolgáltatásidentitás egy kezelt alkalmazást, regisztrálni Azure tevékenység könyvtárba, és adott data factory jelöli.

- Adat-előállító keresztül létrehozásakor **az Azure portál vagy PowerShell**, szolgáltatásidentitás mindig automatikusan létrejön nyilvános előzetes verzió óta.
- Adat-előállító keresztül létrehozásakor **SDK**, szolgáltatásidentitás hozható létre, csak akkor, ha megadja "identitás új FactoryIdentity() =" a gyári objektum létrehozásához. A következő példa [.NET gyors üzembe helyezés – adat-előállító létrehozása](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Adat-előállító keresztül létrehozásakor **REST API**, szolgáltatásidentitás hozható létre, csak akkor, ha a kérés törzsében megadott "identity" szakasz. A következő példa [REST-gyors üzembe helyezés – adat-előállító létrehozása](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Az Azure Key Vault tárolt hitelesítő adatok hivatkozik, kell:

1. Másolja át a "SERVICE IDENTITÁS Alkalmazásazonosító" jön létre a data factory együtt. Tekintse meg [szolgáltatásidentitás beolvasása](#retrieve-service-identity).
2. A szolgáltatás identitásának hozzáférési jogot az Azure Key Vault. A key vaultban -> a hozzáférés-vezérlési -> Hozzáadás -> keresse meg a szolgáltatás identitásának Alkalmazásazonosító hozzáadása olvasási engedéllyel. Lehetővé teszi a kijelölt gyári titkos kulcstároló eléréséhez.
3. Az Azure Key Vault mutató társított szolgáltatás létrehozása. Tekintse meg [a társított szolgáltatásnak Azure Key Vault](#azure-key-vault-linked-service).
4. Adatok kapcsolódó tároló szolgáltatás, mely hivatkozás található a megfelelő mappájában tárolt titkos kulcsot tároló létrehozása. Tekintse meg [key vaultban tárolt hitelesítő adatok hivatkozhat](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Szolgáltatásidentitás beolvasása

A szolgáltatásidentitás Azure-portálon vagy programozottan kérheti le. Az alábbi szakaszok néhány minta látható.

>[!TIP]
> Ha nem látja a szolgáltatásidentitás [szolgáltatásidentitás létrehozása](#generate-service-identity) a gyári frissítésével.

### <a name="using-azure-portal"></a>Az Azure Portal használata

A szolgáltatás azonosító adatok Azure-portálon -> megtalálhatja a data factory -> Beállítások -> tulajdonságai:

- SZOLGÁLTATÁS IDENTITÁSÁNAK AZONOSÍTÓJA
- SZOLGÁLTATÁS IDENTITÁSÁNAK BÉRLŐI
- **SZOLGÁLTATÁS IDENTITÁSÁNAK Alkalmazásazonosító** > ezt az értéket a Key Vault hozzáférést másolása

![Szolgáltatásidentitás beolvasása](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>A PowerShell használata

A szolgáltatásidentitás résztvevő-azonosító és a bérlő azonosítója visszatér, amikor egy adott adat-előállítót az alábbiak szerint:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

A résztvevő-azonosító másolja, majd a futtató alatt az Azure Active Directory-parancshoz egyszerű ID paraméter használatával beolvassa a **ApplicationId**, melynek segítségével hozzáférést biztosíthat a Key Vault:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Az Azure Key Vault társított szolgáltatás

Csatolt Azure Key Vault szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **AzureKeyVault**. | Igen |
| BaseUrl | Adja meg az Azure Key Vault URL-CÍMÉT. | Igen |

**Példa**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>A key vaultban tárolt hivatkozás hitelesítő adatok

A következő tulajdonságok kulcstároló titkos kulcs hivatkozó társított szolgáltatás mező konfigurálásakor támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A mező a type tulajdonságot kell beállítani: **AzureKeyVaultSecret**. | Igen |
| secretName | Az azure key vault a titkos kulcs neve. | Igen |
| secretVersion | A titkos kulcsot az azure key vault verzióját.<br/>Ha nincs megadva, a legújabb verzióját a titkos kulcsot mindig használja.<br/>Ha meg van adva, majd csatlakoztat a megadott verzió lesz.| Nem |
| tároló | Egy Azure Key Vault kapcsolódó szolgáltatás, amely a hitelesítő adatok tárolása hivatkozik. | Igen |

**Példa: (lásd a "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Szolgáltatásidentitás létrehozása

Ha megtalálta a data factory nem rendelkezik a következő társított szolgáltatásidentitás [szolgáltatásidentitás beolvasása](#retrieve-service-identity) utasítás, hozhat létre egy adat-előállító programozott módon való identitás kezdeményező frissítése.

> [!NOTE]
> - **Szolgáltatásidentitás nem módosítható**. Egy adat-előállítót, amelyhez már tartozik szolgáltatásidentitás frissítése nem kell gyakorolt hatás, a szolgáltatásidentitás nem módosított megmarad.
> - **Szolgáltatásidentitás nem lehet törölni**. Ha frissíti egy adat-előállítót, amelyeknek már szolgáltatásidentitás, a gyári objektumban "identity" paraméter megadása nélkül, vagy "identity" szakasz többi kérés törzsében megadása nélkül, elérhetővé válik a hibát.

Az alábbi szakaszok néhány minták megjelenítése identitás a beépített generálása, ha nem létezik.

### <a name="using-powershell"></a>A PowerShell használata

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

### <a name="using-rest-api"></a>A REST API használata

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

### <a name="using-sdk"></a>SDK használatával

Hívja meg a data factory create_or_update függvényt identitású új FactoryIdentity() =. A mintakód .NET használatával:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).