---
title: Adatok másolása az Azure Data Factory használatával nyissa meg központon keresztül SAP Business Warehouse |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat az SAP Business Warehouse (BW) megnyitott központon keresztül támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: 74061eb081fcc7c2c84707f2414a2edfbfde3289
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299537"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Adatok másolása az SAP Business warehouse-hoz az Azure Data Factory használatával nyissa meg központon keresztül

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factory adatokat másolni a egy SAP Business Warehouse (BW) megnyitott központon keresztül. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integration 

[Az SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) hatékony módja az adatok kinyerése az SAP BW. Az alábbi ábrán egy tipikus folyamatok rendelkező ügyfeleink az SAP-rendszerhez, mely megkülönbözteti a kis adatfolyam-gyűjteményre, a SAP ECC -> PSA az -> DSO adatkocka ->.

Az SAP BW Open Hub cél (OHD), amelyhez az SAP-adatok továbbítón keresztüli a cél határozza meg. Támogatott által SAP adatok átvitele folyamatban (DTP) objektumok használható nyílt hub adatforrások, például DSO InfoCube, MultiProvider, adatforrás, stb. Nyissa meg Hub cél típusa – a továbbítón keresztüli tárolódnak az adatok - adatbázistáblák (helyi vagy távoli) is lehet, és egybesimított fájlok. Az SAP BW Open Hub connector támogatása az adatok másolása OHD BW helyi táblájában. Abban az esetben, ha használ egyéb, közvetlenül csatlakozhat az adatbázis vagy fájl rendszer más összekötők használatával.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>Támogatott képességek

SAP Business Warehouse nyílt központon keresztül érkező adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP Business Warehouse nyílt Hub összekötő támogatja:

- SAP Business Warehouse **7.30 vagy újabb verzió (a legutóbbi SAP támogatási csomag veremben lévő a 2015 évi. után kiadott)**.
- Adatmásolás keresztül nyitva Hub cél helyi táblában, amely alá lehet DSO, InfoCube, MultiProvider, adatforrás, stb.
- Alapszintű hitelesítés használata az adatok másolását.
- Csatlakozás az alkalmazáskiszolgáló.

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse nyílt Hub összekötő használatához meg kell:

- Állítsa be egy helyi Integration Runtime 3.13 verziójú vagy újabb. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

- Töltse le a **64 bites [SAP .NET összekötő 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  SAP webhelyről, és telepítse a helyi integrációs modul gépen. Amikor telepíti, a opcionális beállítási lépéseket ablakban jelölje ki a **GAC szerelvények telepítése** lehetőséget az alábbi képen látható módon. 

    ![SAP .NET-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Használja a Data Factory BW-összekötő az SAP-felhasználó kell rendelkeznie az alábbi engedélyek: 

    - RFC és az SAP BW engedélyezését. 
    - A "Végrehajtása" tevékenység engedélyezési objektum "S_SDSAUTH" engedéllyel.

- Hozzon létre az SAP nyílt Hub cél típusú, mint **adatbázistábla** "Műszaki Key" beállítás be van jelölve.  Emellett javasoljuk, hogy a törlés adatok tábla nincs bejelölve, bár ez nem szükséges. Használja ki a DTP (közvetlenül hajtsa végre vagy integrálható a meglévő folyamat lánc) adatok kerül az adatforrás-objektum (például a kocka) nyissa meg a hub céltábla választotta.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok nyújtanak, amelyek meghatározzák az adott Data Factory-entitások SAP Business Warehouse nyílt Hub összekötő-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

SAP Business Warehouse nyissa meg Hub társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **SapOpenHub** | Igen |
| kiszolgáló | A kiszolgálóra, amelyen az SAP BW-példány neve. | Igen |
| systemNumber | Az SAP BW-rendszer rendszer száma.<br/>Érték engedélyezett: kétjegyű tizedes tört egy karakterláncból. | Igen |
| clientId | Az SAP W rendszerben az ügyfél ügyfél-azonosítója.<br/>Érték engedélyezett: háromjegyű tizedes tört egy karakterláncból. | Igen |
| language | Az SAP-rendszer által használt nyelv. | Nem (alapértelmezett érték **EN**)|
| Felhasználónév | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét. | Igen |
| jelszó | A felhasználó jelszava. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Egy helyi Integration Runtime szükség, az említett [Előfeltételek](#prerequisites). |Igen |

**Példa**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. Ez a szakasz a Salesforce-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása a, és az SAP BW Open Hub, állítsa be a type tulajdonság, az adatkészlet **SapOpenHubTable**. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot állítsa **SapOpenHubTable**.  | Igen |
| openHubDestinationName | A megnyitott Hub cél adatokat másolni a nevét. | Igen |
| excludeLastRequest | -E zárva a legutóbbi kérés rekordjait. | Nem (alapértelmezett érték a **igaz**) |
| baseRequestId | A változásadatok betöltéséhez vonatkozó kérés azonosítója. Be van állítva, csak a Kérelemazonosító adatokat **nagyobb** Ez a tulajdonság értékét a rendszer lekéri.  | Nem |

>[!TIP]
>Ha a nyitott Hub tábla csak egyetlen Kérelemazonosító által létrehozott adatokat tartalmaz, például Ön mindig teljes terhelés, és felülírja a meglévő adatokat a táblázatban, vagy csak futtassa egyszer a teszteléshez, a DTP ne feledje, a "excludeLastRequest" jelet annak érdekében, hogy a d másolása az ATA ki.

**Példa**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az SAP BW Open Hub forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-open-hub-as-source"></a>Az SAP BW Open Hub forrásként

Adatok másolása az SAP BW Open Hub, állítsa be a forrás típusaként a másolási tevékenység **SapOpenHubSource**. Noha nem szükséges a másolási tevékenység a további típusspecifikus tulajdonságok **forrás** szakaszban.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW Open hub adattípus-leképezés

Az adatok másolása az SAP BW Open hubhoz, ha a következő hozzárendeléseket használják az SAP BW-adattípusok Azure Data Factory-közbenső adattípusok. Lásd: [séma és adatok írja be a hozzárendelések](copy-activity-schema-and-type-mapping.md) megismerheti, hogyan másolási tevékenység leképezi a forrás séma és adatok típusa a fogadó.

| SAP ABAP Type | Data factory közbenső adattípus |
|:--- |:--- |
| C (karakterlánc) | String |
| I (egész szám) | Int32 |
| F (Float) | Double |
| D (dátum) | String |
| T (idő) | String |
| P (BCD csomagolt, pénznem, Decimal, mennyiség) | Decimal |
| N (Numc) | String |
| X-(bináris és nyers) | String |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
