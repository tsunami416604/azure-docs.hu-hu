---
title: Az Object (blob) tároló használata a Xamarin | Microsoft Docs
description: A Xamarin készült Azure Storage ügyféloldali kódtára lehetővé teszi, hogy a fejlesztők iOS-, Android-és Windows áruházbeli alkalmazásokat hozzanak létre natív felhasználói felületekkel. Ez az oktatóanyag bemutatja, hogyan használható a Xamarin az Azure Blob Storage-t használó alkalmazások létrehozásához.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726343"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>A Blob Storage használata a Xamarin

A Xamarin lehetővé teszi a fejlesztők számára C# , hogy a natív felhasználói felülettel rendelkező iOS-, Android-és Windows áruházbeli alkalmazásokat közös kód használatával hozzanak létre. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Blob Storage-t egy Xamarin-alkalmazással. Ha többet szeretne megtudni az Azure Storage-ról, a kód kimerülése előtt olvassa el a [Microsoft Azure Storage bemutatása](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)című témakört.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Új Xamarin-alkalmazás létrehozása

Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely az Android, az iOS és a Windows rendszert célozza meg. Ez az alkalmazás egyszerűen létrehoz egy tárolót, és feltölt egy blobot ebbe a tárolóba. A Visual studiót a Windowson fogjuk használni, de ugyanezek a betanulások is alkalmazhatók, ha a Xamarin Studio használatával macOS rendszeren is létrehoz egy alkalmazást.

Az alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Ha még nem tette meg, töltse le és telepítse [a Xamarin for Visual Studio](https://www.xamarin.com/download)alkalmazást.
2. Nyissa meg a Visual studiót, és hozzon létre egy üres alkalmazást (natív hordozható): **Fájl > új > projekt > platformfüggetlen > üres alkalmazás (natív hordozható)** .
3. Kattintson a jobb gombbal a megoldásra a Megoldáskezelő ablaktáblán, és válassza a **megoldás NuGet-csomagok kezelése**lehetőséget. Keressen rá a **WindowsAzure. Storage** kifejezésre, és telepítse a legújabb stabil verziót a megoldás összes projektje számára.
4. Hozza létre és futtassa a projektet.

Ekkor egy olyan alkalmazásra van szüksége, amely lehetővé teszi, hogy rákattint egy olyan gombra, amely növeli a számlálót.

## <a name="create-container-and-upload-blob"></a>Tároló létrehozása és blob feltöltése

Ezután a `(Portable)` projekt alatt hozzá kell adnia egy kódot a `MyClass.cs`következőhöz:. Ez a kód létrehoz egy tárolót, és feltölt egy blobot ebbe a tárolóba. `MyClass.cs`a következőhöz hasonlóan kell kinéznie:

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

Ügyeljen arra, hogy a "your_account_name_here" és a "your_account_key_here" kifejezést cserélje le a tényleges fióknév és a fiók kulcsaként.

Az iOS-, Android-és Windows Phone-telefon-projektjei mind a hordozható projektre hivatkoznak, ami azt jelenti, hogy az összes megosztott kód egy helyen írható, és az összes projektben használható. Most már hozzáadhatja az alábbi kódrészletet az egyes projektekhez, így kihasználhatja az előnyeit:`MyClass.performBlobOperation()`

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

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp. iOS > ViewController.cs

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

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp. WinPhone > Főoldal. XAML > MainPage.xaml.cs

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

Most már futtathatja az alkalmazást egy Android-vagy Windows Phone-telefon-emulátorban. Ezt az alkalmazást iOS-emulátorban is futtathatja, de ehhez Mac rendszer szükséges. Az ehhez szükséges részletes útmutatásért olvassa el a [Visual Studio Mac-](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/) hez való csatlakoztatásának dokumentációját.

Az alkalmazás futtatása után létrehozza a `mycontainer` tárolót a Storage-fiókban. Tartalmaznia kell a blobot `myblob`, amely a `Hello, world!`szöveggel rendelkezik. Ezt a [Microsoft Azure Storage Explorer](https://storageexplorer.com/)használatával ellenőrizheti.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre többplatformos alkalmazást az Azure Storage-t használó Xamarin, különös tekintettel a Blob Storage egyik forgatókönyvére. Ugyanakkor sokkal többet is megtehet, ha nem csak Blob Storage, hanem a tábla, a fájl és a Queue Storage is. További információért olvassa el a következő cikkeket:

* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-blobs.md)
* [Bevezetés a Azure Filesba](../files/storage-files-introduction.md)
* [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](../files/storage-dotnet-how-to-use-files.md)
* [Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
