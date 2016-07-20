<properties 
    pageTitle="A Service Bus által felügyelt üzenettovábbítás .NET-oktatóanyaga | Microsoft Azure"
    description="A közvetítőalapú üzenettovábbítás .NET-oktatóanyaga."
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
    ms.date="05/25/2016"
    ms.author="sethm" />

# A Service Bus által felügyelt üzenettovábbítás .NET-oktatóanyaga

Az Azure Service Bus két átfogó üzenetküldési megoldást nyújt: az egyiket egy a felhőben futó központi „továbbító” szolgáltatáson keresztül, amely több különböző átviteli protokollt és webszolgáltatást támogat (többek között SOAP, WS-* és REST). Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A második üzenetküldési megoldás „közvetítőalapú” üzenettovábbítási képességeket tesz lehetővé. Ezek aszinkron vagy leválasztott üzenetküldési funkcióknak tekinthetők, amelyek támogatják a közzétételi-feliratkozási, az időalapú leválasztási és a terheléselosztási forgatókönyveket a Service Bus üzenetküldési infrastruktúrájának használatával. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

A jelen oktatóanyag célja, hogy áttekintést és gyakorlati tapasztalatot nyújtson az üzenetsorokkal kapcsolatban, amelyek a Service Bus által felügyelt üzenettovábbítás alapvető összetevői. Miután a végére ért az oktatóanyag témaköreinek, egy olyan alkalmazással fog rendelkezni, amely feltölt egy üzenetlistát, létrehoz egy üzenetsort és üzeneteket küld ennek az üzenetsornak. Végezetül az alkalmazás megjeleníti az üzenetsorból érkezett üzeneteket, megtisztítja az erőforrásait, majd kilép. A Service Bus Relayen keresztüli üzenetcserét használó alkalmazás létrehozását bemutató kapcsolódó oktatóanyagért lásd a [Service Bus továbbítón keresztüli üzenetcsere oktatóanyagát](service-bus-relay-tutorial.md).

## Bevezetés és előfeltételek

Az üzenetsorok elsőnek be, elsőnek ki (First In, First Out, FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. A FIFO esetében az üzeneteket általában az érzékelők fogadják és dolgozzák fel a sorhoz történő hozzáadásuk időrendje szerint, és minden üzenetet csak egy üzenetfogyasztó fogad és dolgoz fel. Az üzenetsorok használatának egyik legfontosabb előnye, hogy az alkalmazás összetevői*ideiglenesen leválaszthatók*, vagyis az előállítóknak és a fogyasztóknak nem kell egyszerre küldeniük és fogadniuk az üzeneteket, mivel az üzenetsor tartósan tárolja őket. Egy kapcsolódó előny a *terheléselosztás*, amely lehetővé teszi az előállítók és a fogyasztók számára, hogy különböző ütemben küldjenek és fogadjanak üzeneteket.

Az alábbiakban néhány adminisztratív és előfeltételként szolgáló lépést talál, amelyeket az oktatóanyag elkezdése előtt el kell végeznie. Az első egy szolgáltatási névtér létrehozása, valamint egy közös hozzáférésű jogosultságkód (SAS-) kulcs beszerzése. A névtér egy alkalmazáshatárt biztosít a Service Buson keresztül közzétett minden alkalmazáshoz. A SAS-kulcsot a rendszer automatikusan előállítja a szolgáltatásnévtér létrehozásakor. A szolgáltatásnévtér és a SAS-kulcs együttes használata hitelesítő adatokat biztosít a Service Bus számára, amellyel hitelesíti a hozzáférést egy alkalmazáshoz.

### Szolgáltatásnévtér létrehozása és SAS-kulcs beszerzése

1. Szolgáltatásnévtér létrehozásához látogasson el a [klasszikus Azure portálra][]. A bal oldalon kattintson a **Service Bus** elemre, majd kattintson a **Create** (Létrehozás) elemre. Adja meg a névtér nevét, majd kattintson pipára.

1. A portál fő ablakában kattintson az előző lépésben létrehozott névtér nevére.

1. Kattintson a **Configure** (Konfigurálás) elemre.

1. Jegyezze fel a **RootManagerSharedAccessKey** házirendhez társított elsődleges kulcs értékét, vagy másolja a vágólapra. Ezt az értéket az oktatóanyag későbbi részében fogja használni.

A következő lépésben létre kell hoznia egy Visual Studio-projektet, és két olyan segédfüggvényt kell írnia, amely egy vesszővel elválasztott üzenetlistát tölt be egy szigorú típusmegadású [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [Lista](https://msdn.microsoft.com/library/6sh2ey19.aspx) objektumba.

### Visual Studio-projekt létrehozása

1. Nyissa meg a Visual Studiót rendszergazdaként. Ehhez a Start menüben kattintson a jobb gombbal a programra, majd kattintson a **Futtatás rendszergazdaként** parancsra.

1. Hozzon létre új egy új konzolalkalmazás-projektet. Kattintson a **Fájl** menüre, és kattintson az **Új**, majd a **Projekt** elemre. Az **Új projekt** párbeszédpanelen kattintson a **Visual C#** elemre (ha a **Visual C#** nem jelenik meg, keresse meg a **Más nyelvek** területen), kattintson a **Konzolalkalmazás** sablonra, és adja neki a **Microsoft.ServiceBus.Samples** nevet. Használja az alapértelmezett **Helyet**. A projekt létrehozásához kattintson az **OK** gombra.

1. A NuGet-csomagkezelővel adja hozzá a Service Bus-könyvtárakat a projekthez:
    1. A Megoldáskezelőben kattintson a jobb gombbal a **QueueSample** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
    2. A **Manage Nuget Packages** (NuGet-csomagok kezelése) párbeszédpanelen kattintson a **Browse** (Tallózás) lapra, keressen az **Azure Service Bus** kifejezésre, majd kattintson az **Install** (Telepítés) elemre.
<br />
1. A Megoldáskezelőben kattintson duplán a Program.cs fájlra a Visual Studio-szerkesztőben való megnyitásához. Módosítsa a névtér alapértelmezett `QueueSample` nevét a következőre: `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Módosítsa a `using` utasításokat az alábbi kódban látható módon.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Hozzon létre egy Data.csv névvel ellátott szövegfájlt, és másolja a következő, vesszővel tagolt szövegbe.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Mentse és zárja be a Data.csv fájlt, és jegyezze meg, hová mentette.

1. A Megoldáskezelőben kattintson a jobb gombbal a projekt nevére (a jelen példában **QueueSample**), majd kattintson az **Add** (Hozzáadás), majd az **Existing Item** (Meglévő elem) parancsra.

1. Keresse meg a 6. lépésben létrehozott Data.csv fájlt. Kattintson a fájlra, majd kattintson az **Add** (Hozzáadás) gombra. Győződjön meg arról, hogy az **All Files (*.*)** (Minden fájl) lehetőség van kiválasztva a fájltípusok listájában.

### Az üzenetek listáját elemző metódus létrehozása

1. A `Main()` metódus előtti `Program` osztályban deklaráljon két változót: egy **DataTable** típusút, amely a Data.csv fájlban lévő üzenetek listáját tartalmazza. A másik típusa Listaobjektum legyen, amely [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) szigorú típussal rendelkezik. Az utóbbi a közvetítőalapú üzenetek listája, amelyeket az oktatóanyag következő lépései alkalmaznak.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. A `Main()` metóduson kívül határozzon meg egy `ParseCSV()` metódust, amely elemzi a Data.csv fájlban lévő üzenetek listáját, és betölti őket egy [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx) táblába az itt látható módon. A módszer egy **DataTable** objektumot ad vissza.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. A `Main()` metódusban adjon meg egy utasítást a `ParseCSVFile()` metódus meghívására:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### Az üzenetek listáját betöltő metódus létrehozása

1. A `Main()` metóduson kívül határozzon meg egy `GenerateMessages()` metódust, amely fogadja a `ParseCSVFile()` által visszaadott **DataTable** objektumot, és betölti a táblát a közvetítőalapú üzenetek szigorú típusmegadású listájába. A metódus ezután visszaadja a **Lista** objektumot, ahogy az alábbi példában is látható. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. A `Main()` metódusban közvetlenül a `ParseCSVFile()` hívása után adjon hozzá egy utasítást a `GenerateMessages()` metódus meghívásához, amelynek argumentuma a `ParseCSVFile()` visszaadott értéke:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### A felhasználó hitelesítő adatainak beszerzése

1. Először hozzon létre három globális karakterlánc típusú változót az értékek tárolásához. Deklarálja ezeket az értékeket közvetlenül az előző változók deklarációja után; például:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Ezután hozzon létre egy függvényt, amely elfogadja és tárolja a szolgáltatásnévteret és az SAS-kulcsot. Ezt a metódust a `Main()` osztályon kívül adja meg. Példa: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. A `Main()` osztályban közvetlenül a `GenerateMessages()` hívása után adjon hozzá egy utasítást a `CollectUserInput()` metódus meghívásához:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### A megoldás létrehozása

A Visual Studio **Létrehozás** menüjében rákattinthat a **Megoldás fordítása** elemre, vagy a **Ctrl+Shift+B** billentyűkombináció lenyomásával ellenőrizheti az eddigi munkája pontosságát.

## Felügyeleti hitelesítő adatok létrehozása

Ebben a lépésben meghatározza a felügyeleti műveleteket, amelyeket az alkalmazás engedélyezéséhez szükséges közös hozzáférésű jogosultságkód (SAS) hitelesítő adatok létrehozásához fog használni.

1. Az átláthatóság érdekében a jelen oktatóanyag egy külön metódusba helyezi az összes üzenetsor-műveletet. Hozzon létre egy aszinkron `Main()` metódust a `Program` osztályon belül a `Queue()` metódus után. Példa:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. A következő lépés egy SAS-hitelesítő adat létrehozása a [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) objektum használatával. A létrehozási metódushoz a `CollectUserInput()` metódusból származó SAS-kulcs neve és értéke szükséges. Adja hozzá a következő kódot a `Queue()` metódushoz:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Hozzon létre egy új névtér-felügyeleti objektumot egy URI-val, amely argumentumaiként az előző lépésben beszerzett névtér nevét és a felügyeleti hitelesítő adatokat tartalmazza. Adja hozzá ezt a kódot közvetlenül az előző lépésben létrehozott kód után. Győződjön meg arról, hogy a `<yourNamespace>` helyett a szolgáltatásnévtér nevét adja meg:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### Példa

Mostanra a kód az alábbihoz hasonlít:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Üzenetek küldése az üzenetsorba

Ebben a lépésben létrehoz egy üzenetsort, majd elküldi a közvetítőalapú üzenetek listájában lévő üzeneteket ennek az üzenetsornak.

### Üzenetsor létrehozása és üzenetek küldése az üzenetsorba

1. Először hozzon létre egy üzenetsort. Adja neki például a `myQueue` nevet, majd deklarálja közvetlenül az előző lépés során a `Queue()` metódusban hozzáadott felügyeleti műveletek után:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. A `Queue()` metódusban hozzon létre egy üzenetkezelési gyártóobjektumot, amelynek argumentuma egy újonnan létrehozott Service Bus URI. Vegye fel a következő kódot közvetlenül az előző lépésben létrehozott felügyeleti műveletek után. Győződjön meg arról, hogy a `<yourNamespace>` helyett a szolgáltatásnévtér nevét adja meg:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Ezután hozza létre az üzenetsor-objektumot a [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) osztály használatával. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Ezután vegyen fel egy kódot, amely végighalad az előzőleg létrehozott közvetítőalapú üzeneteken, és mindegyiket egy üzenetsorba küldi. Vegye fel a következő kódot közvetlenül az előző lépésben létrehozott `CreateQueueClient()` utasítás után:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## Üzenet fogadása az üzenetsorból

Ebben a lépésben beszerzi az üzenetek listáját az előző lépésben létrehozott üzenetsorból.

### Fogadó létrehozása és üzenetek fogadása az üzenetsorból

A `Queue()` metódusban végezze el az iterációt az üzenetsoron, fogadja az üzeneteket a [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx) metódus használatával, majd nyomtasson minden üzenetet a konzolba. Vegye fel a következő kódot közvetlenül az előző lépésben felvett kód után:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Vegye figyelembe, hogy a `Thread.Sleep` csak az üzenetfeldolgozás szimulálására szolgál, és egy valódi üzenetküldési alkalmazásban valószínűleg nem lenne rá szükség.

### A Queue metódus befejezése és az erőforrások törlése

Az üzenetgyár és az üzenetsor erőforrásainak törléséhez vegye fel az alábbi kódot közvetlenül az előző kód után:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### A Queue metódus meghívása

Az utolsó lépés egy utasítás hozzáadása, amely meghívja a `Queue()` metódust a következőből: `Main()`. Vegye fel az alábbi kiemelt kódsort a Main() végéhez:
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### Példa

Az alábbi kód a teljes **QueueSample** alkalmazást tartalmazza.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## A QueueSample alkalmazás létrehozása és futtatása

Most, hogy elvégezte az előző lépéseket, létrehozhatja és futtathatja a **QueueSample** alkalmazást.

### A QueueSample alkalmazás létrehozása

A Visual Studio **Létrehozás** menüjében kattintson a **Megoldás fordítása** elemre, vagy nyomja le a **Ctrl+Shift+B** billentyűkombinációt. Ha hibákat észlel, az előző lépés végén bemutatott teljes példa alapján ellenőrizze, hogy a kód megfelelő-e.

## További lépések

Ez az oktatóanyag bemutatta, hogyan hozhat létre egy Service Bus-ügyfélalkalmazást és szolgáltatást a Service Bus közvetítőalapú üzenettovábbítási képességeivel. A Service Bus [Relayen keresztüli üzenetcserét](service-bus-messaging-overview.md#Relayed-messaging) alkalmazó oktatóanyagért lásd a [Service Bus továbbítón keresztüli üzenetcsere oktatóanyagát](service-bus-relay-tutorial.md).

A [Service Busról](https://azure.microsoft.com/services/service-bus/) a következő témakörökben talál további információt.

- [A Service Bus üzenetkezelésének áttekintése](service-bus-messaging-overview.md)
- [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-architektúra](service-bus-architecture.md)

[klasszikus Azure portálra]: http://manage.windowsazure.com



<!--HONumber=Jun16_HO2-->


