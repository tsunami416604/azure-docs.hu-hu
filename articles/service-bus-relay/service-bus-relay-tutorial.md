---
title: "Azure Service Bus WCF továbbító útmutató |} Microsoft Docs"
description: "Hozzon létre egy Service Bus-ügyfélalkalmazást, és WCF-továbbítási szolgáltatást."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: sethm
ms.openlocfilehash: 0298a93da0d8cd0b1f2e15146a708c8dd6ecb8e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-wcf-relay-tutorial"></a>Azure WCF továbbító útmutató

Ez az oktatóanyag ismerteti, hogyan hozhat létre egy egyszerű WCF továbbító ügyfélalkalmazást, és Azure Relay szolgáltatást. Egy alkalmazó hasonló oktatóanyagért [Service Bus üzenetkezelés](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), lásd: [Ismerkedés a Service Bus-üzenetsorok](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Az oktatóanyag feldolgozása révén lehetővé teszi egy WCF továbbító ügyfél és -szogáltatásalkalmazás létrehozásához szükséges lépéseket megértéséhez. Az eredeti WCF megfelelők esetében, mint például a "szolgáltatás" olyan konstrukció, amely egy vagy több végpontot, azt mutatja, amelyek egy vagy több szolgáltatási műveletet tesz elérhetővé. A szolgáltatások végpontja megad egy címet, ahol a szolgáltatás megtalálható, egy kötést, amely tartalmazza az információkat, amelyeket az ügyfélnek kommunikálnia kell a szolgáltatás felé, valamint egy szerződést, amely meghatározza a szolgáltatás által az ügyfeleknek nyújtott funkciókat. A WCF és WCF továbbító közötti fő különbség a, hogy a végpont a számítógépre helyi ahelyett, hogy a felhőben van közzétéve.

Miután a végére ért az oktatóanyag témaköreinek, rendelkezni fog egy futó szolgáltatással, valamint egy, a szolgáltatás műveleteinek meghívására képes ügyféllel. Az első témakör egy fiók beállítását ismerteti. A következő lépések leírják, hogyan határozhat meg egy szerződést használó szolgáltatást, hogyan valósíthatja meg a szolgáltatást, valamint hogyan konfigurálhatja a szolgáltatást a kódban. Azt is leírják, hogyan működtethető és futtatható a szolgáltatás. A létrejött szolgáltatás próbaüzemben működik, és az ügyfél és a szolgáltatás ugyanazon a számítógépen fut. A szolgáltatást kód vagy egy konfigurációs fájl segítségével is konfigurálhatja.

Az utolsó három lépés azt írja le, hogy hogyan hozhat létre egy ügyfélalkalmazást, hogyan konfigurálhatja azt, valamint hogyan hozhat létre és használhat olyan ügyfélalkalmazásokat, amelyek hozzáférnek a gazdagép funkcióihoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

* [Microsoft Visual Studio 2015 vagy újabb](http://visualstudio.com). Ez az oktatóanyag a Visual Studio 2017 használja.
* Aktív Azure-fiók. Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

## <a name="create-a-service-namespace"></a>Szolgáltatásnévtér létrehozása

Az első lépés egy névtér létrehozása, valamint a egy [közös hozzáférésű Jogosultságkód (SAS)](../service-bus-messaging/service-bus-sas.md) kulcs. A névtér egy biztosít a továbbítási szolgáltatás keresztül közzétett minden alkalmazáshoz. Az SAS-kulcsot a rendszer automatikusan előállítja a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és SAS-kulcs együttes használata a hitelesítő adatokat az Azure-alkalmazáshoz való hozzáférés hitelesítéséhez biztosít. Relay-névtér létrehozásához kövesse az [itt leírt utasításokat](relay-create-namespace-portal.md).

## <a name="define-a-wcf-service-contract"></a>A WCF szolgáltatási szerződés megadása

A szolgáltatási szerződés Megadja, milyen műveleteket (webszolgáltatás-terminológia a metódusokhoz és függvényeket) a szolgáltatás támogatja. A szerződések a C++, a C# vagy a Visual Basic felület meghatározásával jönnek létre. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. A [ServiceContractAttriibute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútumot minden felületre, az [OperationContractAttribaute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútumot pedig minden műveletre alkalmazni kell. Ha egy felület egy metódusa rendelkezik a [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) attribútummal, de nem rendelkezik az [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) attribútummal, nem lesz közzétéve. A feladatok kódja megtalálható az eljárást követő példában. A szerződések és szolgáltatások részletesebb leírása a WCF-dokumentáció [Designing and Implementing Services](https://msdn.microsoft.com/library/ms729746.aspx) (Szolgáltatások tervezése és megvalósítása) témakörében található.

### <a name="create-a-relay-contract-with-an-interface"></a>Továbbító szerződés létrehozása felülettel

1. Nyissa meg a Visual Studiót rendszergazdaként. Ehhez a **Start** menüben kattintson a jobb gombbal a programra, majd válassza a **Futtatás rendszergazdaként** parancsot.
2. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **File** (Fájl) menüre, és válassza a **New** (Új), majd a **Project** (Projekt) elemet. A **New Project** (Új projekt) párbeszédpanelen kattintson a **Visual C#** elemre (ha a **Visual C#** nem jelenik meg, keresse meg az **Other Languages** (Más nyelvek) területen). Kattintson a **Konzolalkalmazás (.NET-keretrendszer)** sablont, és adjon neki nevet **EchoService**. A projekt létrehozásához kattintson az **OK** gombra.

    ![][2]

3. Telepítse a Service Bus NuGet-csomagot. Ez a csomag automatikusan hivatkozásokat ad a Service Bus-könyvtárakhoz, valamint a WCF **System.ServiceModel** névtérhez. A [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. A Service Bus számos WCF-objektumot és -attribútumot használ a szolgáltatási szerződések meghatározására.

    A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson **NuGet-csomagok kezelése...** . Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Ügyeljen arra, hogy a projekt neve ki legyen jelölve a **Version(s)** (Verzió(k)) mezőben. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

    ![][3]
4. A Solution Explorerben (Megoldáskezelőben) kattintson duplán a Program.cs fájlra a szerkesztőben való megnyitásához, ha még nincs megnyitva.
5. Adja hozzá a következő using utasításokat a fájl elejéhez:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Módosítsa a névtér alapértelmezett **EchoService** nevét a következőre: **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > Ebben az oktatóanyagban a C# névteret használja **Microsoft.ServiceBus.Samples**, amely a szerződés-alapú névtere felügyelt típus, amely a konfigurációs fájl használatban van a [a WCF-ügyfél konfigurálása](#configure-the-wcf-client) a lépést. A minta létrehozásakor bármilyen névteret megadhat, az oktatóanyag azonban nem fog működni, ha ezután nem módosítja a szerződés és a szolgáltatás névtereit is ennek megfelelően az alkalmazáskonfigurációs fájlban. Az App.config fájlban megadott névtérnek egyeznie kell a C# fájlokban megadott névtérrel.
   >
   >
7. Közvetlenül után a `Microsoft.ServiceBus.Samples` névtér-deklaráció, de a névtéren belül, adja meg egy új nevű felületet `IEchoContract` , és alkalmazza a `ServiceContractAttribute` attribútumot a felületre a névtér értékű `http://samples.microsoft.com/ServiceModel/Relay/`. A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke ehelyett egyedi azonosítóként van használatban ehhez a szerződéshez. A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez. Illessze be a következő kódot a névtér-deklaráció után:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > A szolgáltatási szerződés névtere általában tartalmaz egy elnevezési sémát, amely tartalmazza a verzióinformációkat. Ha a verzióinformációk szerepelnek a szolgáltatási szerződés névterében, a szolgáltatások képesek elkülöníteni a nagyobb módosításokat egy új szolgáltatási szerződés új névtérrel való meghatározása, valamint egy új végponton való megjelenítése révén. Ezen a módon kikapcsolja az ügyfelek továbbra is használhatja a régi szolgáltatási szerződést anélkül, hogy frissíteni kell. A verzióinformációk dátumot vagy buildszámot tartalmazhatnak. További információ: [Service Versioning](http://go.microsoft.com/fwlink/?LinkID=180498) (Szolgáltatás verziószámozása). A jelen oktatóanyag esetén a szolgáltatási szerződés elnevezési sémája nem tartalmazza a verzióinformációkat.
   >
   >
8. Belül a `IEchoContract` csatoló, deklaráljon egy metódust az egyetlen művelettel a `IEchoContract` szerződés által a felületen, és alkalmazza a `OperationContractAttribute` attribútumot az alábbiak szerint a WCF továbbító szerződés részeként közzétenni kívánt metódusra:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Közvetlenül az `IEchoContract` felület deklarációja után deklaráljon egy csatornát, amely örökli az `IEchoContract` és az `IClientChannel` felületek tulajdonságait is, ahogy az itt látható:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    A csatorna egy olyan WCF-objektum, amelyen keresztül a gazda és az ügyfél információkat adnak át egymásnak. Később kódot ír majd a csatornára, hogy az echo utasítás használatával kommunikálja az információkat a két alkalmazás között.
10. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre, vagy nyomja meg a **Ctrl+Shift+B** billentyűkombinációt az eddigi munkája pontosságának ellenőrzéséhez.

### <a name="example"></a>Példa

A következő kód bemutatja egy WCF továbbító szerződést meghatározó alapszintű felületet.

```csharp
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

## <a name="implement-the-wcf-contract"></a>A WCF szerződés megvalósítása

Egy Azure relay létrehozásához szükséges, hogy először létre kell hoznia a szerződést, amelyet egy felület használatával. A felület létrehozásáról az előző lépésben találhat további információkat. A következő lépés a felület megvalósítása. Ebbe beletartozik egy `EchoService` nevű osztály létrehozása, amely megvalósítja a felhasználó által megadott `IEchoContract` felületet. A felület megvalósítása után egy App.config konfigurációs fájl segítségével konfigurálhatja azt. A konfigurációs fájl tartalmazza a szükséges információkat az alkalmazás, például a szolgáltatás nevét, a neve, a szerződés és a továbbítási szolgáltatás folytatott kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód megtalálható az eljárást követő példában. A szolgáltatási szerződések megvalósításáról a WCF-dokumentáció [Implementing Service Contracts](https://msdn.microsoft.com/library/ms733764.aspx) (Szolgáltatási szerződések megvalósítása) szakaszában található átfogó ismertetés.

1. Hozzon létre egy új, `EchoService` nevű osztályt közvetlenül az `IEchoContract` felület meghatározása után. Az `EchoService` osztály megvalósítja az `IEchoContract` felületet.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban található, mint a felületdefiníció és a `Main` metódus.
2. Alkalmazza a [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) attribútumot az `IEchoContract` felületre. Az attribútum megadja a szolgáltatás nevét és névterét. Ezután az `EchoService` osztály a következőképp jelenik meg:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Valósítsa meg az `IEchoContract` felületen meghatározott `Echo` metódust az `EchoService` osztályban.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre az eddigi munkája pontosságának ellenőrzéséhez.

### <a name="define-the-configuration-for-the-service-host"></a>A szolgáltatásgazda konfigurációjának meghatározása

1. A konfigurációs fájl nagyon hasonlít a WCF konfigurációs fájlra. Ez magában foglalja a szolgáltatás nevét, végpontját (Ez azt jelenti, hogy azt a helyet, amely Azure továbbítási elérhetővé teszi az ügyfelek és a gazdáknak az egymással való kommunikációhoz) és kötését (a kommunikációhoz használt protokoll típusát). A fő különbség az, hogy ez a konfigurált szolgáltatásvégpont egy [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) kötésre hivatkozik, amely nem része a .NET-keretrendszernek. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) a szolgáltatás által meghatározott kötés egyik.
2. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán az App.config fájlra a Visual Studio-szerkesztőben való megnyitásához.
3. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
4. A `<system.serviceModel>` címkéken belül adjon hozzá egy `<services>` elemet. Több relay alkalmazásban definiálhat egy egyetlen konfigurációs fájlban. Ez az oktatóanyag viszont csak egyet határoz meg.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. A `<services>` elemen belül adjon hozzá egy `<service>` elemet a szolgáltatás nevének meghatározásához.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. A `<service>` elemen belül határozza meg a végpont szerződés helyét, valamint a végpont kötésének típusát.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    A végpont meghatározza, hogy az ügyfél hol keresi majd a gazdaalkalmazást. Később az oktatóprogram ebben a lépésben URI, amely teljesen közzéteszi a gazdagépet keresztül Azure Relay létrehozásához. A kötés deklarálja, hogy használjuk TCP protokollt a relay szolgáltatással való kommunikációra.
7. A **Build** (Létrehozás) menüben kattintson a **Build Solution** (Megoldás létrehozása) elemre a munkája pontosságának ellenőrzéséhez.

### <a name="example"></a>Példa

A következő kód a szolgáltatási szerződés megvalósítását mutatja be.

```csharp
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

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>Üzemeltetése és futtatása egy alapszintű webszolgáltatás a továbbítási szolgáltatás regisztrálása

Ez a lépés ismerteti, hogyan lehet egy Azure-továbbítási szolgáltatás futtatásához.

### <a name="create-the-relay-credentials"></a>A továbbító hitelesítő adatok létrehozása

1. A `Main()` metódusban hozzon létre két változót, amelyben a konzolablakból beolvasott névtér és SAS-kulcs tárolható.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    A SAS-kulcs később a projekt eléréséhez használandó. A névteret a rendszer paraméterként átadja a `CreateServiceUri` számára szolgáltatás URI létrehozásához.
2. Egy [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) objektum segítségével deklarálhatja, hogy SAS-kulcsot fog használni hitelesítési típusként. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>A szolgáltatás alapszintű cím létrehozása

Az előző lépésben felvett kód után hozzon létre egy `Uri` a szolgáltatás alapszintű címéhez példánya. Ez az URI megadja a Service Bus-sémát, a névteret és a szolgáltatási felület útvonalát.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Az „sb” a Service Bus-séma rövidítése, és azt jelzi, hogy a TCP protokollt használjuk. Ez már korábban is jelezve volt a konfigurációs fájlban, amikor kötésként a [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) lett megadva.

Az oktatóanyaghoz az URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Hozza létre és konfigurálja a szolgáltatásgazda

1. A csatlakozási mód beállítása legyen `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    A csatlakozási mód írja le a protokollt, a szolgáltatás használatával kommunikálnak a továbbítási szolgáltatás; HTTP vagy TCP. Az alapértelmezett beállítás használata `AutoDetect`, a szolgáltatás megpróbál csatlakozni Azure továbbítási TCP, amennyiben az rendelkezésre áll, és a HTTP-alapú Ha TCP nem érhető el. Vegye figyelembe, hogy ez nem azonos a szolgáltatás által az ügyfél-kommunikációhoz megadott protokollal. Azt a protokollt az alkalmazott kötés határozza meg. A szolgáltatás használhatja például a [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) kötést, amely megadja, hogy a végpont kommunikál az ügyfelek HTTP Protokollon keresztül. Megadhatja, hogy ugyanazt a szolgáltatást **ConnectivityMode.AutoDetect** , hogy a szolgáltatás TCP protokollon keresztül kommunikál Azure továbbító.
2. Hozza létre a szolgáltatás gazdáját a szakaszban korábban létrehozott URI segítségével.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    A szolgáltatásgazda a WCF-objektum, amely a szolgáltatást példányosítja. Ebben a lépésben adja át számára a létrehozni kívánt szolgáltatástípust (`EchoService` típus), valamint a címet is, amelyen közzé szeretné tenni a szolgáltatást.
3. A Program.cs fájl elejéhez adjon hozzá a következőkre mutató hivatkozásokat: [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) és [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. Újra a `Main()` metódusban konfigurálja a végpontot a nyilvános hozzáférés engedélyezésére.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ez a lépés a továbbítási szolgáltatás, amely az alkalmazás nyilvánosan az ATOM-hírcsatornát a projekthez megvizsgálásával tájékoztatja. Ha a **DiscoveryType** beállítása **private**, attól még egy ügyfél elérheti a szolgáltatást. A szolgáltatás azonban nem jelent a továbbítási névtér kereséséhez. Az ügyfélnek ehhez már ismernie kell a végpont elérési útját.
5. Alkalmazza a szolgáltatás hitelesítő adatait az App.config fájlban meghatározott szolgáltatásvégpontokra:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Ahogyan az az előző lépésben elhangzott, van mód deklarálni több szolgáltatást és végpontot a konfigurációs fájlban. Ha így tett, ez a kód végighalad a konfigurációs fájlon, és megkeres minden végpontot, amelyre alkalmaznia kell a hitelesítő adatokat. Ebben az oktatóanyagban viszont a konfigurációs fájl csak egy végponttal rendelkezik.

### <a name="open-the-service-host"></a>A szolgáltatásgazda megnyitása

1. Nyissa meg a szolgáltatást.

    ```csharp
    host.Open();
    ```
2. Tájékoztassa a felhasználót arról, hogy a szolgáltatás fut, és magyarázza el, hogyan lehet leállítani.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Ha kész, zárja be a szolgáltatásgazdát.

    ```csharp
    host.Close();
    ```
4. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt a projekt felépítéséhez.

### <a name="example"></a>Példa

A befejezett szolgáltatáskód hibáit a következőképpen kell megjelennie. A kód tartalmazza a szolgáltatási szerződés és a korábbi lépések megvalósítását az oktatóanyag, és működteti a szolgáltatást egy konzolalkalmazást.

```csharp
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

A következő lépés, hogy hozzon létre egy ügyfélalkalmazást és a későbbi lépésekben megvalósítandó szolgáltatási szerződés meghatározása. Vegye figyelembe, hogy sok lépés hasonlítanak szolgáltatás létrehozása lépéseire: a Szerződés meghatározása, szerkesztését az App.config fájlt, hitelesítő adatokkal való csatlakozáshoz a továbbítási szolgáltatás, és így tovább. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. Hozzon létre egy új projektet az ügyfél jelenlegi Visual Studio megoldásában az alábbiakat követve:

   1. A Solution Explorerben (Megoldáskezelőben) a szolgáltatást tartalmazó megoldásban kattintson a jobb gombbal az aktuális megoldásra (ne a projektre), és kattintson az **Add** (Hozzáadás) lehetőségre. Ezután kattintson a **New Project** (Új projekt) gombra.
   2. Az a **új projekt hozzáadása** párbeszédpanel, kattintson a **Visual C#** (Ha **Visual C#** jelenik meg, keresse meg a **más nyelvek**) elemre, jelölje be a **Konzolalkalmazás (.NET-keretrendszer)** sablont, és adjon neki nevet **EchoClient**.
   3. Kattintson az **OK** gombra.
      <br />
2. A Solution Explorerben (Megoldáskezelőben) kattintson duplán az **EchoClient** projekt Program.cs fájljára a szerkesztőben való megnyitásához, ha még nincs megnyitva.
3. Módosítsa a névtér alapértelmezett `EchoClient` nevét a következőre: `Microsoft.ServiceBus.Samples`.
4. Telepítse a [Service Bus NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.ServiceBus): a Megoldáskezelőben kattintson a jobb gombbal a **EchoClient** projektre, és kattintson a **NuGet-csomagok kezelése**. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

    ![][3]
5. Adjon hozzá egy `using` utasítást a Program.cs fájlban található [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) névtérhez.

    ```csharp
    using System.ServiceModel;
    ```
6. Adja hozzá a szolgáltatási szerződés meghatározását a névtérhez, ahogyan az az alábbi példában látható. Ügyeljen arra, hogy a meghatározás azonos legyen a **Szolgáltatás** projektben használt meghatározással. Ezt a kódot a `Microsoft.ServiceBus.Samples` névtér tetején adja hozzá.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Nyomja le a **Ctrl+Shift+B** billentyűkombinációt az ügyfél felépítéséhez.

### <a name="example"></a>Példa

A következő kódot a Program.cs fájl aktuális állapotát jeleníti meg a **EchoClient** projekt.

```csharp
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

## <a name="configure-the-wcf-client"></a>A WCF-ügyfél konfigurálása

Ebben a lépésben létrehozza az App.config fájlt egy alapszintű ügyfélalkalmazáshoz, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. Ez az App.config fájl határozza meg a szerződést, a kötést és a végpont nevét. A feladatokhoz használt kód megtalálható az eljárást követő példában.

1. A Solution Explorerben (Megoldáskezelőben) az **EchoClient** projektben kattintson duplán az **App.config** fájlra a fájl a Visual Studio-szerkesztőben való megnyitásához.
2. Az `<appSettings>` elemben cserélje le a helyőrzőket a szolgáltatási névtér nevére, valamint a korábbi lépésben másolt SAS-kulcsra.
3. A system.serviceModel elemen belül adjon hozzá egy `<client>` elemet.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ez a lépés deklarálja, hogy WCF stílusú ügyfélalkalmazást határoz meg.
4. A `client` elemen belül határozza meg a nevet, a szerződést és a kötéstípust a végponthoz.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ez a lépés a végpont, a szolgáltatás, valamint azt, hogy az ügyfélalkalmazás TCP kommunikálni az Azure-továbbítási meghatározott szerződést a nevét adja meg. A következő lépés a végpont neve használatával ezt a végpont-konfigurációt összekapcsolja a szolgáltatás URI-jával.
5. Kattintson a **fájl**, majd kattintson a **összes mentése**.

## <a name="example"></a>Példa

A következő kód az Echo ügyfél App.config fájlját mutatja.

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
Ebben a lépésben megvalósít egy alapszintű ügyfélalkalmazást, amely hozzáfér az ebben az oktatóanyagban korábban létrehozott szolgáltatáshoz. A szolgáltatás hasonló, az ügyfél hajtja végre számos Azure-továbbítási eléréséhez ugyanazokat a műveleteket:

1. Beállítja a csatlakozási módot.
2. Létrehozza a gazdaszolgáltatás helyét megadó URI-t.
3. Megadja a biztonsági hitelesítő adatokat.
4. Alkalmazza a hitelesítő adatokat a kapcsolatra.
5. Megnyitja a kapcsolatot.
6. Végrehajtja az alkalmazásspecifikus feladatokat.
7. Bezárja a kapcsolatot.

Azonban a fő különbségek egyike, hogy az ügyfélalkalmazás egy csatorna való kapcsolódáshoz használ a továbbítási szolgáltatás, mivel a szolgáltatás használja a következőt hívja **ServiceHost**. A feladatokhoz használt kód megtalálható az eljárást követő példában.

### <a name="implement-a-client-application"></a>Ügyfélalkalmazás megvalósítása
1. A csatlakozási mód beállítása legyen **AutoDetect**. Adja hozzá a következő kódot az **EchoClient** alkalmazás `Main()` metódusában.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Határozzon meg változókat, amelyek a konzolról beolvasott szolgáltatásnévtér és SAS-kulcs értékeit tárolják.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Az URI, amely meghatározza a helyét a gazdagépen a továbbítási projekt létrehozásához.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Hozza létre a hitelesítő objektumot a szolgáltatásnévtér végpontjához.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Hozza létre az App.config fájlban leírt konfigurációt betöltő csatornagyárat.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    A csatornagyár egy WCF-objektum, amely létrehoz egy csatornát, amelyen keresztül a szolgáltatás és az ügyfélalkalmazások kommunikálnak.
6. A hitelesítő adatok érvényesek.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Hozza létre és nyissa meg a csatornát a szolgáltatás felé.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Írja meg az Echo alapszintű felhasználói felületét és funkcióit.

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

    Vegye figyelembe, hogy a kód a szolgáltatás proxyjaként használja a csatornaobjektum példányát.
9. Zárja be a csatornát, és zárja be a gyárat.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>Példa

A befejezett kód kell a következőképpen jelenik meg, hozzon létre egy ügyfélalkalmazást, hogyan hívhatja meg a szolgáltatás működésére, és zárhatja be az ügyfelet a művelet hívása után megjelenítő befejeződött.

```csharp
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

## <a name="run-the-applications"></a>Az alkalmazások futtatása

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

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    A szolgáltatásalkalmazás a konzolablakba írja a címet, amelyen figyel, ahogyan az az előbbi példában is látható.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. Az **EchoClient** konzolablakában adja meg a korábban a szolgáltatásalkalmazáshoz megadott adatokat. Az előző lépéseket követve adja meg az ügyfélalkalmazáshoz ugyanezeket a szolgáltatásnévtér és SAS-kulcs értékeket.
11. Az értékek megadása után az ügyfél megnyit egy csatornát a szolgáltatás felé, és szövegbevitelre kéri, ahogyan az az alábbi példa konzolkimenetben látható.

    `Enter text to echo (or [Enter] to exit):`

    Adjon meg szöveget, amelyet a szolgáltatásalkalmazásba szeretne küldeni, majd nyomja le az Enter billentyűt. Ezt a szöveget az Echo szolgáltatásműveleten keresztül küldi el a rendszer a szolgáltatásnak, és megjelenik a szolgáltatás konzolablakában, ahogyan az az alábbi példa kimenetben látható.

    `Echoing: My sample text`

    Az ügyfélalkalmazás fogadja az `Echo` művelet visszaadott értékét, amely az eredeti szöveg, és a konzolablakba írja. Az alábbi egy példa kimenet az ügyfél konzolablakából.

    `Server echoed: My sample text`
12. Ezzel a módszerrel folytathatja szöveges üzenetek küldését az ügyfélről a szolgáltatásba. Ha kész van, a két alkalmazás befejezéséhez nyomja le az Enter billentyűt az ügyfél és a szolgáltatás konzolablakában is.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan hozhat létre egy Azure-továbbítási-ügyfélalkalmazást és szolgáltatást a Service Bus WCF továbbítási képességeivel. Egy alkalmazó hasonló oktatóanyagért [Service Bus üzenetkezelés](../service-bus-messaging/service-bus-messaging-overview.md#brokered-messaging), lásd: [Ismerkedés a Service Bus-üzenetsorok](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Azure Relayjel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket.

* [Az Azure Service Bus-architektúra áttekintése](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Az Azure Relay áttekintése](relay-what-is-it.md)
* [A WCF továbbító szolgáltatás a .NET használatával](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
