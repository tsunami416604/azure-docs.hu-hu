---
title: Az Azure Service Bus .NET és AMQP 1.0 |} Microsoft Docs
description: A .NET-Azure Service Bus használata AMQP
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 28b8d7a71f01d8633d020b99fbe6bc5c16f272b4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>A Service Bus a .NET-használata AMQP 1.0-s

AMQP 1.0-támogatás a Service Bus csomag 2.1-es vagy újabb verziója érhető el. Biztosíthatja, hogy úgy, hogy a Service Bus bits, az letölti a legújabb verzió van [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>.NET-alkalmazások az AMQP 1.0 konfigurálása

Alapértelmezés szerint a Service Bus .NET ügyféloldali kódtár a Service Bus szolgáltatás egy dedikált SOAP-alapú protokoll segítségével kommunikál. Az AMQP 1.0 használata helyett az alapértelmezett protokoll úgy kell explicit konfigurálni a Service Bus kapcsolati karakterlánc a következő szakaszban leírtak szerint. Ez a változás nem alkalmazáskód változatlan marad AMQP 1.0 használata esetén.

A jelenlegi kiadásban van néhány AMQP használata esetén nem támogatott API-funkciókat. Nem támogatott szolgáltatások a szakaszban később szereplő [nem támogatott funkciók, korlátozások és viselkedési különbségek](#unsupported-features-restrictions-and-behavioral-differences). Néhány speciális konfigurációs beállítás is eltérő jelentéssel rendelkezhetnek AMQP használatakor.

### <a name="configuration-using-appconfig"></a>Konfigurációs App.config használatával

Ajánlott az alkalmazások az App.config konfigurációs fájlt használja a beállítások tárolásához. A Service Bus-alkalmazást a App.config segítségével tárolja a Service Bus kapcsolati karakterlánc. Egy példa App.config fájl a következőképpen történik:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Értékét a `Microsoft.ServiceBus.ConnectionString` beállítás a Service Bus kapcsolati karakterlánc, amely a Service Bus kapcsolat konfigurálására szolgál. A formátum a következőképpen történik:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Ha `namespace` és `SAS key` kapott a [Azure-portálon] [ Azure portal] létrehozásakor egy Szolgáltatásbusz-névtér. További információkért lásd: [az Azure portál használatával a Service Bus-névtér létrehozása][Create a Service Bus namespace using the Azure portal].

AMQP használata esetén a kapcsolati karakterlánc hozzáfűzése `;TransportType=Amqp`. Ez a notation arra utasítja az ügyféloldali kódtár a kapcsolat a Service Bus AMQP 1.0 használatával.

## <a name="message-serialization"></a>Üzenet szerializálási

Az alapértelmezett protokoll használata esetén a .NET ügyféloldali kódtár szerializálási alapértelmezés lesz-e használni a [DataContractSerializer] [ DataContractSerializer] szerializálható típust egy [BrokeredMessage] [ BrokeredMessage] az ügyféloldali kódtár és a Service Bus szolgáltatás közötti-példányt. Az AMQP átviteli mód használatával, ha az ügyféloldali kódtár álló AMQP típus rendszert használ az [közvetítőalapú üzenet] [ BrokeredMessage] egy AMQP üzenetbe. A szerializálás lehetővé teszi, hogy az üzenet fogadják és értelmezni a fogadó alkalmazás, potenciálisan futó különböző platform, például egy Java-alkalmazás, amely a JMS API Service Bus eléréséhez.

Ha, hozhat létre egy [BrokeredMessage] [ BrokeredMessage] példány, megadhat egy .NET-objektum az üzenet törzsét szolgálhat a konstruktor paraméterként. Az AMQP egyszerű típusokhoz rendelhetők objektumokat a szervezet szerializálni AMQP adattípusokat. Ha az objektum leképezése nem végezhető el közvetlenül a következő egy AMQP primitív típusra; Ez azt jelenti, hogy az alkalmazás által meghatározott olyan egyéni típusra, akkor az objektum szerializált használatával a [DataContractSerializer][DataContractSerializer], és a szerializált bájtok az AMQP-adatok üzenet küldése.

Csak a közvetlenül az AMQP típusokat az üzenet törzsét akkor szerializálható .NET típusok segítségével a .NET ügyfelek együttműködés elősegítése érdekében. Az alábbi táblázat részletezi ezeket a típusokat és a hozzárendelés az AMQP típus rendszerre.

| .NET törzs objektum típusa | Csatlakoztatott AMQP típusa | AMQP törzs szakasz típusa |
| --- | --- | --- |
| logikai érték |logikai |AMQP érték |
| bájt |ubyte |AMQP érték |
| ushort |ushort |AMQP érték |
| uint |uint |AMQP érték |
| ulong |ulong |AMQP érték |
| sbyte |bájt |AMQP érték |
| rövid |rövid |AMQP érték |
| int |int |AMQP érték |
| hosszú |hosszú |AMQP érték |
| Lebegőpontos |Lebegőpontos |AMQP érték |
| double |double |AMQP érték |
| Decimális |decimal128 |AMQP érték |
| Karakter |Karakter |AMQP érték |
| DateTime |időbélyeg |AMQP érték |
| GUID |UUID |AMQP érték |
| Byte] |Bináris |AMQP érték |
| karakterlánc |karakterlánc |AMQP érték |
| System.Collections.IList illesztőfelületet |lista |AMQP érték: a gyűjteményben lévő elemek csak lehet azokat ebben a táblázatban definiált. |
| System.Array |tömb |AMQP érték: a gyűjteményben lévő elemek csak lehet azokat ebben a táblázatban definiált. |
| System.Collections.IDictionary |térkép |AMQP érték: a gyűjteményben lévő elemek csak lehet azokat ebben a táblázatban definiált. Megjegyzés: csak a karakterlánc-kulcsok használata támogatott. |
| URI |Karakterlánc leírt (lásd az alábbi táblázatot) |AMQP érték |
| DateTimeOffset |Hosszú leírt (lásd az alábbi táblázatot) |AMQP érték |
| A TimeSpan |Hosszú leírt (lásd a következő) |AMQP érték |
| Adatfolyam |Bináris |AMQP adatok (több is lehet). Az adatok szakaszok tartalmazzák a nyers bájt olvasásakor az adatfolyam-objektum. |
| Másik objektum |Bináris |AMQP adatok (több is lehet). Az objektum, amely a DataContractSerializer vagy az alkalmazás által biztosított szerializálót a szerializált bináris fájlt tartalmaz. |

| .NET-típusa | Csatlakoztatott AMQP leírt típusa | Megjegyzések |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| A TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Viselkedés különbségek

Néhány kisebb különbségek vannak a Service Bus .NET API működésében AMQP képest az alapértelmezett protokoll használata esetén:

* A [OperationTimeout] [ OperationTimeout] tulajdonság a rendszer figyelmen kívül hagyja.
* `MessageReceiver.Receive(TimeSpan.Zero)` valósul meg `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Üzenetek befejezése által zárolási jogkivonatok csak végezhető el az üzenetet fogadó számára eredetileg az üzeneteket.

## <a name="control-amqp-protocol-settings"></a>Vezérlő AMQP protokoll beállításait

A [.NET API-k](/dotnet/api/) teszi közzé az AMQP protokoll működését számos beállítás:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: a kezdeti jóváírás hivatkozás alkalmazott szabályozza. Az alapértelmezett érték 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: vezérlők, a maximális AMQP keret méretét érhető el kapcsolat, az egyeztetés során nyissa meg az időt. Az alapértelmezett érték a 65 536 bájt.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: batchable átvitel esetén ez az érték határozza meg, a maximális késleltetési dispositions küldéséhez. Alapértelmezés szerint feladók/fogadók örökli. Egyes feladó/fogadó felülbírálhatja az alapértelmezett, amelyet 20 ezredmásodperc.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: meghatározza, hogy SSL-kapcsolaton keresztül létesít AMQP-kapcsolatokat. Az alapértelmezett érték **igaz**.

## <a name="next-steps"></a>További lépések

Készen áll a további? Látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md

