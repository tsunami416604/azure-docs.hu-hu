---
title: "Az Azure IoT Hub áttekintése | Microsoft Docs"
description: "Az Azure IoT Hub szolgáltatás áttekintése: mi az IoT Hub, az eszközkapcsolat, az eszközök internetes hálózatának kommunikációs mintái, átjárók és a szolgáltatás által támogatott kommunikációs minták"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b00c89183ff0d4e7df49d29834508643e68246bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-the-azure-iot-hub-service"></a>Az Azure IoT Hub szolgáltatás áttekintése

Üdvözli az Azure IoT Hub. Ez a cikk áttekintést nyújt az Azure IoT Hubról, és leírja, hogy miért érdemes ezt a szolgáltatást használnia az Eszközök internetes hálózata- (IoT-) megoldások megvalósításához. Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz között, valamint megoldást biztosít a háttérrendszer kialakításához. Azure IoT Hub:

* Több üzenetkezelési lehetőséget kínál az eszközök és a felhő között mindkét irányban. A lehetőségek közé tartozik az egyirányú üzenetküldés, a fájlátvitel és a kérés-válasz módszerek.
* Beépített deklaratív útválasztást biztosít az üzenetek számára más Azure-szolgáltatások felé.
* Egy lekérdezhető tárolót biztosít az eszközök metaadataihoz és szinkronizáltsági állapotával kapcsolatos információkhoz.
* Az eszközönkénti biztonsági hitelesítő adatok vagy X.509 tanúsítványok segítségével lehetővé teszi a biztonságos kommunikációt és hozzáférés-vezérlést.
* Az eszközkapcsolat és az eszközidentitás-kezelési események széles körű megfigyelését nyújtja.
* Tartalmazza a legnépszerűbb nyelvek és platformok eszközkönyvtárait.

[Az IoT Hub és az Event Hubs összehasonlítása][lnk-compare] leírja a két szolgáltatás közötti fő különbségeket, és kiemeli az IoT Hub IoT-megoldásokban való használatának előnyeit.

Az [eszközök internetes hálózatának biztonsági alapjait][lnk-security-ground-up] ismertető oldalon olvashat arról, hogy az Azure és az IoT Hub hogyan támogatják az IoT-megoldások biztonságát.

![Azure IoT Hub IoT-megoldások felhőátjárójaként][img-architecture]

> [!NOTE]
> Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Microsoft Azure IoT-referenciaarchitektúra).

## <a name="iot-device-connectivity-challenges"></a>Az IoT-eszközkapcsolatok kihívásai

Az IoT Hub és az eszközkódtárak segítségével megbízható és biztonságos módon csatlakoztathatja az eszközöket a megoldás háttérrendszeréhez. IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek.
* Távoli helyeken is lehetnek, ahol a fizikai hozzáférés drága lenne.
* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el.
* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
* A hálózati kapcsolat időszakos, lassú vagy drága lehet.
* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.
* Számos népszerű hardver- és szoftverplatform használatával létrehozhatók.

A fenti követelmények mellett minden egyes IoT-megoldásnak megfelelő méretezhetőséget, biztonságot és megbízhatóságot kell biztosítania. Az ebből következő kapcsolati követelmények megvalósítása rendkívül nehéz és időigényes az olyan hagyományos technológiák segítségével, mint például a webes tárolók vagy üzenetkezelő közvetítők.

## <a name="why-use-azure-iot-hub"></a>Miért érdemes az Azure IoT Hubot használni?

Az Azure IoT Hub az [eszköz-felhő][lnk-d2c-guidance] és [felhő-eszköz][lnk-c2d-guidance] kommunikációs lehetőségek gazdag tárházát kínálja. Emellett az Azure IoT Hub az eszközök megbízható és biztonságos csatlakoztatásával kapcsolatos kihívásokat a következő módszerekkel kezeli:

* **Ikereszközök**. Az [ikereszközök][lnk-twins] használatával tárolhatja, szinkronizálhatja és lekérdezheti az eszközök metaadatait és állapotinformációit. Az ikereszközök JSON-dokumentumok, amelyek az eszközök állapotinformációit, például a metaadatokat, konfigurációkat és állapotokat tárolják. Az IoT Hub a rácsatlakoztatott minden egyes eszközhöz fenntart egy ikereszközt.

* **Eszközönkénti hitelesítés és biztonságos kapcsolat**. Mindegyik eszközhöz saját [biztonsági kulcsot][lnk-devguide-security] építhet ki az IoT Hub-kapcsolat biztosításához. Az [IoT Hub-identitásjegyzék][lnk-devguide-identityregistry] egy megoldásban tárolja az eszközidentitásokat és a kulcsokat. A megoldáshátterek engedélyezési vagy letiltási listákhoz adhatják az egyes eszközöket, ami biztosítja az eszközhozzáférés teljes vezérlését.

* **Megadja az Azure-szolgáltatások felé irányuló eszköz–felhő üzenetek útválasztását a deklaratív szabályok alapján**. Az IoT Hub lehetővé teszi az üzenetek útvonalának szabályokkal történő definiálását, amelyekkel meghatározható, hogy a Hub hová küldi az eszköz–felhő üzeneteket. A szabályok nem igénylik bármilyen kód megírását, és helyettesíthetik a feldolgozás utáni egyéni üzenetkezelőket.

* **IoT Hub-események integrálása az üzleti alkalmazásokba**. Az IoT Hub integrálható az Azure Event Griddel. Ezzel az integrációval konfigurálhat más Azure-szolgáltatásokat vagy külső alkalmazásokat az IoT Hub-események figyelésére. Az Azure Event Grid lehetővé teszi, hogy gyorsan, megbízhatóan, biztonságosan és skálázható módon reagáljon a kritikus eseményekre.

* **Eszközkapcsolatok műveleteinek megfigyelése**. Részletes műveleti naplókat fogadhat az eszközidentitás-kezelési műveletekről és az eszközök kapcsolati eseményeiről. Ezzel a monitorozási képességgel az IoT-megoldás azonosíthatja a kapcsolati hibákat. Ezekkel a naplókkal azonosíthatja a helytelen hitelesítő adatokat megadó, túl gyakran üzenő vagy a felhőből az eszközre irányuló összes üzenetet elutasító eszközöket.

* **Széles körű eszközkönyvtár-készlet**. Az [Azure IoT eszközoldali SDK-k][lnk-device-sdks] többféle nyelven és platformon elérhetők és támogatottak, például C nyelven sok Linux-disztribúcióhoz, Windowshoz és valós idejű operációs rendszerekhez. Az Azure IoT eszközoldali SDK-k a felügyelt nyelveket is támogatják, például a C#, Java és JavaScript nyelveket.

* **IoT-protokollok és bővíthetőség**. Ha a megoldása nem tudja használni az eszközkódtárakat, az IoT Hub olyan nyilvános protokollt tesz közzé, amellyel az eszközök natív módon használhatják az MQTT v3.1.1, HTTPS 1.1 vagy AMQP 1.0 protokollokat. A következő módszerekkel ki is bővítheti az IoT Hubot, hogy támogassa az egyéni protokollokat:

  * Helyszíni átjáró [Azure IoT Edge][lnk-iot-edge] használatával történő létrehozásával, amely az egyéni protokollt az IoT Hub által használt egyik protokollá alakítja.
  * A felhőben futó, nyílt forráskódú [Azure IoT-protokollátjáró][protocol-gateway] összetevő testreszabásával.

* **Méretezés**. Az Azure IoT Hub több millió egyszerre csatlakoztatott eszközre és másodpercenként több millió eseményre méretezhető.

* **Eszköz kiépítése**. Az [IoT Hub eszközkiépítési szolgáltatás](https://docs.microsoft.com/azure/iot-dps/) az IoT Hub segítő szolgáltatása, amely felhasználói beavatkozás nélkül teszi lehetővé a megfelelő IoT Hubon való igény szerinti üzembe helyezést, és amely által az ügyfelek eszközök millióit építhetik ki biztonságos és skálázható módon.

## <a name="gateways"></a>Átjárók

Az IoT-megoldások átjárója általában egy, a felhőben telepített [protokollátjáró][lnk-iotedge], vagy egy helyben, az eszközökkel telepített [helyszíni átjáró][lnk-field-gateway].

A _protokollátjárók_ protokollfordítást végeznek, például MQTT-ről AMQP-re.

A _helyszíni átjárók_ a következőkre használhatók:

* elemzés futtatása a peremhálózaton;
* sürgős döntések a késés csökkentése érdekében;
* eszközkezelési szolgáltatások biztosítása;
* biztonsági és adatvédelmi korlátozások érvényesítése;
* protokollfordítás végrehajtása.

Mindkét átjárótípus közvetítőként működik az eszközök és az IoT Hub között.

A helyszíni átjárók különböznek az egyszerű forgalomirányító eszközöktől (például a hálózati címfordítási eszköztől vagy tűzfaltól), mert általában aktív szerepet játszanak a hozzáférés kezelésében és az információáramlásban a megoldáson belül.

Egy megoldás mindkét protokollt és helyszíni átjárót tartalmazhatja.

## <a name="how-does-iot-hub-work"></a>Hogyan működik az IoT Hub?

Az Azure IoT Hub a [szolgáltatással támogatott kommunikációs][lnk-service-assisted-pattern] mintát valósítja meg az eszközök és a megoldásháttér közötti kommunikáció közvetítése érdekében. A minta célja, hogy megbízható, kétirányú kommunikációs útvonalat hozzon létre egy vezérlőrendszer (például az IoT Hub) és olyan speciális célú eszközök között, amelyek fizikai védelme nem feltétlenül biztosított. A minta a következő alapelveket valósítja meg:

* A biztonság minden más képességgel szemben elsőbbséget élvez.

* Az eszközök nem fogadnak el kéretlen hálózati információkat. Az eszközök csak kifelé irányuló kapcsolatokat és útvonalakat létesíthetnek. Ahhoz, hogy egy eszköz parancsot fogadjon a megoldás háttérrendszeréből, az eszköznek rendszeresen kell kapcsolatot kezdeményeznie a függőben lévő feldolgozandó parancsok ellenőrzéséhez.

* Az eszközök csak jól ismert társított szolgáltatásokhoz csatlakozhatnak vagy csak ezekkel létesíthetnek útvonalat, például az IoT Hubbal.

* Az eszköz és szolgáltatás vagy az átjáró közötti kommunikációs útvonal védelmét az alkalmazásprotokoll rétege biztosítja.

* A rendszerszintű engedélyezés és hitelesítés eszközönkénti identitásokon alapul. Ezek a hozzáférési hitelesítő adatokat és engedélyeket szinte azonnal visszavonhatóvá teszik.

* Az áramellátási vagy csatlakozási okok miatt szórványosan kapcsolódó eszközök esetében a kétirányú kommunikáció úgy működik, hogy a parancsokat és eszközértesítéseket megőrzi, amíg az eszköz nem csatlakozik, hogy lekérje őket. Az IoT Hub eszközspecifikus sorokat tart fent az általa küldött parancsokhoz.

* Az alkalmazás hasznos adatai külön védelmet élveznek egy adott szolgáltatás felé az átjárók keresztül zajló védett átvitel érdekében.

A mobilágazat a szolgáltatással támogatott kommunikációs mintát olyan leküldéses értesítési szolgáltatások megvalósításához használta, mint például [a Windows leküldéses értesítéseket kezelő szolgáltatásai][lnk-wns], a [Google Cloud Messaging][lnk-google-messaging] és az [Apple Push Notification Service][lnk-apple-push].

Az IoT Hub használata az ExpressRoute nyilvános társhálózati útvonalain is támogatott.

## <a name="next-steps"></a>További lépések

Bevezetés a kódírásba és a példák futtatásába: [Bevezetés az IoT Hub használatába][lnk-get-started] című oktatóanyag.

További információk arról, hogyan küldhetők üzenetek egy eszközről és fogadhatók az IoT Hub felől, valamint hogyan konfigurálhatók az üzenetútvonalak: [Üzenetek küldése és fogadása az IoT Hub használatával][lnk-send-messages].

További információk arról, hogyan felügyelheti, konfigurálhatja és frissítheti az eszközeit távolról, az IoT Hub által biztosított szabványalapú eszközfelügyelet segítségével: [Az IoT Hub eszközkezelésének áttekintése][lnk-device-management].

Ha az ügyfélalkalmazásokat az eszközök hardveres platformjainak és operációs rendszereinek széles választékára szeretné telepíteni, használhatja az Azure IoT eszközoldali SDK-kat. Az eszközoldali SDK-k olyan kódtárakat tartalmaznak, amelyek elősegítik a telemetria küldését az IoT Hubra, valamint a felhőből az eszközre irányuló üzenetek fogadását. Amikor az eszközoldali SDK-kat használja, különféle hálózati protokollok közül választhat az IoT Hubbal való kommunikációhoz. További tudnivalókért tekintse meg [az eszközoldali SDK-k ismertető oldalát][lnk-device-sdks].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service Assisted Communication (Szolgáltatással támogatott kommunikáció) Clemens Vasters blogbejegyzése"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-iotedge]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-iot-edge]: https://docs.microsoft.com/azure/iot-edge/
[lnk-send-messages]: iot-hub-devguide-messaging.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-security-ground-up]: iot-hub-security-ground-up.md
