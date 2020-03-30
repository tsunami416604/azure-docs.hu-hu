---
title: Adatok másolása az Azure Data Lake Storage Gen1 szolgáltatásba és onnan
description: Ismerje meg, hogyan másolhat adatokat a Data Lake Store-ba és onnan az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281599"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Adatok másolása a Data Lake Storage Gen1 szolgáltatásba a Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-datalake-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure Data Lake Storage Gen1 összekötő t a V2-ben.](../connector-azure-data-lake-store.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory-ban az Azure Data Lake Storage Gen1 (korábbi nevén Azure Data Lake Store) adatainak áthelyezéséhez. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) ről szóló cikkre épül, amely áttekintést nyújt az adatok mozgásáról a Másolási tevékenységgel.

## <a name="supported-scenarios"></a>Támogatott esetek
Az Azure **Data Lake Store-ból** adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból másolhat adatokat **az Azure Data Lake Store-ba:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Hozzon létre egy Data Lake Store-fiókot, mielőtt létrehozna egy folyamatot a Másolási tevékenységgel. További információ: [Az Azure Data Lake Store – Első lépések.](../../data-lake-store/data-lake-store-get-started-portal.md)

## <a name="supported-authentication-types"></a>Támogatott hitelesítési típusok
A Data Lake Store-összekötő a következő hitelesítési típusokat támogatja:
* Egyszerű szolgáltatásnév hitelesítése
* Felhasználói hitelesítő adatok (OAuth) hitelesítése

Azt javasoljuk, hogy az egyszerű szolgáltatás hitelesítése, különösen az ütemezett adatmásolat. A jogkivonat elévülési viselkedése a felhasználói hitelesítő adatok hitelesítésével fordulhat elő. A konfigurációval kapcsolatos részleteket a [Csatolt szolgáltatás tulajdonságai](#linked-service-properties) című szakaszban találja.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat az Azure Data Lake Store-ba/az Azure Data Lake Store-ból különböző eszközök/API-k használatával helyezi át.

Az adatok másolásához használt folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. A folyamat másolása varázslóval történő létrehozásáról az [Oktatóanyag: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md)című témakörben látható.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Azure-blobstorage-ból egy Azure Data Lake Store-ba, két összekapcsolt szolgáltatást hoz létre az Azure storage-fiók és az Azure Data Lake-áruház és az adat-előállító összekapcsolására. Az Azure Data Lake Store-ra jellemző kapcsolt szolgáltatástulajdonságokról lásd: [csatolt szolgáltatástulajdonságok](#linked-service-properties) szakasz.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában hozzon létre egy adatkészletet a blob tároló és a bemeneti adatokat tartalmazó mappa megadásához. És hozzon létre egy másik adatkészletet, amely megadja a mappát és a fájl elérési útját a Data Lake tárolóban, amely a blob storage-ból másolt adatokat tárolja. Az Azure Data Lake Store-ra jellemző adatkészlet-tulajdonságokat lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában a BlobSource-ot használja forrásként, és az AzureDataLakeStoreSink-t a másolási tevékenység fogadójaként. Hasonlóképpen, ha az Azure Data Lake Store-ból az Azure Blob Storage-ba másolja, használja az AzureDataLakeStoreSource és a BlobSink a másolási tevékenységben. Az Azure Data Lake Store-ra jellemző másolási tevékenységtulajdonságokról a [Másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban található. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az Azure Data Lake Store-ba történő adatmásoláshoz használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples-for-copying-data-to-and-from-data-lake-store) című részében.

A következő szakaszok a Data Lake Store-ra jellemző Data Factory-entitások meghatározására használt JSON-tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
A csatolt szolgáltatás adattaránttárolót kapcsol össze egy adat-előállítóval. Hozzon létre egy **AzureDataLakeStore** típusú összekapcsolt szolgáltatást, amely összekapcsolja a Data Lake Store-adatokat az adat-előállítóval. Az alábbi táblázat a Data Lake Store-hoz kapcsolódó szolgáltatásokRa jellemző JSON-elemeket ismerteti. Az egyszerű szolgáltatás és a felhasználói hitelesítő adatok hitelesítése közül választhat.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **Típus** | A típustulajdonságot **Az AzureDataLakeStore**tulajdonságra kell állítani. | Igen |
| **dataLakeStoreUri** | Az Azure Data Lake Store-fiókkal kapcsolatos információk. Ez az információ az alábbi `https://[accountname].azuredatalakestore.net/webhdfs/v1` formátumok `adl://[accountname].azuredatalakestore.net/`egyikét veszi fel: vagy . | Igen |
| **előfizetésazonosító** | Az Azure-előfizetés azonosítója, amelyhez a Data Lake Áruház-fiók tartozik. | A mosogatóhoz szükséges |
| **resourceGroupName** | Az Azure-erőforráscsoport neve, amelyhez a Data Lake Store-fiók tartozik. | A mosogatóhoz szükséges |

### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatáshitelesítés (ajánlott)
A szolgáltatás egyszerű hitelesítés, regisztráljon egy alkalmazás entitás az Azure Active Directoryban (Azure AD), és adja meg a hozzáférést a Data Lake Store. A részletes lépéseket a [Szolgáltatás-szolgáltatás hitelesítése című témakörben található.](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Jegyezze fel a következő értékeket, amelyek segítségével definiálja a csatolt szolgáltatást:
* Alkalmazásazonosító
* Alkalmazáskulcs
* Bérlőazonosító

> [!IMPORTANT]
> Győződjön meg arról, hogy megadja a szolgáltatás egyszerű megfelelő engedélyt az Azure Data Lake Store-ban:
>- **Ha a Data Lake Store-t forrásként szeretné használni,** legalább **Olvasás + Adathozzáférés végrehajtása** engedélyt adjon egy mappa tartalmának listázására és másolására, vagy **olvasási** engedélyt egyetlen fájl másolására. Nincs követelmény a fiókszintű hozzáférés-vezérléshez.
>- **Ha a Data Lake Store-t szeretné fogadóként használni,** adjon meg legalább **Írás + Adat-hozzáférési** engedély végrehajtása gyermekelemek létrehozásához a mappában. Ha pedig az Azure IR segítségével képessé teszi a másolást (mind a forrás, mind a fogadó a felhőben van), annak érdekében, hogy a Data Factory észlelhesse a Data Lake Store régióját, legalább **Reader** szerepkört adjon a fiókhozzáférés-vezérlésben (IAM). Ha el szeretné kerülni ezt az IAM-szerepkört, adja meg a [executionLocation](data-factory-data-movement-activities.md#global) értéket a Data Lake Store másolási tevékenységben való helyével.
>- Ha a **Másolás varázslóval hoz létre folyamatokat,** legalább **Reader** szerepkört adjon a fiókhozzáférés-vezérlésben (IAM). Továbbá adjon meg legalább **Olvasás + Végrehajtási** engedélyt a Data Lake Store gyökér ("/") és gyermekei számára. Ellenkező esetben a következő üzenet jelenhet meg: "A megadott hitelesítő adatok érvénytelenek."

A szolgáltatásegyszerű hitelesítés használata a következő tulajdonságok megadásával:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **servicePrincipalId** | Adja meg az alkalmazás ügyfélazonosítóját. | Igen |
| **servicePrincipalKey** | Adja meg az alkalmazás kulcsát. | Igen |
| **Bérlő** | Adja meg a bérlői adatokat (tartománynév vagy bérlőazonosító), amely alatt az alkalmazás található. Az egér relamás, az Azure Portal jobb felső sarkában lévő egérrel érhető el. | Igen |

**Példa: Egyszerű szolgáltatáshitelesítés**
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
Másik lehetőségként a következő tulajdonságok megadásával a felhasználói hitelesítő adatok hitelesítésével másolhat a Data Lake Áruházból vagy a Data Lake Store-ba:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **Engedélyezési** | Kattintson a Data Factory Editor **Engedélyezés** gombjára, és adja meg az automatikusan létrehozott engedélyezési URL-címet hozzárendelt hitelesítő adatait ehhez a tulajdonsághoz. | Igen |
| **Munkamenet** | OAuth munkamenet-azonosító az OAuth engedélyezési munkamenetből. Minden munkamenet-azonosító egyedi, és csak egyszer használható. Ez a beállítás automatikusan létrejön a Data Factory Editor használatakor. | Igen |

> [!IMPORTANT]
> Győződjön meg arról, hogy megfelelő engedélyt ad a felhasználónak az Azure Data Lake Store-ban:
>- **Ha a Data Lake Store-t forrásként szeretné használni,** legalább **Olvasás + Adathozzáférés végrehajtása** engedélyt adjon egy mappa tartalmának listázására és másolására, vagy **olvasási** engedélyt egyetlen fájl másolására. Nincs követelmény a fiókszintű hozzáférés-vezérléshez.
>- **Ha a Data Lake Store-t szeretné fogadóként használni,** adjon meg legalább **Írás + Adat-hozzáférési** engedély végrehajtása gyermekelemek létrehozásához a mappában. Ha pedig az Azure IR segítségével képessé teszi a másolást (mind a forrás, mind a fogadó a felhőben van), annak érdekében, hogy a Data Factory észlelhesse a Data Lake Store régióját, legalább **Reader** szerepkört adjon a fiókhozzáférés-vezérlésben (IAM). Ha el szeretné kerülni ezt az IAM-szerepkört, adja meg a [executionLocation](data-factory-data-movement-activities.md#global) értéket a Data Lake Store másolási tevékenységben való helyével.
>- Ha a **Másolás varázslóval hoz létre folyamatokat,** legalább **Reader** szerepkört adjon a fiókhozzáférés-vezérlésben (IAM). Továbbá adjon meg legalább **Olvasás + Végrehajtási** engedélyt a Data Lake Store gyökér ("/") és gyermekei számára. Ellenkező esetben a következő üzenet jelenhet meg: "A megadott hitelesítő adatok érvénytelenek."

**Példa: Felhasználói hitelesítő adatok hitelesítése**
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

#### <a name="token-expiration"></a>Token lejárata
Az **Engedélyezés** gombbal létrehozott engedélyezési kód egy bizonyos idő elteltével lejár. A következő üzenet azt jelenti, hogy a hitelesítési jogkivonat lejárt:

Hitelesítő adatok működési hibája: invalid_grant - AADSTS70002: Hiba a hitelesítő adatok érvényesítése közben. AADSTS70008: A megadott hozzáférési támogatás lejárt vagy visszavonásra került. Nyomazonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelációs azonosító: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Időbélyeg: 2015-12-15 21-09-31Z.

Az alábbi táblázat a különböző típusú felhasználói fiókok lejárati idejét mutatja be:

| Felhasználó típusa | Lejárat után |
|:--- |:--- |
| Az Azure Active Directory által *nem* @hotmail.com kezelt @live.comfelhasználói fiókok (például vagy ) |12 óra |
| Az Azure Active Directory által kezelt felhasználói fiókok |14 nappal az utolsó szelet futtatása után <br/><br/>90 nap, ha egy OAuth-alapú csatolt szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut |

Ha módosítja a jelszót a jogkivonat lejárati ideje előtt, a jogkivonat azonnal lejár. Az ebben a szakaszban korábban említett üzenet jelenik meg.

Újra engedélyezheti a fiókot az **Engedélyezés** gombbal, amikor a token lejár a csatolt szolgáltatás újratelepítéséhez. A **munkamenetazonosító** és az **engedélyezési** tulajdonságokhoz programozott módon is létrehozhat értékeket a következő kód használatával:


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
A kódban használt Data Factory-osztályokról az [AzureDataLakeStoreLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), az [AzureDataServiceLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)és a [AuthorizationSessionGetResponse osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) témakörei című témakörökben olvashat részletesen. Hivatkozás t a `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` kódban `WindowsFormsWebAuthenticationDialog` használt osztály verziójára.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

**Tünet:** Adatok másolásakor az Azure Data Lake **Store-ba,** ha a másolási tevékenység sikertelen a következő hibával:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Kiváltó ok:** Két oka lehet:

1. Az `resourceGroupName` Azure `subscriptionId` Data Lake Store-hoz csatolt szolgáltatásban megadott és/vagy megadott szolgáltatás helytelen;
2. A felhasználó vagy a szolgáltatásnév nem rendelkezik a szükséges engedéllyel.

**Felbontás:**

1. Győződjön `subscriptionId` meg `resourceGroupName` arról, hogy `typeProperties` a és adja meg a csatolt szolgáltatás valóban azok, amelyek a data lake fiók tartozik.

2. Győződjön meg arról, hogy legalább **Reader** szerepkört ad a felhasználó nak vagy a szolgáltatásnévnek a Data Lake-fiókban. Itt van, hogyan lehet ez:

    1. Nyissa meg az Azure Portalon - > a Data Lake Store-fiók
    2. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre a Data Lake Store paneljén
    3. Kattintson **a Szerepkör-hozzárendelés hozzáadása gombra.**
    4. **Szerepkör beállítása** **olvasóként**, és válassza ki a felhasználót vagy a másoláshoz használt egyszerű szolgáltatást a hozzáférés engedélyezéséhez

3. Ha nem szeretné megadni az **Olvasó** szerepkört a felhasználónak vagy a szolgáltatásnévnek, akkor a Data Lake Store helyével [kifejezetten meg kell adnia egy végrehajtási helyet](data-factory-data-movement-activities.md#global) a másolási tevékenységben. Példa:

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
Ha egy Data Lake Store-ban bemeneti adatokat jelölő adatkészletet szeretne megadni, állítsa be az adatkészlet **típustulajdonságát** **az AzureDataLakeStore beállításra.** Állítsa be az adatkészlet **linkedServiceName** tulajdonságát a Data Lake Store csatolt szolgáltatás nevére. Az adatkészletek definiálására rendelkezésre álló JSON-szakaszok és tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. Egy adatkészlet egy json-beli szakaszai, például **a struktúra,** **a rendelkezésre állás**és a **szabályzat,** hasonlóak az összes adatkészlettípushoz (például az Azure SQL-adatbázis, az Azure blob és az Azure-tábla). A **typeProperties** szakasz az adatkészlet egyes típusaiesetében eltérő, és olyan információkat tartalmaz, mint például az adatok helye és formátuma az adattárban.

**Az AzureDataLakeStore** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| **folderPath** |A Data Lake Store tárolójának és mappájának elérési útja. |Igen |
| **fileName** |A fájl neve az Azure Data Lake Store-ban. A **fileName** tulajdonság nem kötelező, és a kis- és nagybetűket nem. <br/><br/>Ha megadegy **fájlnév**, a tevékenység (beleértve a Másolás) működik az adott fájlt.<br/><br/>Ha **a fájlnév** nincs megadva, a Másolás a bemeneti adatkészletben a **folderPath** összes fájlját tartalmazza.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a `Data._Guid_.txt`tevékenységgyűjtőben, a létrehozott fájl neve a formátumban van. Például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nem |
| **particionáltby** |A **partitionedBy** tulajdonság nem kötelező. Ezzel dinamikus elérési utat és fájlnevet adhat meg az idősorozat-adatokhoz. Például **a folderPath** paraméterezhető az adatok minden órájára. A részleteket és példákat lásd: A partitionedBy tulajdonság. |Nem |
| **Formátum** | A következő formátumtípusok támogatottak: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**és **ParquetFormat**. Állítsa a **formátum** alatti **típustulajdonságot** ezen értékek egyikére. További információ: [A Szöveg formátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum,](data-factory-supported-file-and-compression-formats.md#avro-format) [ORC formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és [Parketta formátum](data-factory-supported-file-and-compression-formats.md#parquet-format) szakaszok az Azure Data Factory által támogatott [fájl- és tömörítési formátumokban.](data-factory-supported-file-and-compression-formats.md) <br><br> Ha a fájlalapú tárolók között "as-is" fájlokat szeretne `format` másolni (bináris másolat), hagyja ki a szakaszt a bemeneti és kimeneti adatkészlet-definíciókban is. |Nem |
| **Tömörítés** | Adja meg az adatok tömörítésének típusát és szintjét. A támogatott típusok: **GZip**, **Deflate,** **BZip2**és **ZipDeflate**. A támogatott szintek **optimálisak** és **leggyorsabbak.** További információt az [Azure Data Factory által támogatott fájl- és tömörítési formátumok című témakörben talál.](data-factory-supported-file-and-compression-formats.md#compression-support) |Nem |

### <a name="the-partitionedby-property"></a>A partitionedBy tulajdonság
Dinamikus **folderPath** és **fileName** tulajdonságokat adhat meg az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, a Data Factory függvényekkel és a rendszerváltozókkal. További részletek: az [Azure Data Factory - függvények és a rendszerváltozók](data-factory-functions-variables.md) cikket.


A következő példában `{Slice}` a rendszer a Data Factory `SliceStart` rendszerváltozó értéke helyébe`yyyyMMddHH`a megadott formátumban ( ). A `SliceStart` név a szelet kezdési időpontjára utal. A `folderPath` tulajdonság minden szeletesetében más, `wikidatagateway/wikisampledataout/2014100104`például a vagy a `wikidatagateway/wikisampledataout/2014100103` .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

A következő példában az év, a hónap, a nap és az `SliceStart` idő `folderPath` külön `fileName` változókba lesz kibontva, amelyeket a és a tulajdonságok használnak:
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
Az idősorozat-adatkészletekkel, ütemezésekkel és szeletekkel kapcsolatos további részletekért tekintse meg az Azure Data Factory és a [Data Factory ütemezési és végrehajtási](data-factory-scheduling-and-execution.md) cikkeiben található [adatkészleteket.](data-factory-create-datasets.md)


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. A másolási tevékenység esetében a források és a fogadók típusától függően változnak.

**Az AzureDataLakeStoreSource** a következő tulajdonságot támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **Rekurzív** |Azt jelzi, hogy az adatok olvasása rekurzív anameddig az almappákból vagy csak a megadott mappából történik. |Igaz (alapértelmezett érték), Hamis |Nem |

**Az AzureDataLakeStoreSink** a következő tulajdonságokat támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **copyBehavior (Másként)** |Megadja a másolási viselkedést. |<b>Megőrzés:</b>Megőrzi a fájlhierarchiát a célmappában. A forrásfájl forrásmappához viszonyított elérési útja megegyezik a célfájl célmappához való relatív elérési útjával.<br/><br/><b>Összeolvasztás:</b>A forrásmappából származó összes fájl a célmappa első szintjén jön létre. A célfájlok automatikusan létrehozott nevekkel jönnek létre.<br/><br/><b>MergeFiles</b>: A forrásmappából származó összes fájl egyetlen fájlba egyesítése. Ha a fájl vagy a blob neve meg van adva, az egyesített fájlnév a megadott név. Ellenkező esetben a fájlnév automatikusan létrejön. |Nem |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz a Copy művelet viselkedését ismerteti a rekurzív és copyBehavior értékek különböző kombinációihoz.

| Rekurzív | copyBehavior (Másként) | Eredményül kapott viselkedés |
| --- | --- | --- |
| igaz |megőrzési hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 ugyanazzal a struktúrával jön létre, mint a forrás<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5. |
| igaz |összeolvasztási hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl 3 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl4 automatikusan létrehozott neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File5 fájlhoz |
| igaz |mergeFiles |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a célmappa1 a következő struktúrával jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 tartalmát egyesítik egy fájlautomatikusan generált fájl neve |
| hamis |megőrzési hierarchia |A Mappa1 forrásmappa a következő struktúrával: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |összeolvasztási hierarchia |A Mappa1 forrásmappa a következő struktúrával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fájl automatikusan létrehozott neve1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott név a File2 fájlhoz<br/><br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |
| hamis |mergeFiles |A Mappa1 forrásmappa a következő struktúrával:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Almappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl5<br/><br/>a mappamappa1 a következő struktúrával jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A Fájl1 + File2 tartalom egyetlen, automatikusan generált fájlnévvel rendelkező fájlba egyesül. fájl automatikusan létrehozott neve1<br/><br/>A File3, File4 és File5 almappát a rendszer nem veszi fel. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
További részletek: [A fájl- és tömörítési formátumok az Azure Data Factory cikkben.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-példák a Data Lake Store-ba és a Data Lake Store-ból történő másoláshoz
Az alábbi példák minta JSON-definíciókat tartalmaznak. Ezekkel a mintadefiníciókkal hozhat létre egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A példák bemutatják, hogyan másolhat adatokat a Data Lake Store és az Azure Blob storage. Az adatok azonban _közvetlenül_ a források bármelyikéből átmásolhatók a támogatott fogadók bármelyikébe. További információt az Adatok áthelyezése a [Tevékenység másolása](data-factory-data-movement-activities.md) című cikk "Támogatott adattárolók és -formátumok" című részében talál.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Példa: Adatok másolása az Azure Blob Storage-ból az Azure Data Lake Store-ba
Az ebben a szakaszban található példakód a következőket mutatja:

* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [AzureDataLakeStore](#linked-service-properties)típusú összekapcsolt szolgáltatás.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
* [AzureDataLakeStore](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [AzureDataLakeStoreSink](#copy-activity-properties)használatával végzett másolási tevékenységet használó [folyamat.](data-factory-create-pipelines.md)

A példák azt mutatják be, hogy az Azure Blob Storage idősorozat-adatait hogyan másolja a rendszer óránként a Data Lake Store-ba.

**Azure Storage-hoz csatolt szolgáltatás**

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

**Azure Data Lake Store kapcsolt szolgáltatás**

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
> A konfigurációval kapcsolatos részleteket a [Csatolt szolgáltatás tulajdonságai](#linked-service-properties) című szakaszban találja.
>

**Azure blobbemeneti adatkészlet**

A következő példában az adatokat óránként (`"frequency": "Hour", "interval": 1`egy új blobból veszi fel a rendszer. A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év-, hónap- és napi részét használja. A fájlnév a kezdési időpont órarészét használja. A `"external": true` beállítás tájékoztatja a Data Factory szolgáltatást, hogy a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

**Az Azure Data Lake Store kimeneti adatkészlete**

A következő példa adatokat másol a Data Lake Store-ba. Az új adatok at óránként másolja a rendszer a Data Lake Store-ba.

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

**Tevékenység másolása blobforrással és Data Lake Store-fogadóval rendelkező folyamatban**

A következő példában a folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránként fut. A folyamat `source` JSON-definíciójában a `BlobSource`típus a `sink` . `AzureDataLakeStoreSink`

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Példa: Adatok másolása az Azure Data Lake Store-ból egy Azure blobba
Az ebben a szakaszban található példakód a következőket mutatja:

* [AzureDataLakeStore](#linked-service-properties)típusú összekapcsolt szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
* [AzureDataLakeStore](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
* [AzureDataLakeStoreSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használatával végzett másolási tevékenységet használó [folyamat.](data-factory-create-pipelines.md)

A kód óránként átmásolja az idősorozat-adatokat a Data Lake Store-ból egy Azure blobba.

**Azure Data Lake Store kapcsolt szolgáltatás**

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
> A konfigurációval kapcsolatos részleteket a [Csatolt szolgáltatás tulajdonságai](#linked-service-properties) című szakaszban találja.
>

**Azure Storage-hoz csatolt szolgáltatás**

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
**Azure Data Lake bemeneti adatkészlete**

Ebben a példában a beállítás, `"external"` hogy `true` tájékoztassa a Data Factory szolgáltatás, hogy a tábla külső adat-előállító, és nem az adat-előállító tevékenység által előállított.

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
**Azure blob kimeneti adatkészlet**

A következő példában az adatok óránként egy`"frequency": "Hour", "interval": 1`új blobba ( írnak. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészét használja.

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

**Egy azure Data Lake Store-forrással és egy blob-fogadóval rendelkező folyamat másolási tevékenysége**

A következő példában a folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránként fut. A folyamat `source` JSON-definíciójában a `AzureDataLakeStoreSource`típus a `sink` . `BlobSink`

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

A másolási tevékenység definíciójában a forrásadatkészlet oszlopait is leképezheti a fogadó adatkészlet oszlopaihoz. További információt az [Adatkészlet-oszlopok leképezése az Azure Data Factoryban című témakörben talál.](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A Másolási tevékenység teljesítményét befolyásoló tényezőkről és optimalizálásukról a [Tevékenység teljesítményének másolása és finomhangolása](data-factory-copy-activity-performance.md) című cikkben olvashat.
