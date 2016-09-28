<properties
 pageTitle="Az Azure IoT Hub áttekintése | Microsoft Azure"
 description="Az Azure IoT Hub szolgáltatás áttekintése: mi az IoT Hub, az eszközkapcsolat, az eszközök internetes hálózatának kommunikációs mintái és a szolgáltatás által támogatott kommunikációs minták"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/25/2016"
 ms.author="dobett"/>


# Mi az Azure IoT Hub?

Üdvözli az Azure IoT Hub. Ez a cikk áttekintést nyújt az Azure IoT Hubról, és leírja, hogy miért érdemes ezt a szolgáltatást használnia az Eszközök internetes hálózata- (IoT-) megoldások megvalósításához.

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz között, valamint megoldást biztosít a háttérrendszer kialakításához. Azure IoT Hub:

- Megbízható nagy kapacitású üzenetkezelést kínál az eszközök és a felhő között mindkét irányban.
- Az eszközönkénti biztonsági hitelesítő adatok és hozzáférés-vezérlés segítségével lehetővé teszi a biztonságos kommunikációt.
- Az eszközkapcsolat és az eszközidentitás-kezelési események széles körű megfigyelését nyújtja.
- Tartalmazza a legnépszerűbb nyelvek és platformok eszközkönyvtárait.

[Az IoT Hub és az Event Hubs összehasonlítása][lnk-compare] leírja a két szolgáltatás közötti fő különbségeket, és kiemeli az IoT Hub IoT-megoldásokban való használatának előnyeit.

![Azure IoT Hub IoT-megoldások felhőátjárójaként][img-architecture]

> [AZURE.NOTE] Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture] (Microsoft Azure IoT-referenciaarchitektúra)[lnk-refarch].

## Az IoT-eszközkapcsolatok kihívásai

Az IoT Hub és az eszközkódtárak segítségével megbízható és biztonságos módon csatlakoztathatja az eszközöket a megoldás háttérrendszeréhez. IoT-eszközök:

- Általában beágyazott, emberi beavatkozást nem igénylő rendszerek.
- Távoli helyeken is lehetnek, ahol a fizikai hozzáférés drága lenne.
- Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el.
- Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
- A hálózati kapcsolat időszakos, lassú vagy drága lehet.
- Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.
- Számos népszerű hardver- és szoftverplatform használatával létrehozhatók.

A fenti követelmények mellett minden egyes IoT-megoldásnak megfelelő méretezhetőséget, biztonságot és megbízhatóságot kell biztosítania. Az ebből következő kapcsolati követelmények megvalósítása rendkívül nehéz és időigényes az olyan hagyományos technológiák segítségével, mint például a webes tárolók vagy üzenetkezelő közvetítők.

## Miért érdemes az Azure IoT Hubot használni?

Az Azure IoT Hub a következő módokon kezeli az eszközkapcsolattal kapcsolatos kihívásokat:

-   **Eszközönkénti hitelesítés és biztonságos kapcsolat**. Mindegyik eszközhöz saját [biztonsági kulcsot][lnk-devguide-security] építhet k az IoT Hub-kapcsolat biztosításához. Az [IoT Hub-identitásjegyzék ][lnk-devguide-identityregistry] egy megoldásban tárolja az eszközidentitásokat és a kulcsokat. A megoldáshátterek engedélyezési vagy letiltási listákhoz adhatják az egyes eszközöket, ami biztosítja az eszközhozzáférés teljes vezérlését.

-   **Eszközkapcsolatok műveleteinek megfigyelése**. Részletes műveleti naplókat fogadhat az eszközidentitás-kezelési műveletekről és az eszközök kapcsolati eseményeiről. Ezzel a megfigyelési képességgel az IoT-megoldás azonosíthatja a kapcsolati hibákat, például a helytelen hitelesítő adatokkal kapcsolódni próbáló, túl gyakran üzenő vagy a felhőből az eszközre irányuló összes üzenetet elutasító eszközöket.

-   **Széles körű eszközkönyvtár-készlet**. Az [Azure IoT eszközoldali SDK-k][lnk-device-sdks] többféle nyelven és platformon elérhetők és támogatottak, például C nyelven sok Linux-disztribúcióhoz, Windowshoz és valós idejű operációs rendszerekhez. Az Azure IoT eszközoldali SDK-k a felügyelt nyelveket is támogatják, például a C#, Java és JavaScript nyelveket.

-   **IoT-protokollok és bővíthetőség**. Ha a megoldása nem tudja használni az eszközkódtárakat, az IoT Hub olyan nyilvános protokollt tesz közzé, amellyel az eszközök natív módon használhatják az MQTT v3.1.1, HTTP 1.1 vagy AMQP 1.0 protokollokat. A következőképpen ki is bővítheti az IoT Hubot, hogy támogassa az egyéni protokollokat:

    - Helyszíni átjáró létrehozásával az [Azure IoT átjáró SDK-val][lnk-gateway-sdk], amely az egyéni protokollt az IoT Hub által használt három protokoll egyikévé alakítja. 
    - Az [Azure IoT-protokollátjáró][protocol-gateway] testreszabásával, amely a felhőn futó nyílt forráskódú összetevő.

-   **Méretezés**. Az Azure IoT Hub több millió egyszerre csatlakoztatott eszközre és másodpercenként több millió eseményre méretezhető.

Ezek az előnyök sok kommunikációs minta esetén általánosak. Az IoT Hub jelenleg a következő specifikus kommunikációs minták megvalósítását teszi lehetővé:

-   **Eseményalapú, eszközről a felhőbe irányuló feldolgozás.** Az IoT Hub megbízhatóan fogadhat másodpercenként több millió eseményt az eszközeiről. Ezután a működő elérési úton dolgozhatja fel ezeket egy eseményfeldolgozási alrendszerrel. A nem használt elérési úton emellett tárolhatja is az eseményeket elemzés céljából. Az IoT Hub legfeljebb hét napig őrzi meg az eseményadatokat, hogy megbízható feldolgozást nyújtson, és feldolgozza a csúcsterheléseket.

-   **Megbízható, felhőből az eszközre irányuló üzenetküldés (vagy *parancsok*).** A megoldás háttérrendszerre IoT Hubbal küldhet üzeneteket legalább egyszeri kézbesítési garanciával az egyes eszközökre. Mindegyik üzenet önálló élettartam-beállítással rendelkezik, és a háttérrendszer a kézbesítésről és a lejáratról is kérhet nyugtát. Ezek a nyugták biztosítják a felhőből az eszközre irányuló üzenetek életciklusának teljes átláthatóságát. Ezután olyan üzleti logikát valósíthat meg, amely eszközökön futó műveleteket tartalmaz.

-   **Fájlokat és gyorsítótárazott érzékelői adatokat tölthet fel a felhőbe.** Az eszközei fájlokat tölthetnek fel az Azure Storage-ba az IoT Hub által felügyelt SAS URI-kkal. Az IoT Hub értesítéseket hozhat létre, amikor fájlok érkeznek a felhőbe, hogy a háttérrendszer feldolgozhassa őket.

## Átjárók

Az IoT-megoldások átjárója általában a felhőben telepített [protokollátjáró][lnk-gateway] vagy helyben, az eszközökkel telepített [helyszíni átjáró][lnk-field-gateway]. A protokollátjárók protokollfordítást végeznek, például MQTT-ről AMQP-re. A helyszíni átjárók elemzéseket futtathatnak a peremhálózaton, a késést csökkentő időérzékeny döntéseket hozhatnak, eszközfelügyeleti szolgáltatásokat nyújthatnak, biztonsági és adatvédelmi kényszereket tartathatnak be és protokollfordítást is végezhetnek. Mindkét átjárótípus közvetítőként működik az eszközök és az IoT Hub között.

A helyszíni átjárók különböznek az egyszerű forgalomirányító eszközöktől (például a hálózati címfordítási eszköztől vagy tűzfaltól), mert általában aktív szerepet játszanak a hozzáférés kezelésében és az információáramlásban a megoldáson belül.

Egy megoldás mindkét protokollt és helyszíni átjárót tartalmazhatja.

## Hogyan működik az IoT Hub?

Az Azure IoT Hub a [szolgáltatással támogatott kommunikációs][lnk-service-assisted-pattern] mintát valósítja meg az eszközök és a megoldásháttér közötti kommunikáció közvetítése érdekében. A szolgáltatással segített kommunikáció célja, hogy megbízható, kétirányú kommunikációs útvonalat hozzon létre egy vezérlőrendszer (például az IoT Hub) és olyan speciális célú eszközök között, amelyek nem megbízható fizikai helyen vannak üzembe helyezve. A minta a következő alapelveket valósítja meg:

- A biztonság minden más képességgel szemben elsőbbséget élvez.
- Az eszközök nem fogadnak el kéretlen hálózati információkat. Az eszközök csak kifelé irányuló kapcsolatokat és útvonalakat létesíthetnek. Ahhoz, hogy egy eszköz parancsot fogadjon a háttérrendszerből, az eszköznek rendszeresen kell kapcsolatot kezdeményeznie a függőben lévő feldolgozandó parancsok ellenőrzéséhez.
- Az eszközök csak jól ismert társított szolgáltatásokhoz csatlakozhatnak vagy csak ezekkel létesíthetnek útvonalat, például az IoT Hubbal.
- Az eszköz és szolgáltatás vagy eszköz és átjáró közötti kommunikációs útvonal védelmét az alkalmazásprotokoll rétege biztosítja.
- A rendszerszintű engedélyezés és hitelesítés eszközönkénti identitásokon alapul. Ezek a hozzáférési hitelesítő adatokat és engedélyeket szinte azonnal visszavonhatóvá teszik.
- Az áramellátással és csatlakozási okok miatt szórványosan kapcsolódó eszközök kétirányú kommunikációját megőrzési parancsok és eszközértesítések segítik elő, amíg az eszköz csatlakozik a lekérésükhöz. Az IoT Hub eszközspecifikus sorokat tart fent az általa küldött parancsokhoz.
- Az alkalmazás hasznos adatai külön védelmet élveznek egy adott szolgáltatás felé az átjárók keresztül zajló védett átvitel érdekében.

A mobilágazat nagy mennyiségben használta a szolgáltatással támogatott kommunikációs mintát olyan leküldéses értesítési szolgáltatások megvalósításához, mint például [a Windows leküldéses értesítéseket kezelő szolgáltatásai][lnk-wns], a [Google Cloud Messaging][lnk-google-messaging] és az [Apple Push Notification szolgáltatása][lnk-apple-push].

## Következő lépések

További információk arról, hogyan felügyelheti, konfigurálhatja és frissítheti az eszközeit távolról, az Azure IoT Hub által biztosított szabványalapú IoT-eszközfelügyelet segítségével: [Az Azure IoT Hub-eszközfelügyelet áttekintése][lnk-device-management].

Ha az ügyfélalkalmazásokat az eszközök hardveres platformjainak és operációs rendszereinek széles választékára szeretné telepíteni, használhatja az IoT eszköz SDK-kat. Az IoT eszköz SDK-k olyan kódtárakat tartalmaznak, amelyek elősegítik a telemetria küldését az IoT Hubra, valamint a felhőből az eszközre irányuló parancsok fogadását. Amikor az SDK-kat használja, különféle hálózati protokollok közül választhat az IoT Hubbal való kommunikációhoz. További tudnivalókért lásd: [információ az eszköz SDK-król][lnk-device-sdks].

Bevetés a kódírásba és a példák futtatásába: [Bevezetés az IoT Hub használatába][lnk-get-started] című oktatóanyag.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Szolgáltatással támogatott kommunikáció, a blogbejegyzés írója: Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md



<!--HONumber=Sep16_HO4-->


