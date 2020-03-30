---
title: Azure Windows Communication Foundation (WCF) hibrid helyszíni/felhőalapú alkalmazás (.NET) hibrid továbbítása | Microsoft dokumentumok
description: Megtudhatja, hogyan teheti elérhetővé a helyszíni WCF-szolgáltatásokat egy felhőbeli webalkalmazásnak az Azure Relay használatával
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: b86d535e4cbc275b3ee777d7c70146f7711c502c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212966"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>A helyszíni WCF-szolgáltatás elérhetővé tévő webhelye egy felhőbeli webalkalmazás számára az Azure Relay használatával

Ez a cikk azt mutatja be, hogyan készíthet felhőbeli hibridalkalmazást a Microsoft Azure és a Visual Studio használatával. Hozzon létre egy alkalmazást, amely több Azure-erőforrásokat használ a felhőben. Ez az oktatóanyag segít a következők megismerésén:

* Meglévő webszolgáltatás létrehozása vagy alakítása a használathoz egy webes megoldással.
* Az Azure Windows Communication Foundation (WCF) továbbító szolgáltatás használata adatok megosztására egy Azure-alkalmazás és egy máshol üzemeltetett webszolgáltatás között.

Az oktatóanyagban a következő feladatokat végezze el:

> [!div class="checklist"]
>
> * Telepítse az oktatóanyag előfeltételeit.
> * Tekintse át a forgatókönyvet.
> * Névtér létrehozása.
> * Hozzon létre egy helyszíni kiszolgálót.
> * Hozzon létre egy ASP .NET alkalmazást.
> * Futtassa helyben az alkalmazást.
> * Telepítse a webalkalmazást az Azure-ba.
> * Futtassa az alkalmazást az Azure-on.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2019-et használják.
* Azure SDK a .NET.Azure SDK for .NET. Telepítse az [SDK letöltések oldalon](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Hogyan segít az Azure Relay a hibrid megoldások terén?

Az üzleti megoldások általában egyéni kód és meglévő funkciók kombinációjából állnak. Az egyéni kód új és egyedi üzleti követelményekkel foglalkozik. A már meglévő megoldások és rendszerek meglévő funkciókat biztosítanak.

A megoldások tervezői elkezdték a felhőt használni a méretezési követelmények egyszerűbb kezelése és az alacsonyabb működési költségek érdekében. Ennek során úgy találják, hogy a meglévő szolgáltatási eszközök, amelyeket a megoldásaik építőköveként szeretnének használni, a vállalati tűzfalon belül vannak, és a felhőalapú megoldás nem érhető el könnyen. Számos belső szolgáltatás nem úgy épül fel vagy üzemeltethető, hogy könnyen elérhetővé lehessen tenni őket a vállalati hálózat szélén.

[Az Azure Relay](https://azure.microsoft.com/services/service-bus/) meglévő WCF-webszolgáltatásokat vesz igénybe, és biztonságosan hozzáférhetővé teszi ezeket a szolgáltatásokat a vállalati peremterületen kívül eső megoldások számára anélkül, hogy a vállalati hálózati infrastruktúrát tolakodó módosításokra lenne szükség. Ezek a Relay-szolgáltatások továbbra is a meglévő környezeten belül vannak tárolva, de átadják a bejövő munkamenetek és a kérések figyelését a felhőn tárolt Relay-szolgáltatásnak. Az Azure Relay is védi ezeket a szolgáltatásokat a jogosulatlan hozzáférés segítségével [megosztott hozzáférés (SAS)](../service-bus-messaging/service-bus-sas.md) hitelesítés.

## <a name="review-the-scenario"></a>A forgatókönyv áttekintése

Ebben az oktatóanyagban egy ASP.NET webhelyet hoz létre, amely lehetővé teszi a termékek listájának megtekintését a termékkészlet-oldalon.

![Forgatókönyv][0]

Az oktatóanyag feltételezi, hogy egy meglévő helyi rendszeren elérhetők a termékek adatai, és hogy az Azure Relay közvetítőn keresztül éri el ezt a rendszert. Egy egyszerű konzolalkalmazásban futó webszolgáltatás szimulálja ezt a helyzetet. Ez tartalmaz egy in-memória készlet termékek. Ezt a konzolalkalmazást futtathatja a saját számítógépén, és telepítheti a webes szerepkört az Azure-ba. Ezzel láthatja, hogy az Azure adatközpontban futó webes szerepkör hogyan hívja be a számítógépet. Ez a hívás akkor is megtörténik, ha a számítógép szinte biztosan legalább egy tűzfal és egy hálózati címfordítási (NAT) réteg mögött lesz.

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Az Azure-alkalmazások fejlesztésének megkezdése előtt töltse le az eszközöket és állítsa be a fejlesztési környezetet:

1. Telepítse az Azure SDK for .NET-et az SDK [letöltési oldaláról](https://azure.microsoft.com/downloads/).
1. A **.NET** oszlopban válassza ki a [visual studio](https://www.visualstudio.com) használni kívánt verzióját. Ez az oktatóanyag a Visual Studio 2019-et használja.
1. Amikor a rendszer a telepítő futtatását vagy mentését kéri, válassza a **Futtatás**lehetőséget.
1. A **Webplatform telepítőpárbeszédpanelén** válassza a **Telepítés** lehetőséget, és folytassa a telepítést.

Miután a telepítés befejeződött, akkor minden szükséges kezdeni, hogy dolgozzon ki az alkalmazást. Az SDK olyan eszközöket tartalmaz, amelyekkel könnyedén fejleszthet Azure-alkalmazásokat a Visual Studióban.

## <a name="create-a-namespace"></a>Névtér létrehozása

Az első lépés egy névtér létrehozása és egy [SAS-kulcs](../service-bus-messaging/service-bus-sas.md) beszerzése. A névtér alkalmazáshatárt biztosít a továbbító szolgáltatáson keresztül elérhetővé tett minden egyes alkalmazásszámára. A rendszer automatikusan létrehoz egy SAS-kulcsot a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és a SAS-kulcs kombinációja biztosítja az Azure hitelesítő adatait az alkalmazáshoz való hozzáférés hitelesítéséhez.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Helyszíni kiszolgáló létrehozása

Először hozzon létre egy szimulált helyszíni termékkatalógus-rendszert.  Ez a projekt egy Visual Studio-konzolalkalmazás, és az [Azure Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) használja a Service Bus-kódtárak és konfigurációs beállítások belefoglalása érdekében. <a name="create-the-project"></a>

1. Indítsa el a Microsoft Visual Studio alkalmazást rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, és válassza a **Futtatás rendszergazdaként parancsot.**
1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Keretrendszer)** lehetőséget a C# értékhez, majd a **Tovább**gombot.
1. Nevezze el a *ProductsServer projektet,* és válassza **a Létrehozás lehetőséget.**

   ![Az új projekt konfigurálása][11]

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsServer** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**
1. Válassza a **Tallózás**lehetőséget, majd keresse meg a **WindowsAzure.ServiceBus**elemet. Válassza **a Telepítés**lehetőséget, és fogadja el a használati feltételeket.

   ![NuGet csomag kiválasztása][13]

   A szükséges ügyfélszerelvények most már hivatkoznak.

1. Adjon egy új osztályt a termékszerződéshez. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsServer** projektre, és válassza az**Osztály** **hozzáadása** > parancsot.
1. A **Név mezőbe**írja be a *ProductsContract.cs* nevet, és válassza a **Hozzáadás lehetőséget.**

Hajtsa végre a következő kódmódosításokat a megoldáson:

1. A *ProductsContract.cs* fájlban cserélje le a névtér definícióját a következő kódra, amely meghatározza a szolgáltatás szerződését.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. A *Program.cs*cserélje le a névtérdefiníciót a következő kódra, amely hozzáadja a profilszolgáltatást és az állomást.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. A **Megoldáskezelőben**kattintson duplán az **App.config** fájlra a fájl Visual Studio-szerkesztőben való megnyitásához. Az `<system.ServiceModel>` elem alján, de még `<system.ServiceModel>`mindig belül, adja hozzá a következő XML-kódot. Ügyeljen arra, `yourServiceNamespace` hogy cserélje le a `yourKey` névtér nevét, és a Portálról korábban beolvasott SAS-kulccsal:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Az által `transportClientEndpointBehavior` okozott hiba csak egy figyelmeztetés, és nem blokkoló probléma ebben a példában.

1. Az *App.config*fájlban `<appSettings>` az elemben cserélje le a kapcsolati karakterlánc értékét a portálról korábban beszerzett kapcsolati karakterláncra.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Válassza a Ctrl+Shift+B vagy a **BuildSolution** > **összeállítása** lehetőséget az alkalmazás létrehozásához és az eddigi munka pontosságának ellenőrzéséhez.

## <a name="create-an-aspnet-application"></a>ASP.NET-alkalmazás létrehozása

Ebben a szakaszban egy egyszerű ASP.NET alkalmazást hoz létre, amely megjeleníti a termékszolgáltatásból beolvasott adatokat.

### <a name="create-the-project"></a>A projekt létrehozása

1. Győződjön meg arról, hogy a Visual Studio rendszergazdaként fut.
1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza ASP.NET Web Application (.NET Framework) (C# ( Web Application **(.NET Framework)** lehetőséget a C# számára, majd a **Tovább**gombot.
1. Nevezze el a *projectProductsPortal* nevet, és válassza **a Létrehozás lehetőséget.**
1. Az **Új ASP.NET webalkalmazás létrehozása**csoportban válassza az **MVC** lehetőséget, és válassza a Módosítás **csoportban**a **Módosítás** lehetőséget.

   ![ASP .NET webalkalmazás kiválasztása][16]

1. A **Hitelesítés módosítása csoportban**válassza a **Nincs hitelesítés** lehetőséget, majd kattintson az **OK gombra.** Ebben az oktatóanyagban olyan alkalmazást telepít, amelynem szükséges a felhasználónak a bejelentkezéshez.

    ![Hitelesítés megadása][18]

1. Az **Új ASP.NET webalkalmazás létrehozása**területen válassza a **Létrehozás** lehetőséget az MVC alkalmazás létrehozásához.
1. Azure-erőforrások konfigurálása egy új webalkalmazáshoz. Kövesse a [Webalkalmazás közzététele](../app-service/app-service-web-get-started-dotnet-framework.md#launch-the-publish-wizard)című részben leírt lépéseket. Ezután térjen vissza az oktatóanyaghoz, és folytassa a következő lépéssel.
1. A **Megoldáskezelőben**kattintson a jobb gombbal **a Modellek** elemre, majd válassza az Osztály **hozzáadása** > **parancsot.**
1. Nevezze el az *osztályt Product.cs,* majd válassza **a Hozzáadás**lehetőséget.

    ![Termékmodell létrehozása][17]

### <a name="modify-the-web-application"></a>A webalkalmazás módosítása

1. A Visual Studio *Product.cs* fájljában cserélje le a meglévő névtérdefiníciót a következő kódra:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. A **Megoldáskezelőben**bontsa ki a **Vezérlők csomópontot,** majd kattintson duplán **a HomeController.cs** elemre a fájl Visual Studióban való megnyitásához.
1. A *HomeController.cs*a meglévő névtér-definíciót a következő kóddal helyettesítse:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. A **Megoldáskezelőben**bontsa ki a**Megosztott** **nézetek** > csomópontot, majd kattintson duplán **a _Layout.cshtml** fájlra a Fájl Visual Studio-szerkesztőben való megnyitásához.
1. Változtassa meg az `My ASP.NET Application` összes előfordulását *a Northwind kereskedők termékek*.
1. Távolítsa `Home`el `About`a `Contact` , és hivatkozásokat. A következő példában törölje a kiemelt kódot.

    ![A létrehozott listaelemek törlése][41]

1. A **Megoldáskezelőben**bontsa ki a **Nézetek** > **kezdőlapját,** majd kattintson duplán az **Index.cshtml** fájlra a Fájl Visual Studio-szerkesztőben való megnyitásához. Cserélje le a fájl teljes tartalmát a következő kódra:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Az eddigi munka pontosságának ellenőrzéséhez válassza a Ctrl+Shift+B billentyűkombinációt a projekt létrehozásához.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa az alkalmazást a működése ellenőrzéséhez.

1. Győződjön meg arról, hogy a **ProductsPortal** az aktív projekt. Kattintson a jobb gombbal a projekt nevére a **Megoldáskezelőben,** és válassza **a Beállítás indítási projektként parancsot.**
1. A Visual Studio alkalmazásban válassza az F5 lehetőséget.

Az alkalmazásának meg kell jelennie egy böngészőben.

![Webalkalmazás][21]

## <a name="put-the-pieces-together"></a>Az egyes alkotórészek összeállítása teljes egésszé

A következő lépés, hogy a helyszíni termékkiszolgálót az ASP.NET-alkalmazáshoz csatlakoztassuk.

1. Ha még nincs megnyitva, a Visual Studióban nyissa meg a ASP.NET [alkalmazás létrehozása](#create-an-aspnet-application) szakaszban létrehozott **ProductsPortal** projektet.
1. Hasonlóan a helyszíni [kiszolgáló létrehozása](#create-an-on-premises-server) szakaszlépéséhez hasonlóan adja hozzá a NuGet csomagot a projekthivatkozásokhoz. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsPortal** projektre, majd válassza **a NuGet-csomagok kezelése parancsot.**
1. Keressen a *WindowsAzure.ServiceBus* kifejezésre, és válassza ki az **WindowsAzure.ServiceBus** elemet. Ezután fejezze be a telepítést, és zárja be ezt a párbeszédpanelt.
1. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsPortal** projektre, majd válassza a Meglévő elem **hozzáadása** > **parancsot.**
1. Keresse meg a *ProductsContract.cs* fájlt a **ProductsServer** konzolprojektben. Jelölje ki *ProductsContract.cs.* Jelölje ki a **Hozzáadás**gomb melletti lefelé mutató nyilat, majd válassza **a Hozzáadás hivatkozásként**lehetőséget.

   ![Hozzáadás hivatkozásként][24]

1. Ezután nyissa meg a *HomeController.cs* fájlt a Visual Studio szerkesztőjében, és a névtér definícióját cserélje az alábbi kódra. Ügyeljen arra, `yourServiceNamespace` hogy cserélje le a nevét `yourKey` a szolgáltatás névtér, és a SAS-kulcs. Ez a kód lehetővé teszi, hogy az ügyfél hívja a helyszíni szolgáltatás, a hívás eredményét visszaadva.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsPortal** megoldásra. Győződjön meg arról, hogy a jobb gombbal a megoldásra kattint, nem a projektre. Válassza a**Meglévő projekt** **hozzáadása** > lehetőséget.
1. Keresse meg a **ProductsServer** projektet, és kattintson duplán a *ProductsServer.csproj* megoldásfájlra annak hozzáadásához.
1. **A ProductsServer** szolgáltatásnak futnia kell, hogy megjelenítse az adatokat a **ProductsPortal**webhelyen. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsPortal** megoldásra, és válassza a **Tulajdonságok parancsot** a **tulajdonságlapok**megjelenítéséhez .
1. Válassza a Common Properties Startup Project **(Általános tulajdonságok** > **indítási projekt) lehetőséget,** majd a **Több indítási projekt**lehetőséget. Győződjön meg arról, hogy **a ProductsServer** és **a ProductsPortal** ebben a sorrendben jelenik meg, és hogy mindkettőre **vonatkozóan** a művelet a **Start**.

      ![Több indítási projekt][25]

1. Válassza a **Közös tulajdonságok** > **projektfüggőségek lehetőséget** a bal oldalon.
1. Projektek **esetén**válassza a **ProductsPortal**lehetőséget. Győződjön meg arról, hogy a **ProductsServer** ki van jelölve.

    ![Projektfüggőségek][26]

1. Projektek **esetén**válassza a **ProductsServer**lehetőséget. Győződjön meg arról, hogy a **ProductsPortal** nincs kijelölve, majd a módosítások mentéséhez válassza az **OK gombot.**

## <a name="run-the-project-locally"></a>A projekt helyi futtatása

Az alkalmazás helyi teszteléséhez a Visual Studio programban válassza az F5 lehetőséget. A helyszíni kiszolgálónak, **a ProductsServernek**kell először elindulnia, majd a **ProductsPortal** alkalmazásnak egy böngészőablakban kell elindulnia. Ezúttal láthatja, hogy a termékkészlet felsorolja a helyszíni termékszolgáltatásból beolvasott adatokat.

![Webalkalmazás][10]

Válassza a **Frissítés** lehetőséget a **ProductsPortal** lapon. A lap minden frissítésekor megjelenik a kiszolgálóalkalmazás, amely `GetProducts()` üzenetet jelenít meg a **ProductsServer** szolgáltatásból.

Zárja be mindkét alkalmazást, mielőtt továbblépne a következő szakaszra.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>A ProductsPortal projekt telepítése egy Azure-webalkalmazásba

A következő lépés az Azure Web app **ProductsPortal** előtér-közzététele:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a **ProductsPortal** projektre, és válassza **a Közzététel parancsot.** A **Közzététel** lapon válassza a **Közzététel**lehetőséget.

   > [!NOTE]
   > Előfordulhat, hogy egy hibaüzenet jelenik meg a böngészőablakban, amikor a **ProductsPortal** webprojekt automatikusan elindul a telepítés után. Emiatt nem kell aggódnia, ugyanis az okozza, hogy a **ProductsServer** alkalmazás még nem fut.
   >

1. Másolja az üzembe helyezett webalkalmazás URL-címét. Később szüksége lesz az URL-címre. Ezt az URL-címet a Visual Studio **Azure App Service-tevékenység** ablakából is leszerezheti:

   ![A telepített alkalmazás URL-címe][9]

1. Zárja be a böngészőablakot a futó alkalmazás leállításához.

<a name="set-productsportal-as-web-app"></a>Mielőtt az alkalmazást a felhőben futtatja, gondoskodnia kell arról, hogy a **ProductsPortal** webalkalmazásként a Visual Studio-ból induljön el.

1. A Visual Studio alkalmazásban kattintson a jobb gombbal a **ProductsPortal** projektre, és válassza a **Tulajdonságok parancsot.**
1. Válassza **a Web**lehetőséget . A **Művelet indítása**csoportban válassza **az URL indítása**lehetőséget. Adja meg a korábban telepített webalkalmazás URL-címét ebben a `https://productsportal20190906122808.azurewebsites.net/`példában.

    ![Kezdő URL-cím][27]

1. Válassza az > **Összes** **fájlmentése**lehetőséget.
1. Válassza a Build > **Rebuild Solution (Létrehozási megoldás) lehetőséget.** **Build**

## <a name="run-the-application"></a>Az alkalmazás futtatása

Válassza az F5 lehetőséget az alkalmazás létrehozásához és futtatásához. A helyszíni kiszolgálónak, amely a **ProductsServer** konzolalkalmazás, először el kell kezdenie, majd a **ProductsPortal** alkalmazásnak egy böngészőablakban kell kezdődnie, ahogy az itt látható:

   ![A webalkalmazás futtatása az Azure-ban][1]

A termékleltár felsorolja a helyszíni termékszolgáltatásból beolvasott adatokat, és megjeleníti ezeket az adatokat a webalkalmazásban. Ellenőrizze az URL-címet, és győződjön meg arról, hogy a **ProductsPortal** Azure-webalkalmazás fut a felhőben.

   > [!IMPORTANT]
   > A **ProductsServer** konzolalkalmazásnak futnia kell, és képesnek kell lennie biztosítani az adatokat a **ProductsPortal** alkalmazás számára. Ha a böngésző hibát jelenít meg, várjon még néhány másodpercet, amíg a **ProductsServer** betölti és megjeleníti a következő üzenetet, majd frissítse a böngészőt.
   >

A böngészőben frissítse a **ProductsPortal** lapot. A lap minden frissítésekor megjelenik a kiszolgálóalkalmazás, amely `GetProducts()` üzenetet jelenít meg a **ProductsServer** szolgáltatásból.

![Frissített kimenet][38]

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Helyszíni WCF-szolgáltatás közzététele egy hálózaton kívüli WCF-ügyfélen](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
