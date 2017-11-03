---
title: "A Xamarin Blob Storage használata |} Microsoft Docs"
description: "Az Azure Storage ügyféloldali kódtára a Xamarin segítségével a fejlesztők a saját felhasználói felülettel rendelkező iOS, Android és Windows Áruházbeli alkalmazások létrehozásához. Ez az oktatóanyag bemutatja, hogyan hozzon létre egy Azure Blob Storage tárolót használó alkalmazást a Xamarin segítségével."
services: storage
documentationcenter: xamarin
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 44cb845d-cf78-4942-95b8-952da4f9a2c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: c7b4d0e7d7d95f2e3f8c5a97b78c60c52cc862a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>A Xamarin Blob Storage használata
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Áttekintés
Xamarin-fejlesztők számára lehetővé teszi, hogy használatára a megosztott C# iOS, Android és Windows Áruházbeli alkalmazások létrehozása a natív felhasználói felületeket a kódbázis. Ez az oktatóanyag bemutatja, hogyan Azure Blob storage használata a Xamarin-alkalmazás. Ha azt szeretné, további Azure Storage-ról a kód előtt kapcsolatos információkért tekintse meg [Microsoft Azure Storage bemutatása](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Új Xamarin-alkalmazás létrehozása
Ebben az oktatóanyagban azt fogja létrehozni egy alkalmazást, amelynek célja az Android, iOS és Windows. Az alkalmazás egyszerűen tárolókat hozhat létre, és egy blob feltöltése a tárolóba. Windows rendszeren Visual Studio használatával, de a azonos learnings alkalmazhatók a Xamarin Studio segítségével macOS az alkalmazás létrehozásakor.

Kövesse az alábbi lépéseket az alkalmazás létrehozásához:

1. Ha még nem tette, töltse le és telepítse [Visual Studio Xamarin](https://www.xamarin.com/download).
2. Nyissa meg a Visual Studio, és hozzon létre egy üres alkalmazás (natív hordozható): **fájl > Új > Projekt > platformfüggetlen > üres App(Native Portable)**.
3. Kattintson a jobb gombbal a megoldás a Solution Explorer ablaktáblában, és válassza ki **NuGet-csomagok kezelése megoldáshoz**. Keresse meg **windowsazure.Storage kifejezésre** és telepítse a legújabb stabil verzióját az összes projektet a megoldásban.
4. Hozza létre, és futtatja a projektet.

Most rendelkeznie kell egy alkalmazás, amely lehetővé teszi egy gombra, ami növeli a számlálót.

## <a name="create-container-and-upload-blob"></a>Tároló létrehozása és feltöltése a blob
Ezután bontsa a `(Portable)` projektben fogja hozzáadni bizonyos kód futtatásával `MyClass.cs`. Ez a kód létrehoz egy tárolót, és feltölt egy blobot a tárolóba. `MyClass.cs`a következő hasonlóan kell kinéznie:

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

Ügyeljen arra, hogy a "your_account_name_here" és "your_account_key_here" cserélje a tényleges fiók nevét és a fiókkulcsot. 

Az iOS, Android és Windows Phone-projektek összes rendelkezik a hordozható projekt – azaz írhat összes megosztott kódot egy hivatkozást elhelyezni, és használja az összes projektben. A következő kódsort most már minden olyan projekthez indítására, előnyeinek kihasználása adhat hozzá:`MyClass.performBlobOperation()`

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
Most futtathatja az alkalmazást egy Android- vagy Windows Phone-emulátoron. Ez az alkalmazás egy iOS-emulátoron is futtatható, de ehhez az szükséges, Mac A konkrét utasításokat ebben az esetben olvassa el a dokumentációja [csatlakozik a Visual Studio Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Az alkalmazás futtatása után hoz létre a tároló `mycontainer` tárfiókba. A blob tartalmaznia kell `myblob`, amely rendelkezik a dokumentum szövegét, `Hello, world!`. Ennek segítségével ellenőrizheti a [Microsoft Azure Tártallózó](http://storageexplorer.com/).

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megismerte a platformfüggetlen alkalmazások létrehozása az Azure Storage Blob Storage egy forgatókönyv kifejezetten előtérbe használó Xamarin. Azonban végezhet sokkal több nem csak a Blob Storage a, hanem a tábla, a fájl és a Queue Storage. Vegye ki az alábbi cikkekből tudhat meg többet:

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-blobs.md)
* [Az Azure Files bemutatása](../files/storage-files-introduction.md)
* [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](../files/storage-dotnet-how-to-use-files.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]