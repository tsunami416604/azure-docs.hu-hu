---
title: Ismerkedés az Azure Relay WCF továbbítja a .NET-keretrendszerben |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Relay WCF-továbbítók két, különböző helyen üzemeltetett alkalmazás csatlakozni.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 6c8498a43b127fecc02473177ac955ae51a647d6
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854116"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Az Azure Relay WCF közvetítők használata a .NET használatával
Ez a cikk ismerteti, hogyan használható az Azure Relay szolgáltatásban. A kódminták C# nyelven íródtak, és a Windows Communication Foundation (WCF) API-t használják a Service Bus-összeállításban található bővítményekkel. Az Azure relay kapcsolatos további információkért lásd: a [Azure Relay áttekintése](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Mi az a WCF-továbbító?

Az Azure [ *WCF-továbbító* ](relay-what-is-it.md) szolgáltatás lehetővé teszi hibrid alkalmazások összeállítását, amelyek egy Azure-adatközpontban és a saját helyszíni vállalati környezetben is futnak. A továbbítási szolgáltatás megkönnyíti ezt engedélyezi, hogy biztonságosan kellene nyitni egy tűzfalkapcsolatot, vagy zavaró megkövetelése nélkül a nyilvános felhő, a vállalati hálózaton található Windows Communication Foundation (WCF) szolgáltatásokat vállalati hálózati infrastruktúrában módosításait.

![A WCF Relay szolgáltatással kapcsolatos fogalmak](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Az Azure Relay lehetővé teszi WCF-szolgáltatások üzemeltetését a meglévő vállalati környezetben. Ezután delegálhatja figyeli a bejövő munkamenetek és kérések a WCF-szolgáltatásokhoz az Azure-ban futó továbbító szolgáltatás. Ez lehetővé teszi a szolgáltatások közzétételét az Azure-ban futó alkalmazáskódok, illetve mobil dolgozók vagy külső hálózaton lévő partnerkörnyezetek számára. Relay segítségével szabályozhatja, hogy biztonságosan érheti el az ezeket a szolgáltatásokat, részletesen. Hatékony és biztonságos módot biztosít a meglévő vállalati megoldásaiból származó alkalmazások és adatok közzétételére, valamint kihasználja a felhő előnyeit.

Ez a cikk ismerteti, hogyan hozzon létre egy WCF-webszolgáltatás üzembe helyezése a TCP-csatornán, kötés használatával, amely a két fél között biztonságos párbeszédet megvalósító Azure Relay használatával.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag beszerzése
A Service Bus API beszerzésének, valamint az alkalmazások az összes Service Bus-függőséggel való konfigurálásának legegyszerűbb módja a [Service Bus NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.ServiceBus) telepítése. A NuGet-csomagnak a projektben való telepítéséhez tegye a következőket:

1. A Megoldáskezelőben kattintson a jobb gombbal a **Hivatkozások** elemre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
2. Keressen a „Service Bus” kifejezésre, és válassza ki az **Microsoft Azure Service Bus** elemet. Kattintson az **Install** (Telepítés) gombra a telepítés befejezéséhez, majd zárja be a következő párbeszédpanelt:
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Közzétételére és felhasználására a TCP SOAP-webszolgáltatás
Egy meglévő SOAP WCF-webszolgáltatás külső felhasználásra történő közzétételéhez módosítania kell a szolgáltatás kötéseit és címeit. Ehhez a konfigurációs fájl módosítására vagy a kód módosítására lehet szükség, attól függően, hogy hogyan állította be és konfigurálta a WCF-szolgáltatásokat. Vegye figyelembe, hogy a WCF lehetővé teszi, hogy több hálózati végpont ugyanabban a szolgáltatásban, tehát megtarthatja a meglévő belső végpontokat egyszerre külső hozzáférés céljából továbbítási végpontok hozzáadása során.

Ebben a feladatban egy egyszerű WCF-szolgáltatást hozhat létre, és hozzá tud adni a relay-figyelő. A gyakorlat feltételezi a Visual Studio bizonyos fokú ismeretét, ezért nem ismerteti a projekt létrehozásának minden részletét. Ehelyett magára a kódra összpontosít.

Az alábbi lépések megkezdése előtt végezze el a következő eljárást a környezet beállításához:

1. A Visual Studióban a megoldáson belül hozzon létre egy konzolalkalmazást, amely két projektet, az „Ügyfél” és a „Szolgáltatás” projektet tartalmaz.
2. Mindkét projekthez adja hozzá a Service Bus NuGet-csomagot. Ez a csomag hozzáadja a projektekhez az összes szükséges összeállítási referenciát.

### <a name="how-to-create-the-service"></a>A szolgáltatás létrehozása
Először hozza létre a szolgáltatást. Minden WCF-szolgáltatás legalább három különálló részből áll:

* Egy szerződés meghatározásából, amely leírja, hogy milyen üzenetek lesznek cserélve és milyen műveletek lesznek meghívva.
* A szerződés megvalósítása.
* Egy állomásból, amely üzemelteti a WCF-szolgáltatást, és amely több végpontot is elérhetővé tesz.

A jelen szakaszban lévő kódpéldák mindhárom összetevőt tartalmazzák.

A szerződés egyetlen műveletet, a(z) `AddNumbers` műveletet definiálja, amely két számot ad össze, és visszaadja az eredményt. A(z) `IProblemSolverChannel` interfész lehetővé teszi az ügyfél számára, hogy egyszerűbben kezelje a proxy élettartamát. Az ilyen interfész létrehozása ajánlott eljárás. Érdemes ezt a szerződést egy különálló fájlban elhelyezni, hogy az „Ügyfél” és a „Szolgáltatás” projektből is hivatkozni lehessen a fájlra, de a kódot bemásolhatja mindkét projektbe is.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Az érvényben lévő szerződés megvalósítása a következőképpen történik:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Szolgáltatásgazda konfigurálása szoftveresen
Ha a szerződés és a megvalósítás elkészült, a szolgáltatás üzemeltethető. Az üzemeltetés egy [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx) objektumon belül történik, amely gondoskodik a szolgáltatás felügyelő példányairól, és üzemelteti az üzeneteket figyelő végpontokat. A következő kód egy normál helyi végponttal és a egy továbbítási végpontot, hogy bemutassa a megjelenését, egymás mellett, belső és külső végpontok a szolgáltatás konfigurálja. A *namespace* sztringet helyettesítse a névtér nevével, a *yourKey* sztringet pedig az előző lépésben beszerzett SAS-kulccsal.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

A példában két végpontot fog létrehozni, amelyek ugyanazon a szerződésmegvalósításhoz tartoznak. Helyi és a egy Azure Relay keresztül van kivetítve. A kettő közötti a kötéseik jelentik. [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) számára a helyi és [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) a relay-végpont és a címek. A helyi végpont egy helyi hálózati címmel rendelkezik egy különálló porttal. A relay-végpont rendelkezik végpontcímmel karakterlánc `sb`, a névtér nevével, és az elérési út "solver". Ennek eredményeképpen az URI-t `sb://[serviceNamespace].servicebus.windows.net/solver`, azonosítja a szolgáltatásvégpontot (továbbítóként) a Service Bus TCP-végpontként külső teljesen minősített DNS-név. Ha a kódot a helyőrzők behelyettesítésével elhelyezi a **Szolgáltatás** alkalmazás `Main` függvényében, egy működő szolgáltatást kap. Ha azt szeretné, hogy a szolgáltatás kizárólag a relay, távolítsa el a helyi végpont deklarációját.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Szolgáltatásgazda konfigurálása az App.config fájlban
Az állomást az App.config fájl segítségével is konfigurálhatja. A szolgáltatásüzemeltetési kód ez esetben a következő példában látható.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

A végpontdefiníciók ekkor az App.config fájlba kerülnek. A NuGet-csomag már hozzáadta az App.config fájlhoz, amely az Azure Relay a szükséges konfigurációs bővítmények definíciók egy tartományát. A következő példának, amely pontosan megegyezik az előző kóddal, közvetlenül a **system.serviceModel** elem alatt kell megjelennie. A kódpélda feltételezi, hogy a projekt C# névterének neve **Service**.
A helyőrzőket cserélje le a relay-névtér nevét és a SAS-kulcsot.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

A módosítások végrehajtása után a szolgáltatás ugyanúgy elindul, mint korábban, de most két élő végponttal: egy helyivel, és eggyel, amely a felhőben figyel.

### <a name="create-the-client"></a>Az ügyfél létrehozása
#### <a name="configure-a-client-programmatically"></a>Ügyfél konfigurálása szoftveresen
A szolgáltatás felhasználásához összeállíthat egy WCF-ügyfelet egy [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) objektum használatával. A Service Bus egy jogkivonat-alapú biztonsági modellt használ, amely a SAS segítségével van megvalósítva. A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) osztály egy beépített gyári metódusokat tartalmazó biztonságijogkivonat-szolgáltatót képvisel, amelyek néhány jól ismert jogkivonat-szolgáltatót adnak vissza. A következő példa a [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) metódust használja a megfelelő SAS-jogkivonat beszerzésének kezeléséhez. A név és a kulcs az előző szakaszban leírtak szerint a portálról lekért értékek.

Először hivatkozzon a(z) `IProblemSolver` szerződéskódra, vagy másolja a szolgáltatásból az ügyfélprojektbe.

Ezután cserélje le a kódot a `Main` metódus az ügyfél ismét cserélje le a helyőrzőket a relay-névteret és SAS-kulcsot.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Most már lefordíthatja az ügyfelet és a szolgáltatást, futtathatja őket (először a szolgáltatást), így az ügyfél meghívja a szolgáltatást, és a **9** értéket nyomtatja ki. Az ügyfelet és a szolgáltatást futtathatja eltérő gépeken, akár hálózatokon keresztül is, a kommunikáció továbbra is működni fog. Az ügyfélkód a felhőben vagy helyben is futhat.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Ügyfél konfigurálása az App.config fájlban
A következő kód bemutatja, hogyan konfigurálhatja az ügyfelet az App.config fájl segítségével.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

A végpontdefiníciók ekkor az App.config fájlba kerülnek. A következő példának, amely megegyezik az előzőekben látható kóddal, közvetlenül alatt kell megjelennie a `<system.serviceModel>` elemet. Itt mint korábban, meg kell cserélje le a helyőrzőket a relay-névteret és SAS-kulcsot.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az alapokat, az Azure Relay, kövesse az alábbi hivatkozások további.

* [Mi az az Azure Relay?](relay-what-is-it.md)
* Töltse le a Service Bus-minták [Azure-minták] [ Azure samples] , vagy tekintse meg a [a Service Bus-minták áttekintését][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
