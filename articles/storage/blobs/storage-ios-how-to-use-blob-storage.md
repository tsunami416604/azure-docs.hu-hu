---
title: IOS - Azure-objektum (Blob) tárhelyhez használata |} Microsoft Docs
description: Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló)).
services: storage
documentationcenter: ios
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: a15ba7409b4c5f75729b1b40cd2f333c44ae0368
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-ios"></a>Blob storage-ának iOS használata

Ez a cikk bemutatja, hogyan hajthat végre a szolgáltatást a Microsoft Azure Blob Storage tárolót használó általános forgatókönyvhöz. A minták írt Objective-C és használni a [Azure Storage ügyféloldali kódtára a iOS](https://github.com/Azure/azure-storage-ios). Az ismertetett forgatókönyvek közé tartozik a feltöltése, listázása, letöltése és blobok törlése. A blobok további információkért lásd: a [lépések](#next-steps) szakasz. Emellett letöltheti a [mintaalkalmazás](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) gyorsan megtekintheti az iOS-alkalmazás az Azure Storage használatát.

## <a name="what-is-blob-storage"></a>Mi az a Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Az Azure Storage iOS kódtár importálnia kell az alkalmazás
Az Azure Storage iOS kódtár programba importálhatók az alkalmazás használatával az [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) vagy importálásával a **keretrendszer** fájl. CocoaPod nem ajánlott, mert így egyszerűbb, azonban a keretrendszer-fájlból való importálása a meglévő projekthez kevesebb zavaró könyvtár integrálása.

A könyvtár használatához a következőkre lesz szüksége:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Ha még nem tette, [telepítése CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) nyisson meg egy terminálablakot, és futtassa a következő parancsot a számítógépen
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Ezután a projektkönyvtárban (a .xcodeproj fájlt tartalmazó könyvtár) hozzon létre egy új fájlt nevű _Podfile_(nincs fájl kiterjesztése). Adja hozzá a következőt a _Podfile_ és mentéséhez.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Az a terminálablakot keresse meg a projekt könyvtárában, és futtassa a következő parancsot

    ```shell    
    pod install
    ```

4. Ha a .xcodeproj nyissa meg az xcode-ban, zárja be. A projekt könyvtárában nyissa meg a újonnan létrehozott projektfájlt, amelynek a .xcworkspace bővítmény. Ez az a lesz dolgozik a most már a fájlt.

## <a name="framework"></a>Keretrendszer
A másik lehetőség, hogy használhassa a kódtárat, hogy a keretrendszer összeállítása manuálisan:

1. Első lépésként töltse le, vagy klónozza a [azure-storage-ios tárház](https://github.com/azure/azure-storage-ios).
2. Nyissa meg a *azure-storage-ios* -> *Lib* -> *Azure Storage ügyféloldali kódtár*, és nyissa meg a `AZSClient.xcodeproj` az xcode-ban.
3. Felső – bal oldali részén Xcode módosíthatja az aktív séma "Azure Storage ügyféloldali kódtár"-"Keretrendszer".
4. (⌘ + B) a projekt felépítéséhez. Ezzel létrehoz egy `AZSClient.framework` fájl az asztalon.

Majd importálhatja a keretrendszer fájlt az alkalmazás a következő módon:

1. Hozzon létre egy új projektet, vagy nyissa meg a meglévő projektet az xcode-ban.
2. Húzza a `AZSClient.framework` az Xcode project navigator be.
3. Válassza ki *elemek másolása, szükség esetén*, majd kattintson a *Befejezés*.
4. Kattintson a bal oldali navigációs projektre, és kattintson a *általános* lap felső részén a projekt-szerkesztőt.
5. Az a *csatolt keretrendszerek és könyvtárak* területen kattintson a Hozzáadás gomb (+).
6. A már megadott könyvtárak listájának megtekintéséhez keresse meg a `libxml2.2.tbd` , és adja hozzá a projekthez.

## <a name="import-the-library"></a>A könyvtár importálása 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Ha a Swift használ, akkor hozzon létre egy áthidalási fejlécet, és ott importálni < AZSClient/AZSClient.h >:

1. Hozzon létre egy fejlécfájlt `Bridging-Header.h`, és adja hozzá a fenti importálási utasítást.
2. Lépjen a *Build Settings* lapot, és keresse meg *Objective-C Bridging fejléc*.
3. Kattintson duplán arra a területén *Objective-C Bridging fejléc* és az elérési út hozzáadása a fejléc fájlhoz: `ProjectName/Bridging-Header.h`
4. Építse fel a projektet (⌘ + B) Győződjön meg arról, hogy a áthidalási fejléc volt észlelnie xcode-ban.
5. Indítsa el a közvetlenül a Swift fájl használatával a könyvtárban, és nincs szükség az importálási utasítást.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Az aszinkron műveletek
> [!NOTE]
> Az összes, hajtsa végre a szolgáltatás egy kérelmet módszereket aszinkron műveletek. A Kódminták talál, ezek a módszerek rendelkezik-e a befejezési kezelő. A befejezési kezelő kódot fog futni **után** a kérelem teljesítése. Miután a Befejezés kezelő akkor futtatható kód **közben** alatt a kérelem.
> 
> 

## <a name="create-a-container"></a>Tároló létrehozása
Az Azure Storage összes blobjának egy tárolóban kell lennie. A következő példa bemutatja, hogyan hozhat létre tárolót, úgynevezett *newcontainer*, ha még nem létezik a tárfiókban lévő. A tároló nevét kiválasztásakor kell szem előtt tartva a fent említett elnevezési szabályok.

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

Úgy ellenőrizheti, hogy ez működik, ha megnézi a [Microsoft Azure Tártallózó](http://storageexplorer.com) és ellenőrizze, hogy *newcontainer* a tárfiók tárolók listája szerepel.

## <a name="set-container-permissions"></a>Tároló engedélyeinek beállítása
Egy tároló-engedélyek vannak beállítva a **titkos** alapértelmezés szerint a hozzáférés. Azonban tárolók biztosítják a tároló hozzáférés különböző lehetőségek közül néhány:

* **Személyes**: tároló és a blob adatainak által a fiók tulajdonosa csak olvasható.
* **A BLOB**: Blobadatok található olvasható névtelen kérelem keresztül, de adatai nem érhető el. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok nem tudja felsorolni.
* **Tároló**: tároló és a blob adatainak keresztül névtelen kérelem olvasható. Ügyfelek névtelen kérelem keresztül a tárolóban található blobok enumerálása, de nem tudja felsorolni a tárolók a tárfiókon belül.

A következő példa bemutatja, hogyan hozhat létre a tárolót **tároló** hozzáférési engedélyek, amelyek az interneten lévő összes felhasználója számára engedélyezi a nyilvános, csak olvasható hozzáférést:

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
A a [Blob szolgáltatással kapcsolatos fogalmak](#blob-service-concepts) szakaszban, a Blob Storage blobok három különböző típusú biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat. Az Azure Storage iOS kódtár támogatja az összes háromféle blobot. A legtöbb esetben a blokkblobok használata javasolt.

A következő példa bemutatja, hogyan egy blokkblob egy NSString való feltöltéséhez. Ha egy blobot ugyanazzal a névvel már létezik ebben a tárolóban, a blob tartalma felülíródik.

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

Úgy ellenőrizheti, hogy ez működik, ha megnézi a [Microsoft Azure Tártallózó](http://storageexplorer.com) és ellenőrizze, hogy a tároló *containerpublic*, tartalmaz a blob *sampleblob*. Ez a példa használtuk nyilvános tárolókban úgy is ellenőrizheti, hogy az alkalmazás előre nyissa meg a BLOB URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Mellett a blokkblob egy NSString a feltöltés, a hasonló mód NSData, NSInputStream vagy egy helyi fájl létezik.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A következő példa bemutatja, hogyan minden, a tárolóban lévő blobok listázásához. Ez a művelet végrehajtásakor kell szem előtt tartva a következő paraméterekkel:     

* **continuationToken** -a folytatási token jelöli, ahol a listázási művelet kell kezdődnie. Ha nincs jogkivonat van megadva, az elejétől blobok felsorolja. Korlátlan számú blobot is listázva lehet, akár egy készlet maximális nulla. Akkor is, ha ez a módszer nulla eredményeket ad vissza, ha `results.continuationToken` értéke nem nulla, előfordulhat, hogy további BLOB szolgáltatás, amely nem szerepel.
* **előtag** -blob felsorolást használni kívánt előtagot is megadhat. Csak a előtaggal kezdődő blobok jelenik meg.
* **Listblobs** – ahogy azt a [elnevezési és a tárolók és blobok hivatkozó](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) szakaszban, a Blob szolgáltatás azonban egy egyszerű tároló séma hozhat létre egy virtuális hierarchia történő elérési úttal rendelkező blobok elnevezésével információ. Azonban nem simán listaelem jelenleg nem támogatott. Ez a funkció hamarosan elérhető. Most ezt az értéket kell **Igen**.
* **blobListingDetails** -megadhatja, hogy mely elemek közé tartozik a blobok listázása során
  * _AZSBlobListingDetailsNone_: csak a véglegesített blobok listázása, és nem ad vissza a blob metaadatait.
  * _AZSBlobListingDetailsSnapshots_: véglegesített blobok listázása és a blob-pillanatképeket.
  * _AZSBlobListingDetailsMetadata_: minden egyes blob lekérése blob metaadatait az átjáróra a listában az adott vissza.
  * _AZSBlobListingDetailsUncommittedBlobs_: véglegesített és a nem véglegesített blobok listázása.
  * _AZSBlobListingDetailsCopy_: másolás közé tartoznak az átjáróra a listában szereplő tulajdonságok.
  * _AZSBlobListingDetailsAll_: kilistázhatja az összes elérhető véglegesített BLOB, a nem véglegesített blobok és pillanatképeket, és térjen vissza az ezeket a blobok minden metaadatok és a példány állapota.
* **maxResults** – térjen vissza ehhez a művelethez az eredmények maximális száma. Használja a -1 nem korlátozhatja.
* **completionHandler** – a kódblokkot az eredmények a listázási művelet végrehajtása.

Ebben a példában egy segédmetódust szolgál rekurzív módon hívja a lista blobok módszer minden alkalommal, amikor a folytatási kód adja vissza.

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
A következő példa bemutatja, hogyan töltse le a blob NSString objektumhoz.

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
A következő példa bemutatja, hogyan blobok törléséhez.

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
A következő példa bemutatja, hogyan törölni a tárolót.

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
Most, hogy megismerte a Blob Storage használata az iOS, az alábbi hivatkozásokból tudhat meg többet az iOS-könyvtár és a tároló szolgáltatást.

* [Az IOS rendszerhez készült Azure Storage ügyféloldali kódtár](https://github.com/azure/azure-storage-ios)
* [Az Azure Storage iOS Referenciadokumentációt](http://azure.github.io/azure-storage-ios/)
* [Az Azure Storage-szolgáltatások REST API-ja](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Az Azure Storage csapat blogja](http://blogs.msdn.com/b/windowsazurestorage)

Ha ezt a szalagtárat kapcsolatos kérdése van, úgy, hogy küldje el a [MSDN Azure fórumon](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) vagy [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Ha az Azure Storage szolgáltatási javaslataikat, tegye a [Azure Storage visszajelzés](https://feedback.azure.com/forums/217298-storage/).

