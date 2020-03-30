---
title: Azure Service Bus .NET és AMQP 1.0 rendszerrel | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja az Azure Service Bus egy .NET alkalmazás amqp (Advanced Messaging Queuing protocol) használatával.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8157efac5ff1fc135659a84b4f4825ff36307480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297652"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>A .NET service bus használata az AMQP 1.0-s rendszerrel

Az AMQP 1.0 támogatása a Service Bus 2.1-es vagy újabb verziójában érhető el. A Service Bus bitek letöltésével biztosíthatja, hogy a legújabb verzióval rendelkezzen a [NuGet-ből.][NuGet]

## <a name="configure-net-applications-to-use-amqp-10"></a>A .NET alkalmazások konfigurálása az AMQP 1.0 használatára

Alapértelmezés szerint a Service Bus .NET ügyfélkódtár dedikált SOAP-alapú protokoll használatával kommunikál a Service Bus szolgáltatással. Az AMQP 1.0 használatához az alapértelmezett protokoll helyett explicit konfigurációszükséges a Service Bus kapcsolati karakterláncán, ahogy azt a következő szakasz ismerteti. Más, mint ez a változás, az alkalmazás kódja változatlan marad, ha az AMQP 1.0.Other than this change, application code remains unchanged when using AMQP 1.0.

A jelenlegi kiadásban van néhány API-funkciók, amelyek nem támogatottak az AMQP használatakor. Ezek a nem támogatott szolgáltatások a [Viselkedési különbségek](#behavioral-differences)című szakaszban találhatók. Néhány speciális konfigurációs beállításnak más jelentése is van az AMQP használatakor.

### <a name="configuration-using-appconfig"></a>Konfiguráció az App.config használatával

Célszerű, ha az alkalmazások az App.config konfigurációs fájlt használják a beállítások tárolására. A Service Bus-alkalmazások esetében az App.config segítségével tárolhatja a Service Bus kapcsolati karakterláncát. Az App.config példa fájlja a következő:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

A beállítás `Microsoft.ServiceBus.ConnectionString` értéke a Service Bus kapcsolati karakterlánc, amely a Service Bus-hoz való csatlakozás konfigurálására szolgál. A formátum a következő:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Hol `namespace` `SAS key` és az [Azure Portalon,][Azure portal] amikor létrehoz egy Service Bus-névtér. További információ: [Service Bus-névtér létrehozása az Azure Portalhasználatával][Create a Service Bus namespace using the Azure portal]című témakörben talál.

Az AMQP használatakor fűzze `;TransportType=Amqp`hozzá a kapcsolati karakterláncot a hoz. Ez a jelölés arra utasítja az ügyfélkönyvtárat, hogy az AMQP 1.0 használatával létesítse a kapcsolatot a Service Bus szolgáltatással.

## <a name="message-serialization"></a>Üzenet szerializálása

Az alapértelmezett protokoll használatakor a .NET ügyfélkódtár alapértelmezett szerializálási viselkedése a [DataContractSerializer][DataContractSerializer] típus használata az ügyfélkódtár és a Service Bus szolgáltatás közötti [átvitelbrokeredMessage-példányának][BrokeredMessage] szerializálásához. Az AMQP átviteli mód használatakor az ügyféltár az AMQP típusú rendszert használja a [közvetített üzenet][BrokeredMessage] amqp-üzenetbe történő szerializálásához. Ez a szerializálás lehetővé teszi, hogy az üzenetet egy másik platformon potenciálisan futó fogadó alkalmazás fogadja és értelmezze, például egy Java-alkalmazás, amely a JMS API-t használja a Service Bus eléréséhez.

Amikor egy [BrokeredMessage-példányt][BrokeredMessage] hoz létre, paraméterként adhat meg egy .NET-objektumot a konstruktornak, amely az üzenet törzseként szolgál. Az AMQP primitív típusokhoz leképezhető objektumok esetében a törzs amqp-adattípusokba van szerializálva. Ha az objektum nem képezhető le közvetlenül AMQP primitív típusba; ez az alkalmazás által meghatározott egyéni típus, majd az objektum szerializálása a [DataContractSerializer][DataContractSerializer]használatával történik, és a szerializált bájtok aMQP-adatüzenetben kerülnek elküldésre.

A non-.NET ügyfelekkel való együttműködés megkönnyítése érdekében csak olyan .NET típusokat használjon, amelyek közvetlenül az AMQP-típusokba szerializálhatók az üzenet törzséhez. Az alábbi táblázat részletezi ezeket a típusokat és az AMQP-típusú rendszer megfelelő leképezését.

| .NET szövegtörzsobjektum-típus | Leképezett AMQP-típus | AMQP törzsszakasz ának típusa |
| --- | --- | --- |
| Bool |logikai |AMQP-érték |
| bájt |ubyte |AMQP-érték |
| ushort |ushort |AMQP-érték |
| uint |uint |AMQP-érték |
| ulong |ulong |AMQP-érték |
| sbyte |bájt |AMQP-érték |
| Rövid |Rövid |AMQP-érték |
| int |int |AMQP-érték |
| long |long |AMQP-érték |
| lebegőpontos |lebegőpontos |AMQP-érték |
| double |double |AMQP-érték |
| tizedes tört |decimális 128 |AMQP-érték |
| Char |Char |AMQP-érték |
| DateTime |időbélyeg |AMQP-érték |
| Guid |uuid |AMQP-érték |
| bájt[] |binary |AMQP-érték |
| sztring |sztring |AMQP-érték |
| System.Collections.IList |lista |AMQP-érték: a gyűjteményben lévő elemek csak a táblában definiált elemek lehetnek. |
| System.Tömb |tömb |AMQP-érték: a gyűjteményben lévő elemek csak a táblában definiált elemek lehetnek. |
| System.Collections.IDictionary |térkép |AMQP-érték: a gyűjteményben lévő elemek csak a táblában definiált elemek lehetnek. Megjegyzés: csak a Karakterlánc-kulcsok támogatottak. |
| Uri |Leírt karakterlánc(lásd az alábbi táblázatot) |AMQP-érték |
| DateTimeOffset (Dátumidő-eltolás) |Hosszú(lásd az alábbi táblázatot) |AMQP-érték |
| időtartam |Hosszú (lásd a következőket) |AMQP-érték |
| Stream |binary |AMQP-adatok (több is lehet). Az Adatszakaszok tartalmazzák a Stream objektumból beolvasott nyers bájtokat. |
| Egyéb objektum |binary |AMQP-adatok (több is lehet). A DataContractSerializert vagy az alkalmazás által biztosított szerializáló t használó objektum szerializált bináris fájlját tartalmazza. |

| .NET típus | Leképezett AMQP leírt típus | Megjegyzések |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.Absoluteuri |
| DateTimeOffset (Dátumidő-eltolás) |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| időtartam |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Viselkedési különbségek

Az AMQP használatakor a Service Bus .NET API viselkedése az alapértelmezett protokollhoz képest kisebb különbségeket mutat:

* Az [OperationTimeout][OperationTimeout] tulajdonság figyelmen kívül lesz hagyva.
* `MessageReceiver.Receive(TimeSpan.Zero)`végrehajtása a `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`következőképpen történik: .
* Az üzenetek zárolási jogkivonatok általi kitöltését csak azok az üzenetfogadók végezhetik el, amelyek eredetileg megkapták az üzeneteket.

## <a name="control-amqp-protocol-settings"></a>Az AMQP protokoll beállításainak szabályozása

A [.NET API-k](/dotnet/api/) számos beállítást adnak ki az AMQP protokoll viselkedésének szabályozásához:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: Szabályozza a hivatkozásra alkalmazott kezdeti jóváírást. Az alapértelmezett érték a 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: Szabályozza a maximális AMQP keretméretet, amelyet az egyeztetés során kínálnak a kapcsolat megnyitásának idején. Az alapértelmezett érték 65 536 bájt.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Ha az átvitelkötegelhető, ez az érték határozza meg a leváltási helyek maximális késleltetését. Alapértelmezés szerint a feladók/fogadók öröklik. Az egyes feladók/fogadók felülbírálhatják az alapértelmezett értéket, amely 20 ezredmásodperc.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: Azt szabályozza, hogy az AMQP-kapcsolatok TLS-kapcsolaton keresztül jönnek-e létre. Az alapértelmezett érték **igaz**.

## <a name="next-steps"></a>További lépések

Készen áll a tanulásra? Látogasson el az alábbi linkekre:

* [Service Bus AMQP – áttekintés]
* [AMQP 1.0 protokoll – útmutató]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP – áttekintés]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md

