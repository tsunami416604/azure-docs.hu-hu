---
title: A csatlakoztatott gyáron alapuló megoldás bemutatója – Azure | Microsoft Docs
description: Az Azure IoT csatlakoztatottgyár-alapú megoldásgyorsítónak és architektúrájának leírása.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: be9bde90684b46adef509532eb7b0cc236c5efb1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180602"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>A csatlakoztatottgyár-alapú megoldásgyorsító bemutatója

A csatlakoztatottgyár-alapú [megoldásgyorsítóval][lnk-preconfigured-solutions] egy teljes körű ipari megoldást implementálhat, amely:

* Szimulált gyártósorokon OPC UA-kiszolgálókat futtató szimulált ipari eszközökhöz és valódi OPC UA-kiszolgálóeszközökhöz is csatlakozik. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.
* Megjeleníti ezen eszközök és gyártósorok működési KPI- és OEE-értékeit.
* Bemutatja, hogyan lehet kommunikálni az OPC UA-kiszolgálórendszerekkel felhőalapú alkalmazások használatával.
* Lehetővé teszi, hogy összekapcsolja saját OPC UA-kiszolgálóeszközeit.
* Lehetővé teszi az OPC UA-kiszolgálóadatok tallózását és módosítását.
* Integrálható az Azure Time Series Insights (TSI) szolgáltatással, amely testreszabott nézeteket biztosít az OPC UA-kiszolgálók adatairól.

A megoldást kiindulási pontként használhatja a saját implementációjához, és a saját üzleti igényeinek megfelelően [testreszabhatja][lnk-customize] azt.

Ebben a cikkben bemutatjuk a csatlakoztatottgyár-alapú megoldás néhány fontos elemét, hogy jobban megismerhesse a szolgáltatás működését. A cikk azt is leírja, hogyan haladnak át az adatok a megoldáson. Ezeknek az ismereteknek a birtokában:

* Elháríthatja a megoldásban felmerülő hibákat.
* Megtervezheti, hogy miképpen érdemes testre szabni a megoldást úgy, hogy az megfeleljen egyedi igényeinek.
* Kialakíthatja saját, Azure-szolgáltatásokat használó IoT-megoldását.

További információ: [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Logikai architektúra

A következő diagram a megoldásgyorsító logikai összetevőit vázolja fel:

![Csatlakoztatott gyár logikai architektúrája][connected-factory-logical]

## <a name="communication-patterns"></a>Kommunikációs minták

A megoldás az [OPC UA Pub/Sub specifikációt](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) használja az OPC UA telemetriai adatok JSON-formátumban való küldésére az IoT Hub részére. A megoldás az [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge-modult használja erre a célra.

A megoldás webalkalmazásba integrált OPC UA-ügyféllel is rendelkezik, amely képes kapcsolatot létesíteni a helyszíni OPC UA-kiszolgálókkal. Az ügyfél [fordított proxyt](https://wikipedia.org/wiki/Reverse_proxy) használ, és az IoT Hub segítségével anélkül képes létrehozni a kapcsolatot, hogy nyitott portokra lenne szüksége a helyszíni tűzfalon. Ezt a kommunikációs mintát a szolgáltatással segített kommunikáció nevezzük. A megoldás az [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge-modult használja erre a célra.


## <a name="simulation"></a>Szimuláció

A gyártósorok szimulált állomásokból és szimulált gyártási végrehajtó rendszerekből (MES) állnak. A szimulált eszközök és az OPC-publikációs modul az OPC Foundation által kiadott [OPC UA .NET standardon][lnk-OPC-UA-NET-Standard] alapul.

Az OPC Proxy és az OPC Publisher modulként van megvalósítva az [Azure IoT Edge][lnk-Azure-IoT-Gateway] alapján. Mindegyik szimulált gyártósorhoz az átjáró csatlakoztatva van.

Minden szimulációs összetevő Azure Linux virtuális gépen futtatott Docker-tárolókban fut. A szimuláció úgy van konfigurálva, hogy alapértelmezés szerint nyolc szimulált gyártósort futtasson.

## <a name="simulated-production-line"></a>Szimulált gyártósor

A gyártósorok alkatrészeket gyártanak. Különböző állomásokból állnak: összeszerelő állomásból, tesztelő állomásból és csomagoló állomásból.

A szimuláció futtatja és frissíti az adatokat az OPC UA-csomópontokon keresztül teszik elérhetővé. Minden szimulált gyártósori állomást a MES vezényel az OPC UA-n keresztül.

## <a name="simulated-manufacturing-execution-system"></a>Szimulált gyártási végrehajtó rendszer

A MES az OPC UA-n keresztül figyeli a gyártósor egyes állomásait azok állapotváltozásainak észlelése érdekében. Ez a meghívja az OPC UA az állomások vezérlése érdekében, és továbbítja egyik állomásról a következőre befejezéséig.

## <a name="gateway-opc-publisher-module"></a>Átjáró OPC kiadói modul

Az OPC kiadói modul az állomás OPC UA-kiszolgálóihoz csatlakozik és feliratkozik a kiadandó OPC-csomópontokra. A modul:

1. A csomópont adatokat JSON formátumba alakítja át.
1. A JSON titkosítja.
1. A JSON az IoT Hub küldi OPC UA Pub/Sub üzenetekként.

Az OPC kiadói modulnak csak kimenő https-portra (443) van szüksége, és képes együttműködni a meglévő vállalati infrastruktúrával.

## <a name="gateway-opc-proxy-module"></a>Átjáró OPC-proxymodul

Az átjáró OPC UA-proxymodul bináris OPC UA-parancs- és vezérlési üzeneteket továbbít, és csak kimenő https-portot (443) igényel. Képes a meglévő vállalati infrastruktúrát használni, beleértve a webes proxykat is.

IoT Hub Device módszereket használ az alkalmazási rétegben annak biztosítása érdekében a végpontok megbízhatóságát, az adattitkosítás és az SSL/TLS segítségével integritása eszközmetódusokkal TCP/IP-adatok átviteléhez.

A magán a proxyn keresztül továbbított OPC UA bináris protokoll UA-hitelesítést és -titkosítást használ.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Az átjáró OPC kiadói modul feliratkozik az OPC UA-kiszolgálócsomópontokra az adatértékek változásainak észlelése érdekében. Ha a rendszer adatmódosítást észlel valamelyik csomóponton, ez a modul üzeneteket küld az Azure IoT Hubnak.

Az IoT Hub eseményforrást biztosít az Azure TSI-nek. A TSI 30 napig tárol adatokat az üzenetekhez csatolt időbélyegek alapján. A adatok tartalma:

* OPC UA ApplicationUri
* OPC UA NodeId
* A csomópont értéke
* Forrás időbélyege
* OPC UA DisplayName

A TSI jelenleg nem engedélyezi az ügyfelek számára a testre szabhatja, hogy mennyi ideig szeretnék megőrizni az adatokat.

Lekérdezések TSI a csomópontadatok egy időalapú **SearchSpan** összesítést pedig az **OPC UA ApplicationUri** vagy **OPC UA NodeId** vagy **OPC UA DisplayName**.

Az OEE és KPI-mérőműszer és az idősorozat-diagramok adatok lekéréséhez, a megoldás összesíti az adatokat a események száma szerint **Sum**, **átlagos**, **Min**, és  **Maximális**.

Az idősorozatok más folyamattal vannak felépítve. A megoldás állomások alapadataiból OEE és KPI értékeket számít ki, és az értékeket a gyártósorok, gyárak és vállalati buborékok.

Ezenkívül az OEE- és a KPI-topológia idősorozatai az alkalmazásban lesznek kiszámítva, amikor egy megjelenített időtartomány készen áll. A nap nézet például minden teljes órában frissül.

A csomópontadatok idősorozat nézete közvetlenül a TSI-ból származik az időtartomány összegzése segítségével.

## <a name="iot-hub"></a>IoT Hub
Az [IoT hub][lnk-IoT Hub] fogadja az OPC kiadói modulból a felhőbe küldött adatokat, és elérhetővé teszi azokat az Azure TSI szolgáltatás számára. 

A megoldásban az IoT Hub ezenkívül a következőket teszi:
- Fenntart egy identitásjegyzéket, amely tárolja az összes OPC kiadói modul és az összes OPC-proxymodul azonosítóját.
- Átviteli csatornaként szolgál az OPC-proxymodul kétirányú kommunikációjához.

## <a name="azure-storage"></a>Azure Storage
A megoldás Azure Blob Storage tárolót használ a virtuális gép lemezes tárolójaként, valamint az üzembehelyezési adatok tárolásához.

## <a name="web-app"></a>Webalkalmazás
A megoldásgyorsító részeként üzembe helyezett webalkalmazás integrált OPC UA-ügyfélből, riasztások feldolgozásából és telemetria tartalmazza.

## <a name="telemetry-data-flow"></a>Telemetria-adatfolyam

![Telemetria-adatfolyam](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>A folyamat lépései

1. Az OPC-közzétevő beolvassa a szükséges OPC UA X509 tanúsítványokat és IoT Hub biztonsági hitelesítő adatokat a helyi tanúsítványtárolóból.
    - Szükség esetén az OPC-közzétevő létrehozza és tárolja a tanúsítványtárolóból hiányzó tanúsítványokat vagy hitelesítő adatokat.

2. Az OPC-közzétevő regisztrálja magát az IoT Hubbal.
    - A konfigurált protokollt használja. Az IoT Hub ügyfél SDK által támogatott bármelyik protokollt használhatja. Az alapértelmezett az MQTT.
    - A protokollkommunikációt a TLS védi.

3. Az OPC-közzétevő beolvassa a konfigurációs fájlt.

4. Az OPC-közzétevő létrehoz egy OPC-munkamenetet mindegyik konfigurált OPC UA-kiszolgálóval.
    - TCP-kapcsolatot használ.
    - Az OPC-közzétevő és az OPC UA-kiszolgáló X509 tanúsítványokkal hitelesíti egymást.
    - Az összes további OPC UA-forgalmat a konfigurált OPC UA titkosítási mechanizmus titkosítja.
    - Az OPC-közzétevő minden konfigurált közzétételi intervallum OPC-munkamenetében létrehoz egy OPC-előfizetést.
    - OPC által monitorozott elemeket hoz létre az OPC-csomópontokhoz az OPC-előfizetésben való közzétételhez.

5. Ha egy monitorozott OPC-csomópont értéke módosul, az OPC UA-kiszolgáló frissítéseket küld az OPC-közzétevőhöz.

6. Az OPC-közzétevő átkódolja az új értéket.
    - Több módosítást kötegel, ha a kötegelés engedélyezett.
    - IoT Hub-üzenetet hoz létre.

7. Az OPC-közzétevő üzenetet küld az IoT Hubra.
    - A konfigurált protokollt használja.
    - A kommunikációt a TLS védi.

8. A Time Series Insights (TSI) beolvassa az üzeneteket az IoT Hubról.
    - AMQP-t használ TCP/TLS protokollon keresztül.
    - Ez a lépés az adatközpontban történik.

9. Inaktív adat a TSI-ben.

10. Az Azure AppService-lekérdezésekben a csatlakoztatott gyár webalkalmazása a TSI-ből származó adatokat igényelt.
    - TCP/TLS által védett kommunikációt használ.
    - Ez a lépés az adatközpontban történik.

11. A webböngésző a csatlakoztatott gyár webalkalmazásához csatlakozik.
    - Megjeleníti a csatlakoztatott gyári irányítópultot.
    - HTTPS-en keresztül csatlakozik.
    - A csatlakoztatott gyár alkalmazásának eléréséhez az Azure Active Directoryn keresztül kell hitelesíteni a felhasználót.
    - A csatlakoztatott gyár alkalmazásába irányuló összes WebApi-hívást hamisítás elleni tokenek védik.

12. Amikor adatfrissítés történik, a csatlakoztatott gyár webalkalmazása frissített adatokat küld a webböngészőnek.
    - A SignalR protokollt használja.
    - A TCP/TLS védi.

## <a name="browsing-data-flow"></a>Böngészési adatfolyam

![Böngészési adatfolyam](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>A folyamat lépései

1. Elindul az OPC-proxy (kiszolgáló-összetevő).
    - Beolvassa a megosztott hozzáférési kulcsokat a helyi tárolóból.
    - Szükség esetén a tárolóban tárolja a hiányzó hozzáférési kulcsokat.

2. Az OPC-proxy (kiszolgáló-összetevő) regisztrálja magát az IoT Hubbal.
    - Beolvassa az összes ismert eszközét az IoT Hubról.
    - MQTT-t használ TLS protokollon keresztüli Socketen vagy Secure Websocketen keresztül.

3. Webböngésző a csatlakoztatott gyári webalkalmazás csatlakozik, és megjeleníti a csatlakoztatott gyári irányítópultot.
    - HTTPS-t használ.
    - A felhasználó kiválasztja azt az OPC UA-kiszolgálót, amelyhez csatlakozni kíván.

4. A csatlakoztatott gyár webalkalmazása OPC UA-munkamenetet hoz létre a kiválasztott OPC UA-kiszolgálóval.
    - OPC UA-vermet használ.

5. Az OPC-proxyátvitel kérést kap az OPC UA-veremtől, hogy TCP szoftvercsatorna-kapcsolatot létesítsen az OPC UA-kiszolgálóval.
    - Csak lekéri a TCP hasznos adatokat és módosítás nélkül használja őket.
    - Ez a lépés a csatlakoztatott gyár webalkalmazásában történik.

6. Az OPC-proxy (ügyfélösszetevő) megkeresi az OPC-proxy (kiszolgáló-összetevő) eszközt az IoT Hub eszközjegyzékében. Ezután meghívja az OPC-proxy (kiszolgáló-összetevő) eszközmetódusát az IoT Hubon.
    - TCP/TLS protokollon keresztüli HTTPS-t használ az OPC-proxy kereséséhez.
    - TCP/TLS protokollon keresztüli HTTPS-t használ, hogy TCP szoftvercsatorna-kapcsolatot létesítsen az OPC UA-kiszolgálóval.
    - Ez a lépés az adatközpontban történik.

7. Az IoT Hub meghívja az OPC-proxy (kiszolgáló-összetevő) eszköz egyik eszközmetódusát.
    - Socket vagy Secure WebSocket kapcsolaton keresztüli TLS protokollon keresztüli létrehozott MQTT-t használ, hogy TCP szoftvercsatorna-kapcsolatot létesítsen az OPC UA-kiszolgálóval.

8. Az OPC-proxy (kiszolgáló-összetevő) továbbküldi a TCP hasznos adatait a helyszíni hálózatára.

9. Az OPC UA-kiszolgáló feldolgozza a hasznos adatokat, és visszaküldi a választ.

10. Az OPC-proxy (kiszolgáló-összetevő) szoftvercsatornája fogadja a választ.
    - Az OPC-proxy az eszközmetódus visszatérési értékeként küldi el az adatokat az IoT Hubra és az OPC-proxyra (ügyfélösszetevő).
    - Ezeket az adatokat az OPC UA-szoftvercsatornára kézbesíti a rendszer a csatlakoztatott gyári alkalmazásban.

11. A csatlakoztatott gyár webalkalmazása visszaküldi az OPC UA-kiszolgálótól kapott OPC UA-specifikus adatokkal kiegészült OPC UX-et a webböngészőnek renderelésre.
    - Amíg a felhasználó az OPC-címtérben keresztül jut, és az OPC-címtérben lévő csomópontokra vonatkozik a Funkciók, az OPC UX-et ügyfél AJAX-hívások le adatokat a csatlakoztatott gyári webalkalmazás Hamisításgátló jogkivonatokkal védett HTTPS-kapcsolaton keresztül használja.
    - Szükség esetén az ügyfél a 4-10. lépésekben leírt kommunikációval cserél információkat az OPC UA-kiszolgálóval.

> [!NOTE]
> Az OPC-proxy (kiszolgáló-összetevő) és az OPC-proxy (ügyfél) összetevő elvégzi a 4-10. lépéseket az OPC UA-kommunikációhoz kapcsolódó összes TCP-forgalomhoz.

> [!NOTE]
> A csatlakoztatott gyár webalkalmazásán belüli OPC UA-kiszolgáló és OPC UA-verem esetén az OPC-proxy kommunikáció átlátszó, és a hitelesítésre és titkosításra vonatkozó összes OPC UA biztonsági funkció érvényben van.

## <a name="next-steps"></a>További lépések

Folytassa az IoT-megoldásgyorsítók megismerését az alábbi cikkek elolvasásával:

* [Engedélyek az azureiotsolutions.com webhelyen][lnk-permissions]
* [Átjáró üzembe helyezése Windows vagy Linux rendszeren a csatlakoztatottgyár-alapú megoldásgyorsítóhoz](iot-accelerators-connected-factory-gateway-deployment.md)
* [OPC-közzétevő referenciamegvalósítása](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md).

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
