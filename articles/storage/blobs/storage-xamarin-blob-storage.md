---
title: Hogyan kell használni objektum (Blob) tároló Xamarin | Microsoft dokumentumok
description: A Xamarin Hoz készült Azure Storage ügyfélkódtár lehetővé teszi a fejlesztők számára, hogy iOS- és Android- és Windows Áruházbeli alkalmazásokat hozzanak létre a natív felhasználói felületeikkel. Ez az oktatóanyag bemutatja, hogyan hozhat létre a Xamarin alkalmazást az Azure Blob storage-t használó alkalmazás.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726343"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>A Blob Storage használata a Xamarintól

A Xamarin lehetővé teszi a fejlesztők számára, hogy megosztott C# kódbázist használjanak iOS, Android és Windows Áruházbeli alkalmazások létrehozásához a natív felhasználói felületekkel. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Blob storage-t egy Xamarin-alkalmazással. Ha szeretne többet megtudni az Azure Storage-ról, mielőtt belemerülne a kódba, olvassa el a Bevezetés a [Microsoft Azure Storage-ba című témakört.](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Új Xamarin alkalmazás létrehozása

Ebben az oktatóanyagban egy androidos, iOS-es és Windows-os célokat szolgáló alkalmazást hozunk létre. Ez az alkalmazás egyszerűen hozzon létre egy tárolót, és töltsön fel egy blobot ebbe a tárolóba. A Windows visual Studio-t fogjuk használni, de ugyanazok a tanulságok alkalmazhatók, amikor macOS rendszeren a Xamarin Studio használatával hoz létre alkalmazást.

Az alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Ha még nem tette meg, töltse le és telepítse [a Xamarin alkalmazást a Visual Studio alkalmazásba.](https://www.xamarin.com/download)
2. Nyissa meg a Visual Studio alkalmazást, és hozzon létre egy üres alkalmazást (natív hordozható): **A fájl > új > Project > cross-platform > blank app (natív hordozható)**.
3. Kattintson a jobb gombbal a megoldásra a Megoldáskezelő ablaktáblában, és válassza **a NuGet csomagok kezelése a megoldáshoz parancsot.** Keresse meg a **WindowsAzure.Storage alkalmazást,** és telepítse a legújabb stabil verziót a megoldás összes projektjére.
4. Készítse el és futtassa a projektet.

Most már rendelkeznie kell egy alkalmazással, amely lehetővé teszi, hogy egy olyan gombra kattintson, amely egy számlálón növekszik.

## <a name="create-container-and-upload-blob"></a>Tároló létrehozása és feltöltési blob

Ezután a `(Portable)` projekt alatt hozzáad néhány `MyClass.cs`kódot a hoz. Ez a kód létrehoz egy tárolót, és feltölt egy blobot ebbe a tárolóba. `MyClass.cs`a következőkre kell hasonlítania:

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

Győződjön meg arról, hogy a "your_account_name_here" és a "your_account_key_here" szót a tényleges fióknévre és a fiókkulcsra cseréli.

Az iOS, Android és Windows Phone-projektek mind hivatkoznak a hordozható projektre , ami azt jelenti, hogy az összes megosztott kódot egy helyen írhatja, és az összes projektben használhatja. Mostantól a következő kódsort is hozzáadhatja az egyes projektekhez, hogy elkezdhesse kihasználni a példát:`MyClass.performBlobOperation()`

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
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

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

Most már futtathatja ezt az alkalmazást androidos vagy Windows Phone-emulátorban. Ezt az alkalmazást iOS-emulátorban is futtathatja, de ehhez Mac-re lesz szükség. Ennek módjáról a [Visual Studio Mac számítógéphez való csatlakoztatásának dokumentációjában](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) tájékozódhat.

Miután futtatta az alkalmazást, `mycontainer` létrehozza a tárolót a storage-fiókjában. Tartalmaznia kell a `myblob`blob, , `Hello, world!`amely a szöveget, . Ezt a [Microsoft Azure Storage Explorer](https://storageexplorer.com/)segítségével ellenőrizheti.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy platformfüggetlen alkalmazást a Xamarinban, amely az Azure Storage-t használja, és kifejezetten a Blob Storage egyik forgatókönyvére összpontosít. Azonban nem csak a Blob Storage, hanem a Table, File és Queue Storage segítségével is sokkal többet tehet. Kérjük, nézze meg a következő cikkeket, hogy többet:

* [Get started with Azure Blob Storage using .NET (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel)](storage-dotnet-how-to-use-blobs.md)
* [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md)
* [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](../files/storage-dotnet-how-to-use-files.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Ismerkedés az Azure Queue storage használatával .](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
