---
title: Adatok másolása a és a rendszerből a Dynamics CRM-be vagy a Dynamics 365-es verzióra (Common Data Service) Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatokat a Microsoft Dynamics CRM vagy a Microsoft Dynamics 365 (Common Data Service) rendszerből a fogadó adattárakba vagy a támogatott forrás-adattárakból a Dynamics CRM-be vagy a Dynamics 365-be a másolási tevékenység használatával egy adatfeldolgozó-folyamaton belül.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: c9adcf72eeec82fd4b8f1805fca1f284c0b953b7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680984"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Adatok másolása a és a rendszerből a Dynamics 365 (Common Data Service) vagy a Dynamics CRM-be a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban adatok másolása a Microsoft Dynamics 365 vagy a Microsoft Dynamics CRM rendszerbe. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Dynamics 365 (Common Data Service) vagy a Dynamics CRM adatait átmásolhatja bármely támogatott fogadó adattárba. Az adatok bármely támogatott forrásból származó adattárból is átmásolhatók a Dynamics 365 (Common Data Service) vagy a Dynamics CRM-be. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Dynamics-összekötő a 7. x és 9. x verziójú Dynamics-verziót egyaránt támogatja online vagy helyszíni verzióban. Pontosabban,

- 7-es verzió. x a Dynamics CRM 2015-hez
- A 8. x verzió a Dynamics CRM 2016-re és a Dynamics 365 korábbi verziójára mutat
- A 9. x verzió a Dynamics 365 újabb verziójára mutat

Tekintse át a következő táblázatot a megfelelő Dynamics-verziókra/-termékekre vonatkozó támogatott hitelesítési típusokról és konfigurációkról. (Az internetre irányuló központi telepítés nem rövid.)

| Dynamics-verziók | Hitelesítési típusok | Társított szolgáltatási minták |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office 365 | [Dynamics online + Office 365-hitelesítés](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 helyszíni központi TELEPÍTÉSsel <br> Dynamics CRM 2016 helyszíni központi TELEPÍTÉSsel <br> Dynamics CRM 2015 helyszíni központi TELEPÍTÉSsel | IFD | [Helyszíni Dynamics és központi telepítés – hitelesítés](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

A Dynamics 365 esetében a következő típusú alkalmazások támogatottak:

- Dynamics 365 értékesítéshez
- Dynamics 365 az ügyfélszolgálathoz
- Dynamics 365 a Field Service-hez
- Dynamics 365 a Project Service Automation szolgáltatáshoz
- A Dynamics 365 for marketing

Az összekötő nem támogatja más típusú alkalmazások, például a Pénzügy és a műveletek, a tehetségek stb. használatát.

Ez a Dynamics-összekötő a [Dynamics xrm-eszközökre](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)épül.

>[!TIP]
>A **dynamics 365-as pénzügyi és-műveletek**adatainak másolásához használhatja a [Dynamics AX-összekötőt](connector-dynamics-ax.md).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a rendszer a Dynamics-specifikus entitások Data Factory definiálásához használ.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Dynamics társított szolgáltatás a következő tulajdonságokat támogatja.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 és Dynamics CRM Online

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **Dynamics**, **DynamicsCrm**vagy **CommonDataServiceForApps**értékre kell beállítani. | Igen |
| Megadva | A Dynamics-példány központi telepítési típusa. A Dynamics online számára **"online"** állapotúnak kell lennie. | Igen |
| serviceUri | A Dynamics-példány szolgáltatásának URL-címe, például `https://adfdynamics.crm.dynamics.com`. | Igen |
| authenticationType | A Dynamics-kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítési típus. A Dynamics online számára válassza a **"Office 365"** lehetőséget. | Igen |
| felhasználónév | Adja meg a Dynamicshoz való kapcsolódáshoz használandó felhasználónevet. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem, forrás, igen, ha a forráshoz társított szolgáltatás nem rendelkezik integrációs futtatókörnyezettel |

>[!NOTE]
>Az opcionális "szervezetnév" tulajdonság használatára használt Dynamics Connector a Dynamics CRM/365 online-példány azonosításához. Miközben folyamatosan működik, az új "serviceUri" tulajdonságot kell megadnia ahelyett, hogy jobb teljesítményt szeretne a példányok felderítésében.

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>A Dynamics 365 és a Dynamics CRM helyszíni központi TELEPÍTÉSsel

*A Dynamics online-hoz képest további tulajdonságok a következők: "állomásnév" és "Port".*

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot **Dynamics**, **DynamicsCrm**vagy **CommonDataServiceForApps**értékre kell beállítani. | Igen |
| Megadva | A Dynamics-példány központi telepítési típusa. A helyszíni Dynamics számára a következőnek kell lennie: **"OnPremisesWithIfd"** .| Igen |
| hostName | A helyszíni Dynamics-kiszolgáló állomásneve. | Igen |
| port | A helyszíni Dynamics-kiszolgáló portja. | Nem, az alapértelmezett érték 443 |
| Szervezetnév | A Dynamics-példány szervezetének neve. | Igen |
| authenticationType | A Dynamics-kiszolgálóhoz való kapcsolódáshoz szükséges hitelesítési típus. A központi TELEPÍTÉSsel rendelkező Dynamics helyszíni környezetének **"központi telepítés"** megadása. | Igen |
| felhasználónév | Adja meg a Dynamicshoz való kapcsolódáshoz használandó felhasználónevet. | Igen |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Dönthet úgy, hogy ezt a mezőt SecureString jelöli, hogy az ADF-ben biztonságosan tárolja, vagy a jelszót tárolja Azure Key Vaultban, és hagyja, hogy a másolási tevékenység az Adatmásolás során lekérjen – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | Nem, forrás, igen, fogadó |

**Példa: helyszíni Dynamics helyszíni és központi telepítéssel történő hitelesítéssel**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Dynamics-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok a és a Dynamics rendszerbe való másolásához a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát **DynamicsEntity**, **DynamicsCrmEntity**vagy **CommonDataServiceForAppsEntity**értékre kell beállítani. |Igen |
| entityName | A lekérdezni kívánt entitás logikai neve. | Nem forrás (ha a tevékenység forrásában a "Query" érték van megadva), igen a fogadó számára |

**Példa**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Dynamics forrás és a fogadó típusa által támogatott tulajdonságok listáját tartalmazza.

### <a name="dynamics-as-a-source-type"></a>A Dynamics forrás típusa

Az adatok Dynamicsból való másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát **DynamicsSource**, **DynamicsCrmSource**vagy **CommonDataServiceForAppsSource**értékre kell beállítani. | Igen |
| lekérdezés | A FetchXML egy saját lekérdezési nyelv, amelyet a Dynamics (online és a helyszíni) használ. Tekintse meg a következő példát. További információ: [lekérdezések készítése a FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nem (ha meg van adva a "entityName" az adatkészletben) |

>[!NOTE]
>A PK oszlop mindig akkor is másolódik, ha a FetchXML-lekérdezésben konfigurált oszlop nem tartalmazza azt.

> [!IMPORTANT]
>- A Dynamicsből származó adatok másolása esetén a dinamika és a fogadó közötti explicit oszlop-hozzárendelés nem kötelező, de a determinisztikus másolási eredményének biztosítása érdekében a rendszer kifejezetten újrakövetelte a szolgáltatást.
>- Amikor sémát importál az authoring UI-ben, az ADF kikövetkezteti a sémát a Dynamics lekérdezési eredményből származó legfelső sorok mintavételezésével a forrás oszlopok listájának inicializálásához. ebben az esetben a legfelső sorokban található értékeket nem tartalmazó oszlopok maradnak meg. Ugyanez a viselkedés a másolási végrehajtások esetében is érvényes, ha nincs explicit leképezés. Áttekintheti és hozzáadhat további oszlopokat a leképezéshez, amely a másolási futtatókörnyezet során tiszteletben marad.

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

### <a name="sample-fetchxml-query"></a>Példa FetchXML-lekérdezésre

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

### <a name="dynamics-as-a-sink-type"></a>A Dynamics fogadó típusa

Az adatmásoláshoz a másolási tevékenység fogadója szakasz a következő tulajdonságokat támogatja :

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának Type tulajdonságát **DynamicsSink**, **DynamicsCrmSink**vagy **CommonDataServiceForAppsSink**értékre kell beállítani. | Igen |
| WriteBehavior | A művelet írási viselkedése.<br/>Az engedélyezett érték: **"Upsert"** . | Igen |
| alternateKeyName | A "Upsert" művelet végrehajtásához adja meg az entitásban definiált másodlagos kulcs nevét. | Nem |
| writeBatchSize | Az egyes kötegekben a Dynamicsba írt adatsorok száma. | Nem (az alapértelmezett érték 10) |
| ignoreNullValues | Azt jelzi, hogy a rendszer figyelmen kívül hagyja-e az írási művelet során a bemeneti adatokból származó null értékeket (kivéve a legfontosabb mezőket).<br/>Az engedélyezett értékek értéke **igaz** és **hamis**.<br>- **true (igaz**): az upsert/Update művelet végrehajtásakor a célként megadott objektumban lévő adatértékek változatlanok maradnak. Definiált alapértelmezett érték beszúrása egy beszúrási művelet végrehajtásakor.<br/>- **hamis**: a upsert vagy-frissítési művelet végrehajtásakor a cél objektumban lévő ADATFRISSÍTÉS NULL értékűre. Szúrjon be egy NULL értéket a beszúrási művelet végrehajtásakor. | Nem (az alapértelmezett érték hamis) |

>[!NOTE]
>A "**writeBatchSize**" fogadó alapértelmezett értéke és a " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " másolási tevékenység a Dynamics fogadó esetében egyaránt 10. Ezért az 100-es rekordokat a Dynamics párhuzamosan küldi el.

A Dynamics 365 online esetében legfeljebb [2 egyidejű batch-hívás adható meg a szervezeten belül](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Ha túllépi a korlátot, a rendszer a "kiszolgáló foglalt" hibát az első kérelem végrehajtása előtt kidobja. Ha a "writeBatchSize" értéke kisebb vagy egyenlő, mint 10, akkor elkerülhető az egyidejű hívások szabályozása.

A "**writeBatchSize**" és a "**parallelCopies**" optimális kombinációja az entitás sémájától függ, például az oszlopok számával, a sorok méretével, a beépülő modulok/munkafolyamatok/munkafolyamat-tevékenységek számával, valamint a hívásokkal. A 10 writeBatchSize * 10 parallelCopies alapértelmezett beállítása a Dynamics Service-nek megfelelő javaslat, amely a legtöbb Dynamics-entitás esetében működni fog, bár nem lehet a legjobb teljesítmény. A teljesítmény finomhangolásához módosítsa a kombinációt a másolási tevékenység beállításaiban.

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

## <a name="data-type-mapping-for-dynamics"></a>Adattípusok leképezése a Dynamics számára

A Dynamicsből másolt adatok másolásakor a rendszer a következő leképezéseket használja a Dynamics-adattípusokból Data Factory átmeneti adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

Konfigurálja a megfelelő Data Factory adattípust egy adatkészlet-struktúrában a forrás Dynamics-adattípus alapján a következő leképezési táblázat használatával.

| Dynamics-adattípus | Data Factory időközi adattípus | Forrásként támogatott | Fogadóként támogatott |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Hosszú | ✓ | ✓ |
| AttributeTypeCode. Boolean | Logikai | ✓ | ✓ |
| AttributeType. Customer | GUID | ✓ | |
| AttributeType. DateTime | datetime | ✓ | ✓ |
| AttributeType. decimális | Decimális | ✓ | ✓ |
| AttributeType. Double | duplán | ✓ | ✓ |
| AttributeType. EntityName | Sztring | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (egyetlen célként társított céllal) |
| AttributeType. ManagedProperty | Logikai | ✓ | |
| AttributeType. Memo | Sztring | ✓ | ✓ |
| AttributeType. Money | Decimális | ✓ | ✓ |
| AttributeType. Owner | GUID | ✓ | |
| AttributeType. lista | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Sztring | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> A Dynamics-adattípusok AttributeType. CalendarRules, AttributeType. MultiSelectPicklist és AttributeType. PartyList nem támogatottak.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>További lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
