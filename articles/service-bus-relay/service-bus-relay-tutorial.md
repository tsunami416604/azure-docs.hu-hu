---
title: Az Azure Relay használatával elérhetővé tesz egy előzetes WCF REST-szolgáltatást az ügyfelek számára
description: Ez az oktatóanyag ismerteti, hogyan teheti elérhetővé a helyszíni WCF REST-szolgáltatást egy külső ügyfél nek az Azure WCF-továbbító használatával.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 551c8e662669737d9d074a69cb03d6060ab87ad5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76513082"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Oktatóanyag: Egy helyszíni WCF REST-szolgáltatás elérhetővé tévő része a külső ügyfél számára az Azure WCF-továbbító használatával

Ez az oktatóanyag ismerteti, hogyan hozhat létre egy WCF-továbbító ügyfélalkalmazást és -szolgáltatást az Azure Relay használatával. A Service Bus [üzenetküldést](../service-bus-messaging/service-bus-messaging-overview.md)használó hasonló oktatóanyagról az [Első lépések a Service Bus-várólistákkal kapcsolatban](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Az oktatóanyag segítségével megismerheti a WCF-továbbító ügyfél- és szolgáltatásalkalmazás létrehozásának lépéseit. Az eredeti WCF-megfelelőkhöz hasonlóan a szolgáltatás is egy olyan konstrukció, amely egy vagy több végpontot tesz elérhetővé. Minden végpont egy vagy több szolgáltatási műveletet tesz elérhetővé. A szolgáltatások végpontja megad egy címet, ahol a szolgáltatás megtalálható, egy kötést, amely tartalmazza az információkat, amelyeket az ügyfélnek kommunikálnia kell a szolgáltatás felé, valamint egy szerződést, amely meghatározza a szolgáltatás által az ügyfeleknek nyújtott funkciókat. A fő különbség a WCF és a WCF-továbbító között az, hogy a végpont a felhőben van kitéve, nem pedig helyileg a számítógépen.

Miután végigdolgozta az oktatóanyag szakaszainak sorrendjét, egy futó szolgáltatással fog rendelkezni. Emellett egy ügyfél, amely meghívhatja a szolgáltatás műveleteit. 

Az oktatóanyagban a következő feladatokat végezze el:

> [!div class="checklist"]
>
> * Telepítse az oktatóanyag előfeltételeit.
> * Hozzon létre egy továbbító névteret.
> * WcF szervizszerződés létrehozása.
> * A WCF-szerződés végrehajtása.
> * A WCF szolgáltatás üzemeltetése és futtatása a továbbítási szolgáltatással való regisztrációhoz.
> * Hozzon létre egy WCF-ügyfelet a szervizszerződéshez.
> * Konfigurálja a WCF-ügyfelet.
> * Valósítsa meg a WCF-ügyfelet.
> * Futtassa az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2019-et használják.
* Azure SDK a .NET.Azure SDK for .NET. Telepítse az [SDK letöltések oldalon](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Továbbító névtér létrehozása

Az első lépés egy névtér létrehozása és egy [SAS-kulcs](../service-bus-messaging/service-bus-sas.md) beszerzése. A névtér alkalmazáshatárt biztosít a továbbító szolgáltatáson keresztül elérhetővé tett minden egyes alkalmazásszámára. A rendszer automatikusan létrehoz egy SAS-kulcsot a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és a SAS-kulcs kombinációja biztosítja az Azure hitelesítő adatait az alkalmazáshoz való hozzáférés hitelesítéséhez.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>WcF szervizszerződés definiálása

A szervizszerződés meghatározza, hogy a szolgáltatás milyen műveleteket támogat. A műveletek webszolgáltatás-módszerek vagy -függvények. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútumot minden felületre, az [OperationContractAttribaute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútumot pedig minden műveletre alkalmazni kell. Ha egy olyan felületen lévő metódus, amely rendelkezik a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútummal, nem rendelkezik az [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútummal, akkor ez a metódus nem lesz elérhető. A feladatok kódja megtalálható az eljárást követő példában. A szerződések és szolgáltatások nagyobb megtárgyalását a [Szolgáltatások tervezése és megvalósítása témakörben tetszése látható.](/dotnet/framework/wcf/designing-and-implementing-services)

### <a name="create-a-relay-contract-with-an-interface"></a>Továbbítási szerződés létrehozása felülettel

1. Indítsa el a Microsoft Visual Studio alkalmazást rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, és válassza a **Futtatás rendszergazdaként parancsot.**
1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.
1. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework) (C# )** lehetőséget, majd a **Tovább**gombot.
1. Nevezze el a *projektet az EchoService projektnek,* és válassza **a Létrehozás gombot.**

   ![Konzolalkalmazás létrehozása][2]

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése parancsot.** A **NuGet csomagkezelőben**válassza a **Tallózás**gombot, majd keresse meg a **WindowsAzure.ServiceBus**elemet. Válassza **a Telepítés**lehetőséget, és fogadja el a használati feltételeket.

    ![Service Bus csomag][3]

   Ez a csomag automatikusan hivatkozásokat ad `System.ServiceModel`hozzá a Service Bus-kódtárakhoz és a WCF-hez. A [System.ServiceModel](/dotnet/api/system.servicemodel) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A Service Bus számos WCF-objektumot és -attribútumot használ a szolgáltatási szerződések meghatározására.

1. A következő `using` állításokat a Program.cs tetejére *kell felsoraszasztani:*

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Módosítsa a névtér alapértelmezett `EchoService` nevét a következőre: `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Ez az oktatóanyag a `Microsoft.ServiceBus.Samples` C# névteret használja, amely a [WCF-ügyfél konfigurálása](#configure-the-wcf-client) szakasz konfigurációs fájljában használt szerződésalapú felügyelt típus névtere. A minta összeállításakor tetszőleges névteret megadhat. Az oktatóanyag azonban csak akkor fog működni, ha módosítja a szerződés és a szolgáltatás névtereit az alkalmazás konfigurációs fájljában. Az *App.config* fájlban megadott névtérnek meg kell egyeznie a C# fájlokban megadott névtérrel.
   >

1. Közvetlenül `Microsoft.ServiceBus.Samples` a névtér deklaráció után, de `IEchoContract` a névtéren belül definiáljon egy új nevű felületet, és alkalmazza az `ServiceContractAttribute` attribútumot a kapcsolatra, amelynek névtérértéke `https://samples.microsoft.com/ServiceModel/Relay/`. Illessze be a következő kódot a névtér deklaráció után:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke ehelyett egyedi azonosítóként van használatban ehhez a szerződéshez. A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

   > [!NOTE]
   > A szolgáltatási szerződés névtere általában tartalmaz egy elnevezési sémát, amely tartalmazza a verzióinformációkat. Ha a verzióinformációk szerepelnek a szolgáltatási szerződés névterében, a szolgáltatások képesek elkülöníteni a nagyobb módosításokat egy új szolgáltatási szerződés új névtérrel való meghatározása, valamint egy új végponton való megjelenítése révén. Ily módon az ügyfelek frissítés nélkül is használhatják a régi szervizszerződést. A verzióinformációk dátumot vagy buildszámot tartalmazhatnak. További információ: [Service Versioning](/dotnet/framework/wcf/service-versioning) (Szolgáltatás verziószámozása). Ebben az oktatóanyagban a szervizszerződés i. névtér elnevezési séma nem tartalmaz verzióadatokat.
   >

1. A `IEchoContract` felületen belül deklaráljon `IEchoContract` egy metódust az egyetlen `OperationContractAttribute` művelethez, amelyet a szerződés közzétakar a felületen, és alkalmazza az attribútumot arra a metódusra, amelyet a nyilvános WCF-továbbító szerződés részeként közzé szeretne tenni, az alábbiak szerint:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Közvetlenül az `IEchoContract` felület deklarációja után deklaráljon egy csatornát, amely örökli az `IEchoContract` és az `IClientChannel` felületek tulajdonságait is, ahogy az itt látható:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    A csatorna egy olyan WCF-objektum, amelyen keresztül a gazda és az ügyfél információkat adnak át egymásnak. Később kódot ír a csatornához, hogy visszhangozza a két alkalmazás közötti információkat.

1. Válassza a Build Solution **összeállítása** > **lehetőséget,** vagy válassza a Ctrl+Shift+B lehetőséget az eddigi munka pontosságának megerősítéséhez.

### <a name="example-of-a-wcf-contract"></a>Példa WCF-szerződésre

A következő kód egy egyszerű felületet mutat be, amely egy WCF-továbbító egyezményt határoz meg.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="implement-the-wcf-contract"></a>A WCF-szerződés végrehajtása

Az Azure-továbbító létrehozásához először hozzon létre egy felületet a szerződést. A kapcsolat létrehozásáról az előző szakaszban talál további információt. A következő eljárás megvalósítja a felületet. Ez a feladat magában `EchoService` foglalja egy osztály nevű, `IEchoContract` amely megvalósítja a felhasználó által definiált felületet. A kapcsolat megvalósítása után konfigurálja a kapcsolatot egy *App.config* konfigurációs fájl használatával. A konfigurációs fájl tartalmazza az alkalmazáshoz szükséges információkat. Ez az információ tartalmazza a szolgáltatás nevét, a szerződés nevét és a továbbító szolgáltatással való kommunikációhoz használt protokoll típusát. Az ezekhez a feladatokhoz használt kódot az eljárást követő példában található meg. A szervizszerződések megvalósításáról a [Szervizszerződések megvalósítása (Végrehajtási szerződések) (Szervizszerződések megvalósítása)](/dotnet/framework/wcf/implementing-service-contracts)témakörben tetszést.

1. Hozzon létre egy új, `EchoService` nevű osztályt közvetlenül az `IEchoContract` felület meghatározása után. Az `EchoService` osztály megvalósítja az `IEchoContract` felületet.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban található, mint a felületdefiníció és a `Main()` metódus.

1. Alkalmazza a [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) attribútumot az `IEchoContract` felületre. Az attribútum megadja a szolgáltatás nevét és névterét. Ezután az `EchoService` osztály a következőképp jelenik meg:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Valósítsa meg az `IEchoContract` felületen meghatározott `Echo` metódust az `EchoService` osztályban.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Válassza a > **Build Solution (Build megoldás** **összeállítása)** vagy a Ctrl+Shift+B lehetőséget.

### <a name="define-the-configuration-for-the-service-host"></a>A szolgáltatásgazda konfigurációjának meghatározása

A konfigurációs fájl hasonló a WCF konfigurációs fájlhoz. Ez magában foglalja a szolgáltatás nevét, végpont, és a kötés. A végpont az a hely, amelyet az Azure Relay elérhetővé teszi az ügyfelek és a gazdagépek számára az egymással való kommunikációhoz. A kötés a kommunikációhoz használt protokoll típusa. A fő különbség az, hogy ez a konfigurált szolgáltatásvégpont egy [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) kötésre hivatkozik, amely nem része a . [A NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) a szolgáltatás által meghatározott kötések egyike.

1. A **Megoldáskezelőben**kattintson duplán az **App.config** fájlra a fájl Visual Studio-szerkesztőben való megnyitásához.
1. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
1. A `<system.serviceModel>` címkéken belül adjon hozzá egy `<services>` elemet. Egyetlen konfigurációs fájlban több továbbítóalkalmazást is definiálhat. Ez az oktatóanyag viszont csak egyet határoz meg.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. A `<services>` elemen belül adjon hozzá egy `<service>` elemet a szolgáltatás nevének meghatározásához.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. A `<service>` elemen belül határozza meg a végpont szerződés helyét, valamint a végpont kötésének típusát.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    A végpont meghatározza, hogy az ügyfél hol keresi majd a gazdaalkalmazást. Később az oktatóanyag ezt a lépést használja egy URI-t, amely teljes mértékben elérhetővé teszi a gazdagép az Azure Relay-en keresztül. A kötés kijelenti, hogy a TCP protokollt használjuk a továbbító szolgáltatással való kommunikációhoz.

1. Válassza a Build Solution **összeállítása** > **lehetőséget,** vagy válassza a Ctrl+Shift+B lehetőséget az eddigi munka pontosságának megerősítéséhez.

### <a name="example-of-implementation-of-a-service-contract"></a>Példa a szolgáltatási szerződés végrehajtására

A következő kód a szolgáltatási szerződés megvalósítását mutatja be.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

A következő kód a szolgáltatásállomáshoz társított *App.config* fájl alapvető formátumát mutatja.

```xml
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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>A WCF szolgáltatás üzemeltetése és futtatása a továbbító szolgáltatással való regisztrációhoz

Ez a lépés ismerteti, hogyan kell futtatni egy Azure Relay szolgáltatás.

### <a name="create-the-relay-credentials"></a>A továbbítási hitelesítő adatok létrehozása

1. A `Main()` metódusban hozzon létre két változót, amelyben a konzolablakból beolvasott névtér és SAS-kulcs tárolható.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A SAS-kulcs később lesz használva a projekt eléréséhez. A névteret a rendszer paraméterként átadja a `CreateServiceUri` számára szolgáltatás URI létrehozásához.

1. [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) objektum használatával deklarálja, hogy egy SAS-kulcsot fog használni hitelesítő adattípusként. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>A szolgáltatás alapcímének létrehozása

Az előző szakaszban megadott kód után `Uri` hozzon létre egy példányt a szolgáltatás alapcíméhez. Ez az URI megadja a Service Bus-sémát, a névteret és a szolgáltatási felület útvonalát.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Az "sb" érték a Service Bus rendszer rövidítése. Ez azt jelzi, hogy tcp-t használunk protokollként. Ez a séma korábban a konfigurációs fájlban is szerepelt, amikor a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) kötésként lett megadva.

Az oktatóanyaghoz az URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>A szolgáltatásgazda létrehozása és konfigurálása

1. A beállítás `Main()`továbbra is a `AutoDetect`alkalmazásban működik, állítsa a kapcsolati módot a beállításra.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    A kapcsolódási mód azt a protokollt írja le, amelyet a szolgáltatás a továbbító szolgáltatással való kommunikációhoz használ; HTTP vagy TCP. Az alapértelmezett `AutoDetect`beállítás használatával a szolgáltatás megpróbál csatlakozni az Azure Relay tcp-n keresztül, ha elérhető, és HTTP, ha tcp nem érhető el. Ez az eredmény eltér a szolgáltatás által az ügyfélkommunikációhoz megadott protokolltól. Azt a protokollt az alkalmazott kötés határozza meg. Egy szolgáltatás például használhatja a [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) kötést, amely megadja, hogy a végpontja HTTP-n keresztül kommunikáljon az ügyfelekkel. Ugyanez a szolgáltatás `ConnectivityMode.AutoDetect` megadhatja, hogy a szolgáltatás kommunikál az Azure Relay tcp-n keresztül.

1. Hozza létre a szolgáltatás gazdáját a szakaszban korábban létrehozott URI segítségével.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    A szolgáltatásgazda a WCF-objektum, amely a szolgáltatást példányosítja. Itt adja át azt a szolgáltatástípust, `EchoService` amelyet létre szeretne hozni, egy típust, valamint azt a címet, amelyen a szolgáltatást elérhetővé szeretné tenni.

1. A *Program.cs* fájl tetején adjon hozzá hivatkozásokat a [System.ServiceModel.Description](/dotnet/api/system.servicemodel.description) és a [Microsoft.ServiceBus.Description fájlhoz.](/dotnet/api/microsoft.servicebus.description)

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Újra a `Main()` metódusban konfigurálja a végpontot a nyilvános hozzáférés engedélyezésére.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ez a lépés tájékoztatja a továbbító szolgáltatást arról, hogy az alkalmazás nyilvánosan megtalálható a projekt Atom feedjének vizsgálatával. Ha a `DiscoveryType` `private`beállítás, az ügyfél továbbra is hozzáférhet a szolgáltatáshoz. A szolgáltatás azonban nem jelenik meg, `Relay` amikor a névtérben keres. Az ügyfélnek ehhez már ismernie kell a végpont elérési útját.

1. Alkalmazza a szolgáltatás hitelesítő adatait az *App.config* fájlban definiált szolgáltatásvégpontokra:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Ahogy azt korábban említettük, deklarálhatott több szolgáltatást és végpontot a konfigurációs fájlban. Ha így tett, ez a kód végighalad a konfigurációs fájlon, és megkeres minden végpontot, amelyre alkalmaznia kell a hitelesítő adatokat. Ebben az oktatóanyagban a konfigurációs fájl csak egy végpontot.

### <a name="open-the-service-host"></a>A szolgáltatásállomás megnyitása

1. Még `Main()`mindig, adja hozzá a következő sort a szolgáltatás megnyitásához.

    ```csharp
    host.Open();
    ```

1. Tájékoztassa a felhasználót arról, hogy a szolgáltatás fut, és magyarázza el, hogyan lehet leállítani.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Ha kész, zárja be a szolgáltatásgazdát.

    ```csharp
    host.Close();
    ```

1. A projekt létrehozásához válassza a Ctrl+Shift+B billentyűkombinációt.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Példa szolgáltatás szolgáltatásra egy konzolalkalmazásban

A kitöltött szervizkódnak a következőképpen kell megjelennie. A kód tartalmazza a szolgáltatási szerződést és az oktatóanyag előző lépéseit, és a szolgáltatást egy konzolalkalmazásban üzemelteti.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

            // Add the Relay credentials to all endpoints specified in configuration.
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

## <a name="create-a-wcf-client-for-the-service-contract"></a>WCF-ügyfél létrehozása a szolgáltatási szerződéshez

A következő feladat egy ügyfélalkalmazás létrehozása és a később megvalósítandó szervizszerződés meghatározása. Ezek a lépések hasonlítanak a szolgáltatás létrehozásához használt lépésekhez: szerződés definiálása, *App.config-fájl* szerkesztése, hitelesítő adatok használata a továbbító szolgáltatáshoz való csatlakozáshoz, és így tovább. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. Hozzon létre egy új projektet az aktuális Visual Studio-megoldásban az ügyfél számára:

   1. A **Megoldáskezelőben**kattintson a jobb gombbal az aktuális megoldásra (nem a projektre), és válassza az**Új projekt** **hozzáadása** > parancsot.
   1. Az **Új projekt hozzáadása csoportban**válassza a **Konzolalkalmazás (.NET Framework)** lehetőséget a C#-hoz, és válassza a **Tovább**gombot.
   1. Nevezze el az *EchoClient projektet,* és válassza **a Létrehozás gombot.**

1. A **Megoldáskezelőben**az **EchoClient** projektben kattintson duplán **a Program.cs** elemre a fájl szerkesztőben való megnyitásához, ha még nincs megnyitva.
1. Módosítsa a névtér alapértelmezett `EchoClient` nevét a következőre: `Microsoft.ServiceBus.Samples`.
1. Telepítse a [Service Bus NuGet csomagot:](https://www.nuget.org/packages/WindowsAzure.ServiceBus)

   1. A **Megoldáskezelőben**kattintson a jobb gombbal az **EchoClient** elemre, majd válassza **a NuGet-csomagok kezelése parancsot.**
   1. Válassza a **Tallózás**lehetőséget, majd keresse meg és válassza a **WindowsAzure.ServiceBus**elemet. Válassza **a Telepítés**lehetőséget, és fogadja el a használati feltételeket.

      ![Szolgáltatásbusz-csomag telepítése][4]

1. Adjon `using` hozzá egy utasítást a [System.ServiceModel](/dotnet/api/system.servicemodel) névtérhez a *Program.cs* fájlban.

    ```csharp
    using System.ServiceModel;
    ```

1. Adja hozzá a szolgáltatási szerződés meghatározását a névtérhez, ahogyan az az alábbi példában látható. Ez a definíció megegyezik a **Service** projektben használt definícióval. Adja hozzá ezt a `Microsoft.ServiceBus.Samples` kódot a névtér tetején.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Az ügyfél létrehozásához válassza a Ctrl+Shift+B billentyűkombinációt.

### <a name="example-of-the-echoclient-project"></a>Példa az EchoClient projektre

A következő kód az **EchoClient** projekt ben lévő *Program.cs* fájl aktuális állapotát mutatja.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="configure-the-wcf-client"></a>A WCF-ügyfél konfigurálása

Ebben a lépésben hozzon létre egy *App.config* fájlt egy alapszintű ügyfélalkalmazáshoz, amely hozzáfér az oktatóanyagban korábban létrehozott szolgáltatáshoz. Ez *az App.config* fájl határozza meg a szerződést, a kötést és a végpont nevét. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. A **Megoldáskezelőben**az **EchoClient** projektben kattintson duplán az **App.config** fájlra a fájl Visual Studio-szerkesztőben való megnyitásához.
1. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
1. Az `system.serviceModel` elemen belül `<client>` adjon hozzá egy elemet.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ez a kód azt deklarálja, hogy WCF-stílusú ügyfélalkalmazást határoz meg.

1. A `client` elemen belül határozza meg a nevet, a szerződést és a kötéstípust a végponthoz.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ez a kód határozza meg a végpont nevét. Azt is meghatározza a szerződés a szolgáltatásban meghatározott, és az a tény, hogy az ügyfélalkalmazás tcp-t használ az Azure Relay-rel való kommunikációhoz. A következő lépés a végpont neve használatával ezt a végpont-konfigurációt összekapcsolja a szolgáltatás URI-jával.

1. Válassza az > **Összes** **fájlmentése**lehetőséget.

### <a name="example-of-the-appconfig-file"></a>Példa az App.config fájlra

A következő kód az *Echo-ügyfél App.config* fájlját mutatja.

```xml
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

## <a name="implement-the-wcf-client"></a>A WCF-ügyfél megvalósítása

Ebben a szakaszban valósítmeg egy alapvető ügyfélalkalmazást, amely hozzáfér az oktatóanyagban korábban létrehozott szolgáltatáshoz. A szolgáltatáshoz hasonlóan az ügyfél számos műveletet végez az Azure Relay eléréséhez:

* Beállítja a csatlakozási módot.
* Létrehozza a gazdaszolgáltatás helyét megadó URI-t.
* Megadja a biztonsági hitelesítő adatokat.
* Alkalmazza a hitelesítő adatokat a kapcsolatra.
* Megnyitja a kapcsolatot.
* Végrehajtja az alkalmazásspecifikus feladatokat.
* Bezárja a kapcsolatot.

Az egyik fő különbség azonban az, hogy az ügyfélalkalmazás egy csatornát használ a továbbító szolgáltatáshoz való csatlakozáshoz. A szolgáltatás a **ServiceHost**szolgáltatást hívja. A feladatokhoz használt kód megtalálható az eljárást követő példában.

### <a name="implement-a-client-application"></a>Ügyfélalkalmazás megvalósítása

1. A csatlakozási mód beállítása legyen `AutoDetect`. Adja hozzá a következő kódot az **EchoClient** alkalmazás `Main()` metódusában.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Határozzon meg változókat, amelyek a konzolról beolvasott szolgáltatásnévtér és SAS-kulcs értékeit tárolják.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Hozza létre azt az URI-t, amely meghatározza az állomás helyét a továbbító projektben.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Hozza létre a hitelesítő objektumot a szolgáltatásnévtér végpontjához.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Hozza létre azt a csatornagyárat, amely betölti az *App.config* fájlban leírt konfigurációt.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    A csatornagyár egy WCF-objektum, amely létrehoz egy csatornát, amelyen keresztül a szolgáltatás és az ügyfélalkalmazások kommunikálnak.

1. Alkalmazza a hitelesítő adatokat.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Hozza létre és nyissa meg a csatornát a szolgáltatás felé.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Írja meg az Echo alapszintű felhasználói felületét és funkcióit.

    ```csharp
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

    A kód a csatornaobjektum példányát használja a szolgáltatás proxyjaként.

1. Zárja be a csatornát, és zárja be a gyárat.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Példa az oktatóanyag kódjára

A kitöltött kódnak a következőképpen kell megjelennie. Ez a kód bemutatja, hogyan hozhat létre ügyfélalkalmazást, hogyan hívhatja meg a szolgáltatás műveleteit, és hogyan zárhat be az ügyfelet a művelethívás befejezése után.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
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

## <a name="run-the-applications"></a>Az alkalmazások futtatása

1. A megoldás létrehozásához válassza a Ctrl+Shift+B billentyűkombinációt. Ez a művelet az ügyfélprojektet és az előző lépésekben létrehozott szolgáltatási projektet is létrehozza.
1. Az ügyfélalkalmazás futtatása előtt győződjön meg arról, hogy a szolgáltatásalkalmazás fut. A **Megoldáskezelőben**kattintson a jobb gombbal az **EchoService** megoldásra, majd válassza a **Tulajdonságok parancsot.**
1. A **Tulajdonságlapok**párbeszédpanelen válassza a Common Properties Startup Project **(Közös tulajdonságok** > **indítási projekt)** lehetőséget, majd válassza a Több **indítási projekt**lehetőséget. Győződjön meg arról, hogy a lista első eleme az **EchoService**.
1. Az **Action** (Művelet) mezőt állítsa az **EchoService** és az **EchoClient** projekt esetén is **Start** (Indítás) értékűre.

    ![Projekt tulajdonságlapjai][5]

1. Válassza a **Projektfüggőségek lehetőséget.** A **Projektek alkalmazásban**válassza az **EchoClient**lehetőséget. A **Függ a beállításban**győződjön meg arról, hogy az **EchoService** ki van jelölve.

    ![Projektfüggőségek][6]

1. A **tulajdonságlapok**bezárásához válassza az **OK gombot.**
1. Mindkét projekt futtatásához válassza az F5 lehetőséget.
1. Mindkét konzolablak megnyílik, és a rendszer a névtér nevének megadását kéri. A szolgáltatásnak először futnia kell, ezért az **EchoService** konzol ablakában adja meg a névteret, majd válassza az Enter lehetőséget.
1. Ezután a konzol kéri a SAS-kulcsot. Írja be a SAS-kulcsot, és válassza az Enter lehetőséget.

    Az alábbiakban egy példa kimenet látható a konzolablakból. Az itt enyeleg értékek csak példák.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    A szolgáltatásalkalmazás a konzolablakba írja a címet, amelyen figyel, ahogyan az az előbbi példában is látható.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Az **EchoClient** konzolablakában adja meg a korábban a szolgáltatásalkalmazáshoz megadott adatokat. Adja meg ugyanazt a szolgáltatásnévteret és SAS-kulcsértéket az ügyfélalkalmazáshoz.
1. Az értékek megadása után az ügyfél megnyit egy csatornát a szolgáltatás felé, és szövegbevitelre kéri, ahogyan az az alábbi példa konzolkimenetben látható.

    `Enter text to echo (or [Enter] to exit):`

    Írjon be néhány szöveget, amelyet el szeretne küldeni a szolgáltatási alkalmazásnak, és válassza az Enter lehetőséget. Ezt a szöveget az Echo szolgáltatásműveleten keresztül küldi el a rendszer a szolgáltatásnak, és megjelenik a szolgáltatás konzolablakában, ahogyan az az alábbi példa kimenetben látható.

    `Echoing: My sample text`

    Az ügyfélalkalmazás fogadja az `Echo` művelet visszaadott értékét, amely az eredeti szöveg, és a konzolablakba írja. A következő szöveg példa kimenet az ügyfélkonzol ablakából.

    `Server echoed: My sample text`

1. Ezzel a módszerrel folytathatja szöveges üzenetek küldését az ügyfélről a szolgáltatásba. Ha végzett, válassza az Enter lehetőséget az ügyfél- és szolgáltatáskonzol ablakaiban a két alkalmazás befejezéséhez.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Helyszíni WCF REST-szolgáltatás közzététele egy saját hálózaton kívüli ügyfélen](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
