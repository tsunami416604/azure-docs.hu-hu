<properties
 pageTitle="Az eszközfelügyelet áttekintése | Microsoft Azure"
 description="Az Azure IoT Hub-eszközfelügyelet áttekintése: ikereszközök, eszközlekérdezések, eszközfeladatok"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Az Azure IoT Hub-eszközfelügyelet áttekintése (előzetes verzió)

Az Azure IoT Hub-eszközfelügyelet szabványokon alapuló IoT-eszközfelügyeletet tesz lehetővé, amelynek keretében távolról felügyelheti, konfigurálhatja és frissítheti eszközeit.

Az Azure IoT három főbb eszközfelügyeleti koncepciót alkalmaz:

1.  **Ikereszköz:** a fizikai eszköz leképezése az IoT Hubban.

2.  **Eszközlekérdezések**: e funkcióval megkeresheti az ikereszközöket, és összesített információkkal betekintést nyerhet az ikereszközök csoportjainak állapotába. Lefuttathat például egy lekérdezést, amely megkeresi az 1.0-s verziójú belső vezérlőprogrammal működő összes eszközt.

3.  **Eszközfeladatok**: e funkcióval különböző műveleteket végezhet el egy vagy több fizikai eszközön, például frissítheti a belső vezérlőprogramot, újraindítást végezhet, vagy visszaállíthatja a gyári beállításokat.

## Ikereszközök

Az ikereszközök a fizikai eszköz leképezései az Azure IoT-ben. Az ikereszközt általában a **Microsoft.Azure.Devices.Device** objektum képviseli.

![][img-twin]

Az ikereszköz az alábbi komponensekből áll:

1.  **Eszközmezők:** az eszközmezők előre definiált tulajdonságok, amelyeket az IoT Hub üzenetküldési és eszközfelügyeleti funkciói egyaránt használnak. Az IoT Hub ezekkel azonosítója a fizikai eszközöket, és csatlakozik hozzájuk. Az eszközmezőket a rendszer nem szinkronizálja az eszközzel, ezeket kizárólag az ikereszköz tárolja. Az eszközmezők között szerepel az eszköz azonosítója, illetve a hitelesítési adatok is.

2.  **Eszköztulajdonságok:** az eszköztulajdonságok előre definiált szótára segítségével lehet leírni a fizikai eszközt. A fizikai eszköz minden eszköztulajdonság fölött áll, ez az egyes értékek mérvadó tárháza. A rendszer a felhőben, az ikereszközben tárolja ezeknek a tulajdonságoknak a leképezéseit, amelyek különböző tényezőktől függően többé-kevésbé konzisztensek. A koherenciát és a naprakészséget a különböző szinkronizálási beállítások határozzák meg, amelyekről részletesen is olvashat a [Tutorial: how to use the device twin][lnk-tutorial-twin] (Oktatóanyag: Ikereszközök használata) című cikkben. Az eszköztulajdonságok közé tartozik például a belső vezérlőprogram verziója, az akkumulátor töltöttségi szintje vagy a gyártó neve.

3.  **Szolgáltatás tulajdonságai:** a szolgáltatástulajdonságok **&lt;key,value&gt;** (paraméter-érték) párok, amelyeket a fejlesztő vesz fel a szolgáltatástulajdonságok szótárába. Ezek a tulajdonságok alkalmasak az ikereszköz adatmodelljének bővítésére, amely így jobban alkalmassá tehető az eszköz leírására. A szolgáltatástulajdonságokat a rendszer nem szinkronizálja az eszközzel, ezeket kizárólag a felhőben lévő ikereszköz tárolja. Szolgáltatástulajdonság lehet például a következő: **&lt;NextServiceDate, 11/12/2017&gt;**, amelynek segítségével a következő szervizelés dátuma alapján kereshet az eszközök között.

4.  **Címkék:** a címkék a szolgáltatástulajdonságok részhalmazát alkotják, ezek nem szótártulajdonságok, hanem tetszőleges karakterláncok. Dekorációt fűzhet velük az ikereszközökhöz, illetve csoportokba rendezheti az eszközöket. A címkéket a rendszer nem szinkronizálja az eszközzel, ezeket kizárólag az ikereszköz tárolja. Ha például az ikereszköz egy tényleges teherautó leképezése, felveheti az általa szállított árukat képviselő (**alma**, **narancs** és **banán**) címkéket.

## Eszközlekérdezések

Az előző fejezetben megismerhette az ikereszközök különböző összetevőit. Most elmagyarázzuk, hogyan keresheti meg az ikereszközöket az IoT Hub eszközbeállítás-jegyzékében eszköztulajdonságok, szolgáltatástulajdonságok vagy címkék alapján. Érdemes például lekérdezést alkalmazni, ha szeretné megtalálni az összes eszközt, amely frissítésre szorul. Ebben az esetben lekérdezheti a jegyzékből az összes olyan eszközt, amely megadott verziójú belső vezérlőprogrammal működik, majd az eredményeket átadhatja egy beállítható műveletnek (ezt az IoT Hubban eszközfeladatnak nevezzük, amelyet a következő részben írunk le részletesen).

A lekérdezést elvégezheti címkék és tulajdonságok segítségével:

-   Ikereszközök címkék segítségével való lekérdezéséhez egy karakterlánctömböt kell beküldenie, a lekérdezés pedig visszaadja azokat az eszközöket, amelyek címkéi között az összes megadott karakterlánc megtalálható.

-   Ikereszközök szolgáltatástulajdonságokkal vagy eszköztulajdonságokkal való lekérdezéséhez JSON lekérdezési kifejezést kell használni. Az alábbi példában bemutatjuk, hogyan kérdezheti le az összes eszközt, amelynél a **FirmwareVersion** eszköztulajdonság-paraméter értéke **1.0**. Láthatja, hogy a tulajdonság **type** (típus) értéke **device** (eszköz), ami azt jelzi, hogy nem szolgáltatástulajdonságok, hanem eszköztulajdonságok alapján végzünk lekérdezést:

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Eszközfeladatok

Az eszközfelügyelet következő fontos eleme az eszközfeladatok területe, amelyek segítségével többlépéses feladatokat hajthat végre, egyszerre akár több eszközön is.

Az Azure IoT Hub-eszközfelügyelet jelenleg hat különböző típusú eszközfeladatot tartalmaz (a felhasználói igények alapján a későbbiekben további feladatokat fogunk elérhetővé tenni):

- **Firmware update** (Belső vezérlőprogram frissítése): a fizikai eszköz belső vezérlőprogramjának (vagy operációsrendszer-képének) frissítésére szolgál.
- **Reboot** (Újraindítás): a fizikai eszköz újraindítására szolgál.
- **Factory reset** (Gyári beállítások visszaállítása): visszaállítja a fizikai eszköz belső vezérlőprogramját (vagy operációsrendszer-képét) az eszközön tárolt gyári biztonsági rendszerképre.
- **Configuration update** (Konfigurációfrissítés): a fizikai eszközön futó IoT Hub-ügyfélügynök konfigurálására szolgál.
- **Read device property** (Eszköztulajdonság olvasása): lekéri egy adott eszköztulajdonság legfrissebb értékét a fizikai eszközről.
- **Write device property** (Eszköztulajdonság írása): módosít egy adott eszköztulajdonságot a fizikai eszközön.

Az egyes feladatok használatával kapcsolatos részletes információkért lásd: [API documentation for C\# and node.js][lnk-apidocs] (API-dokumentáció C#-hoz és node.js-hez).

A feladat egyszerre több eszközön is képes elvégezni az előírt műveletet. Amikor elindít egy feladatot, a rendszer létrehoz egy kapcsolódó gyermekfeladatot az összes megadott eszközön. A gyermekfeladatok csak egy adott eszközön futnak. Mindegyik gyermekfeladat tartalmaz egy mutatót a szülőfeladatára. A szülőfeladat csupán a gyermekfeladatok tárolójaként szolgál, nem hajt végre logikai műveleteket az eszköztípusok megkülönböztetésére (így például nem tud különbséget tenni egy Intel Edison és egy Raspberry Pi frissítése között). Az alábbi ábrán a szülőfeladat, az ehhez tartozó gyermekfeladatok, valamint a kapcsolódó fizikai eszközök közötti kapcsolatot illusztráltuk.

![][img-jobs]

Az elindított feladatok állapotának megtekintéséhez lekérdezheti a feladatelőzményeket. Erre több példát is talál [lekérdezéstárunkban][lnk-query-samples].

## Megvalósítás az eszközökön

Most, hogy szót ejtettünk a szolgáltatásra vonatkozó megoldásokról, térjünk rá arra, hogy hogyan lehet beállítani a fizikai eszközök felügyeletét. Az Azure IoT Hub DM-ügyfélkódtára lehetőséget kínál az IoT-eszközök Azure IoT Hub segítségével megvalósított felügyeletére. A „felügyelet” itt olyan műveleteket jelent, mint például az újraindítás, a gyári beállítások visszaállítása vagy a belső vezérlőprogram frissítése.  Ma csupán a platformfüggetlen C-kódtár érhető el, de hamarosan a további nyelvek támogatásán is javítani fogunk.  

A DM-ügyfélkódtár két fontos szerepet tölt be az eszközfelügyeletben:

- Tulajdonságok szinkronizálása a fizikai eszköz és az IoT Hubban lévő megfelelő ikereszköz között
- Az IoT Hub által az eszközökre küldött eszközfeladatok koreografálása

További információk ezekről a feladatokról, valamint a fizikai eszközökön elérhető megvalósítási lehetőségekről: [Introducing the Azure IoT Hub device management client library for C][lnk-library-c] (Az Azure IoT Hub eszközfelügyeleti C#-ügyfélkódtárának bemutatása).

## Következő lépések

Ha az ügyfélalkalmazásokat az eszközök hardveres platformjainak és operációs rendszereinek széles választékára szeretné telepíteni, használhatja az IoT eszköz SDK-kat. Az IoT eszköz SDK-k olyan kódtárakat tartalmaznak, amelyek elősegítik a telemetria küldését az IoT Hubra, valamint a felhőből az eszközre irányuló parancsok fogadását. Amikor az SDK-kat használja, több hálózati protokoll közül választhat az IoT Hubbal való kommunikációhoz. További tudnivalókért lásd: [információ az eszköz SDK-król][lnk-device-sdks].

Ha szeretne részletesebben is megismerkedni az Azure IoT Hub eszközfelügyeleti funkcióival, olvassa el a [Get started with Azure IoT Hub device management][lnk-get-started] (Ismerkedés az Azure IoT Hub eszközfelügyeleti funkcióival) című oktatóanyagot.

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks



<!--HONumber=sep16_HO1-->


