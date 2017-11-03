---
title: "Az Azure Diagnostics teljesítményszámlálók használata |} Microsoft Docs"
description: "Használja az Azure felhőszolgáltatások vagy a virtuális gép teljesítményszámlálók szűk keresztmetszetek kereséséhez, és a teljesítmény hangolására."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
ms.openlocfilehash: 2cf765cb034725199127c547a9b8b997a4a6089c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Hozzon létre és teljesítményszámlálók használata az Azure alkalmazásban
Ez a cikk ismerteti az előnyei és a teljesítményszámlálók üzembe az Azure-alkalmazásában. Az adatok gyűjtéséhez, a szűk keresztmetszetek található és a rendszer és az alkalmazások teljesítményének használhatja őket.

A Windows Server, az IIS és ASP.NET rendelkezésre álló teljesítményszámlálók is gyűjtött és az Azure webes szerepkörök, a feldolgozói szerepkörök és a virtuális gépek állapotának meghatározására használt. Hozhat létre, és egyéni teljesítményszámlálók.  

Láthatja a teljesítményszámláló-adatok

1. Közvetlenül a távoli asztal használatával érhető el a Teljesítményfigyelő eszközzel alkalmazásgazda
2. A System Center Operations Manager használatával az Azure felügyeleti csomag
3. A más Hálózatfigyelő eszközök, amelyhez hozzáférhetnek a diagnosztikai adatok átvitele az Azure storage. Lásd: [Store és diagnosztikai adatok megtekintése az Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) további információt.  

Az alkalmazás teljesítményének figyeléséről további információt a [Azure-portálon](http://portal.azure.com/), lásd: [figyelő felhőalapú szolgáltatások](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

További részletes útmutató a naplózás és nyomkövetés stratégia és diagnosztika és egyéb technikák segítségével problémák megoldásához és az Azure-alkalmazások optimalizálása létrehozásával, lásd: [fejlesztése Azure ajánlott eljárásai hibaelhárítása Alkalmazások](https://msdn.microsoft.com/library/azure/hh771389.aspx).

## <a name="enable-performance-counter-monitoring"></a>Engedélyezze a teljesítményszámláló megfigyeléséhez
Alapértelmezés szerint a teljesítményszámlálók nem engedélyezettek. Az alkalmazás vagy egy indítási tevékenységhez módosítania kell az egyes teljesítményszámlálókra, amelyet figyeli, hogy minden egyes szerepkör példánya kíván felvenni az alapértelmezett diagnosztikai ügynök konfigurációját.

### <a name="performance-counters-available-for-microsoft-azure"></a>A Microsoft Azure rendelkezésre álló teljesítményszámlálók
Azure található rendelkezésre álló teljesítményszámlálók egy részét biztosítja a Windows Server, az IIS és ASP.NET veremben. Az alábbi táblázat néhány teljesítményszámlálót mutat be különösen fontos az Azure-alkalmazások.

| Teljesítményszámláló-kategória: Objektum (példány) | Számláló neve | Referencia |
| --- | --- | --- |
| .NET CLR-kivételek (*globális*) |# Kiváltott kivétel / mp |Kivétel teljesítményszámlálói |
| .NET CLR-memória (*globális*) |% Töltött idő |Memóriateljesítmény-számlálók |
| ASP.NET |Az alkalmazások |ASP.NET teljesítményszámlálók |
| ASP.NET |Kérelem végrehajtási ideje |ASP.NET teljesítményszámlálók |
| ASP.NET |Megszakadt kérések |ASP.NET teljesítményszámlálók |
| ASP.NET |Munkavégző folyamata újraindul |ASP.NET teljesítményszámlálók |
| ASP.NET-alkalmazások (**teljes**) |Az összes kérelem |ASP.NET teljesítményszámlálók |
| ASP.NET-alkalmazások (**teljes**) |Kérelmek/másodperc |ASP.NET teljesítményszámlálók |
| Az ASP.NET 4.0.30319 |Kérelem végrehajtási ideje |ASP.NET teljesítményszámlálók |
| Az ASP.NET 4.0.30319 |Kérés várakozási ideje |ASP.NET teljesítményszámlálók |
| Az ASP.NET 4.0.30319 |Aktuális |ASP.NET teljesítményszámlálók |
| Az ASP.NET 4.0.30319 |Kérései |ASP.NET teljesítményszámlálók |
| Az ASP.NET 4.0.30319 |Visszautasított kérelmek száma |ASP.NET teljesítményszámlálók |
| Memory (Memória) |Rendelkezésre álló memória (MB) |Memóriateljesítmény-számlálók |
| Memory (Memória) |Bájtok |Memóriateljesítmény-számlálók |
| Processzor(_Total) |Processzor kihasználtsága |ASP.NET teljesítményszámlálók |
| TCPv4 |Kapcsolódási hibák |TCP-objektum |
| TCPv4 |Fennálló kapcsolatok |TCP-objektum |
| TCPv4 |Kapcsolatok alaphelyzetbe állítása |TCP-objektum |
| TCPv4 |Felosztja a küldött/mp |TCP-objektum |
| Hálózati Interface(*) |Fogadott bájtok/s |Hálózati kapcsolat objektum |
| Hálózati Interface(*) |Küldött bájtok/s |Hálózati kapcsolat objektum |
| Hálózati adapter (Microsoft virtuális gép Buszán hálózati Adapter _2) |Fogadott bájtok/s |Hálózati kapcsolat objektum |
| Hálózati adapter (Microsoft virtuális gép Buszán hálózati Adapter _2) |Küldött bájtok/s |Hálózati kapcsolat objektum |
| Hálózati adapter (Microsoft virtuális gép Buszán hálózati Adapter _2) |Összes bájt/mp |Hálózati kapcsolat objektum |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Hozzon létre és egyéni teljesítményszámlálóit hozzá az alkalmazáshoz
Azure támogatás létrehozásához és módosításához egyéni teljesítményszámlálók a webes és feldolgozói szerepkörök rendelkezik. A számlálók követheti és figyelheti az alkalmazás-specifikus viselkedését is használható. Hozzon létre, és törli az egyéni teljesítményszámláló-kategóriák és a kulcsszavak indítási tevékenységhez, webes vagy feldolgozói szerepkör emelt jogosultsági szintű.

> [!NOTE]
> Kód, amely módosítást hajt végre egyéni teljesítményszámlálóit kell emelt szintű engedélyekkel futtatásához. Ha a kód egy webes vagy feldolgozói szerepkör, a szerepkör tartalmaznia kell a címke <Runtime executionContext="elevated" /> megfelelően inicializálni a szerepkörhöz a ServiceDefinition.csdef fájlban.
>
>

Azure storage-ban a diagnosztikai ügynök egyéni teljesítményszámláló-adatokat küldhet.

A szabványos teljesítmény számlálóadatok Azure folyamatok állítja elő. A webes szerepkör vagy a feldolgozói szerepkör alkalmazás egyéni teljesítmény számlálóadatok kell létrehoznia. Lásd: [teljesítmény számlálótípusok](https://msdn.microsoft.com/library/z573042h.aspx) információt az egyéni teljesítményszámlálóit tárolt adatok típusát. Lásd: [PerformanceCounters minta](http://code.msdn.microsoft.com/azure/) által létrehozott, és beállítja az egyéni teljesítmény számlálóadatok webes szerepkörrel rendelkező példát.

## <a name="store-and-view-performance-counter-data"></a>Tároló és a nézet teljesítményszámláló-adatok
Azure gyorsítótárazza a teljesítményszámláló-adatok más diagnosztikai információkkal. Ezek az adatok érhető el távoli figyelése közben a szerepkörpéldány asztali távelérést használó eszközöket, például a Teljesítményfigyelő megtekintéséhez. Megőrizni az adatokat a szerepkörpéldányt kívül, a diagnosztikai ügynök az adatátvitelt kell az Azure storage. A gyorsítótárazott teljesítmény számlálóadatok méretkorlátját állíthatja be a diagnosztikai ügynök, vagy a diagnosztikai adatok megosztott maximális részeként is beállítható. A puffer mérete beállításával kapcsolatos további információkért lásd: [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) és [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Lásd: [Store és diagnosztikai adatok megtekintése az Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) állít be a diagnosztikai ügynök adatok átvitele a tárfiók áttekintését.

Minden egyes konfigurált teljesítményszámláló-példány a megadott mintavételi díj rögzítése, és a mintaadatokat átkerül a tárfiók egy ütemezett átviteli kérelem vagy egy igény szerinti átviteli kérelmet. Automatikus átvitelek percenkénti gyakorisággal ütemezhető. A diagnosztikai ügynök által átadott teljesítményszámláló-adatokat egy olyan táblázatában, WADPerformanceCountersTable, a tárfiók tárolódik. Ez a táblázat előfordulhat, hogy érhetők el, és megkérdezi a szabványos az Azure storage API-módszerekkel együtt. Lásd: [Microsoft Azure PerformanceCounters minta](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) kérdez le, és a teljesítmény számlálóadatok WADPerformanceCountersTable táblázat megjelenítése egy példát.

> [!NOTE]
> Diagnosztikai ügynök átviteli gyakoriságát és várólista késés, attól a tárfiókban lévő a legutóbbi teljesítmény számlálóadatok több percet is elavult.
>
>

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Engedélyezze a teljesítményszámlálók diagnosztika konfigurációs fájl használata
A következő eljárással engedélyezheti a teljesítményszámlálók az Azure-alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek
Jelen szakaszban feltételezzük, hogy a diagnosztikai figyelő importálni az alkalmazás és a diagnosztika konfigurációs fájl hozzáadni a Visual Studio megoldás (diagnostics.wadcfg SDK 2.4 és az alatt vagy diagnostics.wadcfgx SDK 2.5-ös vagy újabb). Lásd: 1. és 2 [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](cloud-services-dotnet-diagnostics.md)) olvashat.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>1. lépés: Gyűjt, és a teljesítményszámlálók adatainak tárolásához
Miután hozzáadta a diagnosztika fájlt a Visual Studio megoldás, a gyűjtemény és teljesítmény számlálóadatok konfigurálhatja az Azure alkalmazásban. Ez a diagnosztika fájl teljesítményszámlálóinak felvételével történik. Diagnosztikai adatok, beleértve a teljesítményszámlálókat, először összegyűjtött-példányon. Az adatok majd állandó az Azure Table szolgáltatásban WADPerformanceCountersTable táblához, így is kell adja meg a storage-fiók alkalmazásában. Ha a tesztelést az alkalmazás helyileg a számítási emulátorban, is tárolhatja diagnosztikai adatok helyben a Storage Emulator. Diagnosztikai adatok vannak tárolva, mielőtt először lépjen a [Azure-portálon](http://portal.azure.com/) , és hozzon létre egy hagyományos tárolási fiókot. Az ajánlott eljárás hoz keresse meg a tárfiók földrajzi-és ugyanazon a helyen az Azure-alkalmazásában. Ha megtartja az Azure-alkalmazást és a storage-fiók is az azonos földrajzi helyhez, amelyet megfizetése alól külső sávszélességgel kapcsolatos költségek, és késés csökkentésére.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>A diagnosztika fájl teljesítményszámlálók hozzáadása
Használhatja sok számláló áll rendelkezésre. Az alábbi példában több teljesítményszámlálót mutat be, javasolt a webes és feldolgozói szerepkör figyelését.

Nyissa meg a diagnosztika fájlt (diagnostics.wadcfg SDK 2.4 és az alatt vagy diagnostics.wadcfgx az SDK 2.5-ös vagy újabb), és adja hozzá a következő DiagnosticMonitorConfiguration elemhez:

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

A bufferQuotaInMB attribútum, amely meghatározza a maximális elérhető (az Azure naplói, IIS-naplóit, stb.) a gyűjtemény típusú fájl rendszerhez szükséges tárhelyet. Az alapértelmezett érték 0. A kvóta elérése után legrégebbi adatok törlődnek, az új adatok kerülnek. A bufferQuotaInMB tulajdonságok számának összege a OverallQuotaInMB attribútum értékének nagyobbnak kell lennie. További részletes információt a meghatározása, hogy mennyi tárhelyre lesz szükség a diagnosztikai adatok gyűjtésére, című rész a telepítő ÜVEGVATTA [Azure-alkalmazások fejlesztése ajánlott eljárásai hibaelhárítási](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

A scheduledTransferPeriod attribútumot, amely meghatározza a ütemezett adatok továbbítása közötti távolság, kerekíti a legközelebbi perc. A következő példákban érték PT30M (30 perc). Átvitel beállítása kisebb értékre, például 1 perc, kedvezőtlen hatással van az alkalmazás teljesítményére éles környezetben, de megnéz diagnosztikai gyorsan működik a tesztelés során hasznos lehet. Az ütemezett átviteli időszakot kell kicsi győződjön meg arról, hogy diagnosztikai adatok nem írja felül a példányt, de elég nagy ahhoz, hogy nincs hatással az alkalmazás teljesítményét.

A counterSpecifier attribútum határozza meg, hogy a teljesítményszámláló gyűjtéséhez. A sampleRate attribútum határozza meg a sebesség, amellyel a teljesítményszámláló kell mintát venni, ebben az esetben 30 másodperc.

Szeretne gyűjteni a teljesítményszámlálók felvételét, mentse a diagnosztika fájlba. Ezután meg kell adnia a diagnosztikai adatok maradnak meg a storage-fiókhoz.

### <a name="specify-the-storage-account"></a>Adja meg a storage-fiók
Az Azure Storage-fiók diagnosztikai adatainak megőrzése, meg kell adnia a kapcsolati karakterláncot a szolgáltatás konfigurációs (ServiceConfiguration.cscfg) fájlban.

Az Azure SDK 2.5-ös a Tárfiók a diagnostics.wadcfgx fájlban adható meg.

> [!NOTE]
> Ezek az utasítások az Azure SDK 2.4 és alatt csak vonatkoznak. Az Azure SDK 2.5-ös a Tárfiók a diagnostics.wadcfgx fájlban adható meg.
>
>

A kapcsolati karakterláncok beállítása:

1. Nyissa meg a kedvenc szövegszerkesztőjével használatával ServiceConfiguration.Cloud.cscfg fájlt, és állítsa be a tárolási kapcsolati karakterláncát. A *AccountName* és *AccountKey* értékek találhatók, a tárolási fiók Tárelérési kulcsok irányítópulton Azure-portálon.

    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. ServiceConfiguration.Cloud.cscfg fájl.
3. Nyissa meg a ServiceConfiguration.Local.cscfg fájlt, és ellenőrizze, hogy UseDevelopmentStorage értéke TRUE.

    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   Most, hogy a kapcsolati karakterláncok van beállítva, az alkalmazás az alkalmazás központi telepítésekor a tárfiókhoz diagnosztikai adatok megőrzéséhez.
4. Mentse és a projekt buildjének elkészítéséhez, majd az alkalmazás központi telepítése.

## <a name="step-2-optional-create-custom-performance-counters"></a>2. lépés: (Nem kötelező) hozzon létre egyéni teljesítményszámlálói
Az előre definiált teljesítményszámlálók mellett a saját egyéni teljesítményszámlálóit figyelése webes vagy feldolgozói szerepköröket is hozzáadhat. Egyéni teljesítményszámlálóit nyomon követheti és figyelheti az alkalmazás-specifikus viselkedését és létrehozhatók, illetve indítási tevékenységhez, webes vagy feldolgozói szerepkör emelt jogosultsági szintű törölt is használható.

Az Azure diagnostics ügynök teljesítmény számláló konfigurációjának frissítését a .wadcfg fájlból egy percig elindítása után.  Létrehozhat egyéni teljesítményszámlálóit OnStart metódus, és az indítási feladatok végrehajtásához egy percnél tovább tart, ha az egyéni teljesítményszámlálók nem lettek létrehoz, ha az Azure diagnosztikai ügynök megpróbálja betölteni őket.  Ebben a forgatókönyvben láthatja, hogy Azure Diagnostics megfelelően rögzíti az egyéni teljesítményszámlálóit kivételével minden diagnosztikai adatokat.  A probléma megoldásához, hozzon létre egy indítási feladat a teljesítményszámlálókat, vagy helyezze át az egyes az indítási feladat a OnStart metódus alkalmasak a teljesítményszámlálók létrehozása után.

Hajtsa végre a következő lépésekkel hozza létre egy egyszerű egyéni teljesítmény "\MyCustomCounterCategory\MyButton1Counter" számláló neve:

1. Nyissa meg a szolgáltatásdefiníciós fájlban (CSDEF) az alkalmazáshoz.
2. Adja meg a futtatókörnyezet elemben, amely a webrole típusról vagy a WorkerRole elem emelt szintű jogosultságokkal végrehajtására:

    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. Mentse a fájlt.
4. Nyissa meg a diagnosztika fájlt (diagnostics.wadcfg SDK 2.4 és az alatt vagy diagnostics.wadcfgx az SDK 2.5-ös vagy újabb), és adja hozzá a következőket a DiagnosticMonitorConfiguration

    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Mentse a fájlt.
6. Hozzon létre az egyéni teljesítményszámláló-kategória a szerepkör OnStart metódus talál meghívása előtt. OnStart. A következő C# példa egy egyéni kategória hoz létre, ha még nem létezik:

    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);

         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);

         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }

    return base.OnStart();
    }
    ```
7. Frissítse a számlálók az alkalmazáson belül. A következő példa egy egyéni teljesítményszámláló Button1_Click események frissíti:

    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. Mentse a fájlt.  

Egyéni teljesítmény számlálóadatok mostantól az Azure diagnostics figyelő gyűjtenek.

## <a name="step-3-query-performance-counter-data"></a>3. lépés: A teljesítményszámláló-adatok lekérdezése
Ha az alkalmazás telepítve van és fut, a diagnosztikai figyelő megkezdődik teljesítményszámlálók gyűjtése és megőrzése, hogy az adatok az Azure storage. Eszközök, például a Server Explorer használhatja a Visual Studio [Azure Tártallózó](http://azurestorageexplorer.codeplex.com/), vagy [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) megtekintéséhez Cerebrata által a teljesítményszámlálók adatait a WADPerformanceCountersTable tábla. Szoftveresen is lekérheti a Table szolgáltatás használatával [C#](../cosmos-db/table-storage-how-to-use-dotnet.md), [Java](../cosmos-db/table-storage-how-to-use-java.md), [Node.js](../cosmos-db/table-storage-how-to-use-nodejs.md), [Python](../cosmos-db/table-storage-how-to-use-python.md), [Ruby](../cosmos-db/table-storage-how-to-use-ruby.md), vagy [PHP](../cosmos-db/table-storage-how-to-use-php.md).

Az alábbi C# példa a WADPerformanceCountersTable táblázaton egyik alapvető lekérdezését mutatja be, és menti a diagnosztikai adatokat tartalmazó CSV-fájl. Ha a teljesítményszámlálók CSV-fájlba menti, a nyújtó grafikus funkcióinak használata a Microsoft Excel alkalmazást vagy valamilyen más eszköz adatok megjelenítéséhez. Ne adjon hozzá egy hivatkozást, Microsoft.WindowsAzure.Storage.dll, megtalálható az Azure SDK for .NET 2012. októberi és újabb verziók. A szerelvény telepítve van a % Program Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ könyvtárba.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

Entitások leképezése C# objektumok egy származtatott egyéni osztály használatával **TableEntity**. Az alábbi kód meghatároz egy entitásosztályt, amely teljesítményszámlálója jelöli a **WADPerformanceCountersTable** tábla.

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>Következő lépések
[Az Azure Diagnostics további cikkek megtekintése](../azure-diagnostics.md)
