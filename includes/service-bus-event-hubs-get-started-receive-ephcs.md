## Üzenetek fogadása az EventProcessorHost szolgáltatással

Az [EventProcessorHost][] egy .NET-osztály, amely leegyszerűsíti az események fogadását az Event Hubs szolgáltatásból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott Event Hubs eseményközpontokból. Az [EventProcessorHost][] szolgáltatással feloszthatja az eseményeket több fogadóra, akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az [EventProcessorHost][] egyetlen fogadóhoz. A [horizontálisan felskálázott eseményfeldolgozási][] minta megmutatja, hogyan használható a [EventProcessorHost][] több fogadóval.

Az [EventProcessorHost][] használatához egy [Azure Storage-fiók][] szükséges:

1. Jelentkezzen be a [klasszikus Azure portálra][], és kattintson a **NEW** (ÚJ) lehetőségre a képernyő alján.

2. Kattintson a **Data Services** (Adatszolgáltatások), a **Storage** (Tárolás), majd a **Quick Create** (Gyors létrehozás) lehetőségre, és adja meg a tárfiók nevét. Válassza ki a kívánt régiót, majd kattintson a **Create Storage Account** (Tárfiók létrehozása) lehetőségre.

    ![][11]

3. Kattintson az imént létrehozott tárfiókra, majd a **Manage Access Keys** (Elérési kulcsok kezelése) lehetőségre:

    ![][12]

    Másolja le az elsődleges elérési kulcsot – erre később lesz szükség az oktatóprogramban.

4. Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Receiver** (Fogadó) nevet.

    ![][14]

5. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése megoldáshoz) parancsra.

6. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Ügyeljen arra, hogy a projekt neve (**Receiver**) meg legyen adva a **Version(s)** (Verzió(k)) mezőben. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.

    ![][13]

    A Visual Studio letölti és telepíti az [Azure Service Bus Event Hub - EventProcessorHost NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) minden függőségével együtt, és hozzáad egy rá mutató hivatkozást is.

7. Kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, kattintson az **Add** (Hozzáadás) lehetőségre, majd a **Class** (Osztály) elemre. Nevezze el az új osztályt **SimpleEventProcessor** névre, és kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához.

    ![][15]

8. Adja hozzá a következő utasításokat a SimpleEventProcessor.cs fájl elejéhez:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Ezután helyettesítse be a következő kódot az osztály törzseként:

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Ezt az osztályt fogja meghívni az **EventProcessorHost** az eseményközpontból fogadott események feldolgozásához. Vegye figyelembe, hogy a `SimpleEventProcessor` osztály stoppert használ az ellenőrzőpont-módszer hívásához az **EventProcessorHost** környezetben. Ez biztosítja, hogy ha a fogadó újraindul, nem vész el öt percnél több feldolgozási munka.

9. A **Program** osztályban adja hozzá a következő `using` utasítást a fájl elejéhez:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Ezután cserélje le a `Main` metódust a `Program` osztályban a következő kódra, behelyettesítve az eseményközpont nevét, a korábban mentett névtérszintű kapcsolati karakterláncot, valamint a tárfiókot és a kulcsot, amelyeket a korábbi szakaszokban lemásolt. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] Ez az oktatóprogram az [EventProcessorHost][] egyetlen példányát használja. Az átviteli sebesség növelése érdekében ajánlott az [EventProcessorHost][] több példányának futtatása, ahogy a [horizontálisan felskálázott eseményfeldolgozási][] minta mutatja. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az [EventProcessorHost][] példányoknak. További információ ezekről a témákról: [Event Hubs – áttekintés][] és [Event Hubs programozási útmutató][].

<!-- Links -->
[Event Hubs – áttekintés]: event-hubs-overview.md
[Event Hubs programozási útmutató]: event-hubs-programming-guide.md
[horizontálisan felskálázott eseményfeldolgozási]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure Storage-fiók]: ../storage/storage-create-storage-account-classic-portal.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[klasszikus Azure portálra]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp1.png
[15]: ./media/service-bus-event-hubs-getstarted/create-receiver-csharp2.png




<!--HONumber=sep16_HO1-->


