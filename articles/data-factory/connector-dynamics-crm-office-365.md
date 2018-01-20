---
title: "Adatok másolása/Dynamics CRM-hez és az Azure Data Factory használatával 365 |} Microsoft Docs"
description: "Útmutató adatok a Dynamics CRM-hez és 365 támogatott fogadó adattárolókhoz (vagy másolása) a támogatott forráshierarchiából adattárolókhoz a Dynamics CRM és 365 egy Azure Data Factory-folyamat a másolási tevékenység használatával."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: d577db2b2f14da61baccfb6230b0c6e03a62b9b1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Másolja az adatokat, vagy Dynamics 365 / Dynamics CRM, Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és a Dynamics 365 / Dynamics CRM. Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [másolási tevékenység során a V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása Dynamics 365 / Dynamics CRM bármely támogatott fogadó adattár, vagy bármely támogatott forrás adattár Dynamics 365 / Dynamics CRM-adatok másolása. A másolási tevékenység által támogatott adatforrások/mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Dynamics összekötő támogatja az alábbi Dynamics verziója és a hitelesítési típus (*ELÉRÉS rövid a internetes elérésű telepítés*):

| Dynamics verziók | Hitelesítési típusok | A kapcsolódószolgáltatás-minták |
|:--- |:--- |:--- |
| Dynamics 365 online <br> A Dynamics CRM online | Office365 | [Dynamics Online + Office365 hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 helyszíni ELÉRÉS <br> Dynamics CRM 2016 helyszíni ELÉRÉS <br> Dynamics CRM 2015 a helyszíni az ELÉRÉS | IFD | [Dynamics a helyszíni az internetes Elérésű + ELÉRÉS hitelesítés](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365 konkrétan az alábbi alkalmazástípusokat támogatja:

- Dynamics 365 az értékesítés
- Dynamics 365 az ügyfélszolgálat
- Dynamics 365 mező szolgáltatás
- Dynamics 365 a projekt szolgáltatás automatizálásához
- Dynamics 365 a Marketing

> [!NOTE]
> Dynamics összekötő használatára, a jelszó tárolása az Azure Key Vault, valamint lehetővé teszik a másolási tevékenységek lekéréses ott adatmásolás végrehajtása során. Tekintse meg a konfigurálása a [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

A következő szakaszok részletesen bemutatják Dynamics adat-előállító tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Dynamics társított szolgáltatás támogatott a következő tulajdonságokkal:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és a Dynamics CRM Online

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus a Dynamics példány. Kell **"Online"** Dynamics online. | Igen |
| Szervezetnév | A Dynamics példány szervezet nevét. | Nem szabad megadni, ha a felhasználóhoz társított egynél több Dynamics példány. |
| authenticationType | A hitelesítési típus Dynamics kiszolgálóhoz való csatlakozáshoz. Adja meg **"Office365"** Dynamics Online. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. A jelszó be az Azure Key Vault, és a jelszót, mint a "AzureKeyVaultSecret" konfigurálása rendelkezik. A további [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nincs forrás, Igen a fogadó Ha forrás társított szolgáltatás nem rendelkezik IR |

>[!IMPORTANT]
>Ha az adatok másolásának **történő** Dynamics Azure integrációs futásidejű hajtható végre a példány nem használható alapértelmezett. A más word, ha a forrás társított szolgáltatás nem rendelkezik megadott IR, explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) a következő példa a Dynamics, és a Dynamics hivatkozott szolgáltatásban található társítható közelében hellyel rendelkező.

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
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
| authenticationType | A hitelesítési típus Dynamics kiszolgálóhoz való csatlakozáshoz. Adja meg **"Elérés"** Dynamics helyszíni az Internetre. | Igen |
| felhasználónév | Adja meg a felhasználónevet a Dynamics való kapcsolódáshoz. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ne feledje, a jelszó be az Azure Key Vault, és a jelszót, mint a "AzureKeyVaultSecret" konfigurálja. A további [hitelesítő adatok tárolása a Key Vault](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. | Nem a forrást, a fogadó Igen |

>[!IMPORTANT]
>Használatával adatait átmásolhatja Dynamics, explicit módon [hozzon létre egy Azure-IR](create-azure-integration-runtime.md#create-azure-ir) a következő példa a Dynamics, és a hivatkozott szolgáltatásban található társítható közelében helyét.

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Dynamics dataset által támogatott tulajdonságokról listáját tartalmazza.

Adatok másolása/Dynamics, az adatkészlet típus tulajdonságának beállítása **DynamicsEntity**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **DynamicsEntity** |Igen |
| entityName | Az entitást lekérdezni a logikai neve. | Nincs forrás (Ha a tevékenység forrása "lekérdezést" van megadva), a fogadó az Igen |

> [!IMPORTANT]
>- **Az adatok másolása Dynamics, ha szükség-e a "structure" szakasz** Dynamics adatkészletben, amely megadja, hogy másolja át a kívánt Dynamics adatokat az oszlop nevét és az adatok típusa. A további [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) és a [adattípus-hozzárendelése Dynamics](#data-type-mapping-for-dynamics).
>- **Adat másolása az Dynamics, ha a "structure" szakasz nem kötelező** Dynamics adatkészletben. Mely oszlop(ok) másolja a forrás adatok séma határozza meg. Ha a forrás nélkül fejléc, CSV-fájl a bemeneti adatkészletet, adja meg a "structure" oszlop nevét és az adatok típusú mezőket a CSV-fájlban egyenként ahhoz, hogy.

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

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Dynamics forrás és a fogadó által támogatott tulajdonságokról listáját tartalmazza.

### <a name="dynamics-as-source"></a>Dynamics forrásaként

Adatok másolása Dynamics, állítsa be a forrás típusa a másolási tevékenység **DynamicsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **DynamicsSource**  | Igen |
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

### <a name="dynamics-as-sink"></a>Dynamics mint fogadó

Adatok másolása Dynamics, állítsa be a fogadó típusa a másolási tevékenység **DynamicsSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **DynamicsSink**  | Igen |
| WriteBehavior | A művelet írási viselkedését.<br/>Az érték engedélyezett: **"Upsert"**. | Igen |
| writeBatchSize | A sorok számát az egyes kötegekben Dynamics írt adatok. | Nem (alapértelmezett érték 10) |
| ignoreNullValues | Azt jelzi, hogy a bemeneti adatokból (kivéve a kulcsmezők) null értékek kihagyása írási művelet során.<br/>Két érték engedélyezett: **igaz**, és **hamis**.<br>-igaz: hagyja az adatokat a cél objektum változatlan upsert/frissítési művelet során, és illessze be meghatározott alapértelmezett értéket insert művelet során.<br/>-hamis: frissítse az adatokat a célobjektum NULL upsert/frissítési művelet során, és illessze be NULL értéket, insert művelet során.  | Nem (alapértelmezett értéke "false") |

>[!NOTE]
>Az alapértelmezett érték a fogadó writeBatchSize és a másolási tevékenység [parallelCopies](copy-activity-performance.md#parallel-copy) Dynamics fogadó esetén mindkét 10, ami azt jelenti, hogy nem továbbíthatók Dynamics egyidejűleg 100 rekordja.

**Példa**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Adattípus-leképezést Dynamics

Ha az adatok másolása Dynamics, az Azure Data Factory ideiglenes adattípusok a következő megfeleltetéseket használtak Dynamics adattípusok. Lásd: [séma- és írja be a leképezéseket](copy-activity-schema-and-type-mapping.md) hogyan másolási tevékenység van leképezve a séma- és adatok típusa a fogadó tájékozódhat.

A Data Factory megfelelő adattípus konfigurálja a forrás Dynamics adatok alapján adatkészlet-szerkezetekben írja be az alábbi leképezési táblázat segítségével:

| Dynamics adattípus | Data factory ideiglenes adattípus | Támogatott forrásaként | A fogadó támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode.Boolean | Logikai | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ |  |
| AttributeType.DateTime | Dátum és idő | ✓ | ✓ |
| AttributeType.Decimal | Decimális | ✓ | ✓ |
| AttributeType.Double | Dupla | ✓ | ✓ |
| AttributeType.EntityName | Karakterlánc | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ |  |
| AttributeType.ManagedProperty | Logikai | ✓ |  |
| AttributeType.Memo | Karakterlánc | ✓ | ✓ |
| AttributeType.Money | Decimális | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Karakterlánc | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Dynamics adattípus AttributeType.CalendarRules és AttributeType.PartyList nem támogatottak.

## <a name="next-steps"></a>További lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats).