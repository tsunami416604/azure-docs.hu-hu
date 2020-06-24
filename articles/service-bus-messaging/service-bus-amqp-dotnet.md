---
title: Azure Service Bus .NET-tel és AMQP 1,0 | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Service Bus egy .NET-alkalmazásból a AMQP (Advanced Messaging Queueing Protocol) használatával.
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
ms.date: 06/10/2020
ms.author: aschhab
ms.openlocfilehash: 6555a1718acb0574640e7b7d5d4d47d84b8a72d0
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711051"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Service Bus használata a .NET-ről a AMQP 1,0 használatával

A AMQP 1,0-támogatás a Service Bus csomag 2,1-es vagy újabb verziójában érhető el. A [NuGet][NuGet]-ből a Service Bus BITS letöltésével biztosíthatja a legújabb verziót.

## <a name="configure-net-applications-to-use-amqp-10"></a>.NET-alkalmazások konfigurálása a AMQP 1,0 használatára

Alapértelmezés szerint a Service Bus .NET ügyféloldali kódtár a AMQP protokoll használatával kommunikál a Service Bus szolgáltatással. A AMQP explicit módon is megadhatja az átvitel típusaként, ahogy azt a következő szakaszban is látható. 

A jelenlegi kiadásban néhány API-funkció nem támogatott a AMQP használata esetén. Ezek a nem támogatott funkciók a [viselkedési eltérések](#behavioral-differences)szakaszban vannak felsorolva. A speciális konfigurációs beállítások némelyike más jelentéssel is rendelkezik a AMQP használatakor.

### <a name="configuration-using-appconfig"></a>Konfigurálás App.config használatával

Ajánlott az alkalmazások számára az App.config konfigurációs fájl használata a beállítások tárolásához. Service Bus alkalmazások esetén a Service Bus-kapcsolódási karakterláncot a App.config használatával lehet tárolni. App.config fájl például a következő:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

A `Microsoft.ServiceBus.ConnectionString` beállítás értéke a Service Bus kapcsolódási karakterlánc, amely a Service Bushoz való kapcsolódás konfigurálására szolgál. A formátum a következő:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Ha `namespace` `SAS key` Service Bus névteret hoz létre, a [Azure Portal][Azure portal] Honnan szerezhetők be. További információ: [Service Bus névtér létrehozása a Azure Portal használatával][Create a Service Bus namespace using the Azure portal].

A AMQP használatakor fűzze hozzá a kapcsolódási karakterláncot a paranccsal `;TransportType=Amqp` . Ez a jelölés arra utasítja az ügyféloldali kódtárat, hogy a AMQP 1,0 használatával létesítsen kapcsolatát Service Bus.

## <a name="message-serialization"></a>Üzenet szerializálása

Az alapértelmezett protokoll használatakor a .NET-ügyfél könyvtárának alapértelmezett szerializálási viselkedése a [dataContractSerializer][DataContractSerializer] típus használata az [BrokeredMessage][BrokeredMessage] -példány szerializálásához az ügyféloldali kódtár és a Service Bus szolgáltatás közötti átvitelhez. A AMQP átviteli mód használatakor az ügyféloldali kódtár a AMQP típusú rendszer használatával szerializálja a [közvetített üzenetet][BrokeredMessage] egy AMQP-üzenetbe. Ez a szerializálás lehetővé teszi az üzenet fogadását és értelmezését egy olyan fogadó alkalmazástól, amely esetleg egy másik platformon fut, például egy Java-alkalmazás, amely a JMS API-t használja a Service Bus eléréséhez.

[BrokeredMessage][BrokeredMessage] -példány létrehozásakor megadhat egy .net-objektumot paraméterként a konstruktor számára, amely az üzenet törzsének szolgál. A AMQP primitív típusokhoz rendelhető objektumok esetében a törzs AMQP-adattípusokra van szerializálva. Ha az objektum nem képezhető le közvetlenül AMQP primitív típusra; Ez az alkalmazás által definiált egyéni típus, majd az objektum szerializálása a [dataContractSerializer][DataContractSerializer]használatával történik, és a szerializált bájtok egy AMQP-adatüzenetben lesznek elküldve.

A non-.NET-ügyfelekkel való együttműködés megkönnyítése érdekében csak olyan .NET-típusokat használjon, amelyek közvetlenül az üzenet törzsének AMQP-típusaiba lesznek szerializálva. A következő táblázat részletezi ezeket a típusokat és a AMQP típusrendszer megfelelő leképezését.

| .NET-szövegtörzs objektumtípus | Leképezett AMQP típusa | AMQP törzs szakaszának típusa |
| --- | --- | --- |
| logikai |logikai |AMQP érték |
| byte |ubyte |AMQP érték |
| ushort |ushort |AMQP érték |
| uint |uint |AMQP érték |
| ulong |ulong |AMQP érték |
| sbyte érték |byte |AMQP érték |
| rövid |rövid |AMQP érték |
| int |int |AMQP érték |
| hosszú |hosszú |AMQP érték |
| lebegőpontos |lebegőpontos |AMQP érték |
| double |double |AMQP érték |
| tizedes tört |decimal128 |AMQP érték |
| char |char |AMQP érték |
| DateTime |időbélyeg |AMQP érték |
| Guid |uuid |AMQP érték |
| bájt [] |binary |AMQP érték |
| sztring |sztring |AMQP érték |
| System. Collections. IList |lista |AMQP érték: a gyűjteményben szereplő elemek csak a táblázatban definiált elemekkel rendelkezhetnek. |
| System. Array |tömb |AMQP érték: a gyűjteményben szereplő elemek csak a táblázatban definiált elemekkel rendelkezhetnek. |
| System. Collections. IDictionary |map |AMQP érték: a gyűjteményben szereplő elemek csak a táblázatban definiált elemekkel rendelkezhetnek. Megjegyzés: csak karakterlánc-kulcsok támogatottak. |
| URI |Leírt karakterlánc (lásd az alábbi táblázatot) |AMQP érték |
| DateTimeOffset |Leírt hosszú (lásd a következő táblázatot) |AMQP érték |
| időtartam |Leírt hosszú (lásd a következőt) |AMQP érték |
| Adatfolyam |binary |AMQP-adatértékek (lehet, hogy több). Az adatterületek tartalmazzák a stream objektumból beolvasott nyers bájtokat. |
| Egyéb objektum |binary |AMQP-adatértékek (lehet, hogy több). Az alkalmazás által biztosított DataContractSerializer vagy szerializáló objektumot használó objektum szerializált bináris fájlját tartalmazza. |

| .NET-típus | Leképezett AMQP leírt típusa | Jegyzetek |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |URI. AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset. UtcTicks |
| időtartam |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan. Ticks |

## <a name="behavioral-differences"></a>Viselkedési különbségek

A AMQP használata során a Service Bus .NET API viselkedése némileg különbözik, az alapértelmezett protokollhoz képest:

* A [OperationTimeout][OperationTimeout] tulajdonságot a rendszer figyelmen kívül hagyja.
* `MessageReceiver.Receive(TimeSpan.Zero)`implementálva `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` .
* Az üzenetek zárolási jogkivonatokkal történő elvégzése csak az üzeneteket tartalmazó üzenet-fogadók által végezhető el.

## <a name="control-amqp-protocol-settings"></a>AMQP-protokoll beállításainak szabályozása

A [.NET API](/dotnet/api/) -k számos beállítást tesznek elérhetővé az AMQP protokoll működésének szabályozásához:

* **[MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: a hivatkozásra alkalmazott kezdeti jóváírást vezérli. Az alapértelmezett érték a 0.
* **[MessagingFactorySettings. AmqpTransportSettings. MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: szabályozza a maximális AMQP, amely a csatlakozáskor az egyeztetés során elérhető. Az alapértelmezett érték 65 536 bájt.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: Ha az átvitelek kötegeltek, ez az érték határozza meg a leküldések küldésének maximális késleltetését. Alapértelmezés szerint a küldők/fogadók öröklik. Az egyéni küldő/fogadó felülbírálhatja az alapértelmezett értéket, ami 20 ezredmásodperc.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: azt határozza meg, hogy a AMQP kapcsolatok létesítettek-e TLS-kapcsolaton keresztül. Az alapértelmezett érték a **true (igaz**).

## <a name="next-steps"></a>További lépések

Készen áll a tanulásra? Látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0 protokoll – útmutató]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azureservicebus-4.0.0
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0 protokoll – útmutató]: service-bus-amqp-protocol-guide.md

