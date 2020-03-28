---
title: 'Oktatóanyag: REST-oktatóanyag az Azure Relay használatával'
description: 'Oktatóanyag: Hozzon létre egy Azure Service Bus Relay gazdaalkalmazást, amely egy REST-alapú felületet tesz elérhetővé.'
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 229ed2b00582f2c73ce68c47406d68325abda736
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73718834"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Oktatóanyag: Az Azure WCF relay REST oktatóanyaga

Ez az oktatóanyag ismerteti, hogyan hozhat létre egy Azure Relay gazdaalkalmazást, amely egy REST-alapú felületet elérhetővé teszi. A REST lehetővé teszi egy webes ügyfél, például egy webes böngésző számára, hogy hozzáférjen a HTTP-kérelmeken keresztül a Service Bus alkalmazásprogramozási felületekhez.

Az oktatóanyag a Windows Kommunikációs alaprendszer (WCF) REST programozási modelljét használja egy REST-szolgáltatás létrehozásához az Azure Relay szolgáltatáson. További információt a [WCF REST programozási modellje](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) és [tervezési és végrehajtási szolgáltatásai című témakörben talál.](/dotnet/framework/wcf/designing-and-implementing-services)

Az oktatóanyagban a következő feladatokat végezze el:

> [!div class="checklist"]
>
> * Telepítse az oktatóanyag előfeltételeit.
> * Hozzon létre egy továbbító névteret.
> * REST-alapú WCF szervizszerződés definiálása.
> * A REST-alapú WCF-szerződés megvalósítása.
> * A REST-alapú WCF-szolgáltatás üzemeltetése és futtatása.
> * Futtassa és tesztelje a szolgáltatást.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2019-et használják.
* Azure SDK a .NET.Azure SDK for .NET. Telepítse az [SDK letöltések oldalon](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Továbbító névtér létrehozása

A Relay-funkciók Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít az Azure erőforrásainak címzéséhez az alkalmazáson belül. Relay-névtér létrehozásához kövesse az [itt leírt utasításokat](relay-create-namespace-portal.md).

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>REST-alapú WCF szolgáltatási szerződés definiálása az Azure Relay szolgáltatással

WcF REST-stílusú szolgáltatás létrehozásakor meg kell határoznia a szerződést. A szerződés megadja a gazdagép által támogatott műveleteket. A szolgáltatásművelet hasonlít egy webszolgáltatási metódushoz. Szerződés definiálása C++, C# vagy Visual Basic felülettel. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. Alkalmazza a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútumot az egyes összeköttetésekre, és alkalmazza az [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútumot az egyes műveletekre. 

> [!TIP]
> Ha egy olyan kapcsolaton, amely rendelkezik a [ServiceContractAttribute attribútummal,](/dotnet/api/system.servicemodel.servicecontractattribute) az nem rendelkezik [operationcontractAttribute attribútummal,](/dotnet/api/system.servicemodel.operationcontractattribute)akkor ez a metódus nem elérhető. Az ezekhez a feladatokhoz használt kód az eljárást követő példában jelenik meg.

A WCF-egyezmény és a REST-stílusú egyezmény ek közötti elsődleges különbség egy tulajdonság hozzáadása az [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute attribútumhoz.](/dotnet/api/system.servicemodel.web.webgetattribute) Ez a tulajdonság lehetővé teszi a felület egy metódusának leképezését egy, a felület másik oldalán levő metódussá. Ez a példa a [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) attribútummal `HTTP GET`kapcsolja a metódust a alkalmazáshoz. Ez a megközelítés lehetővé teszi, hogy a Service Bus pontosan beolvassa és értelmezze a felületre küldött parancsokat.

### <a name="to-create-a-contract-with-an-interface"></a>Szerződés létrehozása felülettel

1. Indítsa el a Microsoft Visual Studio alkalmazást rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, és válassza a **Futtatás rendszergazdaként parancsot.**
1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework) (C# )** lehetőséget, majd a **Tovább**gombot.
1. Nevezze el a *projektet ImageListener*. Használja az alapértelmezett **helyet**, majd válassza a **Létrehozás lehetőséget.**

   C# projekt esetén a Visual Studio létrehoz egy *Program.cs* fájlt. Ez az osztály tartalmaz egy üres `Main()` metódust, amely szükséges a konzolalkalmazás projektek helyes létrejöttéhez.

1. A **Megoldáskezelőben**kattintson a jobb gombbal az **ImageListener** projektre, majd válassza **a NuGet-csomagok kezelése parancsot.**
1. Válassza a **Tallózás**lehetőséget, majd keresse meg a **WindowsAzure.ServiceBus**elemet. Válassza **a Telepítés**lehetőséget, és fogadja el a használati feltételeket.

    Ez a lépés hivatkozásokat ad hozzá a Service Bus és a *System.ServiceModel.dll fájlhoz.* Ez a csomag automatikusan hivatkozásokat ad `System.ServiceModel`hozzá a Service Bus-kódtárakhoz és a WCF-hez.

1. Kifejezetten adjon hozzá `System.ServiceModel.Web.dll` hivatkozást a projektre. A **Megoldáskezelőben**kattintson a jobb gombbal a **Hivatkozások** elemre a projektmappa alatt, és válassza a **Hivatkozás hozzáadása parancsot.**
1. A **Hivatkozás hozzáadása csoportban**válassza a **Framework** lehetőséget, és írja be a *System.ServiceModel.Web* kifejezést a **keresésbe.** Jelölje be a **System.ServiceModel.Web** jelölőnégyzetet, majd kattintson az **OK** lehetőségre.

Ezután hajtsa végre a következő kódmódosításokat a projekten:

1. Adja hozzá `using` a következő állításokat a *Program.cs* fájl tetején.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * A [System.ServiceModel](/dotnet/api/system.servicemodel) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A WCF-továbbító a WCF számos objektumát és attribútumát használja a szolgáltatási szerződések definiálásához. Ezt a névteret a legtöbb továbbítóalkalmazásban használhatja.
    * [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) segít meghatározni a csatornát, amelyaz az objektum, amelyen keresztül kommunikál az Azure Relay és az ügyfél webböngésző.
    * [A System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) tartalmazza azokat a típusokat, amelyek lehetővé teszik webalapú alkalmazások létrehozását.

1. Nevezze át `ImageListener` a `Microsoft.ServiceBus.Samples`névteret .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Közvetlenül a névtér deklaráció nyitó kapcsos `IImageContract` zárójele `ServiceContractAttribute` után definiáljon egy új `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`nevű felületet, és alkalmazza az attribútumot a kapcsolatra. 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke a szerződés egyedi azonosítója, és verzióadatokkal kell rendelkeznie. További információ: [Service Versioning](/dotnet/framework/wcf/service-versioning) (Szolgáltatás verziószámozása). A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

1. A `IImageContract` kapcsolaton belül deklaráljon `IImageContract` egy metódust arra az `OperationContract` egyetlen műveletre, amelyet a szerződés közzétesz a kapcsolatban, és alkalmazza az attribútumot arra a metódusra, amelyet a közszolgálati buszszerződés részeként közzé szeretne tenni.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Az `OperationContract` attribútumban adja `WebGet` hozzá az értéket.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Az `WebGet` érték hozzáadása lehetővé teszi, hogy `GetImage`a továbbítószolgáltatás a `GetImage` HTTP `HTTP GETRESPONSE` GET-kérelmeket a rendszerbe irányítsa, és a visszatérési értékeket válaszsá fordítsa. Később az oktatóanyagban egy webböngészőt fog használni a módszer eléréséhez, és a kép böngészőben való megjelenítéséhez.

1. Közvetlenül az `IImageContract` definíciója után deklaráljon egy csatornát, amely örökli az `IImageContract` és az `IClientChannel` felületek tulajdonságait is.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   A csatorna egy olyan WCF-objektum, amelyen keresztül a szolgáltatás és az ügyfél információkat adnak át egymásnak. Később létrehozhatja a csatornát a gazdaalkalmazásban. Az Azure Relay ezután ezt a csatornát használja `GetImage` a HTTP GET-kérelmek továbbítására a böngészőből a megvalósításhoz. A továbbító is használja `GetImage` a csatornát, hogy `HTTP GETRESPONSE` a visszatérési értéket, és fordítsa le egy az ügyfél böngésző.

1. Válassza a Build solution **összeállítása** > **lehetőséget** az eddigi munka pontosságának megerősítéséhez.

### <a name="example-that-defines-a-wcf-relay-contract"></a>WcF-továbbítóegyezményt definiáló példa

A következő kód egy egyszerű felületet mutat be, amely egy WCF-továbbító egyezményt határoz meg.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>A REST-alapú WCF szolgáltatási szerződés megvalósítása

REST-stílusú WCF-továbbító szolgáltatás létrehozásához először hozza létre a szerződést egy felület használatával. A következő lépés a felület megvalósítása. Ez az eljárás magában `ImageService` foglalja egy osztály nevű, `IImageContract` amely megvalósítja a felhasználó által definiált felületet. A szerződés megvalósítása után konfigurálja a kapcsolatot egy *App.config* fájl használatával. A konfigurációs fájl tartalmazza az alkalmazáshoz szükséges információkat. Ez az információ tartalmazza a szolgáltatás nevét, a szerződés nevét és a továbbító szolgáltatással való kommunikációhoz használt protokoll típusát. Az ezekhez a feladatokhoz használt kód az eljárást követő példában jelenik meg.

Az előző lépésekhez ugyanúgy kevés különbség van a REST-stílusú szerződés és a WCF-továbbító szerződés megvalósítása között.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST-stílusú Service Bus szerződés megvalósítása

1. Hozzon létre egy új, `ImageService` nevű osztályt közvetlenül az `IImageContract` felület meghatározása után. Az `ImageService` osztály megvalósítja az `IImageContract` felületet.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban jelenik meg, mint a felületdefiníció és a `Main()` metódus.

1. Alkalmazza a [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) attribútumot az `IImageService` osztályra annak jelzésére, hogy az osztály egy WCF-egyezmény implementációja.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Mint korábban említettük, ez a névtér nem egy hagyományos névtér. Ez része a WCF architektúra, amely azonosítja a szerződést. További információt az [Adategyezmény nevei című témakörben talál.](/dotnet/framework/wcf/feature-details/data-contract-names/)

1. Vegyen fel egy *.jpg* képet a projektbe. Ez a fájl egy kép, amelyet a szolgáltatás a fogadó böngészőben jelenít meg.

   1. Kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás parancsot.**
   1. Ezután válassza **a Meglévő elem lehetőséget.**
   1. A **Meglévő elem hozzáadása** segítségével tallózzon meg egy megfelelő .jpg értékre, majd válassza a **Hozzáadás gombot.** A fájl hozzáadásakor válassza a **Fájlnév**gomb melletti legördülő listából a **Minden fájl** lehetőséget.

   A többi az oktatóanyag feltételezi, hogy a kép neve *image.jpg*. Ha másik fájllal rendelkezik, át kell neveznie a képet, vagy módosítania kell a kódot a kompenzáció érdekében.

1. Annak érdekében, hogy a futó szolgáltatás megtalálja a képfájlt, a **Megoldáskezelőben** kattintson a jobb gombbal a képfájlra, majd válassza a **Tulajdonságok parancsot.** A **Tulajdonságok párbeszédpanelen**állítsa a **Másolás a Kimeneti könyvtárba** **másolást, ha újabb.**

1. A [kapcsolattal kötött szerződés létrehozásához](#to-create-a-contract-with-an-interface) használható a *System.Drawing.dll* kódösszeállításra való hivatkozás hozzáadása a projekthez.

1. Adja hozzá `using` a következő kapcsolódó állításokat:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. Az `ImageService` osztályban adja hozzá a következő konstruktorát, amely betölti a bitképet, és előkészíti az ügyfélböngészőnek való elküldésre:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Közvetlenül az előző kód `GetImage` után adja `ImageService` hozzá a következő metódust az osztályba a képet tartalmazó HTTP-üzenet visszaadására.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Ez a `MemoryStream` megvalósítás a lemezkép lekéréséhez és a böngészőbe való streamelésre való előkészítéséhez használatos. Az adatfolyam pozícióját nullával indítja, az adatfolyam tartalmát *.jpg-ként*deklarálja, és az adatokat streameli.

1. Válassza a > **Build-megoldás lehetőséget.** **Build**

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>A konfiguráció meghatározása a webszolgáltatás Service Buson való futtatásához

1. A **Megoldáskezelőben**kattintson duplán az **App.config** fájlra a fájl Visual Studio-szerkesztőben való megnyitásához.

    Az *App.config* fájl tartalmazza a szolgáltatás nevét, végpontját és kötését. A végpont az a hely, amelyet az Azure Relay elérhetővé teszi az ügyfelek és a gazdagépek számára az egymással való kommunikációhoz. A kötés a kommunikációhoz használt protokoll típusa. A fő különbség itt az, hogy a konfigurált szolgáltatás végpontja egy [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) kötésre hivatkozik.

1. A `<system.serviceModel>` XML-elem egy WCF-elem, amely egy vagy több szolgáltatást határoz meg. Itt a szolgáltatás nevének és végpontjának meghatározására szolgál. Az elem alján, de még `<system.serviceModel>`mindig `<bindings>` belül, `<system.serviceModel>` adjon hozzá egy elemet, amely a következő tartalmat tartalmaz:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Ez a tartalom határozza meg az alkalmazásban használt kötéseket. Több kötést is definiálhat, de ebben az oktatóanyagban csak egyet határoz meg.

    Az előző kód egy WCF-továbbító [webethatározóHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) kötést határoz meg `relayClientAuthenticationType` `None`a beállításával. Ez a beállítás azt jelzi, hogy egy végpont ezt a kötést nem igényel ügyfél hitelesítő adatokat.

1. A `<bindings>` elem után adjon hozzá egy `<services>` elemet. A kötésekhez hasonlóan megadhat több szolgáltatást is egyetlen konfigurációs fájlon belül. Ez az oktatóanyag azonban csak egyet ad meg.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Ez a tartalom olyan szolgáltatást konfigurál, `webHttpRelayBinding`amely a korábban definiált alapértelmezett szolgáltatást használja. Az alapértelmezett `sbTokenProvider`értéket is használja, amely a következő lépésben van definiálva.

1. Az `<services>` elem után `<behaviors>` hozzon létre egy `SAS_KEY` elemet a következő tartalommal, és cserélje le a Közös hozzáférésű aláírás (SAS) kulccsal. Ha SAS-kulcsot szeretne beszerezni az [Azure Portalról,][Azure portal] [olvassa el a Felügyeleti hitelesítő adatok beszerzése című témakört.](service-bus-relay-tutorial.md#get-management-credentials)

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Az *App.config*fájlban `<appSettings>` az elemben a teljes kapcsolati karakterlánc értékét cserélje le a portálról korábban beszerzett kapcsolati karakterláncra.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Válassza a Build megoldás **összeállítása** > **a** teljes megoldás létrehozásához.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Példa a REST-alapú WCF szolgáltatási szerződés megvalósítására

A következő kód a szerződés és a szolgáltatás megvalósítása egy REST-alapú szolgáltatás, amely fut a Service Bus a `WebHttpRelayBinding` kötés használatával.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

A következő példa a szolgáltatáshoz társított *App.config* fájlt mutatja be.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>A REST-alapú WCF-szolgáltatás üzemeltetése az Azure Relay használatához

Ez a szakasz azt ismerteti, hogyan futtathat webszolgáltatást wcf-továbbítóval rendelkező konzolalkalmazással. Az ebben a szakaszban leírt kód teljes listája az eljárást követő példában jelenik meg.

### <a name="to-create-a-base-address-for-the-service"></a>Alapszintű cím létrehozása a szolgáltatáshoz

1. A `Main()` függvénydeklarációban hozzon létre egy változót a projekt névterének tárolására. Ügyeljen arra, `yourNamespace` hogy a korábban létrehozott Továbbító névtér nevére cserélje le.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    A Service Bus a névtér nevét használva létrehoz egy egyedi URI-t.

1. Hozzon létre egy `Uri`-példányt a névtéren alapuló szolgáltatás alapszintű címéhez.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>A webszolgáltatásgazda létrehozása és konfigurálása

Még `Main()`mindig a , hozza létre a webszolgáltatás-gazdagép, a szakasz korábbi részében létrehozott URI-cím használatával.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

A szolgáltatásgazda az a WCF-objektum, amely a gazdaalkalmazást példányosítja. Ez a példa adja át azt a típusú állomás `ImageService`létrehozni kívánt, amely egy , és azt a címet is, amelyen meg szeretné tenni a gazdaalkalmazás.

### <a name="to-run-the-web-service-host"></a>A webszolgáltatásgazda futtatása

1. Még `Main()`mindig, adja hozzá a következő sort a szolgáltatás megnyitásához.

    ```csharp
    host.Open();
    ```

    A szolgáltatás jelenleg fut.

1. Megjelenít egy üzenetet, amely jelzi, hogy a szolgáltatás fut, valamint a szolgáltatás leállításának módját.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Ha kész, zárja be a szolgáltatásgazdát.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Példa a szolgáltatási szerződésre és a végrehajtásra

Az alábbi példa tartalmazza a szolgáltatási szerződést és a megvalósítását az oktatóanyag előző lépéseiből, és egy konzolalkalmazásban működteti a szolgáltatást. Fordítsa le a következő kódot egy *ImageListener.exe*nevű végrehajtható fájlba.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>A szolgáltatás futtatása és tesztelése

A megoldás létrehozása után az alábbi lépéseket követve futtathatja az alkalmazást:

1. Válassza az F5 lehetőséget, vagy keresse meg a végrehajtható fájl helyét, *az ImageListener\bin\Debug\ImageListener.exe*mappát a szolgáltatás futtatásához. Tartsa futtassa az alkalmazást, mert a következő lépéshez szükséges.
1. A kép megtekintéséhez másolja és illessze be a címet a parancssorból egy böngészőbe.
1. Ha végzett, válassza az Enter lehetőséget a parancssorablakban az alkalmazás bezárásához.

## <a name="next-steps"></a>További lépések

Most, hogy az Azure Relay szolgáltatást használó alkalmazást készített, további információért tekintse meg az alábbi cikkeket:

* [Mi az az Azure Relay?](relay-what-is-it.md)
* [Helyszíni WCF REST-szolgáltatás külső ügyfél számára való elérhetővé tévő része az Azure WCF-továbbító használatával](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
