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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: f7604e251bd62ec382ac9ace3de058e345abb863
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Hitelesítő adatok tárolása az Azure Key Vault

Az adattároló hitelesítő adatait tárolhatja egy [Azure Key Vault](../key-vault/key-vault-whatis.md). Az Azure Data Factory olvassa be a hitelesítő adatokat, a tárolót használó tevékenység végrehajtása közben. Jelenleg csak [Dynamics összekötő](connector-dynamics-crm-office-365.md) és [Salesforce összekötő](connector-salesforce.md) támogatja ezt a szolgáltatást.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [adat-előállító version1 dokumentációja](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a funkció a data factory szolgáltatásidentitás támaszkodik. Ismerje meg, hogyan működik a [Data factory szolgáltatásidentitás](data-factory-service-identity.md) , hogy a data factory van társítva egy.

## <a name="steps"></a>Lépések

Az Azure Key Vault tárolt hitelesítő adatok hivatkozik, kell:

1. [Beolvasása a data factory szolgáltatásidentitás](data-factory-service-identity.md#retrieve-service-identity) a "SERVICE IDENTITÁS alkalmazás Azonosítójának értéke" jön létre, valamint a gyári másolásával.
2. A szolgáltatás identitásának hozzáférési jogot az Azure Key Vault. A key vaultban -> a hozzáférés-vezérlési -> Hozzáadás -> a szolgáltatásalkalmazás-identitás hozzáadása legalább azonosító keresési **olvasó** engedéllyel. Lehetővé teszi a kijelölt gyári titkos kulcstároló eléréséhez.
3. Az Azure Key Vault mutató társított szolgáltatás létrehozása. Tekintse meg [a társított szolgáltatásnak Azure Key Vault](#azure-key-vault-linked-service).
4. Adatok kapcsolódó tároló szolgáltatás, mely hivatkozás található a megfelelő mappájában tárolt titkos kulcsot tároló létrehozása. Tekintse meg [key vaultban tárolt hitelesítő adatok hivatkozhat](#reference-credential-stored-in-key-vault).

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).