<properties
    pageTitle="Get started with Azure Blob storage (object storage) using .NET (Az Azure Blob Storage (objektumtár) használatának első lépései a .NET-keretrendszerrel) | Microsoft Azure"
    description="Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="04/25/2016"
    ms.author="tamram"/>


# Get started with Azure Blob Storage using .NET (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Áttekintés

Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

### Az oktatóanyag ismertetése

Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány, az Azure Blob Storage szolgáltatást használó általános forgatókönyvhöz. A tárgyalt forgatókönyvekben szerepel a blobok feltöltése, listázása, letöltése és törlése.

**Az oktatóanyag áttekintésének becsült ideje:** 45 perc

**Előfeltételek:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Az Azure Storage .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Egy [Azure-tárfiók](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### Névtér-deklarációk hozzáadása

Adja hozzá a következő `using` utasításokat a `program.cs` fájl elejéhez:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types

### Kapcsolati karakterlánc elemzése

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### A Blob szolgáltatásügyfél létrehozása

A **CloudBlobClient** osztály lehetővé teszi a Blob Storage-fiókban tárolt tárolók és blobok lekérését. A szolgáltatásügyfél létrehozásának egyik módja:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

Most már készen áll a Blob Storage-ból adatokat olvasó és abba adatokat író kód írására.

## Tároló létrehozása

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

A példa bemutatja, hogyan hozhat létre tárolót, ha még nem rendelkezik vele:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Alapértelmezés szerint az új tároló privát, azaz mielőtt blobokat tölthetne le ebből a tárolóból, meg kell adnia a tárelérési kulcsát. Ha a tárolóban lévő fájlokat mindenki számára elérhetővé kívánja tenni, akkor az alábbi kód segítségével nyilvánossá teheti a tárolót:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

Az interneten mindenki megtekintheti a nyilvános tárolókban lévő blobokat, de csak akkor módosíthatja vagy törölheti őket, ha rendelkezik a megfelelő tárelérési kulccsal vagy közös hozzáférésű jogosultságkóddal.

## Blobok feltöltése a tárolóba

Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat.  A legtöbb esetben a blokkblobok használata javasolt.

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, akkor az **UploadFromStream** metódus hívásával bármilyen streamet feltölthet. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## A tárolóban lévő blobok listázása

A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezt követően a tároló **ListBlobs** metódusának segítéségével lekérheti a benne lévő blobokat és/vagy könyvtárakat. A **IListBlobItem** parancs visszaadásakor elérhető számos tulajdonság és metódus eléréséhez át kell alakítania azt **CloudBlockBlob**, **CloudPageBlob** vagy **CloudBlobDirectory** objektummá.  Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani.  Az alábbi kód bemutatja, hogyan kérhető le és küldhető el a `photos` tárolóban lévő egyes elemek URI azonosítója:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Ahogy fent is látható, a blobok neve tartalmazhat elérési útra vonatkozó adatokat. Ez egy olyan virtuális könyvtárstruktúrát hoz létre, amelyet egy hagyományos fájlrendszerhez hasonlóan rendszerezhet és bejárhat. Ne feledje, hogy a könyvtárstruktúra csupán virtuális – a Blob Storage szolgáltatásban elérhető erőforrások kizárólag a tárolók és a blobok. A Storage ügyféloldali kódtára azonban **CloudBlobDirectory** objektumot biztosít a virtuális könyvtárra való hivatkozáshoz és az így rendszerezett blobokkal való munkavégzés folyamatainak egyszerűsítéséhez.

Vegyük példaként az alábbi blokkblobokat a `photos` nevű tárolóban:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

A **ListBlobs** meghívása a „photos” tárolóban (a fenti példa szerint) hierarchikus listát ad vissza. **CloudBlobDirectory** és **CloudBlockBlob** objektumokat is tartalmaz, amelyek a tárolóban található kódtárakat, illetve blobokat jelölik. Ennek kimenete a következőképpen néz ki:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Beállíthatja a **ListBlobs** metódus **UseFlatBlobListing** paraméterét **true** értékre. Ebben az esetben a tárolóban található minden blobot **CloudBlockBlob** objektumként adja vissza a rendszer. A **ListBlobs** hívása egy strukturálatlan lista visszaadásáért így néz ki:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

az eredmények pedig így néznek ki:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Blobok letöltése

Blobok letöltéséhez először kérjen le egy blobhivatkozást, majd hívja a **DownloadToStream** metódust. A következő példa a **DownloadToStream** metódus segítségével helyezi át a blob tartalmát egy stream objektumra, amelyet egy helyi fájlban megőrizhet.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

A **DownloadToStream** metódussal a blob tartalmát szöveges karakterláncként is letöltheti.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## Blobok törlése

Blobok törléséhez először kérjen le egy blobhivatkozást, majd hívja a **Delete** metódust.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Blobok listázása több oldalon aszinkron módon

Nagyszámú blob listázásakor, vagy ha szabályozni szeretné a listázási művelet által megjelenített eredmények számát, a blobokat több eredményoldalra is listázhatja. Ez a példa bemutatja, hogyan kérhetők le eredmények több oldalon aszinkron módon úgy, hogy amíg egy nagy eredménykészletre várakozik, a rendszer ne tiltsa le a végrehajtást.

Ez a lista egy strukturálatlan bloblistát mutat be, de hierarchikus listát is kérhet, ha a `useFlatBlobListing` paramétert a **ListBlobsSegmentedAsync** metódusban átállítja `false` értékre.

Mivel a mintametódus aszinkron metódust használ, előtagként az `async` kulcsszót kell megadni, és **Task** objektumot kell visszaadnia. A **ListBlobsSegmentedAsync** metódushoz megadott await kulcsszó a listázási feladat befejezéséig felfüggeszti a mintametódus futtatását.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Írás hozzáfűző blobba

A hozzáfűző blob egy új típusú blob, amely a .NET-hez készült Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve érhető el. A hozzáfűző blob hozzáfűzési feladatokra, például naplózásra van optimalizálva. A blokkblobhoz hasonlóan a hozzáfűző blobot is blokkok alkotják, de ha egy hozzáfűző blobhoz új blokkot ad hozzá, az mindig a blob végéhez lesz hozzáfűzve. Hozzáfűző blob meglévő blokkja nem frissíthető és nem törölhető. A hozzáfűző blob blokkazonosítói nincsenek közzétéve, mert azok egy blokkblobhoz tartoznak.

Egy hozzáfűző blob minden blokkja különböző méretű lehet – 4 MB maximális értékig –, egy hozzáfűző blob pedig legfeljebb 50 000 blokkot tartalmazhat. A hozzáfűző blob maximális mérete ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).

Az alábbi példa új hozzáfűző blob létrehozását és adatok hozzáfűzését mutatja be, egy egyszerű naplózási műveletet szimulálva.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist.
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);

    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

A blobok három különböző típusa közötti különbségekről bővebb információt az [Understanding Block Blobs, Page Blobs, and Append Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx) (A blokkblobok, a lapblobok és a hozzáfűző blobok ismertetése) című részben talál.

## A blobok biztonságának kezelése

Alapértelmezés szerint az Azure Storage úgy biztosítja az adatok védelmét, hogy a hozzáférést a fiók tulajdonosára korlátozza, aki a fiók tárelérési kulcsaival rendelkezik. Ha a tárfiókjában lévő blobadatokat kell megosztania, fontos, hogy azt a fiók tárelérési kulcsai biztonságának fenyegetése nélkül tegye. A blobadatokat titkosíthatja, hogy továbbításuk biztonságosan történjen a hálózaton és az Azure Storage szolgáltatásban.

[AZURE.INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### A blobadatokhoz való hozzáférés szabályozása

Alapértelmezés szerint a tárfiókban tárolt adatok csak a fiók tulajdonosa számára hozzáférhetőek. Alapértelmezés szerint a Blob Storage-fiókhoz intézett hitelesítési kérésekhez szükség van a tárelérési kulcs megadására. Lehetséges azonban, hogy bizonyos blobadatokat más felhasználók számára is elérhetővé szeretne tenni. Erre két lehetősége van:

- **Névtelen hozzáférés:** Egy tárolót vagy a benne található blobokat nyilvánosan elérhetővé teheti névtelen hozzáférés céljából. További információk: [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).
- **Közös hozzáférésű jogosultságkód:** A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz az Ön által meghatározott időtartamra és jogosultságokkal. További információk: [Shared Access Signatures: Understanding the SAS model](storage-dotnet-shared-access-signature-part-1.md) (Közös hozzáférésű jogosultságkódok: az SAS-modell bemutatása).

### Blobadatok titkosítása

Az Azure Storage mind az ügyfél, mind a kiszolgáló oldalán támogatja a titkosítást:

- **Ügyféloldali titkosítás:** A Storage .NET-keretrendszerhez készült ügyféloldali kódtára támogatja az adatok ügyfélalkalmazásokon belüli titkosítását az Azure Storage-ba való feltöltésük előtt, illetve az adatok visszafejtését az ügyfélre történő letöltéskor. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. További információ: [Client-Side Encryption with .NET for Microsoft Azure Storage](storage-client-side-encryption.md) (Ügyféloldali titkosítás a .NET for Microsoft Azure Storage szolgáltatással). Lásd még: [Tutorial: Encrypt and decrypt blobs in Microsoft Azure Storage using Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md) (Oktatóanyag: Blobok titkosítása és visszafejtése a Microsoft Azure Storage-ban az Azure Key Vault használatával).
- **Kiszolgálóoldali titkosítás**: Az Azure Storage már a kiszolgálóoldali titkosítást is támogatja. Lásd: [Azure Storage Service Encryption for Data at Rest (Preview)](storage-service-encryption.md) (Az Azure Storage szolgáltatás inaktívadat-titkosítása (Előzetes verzió)). 

## További lépések

Most, hogy megismerte a Blob Storage alapjait, az alábbi hivatkozásokat követve tudhat meg többet.

### Blob Storage-hivatkozás

- [A Storage ügyféloldali kódtára a .NET-hez – referencia](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST API-referencia](http://msdn.microsoft.com/library/azure/dd179355)

### Fogalmi útmutatók

- [Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](storage-use-azcopy.md)
- [Get started with File storage for .NET (Bevezetés a File Storage for .NET használatába)](storage-dotnet-how-to-use-files.md)
- [How to use Azure blob storage with the WebJobs SDK (Az Azure Blob Storage használata a WebJobs SDK-val)](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Az Azure Storage csapat blogja]: http://blogs.msdn.com/b/windowsazurestorage/
  [Kapcsolati karakterláncok konfigurálása]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET ügyféloldali kódtár – referencia]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API-referencia]: http://msdn.microsoft.com/library/azure/dd179355



<!--HONumber=Jun16_HO2--->


