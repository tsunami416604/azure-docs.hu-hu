---
title: Másolja az adatokat, és az Azure Data Lake Store |} Microsoft Docs
description: Ismerje meg az adatok másolása és Data Lake Store az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8f86f43b4d8c474f338285abffb3c444f5ebc2d7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054738"
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Adatok másolása, illetve onnan Data Lake Store Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-azure-datalake-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Azure Data Lake Store-összekötőt, a V2](../connector-azure-data-lake-store.md).

Ez a cikk ismerteti, hogyan másolási tevékenység az Azure Data Factory és az Azure Data Lake Store áthelyezni az adatokat. Az épít, a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység során adatátvitel áttekintését.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **az Azure Data Lake Store** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **Azure Data Lake store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Data Lake Store-fiók létrehozása a másolási tevékenység során a folyamat létrehozása előtt. További információkért lásd: [Ismerkedés az Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
A Data Lake Store-összekötő e hitelesítési típusokat támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítés 

Azt javasoljuk, hogy a szolgáltatás egyszerű hitelesítést, különösen egy ütemezett adatok másolását a használja. Jogkivonat lejáratáról fordulhat elő a felhasználói hitelesítő adatok hitelesítéssel. További konfigurációs információkért lásd: a [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.

## <a name="get-started"></a>Bevezetés
A másolási tevékenység, amely helyezi át az adatokat az Azure Data Lake Store vagy a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Adatok másolása folyamatokat létrehozni legegyszerűbb módja a használja a **másolása varázsló**. Folyamat létrehozása a varázsló segítségével oktatóanyag, lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md).

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például ha a másolt adatok az az Azure blob storage egy Azure Data Lake Store, akkor két társított szolgáltatások létrehozásához az Azure storage-fiókok és az Azure Data Lake store összekapcsolása a data factory. Azure Data Lake Store jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példában az előző lépésben említett hozzon létre egy adatkészlet adja meg a blob-tároló és a bemeneti adatokat tartalmazó mappát. És hoz létre egy másik DataSet adatkészletben, hogy a mappához, majd a fájl elérési útját adja meg a Data Lake-adattár, amely tárolja az adatokat a blob-tároló átmásolva. Azure Data Lake Store adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában BlobSource forrás-és AzureDataLakeStoreSink akár használhatja a fogadó a másolási tevékenységhez. Ehhez hasonlóan az Azure Data Lake Store az Azure Blob Storage másolása, használható AzureDataLakeStoreSource és BlobSink a másolási tevékenység. Azure Data Lake Store adott tevékenység Tulajdonságok másolása, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban.  

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  A mintában használt adatok másolása az Azure Data Lake Store az adat-előállító entitások JSON-definíciók, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-data-lake-store) című szakaszát.

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory entitások adott Data Lake Store JSON-tulajdonságok.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A társított szolgáltatás adattárat egy adat-előállító hivatkozásokat tartalmaz. Típusú társított szolgáltatás létrehozása **AzureDataLakeStore** a Data Lake Store-adatok összekapcsolása a data factory. Az alábbi táblázat a Data Lake Store kapcsolódó szolgáltatások vonatkozó JSON-elemeket ismerteti. Egyszerű szolgáltatásnév és a felhasználói hitelesítő adatok hitelesítése közül választhat.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **type** | A type tulajdonságot meg kell **AzureDataLakeStore**. | Igen |
| **dataLakeStoreUri** | Az Azure Data Lake Store-fiókja adatait. Ez az információ időt vesz igénybe, a következő formátumok egyikének: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| **subscriptionId** | Azure-előfizetése Azonosítóját, amelyhez a Data Lake Store-fiók tartozik. | A fogadó szükséges |
| **resourceGroupName** | Azure erőforráscsoport-név, amely a Data Lake Store-fiók tartozik. | A fogadó szükséges |

### <a name="service-principal-authentication-recommended"></a>Szolgáltatás egyszerű hitelesítés (ajánlott)
Szolgáltatás egyszerű hitelesítést használ, egy alkalmazás entitás regisztrálni kell az Azure Active Directory (Azure AD), és hozzáférést, a Data Lake Store-bA. Részletes útmutató: [szolgáltatások közötti hitelesítési](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő érték, melynek segítségével határozza meg a társított szolgáltatás:
* Alkalmazásazonosító
* Alkalmazás kulcs 
* Bérlőazonosító

> [!IMPORTANT]
> Győződjön meg arról, hogy biztosítsa a szolgáltatás egyszerű megfelelő engedélyeket az Azure Data Lake Store:
>- **Data Lake Store forrásként használandó**, adja meg legalább **olvasási + Execute** adat-hozzáférési listán, és másolja át egy mappa tartalmát vagy **olvasási** engedéllyel egy fájl másolása. Nem követelmény a fióknak szintű hozzáférés-vezérlés.
>- **A fogadó Data Lake Store használandó**, adja meg legalább **írási + hajtható végre** adat-hozzáférési engedélye a mappában található elemek gyermek létrehozására. Azure IR használatakor építve a másolás és (a forrás- és fogadó vannak felhő), lehetővé teszik a Data Factory Data Lake Store régió észleli, hogy adja meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkörhöz. Ha el szeretné kerülni ezt a IAM-szerepkört [adja meg a executionLocation](data-factory-data-movement-activities.md#global) a Data Lake Store a másolási tevékenység az helyét.
>- Ha Ön **a folyamatok történő másolása varázslóval**, adja meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkörhöz. Is, adja meg legalább **olvasási + Execute** engedéllyel a Data Lake Store gyökér ("/") és gyermekét. Ellenkező esetben előfordulhat, hogy megjelenik az üzenet "a megadott hitelesítő adatok érvénytelenek."

Szolgáltatás egyszerű hitelesítés használatára a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsot. | Igen |
| **Bérlői** | Adja meg a bérlői adatokat (tartomány nevét vagy a bérlő azonosító) alatt az alkalmazás található. Azt az Azure-portál jobb felső sarkában az egér rámutató által kérheti le. | Igen |

**Példa: Szolgáltatás egyszerű hitelesítés**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
Felhasználói hitelesítő adatok hitelesítése segítségével azt is megteheti, másolja a következő tulajdonságok megadásával vagy Data Lake Store-bA:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **Engedélyezési** | Kattintson a **engedélyezés** a Data Factory Editor gombra, és adja meg a hitelesítő adatok, amelyek az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ehhez a tulajdonsághoz. | Igen |
| **sessionId** | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan jön létre, a Data Factory Editor használatakor. | Igen |

> [!IMPORTANT]
> Győződjön meg arról, hogy az engedélyeket a felhasználó megfelelő az Azure Data Lake Store:
>- **Data Lake Store forrásként használandó**, adja meg legalább **olvasási + Execute** adat-hozzáférési listán, és másolja át egy mappa tartalmát vagy **olvasási** engedéllyel egy fájl másolása. Nem követelmény a fióknak szintű hozzáférés-vezérlés.
>- **A fogadó Data Lake Store használandó**, adja meg legalább **írási + hajtható végre** adat-hozzáférési engedélye a mappában található elemek gyermek létrehozására. Azure IR használatakor építve a másolás és (a forrás- és fogadó vannak felhő), lehetővé teszik a Data Factory Data Lake Store régió észleli, hogy adja meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkörhöz. Ha el szeretné kerülni ezt a IAM-szerepkört [adja meg a executionLocation](data-factory-data-movement-activities.md#global) a Data Lake Store a másolási tevékenység az helyét.
>- Ha Ön **a folyamatok történő másolása varázslóval**, adja meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkörhöz. Is, adja meg legalább **olvasási + Execute** engedéllyel a Data Lake Store gyökér ("/") és gyermekét. Ellenkező esetben előfordulhat, hogy megjelenik az üzenet "a megadott hitelesítő adatok érvénytelenek."

**Példa: Felhasználók hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejáratáról
Az engedélyezési kód használatával generáló a **engedélyezés** gombra egy bizonyos idő elteltével lejár. A következő üzenet azt jelenti, hogy a hitelesítési jogkivonat lejárt:

Hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő adatok ellenőrzése. AADSTS70008: A megadott hozzáférés biztosítása lejárt vagy visszavonták. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációazonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyegző: 2015-12-15 21-09-31Z.

Az alábbi táblázat a különböző típusú felhasználói fiókok lejárati időpontja:

| Felhasználó típusa | Után lejár |
|:--- |:--- |
| Felhasználói fiókok *nem* kezeli az Azure Active Directoryban (például @hotmail.com vagy @live.com) |12 óra |
| Felhasználói fiókok Azure Active Directory által felügyelt |14 napja után utolsó újrapróbálása futtatása <br/><br/>90 nap, ha egy olyan OAuth-alapú társított szolgáltatás szelet 14 naponta legalább egyszer fut |

Ha a jogkivonat lejárati idő előtt módosítsa jelszavát, a jogkivonat azonnal lejár. Az ebben a szakaszban korábban említett üzenet jelenik meg.

Segítségével tudja ismét engedélyezheti a fiók a **engedélyezés** gombra kattint, amikor a jogkivonat lejár, a társított szolgáltatás újratelepíteni. Értékek is létrehozhat a **sessionId** és **engedélyezési** tulajdonságok programozott módon az alábbi kód használatával:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
A Data Factory osztályok, a kódban használt kapcsolatos részletekért lásd: a [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), és [ AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témaköröket. Adjon hozzá egy hivatkozást verzióra `2.9.10826.1824` a `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` a a `WindowsFormsWebAuthenticationDialog` a kódban használt osztály.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

**Jelenség:** adatok másolásakor **történő** Azure Data Lake Store, ha a másolási tevékenység során a következő hiba miatt sikertelen:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Alapvető ok:** 2 oka:

1. A `resourceGroupName` és/vagy `subscriptionId` az Azure Data Lake Store kapcsolódó szolgáltatás megadott helytelen;
2. A felhasználó vagy az egyszerű szolgáltatás nem rendelkezik a szükséges engedéllyel.

**Megoldás:**

1. Győződjön meg arról, hogy a `subscriptionId` és `resourceGroupName` adja meg, ha a hivatkozott szolgáltatásban található `typeProperties` azok valóban, amelyekre a data lake-fiókhoz tartozik.

2. Győződjön meg arról, hogy megadja a legalább "**olvasó**" a felhasználó vagy a data lake-fiókhoz az egyszerű szolgáltatásnév szerepkör. Ahhoz, hogyan itt található:

    1. Azure portálon válassza a Data Lake Store-fiók ->
    2. A Data Lake Store panelen kattintson az "Access Control (IAM)"
    3. Kattintson a "Hozzáadás" a panelen az "Access Control (IAM)"
    4. Állítsa be a "Szerepkör", "Olvasó", és válassza ki a felhasználó vagy a szolgáltatás egyszerű hozzáférést példány használata

3. Ha nem szeretné, a felhasználó vagy szolgáltatás egyszerű "Olvasó" szerepkör megadását, alernative, hogy [explicit módon adja meg egy végrehajtás helyet](data-factory-data-movement-activities.md#global) a másolási activitywith a Data Lake Store helyét. Példa:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szeretné megadni a bemeneti adatok egy Data Lake Store a dataset, állítsa be a **típus** a DataSet tulajdonság **AzureDataLakeStore**. Állítsa be a **linkedServiceName** a Data Lake Store nevét a DataSet tulajdonság társított szolgáltatás. JSON-szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. A JSON-ban, a DataSet adatkészlet szakaszok például **struktúra**, **rendelkezésre állási**, és **házirend**, minden adatkészlet esetében hasonló (Azure SQL-adatbázis, az Azure blob és az Azure tábla a Példa). A **typeProperties** szakaszban nem egyezik az adatkészlet egyes típusú, és megjelenik többek között a hely és az adattár adatok formátuma. 

A **typeProperties** szakasz egy adatkészlet típusú **AzureDataLakeStore** tartalmazza a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **folderPath** |A tároló és a Data Lake Store mappa elérési útja. |Igen |
| **Fájlnév** |A fájl az Azure Data Lake Store nevét. A **Fájlnév** tulajdonság nem kötelező, és a kis-és nagybetűket. <br/><br/>Ha megad **Fájlnév**, a tevékenység (például a Másolás) működik-e az adott fájlt.<br/><br/>Ha **Fájlnév** nincs megadva, másolása tartalmazza az összes fájl **folderPath** bemeneti adatkészlet.<br/><br/>Ha **Fájlnév** nincs megadva egy kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl neve nem formátumú adatok. _GUID_.txt ". Például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nem |
| **partitionedBy** |A **partitionedBy** tulajdonság nem kötelező megadni. Használhatja a dinamikus elérési út és fájlnév idősorozat adatok megadása. Például **folderPath** adatok órára is lehet paraméterezett. Részletek és példák: [partitionedBy tulajdonság](#using-partitionedby-property). |Nem |
| **Formátumban** | A következő formátumban típusok támogatottak: **szöveges**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és  **ParquetFormat**. Állítsa be a **típus** tulajdonság alapján **formátum** számára a következő értékek egyike. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [az Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátumban ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszában a [Azure Data Factory által támogatott formátumú és tömörítést](data-factory-supported-file-and-compression-formats.md) cikk. <br><br> Ha a másolandó fájlok ",-van" közötti fájlalapú tárolók (bináris másolhatja azokat), hagyja ki a `format` mindkét bemeneti és kimeneti adatkészlet-definíciókban szakasz. |Nem |
| **tömörítés** | Adja meg a típus és az adatok tömörítése szintjét. Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők **Optimal** és **leggyorsabb**. További információkért lásd: [Azure Data Factory által támogatott formátumú és tömörítést](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

### <a name="the-partitionedby-property"></a>A partitionedBy tulajdonság
Megadhatja, hogy a dinamikus **folderPath** és **Fájlnév** tulajdonságok idősorozat adatokhoz a **partitionedBy** tulajdonság, adat-előállító funkciók és rendszerváltozók. További információkért lásd: a [Azure Data Factory - funkciók és rendszerváltozók](data-factory-functions-variables.md) cikk.


A következő példában `{Slice}` cseréli a Data Factory rendszer változó értékének `SliceStart` megadott formátumban (`yyyyMMddHH`). A név `SliceStart` hivatkozik a szelet kezdési idejét. A `folderPath` tulajdonság nem az egyes szeletek, mint az azonos `wikidatagateway/wikisampledataout/2014100103` vagy `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Az alábbi példa, év, hónap, nap, és az idő a `SliceStart` ki kell olvasni a által használt külön változók a `folderPath` és `fileName` tulajdonságok:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Az idősorozat adatkészleteket, az ütemezés és a szeletek további részletekért lásd: a [Azure Data Factory adathalmazok](data-factory-create-datasets.md) és [adat-előállító ütemezés és a végrehajtási](data-factory-scheduling-and-execution.md) cikkeket. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

A tulajdonságok érhetők el a **typeProperties** tevékenység szakasz tevékenységek minden típusának függenek. A másolási tevékenységhez változik a források és mosdók típusától függően.

**AzureDataLakeStoreSource** támogatja a következő tulajdonságot a **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **recursive** |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákat, illetve csak a megadott mappát. |TRUE hamis (alapértelmezés) |Nem |


**AzureDataLakeStoreSink** támogatja a következő tulajdonságok a **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **copyBehavior** |Meghatározza a másolási viselkedését. |<b>PreserveHierarchy</b>: őrzi meg a fájl hierarchia a célmappában. A következő forrásfájl forrásmappához relatív elérési a relatív elérési út a cél-fájlját és a célmappa megegyezik.<br/><br/><b>FlattenHierarchy</b>: összes fájl a forrásmappából az első szintjét a célmappában jönnek létre. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/><b>Mergefiles típusú</b>: egy fájl összes fájlt a forrásmappából egyesíti. Ha a fájl vagy a blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben a fájlnév pedig automatikusan elnevezi. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől rekurzív és copyBehavior kombinációk a másolási művelet.

| rekurzív | copyBehavior | Viselkedésről |
| --- | --- | --- |
| true |preserveHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 forrásaként azonos struktúrájú jön létre.<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A cél az alábbi szerkezettel mappa1 jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájl automatikusan létrehozott fájlnévvel egyesülnek |
| false |preserveHierarchy |A forrásmappa mappa1, az alábbi szerkezettel: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |flattenHierarchy |A forrásmappa mappa1, az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |
| false |mergeFiles |A forrásmappa mappa1, az alábbi szerkezettel:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>A célmappa mappa1 jön létre a következő struktúra<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Egy fájl automatikusan létrehozott fájlnévvel egyesített file1 + File2 tartalma. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 nem átveszik. |

## <a name="supported-file-and-compression-formats"></a>Támogatott formátumú és tömörítés
További információkért lásd: a [formátumú és tömörítést az Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikk.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Adatok másolása, és a Data Lake Store JSON példák
Az alábbi példák megadják minta JSON-definíciók. Ezek a minta a definíciók segítségével hozzon létre egy folyamatot a [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). A példák bemutatják, hogyan irányuló és onnan Data Lake Store és az Azure Blob storage-adatok másolása. Azonban az adatok átmásolhatók _közvetlenül_ bármelyik bármelyik a támogatott adatforrások fogadók esetében. További információkért lásd: "támogatott adattárolókhoz és formátumok" szakasz a a [adatok áthelyezése a másolási tevékenység segítségével](data-factory-data-movement-activities.md) cikk.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Példa: Adatok másolása az Azure Blob Storage az Azure Data Lake Store
Ez a szakasz a példakód mutatja:

* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* A társított szolgáltatás típusa [AzureDataLakeStore](#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureDataLakeStore](#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureDataLakeStoreSink](#copy-activity-properties).

A példák azt szemléltetik, hogyan idősorozat adatokat az Azure Blob Storage van Data Lake Store másolt óránként. 

**Azure Storage társított szolgáltatás**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Azure Data Lake Store társított szolgáltatás**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> További konfigurációs információkért lásd: a [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.
>

**Azure blobbemeneti adatkészlet**

A következő példában adatok van felvett egy új blobból minden órában (`"frequency": "Hour", "interval": 1`). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja, év, hónap és nap részéhez kezdési idejét. A fájl nevét a kezdő időpontja óra részét használja. A `"external": true` beállítás arról értesíti az, hogy a tábla az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store kimeneti adatkészlet**

A következő példa a Data Lake Store adatainak másolása. Új adatok másolásakor Data Lake Store óránként.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**A folyamat egy blob-forrás és a Data Lake Store fogadó másolási tevékenység**

A következő példában a feldolgozási sor tartalmazza a másolási tevékenység során használja a bemeneti és kimeneti adatkészletek van konfigurálva. A másolási tevékenység óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a `source` típusúra `BlobSource`, és a `sink` típusúra `AzureDataLakeStoreSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Példa: Adatok másolása az Azure Data Lake Store az Azure-blobba
Ez a szakasz a példakód mutatja:

* A társított szolgáltatás típusa [AzureDataLakeStore](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureDataLakeStore](#dataset-properties).
* Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt [AzureDataLakeStoreSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A kód másolja-idősoros adatok Data Lake Store-ból az Azure blob minden órában. 

**Azure Data Lake Store társított szolgáltatás**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> További konfigurációs információkért lásd: a [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz.
>

**Azure Storage társított szolgáltatás**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake bemeneti adatkészlet**

Ebben a példában, beállítása `"external"` való `true` tájékoztatja a Data Factory szolgáltatásnak, hogy a tábla külső data factoryval való, és nem hozzák adat-előállító tevékenység.

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Azure blobkimeneti adatkészlet**

A következő példában adatot ír egy új blob minden órában (`"frequency": "Hour", "interval": 1`). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap, és a kezdési idő órában része.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**A másolási tevékenység során az Azure Data Lake Store és egy blob fogadó egy folyamaton belül**

A következő példában a feldolgozási sor tartalmazza a másolási tevékenység során használja a bemeneti és kimeneti adatkészletek van konfigurálva. A másolási tevékenység óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a `source` típusúra `AzureDataLakeStoreSource`, és a `sink` típusúra `BlobSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

A másolási tevékenység definíciójának oszlop szerepel a fogadó dataset a forrás adatkészletből oszlopokat is leképezheti. További információkért lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A másolási tevékenység teljesítmény- és hogyan optimalizálható az azt befolyásoló tényezők kapcsolatos további tudnivalókért lásd: a [másolási tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md) cikk.
