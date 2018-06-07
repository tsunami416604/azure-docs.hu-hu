---
title: Az Azure IoT-eszközök SDK c - szerializáló |} Microsoft Docs
description: Hogyan használható az Azure IoT-eszközök C-hez készült SDK a szerializáló szalagtár kommunikáló eszközön futó alkalmazások létrehozásához az IoT-központ számára.
author: yzhong94
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: yizhon
ms.openlocfilehash: a724fa5acc930475bdbe4ffcc74141470a92326c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634144"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Az Azure IoT-eszközök SDK c – további információk a szerializáló
A [először a következő cikket:](iot-hub-device-sdk-c-intro.md) a sorozat bevezette a **C-hez készült SDK Azure IoT-eszközök**. A következő cikk megadott részletes leírása a [ **IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). Ez a cikk az SDK körét befejeződik, adja meg a részletes leírását, a többi összetevő: a **szerializáló** könyvtár.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A bevezető cikkben leírt használata a **szerializáló** események üzeneteket küldjön és fogadjon az IoT-központ könyvtár. Ebben a cikkben tovább bővítjük ezekre a kérdésekre adott válaszokat azáltal, hogy egy több teljes magyarázatát, hogy hogyan a következő modellre: az adatok a **szerializáló** makró nyelv. A cikk is tartalmaz, hogy a szalagtár rendezi sorba üzenetek további információkra (és egyes esetekben, hogy miként szabályozható a szerializálási viselkedés). Egyes paraméterek módosíthatja a modellek méretének meghatározó is ismerteti.

Végezetül a cikk revisits néhány témaköre például üzenet és a tulajdonság kezelési előző cikkek tárgyalja. Mint azt fogja megtudhatja, ezen szolgáltatások munkahelyi ugyanazon módon használatával a **szerializáló** könyvtár, mint az a **IoTHubClient** könyvtár.

Minden ebben a cikkben leírtak alapján a **szerializáló** SDK minták. Ha azt szeretné, követéséhez, tekintse meg a **simplesample\_amqp** és **simplesample\_http** alkalmazások az Azure IoT-eszközök SDK szerepel a c kiszolgálóra.

Megtalálhatja az [ **C-hez készült SDK Azure IoT-eszközök** ](https://github.com/Azure/azure-iot-sdk-c) GitHub tárház és nézet adatai az API-nak a [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-modeling-language"></a>A modellezési nyelv
A [bevezető cikkben](iot-hub-device-sdk-c-intro.md) a sorozat bevezetni a **C-hez készült SDK Azure IoT-eszközök** modellezési nyelvi keresztül a megadott példa a **simplesample\_amqp** alkalmazás:

```
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

Ahogy látja, a modellezési nyelvi C makrók alapul. Mindig megkezdi a definíció **BEGIN\_NÉVTÉR** és mindig végződhet **END\_NÉVTÉR**. A névtér nevét, a vállalat, vagy a példában a projekt, amely dolgozik közös.

Mi kerül a névtéren belül modell definíciókat. Ebben az esetben van egy anemometer egyetlen modellt. Ebben az esetben a modell neve a következő lehet bármi, de általában ez nevű az eszköz vagy az IoT-központ való kívánt adatok típusát.  

Modellek tartalmazza a következő definícióját az események irányuló, az IoT-központ is (a *adatok*) és az IoT-központ fogadhat üzeneteket (a *műveletek*). Ahogy látja, a példában, események rendelkezik-e egy típust és egy nevet; műveletek nevét és a választható paraméterek: (minden típusú) rendelkezik.

Mi nem mutatja be ezt a mintát olyan további adatokat az SDK által támogatott. Oktatóanyag a következőket ismerteti, hogy a Tovább gombra.

> [!NOTE]
> Az IoT-központ hivatkozik rá küld egy eszközt az adatok *események*, míg a modellezési nyelvi hivatkozik rá *adatok* (segítségével meghatározott **WITH_DATA**). Hasonlóképpen, az IoT-központ hivatkozik-e az eszközök és küldött adatok *üzenetek*, míg a modellezési nyelvi hivatkozik rá *műveletek* (segítségével meghatározott **WITH_ACTION**). Vegye figyelembe, hogy ezek a feltételek szabadon felcserélhetők ebben a cikkben.
> 
> 

### <a name="supported-data-types"></a>Támogatott adattípusokat
A következő típusok támogatottak a létrehozott modelleket a **szerializáló** könyvtár:

| Típus | Leírás |
| --- | --- |
| double |dupla pontosságú lebegőpontos szám |
| int |32 bites egész szám |
| lebegőpontos |az egyszeres pontosságú lebegőpontos szám |
| hossz |hosszú egész szám |
| int8\_t |8 bites egész szám |
| Int16\_t |16 bites egész szám |
| Int32\_t |32 bites egész szám |
| Int64\_t |64 bites egész szám |
| logikai érték |logikai |
| ascii\_char\_ptr |ASCII-karakterlánc |
| EDM\_DÁTUM\_IDŐ\_ELTOLÁSA |dátum idő eltolása |
| EDM\_GUID |GUID |
| EDM\_BINÁRIS |Bináris |
| DEKLARÁLJA\_STRUKTÚRA |Összetett adattípusú |

Kezdjük az utolsó típusát. A **DECLARE\_STRUCT** összetett adattípusú, amelyek csoportosításait. emellett az egyéb egyszerű típusú adható meg. Ezek a Csoportosítások lehetővé teszik a számunkra, amely a következőképpen néz ki a modellek meghatározásához:

```
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

A modell típusú egyetlen eseményt tartalmaz **TestType**. **TestType** több tag, amelyek együttesen bemutatása által támogatott primitív típusok tartalmazó összetett típus a **szerializáló** modeling language.

Az ilyen modellek azt is írhat kódot szeretnék adatokat küldeni a IoT-központ, amely a következőképpen jelenik meg:

```
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

Alapvetően, azt még hozzárendelése értéket minden tagja a **teszt** szerkezetét, és azt, majd hívja **sendasync metódusok párhuzamosan** küldeni a **teszt** adatok esemény a felhőbe. **Sendasync metódusok párhuzamosan** küld egy egyetlen eseményt az IoT hubhoz segítő függvény:

```
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

Ez a függvény a megadott esemény rendezi sorba, és elküldi IoT-központ használatával **IoTHubClient\_SendEventAsync**. Ez az előző cikkekben ismertetett ugyanazt a kódot (**sendasync metódusok párhuzamosan** magában foglalja a logika egy kényelmes függvénynek).

Egy előző kóddal használt egyéb segítő függvény **GetDateTimeOffset**. Ez a függvény a megadott időn átalakítja típusú érték a **EDM\_dátum\_idő\_ELTOLÁS**:

```
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

Ha ezt a kódot, a következő üzenetet küld az IoT-központ:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Vegye figyelembe, hogy a szerializálás JSON, amely formátuma az előállított által a **szerializáló** könyvtár. Vegye figyelembe azt is, hogy a szerializált JSON-objektum minden egyes tagjára megegyezik-e a tagjai a **TestType** , amely meghatározott a modellben. Az értékek is pontosan megegyeznek a kódban használt. Azonban vegye figyelembe, hogy a bináris adatok base64-kódolású: "AQID" a base64 kódolása {0x01, 0x02, 0x03}.

Ez a példa bemutatja, hogyan használatának előnye a **szerializáló** library – lehetővé teszi, hogy JSON küldjük a felhőbe, anélkül, hogy az alkalmazás a szerializálási explicit módon kezelésére. Összes foglalkoznia kell van állítsa be az értékeket az adatok események tekinthetők, és ezeket az eseményeket a felhőbe küldése egyszerű API-k majd hívásakor.

Az információ azt adhatja meg a modellek, amelyek közé tartozik a támogatott adattípusok, beleértve a komplex típusok (sikerült is magában foglalja az összetett típusok más összetett típusok belül) tartományán. Azonban, hogy szerializálni által létrehozott JSON a fenti példában egy fontos pont megjelenik. *Hogyan* adatok küldünk a **szerializáló** könyvtár meghatározza, hogy pontosan hogyan a JSON formátuma. Hogy az adott pont áll, mi oktatóanyag a következőket ismerteti mellett.

## <a name="more-about-serialization"></a>További információ a szerializálási
Az előző szakaszban által létrehozott kimeneti példát kiemeli a **szerializáló** könyvtár. Ebben a részben azt ismertetjük, hogyan rendezi sorba a könyvtárban az adatokat, és hogy miként szabályozható, hogy a szerializálás API-k használatával viselkedését.

Ahhoz, hogy előre a szerializálási döntéseken, dolgozunk lesz az új modell egy termosztát alapján. Először hozzuk adja meg a bizonyos tapasztalattal a forgatókönyvön keresztül próbáljuk cím.

A modell egy termosztát, és mérheti a hőmérséklet és a páratartalom szeretnénk. Minden adat küldendő IoT-központ eltérő lesz. Alapértelmezés szerint a termosztát ingresses egy hőmérséklet esemény egyszer 2 percenként; a páratartalom esemény ingressed 15 percenként egyszer. Ha bármelyik esemény ingressed, tartalmaznia kell egy Timestamp típusú, amely a időt jeleníti meg, hogy a megfelelő hőmérséklet és a páratartalom mérték.

Ebben a forgatókönyvben, az adatok két különböző módon lesz bemutatjuk, és azt ismertetjük, hogy a hatást, hogy a szerializált kimeneti modellezési rendelkezik.

### <a name="model-1"></a>1 modell
Az első verzió-modell, amely támogatja az előző példában a következő:

```
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

Vegye figyelembe, hogy a modell tartalmaz-e a két adatok események: **hőmérséklet** és **páratartalom**. Előző példák ellentétben minden esemény típus megadott struktúra **DECLARE\_STRUCT**. **TemperatureEvent** tartalmaz egy hőmérséklet mérési és egy időbélyegzőt adnak; **HumidityEvent** páratartalom a mérés és egy időbélyegzőt adnak tartalmazza. Ez a modell lehetőséget nyújt nekünk természetes az adatok a fent leírt forgatókönyv esetében. Ha az esemény nem küldeni a felhőben, hőmérséklet/időbélyeg vagy páratartalom/időbélyeg-párból vagy küldünk.

Hőmérséklet-esemény elküldhetjük a felhőre, például az alábbi kód használatával:

```
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

Rendszer fogja változtatható értékek használata a hőmérséklet és a páratartalom példakód, de a képzelhető el, hogy azt ténylegesen olvas be ezeket az értékeket a termosztát a megfelelő érzékelők mintavétellel.

A fenti által használt kódot a **GetDateTimeOffset** korábban bevezetett segítő. Törölje későbbi lesz okokból ezt a kódot explicit módon elválasztja a tevékenység szerializálása során, és az esemény küldése. Az előző kód a hőmérséklet esemény be pufferbe rendezi sorba. Ezt követően **sendMessage** segítő függvény (szereplő **simplesample\_amqp**) IoT-központ, amely elküldi az esemény:

```
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

Ez a kód alkészlete a **sendasync metódusok párhuzamosan** segítő az előző szakaszban leírt, így azt nem ismerteti azt újra ide.

Az előző kód küldése az hőmérséklet esemény futtatásakor az esemény a szerializált képernyőn zajlik az IoT hubhoz:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Azért küldjük egy hőmérséklet, amelynek a típusa **TemperatureEvent** és, hogy a struct tartalmaz egy **hőmérséklet** és **idő** tag. Ez közvetlenül a szerializált adatokban is megjelenik.

Hasonlóképpen elküldhetjük a páratartalom esemény ezzel a kóddal:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

A szerializált formában küldi el az IoT-központ a következőképpen jelenik meg:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ebben az esetben ez az elvárt módon.

Ebben a modellben hogyan további események is tekinthető könnyen lehet hozzáadni. Adhat meg további struktúrák használatával **DECLARE\_STRUCT**, és adja meg a kapcsolódó esemény a modell használatával **WITH\_adatok**.

Most módosítsa a modellt, hogy ugyanazokat az adatokat tartalmazza, de különböző struktúrával.

### <a name="model-2"></a>A modell 2
Vegye figyelembe a fentiekhez alternatív modell:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ebben az esetben azt biztosan nem a **DECLARE\_STRUCT** makrók és egyszerűen meghatározásakor a adatelemek a mi esetünkben a modellezési nyelven történő egyszerű típus használatával.

Csak a pillanatra most figyelmen kívül hagyja a **idő** esemény. Az adott tartalékoljon, ez a kód érkező **hőmérséklet**:

```
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

Ez a kód a következő szerializált eseményt küld az IoT-központ:

```
{"Temperature":75}
```

És a páratartalom esemény küldése a kódját a következőképpen jelenik meg:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód küld a Ez az IoT-központ:

```
{"Humidity":45}
```

Amennyiben még vannak nem meglepetések számát. Most módosítsuk a SZERIALIZÁLÁSA makró felhasználási módját.

A **SZERIALIZÁLÁSA** makró több adat eseményeket, mint szerepkör argumentumokban vehet igénybe. Ez lehetővé teszi, hogy szerializálni a **hőmérséklet** és **páratartalom** esemény együtt, és küldje el az IoT-központ egy hívásban:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Előfordulhat, hogy kitalálja, hogy ez a kód eredménye, hogy két adatok események küldhetők az IoT-központ:

[

{"Hőmérséklet": 75},

{"Páratartalom": 45}

]

Ez azt jelenti, előfordulhat, hogy várt, hogy ez a kód megegyezik a küldő **hőmérséklet** és **páratartalom** külön-külön. Mindkét eseményeket továbbítani csupán a könnyebb **SZERIALIZÁLÁSA** az adott hívásban. Azonban, amely nem a helyzet. Ehelyett a fenti kódot az IoT hubhoz küld a egyetlen eseményt:

{"Hőmérséklet": 75, "páratartalom": 45}

Ez úgy látszhat furcsa, mert a modell meghatározása **hőmérséklet** és **páratartalom** , két *külön* események:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Több a pontra jelenleg nem a modell ezek az események hol **hőmérséklet** és **páratartalom** ugyanazon szerkezetben vannak:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Ez a modell esetén könnyebben érthetőek legyenek lenne hogyan **hőmérséklet** és **páratartalom** küldheti az adatokat az azonos szerializált üzenet. Azonban nem lehet egyértelmű ezért működik, hogy ha adja meg mindkét adatok események **SZERIALIZÁLÁSA** 2 modell használatával.

Ez a viselkedés könnyebben érthetőek legyenek, ha tudja, hogy a feltételek, amelyek a **szerializáló** könyvtár okozza. Az ezen lépjen vissza a modell célszerű:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ez a modell gondol objektumorientált feltételeit. Ebben az esetben azt még modellezési egy fizikai eszköz (termosztát), és eszközökön is tulajdonságai, például a **hőmérséklet** és **páratartalom**.

A modell a következő kóddal teljes állapotának kérhessen:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Feltéve, hogy az értékek hőmérséklet, páratartalom és az idő beállítása, azt például ez az IoT-központ küldött egy esemény jelenik meg:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Egyes esetekben előfordulhat, hogy csak szeretne küldeni *néhány* (Ez a különösen igaz, ha a modell tartalmaz számos adatok események) a felhőbe a modell tulajdonságai. Akkor célszerű küldendő adatok események csak egy részét, mint a korábbi példában:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Ezt követően pontosan azonos szerializált esemény, mintha meghatározott kellett egy **TemperatureEvent** rendelkező egy **hőmérséklet** és **idő** tag, hasonló módon az azt volt a modell 1. Ebben az esetben volt pontosan azonos szerializált esemény létrehozása egy másik modell (modell 2) használatával, mert hívtuk **SZERIALIZÁLÁSA** eltérő módon.

A legfontosabb, hogy ha több adat események át **SZERIALIZÁLÁSA,** és az azt feltételezi, hogy minden esemény egy adott JSON-objektum tulajdonság.

A legjobb módszer függ, és hogyan úgy gondolja, hogy a modell kapcsolatos. Ha az "események" küldjük a felhőhöz, és minden esemény egy adott csoportján tulajdonságait tartalmazza, az első módszer logika számos tesz. Ebben az esetben használhatja **DECLARE\_STRUCT** számára adja meg minden esemény szerkezetét, és azokat a modellben az a **WITH\_adatok** makró. Ezután küldenie minden esemény hasonlóan a fenti példában szereplő. Ez a módszer az lenne csak át egy egyetlen esemény **SZERIALIZÁLÓ**.

Ha úgy gondolja, hogy a modell csak objektumorientált módon kapcsolatos, a második megközelítés lehet, hogy megfelelően meg. Ebben az esetben az elemek megadott **WITH\_adatok** a "Tulajdonságok" az objektum. Bármilyen események részhalmazát át **SZERIALIZÁLÁSA** kínálatából, attól függően, hogy mennyi a "objektum" állapota szeretne küldeni a felhőbe.

Nether megoldás, a megfelelő vagy nem megfelelő. Csak vegye figyelembe, hogy a **szerializáló** könyvtár működik, és mentse a modellezési megközelítés, amely a leginkább megfelel az igényeinek.

## <a name="message-handling"></a>Üzenetkezelés
Ez a cikk, amennyiben rendelkezik csak az IoT hubhoz küldő események tárgyalt, és még nem címzett üzenetek fogadására. Az oka az okozza, hogy mit kell tudnia üzenetek fogadására van nagy mértékben volt szó egy [korábbi cikk](iot-hub-device-sdk-c-intro.md). A cikkben visszahívás, hogy Ön által feldolgozott üzenetek üzenet visszahívási függvény regisztrálásával:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Ezután ír a visszahívási függvény, amelyet egy üzenet fogadásakor:

```
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

Ez a megvalósítás a **IoTHubMessage** meghívja az adott függvény a modellben minden egyes művelethez. Ha például a modell határozza meg a műveletet:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Meg kell adnia a függvény a következő aláírást:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**SetAirResistance** majd nevezzük, amikor adott üzenetet küld az eszközre.

Mi azt még nem magyaráznak még az üzenet a szerializált verzió néz. Ez azt jelenti Ha elküldi egy **SetAirResistance** üzenet az eszközön, what does adott néz?

Ha egy üzenetet küldünk egy eszközre, tennénk az Azure IoT SDK szolgáltatáson keresztül. Továbbra is szeretné tudja, milyen karakterlánc küldeni egy adott művelet hívása. Üzenetküldés az általános formátum a következőképpen jelenik meg:

```
{"Name" : "", "Parameters" : "" }
```

Küldünk egy szerializált JSON-objektum két tulajdonságokkal: **neve** neve, a művelet (üzenet) és **paraméterek** intézkedés paramétereket tartalmaz.

Ahhoz például, hogy meghívása **SetAirResistance** ezt az üzenetet küld egy eszköz:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

A művelet nevének pontosan meg kell egyeznie a modellben definiált művelet. A paraméter nevét is meg kell egyeznie. Azt is vegye figyelembe a kis-és nagybetűk. **Név** és **paraméterek** a rendszer mindig a nagybetűk. Győződjön meg arról, hogy a művelet nevének és a paraméterek a modell nagybetűk. Ebben a példában a művelet neve: "SetAirResistance", és nem "setairresistance".

A két más műveletek **TurnFanOn** és **TurnFanOff** el ezeket az üzeneteket küld egy eszköz:

```
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Ebben a szakaszban leírt mindent, amire szüksége tudni, hogy mikor küldő események és fogadását az üzeneteket a **szerializáló** könyvtár. Mielőtt továbblép, most is beállíthat néhány paraméter, a modell mekkora szabályozó foglalkozik.

## <a name="macro-configuration"></a>A makróban konfiguráció
Használata esetén a **szerializáló** könyvtár az SDK tisztában lenni a fontos része megtalálható az azure-c-megosztott-segédprogram kódtára.
Ha az Azure iot-sdk--c tárház a Githubról--rekurzív funkcióval rendelkezik klónozták, majd megtalálja a megosztott segédprogram kódtára itt:

```
.\\c-utility
```

Ha a szalagtár nem rendelkezik klónozott, megtalálhatja [Itt](https://github.com/Azure/azure-c-shared-utility).

A megosztott segédeszközök könyvtárból talál a következő mappát:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ez a mappa tartalmaz, amely a Visual Studio megoldást **makró\_utils\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

Ebben a megoldásban a program létrehozza a **makró\_utils.h** fájlt. Nincs alapértelmezett makró\_utils.h fájl tartalmazza az SDK-val. Ez a megoldás lehetővé teszi egyes paraméterek módosítása, és hozza létre a következő paraméterek alapján fejlécfájlt.

A két fő paramétereit, és az érintett **nArithmetic** és **nMacroParameters** amely makró található két sort definiált\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Ezeket az értékeket az SDK részét képező alapértelmezett paraméterek. Minden paraméternek a következő jelentése:

* nMacroParameters – meghatározza, hány paraméterek egy DECLARE lehet\_MODELL makró definíciója.
* nArithmetic – meghatározza a modell engedélyezett tagok száma.

Ezek a paraméterek számára fontos oka, mivel azok vezérelni hogyan nagy is lehet a modellben. Vegyük példaként a modell definíciója:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Ahogy korábban említettük **DECLARE\_MODELL** csak egy C makró. A modell neve és a **WITH\_adatok** utasítás (még egy másik makró) paraméterei **DECLARE\_MODELL**. **nMacroParameters** határozza meg, hány paraméterek tartalmazhat **DECLARE\_MODELL**. Hatékonyan hogy hány adatok esemény és művelet nyilatkozatok, akkor is határozza meg. Mint ilyen az alapértelmezett határérték 124 Ez azt jelenti, hogy definiálhat egy modell készül 60 műveletek és adatok események kombinációjával. Ha túllépi ezt a határt, kapni fog ilyen fordítóprogram-hibákkal:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

A **nArithmetic** paraméter többet az az alkalmazás a makró nyelvi belső működését.  Azt szabályozza, hogy a modell lehet tagok száma beleértve **DECLARE_STRUCT** makrókat. Ha például a fordítóprogram-hibákkal jelenítse, akkor kell próbálja növelni **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Ha szeretné módosítani ezeket a paramétereket, módosítsa a makróban\_utils.tt fájlt, a makró újrafordítása\_utils\_h\_generator.sln megoldás, és futtassa a lefordított program. Ha így tesz, így új makró\_utils.h fájl jön létre, és elhelyezni a.\\ közös\\inc könyvtár.

Ahhoz, hogy az új verzió makró\_utils.h, távolítsa el a **szerializáló** NuGet-csomagot a megoldás, és helyette közé tartozik a **szerializáló** Visual Studio-projektet. Ez lehetővé teszi, hogy a kód fordítható legyen a forráskód a szerializáló szalagtár ellen. Ez magában foglalja a frissített makró\_utils.h. Ha azt szeretné, hogy erre a **simplesample\_amqp**, indítsa el a megoldás a szerializáló Library NuGet-csomag eltávolítása:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Majd adja hozzá a projektet a Visual Studio megoldás:

> .\\c\\serializer\\build\\windows\\serializer.vcxproj
> 
> 

Amikor elkészült, a megoldás így kell kinéznie:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Ha most a a megoldás, a frissített makró fordítási\_utils.h a bináris tartalmazza.

Vegye figyelembe, hogy ezeket az értékeket elég magas növelése lépheti túl a fordítóprogram korlátok. Erre a pontra a **nMacroParameters** a fő paraméter, amellyel érintett. A C99 spec határozza meg, hogy 127 paraméterek közül legalább egy makró definition engedélyezettek. A Microsoft fordítóprogram a spec pontosan követi (és maximális hossza 127), ezért nem lehet növelheti a **nMacroParameters** meghaladja az alapértelmezett. Más compilers – előfordulhat, hogy lehetővé teszi (például a GNU fordító magasabb határérték támogatja).

Amennyiben azt már szabályozott tudni, hogyan írhat kódot a szükséges összes a **szerializáló** könyvtár. Mielőtt áthaladva addig tart, most le újra az előző cikket, esetleg kíváncsi kapcsolatos témaköröket.

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k
Ez a cikk arra irányul mintaalkalmazás **simplesample\_amqp**. Ez a minta használja a magasabb szintű (a nem "r") API-k események küldhet és fogadhat üzeneteket. Ezen API-k használatakor a háttérszálon fut, amely gondoskodik az események üzenetek küldése és fogadása. Az alacsonyabb szintű (r) API-k használatával azonban a háttérszálon megszüntetéséhez, és tegye meg explicit szabályozhatják, amikor küldi az eseményeket, vagy a felhőbe érkező üzenetek fogadására.

Lásd: a [előző cikkben](iot-hub-device-sdk-c-iothubclient.md), nincs funkciók olyan készlete, amely a magasabb szintű API-k áll:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_megszüntetése

Az ezen API-k egy **simplesample\_amqp**.

Szerepel továbbá egy hasonló alacsonyabb szintű API-készlet.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_inden\_megszüntetése

Vegye figyelembe, hogy az alacsonyabb szintű API-k pontosan ugyanúgy működnek, a fenti cikkekben leírt módon. Az első API-készlet is használhatja, ha azt szeretné, hogy a háttérszálon eseményeket küldő és fogadó üzenetek kezelésére. Ha azt szeretné, hogy explicit szabályozhatják, amikor Ön adatokat küldeni és fogadni az IoT-központ használhatja a második API-készlet. Vagy API-k munkahelyi készlete egyaránt valamint a **szerializáló** könyvtár.

Az alacsonyabb szintű API-k használata például a **szerializáló** könyvtár, tekintse meg a **simplesample\_http** alkalmazás.

## <a name="additional-topics"></a>További témakörök
Érdemes megemlíteni néhány más témakörök újra szolgálnak tulajdonság kezelési, alternatív hitelesítő adatai és konfigurációs lehetőség használatával. Ezek a témakörök ismertetett egy [előző cikkben](iot-hub-device-sdk-c-iothubclient.md). A fő pont, hogy ezek a funkciók működik együtt a a **szerializáló** könyvtár, mint az a **IoTHubClient** könyvtárban. Például, ha szeretné tulajdonságok csatolása eseményhez a modellből, használhat **IoTHubMessage\_tulajdonságok** és **térkép**\_**AddorUpdate**, ugyanúgy, mint korábban leírt:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Hogy az esemény származik a **szerializáló** könyvtár segítségével manuálisan létrehozott vagy a **IoTHubClient** könyvtár nem számít.

A hitelesítő adatok másik eszköz használatával **IoTHubClient\_inden\_létrehozása** is működik, **IoTHubClient\_CreateFromConnectionString** a rendeljen egy **IOT HUBBAL\_ügyfél\_KEZELNI**.

Végezetül használata a **szerializáló** könyvtár, állíthatja be a konfigurációs beállítások **IoTHubClient\_inden\_SetOption** csak úgy, ahogy a használatakor**IoTHubClient** könyvtárban.

Egy szolgáltatás, amely egyedi a **szerializáló** könyvtárban az inicializálás API-k. A könyvtár használata előtt meg kell hívnia **szerializáló\_init**:

```
serializer_init(NULL);
```

Ez történik, mielőtt meghívja a **IoTHubClient\_CreateFromConnectionString**.

Hasonlóképpen, ha elkészült dolgozik a könyvtárban, az utolsó hívás, meg kell győződnie, hogy **szerializáló\_deinit**:

```
serializer_deinit();
```

Ellenkező esetben a többi a fent felsorolt funkciók azonos működik a **szerializáló** könyvtár mint a **IoTHubClient** könyvtárban. Az e témakörökkel kapcsolatos további információkért tekintse meg a [előző cikkben](iot-hub-device-sdk-c-iothubclient.md) a sorozat.

## <a name="next-steps"></a>További lépések
Ez a cikk ismerteti részletesen a egyedi szempontjait a **szerializáló** függvénytár szerepel a **C-hez készült SDK Azure IoT-eszközök**. Az információk adni érdeme alaposan megismernie az események üzeneteket küldjön és fogadjon az IoT-központ modellek használata.

Ezzel befejezte a három részből sorozat hogyan az alkalmazások fejlesztéséhez a **C-hez készült SDK Azure IoT-eszközök**. Ez legyen elegendő információt nem csak első lépésekhez, de Önnek az API-k működése alapos ismerete. További információkért nincsenek néhány mintákat az SDK nem vonatkozik. Ellenkező esetben a [SDK-dokumentáció](https://github.com/Azure/azure-iot-sdk-c) helyes erőforrás további információt.

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k][lnk-sdks].

Az IoT-központ képességeit további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
