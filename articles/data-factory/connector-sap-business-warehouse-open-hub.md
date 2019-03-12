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
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 474ebaad60328b011e91337c46040ae37c603e21
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731054"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Adatok másolása az SAP Business warehouse-hoz az Azure Data Factory használatával nyissa meg központon keresztül

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factory adatokat másolni a egy SAP Business Warehouse (BW) megnyitott központon keresztül. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

SAP Business Warehouse nyílt központon keresztül érkező adatokat másolhatja bármely támogatott fogadó adattárba. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az SAP Business Warehouse nyílt Hub összekötő támogatja:

- SAP Business Warehouse **7.01 vagy újabb verzió (a legutóbbi SAP támogatási csomag veremben lévő a 2015 évi. után kiadott)**.
- Adatmásolás keresztül nyitva Hub cél helyi táblában, amely alá lehet DSO, InfoCube, MultiProvider, adatforrás, stb.
- Alapszintű hitelesítés használata az adatok másolását.
- Csatlakozás az alkalmazáskiszolgáló.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub Integration 

[Az SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) hatékony módja az adatok kinyerése az SAP BW. Az alábbi ábrán egy tipikus folyamatok rendelkező ügyfeleink az SAP-rendszerhez, mely megkülönbözteti a kis adatfolyam-gyűjteményre, a SAP ECC -> PSA az -> DSO adatkocka ->.

Az SAP BW Open Hub cél (OHD), amelyhez az SAP-adatok továbbítón keresztüli a cél határozza meg. Támogatott által SAP adatok átvitele folyamatban (DTP) objektumok használható nyílt hub adatforrások, például DSO InfoCube, adatforrás, stb. Nyissa meg Hub cél típusa – a továbbítón keresztüli tárolódnak az adatok - adatbázistáblák (helyi vagy távoli) is lehet, és egybesimított fájlok. Az SAP BW Open Hub connector támogatása az adatok másolása OHD BW helyi táblájában. Abban az esetben, ha használ egyéb, közvetlenül csatlakozhat az adatbázis vagy fájl rendszer más összekötők használatával.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Változások a kinyerési folyamat

Az ADF SAP BW Open Eseményközpont-összekötő biztosít két nem kötelező tulajdonság: `excludeLastRequest` és `baseRequestId` nyílt hubról különbözeti terhelés kezelésére használható. 

- **excludeLastRequestId**: -E zárva a legutóbbi kérés rekordjait. Alapértelmezett érték: igaz. 
- **baseRequestId**: A változásadatok betöltéséhez vonatkozó kérés azonosítója. Be van állítva, csak a Kérelemazonosító: Ez a tulajdonság értéke nagyobb az adatok lekérésének. 

Az Azure Data Factory (ADF) az SAP InfoProviders kinyerési összességében 2 lépésekből áll: 

1. **SAP BW adatok átvitele folyamatban (DTP)** ebben a lépésben az adatokat másolja az SAP BW InfoProvider SAP BW Open Hub táblához 

1. **Adatok másolása az ADF** ebben a lépésben a nyitott Hub tábla olvassa el az ADF-összekötővel 

![Változások a kinyerési folyamat](media\connector-sap-business-warehouse-open-hub\delta-extraction-flow.png)

Az első lépésben egy DTP hajtja végre. Minden egyes végrehajtása hoz létre egy új SAP-kérelmet. A kérelem azonosítója a nyitott Hub tábla tárolja, és ezután az ADF-összekötő által azonosítására szolgál a különbözeti. Aszinkron módon futtatja a két lépés: a DTP az SAP által aktivált, és az ADF adatmásolás keresztül ADF aktiválódik. 

Alapértelmezés szerint az ADF nem olvassa a legújabb különbözeti a nyitott Hub tábla ("kizárása utolsó kérelem" beállítás értéke igaz). Ezennel az ADF-ben az adatok nem 100 %-os naprakészen tartása a (a legutóbbi különbözeti nem található) nyissa meg a Hub táblában lévő adatokat. Cserébe Ez az eljárás biztosítja, hogy nincsenek sorok beolvasása elveszett aszinkron kibontásával okozhatja. Jól működik akkor is, ha az ADF éppen olvas az Open Hub táblázat, a DTP még mindig ugyanazon a táblán való írása közben. 

Általában a maximális másolt Kérelemazonosító tárolása a legutóbbi futtatás ADF egy átmeneti adattár (például az Azure Blob a fenti ábra). Ezért a kérésben nem olvasható másodszor ADF a következő futtatás. Ugyanakkor vegye figyelembe az adatok nem törlődnek automatikusan, a nyílt Hub táblából.

Megfelelő különbözeti azt nem engedélyezett szeretné, hogy a kérelem különböző DTPs azonosítókat nyissa meg az Eseményközpont ugyanabban a táblában. Ezért kell ne hozzon létre több DTP minden nyitott Hub cél (OHD) számára. Ha az azonos InfoProvider a teljes és különbözeti kinyerési, létre kell hoznia két OHDs számára az azonos InfoProvider. 

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

> [!TIP]
>
> Az SAP BW Open Eseményközpont-összekötő használatával, olvassa [SAP Business Warehouse (BW) az adatok betöltése az Azure Data Factory használatával](load-sap-bw-data.md).

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
