---
title: Adatok másolása az SAP Business Warehouse-ból az Open Hub-on keresztül
description: Megtudhatja, hogyan másolhatja az adatokat az SAP Business Warehouse (BW) open hubon keresztül támogatott fogadó adattárak egy Azure Data Factory-folyamat másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ff3b4799f42e85ad3df62ef18469a26120ae3021
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418082"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Adatok másolása az SAP Business Warehouse-ból az Open Hub szolgáltatáson keresztül az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory-ban az SAP Business Warehouse (BW) open hubon keresztül történő másolásához. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>Ha meg szeretné tudni, hogy az ADF általános támogatást nyújt az SAP-adatintegrációs forgatókönyvhöz, tekintse meg az [SAP-adatok integrálását az Azure Data Factory használatával című tanulmányban,](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) részletes bevezetéssel, összevetéssel és útmutatással.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Business Warehouse open hub-összekötőn keresztül a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

Az SAP Business Warehouse-ból az Open Hub-on keresztül adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Ez az SAP Business Warehouse Open Hub-összekötő különösen a következőket támogatja:

- SAP Business Warehouse **7.01-es vagy újabb verziója (a 2015-ös év után kiadott legutóbbi SAP támogatási csomagveremben).**
- Adatok másolása az Open Hub Destination helyi táblán keresztül, amely alatta lehet DSO, InfoCube, MultiProvider, DataSource stb.
- Adatok másolása egyszerű hitelesítéssel.
- Csatlakozás az alkalmazáskiszolgálóhoz.

## <a name="sap-bw-open-hub-integration"></a>SAP BW open hub integráció 

[Az SAP BW Open Hub service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) hatékony módja az SAP BW-ből történő adatok kinyerése. Az alábbi ábrán az ügyfelek az SAP-rendszerükben található tipikus folyamatok egyikét mutatják be, amely esetben az SAP ECC -> PSA -> DSO -> Cube-ból származó adatfolyamok.

Az SAP BW Open Hub Destination (OHD) határozza meg azt a célt, amelyre az SAP-adatok továbbításra kerül. Az SAP adatátviteli folyamat (DTP) által támogatott objektumok nyílt hub adatforrásként használhatók, például DSO, InfoCube, DataSource stb. A Hub Destination nyílt típusa – ahol a továbbított adatok tárolódnak – lehet adatbázistábla (helyi vagy távoli) és egyhelyen eső fájlok. Ez az SAP BW Open Hub-összekötő támogatja az OHD helyi táblából származó adatok másolását a BW-ben. Abban az esetben, ha más típusokat használ, közvetlenül csatlakozhat az adatbázishoz vagy a fájlrendszerhez más összekötők használatával.

![SAP BW nyílt központ](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Delta extrakciós folyamat

Az ADF SAP BW Open Hub `excludeLastRequest` `baseRequestId` Connector két választható tulajdonságot kínál: és amely az Open Hub különbözeti terheléskezelésére használható. 

- **excludeLastRequestId**: Az utolsó kérelem rekordjainak kizárása. Az alapértelmezett érték igaz. 
- **baseRequestId**: A különbözeti betöltési kérelem azonosítója. Beállítása után csak a tulajdonság értékénnél nagyobb requestId azonosítóval rendelkező adatok kerülnek beolvasásra. 

Összességében az SAP InfoProviders-tól az Azure Data Factory (ADF) szolgáltatásig történő kinyerés két lépésből áll: 

1. **SAP BW adatátviteli folyamat (DTP)** Ez a lépés az SAP BW InfoProvider adatait másolja egy SAP BW Open Hub táblába 

1. **ADF-adatok másolása** Ebben a lépésben az Open Hub táblát az ADF-összekötő olvassa be 

![Delta extrakciós folyamat](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Az első lépésben a DTP végrehajtása történik. Minden végrehajtás létrehoz egy új SAP-kérelemazonosítót. A kérelemazonosító az Open Hub táblában tárolódik, majd az ADF-összekötő a delta azonosítására használja. A két lépés aszinkron módon fut: a DTP-t az SAP indítja el, és az ADF-adatmásolás az ADF-en keresztül aktiválódik. 

Alapértelmezés szerint az ADF nem olvassa a legújabb különbözetet az Open Hub táblából (az "Utolsó kérelem kizárása" beállítás igaz). Ezáltal az ADF adatai nem 100%-ban naprakészek az Open Hub táblában szereplő adatokkal (az utolsó delta hiányzik). Cserébe ez az eljárás biztosítja, hogy az aszinkron kinyerés által okozott sorok ne vesszenek el. Akkor is jól működik, ha az ADF az Open Hub táblát olvassa, miközben a DTP még mindig ugyanabba a táblázatba ír. 

Általában tárolja a maximális másolt kérelem azonosítóját az ADF által utoljára futtatott átmeneti adattárban (például az Azure Blob a fenti ábrán). Ezért ugyanazt a kérést az ADF nem olvassa el másodszor a következő futtatássorán. Eközben vegye figyelembe, hogy az adatok nem törlődnek automatikusan az Open Hub táblából.

A megfelelő különbözeti kezelés hez nem engedélyezett, hogy különböző DTP-ktől kérjen azonosítókat ugyanabban az Open Hub táblában. Ezért nem hozhat létre egynél több DTP-t minden egyes nyílt központi célhoz (OHD). Ha ugyanattól az InfoSzolgáltatótól teljes és különbözeti kinyerésre van szüksége, két OHD-t kell létrehoznia ugyanahhoz az InfoProvider-hez. 

## <a name="prerequisites"></a>Előfeltételek

Az SAP Business Warehouse Open Hub összekötő használatához a következőket kell használnia:

- Állítsa be a saját üzemeltetésű integrációs futásidejű 3.13-as vagy újabb verzióval. A [részleteket a saját üzemeltetésű integrációs futásidejű](create-self-hosted-integration-runtime.md) cikkben találja.

- Töltse le a **64 bites [SAP .NET Connector 3.0-s rendszert](https://support.sap.com/en/product/connectors/msnet.html) ** az SAP webhelyéről, és telepítse a saját üzemeltetésű infravörös számítógépre. Telepítéskor a választható beállítási lépések ablakban válassza a **Szerelések telepítése gac-ra** lehetőséget az alábbi képen látható módon. 

    ![Sap .NET-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A Data Factory BW-összekötőben használt SAP-felhasználónak a következő engedélyekkel kell rendelkeznie: 

    - Az RFC és az SAP BW engedélyezése. 
    - A "S_SDSAUTH" engedélyezési objektum "Végrehajtása" tevékenységéhez szükséges engedélyek.

- Hozzon létre SAP Open Hub Destination type **adatbázistáblaként** a "Technical Key" beállításbe van jelölve.  Azt is javasoljuk, hogy hagyja a Törlés i adatok táblából bejelölve, bár ez nem szükséges. Használja ki a DTP-t (közvetlenül hajtsa végre vagy integrálja a meglévő folyamatláncba) a forrásobjektumból (például a kocka) a nyílt hub céltáblájába választott adatok le, hogy az adatokat a nyílt hub céltáblájába hozhassa.

## <a name="getting-started"></a>Első lépések

> [!TIP]
>
> Az SAP BW Open Hub-összekötő használatának forgatókönyve: [Adatok betöltése az SAP Business Warehouseból (BW) az Azure Data Factory használatával](load-sap-bw-data.md)című témakörben található.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok az SAP Business Warehouse Open Hub-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az SAP Business Warehouse Open Hub kapcsolódó szolgáltatása a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **SapOpenHub** | Igen |
| kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | Igen |
| systemNumber (rendszerszám) | Az SAP BW rendszer rendszerszáma.<br/>Megengedett érték: karakterláncként ábrázolt kétjegyű tizedesszám. | Igen |
| ügyfél-azonosító | Az SAP W rendszerben lévő ügyfél ügyfélazonosítója.<br/>Megengedett érték: karakterláncként ábrázolt háromjegyű tizedesszám. | Igen |
| language | Az SAP-rendszer által használt nyelv. | Nem **(az**alapértelmezett érték EN)|
| userName (Felhasználónév) | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| jelszó | A felhasználó jelszava. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) Az előfeltételek ben említettek szerint saját üzemeltetésű [integrációs](#prerequisites)futásidejű re van szükség. |Igen |

**Példa:**

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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [Adatkészletek](concepts-datasets-linked-services.md) című cikkben olvashatja. Ez a szakasz az SAP BW Open Hub adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az SAP BW Open Hubról történő másoláshoz állítsa az adatkészlet típustulajdonságát **sapOpenHubTable (SapOpenHubTable)** tulajdonságra. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot **SapOpenHubTable (SapOpenHubTable )** tulajdonságra kell állítani.  | Igen |
| openHubDestinationName | Az adatok másolni hozandó nyílt központ célneve. | Igen |

Ha beállítást `excludeLastRequest` és `baseRequestId` adatkészletet, továbbra is támogatott, ahogy van, miközben azt javasoljuk, hogy használja az új modell tevékenységforrás megy előre.

**Példa:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz az SAP BW Open Hub forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub forrásként

Az SAP BW Open Hub-ból történő adatok másolásához a következő tulajdonságokat támogatja a másolási tevékenység **forrása** szakasz:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságát** **SapOpenHubSource (SapOpenHubSource)** típusú tulajdonságra kell állítani. | Igen |
| excludeLastRequest | Azt jelzi, hogy ki kell-e zárni az utolsó kérelem rekordjait. | Nem (az alapértelmezett **érték igaz)** |
| baseRequestId | A delta-betöltési kérelem azonosítója. Beállítása után csak a tulajdonság értékénnél **nagyobb** requestId azonosítóval rendelkező adatok kerülnek beolvasásra.  | Nem |

>[!TIP]
>Ha az Open Hub tábla csak az egykérelem-azonosító által létrehozott adatokat tartalmazza, például mindig teljes terhelést végez, és felülírja a táblázatban lévő meglévő adatokat, vagy csak egyszer futtatja a DTP-t tesztre, ne felejtse el kilépni a "excludeLastRequest" opció jelölőnégyzetből az adatok másolásához.

Az adatok betöltésének felgyorsítása érdekében beállíthatja [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) a másolási tevékenységet, hogy párhuzamosan töltse be az adatokat az SAP BW Open Hub-ból. Ha például négyre állít, `parallelCopies` a Data Factory egyidejűleg négy RFC-hívást hajt végre, és minden Egyes RFC-hívás lekéri az ADATOK egy részét az SAP BW Open Hub táblából, amelyet a DTP-kérelemazonosító és a csomagazonosító particionál. Ez akkor érvényes, ha az egyedi DTP-kérelemazonosító + csomagazonosító száma nagyobb, mint a `parallelCopies`értéke. Amikor adatokat másol fájlalapú adattárba, azt is megkell parancsolni, hogy több fájlként írjon egy mappába (csak adja meg a mappa nevét), ebben az esetben a teljesítmény jobb, mint egyetlen fájlba írni.

**Példa:**

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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Adattípus-hozzárendelés az SAP BW Open Hubhoz

Az SAP BW Open Hub adatainak másolásakor a következő leképezések az SAP BW adattípusokból az Azure Data Factory köztes adattípusaiba kerülnek. A [Séma- és adattípus-hozzárendelések](copy-activity-schema-and-type-mapping.md) című témakörből megtudhatja, hogy a másolási tevékenység hogyan képezi le a forrássémát és az adattípust a fogadóhoz.

| SAP ABAP-típus | Adatgyár köztes adattípusa |
|:--- |:--- |
| C (karakterlánc) | Sztring |
| I (egész szám) | Int32 |
| F (úszó) | Double |
| D (dátum) | Sztring |
| T (idő) | Sztring |
| P (BCD csomagolt, pénznem, decimális, mennyiség) | Decimal |
| N (Numc) | Sztring |
| X (bináris és nyers) | Sztring |

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

**Tünetek:** Ha az SAP BW-t hana-n futtatja, és csak az adatok egy részét másolja át az ADF másolási tevékenység (1 millió sor) használatával, a lehetséges ok az, hogy engedélyezi az "SAP HANA Execution" beállítást a DTP-ben, amely esetben az ADF csak az adatok első kötegét tudja beolvasni.

**Felbontás:** Tiltsa le az "SAP HANA Execution" beállítást a DTP-ben, dolgozza fel újra az adatokat, majd próbálja meg újra végrehajtani a másolási tevékenységet.

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
