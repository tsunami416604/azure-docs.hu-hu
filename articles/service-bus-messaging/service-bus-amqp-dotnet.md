---
title: Az Azure Service Bus .NET-tel és az AMQP 1.0-s |} A Microsoft Docs
description: Az AMQP használatával a .NET használatával az Azure Service Bus használatával
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 332bcb13-e287-4715-99ee-3d7d97396487
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2018
ms.author: spelluru
ms.openlocfilehash: ad789b7a65fd12abb2a6e92c7c8896677de80cec
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702238"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>A Service Bus, a .NET használata az AMQP 1.0 használatával

Az AMQP 1.0-támogatás a Service Bus csomag 2.1-es vagy újabb verzió érhető el. Biztosíthatja úgy, hogy a Service Bus bits, az letölti a legújabb verzióval rendelkezik [NuGet][NuGet].

## <a name="configure-net-applications-to-use-amqp-10"></a>Az AMQP 1.0-s használata .NET-alkalmazások konfigurálása

Alapértelmezés szerint a Service Bus .NET ügyféloldali kódtár a Service Bus szolgáltatás egy dedikált SOAP-alapú protokoll használatával kommunikál. Az AMQP 1.0-s használata helyett az alapértelmezett protokoll a Service Bus kapcsolati karakterlánc az explicit konfigurációt igényel, a következő szakaszban leírtak szerint. Ez a változás nem alkalmazáskód változatlan marad az AMQP 1.0-s használata esetén.

A jelenlegi kiadásban van néhány API-funkciók az AMQP használata esetén nem támogatott. Nem támogatott szolgáltatások a szakaszban felsorolt [viselkedésbeli különbségeket](#behavioral-differences). Néhány speciális konfigurációs beállítás is eltérő jelentéssel rendelkeznek az AMQP használata esetén.

### <a name="configuration-using-appconfig"></a>Konfiguráció App.config használatával

Ajánlott alkalmazások számára az App.config konfigurációs fájlt használja a beállítások tárolásához. A Service Bus-alkalmazások a Service Bus kapcsolati karakterlánc tárolására App.config használhatja. Egy példa az App.config fájlra a következőképpen történik:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Értékét a `Microsoft.ServiceBus.ConnectionString` beállítás a Service Bus kapcsolati karakterlánc, amellyel a Service Bus-kapcsolat konfigurálása. A formátum a következőképpen történik:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Ahol `namespace` és `SAS key` kérhetők le a [az Azure portal] [ Azure portal] Service Bus-névtér létrehozásakor. További információkért lásd: [létrehozása az Azure portal használatával a Service Bus-névtér][Create a Service Bus namespace using the Azure portal].

Az AMQP használata esetén fűzze hozzá a kapcsolati karakterláncot `;TransportType=Amqp`. Ezen jelölés arra utasítja az ügyféloldali kódtár, hogy a kapcsolatot a Service Bus AMQP 1.0-val.

## <a name="message-serialization"></a>Üzenet-szerializáció

Az alapértelmezett protokoll használata esetén az alapértelmezett szerializálási viselkedését a .NET ügyféloldali kódtár használatára-e a [DataContractSerializer] [ DataContractSerializer] szerializálható típust egy [BrokeredMessage] [ BrokeredMessage] példány esetében az ügyféloldali kódtár és a Service Bus szolgáltatás között. Az AMQP közlekedési mód használatakor az ügyféloldali kódtár terjesztésről AMQP típus rendszert használ a [közvetítőalapú üzenet] [ BrokeredMessage] egy AMQP üzenetbe. A szerializálás lehetővé teszi, hogy az üzenet és egy fogadó alkalmazás, amely potenciálisan fut egy másik platformon, például Java-alkalmazás elérésére a Service Bus a JMS API-t használó értelmezi.

Amikor hozhat létre egy [BrokeredMessage] [ BrokeredMessage] példány, megadhat egy .NET-objektumokat az üzenet törzsét, mely a konstruktor paramétereként. Az AMQP-típusokban leképezhető objektumok a szervezet szerializálni AMQP adattípusokat. Ha az objektum nem lehet közvetlenül hozzá van rendelve, egy AMQP primitív típus; azt jelenti, egy egyéni írja be az alkalmazás által meghatározott, akkor az objektum szerializált használatával a [DataContractSerializer][DataContractSerializer], és a szerializált bájtok küldése történik az adatok AMQP üzenet.

Csak a közvetlenül az üzenet törzse AMQP típusainak szerializálására .NET Tulajdonságtípusokat használatával nem .NET-ügyfelek együttműködés elősegítése érdekében. Az alábbi táblázat részletezi ezeket a feldolgozástípusokat és a megfelelő hozzárendelésének az AMQP-típus rendszerre.

| .NET-törzs objektumtípus | A csatlakoztatott AMQP típusa | Az AMQP törzs szakasz típusa |
| --- | --- | --- |
| Logikai |logikai |Az AMQP-érték |
| bájt |ubyte |Az AMQP-érték |
| ushort |ushort |Az AMQP-érték |
| uint |uint |Az AMQP-érték |
| ulong |ulong |Az AMQP-érték |
| sbyte |bájt |Az AMQP-érték |
| rövid |rövid |Az AMQP-érték |
| int |int |Az AMQP-érték |
| hossz |hossz |Az AMQP-érték |
| lebegőpontos |lebegőpontos |Az AMQP-érték |
| double |double |Az AMQP-érték |
| tizedes tört |decimal128 |Az AMQP-érték |
| CHAR |CHAR |Az AMQP-érték |
| DateTime |időbélyeg |Az AMQP-érték |
| GUID |UUID |Az AMQP-érték |
| byte] |Bináris |Az AMQP-érték |
| sztring |sztring |Az AMQP-érték |
| System.Collections.IList |lista |AMQP érték: a gyűjteményben szereplő elemek csak lehet, amelyek a táblában. |
| System.Array |tömb |AMQP érték: a gyűjteményben szereplő elemek csak lehet, amelyek a táblában. |
| System.Collections.IDictionary |Térkép |AMQP érték: a gyűjteményben szereplő elemek csak lehet, amelyek a táblában. Megjegyzés: a csak karakterlánc-kulcsok használata támogatott. |
| URI |Karakterlánc leírt (lásd az alábbi táblázatban) |Az AMQP-érték |
| DateTimeOffset |Mennyi ideig leírt (lásd az alábbi táblázatban) |Az AMQP-érték |
| Időtartam |Mennyi ideig leírt (lásd az alábbi) |Az AMQP-érték |
| Stream |Bináris |Az AMQP-adatok (több is lehet). Az adatok szakaszok a nyers bájt, olvassa el a Stream-objektumból. |
| Egyéb objektum |Bináris |Az AMQP-adatok (több is lehet). Az objektum, amely a DataContractSerializer vagy az alkalmazás által biztosított serializátor használ a szerializált bináris tartalmazza. |

| Typ .NET | A csatlakoztatott AMQP leírt típusa | Megjegyzések |
| --- | --- | --- |
| URI |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| Időtartam |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Viselkedésbeli különbségeket

Van néhány kisebb eltérés, a Service Bus .NET API viselkedését az AMQP és képest az alapértelmezett protokoll használata esetén:

* A [így időtúllépés történt] [ OperationTimeout] tulajdonság figyelmen kívül hagyja.
* `MessageReceiver.Receive(TimeSpan.Zero)` van megvalósítva `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Üzenetek befejezése zárolási jogkivonatok szerint csak az üzenet hozzáadásuk kezdetben fogadott üzenetek teheti meg.

## <a name="control-amqp-protocol-settings"></a>AMQP protokoll beállításai

A [.NET API-k](/dotnet/api/) tegye elérhetővé az AMQP protokollt viselkedését vezérlő több beállítások:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: meghatározza a kezdeti kredit egy hivatkozást a alkalmazni. Az alapértelmezett érték a 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: AMQP keret maximális méretét érhető el a kapcsolatot az egyeztetés során vezérlők nyissa meg az idő. Az alapértelmezett érték a 65 536 bájt.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: batchable adatátvitel esetén ez az érték határozza meg, a maximális késleltetés dispositions küldéséhez. Alapértelmezés szerint öröklik a küldők és fogadók. Az egyes küldő/fogadó felülbírálhatja az alapértelmezett beállítás, amely 20 ezredmásodperc.
* **[MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity?view=azureservicebus-4.0.0#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: szabályozza, hogy az AMQP-kapcsolatok létrehozása történik SSL-kapcsolaton keresztül. Az alapértelmezett érték **igaz**.

## <a name="next-steps"></a>További lépések

Készen áll további? Látogasson el az alábbi hivatkozásokat:

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

