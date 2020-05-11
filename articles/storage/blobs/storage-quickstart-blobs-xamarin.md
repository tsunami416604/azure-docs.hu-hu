---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – Xamarin'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtárat a 12-es verzióban a Xamarin használatával tárolók és Blobok (Object) tárolók létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a mobileszközön, és hogyan listázhatja az összes blobot egy tárolóban.
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e0845e7cdc2ce6dc57ed5a18d263f117f0c2005c
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006243"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtár V12 és Xamarin

Ismerkedjen meg az Azure Blob Storage-beli Xamarin-vel. Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

Használja az Azure Blob Storage ügyféloldali kódtárat a Xamarin:

* Tároló létrehozása
* BLOB feltöltése az Azure Storage-ba
* Egy tároló összes blobjának listázása
* A blob letöltése az eszközre
* Tároló törlése

[API-referenciák dokumentációs](/dotnet/api/azure.storage.blobs) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [-csomagjához (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs) | tartozó[minta](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* A Visual Studio és a [Mobile Development for .net](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows) számítási feladat telepítve vagy [Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Beállítás
    
Ez a szakasz végigvezeti a projekt előkészítésének folyamatán az Azure Blob Storage-beli Xamarin-vel való együttműködéshez.
    
### <a name="create-the-project"></a>A projekt létrehozása

1. Nyissa meg a Visual studiót, és hozzon létre egy üres Forms alkalmazást.
1. Név: BlobQuickstartV12

### <a name="install-the-package"></a>A csomag telepítése

1. Kattintson a jobb gombbal a megoldásra a Megoldáskezelő ablaktáblán, és válassza a **megoldás NuGet-csomagok kezelése**lehetőséget.
1. Keressen rá az **Azure. Storage. Blobok** kifejezésre, és telepítse a legújabb stabil verziót a megoldás összes projektje számára.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A **BlobQuickstartV12** könyvtárából:

1. Nyissa meg a *Főoldal. XAML* fájlt a szerkesztőben
1. Távolítson el mindent `<ContentPage></ContentPage>` az elemek között, és cserélje le az alábbira:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

Használja az alábbi .NET-osztályokat a következő erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): a `BlobContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): az `BlobDownloadInfo` osztály a blob letöltésekor visszaadott tulajdonságokat és tartalmat jelöli.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan hajthatja végre a következő feladatokat a .NET-hez készült Azure Blob Storage ügyféloldali kódtára Xamarin. Forms alkalmazásban:

* [Osztály szintű változók létrehozása](#create-class-level-variables)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [A tárolóban lévő blobok listázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="create-class-level-variables"></a>Osztály szintű változók létrehozása

Az alábbi kód több Class szintű változót deklarál. Szükségük volt az Azure Blob Storage-hoz való kommunikációra a minta további részében.

Ezek a tárolási fiók a [Storage-kapcsolódási karakterlánc konfigurálása](#configure-your-storage-connection-string) szakaszban beállított kapcsolódási karakterláncán kívül vannak.

Adja hozzá ezt a kódot az *MainPage.XAML.cs* fájlban található osztály szintű változókhoz:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy GUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) osztály egy példányát. Ezután hívja meg a [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metódust a tároló létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a *MainPage.XAML.cs* -fájlhoz:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Létrehoz egy `MemoryStream` szöveget.
1. Feltölti a szöveget egy Blobba úgy, hogy meghívja a [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) osztály [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) függvényét, és átadja azt az osztály szintjének változót `MemoryStream` és a szöveget is. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

Adja hozzá ezt a kódot a *MainPage.XAML.cs* -fájlhoz:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő Blobok listázása a [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a *MainPage.XAML.cs* -fájlhoz:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) metódus meghívásával. A példában szereplő kód először `Stream` a blobot másolja át a `MemoryStream` -ba, majd `StreamReader` a szöveg megjelenítéséhez.

Adja hozzá ezt a kódot a *MainPage.XAML.cs* -fájlhoz:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat a teljes tároló törlésével a [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)használatával.

Az alkalmazás először kéri a megerősítést, mielőtt törli a blobot és a tárolót. Ez jó eséllyel ellenőrizhető, hogy az erőforrások megfelelően lettek-e létrehozva, mielőtt törölné őket.

Adja hozzá ezt a kódot a *MainPage.XAML.cs* -fájlhoz:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazás indításakor először létrehozza a tárolót, ahogy az megjelenik. Ezután a gombokra kattintva feltöltheti, listázhatja, letöltheti a blobokat, és törölheti a tárolót.

Az alkalmazás futtatása Windows rendszeren nyomja meg az F5 billentyűt. Az alkalmazás Mac gépen való futtatásához nyomja le a cmd + ENTER billentyűkombinációt.

Az alkalmazás minden művelet után ír a képernyőre. Az alkalmazás kimenete az alábbi példához hasonló:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

A tisztítási folyamat megkezdése előtt ellenőrizze, hogy a blob tartalmának kimenete megfelel-e a feltöltött értéknek.

Miután ellenőrizte az értékeket, erősítse meg a tároló törlésére és a bemutató befejezésére vonatkozó kérést.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat az Azure Blob Storage ügyféloldali Library V12 és a Xamarin használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Xamarin minta](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Az oktatóanyagok, minták, gyors indítás és egyéb dokumentációk az [Azure-ban Mobile Developers szolgáltatásban](/azure/mobile-apps)találhatók.
* A Xamarin szolgáltatással kapcsolatos további tudnivalókért tekintse meg a [Xamarin első lépéseivel foglalkozó](/xamarin/get-started/)témakört.