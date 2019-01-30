---
title: IOS – az Azure objektumtárat (Blobtárat) storage használata |} A Microsoft Docs
description: Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló)).
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: 9e4c717b3b205d6c8fdd309dada918eb6df35181
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244760"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Az IOS-es Blob storage használata

Ez a cikk bemutatja, hogyan hajthat végre a Microsoft Azure Blob storage szolgáltatást használó általános forgatókönyvhöz. A minták írt Objective-C és használni a [iOS-hez készült Azure Storage ügyféloldali kódtár](https://github.com/Azure/azure-storage-ios). Az ismertetett forgatókönyvek között megtalálható a feltöltése, listázása, letöltése és a blobok törlése. A blobok további információkért lásd: a [lépések](#next-steps) szakaszban. Emellett letöltheti a [mintaalkalmazás](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) gyorsan megtekintheti az iOS-alkalmazásokba az Azure Storage használatát.

A Blob storage szolgáltatással kapcsolatos további tudnivalókért lásd: [Azure Blob storage bemutatása](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Az Azure Storage iOS függvénytár importálása az alkalmazásba
Importálhatja az Azure Storage-IOS-es kódtárat az alkalmazás használatával az [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) vagy importálásával a **keretrendszer** fájlt. CocoaPod nem ajánlott, mert a integrálása az egyszerűbb, azonban a keretrendszer fájlt importálja a kevésbé zavaró a meglévő projekt library lehetővé teszi.

A könyvtár használatához a következőkre lesz szüksége:
- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod
1. Ha Ön még nem tette, [CocoaPods telepítése](https://guides.cocoapods.org/using/getting-started.html#toc_3) nyissa meg a terminálablakot, és futtassa a következő parancsot a számítógépen
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Ezután hozzon létre egy új fájlt a projekt könyvtárában (az .xcodeproj fájlt tartalmazó könyvtár) _Podfile_(fájlkiterjesztés nélkül). Adja hozzá a következőt _Podfile_ és mentéséhez.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. A terminálablakban keresse meg a projekt könyvtárában, és futtassa a következő parancsot

    ```shell    
    pod install
    ```

4. Ha az .xcodeproj nyissa meg az xcode-ban, zárja be. A projekt könyvtárában nyissa meg az újonnan létrehozott projektre fájlt, amely a .xcworkspace bővítmény lesz. Ez az az használni fog a a most már a fájlt.

## <a name="framework"></a>Keretrendszer
A másik lehetőség, a könyvtár használata, manuális létrehozásához a keretrendszer:

1. Először töltse le vagy klónozza a [azure-storage-ios tárház](https://github.com/azure/azure-storage-ios).
2. Nyissa meg a távoli *azure-storage-ios* -> *Lib* -> *Azure Storage ügyféloldali kódtár*, és nyissa meg a `AZSClient.xcodeproj` az xcode-ban.
3. Felső – bal oldali részén Xcode módosíthatja az aktív séma "Az Azure Storage ügyféloldali kódtára"-"Keretrendszer előírásait".
4. (⌘ + B) a projekt felépítése. Ezzel létrehoz egy `AZSClient.framework` fájlt az asztalon.

Importálhatja a keretrendszer fájlt az alkalmazásba az alábbiak szerint:

1. Hozzon létre egy új projekt, vagy nyissa meg a meglévő projektet az xcode-ban.
2. Húzza át a `AZSClient.framework` az Xcode project navigator be.
3. Válassza ki *elemek másolása, ha szükséges*, majd kattintson a *Befejezés*.
4. Kattintson a bal oldali navigációs projektre, majd kattintson a *általános* lap felső részén a project-szerkesztőt.
5. Alatt a *csatolt keretrendszerek és könyvtárak* területén kattintson a Hozzáadás (+) gombot.
6. A már megadott függvénytárak listáját, keressen `libxml2.2.tbd` , és adja hozzá a projekthez.

## <a name="import-the-library"></a>A függvénytár importálása 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Ha a Swift használ, hozzon létre egy áthidalási fejlécet, és ott importálni < AZSClient/AZSClient.h > kell:

1. Hozzon létre egy fejléc fájlt `Bridging-Header.h`, és adja hozzá a fenti importálási utasítást.
2. Nyissa meg a *Build Settings* lapra, és keressen rá a *Objective-C Bridging fejléc*.
3. Kattintson duplán a mezőre, *Objective-C Bridging fejléc* és a fejlécfájlt a elérési út hozzáadása: `ProjectName/Bridging-Header.h`
4. Állítsa össze a projektet (⌘ + B) ellenőrizze, hogy a áthidalási fejléc volt mértékének növelése xcode-ban.
5. Indítsa el a kódtár használatával közvetlenül a bármely Swift fájlban, nem szükséges az importálási utasításokat.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Az aszinkron műveletek
> [!NOTE]
> Aszinkron műveletek, amelyek az összes módszer, amely a szolgáltatás egy kérelem végrehajtása. A Kódminták a találja, hogy ezek a metódusok rendelkezik-e a befejezéskezelőbe. A befejezéskezelőbe kódot fog futni **után** a kérelem teljesítése. Code, miután a Befejezés kezelő akkor futtatható **közben** történik a kérelmet.
> 
> 

## <a name="create-a-container"></a>Tároló létrehozása
Az Azure Storage összes blobjának egy tárolóban kell lennie. Az alábbi példa bemutatja, hogyan hozzon létre egy tárolót, nevű *newcontainer*, a tárfiókban, ha még nem létezik. Ha a tároló nevét, lehet az elnevezési szabályok a fent említett szem előtt tartva.

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

Ellenőrizheti, hogy ez működik megnézzük a [Microsoft Azure Storage Explorer](http://storageexplorer.com) és ellenőrizte, hogy *newcontainer* szerepel a listán a tárfiókhoz tartozó tárolót.

## <a name="set-container-permissions"></a>Tároló engedélyeinek beállítása
Egy tároló-engedélyek vannak beállítva **privát** hozzáférés alapértelmezés szerint. Azonban tárolók biztosítják a tároló hozzáférésének néhány másik lehetőség:

* **Privát**: Tároló és blobnév adatok csak a fióktulajdonos által is olvasható.
* **Blob**: Ebben a tárolóban lévő blobok adatai olvashatók névtelen kérelem használatával, de adatokat tároló nem érhető el. Ügyfelek névtelen kérelem használatával a tárolóban lévő blobok nem lehet számba venni.
* **tároló**: Tároló és blobnév adat olvasható névtelen kérelem használatával. Ügyfelek névtelen kérelem használatával a tárolóban található blobok enumerálása, de nem sikerült felsorolni a tárfiókban lévő tárolókat.

Az alábbi példa bemutatja, hogyan hozzon létre egy tárolót az **tároló** hozzáférési engedélyeket, amelyek lehetővé teszik az összes felhasználó számára az interneten nyilvános, írásvédett hozzáférést:

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
Említetteknek megfelelően az [Blob szolgáltatással kapcsolatos fogalmak](#blob-service-concepts) szakaszban, a Blob Storage blobok három különböző típusú biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat. Az Azure Storage-IOS-es kódtárat a blobok három típusú támogatja. A legtöbb esetben a blokkblobok használata javasolt.

Az alábbi példa bemutatja, hogyan tölthet fel egy blokkblobot az egy NSString. Ha egy blob ugyanazzal a névvel már létezik ebben a tárolóban, a blob tartalma felülíródik.

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

Ellenőrizheti, hogy ez működik megnézzük a [Microsoft Azure Storage Explorer](http://storageexplorer.com) és ellenőrizte, hogy a tároló *containerpublic*, tartalmazza a blob *sampleblob*. Ebben a példában is használt egy nyilvános tárolóban úgy is ellenőrizheti, hogy az alkalmazás a blobok URI-t a dolgozni:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Mellett egy NSString a blokkblob feltöltése, hasonló módszerekkel létező NSData, NSInputStream vagy egy helyi fájlba.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
Az alábbi példa bemutatja, hogyan listázhatja a tárolóban lévő összes BLOB. Ez a művelet végrehajtásakor kell szem előtt tartva a következő paraméterek közül:     

* **continuationtoken argumentumot használja** -a folytatási token jelöli, ahol a listázási művelet el kell indulnia. Ha nem jogkivonat nincs megadva, a blobok az elejétől listázza. Korlátlan számú blobot is listázva lehet, akár egy sor maximális nulla. Akkor is, ha ez a módszer nulla eredményeket ad vissza, ha `results.continuationToken` következő nem üres, előfordulhat, hogy a szolgáltatás további a blobokat, amelyekre nincs felsorolva.
* **előtag** -a blob felsorolását használni kívánt előtagot is megadhat. Csak azok a blobok ezzel az előtaggal kezdődő jelennek meg.
* **Listblobs** – említetteknek megfelelően az [elnevezésével és hivatkozásával tárolók és blobok](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) szakaszban, bár a blobszolgáltatás egy egybesimított storage séma is létrehozhat egy virtuális hierarchia elérési úttal rendelkező blobok elnevezési információk. Azonban nem sík listaelem jelenleg nem támogatott. Ez a funkció hamarosan elérhető lesz. Egyelőre ennek az értéknek kell lennie **Igen**.
* **blobListingDetails** – megadhatja, hogy mely elemek közé tartozik a blobok listázásakor
  * _AZSBlobListingDetailsNone_: Csak a véglegesített blobok listázása, és a blob metaadatait nem adják vissza.
  * _AZSBlobListingDetailsSnapshots_: Véglegesített blobok listázása és a blob-pillanatképekkel.
  * _AZSBlobListingDetailsMetadata_: A lista minden egyes blob lekérése blob metaadatait adja vissza.
  * _AZSBlobListingDetailsUncommittedBlobs_: Véglegesített és a nem véglegesített blobok listázása.
  * _AZSBlobListingDetailsCopy_: Tulajdonságok másolása felvétel a listában.
  * _AZSBlobListingDetailsAll_: Az összes elérhető véglegesített blobok, a nem véglegesített blobok és a pillanatfelvételeket listában, és visszaadja azokat a blobokat minden metaadatok és a példány állapotát.
* **maxResults** – Ez a művelet esetében visszaadandó eredmények maximális száma. Használja a -1 nem állítson be kényszerített korlátot.
* **completionHandler** – a kódblokkot, hajtsa végre a listázási művelet eredményét.

Ebben a példában egy olyan segédmetódus szolgál, hogy a lista rekurzív hívás blobok metódus minden alkalommal, amikor egy folytatási tokent ad vissza.

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
Az alábbi példa bemutatja, hogyan letölt egy blobot egy NSString objektumhoz.

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
Az alábbi példa bemutatja, hogyan blobok törléséhez.

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

## <a name="delete-a-blob-container"></a>A blob-tároló törlése
Az alábbi példa bemutatja, hogyan törölhet egy tárolót.

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
Most, hogy megismerte a Blob Storage használata az iOS, az alábbi hivatkozásokból tudhat meg többet az iOS-klienskódtár és a Storage szolgáltatás.

* [IOS-hez készült Azure Storage ügyféloldali kódtára](https://github.com/azure/azure-storage-ios)
* [Az Azure Storage iOS dokumentációja](http://azure.github.io/azure-storage-ios/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](https://blogs.msdn.com/b/windowsazurestorage)

Ha ez a kódtár kapcsolatban kérdése van, nyugodtan közzététele a [MSDN Azure-fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) vagy [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Ha az Azure Storage szolgáltatási javaslataikat, helyezzen el bejegyzést [Azure Storage visszajelzési](https://feedback.azure.com/forums/217298-storage/).

