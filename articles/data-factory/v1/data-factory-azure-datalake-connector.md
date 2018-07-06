---
title: Másolja az adatokat, és az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat, és a Data Lake Store az Azure Data Factory használatával
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
ms.openlocfilehash: 97bd2081df8c90f885996629862f25cbec8fd2c2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860231"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Adatok másolása és a Data Lake Storage Gen1 Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-datalake-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure Data Lake Storage Gen1 összekötő](../connector-azure-data-lake-store.md).

Ez a cikk azt ismerteti, hogyan Azure Data Factory másolási tevékenység használatával adatok importálására és az Azure Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store). Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikkre, adatok áthelyezése másolási tevékenységgel rendelkező áttekintése.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **az Azure Data Lake Store** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **az Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Másolási tevékenységgel rendelkező folyamat létrehozása előtt hozzon létre egy Data Lake Store-fiókot. További információkért lásd: [Azure Data Lake Store használatának első lépései](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
A Data Lake Store-összekötő ezeket a hitelesítési típusokat támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítés 

Azt javasoljuk, hogy használjon egyszerű szolgáltatásnév hitelesítése, különösen egy ütemezett példányt. Jogkivonat lejárati fordulhat elő a felhasználói hitelesítő adatok hitelesítéssel. További konfigurációs információkért lásd: a [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez és-tárolókról az Azure Data Lake Store más eszközök/API-k használatával.

Az adatok másolása folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. A folyamat létrehozása a másolás varázsló használatával, olvassa el [oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

-Folyamatok létrehozására is használhatja az alábbi eszközöket: **az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat. 
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha Ön adatokat másol egy Azure blob storage-ból egy Azure Data Lake Store, hoz létre az Azure storage-fiókot és az Azure Data Lake store összekapcsolása a data factory két társított szolgáltatást. Konkrétan az Azure Data Lake Store a társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban. 
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett megadja a blobtárolót és a bemeneti adatokat tartalmazó mappát egy adatkészletet hoz létre. És a mappa és fájl elérési útját adja meg a Data Lake adattár, amely tárolja az adatokat a blob storage-tárhelyéből egy másik adatkészletet hoz létre. Az Azure Data Lake Store adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük használhat BlobSource a forrás-és AzureDataLakeStoreSink fogadóként a másolási tevékenység. Hasonlóképpen, az Azure Data Lake Store másolása az Azure Blob Storage, AzureDataLakeStoreSource és a használata BlobSink a másolási tevékenység. Másolási tevékenység tulajdonságai az Azure Data Lake Store jellemző, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.  

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amelyek és- tárolókról az Azure Data Lake Store az adatok másolása JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-data-lake-store) című szakaszát.

A következő szakaszok a Data Lake Store adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárba hivatkozik, adat-előállító. Létrehoz egy társított szolgáltatást típusú **AzureDataLakeStore** a Data Lake Store-adatok összekapcsolása a data factoryhoz. A következő táblázat ismerteti a Data Lake Store társított szolgáltatás JSON elemeket. Egyszerű szolgáltatás és a felhasználói hitelesítő adatok hitelesítési közül választhat.

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **type** | A type tulajdonságot állítsa **AzureDataLakeStore**. | Igen |
| **dataLakeStoreUri** | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ezt az információt a következő formátumok egyikét veheti fel: `https://[accountname].azuredatalakestore.net/webhdfs/v1` vagy `adl://[accountname].azuredatalakestore.net/`. | Igen |
| **subscriptionId** | Azure-előfizetés azonosítója, amelyhez a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |
| **resourceGroupName** | Azure erőforráscsoport neve, amelyhez a Data Lake Store-fiókhoz tartozik. | A fogadó megadása kötelező |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Egyszerű szolgáltatásnév hitelesítése alkalmazás entitás regisztrálása az Azure Active Directory (Azure AD), és hozzáférést, a Data Lake Store. Részletes lépéseiért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, mert a társított szolgáltatás definiálásához használja:
* Alkalmazásazonosító
* Alkalmazáskulcs 
* Bérlőazonosító

> [!IMPORTANT]
> Győződjön meg arról, hogy engedélyeket a szolgáltatás egyszerű megfelelő az Azure Data Lake Store:
>- **A Data Lake Store használata forrásként**, adjon meg legalább **Olvasás + végrehajtás** adat-hozzáférési engedély listázása és a egy mappát, másolja vagy **olvasási** engedéllyel egy fájl másolása. Nem követelmény, a fiók fájlszintű hozzáférés-vezérlés.
>- **Data Lake Store használatára pedig a fogadó**, adjon meg legalább **írás + végrehajtás** adat-hozzáférési engedély szükséges gyermekelemek létrehozásához a mappában. Ha Azure integrációs modul használatával lehetővé teheti a másolás és (a forrás- és fogadó olyan felhőbeli), annak érdekében, hogy a Data Lake Store régió észlelése, a Data Factory szolgáltatás segítségével, adjon meg legalább **olvasó** szerepkört a fiókhoz hozzáférés-vezérlés (IAM). Ha el szeretné kerülni ezt IAM szerepkör [adja meg az executionlocation használata a következő](data-factory-data-movement-activities.md#global) helyét, a Data Lake Store a másolási tevékenység.
>- Ha Ön **folyamatok létrehozását a másolás varázsló használatával**, adjon meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkör. Emellett adja meg legalább **Olvasás + végrehajtás** a Data Lake Store gyökér ("/") és annak gyermekeihez engedéllyel. Ellenkező esetben előfordulhat, hogy az üzenetet látja "a megadott hitelesítő adatok érvénytelenek."

Egyszerű szolgáltatásnév hitelesítése használja a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfél-azonosítót. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazáskulcsot. | Igen |
| **bérlő** | Adja meg a bérlő információkat (tartomány neve vagy a bérlő azonosítója) alatt az alkalmazás található. Kérheti a viszi az egérmutatót az Azure portal jobb felső sarkában. | Igen |

**Példa: Egyszerű szolgáltatásnév hitelesítése**
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
Felhasználói hitelesítő adatok hitelesítési segítségével azt is megteheti, másolja a következő tulajdonságok megadásával vagy a Data Lake Store:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **Engedélyezési** | Kattintson a **engedélyezés** gombra a Data Factory szerkesztőjében, és adja meg a hitelesítő adat, amelyet az automatikusan létrehozott engedélyezési URL-címet rendel hozzá ezt a tulajdonságot. | Igen |
| **sessionId** | OAuth munkamenet-azonosító az OAuth hitelesítési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használhatók fel. Ez a beállítás automatikusan jön létre a Data Factory Editor használata esetén. | Igen |

> [!IMPORTANT]
> Győződjön meg arról, hogy a felhasználó megfelelő engedélye az Azure Data Lake Store biztosít:
>- **A Data Lake Store használata forrásként**, adjon meg legalább **Olvasás + végrehajtás** adat-hozzáférési engedély listázása és a egy mappát, másolja vagy **olvasási** engedéllyel egy fájl másolása. Nem követelmény, a fiók fájlszintű hozzáférés-vezérlés.
>- **Data Lake Store használatára pedig a fogadó**, adjon meg legalább **írás + végrehajtás** adat-hozzáférési engedély szükséges gyermekelemek létrehozásához a mappában. Ha Azure integrációs modul használatával lehetővé teheti a másolás és (a forrás- és fogadó olyan felhőbeli), annak érdekében, hogy a Data Lake Store régió észlelése, a Data Factory szolgáltatás segítségével, adjon meg legalább **olvasó** szerepkört a fiókhoz hozzáférés-vezérlés (IAM). Ha el szeretné kerülni ezt IAM szerepkör [adja meg az executionlocation használata a következő](data-factory-data-movement-activities.md#global) helyét, a Data Lake Store a másolási tevékenység.
>- Ha Ön **folyamatok létrehozását a másolás varázsló használatával**, adjon meg legalább **olvasó** fiók hozzáférés-vezérlés (IAM) szerepkör. Emellett adja meg legalább **Olvasás + végrehajtás** a Data Lake Store gyökér ("/") és annak gyermekeihez engedéllyel. Ellenkező esetben előfordulhat, hogy az üzenetet látja "a megadott hitelesítő adatok érvénytelenek."

**Például: Felhasználói hitelesítő adatok hitelesítése**
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
Az engedélyezési kódot, amely a használatával létrehozhat a **engedélyezés** gombra egy bizonyos idő elteltével lejár. A következő üzenet azt jelenti, hogy a hitelesítési jogkivonat lejárt:

Hitelesítőadat-műveleti hiba: invalid_grant - AADSTS70002: Hiba történt a hitelesítő adatok ellenőrzése. AADSTS70008: A megadott hozzáférési engedély lejárt vagy visszavont. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelációs azonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 időbélyeg: 2015. 12 – 15 21-09-31Z.

Az alábbi táblázat a különböző típusú felhasználói fiókokat lejárati idejét jeleníti meg:

| Felhasználó típusa | Tétlenség után lejár |
|:--- |:--- |
| Felhasználói fiókok *nem* Azure Active Directory által felügyelt (például @hotmail.com vagy @live.com) |12 óra |
| Felhasználói fiókok Azure Active Directory által felügyelt |az utolsó szelet futtatása után két hétben <br/><br/>90 nap, ha egy olyan OAuth-alapú társított szolgáltatás alapján végezzen szeletelést, 14 naponta legalább egyszer fut |

Ha módosítja a jelszavát a jogkivonat lejárati idő előtt, a jogkivonat azonnal lejár. Ebben a szakaszban korábban említettük üzenet jelenik meg.

Akkor is engedélyezze újra a fiók használatával a **engedélyezés** gombot, ha a jogkivonat lejár, telepítse újra a társított szolgáltatást. Értékeit is létrehozhat a **sessionId** és **engedélyezési** tulajdonságok programozott módon az alábbi kód használatával:


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
A Data Factory-osztályokat használja a kód kapcsolatos részletekért lásd: a [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), és [ AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témaköröket. Vegyen fel egy hivatkozást verzió `2.9.10826.1824` , `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` számára a `WindowsFormsWebAuthenticationDialog` osztályt használja a kód.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

**Jelenség:** Példatípust az adatok **be** Azure Data Lake Store, ha a másolási tevékenység sikertelen lesz a következő hiba miatt:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Alapvető ok:** 2 oka lehet:

1. A `resourceGroupName` és/vagy `subscriptionId` megadott Azure Data Lake Store társított szolgáltatás helytelen;
2. A felhasználó vagy az egyszerű szolgáltatás nem rendelkezik a szükséges engedéllyel.

**Megoldás:**

1. Győződjön meg arról, hogy a `subscriptionId` és `resourceGroupName` adja meg, ha a hivatkozott szolgáltatásban található `typeProperties` valóban azok, amelyekre a data lake-fiók tartozik.

2. Ellenőrizze, hogy legalább számára biztosítson "**olvasó**" szerepkör a felhasználó vagy szolgáltatásnév az a data lake-fiók. Itt látható, hogy hogyan:

    1. Válassza az Azure Portal -> a Data Lake Store-fiók
    2. A Data Lake Store paneljén kattintson a "hozzáférés-vezérlés (IAM)"
    3. Kattintson a "Hozzáadás", "hozzáférés-vezérlés (IAM)" panelen
    4. Állítsa be a "Szerepkör", "Olvasó", és válassza ki a felhasználó vagy az egyszerű szolgáltatás hozzáférést példányáért használja

3. Ha nem szeretné adni az "Olvasó" szerepkör a felhasználó vagy szolgáltatásnév, alernative, hogy [kifejezetten megad egy végrehajtási helyéhez](data-factory-data-movement-activities.md#global) másolási activitywith helyét, a Data Lake Store a. Példa:

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
Adjon meg egy Data Lake Store bemeneti adatokat képviselő adatkészlet, akkor állítsa a **típus** tulajdonság, az adatkészlet **AzureDataLakeStore**. Állítsa be a **linkedServiceName** tulajdonságot az adatkészlet nevét a Data Lake Store-beli társított szolgáltatást. JSON-szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Egy adatkészlet JSON szakaszait például **struktúra**, **rendelkezésre állási**, és **házirend**, hasonlóak az összes adatkészlet esetében (Azure SQL database, az Azure blob és az Azure table számára a példában). A **typeProperties** szakasz eltérő az egyes adatkészletet, és többek között a hely és az adattárban lévő adatok formátumát. 

A **typeProperties** szakasz egy adatkészlet típusú **AzureDataLakeStore** a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| **folderPath** |A tároló és a Data Lake Store-mappában elérési útja. |Igen |
| **Fájlnév** |Az Azure Data Lake Store a fájl nevét. A **fileName** tulajdonság nem kötelező, és a kis-és nagybetűket. <br/><br/>Ha megad **fileName**, a (beleértve a Másolás) tevékenységet az adott fájlon működik.<br/><br/>Amikor **fileName** nincs megadva, másolási tartalmazza az összes fájl **folderPath** a bemeneti adatkészletben.<br/><br/>Amikor **fileName** nincs megadva a kimeneti adatkészlet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a létrehozott fájl neve a következő formátumban kell adatokat. _GUID_.txt'. Például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nem |
| **partitionedBy** |A **partitionedBy** tulajdonság nem kötelező. Használhatja a dinamikus elérési út és fájlnév idősorozat-adatok megadása. Ha például **folderPath** az adatok óránkénti rendelkeznek. Részletek és példák: [partitionedBy tulajdonság](#using-partitionedby-property). |Nem |
| **Formátum** | A következő formátumtípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, és  **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt **formátum** az alábbi értékek egyikére. További információkért lásd: a [szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC formátum](data-factory-supported-file-and-compression-formats.md#orc-format), és [Parquet formátum ](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszát a [Azure Data Factory által támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md) cikk. <br><br> Ha azt szeretné, a fájlok másolása a "as-van" közötti fájlalapú tárolók (bináris másolat), hagyja ki a `format` mindkét bemeneti és kimeneti adatkészlet-definíciók szakaszában. |Nem |
| **a tömörítés** | Adja meg a típus és az adatok tömörítési szintje. Támogatott típusok a következők **GZip**, **Deflate**, **BZip2**, és **ZipDeflate**. Támogatott szintek a következők **Optimal** és **leggyorsabb**. További információkért lásd: [Azure Data Factory által támogatott fájl- és tömörítési formátumok](data-factory-supported-file-and-compression-formats.md#compression-support). |Nem |

### <a name="the-partitionedby-property"></a>A partitionedBy tulajdonság
Megadhatja, hogy a dinamikus **folderPath** és **fileName** idősorozat-adatok a tulajdonságok a **partitionedBy** tulajdonság, a Data Factory-függvények és rendszerváltozók. További információkért lásd: a [Azure Data Factory - függvények és rendszerváltozók](data-factory-functions-variables.md) cikk.


A következő példában `{Slice}` a Data Factory rendszer változó értékének helyére `SliceStart` megadott formátumban (`yyyyMMddHH`). A név `SliceStart` hivatkozik a szelet kezdési idejét. A `folderPath` tulajdonság nem egyezik az egyes a szeletekhez megfelelően: `wikidatagateway/wikisampledataout/2014100103` vagy `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Az alábbi példa, az év, hónap, nap és idején a `SliceStart` által használt külön változókba ki kell olvasni az `folderPath` és `fileName` tulajdonságai:
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
Az idősorozat-adatkészletek, ütemezési és szeletek további részletekért lásd: a [adatkészleteket az Azure Data Factoryban](data-factory-create-datasets.md) és [adat-előállító ütemezési és végrehajtási](data-factory-scheduling-and-execution.md) cikkeket. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

A rendelkezésre álló tulajdonságok a **typeProperties** a tevékenységek szakaszban tevékenységek minden típusának számától függ. A másolási tevékenység azok változhat a forrásként és fogadóként típusú is.

**AzureDataLakeStoreSource** támogatja a következő tulajdonságot a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **recursive** |Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. |(Alapértelmezett érték), true a False |Nem |


**AzureDataLakeStoreSink** támogatja a következő tulajdonságok a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| **a copyBehavior** |Meghatározza a másolási viselkedés. |<b>PreserveHierarchy</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><br/><b>FlattenHierarchy</b>: az első szintjét a célmappában jönnek létre a forrásmappából összes fájlt. A cél fájlok jönnek létre automatikusan létrehozott névvel.<br/><br/><b>MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. A fájl- vagy blob neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben a fájlnév formátuma automatikusan létrehozott. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>a rekurzív és copyBehavior példák
Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| a rekurzív | a copyBehavior | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappában mappa1 szerkezete ugyanaz, mint a forrás jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel egyesítésekor |
| false |preserveHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |flattenHierarchy |Forrás mappa mappa1 az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |mergeFiles |Forrás mappa mappa1 az alábbi struktúra használatával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + 2. fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel összefésülése megtörténjen. automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
További információkért lásd: a [fájl- és tömörítési formátumok az Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md) cikk.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Az adatok másolása, és a Data Lake Store JSON-példák
Az alábbi példák megadják példa JSON-definíciói. Ezeknek a definícióknak minta segítségével hozzon létre egy folyamatot a [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). A példák bemutatják, hogyan másolhat adatokat a Data Lake Store és az Azure Blob storage szolgáltatásba vagy onnan. Azonban az adatok átmásolhatók _közvetlenül_ bármely, bármelyik támogatott forrás fogadók. További információkért lásd: a "támogatott adattárak és formátumok" szakasz a a [adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md) cikk.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Példa: Adatok másolása az Azure Blob Storage-ból az Azure Data Lake Store
Ebben a szakaszban található példakód mutatja:

* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* A társított szolgáltatás típusa [AzureDataLakeStore](#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureDataLakeStore](#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureDataLakeStoreSink](#copy-activity-properties).

A példák bemutatják, hogyan idősorozat-adatok Azure Blob Storage-ból a Data Lake Store minden órában másolva. 

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

**Az Azure Data Lake Store társított szolgáltatás**

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
> További konfigurációs információkért lásd: a [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
>

**Azure blobbemeneti adatkészlet**

A következő példában adatok felülettől új blob minden órában (`"frequency": "Hour", "interval": 1`). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útját használja, az év, hónap és nap részéhez a kezdési időpontot. A fájl nevét a kezdő időpontja óra részét használja. A `"external": true` beállítás, hogy a tábla a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

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

**Az Azure Data Lake Store kimeneti adatkészlet**

Az alábbi példa a Data Lake Store adatokat másolja. Az új adatok Data Lake Store minden órában másolja.

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


**Egy blob forrás és a egy Data Lake Store fogadó a folyamat másolási tevékenysége**

A következő példában a folyamat egy másolási tevékenység, amely konfigurálva van használni a bemeneti és kimeneti adatkészleteket tartalmazza. A másolási tevékenység való óránkénti futásra van ütemezve. A folyamat JSON-definíciót a `source` típusa `BlobSource`, és a `sink` típusa `AzureDataLakeStoreSink`.

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
Ebben a szakaszban található példakód mutatja:

* A társított szolgáltatás típusa [AzureDataLakeStore](#linked-service-properties).
* A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureDataLakeStore](#dataset-properties).
* Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [AzureDataLakeStoreSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A kód idősorozat-adatokat másol Data Lake Store az Azure-blobba óránként. 

**Az Azure Data Lake Store társított szolgáltatás**

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
> További konfigurációs információkért lásd: a [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
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
**Az Azure Data Lake bemeneti adatkészlet**

Ebben a példában beállítás `"external"` való `true` a Data Factory szolgáltatás tájékoztatja, hogy a tábla a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

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

A következő példában adatokat írt egy új blob minden órában (`"frequency": "Hour", "interval": 1`). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, az év, hónap, nap és a kezdő időpontja óra részét.

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

**Az Azure Data Lake Store forrás és a egy blob fogadó rendelkező folyamatot egy másolási tevékenységgel**

A következő példában a folyamat egy másolási tevékenység, amely konfigurálva van használni a bemeneti és kimeneti adatkészleteket tartalmazza. A másolási tevékenység való óránkénti futásra van ütemezve. A folyamat JSON-definíciót a `source` típusa `AzureDataLakeStoreSource`, és a `sink` típusa `BlobSink`.

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

A másolási tevékenységhez tartozó definíció a fogadó-adatkészlet oszlopaihoz a forrásadatkészlet oszlopát is leképezheti. További információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A másolási tevékenységek teljesítményéhez és hogyan optimalizálható az azt befolyásoló tényezők kapcsolatos további információkért tekintse meg a [másolási tevékenységek teljesítményéről és finomhangolásáról szóló útmutató](data-factory-copy-activity-performance.md) cikk.
