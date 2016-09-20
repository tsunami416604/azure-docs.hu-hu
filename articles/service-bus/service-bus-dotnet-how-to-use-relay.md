<properties
    pageTitle="A Service Bus Relay használata a .NET-keretrendszerrel | Microsoft Azure"
    description="Ismerje meg, hogyan használhatja az Azure Service Bus Relay szolgáltatást két különböző helyen üzemeltetett alkalmazás összekapcsolására."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="05/06/2016"
    ms.author="sethm"/>


# Az Azure Service Bus Relay szolgáltatás használata

Ez a cikk a Service Bus Relay szolgáltatás használatát ismerteti. A kódminták C# nyelven íródtak, és a Windows Communication Foundation (WCF) API-t használják a Service Bus-összeállításban található bővítményekkel. A Service Bus Relayjel kapcsolatos további információkért lásd a [Service Bus továbbítón keresztüli üzenetcsere](service-bus-relay-overview.md) áttekintését.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Mi a Service Bus Relay?

A [Service Bus *Relay* szolgáltatás](service-bus-relay-overview.md) lehetővé teszi hibrid alkalmazások összeállítását, amelyek egy Azure-adatközpontban és a saját helyszíni vállalati környezetben is futnak. A Service Bus Relay ezt úgy segíti elő, hogy biztonságosan lehetővé teszi a vállalati hálózaton belüli Windows Communication Foundation (WCF-) szolgáltatások közzétételét a nyilvános felhőben anélkül, hogy meg kellene nyitni egy tűzfalkapcsolatot, vagy zavaró módosításokat kellene végrehajtani a vállalati hálózati infrastruktúrában.

![A Relay szolgáltatással kapcsolatos fogalmak](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

A Service Bus Relay lehetővé teszi WCF-szolgáltatások üzemeltetését a meglévő vállalati környezetben. Az ilyen WCF-szolgáltatásokhoz érkező bejövő munkamenetek és kérések figyelését ezután az Azure-ban futó Service Bus szolgáltatásnak delegálhatja. Ez lehetővé teszi a szolgáltatások közzétételét az Azure-ban futó alkalmazáskódok, illetve mobil dolgozók vagy külső hálózaton lévő partnerkörnyezetek számára. A Service Bus használatával részletesen és biztonságosan vezérelhető, hogy ki férhet hozzá a szolgáltatásokhoz. Hatékony és biztonságos módot biztosít a meglévő vállalati megoldásaiból származó alkalmazások és adatok közzétételére, valamint kihasználja a felhő előnyeit.

Ez a cikk bemutatja, hogyan használhatja a Service Bus Relayt egy WCF-webszolgáltatás létrehozására, amelyet a két fél között biztonságos párbeszédet megvalósító TCP-csatornakötéssel tehet közzé.

## Szolgáltatásnévtér létrehozása

Az Service Bus Relay Azure-ban való használatához először egy névteret kell létrehoznia. A névtér egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Szolgáltatásnévtér létrehozása:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## A Service Bus NuGet-csomag beszerzése

A Service Bus API beszerzésének, valamint az alkalmazások az összes Service Bus-függőséggel való konfigurálásának legegyszerűbb módja a [Service Bus NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.ServiceBus) telepítése. A NuGet-csomag az alkalmazásban való telepítéséhez tegye a következőket:

1.  A Megoldáskezelőben kattintson a jobb gombbal a **Hivatkozások** elemre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
2.  Keressen a „Service Bus” kifejezésre, és válassza ki az **Microsoft Azure Service Bus** elemet. Kattintson az **Install** (Telepítés) gombra a telepítés befejezéséhez, majd zárja be a következő párbeszédpanelt.

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## A Service Bus használata egy SOAP-webszolgáltatás TCP-vel történő közzétételére és felhasználására

Egy meglévő SOAP WCF-webszolgáltatás külső felhasználásra történő közzétételéhez módosítania kell a szolgáltatás kötéseit és címeit. Ehhez a konfigurációs fájl módosítására vagy a kód módosítására lehet szükség, attól függően, hogy hogyan állította be és konfigurálta a WCF-szolgáltatásokat. Vegye figyelembe, hogy a WCF lehetővé teszi több hálózati végpont használatát ugyanabban a szolgáltatásban, tehát megtarthatja a meglévő belső végpontokat, miközben Service Bus-végpontokat vesz fel külső hozzáférés céljából.

Ebben a feladatban egy egyszerű WCF-szolgáltatást fog összeállítani, majd hozzáad egy Service Bus-figyelőt. A gyakorlat feltételezi a Visual Studio bizonyos fokú ismeretét, ezért nem ismerteti a projekt létrehozásának minden részletét. Ehelyett magára a kódra összpontosít.

Az alábbi lépések megkezdése előtt végezze el a következő eljárást a környezet beállításához:

1.  A Visual Studióban a megoldáson belül hozzon létre egy konzolalkalmazást, amely két projektet, az „Ügyfél” és a „Szolgáltatás” projektet tartalmaz.
2.  Mindkét projekthez adja hozzá a Microsoft Azure Service Bus NuGet-csomagot. Ez hozzáadja a projektekhez az összes szükséges összeállítási referenciát.

### A szolgáltatás létrehozása

Először hozza létre a szolgáltatást. Minden WCF-szolgáltatás legalább három különálló részből áll:

-   Egy szerződés meghatározásából, amely leírja, hogy milyen üzenetek lesznek cserélve és milyen műveletek lesznek meghívva.
-   A fenti szerződés megvalósításából.
-   Egy állomásból, amely üzemelteti a WCF-szolgáltatást, és amely elérhetővé tesz számos végpontot.

A jelen szakaszban lévő kódpéldák mindhárom összetevőt tartalmazzák.

A szerződés egyetlen műveletet, a(z) `AddNumbers` műveletet definiálja, amely két számot ad össze, és visszaadja az eredményt. A(z) `IProblemSolverChannel` interfész lehetővé teszi az ügyfél számára, hogy egyszerűbben kezelje a proxy élettartamát. Az ilyen interfész létrehozása ajánlott eljárás. Érdemes ezt a szerződést egy különálló fájlban elhelyezni, hogy az „Ügyfél” és a „Szolgáltatás” projektből is hivatkozni lehessen a fájlra, de a kódot bemásolhatja mindkét projektbe is.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Ha a szerződés elkészült, a megvalósítása nagyon egyszerű.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Szolgáltatásgazda konfigurálása szoftveresen

Ha a szerződés és a megvalósítás elkészült, a szolgáltatás üzemeltethető. Az üzemeltetés egy [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) objektumon belül történik, amely gondoskodik a szolgáltatás felügyelő példányairól, és üzemelteti az üzeneteket figyelő végpontokat. A következő kód egy normál helyi végponttal és egy Service Bus-végponttal konfigurálja a szolgáltatást, hogy bemutassa a belső és külső végpontok egymás melletti megjelenését. A *namespace* karakterláncot helyettesítse a névtér nevével, a *yourKey* karakterláncot pedig az előző lépésben beszerzett SAS-kulccsal.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

A példában két végpontot fog létrehozni, amelyek ugyanazon a szerződésmegvalósításhoz tartoznak. Az egyik helyi, a másik pedig a Service Buson keresztül van kivetítve. A kettő közötti alapvető különbséget a kötéseik jelentik. Ez a helyi végpont esetében [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx), a Service Bus-végpont és -címek esetében pedig [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx). A helyi végpont egy helyi hálózati címmel rendelkezik egy különálló porttal. A Service Bus-végpont a(z) `sb` karakterláncból, a névtér nevéből és a „solver” elérési útból álló végpontcímmel rendelkezik. Ennek az eredménye a(z) `sb://[serviceNamespace].servicebus.windows.net/solver` URI, amely egy teljes képzésű külső DNS-névvel rendelkező Service Bus TCP-végpontként azonosítja a szolgáltatásvégpontot. Ha a kódot a helyőrzők a fentiekben elmagyarázott behelyettesítésével elhelyezi a **Szolgáltatás** alkalmazás `Main` függvényében, egy működő szolgáltatást kap. Ha szeretné, hogy a szolgáltatás kizárólag a Service Buson figyeljen, távolítsa el a helyi végpont deklarációját.

### Szolgáltatásgazda konfigurálása az App.config fájlban

Az állomást az App.config fájl segítségével is konfigurálhatja. A szolgáltatásüzemeltetési kód ez esetben a következő példában látható.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

A végpontdefiníciók ekkor az App.config fájlba kerülnek. Vegye figyelembe, hogy a NuGet-csomag már hozzáadta az App.config fájlhoz a definíciók egy tartományát, amelyek a Service Bushoz szükséges konfigurációs bővítmények. A következő példának, amely pontosan megegyezik az előző kóddal, közvetlenül a **system.serviceModel** elem alatt kell megjelennie. A kódpélda feltételezi, hogy a projekt C# névterének neve **Service**.
A helyőrzőket cserélje le a Service Bus-szolgáltatásának névterére és SAS-kulcsára.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

A módosítások végrehajtása után a szolgáltatás ugyanúgy elindul, mint korábban, de most két élő végponttal: egy helyivel, és eggyel, amely a felhőben figyel.

### Az ügyfél létrehozása

#### Ügyfél konfigurálása szoftveresen

A szolgáltatás felhasználásához összeállíthat egy WCF-ügyfelet egy [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) objektum használatával. A Service Bus egy jogkivonat-alapú biztonsági modellt használ, amely a SAS segítségével van megvalósítva. A [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) osztály egy beépített gyári metódusokat tartalmazó biztonságijogkivonat-szolgáltatót képvisel, amelyek néhány jól ismert jogkivonat-szolgáltatót adnak vissza. Az alábbi példa a [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) metódust használja a megfelelő SAS-jogkivonat beszerzésének kezeléséhez. A név és a kulcs az előző szakaszban leírtak szerint a portálról lekért értékek.

Először hivatkozzon a(z) `IProblemSolver` szerződéskódra, vagy másolja a szolgáltatásból az ügyfélprojektbe.

Ezután cserélje le az ügyfél `Main` metódusában lévő kódot, és ismét cserélje le a helyőrzőket a Service Bus-névterére és a SAS-kulcsára.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Most már lefordíthatja az ügyfelet és a szolgáltatást, futtathatja őket (először a szolgáltatást), így az ügyfél meghívja a szolgáltatást, és a **9** értéket adja ki. Az ügyfelet és a szolgáltatást futtathatja eltérő gépeken, akár hálózatokon keresztül is, a kommunikáció továbbra is működni fog. Az ügyfélkód a felhőben vagy helyben is futhat.

#### Ügyfél konfigurálása az App.config fájlban

A következő kód bemutatja, hogyan konfigurálhatja az ügyfelet az App.config fájl segítségével.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

A végpontdefiníciók ekkor az App.config fájlba kerülnek. A következő példának, amely megegyezik az előzőekben látható kóddal, közvetlenül a **system.serviceModel** elem alatt kell megjelennie. A korábbiakhoz hasonlóan a helyőrzőket itt is cserélje le a Service Bus-szolgáltatásának névterére és SAS-kulcsára.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Következő lépések

Most, hogy megismerte a Service Bus Relay szolgáltatás alapjait, az alábbi hivatkozásokból tudhat meg többet.

- [A Service Bus Relayen keresztüli üzenetcsere áttekintése](service-bus-relay-overview.md)
- [Azure Service Bus architectural overview (Az Azure Service Bus) architektúrájának áttekintése](service-bus-fundamentals-hybrid-solutions.md)
- Letölthet Service Bus-mintákat az [Azure-minták][] közül, illetve megtekintheti a [Service Bus-minták áttekintését][].

  [Shared Access Signature Authentication with Service Bus]: service-bus-shared-access-signature-authentication.md
  [Azure-minták]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Service Bus-minták áttekintését]: service-bus-samples.md


<!--HONumber=sep16_HO1-->


