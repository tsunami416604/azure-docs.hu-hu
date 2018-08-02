---
title: A Xamarin objektumtárat (Blobtárat) storage használata |} A Microsoft Docs
description: Az Azure Storage ügyféloldali kódtára a Xamarin lehetővé teszi a fejlesztők számára az iOS, Android és Windows Store apps és a natív felhasználói felületek. Ez az oktatóanyag bemutatja, hogyan hozhat létre alkalmazást az Azure Blob storage a Xamarin segítségével.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b35bec31035c0219bf34a31cb34e20f7dc3a72c5
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397029"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>A Xamarin Blob Storage használata

Xamarin-fejlesztők számára lehetővé teszi, hogy egy megosztott C# használatával hozhat létre iOS, Android és Windows Store apps és a natív felhasználói felületek kódbázis. Az oktatóanyag bemutatja, hogyan használható az Azure Blob storage egy Xamarin-alkalmazással. Ha szeretne további Azure Storage-ról a kód áttekintése előtt kapcsolatos információkért tekintse meg [a Microsoft Azure Storage bemutatása](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Egy új Xamarin-alkalmazás létrehozása
Ebben az oktatóanyagban azt hoz létre egy alkalmazást, amely az Android, iOS és Windows célozza. Ez az alkalmazás egyszerűen hozzon létre egy tárolót, és a blob feltöltése a tárolóba. Fogjuk használni a Visual Studio a Windows, de az azonos tapasztalatainkat is alkalmazható, amikor egy alkalmazást a Xamarin Studiót használja a macOS rendszeren hoz létre.

Kövesse az alábbi lépéseket az alkalmazás létrehozása:

1. Ha még nem tette, töltse le és telepítse [Xamarin for Visual Studio](https://www.xamarin.com/download).
2. Nyissa meg a Visual Studiót, és hozzon létre egy üres alkalmazás (natív hordozható): **File > New > Project > többplatformos > üres App(Native Portable)**.
3. Kattintson a jobb gombbal a megoldás a Solution Explorer panelen, és válassza ki **NuGet-csomagok kezelése megoldáshoz**. Keresse meg **WindowsAzure.Storage** , és telepítse a legújabb stabil verzióra az összes projektet a megoldásban.
4. Hozhat létre, és futtassa a projektet.

Ekkor rendelkezik olyan alkalmazás, amely lehetővé teszi, hogy egy gombra, amely növeli a számlálót.

## <a name="create-container-and-upload-blob"></a>Tároló létrehozása és feltöltése a blob
A következő a `(Portable)` projektben adunk hozzá néhány kódot `MyClass.cs`. Ez a kód létrehoz egy tárolót, és feltölt egy blobot a tárolóba. `MyClass.cs` a következőhöz hasonlóan kell kinéznie:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Ellenőrizze, hogy "your_account_name_here" és "your_account_key_here" cserélje a tényleges fiók nevét és kulcsát. 

Az iOS, Android és Windows Phone-projektek minden hivatkoznak a hordozható projekt – azaz írhat összes megosztott kódot egy helyezze el, és a projektek összes vele. Most már minden projekthez, hogy éljen adja hozzá a következő kódsort: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása
Most már futtathatja az alkalmazás egy Android vagy Windows Phone-emulátoron. Ez az alkalmazás egy iOS-emulátorban is futtathatja, de ehhez az szükséges, Mac Az információk arra vonatkozóan, hogyan teheti ezt, kérjük, olvassa el a dokumentációban [egy Mac számítógéphez való csatlakoztatásával a Visual Studio](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Az alkalmazás futtatása után, hozza létre a tároló `mycontainer` a Storage-fiókban. Tartalmaznia kell a blob `myblob`, a szöveg, amelynek `Hello, world!`. Ennek segítségével ellenőrizheti a [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan az Azure Storage, Blob Storage-ban egy forgatókönyv kifejezetten összpontosító használó Xamarin többplatformos alkalmazások létrehozásához. Azonban megteheti sokkal nem csak Blob Storage-tárolókat, hanem a Table, File és Queue Storage. Tekintse meg a további a következő cikkeket:

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-blobs.md)
* [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md)
* [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](../files/storage-dotnet-how-to-use-files.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]