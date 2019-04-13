---
title: Adatok másolása Dynamics CRM vagy a Dynamics 365 (Common Data Service) és az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat a Microsoft Dynamics CRM vagy a Microsoft Dynamics 365 (Common Data Service szolgáltatásban) a támogatott adattárak fogadó, illetve a támogatott forrás adattárak a Dynamics CRM vagy a Dynamics 365, egy data factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: jingwang
ms.openlocfilehash: 772b9b191a2e6464ff481ff6661308e00ef6033a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545433"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Adatok másolása (Common Data Service) Dynamics 365 vagy Dynamics CRM-hez és az Azure Data Factory használatával

Ez a cikk ismerteti az Azure Data Factory másolási tevékenység használatával a Microsoft Dynamics 365 vagy a Microsoft Dynamics CRM-hez és az adatok másolása. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatokat másolhat a Dynamics 365 (Common Data Service) vagy a Dynamics CRM bármely támogatott fogadó adattárba. Is másolhatja adatokat bármely támogatott forrásadattárból (Common Data Service) Dynamics 365 vagy Dynamics CRM-hez. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

A Dynamics-összekötő a következő Dynamics-verziók és a hitelesítési típusokat támogatja. (Internetes Elérésű az internetre irányuló rövid a telepítés.)

| Dynamics-verziók | Hitelesítési típusok | A kapcsolódószolgáltatás-minták |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online- és Office 365-hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 a helyszínen az internetes Elérésű <br> A Dynamics CRM 2016 helyszíni internetes Elérésű <br> A Dynamics CRM 2015 a helyszínen az internetes Elérésű | IFD | [Dynamics helyszíni az internetes Elérésű + internetes Elérésű hitelesítés](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365, a következő alkalmazástípusokat támogatja:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Customer Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Többi alkalmazástípus esetében például pénzügyi, és nem támogatott az összekötő által Operations, szakembereket, stb.

>[!TIP]
>Adatokat másolni **Dynamics 365 Finance and Operations**, használhatja a [Dynamics AX-összekötő](connector-dynamics-ax.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek adott Data Factory-entitások definiálása Dynamics-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Dynamics-társított szolgáltatást a következő tulajdonságok támogatottak.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és Dynamics CRM Online

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus, a Dynamics-példány. Lehet **"Online"** Dynamics online. | Igen |
| serviceUri | A szolgáltatás URL-címét a Dynamics-példányt, pl. `https://adfdynamics.crm.dynamics.com`. | Igen |
| authenticationType | A hitelesítési típus, a Dynamics-kiszolgálóhoz való csatlakozáshoz. Adja meg **"Office 365"** Dynamics online. | Igen |
| felhasználónév | Adja meg a felhasználónevet, Dynamics csatlakozni. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nincs forrás, Igen a fogadó Ha a forrás-beli társított szolgáltatás nem rendelkezik egy saját üzemeltetésű integrációs |

>[!IMPORTANT]
>Adatok másolása a Dynamics-be, amikor az alapértelmezett Azure integrációs modul másolás végrehajtásához nem használható. Más szóval, ha a forrás-beli társított szolgáltatás nem rendelkezik a megadott integration Runtime-nak explicit módon [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) és a egy Önhöz közeli helyszínt a Dynamics-példány. Keresse meg, hol található a Dynamics-példány lére a [régió lista a Dynamics 365](https://docs.microsoft.com/dynamics365/customer-engagement/admin/datacenter/new-datacenter-regions). Társítsa azt a Dynamics társított szolgáltatás az alábbi példában látható módon.

>[!NOTE]
>A Dynamics-összekötő segítségével "szervezetnév" tulajdonság csak akkor használható a Dynamics CRM/365 Online példányát azonosítja. Tartja működik, amíg a használata javasolt a jobb teljesítmény érdekében például felderítési próbál a jeggyel Ehelyett adja meg az új "serviceUri" tulajdonság.

**Példa: Dynamics online Office 365-hitelesítés használatával**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 és Dynamics CRM a helyszínen az internetes Elérésű

*Hasonlítsa össze a Dynamics online további tulajdonságokat a következők: "állomásnév" és "port".*

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **Dynamics**. | Igen |
| deploymentType | A központi telepítési típus, a Dynamics-példány. Lehet **"OnPremisesWithIfd"** Dynamics helyszíni az internetes Elérésű.| Igen |
| Állomásnév | A helyszíni Dynamics-kiszolgáló állomásneve. | Igen |
| port | A port, a helyszíni Dynamics-kiszolgáló. | Nem, az alapértelmezett érték 443 |
| Cégnév | A szervezet neve, a Dynamics-példány. | Igen |
| authenticationType | A hitelesítési típus, a Dynamics-kiszolgálóhoz való csatlakozáshoz. Adja meg **"internetes telepítés"** Dynamics helyszíni az internetes Elérésű. | Igen |
| felhasználónév | Adja meg a felhasználónevet, Dynamics csatlakozni. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Válassza ezt a mezőt megjelölése a SecureString tárolja biztonságos helyen az ADF-ben, vagy a jelszó tárolásához az Azure Key Vaultban, és lehetővé teszik a másolási tevékenység végrehajtásakor az adatok másolása innen lekéréses – ismerje meg alaposabban a [Store hitelesítő adatokat a Key Vaultban](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [integrációs modul](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. | Nincs forrás, a fogadó Igen |

>[!IMPORTANT]
>Az adatok másolása a Dynamics-be explicit módon [Azure integrációs modul létrehozása](create-azure-integration-runtime.md#create-azure-ir) közelében a Dynamics-példány helyét. Társítsa azt a társított szolgáltatás az alábbi példában látható módon.

**Példa: Dynamics a helyszínen az internetes Elérésű, internetes Elérésű hitelesítés használatával**

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
                "type": "SecureString",
                "value": "<password>"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Dynamics-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a kezdő és a Dynamics, állítsa be a type tulajdonság, az adatkészlet **DynamicsEntity**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani **DynamicsEntity**. |Igen |
| entityName | Az entitást lekérdezni a logikai neve. | Nincs forrás (Ha a tevékenység forrása az "query" van megadva), a fogadó Igen |

> [!IMPORTANT]
>- Adatok másolása a Dynamics, a "struktúra" szakaszban esetén nem kötelező, de recommanded Dynamics adatkészlet determinisztikus másolási eredmény biztosítása érdekében. Azt határozza meg a Dynamics-adatok, másolja át kívánt oszlop nevét és adattípusát. További tudnivalókért lásd: [adatkészlet-szerkezetekben](concepts-datasets-linked-services.md#dataset-structure) és [adattípus-leképezés Dynamics](#data-type-mapping-for-dynamics).
>- A szerzői műveletek a felhasználói felület séma importálásakor ADF kikövetkeztetni a séma szerint, a Dynamics-lekérdezés eredménye, amelyben nincs érték megkülönbözteti a kis oszlopok kimarad a struktúra építésével inicializálása a legelső sorok mintavételi. Tekintse át, és adjon hozzá több oszlopot, a Dynamics séma/adatkészletszerkezet szükséges, amely másolása során lesz figyelembe.
>- Adatok másolása a Dynamics, a "struktúra" szakaszban esetén nem kötelező, a Dynamics-adatkészletben. Mely oszlopokat másolja be a forrás sémát határozza meg. Ha a forrás, fejléc nélküli CSV-fájl a bemeneti adatkészletben, adja meg a "struktúra" az az oszlop nevét és adattípusát. Ezek a CSV-fájl egyenként ahhoz szereplő mezők leképezése.

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
        "typeProperties": {
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

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a Dynamics-forrás és fogadó típusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="dynamics-as-a-source-type"></a>Dynamics-forrás típusa

Adatok másolása a Dynamics, állítsa be a forrás típusaként a másolási tevékenység **DynamicsSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása típusa tulajdonságát állítsa **DynamicsSource**. | Igen |
| lekérdezés | FetchXML egy saját fejlesztésű lekérdezési nyelvet használ, a Dynamics (online és helyszíni). Tekintse meg a következő példát. További tudnivalókért lásd: [FeachXML lekérdezéseket hozhat létre](https://msdn.microsoft.com/library/gg328332.aspx). | Nem (ha az adatkészlet "entityName" van megadva) |

>[!NOTE]
>Az oszlophoz a rendszer mindig másolja akkor is, ha az oszlop leképezése, konfigurálja a FetchXML-lekérdezés nem tartalmazza azt.

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

### <a name="sample-fetchxml-query"></a>Sample FetchXML query

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics, a fogadó típusa

Adatok másolása a Dynamics, állítsa a fogadó típusa a másolási tevékenység **DynamicsSink**. A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó típusa tulajdonságát állítsa **DynamicsSink**. | Igen |
| WriteBehavior | A művelet írási viselkedését.<br/>Az érték engedélyezett **"Upsert"**. | Igen |
| WriteBatchSize | A sorok száma az egyes kötegekben lévő Dynamics írt adatok. | Nem (az alapértelmezett érték 10) |
| ignoreNullValues | Azt jelzi, hogy a bemeneti adatok (kivéve a kulcsmezők) null értéket figyelmen kívül a írási művelet során.<br/>Engedélyezett értékek a következők **igaz** és **hamis**.<br>- **Igaz**: Hagyja meg az adatokat a rendeltetési objektum változatlan marad, ha így tesz, upsert/frissítés művelet. Helyezze be egy meghatározott alapértelmezett értéket, amikor ezt teszi, hogy egy insert művelet.<br/>- **FALSE (hamis)**: Frissítse az adatokat a rendeltetési objektumban NULL upsert/frissítés művelet végrehajtásakor. NULL érték szúrható, amikor ezt teszi, hogy egy insert művelet. | Nem (az alapértelmezett érték FALSE (hamis)) |

>[!NOTE]
>Az alapértelmezett érték a fogadó "**writeBatchSize**"és a másolási tevékenység"**[parallelCopies](copy-activity-performance.md#parallel-copy)**", a Dynamics-fogadó le mindkét 10. Tehát 100 rekordig elküldi Dynamics egyidejűleg.

A Dynamics 365 online, nincs korlát [2 egyidejű batch hívások szervezetenként](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Ha túllépi ezt a korlátot, az első kérelem minden eddiginél végrehajtása előtt egy "Foglalt kiszolgáló" hiba lépett fel. "WriteBatchSize" tartja, legfeljebb 10 ne volna az ilyen egyidejű hívás szabályozás.

Az optimális kombinációja "**writeBatchSize**"és"**parallelCopies**" függ az entitás sémájának például oszlopok, a sor mérete, a beépülő modulok/munkafolyamatok/munkafolyamat-tevékenység csatolja száma száma az e hívásokat, és így tovább. A beállítás alapértelmezett értékét 10 writeBatchSize * 10 parallelCopies a javaslat Dynamics-szolgáltatás, amely a legtöbb Dynamics-entitások azonban nem feltétlenül meg a legjobb teljesítmény akkor működik megfelelően. A teljesítmény hangolhassa a másolási tevékenység beállításainál együttes módosításával.

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

## <a name="data-type-mapping-for-dynamics"></a>Adattípus a következő Dynamics hozzárendelése

Ha az adatok másolása a Dynamics, a Data Factory-közbenső adattípusok a következő hozzárendeléseket használtak Dynamics adattípusok. Hogyan a másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó kapcsolatban lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md).

A Data Factory megfelelő adattípusokat konfigurálja a forrás Dynamics adattípus alapján a következő hozzárendelési táblázatában adatkészlet-szerkezetekben.

| Dynamics-adattípus | Data Factory közbenső adattípus | Forrásként támogatott | Fogadóként támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode.Boolean | Logikai | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | | 
| AttributeType.DateTime | Dátum és idő | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (a kapcsolódó egyetlen cél) |
| AttributeType.ManagedProperty | Logikai | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> AttributeType.CalendarRules és AttributeType.PartyList Dynamics adattípusok nem támogatottak.

## <a name="next-steps"></a>További lépések
A másolási tevékenység, Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
