---
title: Helyszíni WCF REST-szolgáltatás elérhetővé tétele az ügyfeleknek a Azure Relay használatával
description: Ez az oktatóanyag azt ismerteti, hogyan tehet elérhetővé egy helyszíni WCF REST-szolgáltatást egy külső ügyfél számára az Azure WCF Relay használatával.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513082"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Oktatóanyag: helyszíni WCF REST-szolgáltatás közzététele külső ügyfél számára az Azure WCF Relay használatával

Ez az oktatóanyag azt ismerteti, hogyan hozhat létre WCF Relay ügyfélalkalmazás és-szolgáltatást a Azure Relay használatával. A [Service Bus üzenetkezelést](../service-bus-messaging/service-bus-messaging-overview.md)használó hasonló oktatóanyagért lásd: ismerkedés [a Service Bus Queues szolgáltatással](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

A jelen oktatóanyagban való munka során megismerheti a WCF Relay ügyfél és szolgáltatásalkalmazás létrehozásának lépéseit. Az eredeti WCF-társaihoz hasonlóan a szolgáltatás olyan szerkezet, amely egy vagy több végpontot tesz elérhetővé. Mindegyik végpont egy vagy több szolgáltatási műveletet tesz elérhetővé. A szolgáltatások végpontja megad egy címet, ahol a szolgáltatás megtalálható, egy kötést, amely tartalmazza az információkat, amelyeket az ügyfélnek kommunikálnia kell a szolgáltatás felé, valamint egy szerződést, amely meghatározza a szolgáltatás által az ügyfeleknek nyújtott funkciókat. A WCF és a WCF Relay közötti fő különbség az, hogy a végpontot a felhőben, helyileg a számítógépén teszi elérhetővé.

Miután elvégezte az oktatóanyagban szereplő szakaszokat, egy futó szolgáltatással fog rendelkezni. Egy olyan ügyféllel is rendelkezhet, amely meghívja a szolgáltatás műveleteit. 

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
>
> * Az oktatóanyag előfeltételeinek telepítése.
> * Hozzon létre egy Relay-névteret.
> * WCF szolgáltatási szerződés létrehozása.
> * A WCF-szerződés implementálása.
> * Gazdagép és futtassa a WCF szolgáltatást a Relay szolgáltatásban való regisztráláshoz.
> * Hozzon létre egy WCF-ügyfelet a szolgáltatási szerződéshez.
> * Konfigurálja a WCF-ügyfelet.
> * A WCF-ügyfél implementálása.
> * Futtassa az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb](https://www.visualstudio.com). Az oktatóanyagban szereplő példák a Visual Studio 2019-et használják.
* Azure SDK a .NET-hez. Telepítse az [SDK letöltési oldaláról](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Relay-névtér létrehozása

Első lépésként létre kell hoznia egy névteret, és meg kell szereznie egy [közös hozzáférésű aláírás (SAS)](../service-bus-messaging/service-bus-sas.md) kulcsát. A névtér egy alkalmazás-határt biztosít minden olyan alkalmazáshoz, amelyet a továbbítási szolgáltatás tesz elérhetővé. A rendszer automatikusan létrehoz egy SAS-kulcsot a szolgáltatás névterének létrehozásakor. A szolgáltatási névtér és az SAS-kulcs kombinációja biztosítja az Azure számára az alkalmazásokhoz való hozzáférés hitelesítéséhez szükséges hitelesítő adatokat.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>WCF szolgáltatási szerződés megadása

A szolgáltatási szerződés meghatározza a szolgáltatás által támogatott műveleteket. A műveletek webszolgáltatási metódusok vagy függvények. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútumot minden felületre, az [OperationContractAttribaute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútumot pedig minden műveletre alkalmazni kell. Ha a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútummal rendelkező illesztőfelület egyik metódusa nem rendelkezik a [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútummal, akkor ez a metódus nincs kitéve. A feladatok kódja megtalálható az eljárást követő példában. A szerződések és szolgáltatások nagyobb mértékű megvitatását lásd: [szolgáltatások tervezése és implementálása](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Továbbító szerződés létrehozása illesztőfelülettel

1. Indítsa el a Microsoft Visual studiót rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, majd válassza a **Futtatás rendszergazdaként**lehetőséget.
1. A Visual Studióban válassza az **új projekt létrehozása**lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget, és kattintson a C# **Tovább gombra**.
1. Nevezze el a projekt *EchoService* , és válassza a **Létrehozás**lehetőséget.

   ![Konzolalkalmazás létrehozása][2]

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **NuGet csomagkezelő**területén válassza a **Tallózás**elemet, majd keresse meg és válassza a **WindowsAzure. ServiceBus**elemet. Válassza a **telepítés**lehetőséget, és fogadja el a használati feltételeket.

    ![Service Bus csomag][3]

   Ez a csomag automatikusan hozzáadja a Service Bus-kódtárak és a WCF-`System.ServiceModel`hivatkozásait. A [System.ServiceModel](/dotnet/api/system.servicemodel) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A Service Bus számos WCF-objektumot és -attribútumot használ a szolgáltatási szerződések meghatározására.

1. Adja hozzá a következő `using` utasításokat a *program.cs*tetején:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Módosítsa a névtér alapértelmezett `EchoService` nevét a következőre: `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > Ez az oktatóanyag a C# `Microsoft.ServiceBus.Samples` névteret használja, amely a [WCF-ügyfél konfigurálása](#configure-the-wcf-client) szakasz konfigurációs fájljában használt, a szerződésen alapuló felügyelt típus névtere. Megadhatja a minta összeállításakor használni kívánt névtereket is. Az oktatóanyag azonban nem fog működni, hacsak nem módosítja a szerződés és a szolgáltatás névtereit az alkalmazás konfigurációs fájljában. Az *app. config* fájlban megadott névtérnek meg kell egyeznie a fájlban megadott C# névtérrel.
   >

1. Közvetlenül a `Microsoft.ServiceBus.Samples` névtér deklarációja után, de a névtéren belül adjon meg egy `IEchoContract` nevű új felületet, és alkalmazza a `ServiceContractAttribute` attribútumot az illesztőfelületre `https://samples.microsoft.com/ServiceModel/Relay/`névtér értékével. Illessze be a következő kódot a névtér-deklaráció után:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke ehelyett egyedi azonosítóként van használatban ehhez a szerződéshez. A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

   > [!NOTE]
   > A szolgáltatási szerződés névtere általában tartalmaz egy elnevezési sémát, amely tartalmazza a verzióinformációkat. Ha a verzióinformációk szerepelnek a szolgáltatási szerződés névterében, a szolgáltatások képesek elkülöníteni a nagyobb módosításokat egy új szolgáltatási szerződés új névtérrel való meghatározása, valamint egy új végponton való megjelenítése révén. Ily módon az ügyfelek továbbra is használhatják a régi szolgáltatási szerződést anélkül, hogy frissíteni kellene őket. A verzióinformációk dátumot vagy buildszámot tartalmazhatnak. További információ: [Service Versioning](/dotnet/framework/wcf/service-versioning) (Szolgáltatás verziószámozása). Ebben az oktatóanyagban a szolgáltatási szerződés névterének elnevezési sémája nem tartalmazza a verzióval kapcsolatos információkat.
   >

1. Az `IEchoContract` felületen belül deklaráljon egy metódust az egyetlen művelethez, amely a `IEchoContract` szerződést felfedi a felületen, és alkalmazza a `OperationContractAttribute` attribútumot arra a metódusra, amelyet közzé szeretne tenni a nyilvános WCF Relay szerződés részeként, a következőképpen:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Közvetlenül az `IEchoContract` felület deklarációja után deklaráljon egy csatornát, amely örökli az `IEchoContract` és az `IClientChannel` felületek tulajdonságait is, ahogy az itt látható:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    A csatorna egy olyan WCF-objektum, amelyen keresztül a gazda és az ügyfél információkat adnak át egymásnak. Később kódot írhat a csatornához a két alkalmazás közötti echo-információkkal.

1. Válassza a **build** > **Build megoldás** lehetőséget, vagy a CTRL + SHIFT + B billentyűkombinációval erősítse meg az eddigi munkája pontosságát.

### <a name="example-of-a-wcf-contract"></a>WCF-szerződés – példa

A következő kód egy alapszintű felületet mutat be, amely meghatározza a WCF Relay szerződést.

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

## <a name="implement-the-wcf-contract"></a>A WCF-szerződés implementálása

Az Azure Relay létrehozásához egy felület használatával először létre kell hoznia a szerződést. Az interfész létrehozásával kapcsolatos további információkért tekintse meg az előző szakaszt. A következő eljárás implementálja a felületet. Ez a feladat magában foglalja egy `EchoService` nevű osztály létrehozását, amely megvalósítja a felhasználó által definiált `IEchoContract` felületet. Az interfész megvalósítása után a felületet az *app. config* konfigurációs fájljának használatával kell konfigurálnia. A konfigurációs fájl az alkalmazáshoz szükséges információkat tartalmazza. Ez az információ tartalmazza a szolgáltatás nevét, a szerződés nevét, valamint a továbbítási szolgáltatással való kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód az eljárást követő példában található. A szolgáltatási szerződések megvalósításával kapcsolatos általánosabb információkért lásd: [szolgáltatási szerződések](/dotnet/framework/wcf/implementing-service-contracts)implementálása.

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

1. Válassza a **build** > **Build megoldás** lehetőséget, vagy válassza a CTRL + SHIFT + B billentyűkombinációt.

### <a name="define-the-configuration-for-the-service-host"></a>A Service Host konfigurációjának megadása

A konfigurációs fájl hasonló a WCF konfigurációs fájlhoz. Tartalmazza a szolgáltatás nevét, végpontját és kötését. A végpont az a hely, Azure Relay az ügyfelek és a gazdagépek számára lehetővé teszi az egymással való kommunikációt. A kötés a kommunikációhoz használt protokoll típusa. A fő különbség az, hogy ez a konfigurált szolgáltatási végpont egy [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) kötésre hivatkozik, amely nem része a .NET-keretrendszernek. A [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) a szolgáltatás által meghatározott kötések egyike.

1. A **megoldáskezelő**kattintson duplán az **app. config** fájlra, és nyissa meg a fájlt a Visual Studio szerkesztőjében.
1. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
1. A `<system.serviceModel>` címkéken belül adjon hozzá egy `<services>` elemet. Egyetlen konfigurációs fájlban több továbbító alkalmazást is meghatározhat. Ez az oktatóanyag viszont csak egyet határoz meg.

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

    A végpont meghatározza, hogy az ügyfél hol keresi majd a gazdaalkalmazást. Később az oktatóanyag ezt a lépést használja egy olyan URI létrehozásához, amely teljes mértékben elérhetővé teszi a gazdagépet a Azure Relayon keresztül. A kötés deklarálja, hogy a TCP protokollt használja a továbbítási szolgáltatással való kommunikációhoz.

1. Válassza a **build** > **Build megoldás** lehetőséget, vagy a CTRL + SHIFT + B billentyűkombinációval erősítse meg az eddigi munkája pontosságát.

### <a name="example-of-implementation-of-a-service-contract"></a>Szolgáltatási szerződés megvalósításának példája

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

A következő kód a Service hosthez társított *app. config* fájl alapszintű formátumát mutatja be.

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

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Gazdagép és futtassa a WCF szolgáltatást a Relay szolgáltatásban való regisztráláshoz

Ez a lépés egy Azure Relay szolgáltatás futtatását ismerteti.

### <a name="create-the-relay-credentials"></a>A továbbító hitelesítő adatainak létrehozása

1. A `Main()` metódusban hozzon létre két változót, amelyben a konzolablakból beolvasott névtér és SAS-kulcs tárolható.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Az SAS-kulcsot később a projekt eléréséhez fogja használni a rendszer. A névteret a rendszer paraméterként átadja a `CreateServiceUri` számára szolgáltatás URI létrehozásához.

1. [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) objektum használatával állapítsa meg, hogy a hitelesítő adatok típusaként egy sas-kulcsot fog használni. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Alapcímek létrehozása a szolgáltatáshoz

Az előző szakaszban hozzáadott kód után hozzon létre egy `Uri` példányt a szolgáltatás alapszintű címeként. Ez az URI megadja a Service Bus-sémát, a névteret és a szolgáltatási felület útvonalát.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Az "SB" érték a Service Bus séma rövidítése. Azt jelzi, hogy a TCP protokollt használja protokollként. Ezt a sémát korábban is jelezte a konfigurációs fájlban, ha a [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) meg van adva Kötésként.

Az oktatóanyaghoz az URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>A szolgáltatás gazdagépének létrehozása és konfigurálása

1. A `Main()`továbbra is működik a kapcsolati módban, hogy `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    A kapcsolati mód leírja azt a protokollt, amelyet a szolgáltatás a továbbítási szolgáltatással való kommunikációhoz használ. HTTP vagy TCP. Az alapértelmezett beállítás `AutoDetect`a szolgáltatás a TCP protokollon keresztül próbál csatlakozni a Azure Relayhoz, ha az elérhető, és HTTP, ha a TCP nem érhető el. Ez az eredmény eltér a szolgáltatás által az ügyfél-kommunikációhoz megadott protokolltól. Azt a protokollt az alkalmazott kötés határozza meg. Egy szolgáltatás például használhatja a [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) kötést, amely megadja, hogy a VÉGPONTja http-n keresztül kommunikál az ügyfelekkel. Ugyanez a szolgáltatás megadhatja `ConnectivityMode.AutoDetect`, hogy a szolgáltatás a TCP protokollon keresztül kommunikáljon Azure Relay.

1. Hozza létre a szolgáltatás gazdáját a szakaszban korábban létrehozott URI segítségével.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    A szolgáltatásgazda a WCF-objektum, amely a szolgáltatást példányosítja. Itt adhatja meg a létrehozni kívánt szolgáltatás típusát, egy `EchoService` típusát, valamint azt a internetcímet is, amelyen ki szeretné tenni a szolgáltatást.

1. A *program.cs* fájl elején adja meg a következő hivatkozásokat a [System. ServiceModel. Description](/dotnet/api/system.servicemodel.description) és a [Microsoft. ServiceBus. Description](/dotnet/api/microsoft.servicebus.description)elemre.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Újra a `Main()` metódusban konfigurálja a végpontot a nyilvános hozzáférés engedélyezésére.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ez a lépés tájékoztatja a Relay szolgáltatást, hogy az alkalmazás nyilvánosan elérhető a projekthez tartozó Atom-hírcsatorna vizsgálatával. Ha `private`re állítja be a `DiscoveryType`, az ügyfél továbbra is hozzáférhet a szolgáltatáshoz. A szolgáltatás azonban nem jelenik meg, amikor megkeresi a `Relay` névteret. Az ügyfélnek ehhez már ismernie kell a végpont elérési útját.

1. Alkalmazza a szolgáltatás hitelesítő adatait az *app. config* fájlban definiált szolgáltatási végpontokra:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Ahogy korábban már említettük, több szolgáltatást és végpontot is deklarálhat a konfigurációs fájlban. Ha így tett, ez a kód végighalad a konfigurációs fájlon, és megkeres minden végpontot, amelyre alkalmaznia kell a hitelesítő adatokat. Ebben az oktatóanyagban a konfigurációs fájlnak csak egy végpontja van.

### <a name="open-the-service-host"></a>A szolgáltatás gazdagépének megnyitása

1. Továbbra is `Main()`ban adja hozzá a következő sort a szolgáltatás megnyitásához.

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

1. Válassza a CTRL + SHIFT + B billentyűkombinációt a projekt felépítéséhez.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Egy szolgáltatás egy konzol alkalmazásban való üzemeltetésére szolgáló példa

A befejezett szolgáltatási kód a következőképpen jelenik meg. A kód tartalmazza a szolgáltatási szerződést és a megvalósítást az oktatóanyag előző lépéseiből, és futtatja a szolgáltatást egy konzol alkalmazásban.

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

A következő feladat egy ügyfélalkalmazás létrehozása és a később megvalósított szolgáltatási szerződés meghatározása. Ezek a lépések a szolgáltatás létrehozásához szükséges lépéseket hasonlítják össze: szerződések definiálása, *app. config* fájl szerkesztése, a továbbítási szolgáltatáshoz való kapcsolódáshoz használt hitelesítő adatokkal és így tovább. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. Hozzon létre egy új projektet a jelenlegi Visual Studio-megoldásban az ügyfél számára:

   1. A **Megoldáskezelőban**kattintson a jobb gombbal az aktuális megoldásra (ne a projektre), majd válassza a > **új projekt** **hozzáadása** lehetőséget.
   1. Az **új projekt hozzáadása**területen válassza ki a C# **konzol alkalmazás (.NET-keretrendszer)** elemet, majd kattintson a **Tovább gombra**.
   1. Nevezze el a Project *EchoClient* , és válassza a **Létrehozás**lehetőséget.

1. **Megoldáskezelő**a **EchoClient** projektben kattintson duplán a **program.cs** elemre, hogy megnyissa a fájlt a szerkesztőben, ha még nincs megnyitva.
1. Módosítsa a névtér alapértelmezett `EchoClient` nevét a következőre: `Microsoft.ServiceBus.Samples`.
1. Telepítse a [Service Bus NuGet csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. **Megoldáskezelő**kattintson a jobb gombbal a **EchoClient** elemre, majd válassza a **NuGet-csomagok kezelése**lehetőséget.
   1. Válassza a **Tallózás**elemet, majd keresse meg és válassza ki a **WindowsAzure. ServiceBus**elemet. Válassza a **telepítés**lehetőséget, és fogadja el a használati feltételeket.

      ![A Service Bus-csomag telepítése][4]

1. Adjon hozzá egy `using` utasítást a [System. ServiceModel](/dotnet/api/system.servicemodel) névtérhez a *program.cs* fájlban.

    ```csharp
    using System.ServiceModel;
    ```

1. Adja hozzá a szolgáltatási szerződés meghatározását a névtérhez, ahogyan az az alábbi példában látható. Ez a definíció megegyezik a **szolgáltatási** projektben használt definícióval. Adja hozzá ezt a kódot a `Microsoft.ServiceBus.Samples` névtér elején.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Válassza a CTRL + SHIFT + B billentyűkombinációt az ügyfél létrehozásához.

### <a name="example-of-the-echoclient-project"></a>Példa a EchoClient projektre

A következő kód a *program.cs* fájl aktuális állapotát jeleníti meg a **EchoClient** projektben.

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

Ebben a lépésben létrehoz egy *app. config* fájlt egy alapszintű ügyfélalkalmazás számára, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. Ez az *app. config* fájl határozza meg a szerződést, a kötést és a végpont nevét. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. **Megoldáskezelő**a **EchoClient** projektben kattintson duplán az **app. config** fájlra, és nyissa meg a fájlt a Visual Studio szerkesztőjében.
1. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
1. A `system.serviceModel` elemen belül adjon hozzá egy `<client>` elemet.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ez a kód azt deklarálja, hogy WCF-stílusú ügyfélalkalmazás van definiálva.

1. A `client` elemen belül határozza meg a nevet, a szerződést és a kötéstípust a végponthoz.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ez a kód a végpont nevét határozza meg. Meghatározza továbbá a szolgáltatásban definiált szerződést és azt is, hogy az ügyfélalkalmazás a TCP-t használja a Azure Relay való kommunikációhoz. A következő lépés a végpont neve használatával ezt a végpont-konfigurációt összekapcsolja a szolgáltatás URI-jával.

1. Válassza a **fájl** > **az összes mentése**lehetőséget.

### <a name="example-of-the-appconfig-file"></a>Példa az app. config fájlra

A következő kód az ECHO-ügyfél *app. config* fájlját mutatja.

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

## <a name="implement-the-wcf-client"></a>A WCF-ügyfél implementálása

Ebben a szakaszban egy alapszintű ügyfélalkalmazás megvalósítását hajtja végre, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. A szolgáltatáshoz hasonlóan a-ügyfél is sok műveletet végez a Azure Relay eléréséhez:

* Beállítja a csatlakozási módot.
* Létrehozza a gazdaszolgáltatás helyét megadó URI-t.
* Megadja a biztonsági hitelesítő adatokat.
* Alkalmazza a hitelesítő adatokat a kapcsolatra.
* Megnyitja a kapcsolatot.
* Végrehajtja az alkalmazásspecifikus feladatokat.
* Bezárja a kapcsolatot.

Azonban az egyik fő különbség az, hogy az ügyfélalkalmazás egy csatornát használ a Relay szolgáltatáshoz való kapcsolódáshoz. A szolgáltatás a **ServiceHost**hívását használja. A feladatokhoz használt kód megtalálható az eljárást követő példában.

### <a name="implement-a-client-application"></a>Ügyfélalkalmazás implementálása

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

1. Hozza létre azt az URI-t, amely meghatározza a gazdagép helyét a továbbító projektben.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Hozza létre a hitelesítő objektumot a szolgáltatásnévtér végpontjához.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Hozza létre a Channel Factoryt, amely betölti az *app. config* fájlban leírt konfigurációt.

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

    A kód a csatorna objektum példányát használja proxyként a szolgáltatáshoz.

1. Zárja be a csatornát, és zárja be a gyárat.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Példa az oktatóanyaghoz tartozó kódra

A befejezett kód a következőképpen jelenik meg. Ez a kód bemutatja, hogyan hozhat létre egy ügyfélalkalmazás, hogyan hívhatja meg a szolgáltatás műveleteit, és hogyan zárhatja be az ügyfelet a művelet meghívásának befejeződése után.

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

1. Válassza a CTRL + SHIFT + B billentyűkombinációt a megoldás felépítéséhez. Ez a művelet létrehozza az ügyfél-projektet és az előző lépésekben létrehozott szolgáltatási projektet is.
1. Az ügyfélalkalmazás futtatása előtt győződjön meg arról, hogy a szolgáltatásalkalmazás fut. **Megoldáskezelő**kattintson a jobb gombbal a **EchoService** -megoldásra, majd válassza a **Tulajdonságok parancsot**.
1. A **tulajdonságlapok**, **Általános tulajdonságok** > **indítási projekt**elemnél válassza a **több indítási projekt**lehetőséget. Győződjön meg arról, hogy a lista első eleme az **EchoService**.
1. Az **Action** (Művelet) mezőt állítsa az **EchoService** és az **EchoClient** projekt esetén is **Start** (Indítás) értékűre.

    ![Projekt tulajdonságlapjának lapja][5]

1. Válassza a **projektek függőségei**lehetőséget. A **projektek**területen válassza a **EchoClient**lehetőséget. A- **től függ**, hogy a **EchoService** van-e kiválasztva.

    ![Projekt függőségei][6]

1. A **tulajdonságlapok**bezárásához kattintson **az OK gombra** .
1. Válassza az F5 lehetőséget mindkét projekt futtatásához.
1. Mindkét konzolablak megnyílik, és a rendszer a névtér nevének megadását kéri. Először a szolgáltatásnak kell futnia, ezért a **EchoService** -konzol ablakban adja meg a névteret, majd válassza az ENTER billentyűt.
1. Ezután a konzol felszólítja az SAS-kulcs megadására. Adja meg az SAS-kulcsot, és válassza az ENTER billentyűt.

    Az alábbiakban egy példa kimenet látható a konzolablakból. Az itt látható értékek csak példák.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    A szolgáltatásalkalmazás a konzolablakba írja a címet, amelyen figyel, ahogyan az az előbbi példában is látható.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Az **EchoClient** konzolablakában adja meg a korábban a szolgáltatásalkalmazáshoz megadott adatokat. Adja meg a szolgáltatási névtér és az SAS-kulcs értékét az ügyfélalkalmazás számára.
1. Az értékek megadása után az ügyfél megnyit egy csatornát a szolgáltatás felé, és szövegbevitelre kéri, ahogyan az az alábbi példa konzolkimenetben látható.

    `Enter text to echo (or [Enter] to exit):`

    Adja meg a szolgáltatásalkalmazás számára küldendő szöveget, majd válassza az ENTER billentyűt. Ezt a szöveget az Echo szolgáltatásműveleten keresztül küldi el a rendszer a szolgáltatásnak, és megjelenik a szolgáltatás konzolablakában, ahogyan az az alábbi példa kimenetben látható.

    `Echoing: My sample text`

    Az ügyfélalkalmazás fogadja az `Echo` művelet visszaadott értékét, amely az eredeti szöveg, és a konzolablakba írja. Az alábbi szöveg például az ügyféloldali konzol ablakának kimenete.

    `Server echoed: My sample text`

1. Ezzel a módszerrel folytathatja szöveges üzenetek küldését az ügyfélről a szolgáltatásba. Ha elkészült, válassza az ENTER lehetőséget az ügyfél és a szolgáltatás konzolján a Windows mindkét alkalmazás befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Helyszíni WCF REST-szolgáltatás közzététele a hálózaton kívüli ügyfelek számára](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
