---
title: Objektum (Blob) tároló használata iOS-ből - Azure | Microsoft dokumentumok
description: Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló)).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726399"
---
# <a name="how-to-use-blob-storage-from-ios"></a>A Blob storage használata iOS-ből

Ez a cikk bemutatja, hogyan hajthatja végre a gyakori forgatókönyvek a Microsoft Azure Blob storage használatával. A minták a C célkitűzés ben íródnak, és az [iOS-hez az Azure Storage ügyfélkönyvtárát](https://github.com/Azure/azure-storage-ios)használják. Az érintett forgatókönyvek közé tartozik a feltöltés, a listaba vétel, a letöltés és a blobok törlése. A blobokkal kapcsolatos további információkért tekintse meg a [Következő lépések szakaszt.](#next-steps) A [mintaalkalmazást](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) is letöltheti, hogy gyorsan láthassa az Azure Storage használatát egy iOS-alkalmazásban.

A Blob storage bemutatása című témakörben olvashat [bővebben.](storage-blobs-introduction.md)

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Az Azure Storage iOS-könyvtárának importálása az alkalmazásba

Importálhatja az Azure Storage iOS-könyvtárat az alkalmazásba az [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) használatával vagy a **Framework** fájl importálásával. CocoaPod van a ajánlott út mint ez ideiglenes tákolmány integráló a könyvtár könnyebb, bármennyire behoz -ból keret reszelő van kevesebb tolakodó részére -a létező tervez.

A tár használatához a következőkre van szükség:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>Kakaópod

1. Ha még nem tette meg, [telepítse a CocoaPods-ot](https://guides.cocoapods.org/using/getting-started.html#toc_3) a számítógépre egy terminálablak megnyitásával és a következő parancs futtatásával

    ```shell
    sudo gem install cocoapods
    ```

2. Ezután a projekt könyvtárában (az .xcodeproj fájlt tartalmazó könyvtárban) hozzon létre egy új fájlt _Podfile_néven (nincs fájlkiterjesztés). Adja hozzá a következőket a _Podfile fájlhoz,_ és mentse.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. A terminálablakban keresse meg a projekt könyvtárát, és futtassa a következő parancsot

    ```shell
    pod install
    ```

4. Ha a .xcodeproj xcode-ban van megnyitva, zárja be. A projektkönyvtárban nyissa meg az újonnan létrehozott projektfájlt, amelynek .xcworkspace kiterjesztése lesz. Ez az akta, amit mostantól dolgozni fogsz.

## <a name="framework"></a>Keretrendszer

A könyvtár használatának másik módja a keretrendszer manuális létrehozása:

1. Először töltse le vagy klónozza az [azure-storage-ios repo](https://github.com/azure/azure-storage-ios).
2. Lépjen be *az azure-storage-ios* -> *Lib* -> Azure `AZSClient.xcodeproj` Storage*ügyfélkönyvtárba,* és nyissa meg az Xcode-ot.
3. Az Xcode bal felső részén módosítsa az aktív sémát "Azure Storage Ügyfélkönyvtárról" "Framework"-re.
4. A projekt létrehozása (啦+B). Ezzel létrehoz `AZSClient.framework` egy fájlt az asztalon.

Ezután importálhatja a keretrendszerfájlt az alkalmazásba az alábbi módon:

1. Hozzon létre egy új projektet, vagy nyissa meg a meglévő projektet az Xcode-ban.
2. Húzza az `AZSClient.framework` Xcode projekt navigátorába.
3. Válassza az *Elemek másolása lehetőséget, ha szükséges*, és kattintson a Befejezés *gombra.*
4. Kattintson a projektre a bal oldali navigációs sávon, és kattintson a projektszerkesztő tetején található *Általános* fülre.
5. A *Csatolt keretek és könyvtárak csoportban* kattintson a Hozzáadás gombra (+).
6. A már megadott tárak listájában keresse `libxml2.2.tbd` meg és adja hozzá a projekthez.

## <a name="import-the-library"></a>A könyvtár importálása

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Ha swiftet használ, létre kell hoznia egy \<áthidaló fejlécet, és importálnia kell az AZSClient/AZSClient.h>:

1. Hozzon létre `Bridging-Header.h`egy fejlécfájlt , és adja hozzá a fenti importálási utasítást.
2. Nyissa meg a *Build Settings (Létrehozási beállítások)* lapot, és keresse meg az *Objective-C összekötő fejlécet.*
3. Kattintson duplán a *C objektív összekötő fejléc területére,* és adja hozzá az elérési utat a fejlécfájlhoz:`ProjectName/Bridging-Header.h`
4. A projekt (啦+B) összeállítása annak ellenőrzéséhez, hogy az áthidaló fejlécet az Xcode felvette-e.
5. Kezdje el használni a könyvtárat közvetlenül bármely Swift fájlban, nincs szükség importálási utasításokra.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Aszinkron műveletek

> [!NOTE]
> A szolgáltatással szemben kérelmet végző összes metódus aszinkron művelet. A kódmintákban azt fogja találni, hogy ezek a módszerek rendelkeznek egy befejezési kezelővel. A befejezési kezelőn belüli kód a kérelem befejezése **után** fog futni. A befejezési kezelő utáni kód a kérés eltöltése **közben** fog futni.

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Storage minden blobjának egy tárolóban kell lennie. A következő példa bemutatja, hogyan hozhat létre egy tárolót, az *úgynevezett newcontainer,* a tárfiókban, ha még nem létezik. A tároló nevének kiválasztásakor vegye figyelembe a fent említett elnevezési szabályokat.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Megerősítheti, hogy ez működik, ha megnézi a [Microsoft Azure Storage Explorert,](https://storageexplorer.com) és ellenőrzi, hogy az *új tároló* szerepel-e a storage-fiók tárolóinak listájában.

## <a name="set-container-permissions"></a>Tárolóengedélyek beállítása

A tároló engedélyei alapértelmezés szerint **magánhozzáférésre** vannak konfigurálva. A tárolók azonban néhány különböző lehetőséget biztosítanak a tárolók eléréséhez:

- **Magán:** A tároló- és blobadatokat csak a fiók tulajdonosa olvashatja.
- **Blob:** Blob adatok ebben a tárolóban névtelen kérelem, de a tároló adatok nem érhetők el. Az ügyfelek névtelen kérelem melinációk on-át nem sorolhatják fel a tárolón belüli blobokat.
- **Tároló:** A tároló- és blobadatok névtelen kérelemmel olvashatók. Az ügyfelek névtelen kéréssel felsorolhatják a tárolón belüli blobokat, de a tárfiókon belüli tárolókat nem.

A következő példa bemutatja, hogyan hozhat létre **tárolót tárolóhozzáférési** engedélyekkel, amely lehetővé teszi a nyilvános, csak olvasható hozzáférést az összes felhasználó számára az interneten:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba

ABlob-szolgáltatás fogalmai szakaszban említettek szerint a Blob Storage három különböző típusú blobot kínál: blokkblobokat, hozzáfűző blobokat és lapblobokat. Az Azure Storage iOS-kódtár mindhárom blobtípust támogatja. A legtöbb esetben a blokkblobok használata javasolt.

A következő példa bemutatja, hogyan tölthet fel egy blokkblobot egy NSStringből. Ha egy blob azonos nevű már létezik ebben a tárolóban, a blob tartalmát felülírja.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Ez a Microsoft Azure Storage [Explorer](https://storageexplorer.com) segítségével ellenőrizheti, hogy a tároló , *containerpublic*, tartalmazza-e a blobot, *sampleblob.* Ebben a példában egy nyilvános tárolót használtunk, így azt is ellenőrizheti, hogy ez az alkalmazás működött-e a blobok URI-ba való áttérésével:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

A blokkblob NSStringből való feltöltése mellett hasonló módszerek léteznek az NSData, az NSInputStream vagy a helyi fájl számára is.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A következő példa bemutatja, hogyan listázza az összes blobot egy tárolóban. A művelet végrehajtásakor vegye figyelembe a következő paramétereket:

- **continuationToken** - A folytatási jogkivonat azt jelzi, hogy a lista-műveletnek hol kell elindulnia. Ha nincs token, akkor a blobok az elejétől kezdve. Tetszőleges számú blobok listázható, nullától a beállított maximumig. Még akkor is, ha `results.continuationToken` ez a módszer nulla eredményt ad vissza, ha nem nulla, előfordulhat, hogy több blobok a szolgáltatás, amely nem szerepel a listán.
- **előtag** – Megadhatja a bloblistázáshoz használandó előtagot. Csak az ezzel az előtaggal kezdődő blobok jelennek meg a listában.
- **useFlatBlobListing** – Ahogy az [elnevezési és hivatkozási tárolók és blobok](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) szakaszban, bár a Blob szolgáltatás egy sík tárolási séma, létrehozhat egy virtuális hierarchia elnevezésével blobok elérési út adatait. A nem lakásalapú listaazonban jelenleg nem támogatott. Ez a funkció hamarosan. Most, ez az érték kell **IGEN**.
- **blobListingDetails** - Megadhatja, hogy mely elemeket kell belefoglalni a blobok listázásakor
  - _AZSBlobListingDetailsNone_: Csak a véglegesített blobok listája, és ne adja vissza a blob metaadatait.
  - _AZSBlobListingDetailsSnapshots_: Véglegesített blobok és blobpillanatképek listázása.
  - _AZSBlobListingDetailsMetadata_: Blob metaadatok lekérése a listaelemben visszaadott minden blobhoz.
  - _AZSBlobListingDetailsUncommittedBlobs_: Véglegesített és nem véglegesített blobok listája.
  - _AZSBlobListingDetailsCopy_: Másolási tulajdonságok felvétele a listához.
  - _AZSBlobListingDetailsAll_: Az összes rendelkezésre álló véglegesített blobok, nem véglegesített blobok és pillanatképek, és adja vissza az összes metaadatok és másolási állapot a blobok.
- **maxResults** - A művelethez visszaadandó eredmények maximális száma. A -1 használatával ne állítson be korlátot.
- **completionHandler** - A listaelem-művelet eredményeivel végrehajtandó kódblokk.

Ebben a példában egy segítő módszert használ rekurzív an a listablobok metódus minden alkalommal, amikor egy folytatási jogkivonatot ad vissza.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Blob letöltése

A következő példa bemutatja, hogyan tölthet le egy blobot egy NSString-objektumra.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Blob törlése

A következő példa bemutatja, hogyan kell törölni egy blobot.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Blob-tároló törlése

A következő példa bemutatja, hogyan kell törölni egy tárolót.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a Blob Storage iOS-ből való használatát, kövesse ezeket a hivatkozásokat, hogy többet tudjon meg az iOS-könyvtárról és a Storage szolgáltatásról.

- [IOS-hez épülő Azure Storage-ügyfélkönyvtár](https://github.com/azure/azure-storage-ios)
- [Az Azure Storage iOS referenciadokumentációja](https://azure.github.io/azure-storage-ios/)
- [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Az Azure Storage-csapat blogja](https://blogs.msdn.com/b/windowsazurestorage)

Ha kérdése van ezzel a könyvtárral kapcsolatban, nyugodtan tegye közzé [az MSDN Azure fórumán](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) vagy [a Stack Overflow szolgáltatásban.](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)
Ha funkciójavaslatai vannak az Azure Storage szolgáltatáshoz, kérjük, írjon az [Azure Storage-visszajelzéshez.](https://feedback.azure.com/forums/217298-storage/)
