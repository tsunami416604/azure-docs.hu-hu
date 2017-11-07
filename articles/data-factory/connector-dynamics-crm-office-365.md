---
title: "Adatok másolása az Azure Data Factory használatával 365 és a Dynamics CRM-hez |} Microsoft Docs"
description: "További adatok a Dynamics CRM-hez és 365 támogatott fogadó adattárolókhoz másolása az Azure Data Factory-folyamat a másolási tevékenység használatával."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Adatok másolása az Dynamics 365 / Dynamics CRM, Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatokat másolni Dynamics 365 / Dynamics CRM. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Dynamics 365 / Dynamics CRM adatok bármely támogatott fogadó adattárolóhoz másolhatja. A másolási tevékenység által támogatott adatforrások/mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban a Dynamics összekötő támogatja az alábbi Dynamics verziója és a hitelesítési típus:

| Dynamics verziók | Hitelesítési típusok | A kapcsolódószolgáltatás-minták |
|:--- |:--- |:--- |
| Dynamics 365 online <br> A Dynamics CRM online | Office365 | [Dynamics Online + Office365 hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 helyszíni ELÉRÉS <br> Dynamics CRM 2016 helyszíni ELÉRÉS <br> Dynamics CRM 2015 a helyszíni az ELÉRÉS | ELÉRÉS | [Dynamics a helyszíni az internetes Elérésű + ELÉRÉS hitelesítés](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*Internetes Elérésű rövid a internetes elérésű telepítés.*

> [!NOTE]
> Dynamics összekötő használatára, a jelszó az Azure Key Vault tárolja, és lehetővé teszik az ADF másolási tevékenységek lekéréses onnan, adatmásolás végrehajtása során. Tekintse meg a konfigurálása a [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.

## <a name="getting-started"></a>Bevezetés

A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják Dynamics adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Dynamics társított szolgáltatás támogatott a következő tulajdonságokkal:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és a Dynamics CRM Online

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus a Dynamics példány. Kell **"Online"** Dynamics online. | Igen |
| Szervezetnév | A Dynamics példány szervezet nevét. | Nem szabad megadni, ha a felhasználóhoz társított egynél több Dynamics példány. |
| AuthenticationType | A hitelesítési típus Dynamics kiszolgálóhoz való csatlakozáshoz. Adja meg **"Office365"** Dynamics Online. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. A jelszó be az Azure Key Vault, és a jelszót, mint a "AzureKeyVaultSecret" konfigurálása rendelkezik. További információkért lásd: a [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md) cikk. | Igen |

**Példa: A Dynamics online Office365-hitelesítés használatával**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 és a Dynamics CRM a helyszíni az ELÉRÉS

*Online Dyanmics való hasonlítás további tulajdonságok a következők: "állomásnév" és "port".*

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus a Dynamics példány. Kell **"OnPremisesWithIfd"** Dynamics helyszíni az Internetre.| Igen |
| **Állomásnév** | A helyszíni Dynamics kiszolgáló állomásnevét. | Igen |
| **port** | A helyszíni Dynamics kiszolgáló portját. | Nem, az alapértelmezett: 443 |
| Szervezetnév | A Dynamics példány szervezet nevét. | Igen |
| AuthenticationType | A hitelesítési típus Dynamics kiszolgálóhoz való csatlakozáshoz. Adja meg **"Elérés"** Dynamics helyszíni az Internetre. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ne feledje, a jelszó be az Azure Key Vault, és a jelszót, mint a "AzureKeyVaultSecret" konfigurálja. További információkért lásd: a [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md) cikk. | Igen |

**Példa: Dynamics a helyszíni az internetes Elérésű hitelesítéssel ELÉRÉS**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Dynamics dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása Dynamics, az adatkészlet típus tulajdonságának beállítása **DynamicsEntity**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **DynamicsEntity** |Igen |
| entityName | Az entitást lekérdezni a logikai neve. | Nem (Ha a tevékenység forrása az "query" van megadva) |

> [!IMPORTANT]
> **A "structure" című adathalmaz szükség a Dynamics**, amely megadja, hogy másolja át a kívánt Dynamics adatokat az oszlop nevét és az adatok típusa. A további [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) és a [adattípus-hozzárendelése Dynamics](#data-type-mapping-for-dynamics).

**Példa**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Dynamics forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="dynamics-as-source"></a>Dynamics forrásaként

Adatok másolása Dynamics, állítsa be a forrás típusa a másolási tevékenység **DynamicsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **DynamicsSource**  | Igen |
| lekérdezés  | FetchXML egy saját fejlesztésű lekérdező nyelv, amely használatban van a Microsoft Dynamics (online & helyszíni). Lásd az alábbi példa, és további információhoz [hozhatók létre olyan lekérdezések a FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Nem (Ha a "entityName" adatkészlet paraméter van megadva)  |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>A minta FetchXML lekérdezés

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

## <a name="data-type-mapping-for-dynamics"></a>Adattípus-leképezést Dynamics

Ha az adatok másolása Dynamics, az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Dynamics adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

Konfigurálja a megfelelő ADF-adattípus a forrás Dynamics adatok alapján adatkészlet-szerkezetekben írja be az alábbi leképezési táblázat segítségével:

| Dynamics adattípus | Data factory ideiglenes adattípus |
|:--- |:--- |
| AttributeTypeCode.BigInt | Hosszú |
| AttributeTypeCode.Boolean | Logikai érték |
| AttributeType.Customer | GUID |
| AttributeType.DateTime | Dátum és idő |
| AttributeType.Decimal | Decimális |
| AttributeType.Double | Dupla |
| AttributeType.EntityName | Karakterlánc |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | GUID |
| AttributeType.ManagedProperty | Logikai érték |
| AttributeType.Memo | Karakterlánc |
| AttributeType.Money | Decimális |
| AttributeType.Owner | GUID |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | GUID |
| AttributeType.String | Karakterlánc |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Dynamics adattípus AttributeType.CalendarRules és AttributeType.PartyList nem támogatottak.


## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).