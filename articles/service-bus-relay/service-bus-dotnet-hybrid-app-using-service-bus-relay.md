---
title: "Azure WCF Relay hibrid helyszíni/felhőbeli alkalmazás (.NET) | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan hozhat létre helyszíni/felhőbeli .NET-hibridalkalmazást az Azure WCF Relay használatával."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: 77bb769a094c2a619c0c75363e23ae3ee561c1e4
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Helyszíni/felhőbeli .NET-hibridalkalmazás az Azure WCF Relay használatával

Ez a cikk azt mutatja be, hogyan készíthet felhőbeli hibridalkalmazást a Microsoft Azure és a Visual Studio használatával. Az oktatóanyagban feltételezzük, hogy nincs korábbi tapasztalata az Azure használatával kapcsolatban. 30 percen belül olyan alkalmazással rendelkezhet, amely több, a felhőben működő Azure-erőforrást is használ.

Az oktatóanyagban érintett témák köre:

* Meglévő webszolgáltatás létrehozása vagy alakítása a használathoz egy webes megoldással.
* Az Azure WCF Relay szolgáltatás használata az Azure-alkalmazások és a máshol tárolt webszolgáltatások közötti adatmegosztásra.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Hogyan segít az Azure Relay a hibrid megoldások terén?

Az üzleti megoldások általában egyéni kódok kombinációjából állnak, amelyeket az új és egyedi üzleti követelmények és már meglévő megoldások és rendszerek által szolgáltatott létező funkciók kezeléséhez írtak.

A megoldások tervezői elkezdték a felhőt használni a méretezési követelmények egyszerűbb kezelése és az alacsonyabb működési költségek érdekében. Mindeközben azt vették észre, hogy a megoldásaik építőelemeként használni kívánt meglévő szolgáltatási eszközök a vállalati tűzfalon belül vannak, és a felhőalapú megoldással nehéz elérni ezeket. Számos belső szolgáltatás nem úgy van felépítve vagy tárolva, hogy könnyen elérhető legyen vállalati hálózat peremén.

Az [Azure Relay](https://azure.microsoft.com/services/service-bus/) azon használati esethez lett tervezve, amelynek során a meglévő Windows Communication Foundation- (WCF-) webszolgáltatásokat biztonságosan elérhetik a szervezeti hálózaton kívüli megoldások anélkül, hogy zavaró módosításokat kellene végezni a vállalati hálózat infrastruktúráján. Ezek a Relay-szolgáltatások továbbra is a meglévő környezeten belül vannak tárolva, de átadják a bejövő munkamenetek és a kérések figyelését a felhőn tárolt Relay-szolgáltatásnak. Az Azure Relay ezeket a szolgáltatásokat [közös hozzáférésű jogosultságkód- (SAS-)](../service-bus-messaging/service-bus-sas.md) hitelesítéssel a jogosulatlan hozzáféréssel szemben is védi.

## <a name="solution-scenario"></a>A megoldás forgatókönyve
Az oktatóanyag során létrehoz egy ASP.NET-webhelyet, amelyen láthatja a termékleltár oldalán a termékek listáját.

![][0]

Az oktatóanyag feltételezi, hogy egy meglévő helyi rendszeren elérhetők a termékek adatai, és hogy az Azure Relay közvetítőn keresztül éri el ezt a rendszert. Ezt egy olyan webszolgáltatás szimulálja, amely egyszerű konzolalkalmazásként fut, és a termékek memóriában szereplő készletére épül. Ezt a konzolalkalmazást a saját számítógépén futtathatja, és a webes szerepkört az Azure-ban telepítheti. Így láthatja, hogy az Azure adatközpontjában futó webes szerepkör valójában a számítógéphez intéz hívást, bár a számítógép szinte biztosan legalább egy tűzfal és egy hálózati címfordítási (NAT-) réteg mögött található.

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Az Azure-alkalmazások fejlesztésének megkezdése előtt töltse le az eszközöket és állítsa be a fejlesztési környezetet:

1. Telepítse az Azure SDK for .NET-et az SDK [letöltési oldaláról](https://azure.microsoft.com/downloads/).
2. A **.NET** oszlopban kattintson a használt [Visual Studio](http://www.visualstudio.com)-verzióra. A jelen oktatóanyagban szereplő lépések a Visual Studio 2017 verzión alapulnak.
3. A telepítő futtatásának vagy mentésének kérdésére válaszolva kattintson a **Futtatás** gombra.
4. A **Webplatform-telepítőben** kattintson a **Telepítés** gombra, és folytassa a telepítést.
5. A telepítés végén az alkalmazás fejlesztésének megkezdéséhez szükséges összes eszközzel rendelkezni fog. Az SDK olyan eszközöket tartalmaz, amelyekkel könnyedén fejleszthet Azure-alkalmazásokat a Visual Studióban.

## <a name="create-a-namespace"></a>Névtér létrehozása

A Relay-funkciók Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít az Azure erőforrásainak címzéséhez az alkalmazáson belül. Relay-névtér létrehozásához kövesse az [itt leírt utasításokat](relay-create-namespace-portal.md).

## <a name="create-an-on-premises-server"></a>Helyszíni kiszolgáló létrehozása

Először létrehoz egy (utánzatként funkcionáló) helyszíni termékkatalógus-rendszert. Ez egészen egyszerű lesz. Erre úgy tekinthet, mint ami egy tényleges helyszíni termékkatalógus-rendszert képvisel, integrálni próbált teljes szolgáltatási felülettel.

Ez a projekt egy Visual Studio-konzolalkalmazás, és az [Azure Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) használja a Service Bus-kódtárak és konfigurációs beállítások belefoglalása érdekében.

### <a name="create-the-project"></a>A projekt létrehozása

1. Rendszergazdai jogosultságokkal indítsa el a Microsoft Visual Studiót. Ehhez kattintson a jobb gombbal a Visual Studio programikonra, majd kattintson a **Futtatás rendszergazdaként** parancsra.
2. A Visual Studio programban, a **File** (Fájl) menüben kattintson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre.
3. Az **Installed Templates** (Telepített sablonok) lap **Visual C#** területén kattintson a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer)) elemre. A **Name** (Név) mezőbe írja be a **ProductsServer** nevet:

   ![][11]
4. A **ProductsServer** projekt létrehozásához kattintson az **OK** gombra.
5. Ha már telepítette a NuGet-csomagkezelőt a Visual Studióhoz, hagyja ki a következő lépést. Ellenkező esetben látogasson el a [NuGet][NuGet] oldalára, és kattintson az [Install NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (NuGet telepítése) parancsra. Kövesse a NuGet-csomagkezelő telepítésének utasításait, majd indítsa újra a Visual Studiót.
6. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsServer** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) elemre.
7. Kattintson a **Browse** (Tallózás) lapra, és keressen rá a következőre: **WindowsAzure.ServiceBus**. Válassza a **WindowsAzure.ServiceBus** csomagot.
8. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

   ![][13]

   Vegye figyelembe, hogy a szükséges ügyfélszerelvények most már hivatkozottak.
8. Adjon egy új osztályt a termékszerződéshez. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsServer** projektre, és kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) lehetőségre.
9. A **Name** (Név) mezőbe írja be a **ProductsContract.cs** nevet. Ezután kattintson az **Add** (Hozzáadás) gombra.
10. A **ProductsContract.cs** fájlban cserélje le a névtér definícióját a következő kódra, amely meghatározza a szolgáltatás szerződését.

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
11. A ProductsContract.cs fájlban cserélje le a névtér definícióját a következő kódra, amely hozzáadja a profilszolgáltatást és annak állomását.

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
12. A Megoldáskezelőben kattintson duplán az **App.config** fájlra a Visual Studio-szerkesztőben való megnyitásához. A `<system.ServiceModel>` elem alján (de még mindig a `<system.ServiceModel>` elemen belül) írja be a következő XML-kódot. Győződjön meg arról, hogy a *yourServiceNamespace* helyett a saját névterét adja meg, és a *yourKey* helyett pedig a portálról korábban lekért SAS-kulcsot.

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
    A "transportClientEndpointBehavior" által okozott hiba csak figyelmeztetés, és nem hátráltatja ezt a példát.
    
13. Még mindig az App.config fájlban, az `<appSettings>` elemben cserélje le a kapcsolati karakterlánc értékét a korábban a portálról beszerzett kapcsolati karakterláncéra.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt, vagy a **Build** (Fordítás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre, és ellenőrizze az eddigi munkája pontosságát.

## <a name="create-an-aspnet-application"></a>ASP.NET-alkalmazás létrehozása

Ebben a szakaszban egy egyszerű ASP.NET-alkalmazást fog létrehozni, amely megjeleníti a termékszolgáltatásból lekért adatokat.

### <a name="create-the-project"></a>A projekt létrehozása

1. Ellenőrizze, hogy a Visual Studio rendszergazdai jogosultságokkal fut-e.
2. A Visual Studio programban, a **File** (Fájl) menüben kattintson a **New** (Új) elemre, majd kattintson a **Project** (Projekt) elemre.
3. Az **Installed Templates** (Telepített sablonok) lap **Visual C#** területén kattintson az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET keretrendszer)) elemre. Adja a projektnek a **ProductsPortal** nevet. Ezután kattintson az **OK** gombra.

   ![][15]

4. A **New ASP.NET Web Application** (Új ASP.NET-es webalkalmazás) párbeszédpanel **ASP.NET sablonok** listájában kattintson az **MVC** lehetőségre.

   ![][16]

6. Kattintson a **Change Authentication** (Hitelesítés módosítása) gombra. Győződjön meg róla, hogy a **No Authentication** (Nincs hitelesítés) elem van kiválasztva a **Change Authentication** (Hitelesítés módosítása) párbeszédpanelen, majd kattintson az **OK** gombra. Ebben az oktatóanyaghoz egy olyan alkalmazást helyezhet üzembe, amelyhez nincs szükség felhasználói bejelentkezésre.

    ![][18]

7. A **New ASP.NET Web Application** (Új ASP.NET-webalkalmazás) párbeszédpanelen kattintson az **OK** gombra az MVC-alkalmazás létrehozásához.
8. Most az Azure-erőforrásokat kell konfigurálnia az új webalkalmazáshoz. Kövesse a [cikk Közzététel az Azure platformon szakaszában](../app-service/app-service-web-get-started-dotnet.md#publish-to-azure) található lépéseket. Ezután térjen vissza ehhez az oktatóanyaghoz, és folytassa a következő lépéssel.
10. A Megoldáskezelőben kattintson a jobb gombbal a **Models** (Modellek) elemre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) elemre. A **Name** (Név) mezőbe írja be a **Product.cs** nevet. Ezután kattintson az **Add** (Hozzáadás) gombra.

    ![][17]

### <a name="modify-the-web-application"></a>A webalkalmazás módosítása

1. A Visual Studióban a Product.cs fájlban cserélje le a meglévő névtér-definíciót az alábbi kódra.

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
2. A Megoldáskezelőben bontsa ki a **Controllers** (Vezérlők) mappát, majd kattintson duplán a **HomeController.cs** fájlra, hogy megnyissa a Visual Studióban.
3. A **HomeController.cs** fájlban cserélje le a meglévő névtér-definíciót az alábbi kódra.

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
4. A Megoldáskezelőben bontsa ki a Views\Shared (Nézetek\Megosztott) mappát, majd kattintson duplán a **_Layout.cshtml** fájlra, hogy megnyissa a Visual Studio szerkesztőjében.
5. Módosítsa a **My ASP.NET Application** (Saját ASP.NET-alkalmazás) minden előfordulását **Northwind Traders Products** (Northwind Traders-termékek) értékre.
6. Távolítsa el a **Home** (Kezdőlap), **About** (Névjegy) és **Contact** (Kapcsolatfelvétel) hivatkozásokat. A következő példában törölje a kiemelt kódot.

    ![][41]

7. A Megoldáskezelőben bontsa ki a Views\Home (Nézetek\Kezdőlap) mappát, majd kattintson duplán az **Index.cshtml** fájlra, hogy megnyissa a Visual Studio szerkesztőjében. Cserélje le a fájl teljes tartalmát a következő kódra.

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
8. Az eddigi munkája pontosságának ellenőrzéséhez lenyomhatja a **Ctrl+Shift+B** billentyűkombinációt a projekt létrehozásához.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa az alkalmazást a működése ellenőrzéséhez.

1. Győződjön meg arról, hogy a **ProductsPortal** az aktív projekt. Kattintson a jobb gombbal a projekt nevére a Megoldáskezelőben, majd válassza a **Set As Startup Project** (Beállítás indítási projektként) lehetőséget.
2. Nyomja le az **F5** billentyűt a Visual Studióban.
3. Az alkalmazásának meg kell jelennie egy böngészőben.

   ![][21]

## <a name="put-the-pieces-together"></a>Az egyes alkotórészek összeállítása teljes egésszé

A következő lépés, hogy a helyszíni termékkiszolgálót az ASP.NET-alkalmazáshoz csatlakoztassuk.

1. Ha még nincs megnyitva, a Visual Studióban nyissa meg ismét az [ASP.NET-alkalmazás létrehozása](#create-an-aspnet-application) című szakaszban létrehozott **ProductsPortal** projektet.
2. A „Helyszíni kiszolgáló létrehozása” című szakaszhoz hasonlóan adja a NuGet-csomagot a projekt referenciáihoz. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsPortal** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőségre.
3. Keressen a **WindowsAzure.ServiceBus** kifejezésre, és válassza ki az **WindowsAzure.ServiceBus** elemet. Ezután fejezze be a telepítést, és zárja be a párbeszédpanelt.
4. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsPortal** projektre, majd kattintson az **Add** (Hozzáadás), azután pedig az**Existing Item** (Létező elem) lehetőségre.
5. Keresse meg a **ProductsContract.cs** fájlt a **ProductsServer** konzolprojektben. Kattintással jelölje ki a ProductsContract.cs fájlt. Kattintson a lefelé mutató nyílra az **Add** (Hozzáadás) elem mellett, majd kattintson az **Add as Link** (Hozzáadás hivatkozásként) parancsra.

   ![][24]

6. Ezután nyissa meg a **HomeController.cs** fájlt a Visual Studio szerkesztőjében, és a névtér definícióját cserélje az alábbi kódra. Győződjön meg arról, hogy a *yourServiceNamespace* helyett a saját szolgáltatásnévterét adja meg, és a *yourKey* helyett pedig a saját SAS-kulcsát. Így teheti lehetővé, hogy az ügyfél helyszíni szolgáltatásokat hívjon meg, és vissza tudja adni a hívás eredményeit.

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
7. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsPortal** megoldásra (ügyeljen arra, hogy a jobb gombbal a megoldásra, és ne a projektre kattintson). Kattintson az **Add** (Hozzáadás), majd az **Existing Project** (Meglévő projekt) elemre.
8. Keresse meg a **ProductsServer** projektet, és kattintson duplán a **ProductsServer.csproj** megoldásfájlra annak hozzáadásához.
9. A **ProductsPortal** portálra vonatkozó adatok csak akkor jeleníthetők meg, ha közben fut a **ProductsServer**. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsPortal** megoldásra, majd kattintson a **Properties** (Tulajdonságok) lehetőségre. Megnyílik a **Property Pages** (Tulajdonságlapok) párbeszédpanel.
10. A bal oldalon kattintson a **Startup Project** (Kezdőprojekt) elemre. A jobb oldalon kattintson a **Multiple startup projects** (Több kezdőprojekt elemre). Győződjön meg arról, hogy a **ProductsServer** és a **ProductsPortal** is megjelenik (ebben a sorrendben), és mindkettőhöz a **Start** (Indítás) művelet legyen beállítva.

      ![][25]

11. Még mindig a **Properties** (Tulajdonságok) párbeszédpanelen maradva kattintson a **Project Dependencies** (Projektfüggőségek) elemre a bal oldalon.
12. A **Projects** (Projektek) listában kattintson a **ProductsServer** elemre. Győződjön meg arról, hogy a **ProductsPortal** nincs kijelölve.
13. A **Projects** (Projektek) listában kattintson a **ProductsPortal** elemre. Győződjön meg arról, hogy a **ProductsServer** ki van jelölve.

    ![][26]

14. A **Property Pages** (Tulajdonságlapok) párbeszédpanelen kattintson az **OK** gombra.

## <a name="run-the-project-locally"></a>A projekt helyi futtatása

Az alkalmazás helyi teszteléséhez nyomja le az **F5** billentyűt a Visual Studióban. Először a helyszíni kiszolgálónak (**ProductsServer**) kell elindulnia, és ezt követően kell megnyílnia a **ProductsPortal** alkalmazásnak egy böngészőablakban. Ezúttal a termék helyszíni rendszeréből származó adatokat fog látni a termékleltárban.

![][10]

A **ProductsPortal** oldalon kattintson a **Frissítés** parancsra. Valahányszor frissíti az oldalt, a kiszolgáló alkalmazása megjelenít egy üzenetet, amikor a **ProductsServer** `GetProducts()` funkcióját meghívják.

Zárja be mindkét alkalmazást, mielőtt a következő lépéssel folytatná.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>A ProductsPortal projekt telepítése egy Azure-webalkalmazásba

A következő lépés az Azure-webalkalmazás **ProductsPortal** előtérkiszolgálójának újbóli közzététele. Tegye a következőket:

1. A Megoldáskezelőben kattintson a jobb gombbal a **ProductsPortal** projektre, majd kattintson a **Publish** (Közzététel) lehetőségre. Majd a **Publish** (Közzététel) oldalon kattintson a **Publish** (Közzététel) elemre.

  > [!NOTE]
  > Előfordulhat, hogy egy hibaüzenet jelenik meg a böngészőablakban, amikor a **ProductsPortal** webprojekt automatikusan elindul a telepítés után. Emiatt nem kell aggódnia, ugyanis az okozza, hogy a **ProductsServer** alkalmazás még nem fut.
>
>

2. Másolja ki a telepített webalkalmazás URL-címét, mert szükség lesz rá a következő lépésben. Az URL-cím a Visual Studio Azure App Service-tevékenység ablakában is elérhető:

  ![][9]

3. Zárja be a böngészőablakot a futó alkalmazás leállításához.

### <a name="set-productsportal-as-web-app"></a>A ProductsPortal beállítása webalkalmazásként

Mielőtt futtatná az alkalmazást a felhőben, győződjön meg arról, hogy a **ProductsPortal** webalkalmazásként indult el a Visual Studióban.

1. A Visual Studióban kattintson a jobb gombbal a **ProductsPortal** projektre, majd kattintson a **Properties** (Tulajdonságok) lehetőségre.
2. A bal oldali oszlopban kattintson a **Web** elemre.
3. A **Start Action** (Művelet indítása) részen kattintson az **Start URL** (URL-cím indítása) gombra, majd írja be a szövegmezőbe a korábban telepített webalkalmazás URL-címét (például: `http://productsportal1234567890.azurewebsites.net/`).

    ![][27]

4. A Visual Studio **File** (Fájl) menüjében kattintson az **Save All** (Összes mentése) parancsra.
5. A Visual Studio Build (Fordítás) menüjében kattintson a **Rebuild Solution** (Megoldás újrafordítása) parancsra.

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Nyomja le az F5 billentyűt az alkalmazás fordításához és futtatásához. Először a helyszíni kiszolgálónak (**ProductsServer** konzolalkalmazás) kell elindulnia, ezt követően indul el a **ProductsPortal** alkalmazás a böngészőablakban a képernyőképen is látható módon. Figyelje meg, hogy a termék helyszíni rendszeréből származó adatokat láthat a termékleltárban, és ezek az adatok a webalkalmazásban jelennek meg. Ellenőrizze az URL-címet, és győződjön meg arról, hogy a **ProductsPortal** Azure-webalkalmazás fut a felhőben.

   ![][1]

   > [!IMPORTANT]
   > A **ProductsServer** konzolalkalmazásnak futnia kell, és képesnek kell lennie biztosítani az adatokat a **ProductsPortal** alkalmazás számára. Ha a böngészőben egy hibaüzenet jelenik meg, várjon néhány másodpercet, amíg a **ProductsServer** betöltődik, és megjeleníti a következő üzenetet. Ezután kattintson a **Frissítés** gombra a böngészőben.
   >
   >

   ![][37]
2. A **ProductsPortal** böngészőoldalán kattintson a **Frissítés** gombra. Valahányszor frissíti az oldalt, a kiszolgáló alkalmazása megjelenít egy üzenetet, amikor a **ProductsServer** `GetProducts()` funkcióját meghívják.

    ![][38]

## <a name="next-steps"></a>Következő lépések

A Azure Relay szolgáltatásól a következő forrásanyagokban találhat további információkat:  

* [Mi az az Azure Relay?](relay-what-is-it.md)  
* [Az Azure Relay használata](relay-wcf-dotnet-get-started.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
