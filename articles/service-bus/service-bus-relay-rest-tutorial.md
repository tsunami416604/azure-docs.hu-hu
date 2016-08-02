<properties
    pageTitle="A Service Bus REST oktatóanyag továbbítón keresztüli üzenetcsere használatával | Microsoft Azure"
    description="Hozzon létre egy egyszerű, REST-alapú felületet közzétevő Service Bus Relay gazdaalkalmazást."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/03/2016"
    ms.author="sethm" />

# Service Bus REST tutorial (A Service Bus REST-oktatóanyaga)

Ez az oktatóanyag leírja, hogyan hozhat létre egy egyszerű, REST-alapú felületet közzétevő Service Bus gazdaalkalmazást. A REST lehetővé teszi egy webes ügyfél, például egy webes böngésző számára, hogy hozzáférjen a HTTP-kérelmeken keresztül a Service Bus alkalmazásprogramozási felületekhez.

Ez az oktatóanyag a Windows Communication Foundation (WCF) REST programozási modell segítségével hoz létre REST-szolgáltatást a Service Buson. További információt a WCF-dokumentáció [WCF REST Programming Model](https://msdn.microsoft.com/library/bb412169.aspx) (WCF REST programozási modell) és [Designing and Implementing Services](https://msdn.microsoft.com/library/ms729746.aspx) (Szolgáltatások tervezése és megvalósítása) témakörében találhat.

## 1. lépés: Szolgáltatásnévtér létrehozása

Az első lépés egy névtér létrehozása, valamint egy közös hozzáférésű jogosultságkód (SAS-) kulcs beszerzése. A névtér egy alkalmazáshatárt biztosít a Service Buson keresztül közzétett minden alkalmazáshoz. Az SAS-kulcsot a rendszer automatikusan előállítja a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és az SAS-kulcs együttes használata hitelesítő adatokat biztosít a Service Bus számára, amellyel hitelesíti a hozzáférést egy alkalmazáshoz.

1. Szolgáltatásnévtér létrehozásához látogasson el a [klasszikus Azure portálra][]. A bal oldalon kattintson a **Service Bus** elemre, majd kattintson a **Létrehozás** elemre. Adja meg a névtér nevét, majd kattintson pipára.

2. A [klasszikus Azure portálra][] fő ablakában kattintson az előző lépésben létrehozott névtér nevére.

3. Kattintson a **Konfigurálás** fülre.

4. A **megosztottelérésikulcs-készítő** területen jegyezze fel a **RootManageSharedAccessKey** házirendhez társított **elsődleges kulcs** értékét, vagy másolja a vágólapra. Ezt az értéket az oktatóanyag későbbi részében fogja használni.

## 2. lépés: A Service Busszal használni kívánt REST-alapú WCF szolgáltatási szerződés megadása

Akárcsak más Service Bus szolgáltatások esetében, a REST-stílusú szolgáltatás létrehozásakor meg kell adnia egy szerződést. A szerződés megadja a gazdagép által támogatott műveleteket. A szolgáltatási művelet tekinthető webszolgáltatási módszernek. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútumot minden felületre, az [OperationContractAttribaute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútumot pedig minden műveletre alkalmazni kell. Ha egy felület egy metódusa rendelkezik a [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútummal, de nem rendelkezik az [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútummal, nem lesz közzétéve. A feladatokhoz használt kód megtalálható az eljárást követő példában.

Az elsődleges különbség egy alapszintű Service Bus szerződés és egy REST-stílusú szerződés között a következő tulajdonság hozzáadása az[OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútumhoz: [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Ez a tulajdonság lehetővé teszi a felület egy metódusának leképezését egy, a felület másik oldalán levő metódussá. Ebben az esetben a [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) segítségével csatolunk egy metódust a HTTP GET kérelemhez. Ez lehetővé teszi a Service Bus számára, hogy pontosan lekérje és értelmezze a felületre küldött parancsokat.

### Service Bus szerződés létrehozása felülettel

1. Nyissa meg a Visual Studiót rendszergazdaként: ehhez a **Start** menüben kattintson a jobb gombbal a programra, majd kattintson a **Futtatás rendszergazdaként** parancsra.

2. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **File** (Fájl) menüre, és válassza a **New** (Új), majd a **Project** (Projekt) elemet. A **New Project** (Új projekt) párbeszédpanelen kattintson a **Visual C#** elemre, válassza ki a **Console Application** (Konzolalkalmazás) sablont, és nevezze el **ImageListener** néven. Használja az alapértelmezett **Location** (Hely) értéket. A projekt létrehozásához kattintson az **OK** gombra.

3. C# projekt esetében a Visual Studio létrehoz egy `Program.cs` fájlt. Ez az osztály tartalmaz egy üres `Main()` metódust, amely szükséges a konzolalkalmazás projektek helyes létrejöttéhez.

4. Adjon hozzá Service Bus- és **System.ServiceModel.dll**-hivatkozásokat a projekthez a Service Bus NuGet csomag telepítésével. Ez a csomag automatikusan hivatkozásokat ad a Service Bus-könyvtárakhoz, valamint a WCF **System.ServiceModel** névtérhez. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **ImageListener** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

5. Expliciten hozzá kell adnia egy hivatkozást a projekt **System.ServiceModel.Web.dll** fájljához:

    a. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **References** (Hivatkozások) mappára a projektmappa területén, majd kattintson az **Add Reference** (Hivatkozás hozzáadása) lehetőségre.

    b. Az **Add Reference** (Hivatkozás hozzáadása) párbeszédablakban kattintson a bal oldalon a **Framework** (Keretrendszer) lapra, majd a **Search** (Keresés) mezőben írja be a **System.ServiceModel.Web** kifejezést. Jelölje be a **System.ServiceModel.Web** jelölőnégyzetet, majd kattintson az **OK** lehetőségre.

6. Adja hozzá a következő `using` utasításokat a Program.cs fájl elejéhez:

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    A [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A Service Bus számos WCF-objektumot és -attribútumot használ a szolgáltatási szerződések meghatározására. A legtöbb Service Bus Relay alkalmazásban használni fogja ezt a névteret. Hasonlóképp, a [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) segítségével meghatározhatja a csatornát, amely az az objektum, amelyen keresztül kommunikálhat a Service Bus szolgáltatással és az ügyfél webböngészőjével. Végül a [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) tartalmazza a webalapú alkalmazások létrehozását engedélyező típusokat.

7. Nevezze át az `ImageListener` névteret **Microsoft.ServiceBus.Samples** névre.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Közvetlenül a névtér-deklaráció nyitó zárójele után adjon meg egy új, **IImageContract** nevű felületet, és alkalmazza a **ServiceContractAttribute** attribútumot a felületen `http://samples.microsoft.com/ServiceModel/Relay/` értékkel. A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke egyedi azonosítóként van használatban ehhez a szerződéshez, és rendelkeznie kell a verzióinformációkkal. További információ: [Service Versioning](http://go.microsoft.com/fwlink/?LinkID=180498) (Szolgáltatás verziószámozása). A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Az `IImageContract` felületen belül deklaráljon egy metódust az `IImageContract` szerződés által a felületen közzétett egyetlen művelethez, valamint alkalmazza az `OperationContractAttribute` attribútumot a nyilvános Service Bus szerződés részeként közzétenni kívánt metódusra.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Az **OperationContract** attribútumban adja hozzá a **WebGet** értéket.

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Ezzel engedélyezi a Service Bus számára a HTTP GET kérelmek átirányítását ide: `GetImage`, valamint a `GetImage` visszaadott értékeinek lefordítását egy HTTP GETRESPONSE válaszba. Az oktatóanyagban később egy webböngészőt használhat majd a metódus eléréséhez és a kép megjelenítéséhez böngészőben.

11. Közvetlenül az `IImageContract` definíciója után deklaráljon egy csatornát, amely örökli az `IImageContract` és az `IClientChannel` felületek tulajdonságait is.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    A csatorna egy olyan WCF-objektum, amelyen keresztül a szolgáltatás és az ügyfél információkat adnak át egymásnak. Később létrehozhat egy csatornát a gazdaalkalmazásban. A Service Bus ezután ennek a csatornának a segítségével továbbítja a HTTP GET kérelmeket a böngészőből a **GetImage** megvalósításba. A Service Bus ezt a csatornát használja a **GetImage** visszaadási érték beszerzésére, majd az ügyfélböngészőhöz a HTTP GETRESPONSE értékre való fordítására is.

12. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre az eddigi munkája pontosságának ellenőrzéséhez.

### Példa

Az alábbi kód egy Service Bus szerződést meghatározó alapszintű felületet mutat be.

```
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

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

## 3. lépés: A Service Busszal használni kívánt REST-alapú WCF szolgáltatási szerződés megvalósítása

REST stílusú Service Bus szolgáltatás létrehozásához először létre kell hoznia a szerződést, amelyet egy felület használatával határozhat meg. A következő lépés a felület megvalósítása. Ebbe beletartozik egy **ImageService** nevű osztály létrehozása, amely megvalósítja a felhasználó által megadott **IImageContract** felületet. A szerződés megvalósítása után egy App.config fájl segítségével konfigurálhatja a felületet. A konfigurációs fájl tartalmazza az alkalmazáshoz szükséges információkat, például a szolgáltatás nevét, a szerződés nevét, valamint a Service Busszal való kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód megtalálható az eljárást követő példában.

Akár az előző lépéseknél, nagyon kicsi a különbség a REST-stílusú szerződés és az alapszintű Service Bus szerződés megvalósítása között.

### REST-stílusú Service Bus szerződés megvalósítása

1. Hozzon létre egy új, **ImageService** nevű osztályt közvetlenül az **IImageContract** felület meghatározása után. Az **ImageService** osztály az **IImageContract** felületet valósítja meg.

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban jelenik meg, mint a felületdefiníció és a `Main()` metódus.

2. Alkalmazza a [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) attribútumot az **IImageService** osztályra, így jelezheti, hogy az osztály egy WCF-szerződés megvalósítása.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Amint azt korábban említettük, ez a névtér nem egy hagyományos névtér. Ehelyett ez a szerződést azonosító WCF-architektúra része. További információt a WCF-dokumentáció [Data Contract Names](https://msdn.microsoft.com/library/ms731045.aspx) (Adategyezmény-nevek) témakörében találhat.

3. Adjon hozzá egy .jpg képet a projekthez.  

    Ez egy kép, amelyet a szolgáltatás megjelenít a fogadó böngészőben. Kattintson a jobb gombbal a projektre, majd kattintson az **Add** (Hozzáadás) lehetőségre.  Ezután kattintson az **Existing Item** (Meglévő elem) elemre. Az **Add Existing Item** (Meglévő elem hozzáadása) párbeszédablak segítségével keressen egy megfelelő .jpg fájlt, és kattintson az **Add** (Hozzáadás) lehetőségre.

    Egy fájl hozzáadásakor győződjön meg arról, hogy a legördülő menüben kiválasztotta az **All Files** (Minden fájl) lehetőséget a **File nane:** (Fájlnév:) mező mellett. Az oktatóanyag hátralevő része feltételezi, hogy a kép neve image.jpg. Ha egy másik fájlja van, át kell neveznie a képet, vagy módosítania kell a kódot a helyesbítéshez.

4. Annak biztosításához, hogy a futó szolgáltatás megtalálja a képfájlt, a **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal képfájlra, majd kattintson a **Properties** (Tulajdonságok) lehetőségre. A **Properties** (Tulajdonságok) panelen állítsa a **Copy to Output Directory** (Másolás a kimeneti könyvtárba) beállítást **Copy if newer** (Másolás, ha újabb) értékre.

5. Adjon egy hivatkozást a projekt **System.Drawing.dll** szerelvényéhez, és adja hozzá az alábbi társított `using` utasításokat is.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Az **ImageService** osztályban adja hozzá az alábbi konstruktort, amely betölti a bitképet, és előkészíti az ügyfél böngészőjébe való küldéshez.

    ```
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

7. Közvetlenül az előző kód után adja hozzá az alábbi **GetImage** metódust az **ImageService** osztályban egy, a képet tartalmazó HTTP-üzenet visszaadásához.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Ez a megvalósítás a **MemoryStream** segítségével kéri le a képet, és előkészíti a böngészőbe történő streameléshez. Nullánál indítja el a streamelési pozíciót, jpeg-ként deklarálja a stream tartalmát, valamint streameli az információt.

8. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre.

### A konfiguráció meghatározása a webszolgáltatás Service Buson való futtatásához

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán az **App.config** fájlra a Visual Studio-szerkesztőben való megnyitásához.

    Az **App.config** fájl hasonlít egy WCF-konfigurációs fájlra, valamint tartalmazza a szolgáltatás nevét, végpontját (azaz azt a helyet, amelyet a Service Bus közzétesz az ügyfeleknek és a gazdáknak az egymással való kommunikációhoz) és kötését (a kommunikációhoz használt protokoll típusát). A fő különbség itt az, hogy a konfigurált szolgáltatásvégpont egy [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) kötésre hivatkozik, amely nem része a .NET-keretrendszernek.

2. A `<system.serviceModel>` XML-elem egy WCF-elem, amely egy vagy több szolgáltatást határoz meg. Itt a szolgáltatás nevének és végpontjának meghatározására szolgál. A `<system.serviceModel>` elem aljánál (de még a `<bindings>` elemen belül), adjon hozzá egy, az alábbi tartalommal rendelkező `<system.serviceModel>` elemet. Ez határozza meg az alkalmazásban használt kötéseket. Meghatározhat több kötést, de ez az oktatóanyag csak egyet határoz meg.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Ez a lépés meghatároz egy Service Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) kötést, ahol a **relayClientAuthenticationType** beállítása **None** (Nincs). Ez a beállítás jelöli, ha egy, a kötést használó végpont nem igényel ügyfél-hitelesítőt.

3. A `<bindings>` elem után adjon hozzá egy `<services>` elemet. A kötésekhez hasonlóan megadhat több szolgáltatást is egyetlen konfigurációs fájlon belül. Ez az oktatóanyag azonban csak egyet ad meg.

    ```
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

    Ez a lépés konfigurál egy szolgáltatást, amely a korábban meghatározott alapértelmezett **webHttpRelayBinding** elemet használja. Használja az alapértelmezett **sbTokenProvider** elemet is, amely a következő lépésben lesz meghatározva.

4. A `<services>` elem után hozzon létre egy `<behaviors>` elemet az alábbi tartalommal. A SAS_KEY értéket cserélje le a [klasszikus Azure portálra][] az 1. lépésben lekért *közös hozzáférésű jogosultságkód* (SAS) kulcsára.

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
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

5. Még mindig az App.config fájlban, az `<appSettings>` elemben cserélje le a teljes kapcsolati karakterlánc értékét a korábban a portálról beszerzett kapcsolati karakterláncéra. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. A teljes megoldás létrehozásához a **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre.

### Példa

Az alábbi kód bemutatja a szerződés és a szolgáltatás megvalósítását egy REST-alapú szolgáltatáshoz, amely a **WebHttpRelayBinding** kötés használatával fut a Service Buson.

```
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


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

A következő példa a szolgáltatáshoz társított App.config fájlt mutatja be.

```
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
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
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
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## 4. lépés: A Service Busszal használni kívánt REST-alapú WCF szolgáltatás működtetése

Ez a lépés leírja egy webszolgáltatás futtatását a Service Buson egy konzolalkalmazás használatával. Az ebben a lépésben írt kód teljes listája megtalálható az eljárást követő példában.

### Alapszintű cím létrehozása a szolgáltatáshoz

1. A `Main()` függvény deklarációjában hozzon létre egy változót a Service Bus projekt névterének tárolásához. Győződjön meg arról, hogy a `yourNamespace` helyett a korábban létrehozott szolgáltatásnévtér nevét adja meg.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    A Service Bus a névtér nevét használva létrehoz egy egyedi URI-t.

2. Hozzon létre egy `Uri`-példányt a névtéren alapuló szolgáltatás alapszintű címéhez.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### A webszolgáltatásgazda létrehozása és konfigurálása

- Hozza létre a webszolgáltatás gazdáját a szakaszban korábban létrehozott URI-cím használatával.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    A szolgáltatásgazda az a WCF-objektum, amely a gazdaalkalmazást példányosítja. Ez a példa továbbítja a létrehozni kívánt gazda típusát (**ImageService**), valamint a címet, ahol közzé kívánja tenni a gazdaalkalmazást.

### A webszolgáltatásgazda futtatása

1. Nyissa meg a szolgáltatást.

    ```
    host.Open();
    ```
    A szolgáltatás jelenleg fut.

2. Megjelenít egy üzenetet, amely jelzi, hogy a szolgáltatás fut, valamint a szolgáltatás leállításának módját.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Ha kész, zárja be a szolgáltatásgazdát.

    ```
    host.Close();
    ```

## Példa

Az alábbi példa tartalmazza a szolgáltatási szerződést és a megvalósítását az oktatóanyag előző lépéseiből, és egy konzolalkalmazásban működteti a szolgáltatást. Fordítsa le az alábbi kódot egy ImageListener.exe nevű végrehajtható fájlba.

```
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

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
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

### A kód fordítása

A megoldás létrehozása után az alábbi lépéseket követve futtathatja az alkalmazást:

1. A szolgáltatás futtatásához nyomja le az **F5** billentyűt, vagy lépjen a végrehajtható fájlhoz (ImageListener\bin\Debug\ImageListener.exe). Nem állítsa le az alkalmazás futását, mert szükség van rá a következő lépés elvégzéséhez.

2. A kép megtekintéséhez másolja és illessze be a címet a parancssorból egy böngészőbe.

3. Ha kész van, a parancssori ablakban az **Enter** billentyűt lenyomva zárja be az alkalmazást.

## Következő lépések

Most, hogy létrehozott egy, a Service Bus Relay szolgáltatást használó alkalmazást, az alábbi cikkekből tudhat meg többet a továbbítón keresztüli üzenetcseréről:

- [Azure Service Bus architectural overview (Az Azure Service Bus architektúrájának áttekintése)](service-bus-fundamentals-hybrid-solutions.md#relays)

- [A Service Bus Relay szolgáltatás használata](service-bus-dotnet-how-to-use-relay.md)

[klasszikus Azure portálra]: http://manage.windowsazure.com


<!--HONumber=Jun16_HO2-->


