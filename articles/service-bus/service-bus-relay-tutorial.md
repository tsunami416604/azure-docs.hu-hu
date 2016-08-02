<properties 
    pageTitle="A Service Bus továbbítón keresztüli üzenetcsere oktatóanyaga | Microsoft Azure"
    description="Létrehozhat egy Service Bus-ügyfélalkalmazást és -szolgáltatást a Service Bus továbbítón keresztüli üzenetcsere használatával."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/17/2016"
    ms.author="sethm" />

# A Service Bus-továbbítón keresztüli üzenetcsere oktatóanyaga

Ez az oktatóanyag leírja, hogyan hozhat létre egy egyszerű Service Bus-ügyfélalkalmazást és -szolgáltatást a Service Bus továbbítási képességeivel. A Service Bus [közvetítőalapú üzenettovábbítást](service-bus-messaging-overview.md#Brokered-messaging) alkalmazó megfelelő oktatóanyagért lásd a [Service Bus közvetítőalapú üzenettovábbítás .NET oktatóanyagát](service-bus-brokered-tutorial-dotnet.md).

Az oktatóanyag feldolgozása révén alaposan megismerheti a Service Bus-ügyfél és -szogáltatásalkalmazás létrehozásához szükséges lépéseket. Akárcsak a WCF megfelelők esetében, a szolgáltatás egy olyan konstrukció, amely közzétesz egy vagy több végpontot, azok pedig mind közzétesznek egy vagy több szolgáltatási műveletet. A szolgáltatások végpontja megad egy címet, ahol a szolgáltatás megtalálható, egy kötést, amely tartalmazza az információkat, amelyeket az ügyfélnek kommunikálnia kell a szolgáltatás felé, valamint egy szerződést, amely meghatározza a szolgáltatás által az ügyfeleknek nyújtott funkciókat. A fő különbség a WCF- és a Service Bus-szolgáltatások között az, hogy a végpont nem helyben, a számítógépen, hanem a felhőben van közzétéve.

Miután a végére ért az oktatóanyag témaköreinek, rendelkezni fog egy futó szolgáltatással, valamint egy, a szolgáltatás műveleteinek meghívására képes ügyféllel. Az első témakör egy fiók beállítását ismerteti. A következő lépések leírják, hogyan határozhat meg egy szerződést használó szolgáltatást, hogyan valósíthatja meg a szolgáltatást, valamint hogyan konfigurálhatja a szolgáltatást a kódban. Azt is leírják, hogyan működtethető és futtatható a szolgáltatás. A létrejött szolgáltatás próbaüzemben működik, és az ügyfél és a szolgáltatás ugyanazon a számítógépen fut. A szolgáltatást kód vagy egy konfigurációs fájl segítségével is konfigurálhatja.

Az utolsó három lépés azt írja le, hogy hogyan hozhat létre egy ügyfélalkalmazást, hogyan konfigurálhatja azt, valamint hogyan hozhat létre és használhat olyan ügyfélalkalmazásokat, amelyek hozzáférnek a gazdagép funkcióihoz.

A szakasz mindegyik témaköre feltételezi, hogy fejlesztési környezetként a Visual Studiót használja.

## Fiók létrehozása

Az első lépés egy Service Bus-szolgáltatásnévtér létrehozása, valamint egy közös hozzáférésű jogosultságkód (SAS-) kulcs beszerzése. A névtér egy alkalmazáshatárt biztosít a Service Buson keresztül közzétett minden alkalmazáshoz. A névtér és a SAS-kulcs együttes használata hitelesítő adatokat biztosít a Service Bus számára, amellyel hitelesíti a hozzáférést egy alkalmazáshoz.

1. Névtér létrehozásához látogasson el a [klasszikus Azure portálra][]. A bal oldalon kattintson a **Service Bus** elemre, majd kattintson a **Létrehozás** elemre. Adja meg a névtér nevét, fogadja az alapértelmezett értéket minden más helyen, majd kattintson az OK pipára.

    >[AZURE.NOTE] Nem kell ugyanazt a névteret használnia az ügyfélhez és a szolgáltatásalkalmazásokhoz is.

    ![][4]

1. A portál fő ablakában kattintson az előző lépésben létrehozott névtér nevére.

2. Kattintson a **Konfigurálás** lapra a névtérhez tartozó alapértelmezett megosztott elérési házirendek és kulcsok megtekintéséhez.

    ![][1]

3. Jegyezze fel a **RootManageSharedAccessKey** házirend elsődleges kulcs értékét, vagy másolja a vágólapra. Ezt az értéket az oktatóanyag későbbi részében fogja használni.

## A Service Busszal használni kívánt WCF szolgáltatási szerződés megadása

A szolgáltatási szerződés megadja, milyen műveleteket (webszolgáltatás-terminológia a metódusokhoz és függvényeket) támogat a szolgáltatás. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútumot minden felületre, az [OperationContractAttribaute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútumot pedig minden műveletre alkalmazni kell. Ha egy felület egy metódusa rendelkezik a [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútummal, de nem rendelkezik az [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútummal, nem lesz közzétéve. A feladatok kódja megtalálható az eljárást követő példában. A szerződések és szolgáltatások részletesebb leírása a WCF-dokumentáció [Designing and Implementing Services](https://msdn.microsoft.com/library/ms729746.aspx) (Szolgáltatások tervezése és megvalósítása) témakörében található.

### Service Bus szerződés létrehozása felülettel

1. Nyissa meg a Visual Studiót rendszergazdaként. Ehhez a **Start** menüben kattintson a jobb gombbal a programra, majd válassza a **Futtatás rendszergazdaként** parancsot.

2. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **File** (Fájl) menüre, és válassza a **New** (Új), majd a **Project** (Projekt) elemet. A **New Project** (Új projekt) párbeszédpanelen kattintson a **Visual C#** elemre (ha a **Visual C#** nem jelenik meg, keresse meg az **Other Languages** (Más nyelvek) területen). Kattintson a **Console Application** (Konzolalkalmazás) sablonra, és adja neki az **EchoService** nevet. A projekt létrehozásához kattintson az **OK** gombra.

    ![][2]

3. Telepítse a Service Bus NuGet-csomagot. Ez a csomag automatikusan hivatkozásokat ad a Service Bus-könyvtárakhoz, valamint a WCF **System.ServiceModel** névtérhez. A [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A Service Bus számos WCF-objektumot és -attribútumot használ a szolgáltatási szerződések meghatározására.

    A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése megoldáshoz) parancsra. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Ügyeljen arra, hogy a projekt neve ki legyen jelölve a **Version(s)** (Verzió(k)) mezőben. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

    ![][3]

3. A Solution Explorerben (Megoldáskezelőben) kattintson duplán a Program.cs fájlra a szerkesztőben való megnyitásához, ha még nincs megnyitva.

4. Adja hozzá a következő using utasításokat a fájl elejéhez:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Módosítsa a névtér alapértelmezett **EchoService** nevét a következőre: **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Ez az oktatóanyag a **Microsoft.ServiceBus.Samples** C# névteret használja, amely [A WCF-ügyfél konfigurálása](#configure-the-wcf-client) lépésben szereplő konfigurációs fájlban használt felügyelt típusú szerződés névtere. A minta létrehozásakor bármilyen névteret megadhat, az oktatóanyag azonban nem fog működni, ha ezután nem módosítja a szerződés és a szolgáltatás névtereit is ennek megfelelően az alkalmazáskonfigurációs fájlban. Az App.config fájlban megadott névtérnek egyeznie kell a C# fájlokban megadott névtérrel.

1. Közvetlenül a `Microsoft.ServiceBus.Samples` névtér-deklaráció után, de a névtéren belül, adjon meg egy új, `IEchoContract` nevű felületet, és alkalmazza a `ServiceContractAttribute` attribútumot a felületre a **http://samples.microsoft.com/ServiceModel/Relay/** névtérértékkel. A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke ehelyett egyedi azonosítóként van használatban ehhez a szerződéshez. A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] A szolgáltatási szerződés névtere általában tartalmaz egy elnevezési sémát, amely tartalmazza a verzióinformációkat. Ha a verzióinformációk szerepelnek a szolgáltatási szerződés névterében, a szolgáltatások képesek elkülöníteni a nagyobb módosításokat egy új szolgáltatási szerződés új névtérrel való meghatározása, valamint egy új végponton való megjelenítése révén. Így az ügyfelek használhatják továbbra is a régi szolgáltatási szerződést anélkül, hogy frissíteniük kellene. A verzióinformációk dátumot vagy buildszámot tartalmazhatnak. További információ: [Service Versioning](http://go.microsoft.com/fwlink/?LinkID=180498) (Szolgáltatás verziószámozása). A jelen oktatóanyag esetén a szolgáltatási szerződés elnevezési sémája nem tartalmazza a verzióinformációkat.

1. Az `IEchoContract` felületen belül deklaráljon egy metódust az `IEchoContract` szerződés által a felületen közzétett egyetlen művelethez, valamint alkalmazza az `OperationContractAttribute` attribútumot a nyilvános Service Bus szerződés részeként közzétenni kívánt metódusra.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Közvetlenül az `IEchoContract` felület deklarációja után deklaráljon egy csatornát, amely örökli az `IEchoContract` és az `IClientChannel` felületek tulajdonságait is, ahogy az itt látható:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    A csatorna egy olyan WCF-objektum, amelyen keresztül a gazda és az ügyfél információkat adnak át egymásnak. Később kódot ír majd a csatornára, hogy az echo utasítás használatával kommunikálja az információkat a két alkalmazás között.

1. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre, vagy nyomja meg a **Ctrl+Shift+B** billentyűkombinációt az eddigi munkája pontosságának ellenőrzéséhez.

### Példa

Az alábbi kód egy Service Bus szerződést meghatározó alapszintű felületet mutat be.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Most, hogy létrejött a felület, megvalósíthatja azt.

## A WCF szerződés megvalósítása a Service Bus használatára

A Service Bus Relay létrehozásához először létre kell hoznia a szerződést, amelyet egy felület használatával határozhat meg. A felület létrehozásáról az előző lépésben találhat további információkat. A következő lépés a felület megvalósítása. Ebbe beletartozik egy `EchoService` nevű osztály létrehozása, amely megvalósítja a felhasználó által megadott `IEchoContract` felületet. A felület megvalósítása után egy App.config konfigurációs fájl segítségével konfigurálhatja azt. A konfigurációs fájl tartalmazza az alkalmazáshoz szükséges információkat, például a szolgáltatás nevét, a szerződés nevét, valamint a Service Busszal való kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód megtalálható az eljárást követő példában. A szolgáltatási szerződések megvalósításáról a WCF-dokumentáció [Implementing Service Contracts](https://msdn.microsoft.com/library/ms733764.aspx) (Szolgáltatási szerződések megvalósítása) szakaszában található átfogó ismertetés.

1. Hozzon létre egy új, `EchoService` nevű osztályt közvetlenül az `IEchoContract` felület meghatározása után. Az `EchoService` osztály megvalósítja az `IEchoContract` felületet. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban található, mint a felületdefiníció és a `Main` metódus.

1. Alkalmazza a [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) attribútumot az `IEchoContract` felületre. Az attribútum megadja a szolgáltatás nevét és névterét. Ezután az `EchoService` osztály a következőképp jelenik meg:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Valósítsa meg az `IEchoContract` felületen meghatározott `Echo` metódust az `EchoService` osztályban. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre az eddigi munkája pontosságának ellenőrzéséhez.

### A szolgáltatásgazda konfigurációjának meghatározása

1. A konfigurációs fájl nagyon hasonlít a WCF konfigurációs fájlra. Tartalmazza a szolgáltatás nevét, végpontját (azaz azt a helyet, amelyet a Service Bus közzétesz az ügyfeleknek és a gazdáknak az egymással való kommunikációhoz) és kötését (a kommunikációhoz használt protokoll típusát). A fő különbség az, hogy ez a konfigurált szolgáltatásvégpont egy [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) kötésre hivatkozik, amely nem része a .NET-keretrendszernek. A [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) egy, a Service Bus által meghatározott kötés.

1. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán az App.config fájlra a Visual Studio-szerkesztőben való megnyitásához.

2. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra. 

1. A `<system.serviceModel>` címkéken belül adjon hozzá egy `<services>` elemet. Meghatározhat több Service Bus-alkalmazást is egyetlen konfigurációs fájlban. Ez az oktatóanyag viszont csak egyet határoz meg.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. A `<services>` elemen belül adjon hozzá egy `<service>` elemet a szolgáltatás nevének meghatározásához.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. A `<service>` elemen belül határozza meg a végpont szerződés helyét, valamint a végpont kötésének típusát.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    A végpont meghatározza, hogy az ügyfél hol keresi majd a gazdaalkalmazást. Az oktatóanyag később ezzel a lépéssel hoz létre egy URI-t, amely teljesen közzéteszi a gazdagépet a Service Buson keresztül. A kötés deklarálja, hogy a TCP protokollt használjuk a Service Busszal való kommunikációra.

1. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre a munkája pontosságának ellenőrzéséhez.

### Példa

A következő kód a szolgáltatási szerződés megvalósítását mutatja be.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

A következő kód a szolgáltatásgazdához társított App.config fájl alapszintű formátumát mutatja be.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Alapszintű webszolgáltatás üzemeltetése és futtatása a Service Bus szolgáltatással való regisztrációhoz

Ez a lépés leírja alapszintű Service Bus szolgáltatás futtatását.

### A Service Bus-hitelesítő adatok létrehozása

1. A `Main()` metódusban hozzon létre két változót, amelyben a konzolablakból beolvasott névtér és SAS-kulcs tárolható.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Az SAS-kulcs később a Service Bus-projekt elérésére szolgál majd. A névteret a rendszer paraméterként átadja a `CreateServiceUri` számára szolgáltatás URI létrehozásához.

4. Egy [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) objektum segítségével deklarálhatja, hogy SAS-kulcsot fog használni hitelesítési típusként. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### Alapszintű cím létrehozása a szolgáltatáshoz

1. Az előző lépésben felvett kód után hozzon létre egy `Uri` példányt a szolgáltatás alapszintű címéhez. Ez az URI megadja a Service Bus-sémát, a névteret és a szolgáltatási felület útvonalát.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    Az „sb” a Service Bus-séma rövidítése, és azt jelzi, hogy a TCP protokollt használjuk. Ez már korábban is jelezve volt a konfigurációs fájlban, amikor kötésként a [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) lett megadva.
    
    Az oktatóanyaghoz az URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### A szolgáltatásgazda létrehozása és konfigurálása

1. A csatlakozási mód beállítása legyen `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    A csatlakozási mód írja le azt a protokollt, amelyet a szolgáltatás használ a Service Bus szolgáltatással való kommunikációhoz, lehet HTTP vagy TCP. Az alapértelmezett `AutoDetect` beállítás esetén a szolgáltatás TCP protokollal próbál meg kapcsolódni a Service Bushoz, ha pedig az nem érhető el, akkor HTTP protokollal. Vegye figyelembe, hogy ez nem azonos a szolgáltatás által az ügyfél-kommunikációhoz megadott protokollal. Azt a protokollt az alkalmazott kötés határozza meg. A szolgáltatás használhatja például a [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) kötést, amely megadja, hogy a végpontja (amely a Service Bus szolgáltatáson van közzétéve) HTTP segítségével kommunikál az ügyfelekkel. Ugyanez a szolgáltatás megadhatja a **ConnectivityMode.AutoDetect** beállítást is, így a szolgáltatás TCP-n keresztül kommunikál a Service Bus szolgáltatással.

1. Hozza létre a szolgáltatás gazdáját a szakaszban korábban létrehozott URI segítségével.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    A szolgáltatásgazda a WCF-objektum, amely a szolgáltatást példányosítja. Ebben a lépésben adja át számára a létrehozni kívánt szolgáltatástípust (`EchoService` típus), valamint a címet is, amelyen közzé szeretné tenni a szolgáltatást.

1. A Program.cs fájl elejéhez adjon hozzá a következőkre mutató hivatkozásokat: [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) és [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Újra a `Main()` metódusban konfigurálja a végpontot a nyilvános hozzáférés engedélyezésére.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ez a lépés a Service Bus-projektjéhez tartozó ATOM-hírcsatornájának megvizsgálásával tájékoztatja a Service Bust, hogy az alkalmazás nyilvánosan elérhető. Ha a **DiscoveryType** beállítása **private**, attól még egy ügyfél elérheti a szolgáltatást. Azonban a szolgáltatás nem jelenik meg, amikor keresi a Service Bus-névteret. Az ügyfélnek ehhez már ismernie kell a végpont elérési útját.

1. Alkalmazza a szolgáltatás hitelesítő adatait az App.config fájlban meghatározott szolgáltatásvégpontokra:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Ahogyan az az előző lépésben elhangzott, van mód deklarálni több szolgáltatást és végpontot a konfigurációs fájlban. Ha így tett, ez a kód végighalad a konfigurációs fájlon, és megkeres minden végpontot, amelyre alkalmaznia kell a hitelesítő adatokat. Ebben az oktatóanyagban viszont a konfigurációs fájl csak egy végponttal rendelkezik.

### A szolgáltatásgazda megnyitása

1. Nyissa meg a szolgáltatást.

    ```
    host.Open();
    ```

1. Tájékoztassa a felhasználót arról, hogy a szolgáltatás fut, és magyarázza el, hogyan lehet leállítani.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Ha kész, zárja be a szolgáltatásgazdát.

    ```
    host.Close();
    ```

1. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt a projekt felépítéséhez.

### Példa

Az alábbi példa tartalmazza a szolgáltatási szerződést és a megvalósítását az oktatóanyag előző lépéseiből, és egy konzolalkalmazásban működteti a szolgáltatást. Fordítsa le az alábbiakat egy EchoService.exe nevű végrehajtható fájlba.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## WCF-ügyfél létrehozása a szolgáltatási szerződéshez

A következő lépés egy alapszintű Service Bus-ügyfélalkalmazás létrehozása, valamint a későbbi lépésekben megvalósítandó szolgáltatási szerződés meghatározása. Figyelje meg, hogy sok lépés hasonlít a szolgáltatás létrehozásának lépéseire: a szerződés meghatározása, az App.config fájl szerkesztése, hitelesítő adatok használata a Service Bus szolgáltatáshoz való csatlakozáshoz stb. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. Hozzon létre egy új projektet az ügyfél jelenlegi Visual Studio megoldásában az alábbiakat követve:
    1. A Solution Explorerben (Megoldáskezelőben) a szolgáltatást tartalmazó megoldásban kattintson a jobb gombbal az aktuális megoldásra (ne a projektre), és kattintson az **Add** (Hozzáadás) lehetőségre. Ezután kattintson a **New Project** (Új projekt) gombra.
    2. Az **Add New Project** (Új projekt hozzáadása) párbeszédpanelen kattintson a **Visual C#** elemre (ha a **Visual C#** nem jelenik meg, keresse meg az **Other Languages** (Más nyelvek) területen), válassza ki a **Console Application** (Konzolalkalmazás) sablont, és nevezze el **EchoClient** néven.
    3. Kattintson az **OK** gombra.
<br />

1. A Solution Explorerben (Megoldáskezelőben) kattintson duplán az **EchoClient** projekt Program.cs fájljára a szerkesztőben való megnyitásához, ha még nincs megnyitva.

1. Módosítsa a névtér alapértelmezett `EchoClient` nevét a következőre: `Microsoft.ServiceBus.Samples`.

1. Telepítse a [Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus). A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **EchoClient** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

    ![][3]

1. Adjon hozzá egy `using` utasítást a Program.cs fájlban található [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) névtérhez. 

    ```
    using System.ServiceModel;
    ```

1. Adja hozzá a szolgáltatási szerződés meghatározását a névtérhez, ahogyan az az alábbi példában látható. Ügyeljen arra, hogy a meghatározás azonos legyen a **Szolgáltatás** projektben használt meghatározással. Ezt a kódot a `Microsoft.ServiceBus.Samples` névtér tetején adja hozzá.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt az ügyfél felépítéséhez.

### Példa

Az alábbi kód megjeleníti a Program.cs fájl aktuális állapotát az EchoClient projektben.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## A WCF-ügyfél konfigurálása

Ebben a lépésben létrehozza az App.config fájlt egy alapszintű ügyfélalkalmazáshoz, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. Ez az App.config fájl határozza meg a szerződést, a kötést és a végpont nevét. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. A Solution Explorerben (Megoldáskezelőben) az **EchoClient** projektben kattintson duplán az **App.config** fájlra a fájl a Visual Studio-szerkesztőben való megnyitásához.

2. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.

1. A system.serviceModel elemen belül adjon hozzá egy `<client>` elemet.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ez a lépés deklarálja, hogy WCF stílusú ügyfélalkalmazást határoz meg.

1. A `client` elemen belül határozza meg a nevet, a szerződést és a kötéstípust a végponthoz.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ez a lépés határozza meg a végpont nevét, a szolgáltatásban meghatározott szerződést, valamint azt, hogy az ügyfélalkalmazás TCP-vel kommunikál a Service Bus szolgáltatással. A következő lépés a végpont neve használatával ezt a végpont-konfigurációt összekapcsolja a szolgáltatás URI-jával.

1. Kattintson a **File** (Fájl), majd a **Save All** (Az összes mentése) lehetőségre.

## Példa

A következő kód az Echo ügyfél App.config fájlját mutatja.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## A WCF-ügyfél megvalósítása a Service Bus meghívásához

Ebben a lépésben megvalósít egy alapszintű ügyfélalkalmazást, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. Az ügyfél is majdnem ugyanazokat a műveletet hajtja végre a Service Bus eléréséhez, mint a szolgáltatás:

1. Beállítja a csatlakozási módot.
1. Létrehozza a gazdaszolgáltatás helyét megadó URI-t.
1. Megadja a biztonsági hitelesítő adatokat.
1. Alkalmazza a hitelesítő adatokat a kapcsolatra.
1. Megnyitja a kapcsolatot.
1. Végrehajtja az alkalmazásspecifikus feladatokat.
1. Bezárja a kapcsolatot.

Az egyik fő különbség azonban az, hogy az ügyfélalkalmazás egy csatorna segítségével csatlakozik a Service Bus szolgáltatáshoz, a szolgáltatás pedig a **ServiceHost** meghívásával. A feladatokhoz használt kód megtalálható az eljárást követő példában.

### Ügyfélalkalmazás megvalósítása

1. A csatlakozási mód beállítása legyen **AutoDetect**. Adja hozzá a következő kódot az **EchoClient** alkalmazás `Main()` metódusában.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Határozzon meg változókat, amelyek a konzolról beolvasott szolgáltatásnévtér és SAS-kulcs értékeit tárolják.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Hozza létre a gazdagép helyét meghatározó URI-t a Service Bus-projektben.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Hozza létre a hitelesítő objektumot a szolgáltatásnévtér végpontjához.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Hozza létre az App.config fájlban leírt konfigurációt betöltő csatornagyárat.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    A csatornagyár egy WCF-objektum, amely létrehoz egy csatornát, amelyen keresztül a szolgáltatás és az ügyfélalkalmazások kommunikálnak.

1. Alkalmazza a Service Bus-hitelesítő adatokat.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Hozza létre és nyissa meg a csatornát a szolgáltatás felé.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Írja meg az Echo alapszintű felhasználói felületét és funkcióit.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Vegye figyelembe, hogy a kód a szolgáltatás proxyjaként használja a csatornaobjektum példányát.

1. Zárja be a csatornát, és zárja be a gyárat.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## Az alkalmazások futtatása

1. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt a megoldás felépítéséhez. Ez felépíti az előző lépésekben létrehozott ügyfélprojektet és szolgáltatásprojektet.

2. Az ügyfélalkalmazás futtatása előtt győződjön meg arról, hogy a szolgáltatásalkalmazás fut. A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **EchoService** megoldásra, majd kattintson a **Properties** (Tulajdonságok) elemre.

3. A megoldás tulajdonságait tartalmazó párbeszédpanelen kattintson a **Startup Project** (Kezdőprojekt), majd a **Multiple startup projects** (Több kezdőprojekt) gombra. Győződjön meg arról, hogy a lista első eleme az **EchoService**. 

4. Az **Action** (Művelet) mezőt állítsa az **EchoService** és az **EchoClient** projekt esetén is **Start** (Indítás) értékűre.

    ![][5]

5. Kattintson a **Project Dependencies** (Projektfüggőségek) lehetőségre. A **Projects** (Projektek) mezőben válassza az **EchoClient** elemet. Győződjön meg arról, hogy a **Depends on** (Függ:) mezőben az **EchoService** be van jelölve.

    ![][6]

6. Kattintson az **OK** gombra a **Properties** (Tulajdonságok) párbeszédpanel bezárásához.

7. Mindkét projekt futtatásához nyomja le az **F5** billentyűt.

8. Mindkét konzolablak megnyílik, és a rendszer a névtér nevének megadását kéri. A szolgáltatásnak először futnia kell, ezért adja meg a névteret az **EchoService** konzolablakában, majd nyomja le az **Enter** billentyűt.

9. Ezután a rendszer az SAS-kulcs megadását kéri. Adja meg az SAS-kulcsot, és nyomja le az ENTER billentyűt.

    Az alábbiakban egy példa kimenet látható a konzolablakból. Vegye figyelembe, hogy az itt megadott értékek csupán példák.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    A szolgáltatásalkalmazás a konzolablakba írja a címet, amelyen figyel, ahogyan az az előbbi példában is látható.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Az **EchoClient** konzolablakában adja meg a korábban a szolgáltatásalkalmazáshoz megadott adatokat. Az előző lépéseket követve adja meg az ügyfélalkalmazáshoz ugyanezeket a szolgáltatásnévtér és SAS-kulcs értékeket.

11. Az értékek megadása után az ügyfél megnyit egy csatornát a szolgáltatás felé, és szövegbevitelre kéri, ahogyan az az alábbi példa konzolkimenetben látható.

    `Enter text to echo (or [Enter] to exit):` 

    Adjon meg szöveget, amelyet a szolgáltatásalkalmazásba szeretne küldeni, majd nyomja le az Enter billentyűt. Ezt a szöveget az Echo szolgáltatásműveleten keresztül küldi el a rendszer a szolgáltatásnak, és megjelenik a szolgáltatás konzolablakában, ahogyan az az alábbi példa kimenetben látható.

    `Echoing: My sample text`

    Az ügyfélalkalmazás fogadja az `Echo` művelet visszaadott értékét, amely az eredeti szöveg, és a konzolablakba írja. Az alábbi egy példa kimenet az ügyfél konzolablakából.

    `Server echoed: My sample text`

12. Ezzel a módszerrel folytathatja szöveges üzenetek küldését az ügyfélről a szolgáltatásba. Ha kész van, a két alkalmazás befejezéséhez nyomja le az Enter billentyűt az ügyfél és a szolgáltatás konzolablakában is.

## Példa

Az alábbi példa bemutatja, hogyan hozhat létre egy ügyfélalkalmazást, hogyan hívhatja meg a szolgáltatás műveleteit, és hogyan zárhatja be az ügyfelet a művelet meghívásának befejeződése után.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## Következő lépések

Ez az oktatóanyag bemutatta, hogyan hozhat létre egy Service Bus-ügyfélalkalmazást és szolgáltatást a Service Bus továbbítási képességeivel. A Service Bus [közvetítőalapú üzenettovábbítást](service-bus-messaging-overview.md#Brokered-messaging) alkalmazó hasonló oktatóanyagért lásd a [Service Bus közvetítőalapú üzenettovábbítás .NET oktatóanyagát](service-bus-brokered-tutorial-dotnet.md).

A Service Busról a következő témakörökben talál további információt.

- [Service Bus messaging overview (A Service Bus üzenetkezelésének áttekintése)](service-bus-messaging-overview.md)
- [Service Bus fundamentals (A Service Bus alapjai)](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-architektúra](service-bus-architecture.md)

[klasszikus Azure portálra]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png



<!--HONumber=Jun16_HO2-->


