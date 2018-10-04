---
title: Az Azure IoT eszközoldali SDK-t a C - szerializáló |} A Microsoft Docs
description: Hogyan használható a szerializáló kódtárat, az Azure IoT eszközoldali c SDK-t a IoT hub-kommunikációs eszköz alkalmazások létrehozásához.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: 410ef936da7cf464dbef1698cf7019643cc1fb42
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249315"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT eszközoldali SDK-t a C – további információ a szerializálóról

Az oktatóanyag-sorozatban bemutatott első cikkében a [c nyelvhez készült Azure IoT eszközoldali SDK-t bemutatása](iot-hub-device-sdk-c-intro.md). A következő cikkben foglalt részletes leírását a [Azure IoT eszközoldali SDK-C – Iothubclientről az](iot-hub-device-sdk-c-iothubclient.md). Ez a cikk az SDK lefedettség befejezése azáltal, hogy a többi összetevő részletesebb leírását: a **szerializáló** könyvtár.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A bevezető cikkben leírt használata a **szerializáló** kódtárat az eseményeket az üzenetek küldése és fogadása az IoT hubról. Ebben a cikkben azt kiterjesztése adott vitafórum azáltal, hogy az adatok modellezése a teljes ismertetése az **szerializáló** makró nyelv. A cikk is tartalmaz részletes információkat olvashat, hogyan a tár szerializálja a üzenetek (és olykor hogyan szabályozható a szerializálási működése). Egyes paraméterek módosíthatja, amely meghatározza a létrehozott modellek méretét is ismertetjük.

Végül a cikk revisits előző cikkeket, például az üzenet és kezelési tulajdonság ismertetett témaköröket. Kapcsán fog ismerje meg, ezen funkciók munkahelyi azonos módon használatával a **szerializáló** könyvtár mivel ugyanúgy a **Iothubclientről** könyvtár.

Minden ebben a cikkben leírtak alapján a **szerializáló** SDK minták. Ha azt szeretné, hogy követni tudja, tekintse meg a **simplesample\_amqp** és **simplesample\_http** alkalmazások for c Azure IoT eszközoldali SDK-t tartalmaz

Annak a [ **a c nyelvhez készült Azure IoT eszközoldali SDK** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárházat és a nézet részletei az API-t a [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>A modellezés nyelv

A [a c nyelvhez készült Azure IoT eszközoldali SDK](iot-hub-device-sdk-c-intro.md) az oktatóanyag-sorozatban bemutatott a cikk a **a c nyelvhez készült Azure IoT eszközoldali SDK** keresztül a megadott példa lecserélésére az nyelvi modellezési a **simplesample\_ az amqp** alkalmazás:

```C
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Amint láthatja, a modellezési nyelv C makrók alapul. Mindig hozzákezd az meghatározás, **BEGIN\_NÉVTÉR** , és mindig **záró\_NÉVTÉR**. Szokás a névtér neve, a vállalat vagy, mint ebben a példában a projektet, amelyen dolgozik.

Modell-definíciók mi hová kerül a névtéren belül. Ebben az esetben van egy anemometer egy egyetlen modellt. Még egyszer a modell neve lehet bármi, de a modell neve általában az eszköz vagy cserél az IoT Hub kívánt adatok típusától.  

Modellek tartalmaz definíciót az eseményeket is az IoT hub bejövő (a *adatok*) és az IoT hubról fogadhat üzeneteket (a *műveletek*). Ahogy a példában látható, események rendelkezik egy típust és a egy nevet; műveletek nevét és opcionális paraméterek (minden típusú) rendelkezik.

Mi nem mutatja be ezt a mintát olyan további adatokat az SDK által támogatott. Ismertetjük, hogy a Tovább gombra.

> [!NOTE]
> Az IoT Hub hivatkozik az adatok egy eszköz által küldött el *események*, míg a modellezési nyelv hivatkozik, mint *adatok* (néhány meghatározott **WITH_DATA**). Hasonlóképpen, az IoT Hub eszköz küldött adatokat hivatkozik *üzenetek*, míg a modellezési nyelv hivatkozik, mint *műveletek* (néhány meghatározott **WITH_ACTION**). Vegye figyelembe, hogy ezeket a feltételeket is felhasználható felcserélhető ebben a cikkben.
> 
> 

## <a name="supported-data-types"></a>Támogatott adattípusok

Az alábbi adattípusok használatával létrehozott modellek támogatottak a **szerializáló** könyvtár:

| Típus | Leírás |
| --- | --- |
| double |kétszeres pontosságú lebegőpontos számot |
| int |32 bites egész számot |
| lebegőpontos |egyszeres pontosságú lebegőpontos számot |
| hossz |hosszú egész szám |
| int8\_t |8 bites egész számot |
| Int16\_t |16 bites egész számot |
| Int32\_t |32 bites egész számot |
| Int64\_t |64 bites egész számot |
| Logikai |logikai |
| ascii\_char\_ptr |ASCII-karakterlánc |
| EDM\_DÁTUM\_IDŐ\_ELTOLÁSA |dátum-idő eltolása |
| EDM\_GUID |GUID |
| EDM\_BINÁRIS |Bináris |
| DEKLARÁLJA\_STRUCT |Az összetett típus |

Kezdjük az utolsó adattípus. A **DECLARE\_STRUCT** lehetővé teszi összetett adattípusok, amely a többi típusokban csoportosításán alapulnak. Ez a csoportosítás lehetővé számunkra, hogy a modell a következőhöz hasonló:

```C
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

A modell típusa egyetlen eseményt tartalmaz **TestType**. **TestType** egy összetett típus, amely tartalmazza a által támogatott primitív típus együttesen bemutatásához tagok a **szerializáló** nyelvi modellezés.

Az ilyen modellek azt írhat kódot, hogy adatokat küldjön az IoT hubnak, amely a következőképpen jelenik meg:

```C
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Alapvetően, hogy már értékeket rendelne a minden tagja a **teszt** szerkezetét, és ezután hívása **SendAsync** küldése a **teszt** adatok esemény a felhőbe. **SendAsync** segítő függvény, amely egy egyetlen eseményt küld az IoT hubnak:

```C
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Ez a függvény a megadott esemény szerializálja, és elküldi azt az IoT Hub használatának **Iothubclientről\_SendEventAsync**. Ez az előző cikkben tárgyalt ugyanazt a kódot (**SendAsync** magában foglalja a logika egy kényelmes függvény).

Van egy másik segédfüggvény, használja az előző kód **GetDateTimeOffset**. Ez a függvény a megadott időn alakítja típusú érték **EDM\_dátum\_idő\_ELTOLÁS**:

```C
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Ha ezt a kódot futtatja, a következő üzenetet küld az IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Vegye figyelembe, hogy a szerializálási JSON formátumban által generált a **szerializáló** könyvtár. Vegye figyelembe, hogy minden egyes tagja a szerializált JSON-objektum megegyezik-e a tagjai a **TestType** a modellben meghatározott. Az értékek egyeznek használja a kód pontosan is. Ugyanakkor vegye figyelembe, hogy a bináris adatok base64-kódolású: "AQID" a base64 kódolás {0x01, 0x02, 0x03}.

Ez a példa bemutatja a használatának előnye a **szerializáló** kódtára –, lehetővé teszi a JSON elküldeni a felhőbe, anélkül hogy explicit módon bajlódnia a szerializálás az alkalmazásban. A modell és egyszerű API-k, az események küldéséhez a felhő majd hívása foglalkoznia kell, hogy az összes állítja az értékeket az adatok események.

Ezekkel az információkkal közé tartozik a támogatott adattípusok, beleértve a komplex típusok (sikerült belül más összetett típusok összetett típusok még belefoglaljuk) számos modellt is meghatározzuk. A fenti példa által generált szerializált JSON azonban nagyon fontos kimenetei. *Hogyan* adatok küldünk a **szerializáló** könyvtár meghatározza, hogy pontosan milyen JSON formátuma. Hogy az adott pont áll, az ismertetett témakörök mellett.

## <a name="more-about-serialization"></a>További információ a szerializálási

Az előző szakasz kiemeli a kimenetet egy példát a **szerializáló** könyvtár. Ebben a szakaszban elmagyarázzuk, hogyan a tár szerializálja a data, és hogy miként szabályozható a működés a szerializálási API-k használatával.

Annak érdekében, hogy azt mutatja be a szerializálási beszélgetések, azt használni egy thermostat alapján új modellt. Először is tekintsük meg bizonyos tapasztalattal a forgatókönyvet, hogy cím próbál.

Szeretnénk modell egy okosórák, amely hőmérséklettel és páratartalommal kapcsolatos méri. Minden adat küldését az IoT Hub másképp történik. Az okosórák ingresses alapértelmezés szerint a hőmérséklet esemény egyszer 2 percenkénti; egy páratartalom-esemény ingressed 15 percenként egyszer. Ha bármelyik esemény ingressed, tartalmaznia kell az időt jeleníti meg, hogy a megfelelő hőmérséklet vagy páratartalom mérték időbélyeget.

Adja meg ebben a forgatókönyvben, bemutatjuk, hogy két különböző módon modellezheti az adatokat, és elmagyarázzuk, hogy azt a hatást, hogy rendelkezik-e modellezési szerializált kimenetén.

### <a name="model-1"></a>1 modell

Íme egy modellt, amely támogatja az előző forgatókönyvben az első verziója:

```C
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Vegye figyelembe, hogy a modell tartalmazza-e két adatesemények: **hőmérséklet** és **páratartalom**. Ellentétben az előző példák az egyes eseményeket típus egy meghatározott struktúra **DECLARE\_STRUCT**. **TemperatureEvent** magában foglalja a hőmérséklet a mérés és a egy időbélyegzőt adnak; **HumidityEvent** páratartalom mérték és egy időbélyegzőt adnak tartalmazza. Ez a modell a fent leírt forgatókönyv az adatok természetes módon biztosít számunkra. A felhőbe küldünk egy eseményt, amikor az időbélyegző/hőmérséklet vagy páratartalom/timestamp párt vagy küldünk.

Egy hőmérsékleti esemény is küldünk a felhőbe, például a következő kód használatával:

```C
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Változtatható értékek használata hőmérséklettel és páratartalommal kapcsolatos a mintakódban egyelőre tegyük fel, hogy hogy ténylegesen olvas be ezeket az értékeket a megfelelő érzékelőket, az okosórák a mintavétellel.

A kód felett használja a **GetDateTimeOffset** segítő korábban jelent meg. Törölje újabb váló okokból ez a kód explicit módon elkülöníti szerializálása és az események a feladat. Az előző kód a hőmérséklet esemény szerializálja a pufferbe. Ezt követően **sendMessage** segítő függvény (szerepel **simplesample\_amqp**), amely az eseményt küld az IoT hubnak:

```C
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Ez a kód egy részét a **SendAsync** segítő az előző szakaszban leírt, ezért most nem megyünk visszakapom a névjegyeimet újra itt.

Az hőmérséklet-eseményt az előző kód futtatásakor a szerializált képernyőn az esemény érkezik, az IoT hubhoz:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Küldünk egy hőmérséklet típusú **TemperatureEvent** és a struktúrát tartalmaz egy **hőmérséklet** és **idő** tag. Ez közvetlenül a szerializált adatokban is megjelenik.

Hasonlóképpen küldünk egy páratartalom-esemény ezt a kódot:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A szerializált képernyő, az IoT hubnak küldött a következőképpen jelenik meg:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Újra ez az elvárt módon.

Ebben a modellben hogyan további események is imagine egyszerűen adhatók hozzá. Megadhat további struktúrák használatával **DECLARE\_STRUCT**, és a megfelelő eseményt tartalmazzák a modellben található **WITH\_adatok**.

Most, hogy ugyanazokat az adatokat tartalmazza, de eltérő szerkezetűek módosítsa a modellt.

### <a name="model-2"></a>2 modell

Vegye figyelembe a fenti alternatív modellt:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ebben az esetben fejlesztőkörnyezettel megszűnt a **DECLARE\_STRUCT** makrók és egyszerűen definiálása adatok elemet a forgatókönyvünk modellezési nyelvvel egyszerű típus használatával.

Csak for a pillanatban, figyelmen kívül hagyja a **idő** esemény. A feltöltési, az itt látható a kódot a bejövő forgalom **hőmérséklet**:

```C
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód a következő szerializált eseményt küld az IoT hubnak:

```C
{"Temperature":75}
```

És a kódját a páratartalommal kapcsolatos események a következőképpen jelenik meg:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód küld ennek az IoT hubnak:

```C
{"Humidity":45}
```

Eddig még mindig vannak meglepetések. Most módosítsuk a SZERIALIZÁLÁSA makró felhasználási módját.

A **SZERIALIZÁLÁSA** makró is igénybe vehet a argumentumként több adat-eseményeket. Ez lehetővé teszi számunkra, hogy szerializálni a **hőmérséklet** és **páratartalom** esemény együtt, és elküldheti azokat az IoT Hub hívásonként:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Előfordulhat, hogy kitalálja, hogy ez a kód eredménye, hogy két események küldhetők az IoT Hub:

[{"Hőmérséklet": 75}, {"Páratartalom": 45}]

Más szóval, előfordulhat, hogy várt, hogy ez a kód megegyezik a küldő **hőmérséklet** és **páratartalom** külön-külön. Csupán a könnyebb mindkét események át **SZERIALIZÁLÁSA** az adott hívásban. Azonban ez nem így. Ehelyett a fenti kód Ez egyetlen data eseményt küld az IoT hubnak:

{"Hőmérséklet": 75, "páratartalom": 45}

Tűnhet furcsa, mert a modell meghatározza **hőmérséklet** és **páratartalom** , két *külön* események:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Több a pontra, hogy nem modell ezek az események hol **hőmérséklet** és **páratartalom** ugyanazzal a struktúrával szerepelnek:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Ez a modell használatakor megérteni lenne hogyan **hőmérséklet** és **páratartalom** a szerializált ugyanazt az üzenetet küldi el. Azonban nem mindig egyértelmű ezért mindkét adatok események való megfelelés ily módon működik **SZERIALIZÁLÁSA** 2 modell használatával.

Ez a viselkedés akkor megérteni, ha tudja, hogy a feltételek, amelyek a **szerializáló** könyvtár, hogy így. Lépjen vissza a modell e összeállnak:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ez a modell felfoghatók objektumorientált feltételeit. Ebben az esetben azt már egy fizikai eszköz (thermostat) modellezés, és az eszköz tartalmaz-attribútumok **hőmérséklet** és **páratartalom**.

A modell, például a következő kóddal teljes állapotát is küldünk:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Feltéve, hogy az értékeket a hőmérséklet, páratartalmat és idő van beállítva, azt egy IoT hubra küldött hasonló esemény jelenik:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Egyes esetekben előfordulhat, hogy csak szeretne küldeni *néhány* (Ez akkor különösen igaz, ha a modell tartalmazza a nagy mennyiségű adat-eseményeket) a felhőbe a modell tulajdonságait. A hasznos adatok eseményeket, csak egy részhalmazát küldése például a korábbi példában:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Ez létrehoz pontosan az azonos szerializált esemény, mintha a korábban meghatározott egy **TemperatureEvent** együtt egy **hőmérséklet** és **idő** tag, ahogy a mi volt a modell 1. Ebben az esetben tudtuk pontosan az azonos szerializált esemény létrehozása egy másik modell (2. típus) használatával, mert felhívtuk **SZERIALIZÁLÁSA** eltérő módon.

A fontos pontot, hogy ha a felhasználó több adat események **SZERIALIZÁLÁSA,** azt feltételezi, hogy minden egyes esemény egy tulajdonságot egy JSON-objektumként, majd.

A legjobb módja, és hogyan úgy gondolja, hogy a modellel kapcsolatos függ. Ha az "események" küld a felhőbe, és minden egyes esemény tartalmaz egy meghatározott készletével tulajdonságok, az első módszer párosítása lehetővé teszi. Ebben az esetben használhatja **DECLARE\_STRUCT** struktúráját az egyes eseményeket, és hozzáadhatja őket a modellben a a **WITH\_adatok** makra. Ezután minden egyes esemény küldése, mint a fenti példában szereplő. Ez a megközelítés akkor csak adja át egy egyetlen esemény **SZERIALIZÁLÓ**.

Ha úgy gondolja, hogy a modell objektumorientált módon kapcsolatos, a második megközelítéssel is megfelelnek Önnek. Ebben az esetben az elemek meghatározott **WITH\_adatok** vannak a "Tulajdonságok" az objektum. Bármilyen események részhalmazát adja át **SZERIALIZÁLÁSA** , adja meg, attól függően, mekkora az "objektum" állapota is szeretne küldeni a felhőbe.

Nether módszer a megfelelő vagy nem megfelelő. Csak vegye figyelembe, hogy a **szerializáló** könyvtár működik, és válasszon a modellezési megközelítés, amely a leginkább megfelel az igényeinek.

## <a name="message-handling"></a>Üzenetkezelés

Ebben a cikkben eddig még csak az IoT hubra küldő események tárgyalt, és még nem rendelkező üzenetek fogadása. Oka a következő, hogy mit kell tudni üzenetek fogadása rendelkezik nagymértékben lett a cikkben foglalt [Azure IoT eszközoldali SDK-t a c nyelvhez készült](iot-hub-device-sdk-c-intro.md). Az említett cikkből visszahívása, egy üzenet visszahívási függvény regisztrálásával üzenetek feldolgozása:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Ezután ír a visszahívási függvény, amelyek akkor aktiválódnak, ha egy üzenet jelenik meg:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Ez a megvalósítás a **IoTHubMessage** meghívja az adott függvény a modellben minden egyes művelethez. Például ha a modell meghatározza a művelet:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Meg kell adnia egy függvényt az aláírás:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** majd nevezzük, amikor üzenetet küld az eszköz.

Mi azt még nem ismertetett még nem a szerializált verzió üzenet néz ki. Más szóval ha elküldi egy **SetAirResistance** üzenet az eszközön, mi does, hogy néz ki?

Ha egy üzenetet küld az eszközökre, tennénk keresztül az Azure IoT service SDK-t. Továbbra is szeretné tudja, milyen karakterlánc küldése egy adott művelet meghívásához. Egy üzenet küldéséhez az általános formátuma a következőképpen jelenik meg:

```C
{"Name" : "", "Parameters" : "" }
```

JSON-szerializált objektum, két tulajdonságokkal küld: **neve** neve, a művelet (üzenet) és **paraméterek** tartalmazza a művelet paramétereit.

Például meghívása **SetAirResistance** ezt az üzenetet küld egy eszköz:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

A művelet neve pontosan egyeznie kell a modellben meghatározott műveletet. A paraméterek nevei, valamint meg kell egyeznie. Azt is vegye figyelembe a kis-és nagybetűk. **Név** és **paraméterek** állandóan nagybetűt. Ellenőrizze, hogy a művelet nevét és a paraméterek a modellben szereplő nagybetűk. Ebben a példában a művelet neve pedig "SetAirResistance" nem "setairresistance".

A két más műveletek **TurnFanOn** és **TurnFanOff** elindítható, ezeket az üzeneteket küld egy eszköz:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Ebben a szakaszban leírt, amit tudni, hogy mikor küldő események és -fogadást az üzeneteket a **szerializáló** könyvtár. Mielőtt továbblép, most konfigurálhatja néhány paraméter, amely szabályozza, hogy mekkora a modellt, foglalkozik.

## <a name="macro-configuration"></a>Makró-konfiguráció

Ha használja a **szerializáló** érdemes figyelembe vennie az SDK fontos része az azure-c-megosztott segédeszközök erőforrástárban található könyvtár.

Ha rendelkeznie klónozta a Githubról, a--rekurzív lehetőség használata az Azure-iot-sdk-c adattár, majd megtalálja a megosztott segédprogram kódtára itt:

```C
.\\c-utility
```

Ha a könyvtár nem rendelkeznie klónozta, megtalálhatja azt [Itt](https://github.com/Azure/azure-c-shared-utility).

A megosztott segédeszközök könyvtárból talál a következő mappában:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ez a mappa tartalmaz egy Visual Studio-megoldás nevű **makró\_utils\_h\_generator.sln**:

  ![A Visual Studio-megoldás maco_utils_h_generator képernyőképe](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

Ebben a megoldásban a program létrehozza az **makró\_utils.h** fájlt. Van egy alapértelmezett makró\_utils.h fájlban szerepel az SDK-val. Ez a megoldás lehetővé teszi bizonyos paramétereit, és hozza létre a fejlécfájlt ezen paraméterek alapján.

Az érintett két kulcs paraméterei a következők **nArithmetic** és **nMacroParameters** amely határozzák meg az alábbi két sort található a makró\_utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Ezeket az értékeket a rendszer az alapértelmezett paraméterekkel, az SDK-val együtt. Egyes paraméterek a következő jelentése van:

* nMacroParameters – azt szabályozza, használhat egy DECLARE hány paraméterek\_MODELL definice makra.
* nArithmetic – azt szabályozza, a modellek engedélyezett tagok száma.

Ezek a paraméterek fontosak oka mivel általuk milyen méretű lehet a modellt. Vegyük példaként a modell definíciója:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Ahogy korábban említettük **DECLARE\_MODELL** csak egy C makró. A modell nevei és az **WITH\_adatok** utasítás (még egy másik makró) paraméterei **DECLARE\_MODELL**. **nMacroParameters** határozza meg, hány paraméterek tartalmazhat **DECLARE\_MODELL**. Hatékony hány adatok események és műveletek nyilatkozatok rendelkezhet határozza meg. Mint ilyen az alapértelmezett korlát a 124 Ez azt jelenti, hogy definiálhat egy modellt kombinációját mintegy 60 műveletek és adatok események. Ha túllépi ezt a határt, kapni fog a fordítási hibákat adta, az alábbihoz hasonló lehet:

  ![Képernyőkép a makró paraméterek fordítóprogram-hibákkal](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

A **nArithmetic** paraméter, mint az alkalmazás belső működését a makró nyelv tudnivalók több.  Azt szabályozza, hogy az is lehet a modellt, a tagok összes száma például **DECLARE_STRUCT** makrókat. A fordítási hibák kezdenek, akkor meg kell próbálnia növelése **nArithmetic**:

   ![Képernyőkép a aritmetikai fordítóprogram-hibákkal](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Ha szeretné módosítani ezeket a paramétereket, módosítsa az értékeket a makró\_utils.tt fájlt, a makró újrafordítottuk\_utils\_h\_generator.sln megoldást, és futtassa a lefordított program. Ha így tesz, így új makró\_utils.h fájl jön létre, és elhelyezni a.\\ közös\\inc könyvtárat.

A makró új verziójának használatához\_utils.h, távolítsa el a **szerializáló** megoldását, és helyette NuGet-csomagot tartalmazza a **szerializáló** Visual Studio-projekt. Ez lehetővé teszi a kód fordítása a szerializáló kódtár forráskódja ellen. Ez magában foglalja a frissített makró\_utils.h. Ha azt szeretné, hogy tegye meg **simplesample\_amqp**, indítsa el a megoldás a szerializáló-könyvtár NuGet-csomag eltávolításával:

   ![Képernyőfelvétel a NuGet-csomag a szerializáló könyvtár eltávolítása](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Ezután adjon hozzá a projektet a Visual Studio-megoldás:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Ha elkészült, a megoldás módon kell kinéznie:

   ![A Visual Studio-megoldás simplesample_amqp képernyőképe](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Ha most a megoldását, a frissített makró fordítása\_utils.h a bináris fájlt tartalmaz.

Vegye figyelembe, hogy ezek az értékek elég nagy növelése túlléphetik a fordító korlátok. Ezt a pontot, hogy a **nMacroParameters** fő paraméter, amellyel érintett. A C99 specifikációja Megadja, hogy 127 paraméterek közül legalább egy definice makra engedélyezettek. A Microsoft fordító pontosan követi az specifikációja (és legfeljebb 127 karakter), így növelheti a fogunk **nMacroParameters** az alapértelmezett mellé. Más fordítóprogramot lehetővé teheti, hogy ehhez (például a GNU fordító nagyobb Korlátértékre támogatja).

Eddig már szó szinte minden, amit tudni, hogy a kód írásával kapcsolatban a **szerializáló** könyvtár. Előtt áthaladva addig tart, most nyissa meg újra az előző cikkek, amelyek most felmerülhet Önben kapcsolatos témaköröket.

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k
A mintaalkalmazás, amelyen ez a cikk a fókuszban lévő **simplesample\_amqp**. Ebben a példában a magasabb szintű (a nem -**LL**) API-k eseményeket küldhet és fogadhat üzeneteket. Ezen API-k használatakor a háttérbeli szálon fut, amely gondoskodik is eseményt üzenetek küldése és fogadása. Az alacsonyabb szintű (r) API-k használatával azonban a háttérben futó szálak számára, és explicit vezérlését, amikor eseményeket küldeni, vagy üzenet fogadása a felhőben.

Leírtak szerint egy [előző cikk](iot-hub-device-sdk-c-iothubclient.md), funkciók egy halmaza, amely a magasabb szintű API-k áll:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* Iothubclientről\_megszüntetése

Ezen API-k találja meg a **simplesample\_amqp**.

Emellett van egy ehhez hasonló alacsonyabb szintű API-k készlete.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* Iothubclientről\_LL\_megszüntetése

Vegye figyelembe, hogy az alacsonyabb szintű API-k pontosan ugyanúgy működnek, a fenti cikkekben leírtak szerint. Az első API-k készlete használhatja, ha azt szeretné, hogy eseményeket küldő és fogadó üzenetek kezelésére egy háttérszálból történik. Ha azt szeretné, hogy mikor küldött és fogadott adatok az IoT hubról explicit szabályozhatja a második API-k készlete használhatja. Bármelyik munkahelyi API-k készlete egyaránt jól az a **szerializáló** könyvtár.

Az alacsonyabb szintű API-k használata a példa a **szerializáló** könyvtár, lásd: a **simplesample\_http** alkalmazás.

## <a name="additional-topics"></a>További témakörök
Érdemes megemlíteni néhány egyéb témakörök újra tulajdonság kezelnek, alternatív hitelesítő adatai, és konfigurációs lehetőségeket. Ezek a tárgyalt témakörök egy [előző cikk](iot-hub-device-sdk-c-iothubclient.md). A fő pont, hogy ezek a funkciók a ugyanúgy működik a **szerializáló** könyvtár mivel ugyanúgy a **Iothubclientről** könyvtár. Például, ha szeretné a modellből származó tulajdonságok csatolása eseményhez, használhatja **IoTHubMessage\_tulajdonságok** és **térkép**\_**AddorUpdate**, ugyanúgy, mint a korábban ismertetett:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

E esemény létrejött-e a a **szerializáló** könyvtár segítségével manuálisan létrehozott vagy a **Iothubclientről** könyvtár nem számít.

A hitelesítő adatokat másik eszköz használatával **Iothubclientről\_LL\_létrehozás** ugyanolyan jól működik, **Iothubclientről\_CreateFromConnectionString** számára lefoglalása egy **IOTHUB\_ügyfél\_KEZELNI**.

Végül használata a **szerializáló** könyvtár, beállíthatja a konfigurációs beállítások **Iothubclientről\_LL\_SetOption** ugyanúgy, mint a használatasorán**Iothubclientről** könyvtár.

Egy szolgáltatás, amely egyedi az **szerializáló** könyvtár az API-k inicializálása. A kódtár használata előtt el kell meghívnia **szerializáló\_init**:

```C
serializer_init(NULL);
```

Ez akkor történik meg, csak meghívása előtt **Iothubclientről\_CreateFromConnectionString**.

Hasonlóképpen, ha befejezte a tár dolgozik, az utolsó hívás fogja, hogy **szerializáló\_deinit**:

```C
serializer_deinit();
```

Ellenkező esetben a többi a fent felsorolt funkciók működni a **szerializáló** könyvtár mint az a **Iothubclientről** könyvtár. Ezek a témakörök kapcsolatban további információkért lásd: a [előző cikk](iot-hub-device-sdk-c-iothubclient.md) az oktatóanyag-sorozatban.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti, egyedi aspektusai a **szerializáló** függvénytár szerepel az **Azure IoT eszközoldali SDK-t a c nyelvhez készült**. Az adatokkal jól ismerik a modellek használata eseményt üzenetek küldése és fogadása az IoT hubról, rendelkeznie kell biztosítani.

Ezzel befejezte a háromrészes sorozat, hogyan hozhat létre alkalmazásokat a **Azure IoT eszközoldali SDK-t a c nyelvhez készült**. Nem csak az első lépéseit, de biztosít az API-k működésének alapos ismerete elegendő információt kell lennie. További információk találhatók néhány minták az SDK nem vonatkozik. Ellenkező esetben a [Azure IoT SDK-dokumentáció](https://github.com/Azure/azure-iot-sdk-c) akkor a leghasznosabb további információt.

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Részletesebb megismerése az IoT Hub képességeit, tekintse meg [telepítése AI peremhálózati eszközökre az Azure IoT Edge segítségével](../iot-edge/tutorial-simulate-device-linux.md).