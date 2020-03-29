---
title: Azure IoT-eszköz SDK C - Szerializáló | Microsoft dokumentumok
description: A Szerializáló-kódtár használata az Azure IoT-eszköz SDK C-hez szolgáltatásában az IoT-központtal kommunikáló eszközalkalmazások létrehozásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 09/06/2016
ms.author: robinsh
ms.openlocfilehash: dfea53e62383409411925f2fe2f18d61a6855ec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429371"
---
# <a name="azure-iot-device-sdk-for-c--more-about-serializer"></a>Azure IoT-eszköz SDK C-hez – további információk a szerializálásról

A sorozat első cikke bemutatta az [Azure IoT-eszköz C-hez való bevezetést.](iot-hub-device-sdk-c-intro.md) A következő cikk részletesebb leírást tartalmaz az [Azure IoT-eszköz C -- IoTHubClient](iot-hub-device-sdk-c-iothubclient.md). Ez a cikk az SDK lefedettségét a fennmaradó összetevő részletesebb leírásával egészíti ki: a **szerializáló** könyvtár.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A bevezető cikk ismerteti, hogyan kell használni a **szerializáló** könyvtár események küldésére és üzenetek fogadására az IoT Hub. Ebben a cikkben kiterjesztjük ezt a vitát azáltal, hogy teljesebb magyarázatot adunk arra, hogyan modellezheti az adatokat a **szerializáló** makrónyelvvel. A cikk további részleteket is tartalmaz arról, hogy a könyvtár hogyan szerializálja az üzeneteket (és bizonyos esetekben hogyan szabályozhatja a szerializálási viselkedést). Azt is leírja, néhány paramétert módosíthat, amelyek meghatározzák a méret a létrehozott modellek.

Végül, a cikk újra felkeresi a korábbi cikkekben tárgyalt témaköröket, például az üzenet- és vagyonkezelést. Amint azt meg tudjuk tudni, ezek a funkciók ugyanúgy működnek a **szerializáló** könyvtár használatával, mint az **IoTHubClient** könyvtár.

Minden ebben a cikkben leírt alapul **a szerializáló** SDK mintákat. Ha szeretné követni, tekintse meg az **egyszerűminta\_amqp** és **simplesample\_http** alkalmazások az Azure IoT-eszköz SDK C-hez.

Az Azure [**IoT-eszköz SDK-t**](https://github.com/Azure/azure-iot-sdk-c) a C GitHub-tárházhoz, és megtekintheti az API részleteit a [C API referencia.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-modeling-language"></a>A modellezési nyelv

Az [Azure IoT-eszköz SDK C-cikk](iot-hub-device-sdk-c-intro.md) ebben a sorozatban bevezette az **Azure IoT-eszköz SDK C** modellezési nyelv a példa az **\_egyszerűminta amqp** alkalmazás:

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

Amint láthatja, a modellezési nyelv C makrókon alapul. A definíciót mindig **\_begin névtérnel kezdi,** és mindig **end\_namespace**. Gyakori, hogy elnevezi a vállalat névterét, vagy ahogy ebben a példában, a projekten dolgozik.

Mi megy a névtérben a modell definíciók. Ebben az esetben egyetlen modell van az anemométerhez. Még egyszer, a modell nevezhető bármit, de általában a modell neve az eszköz vagy típusú adatok cseréje az IoT Hub.  

A modellek tartalmazzák az IoT Hubba (az *adatokat)* beérkező események, valamint az IoT Hubból (a műveletek) kapott üzenetek *definícióját.* Amint a példában látható, az eseményeknek típusa és neve van; a műveletek nek neve és választható paraméterei vannak (mindegyiknek van típusa).

Ebben a mintában nem bemutatott további adattípusok, amelyek et az SDK támogatja. Majd legközelebb fedezzük.

> [!NOTE]
> Az IoT Hub az eszköz által neki küldött adatokat *eseményként,* míg a modellezési nyelv *adatként* hivatkozik rá **(WITH_DATA**használatával definiálva). Hasonlóképpen az IoT Hub az eszközöknek küldött adatokat *üzenetként,* míg a modellezési nyelv *műveletekként* **(WITH_ACTION**használatával definiálva) hivatkozik rá. Ne feledje, hogy ezek a kifejezések szinonimaként használhatók ebben a cikkben.
> 
> 

## <a name="supported-data-types"></a>Támogatott adattípusok

A **szerializáló** tárral létrehozott modellek a következő adattípusokat támogatják:

| Típus | Leírás |
| --- | --- |
| double |dupla pontosságú lebegőpontos szám |
| int |32 bites egész szám |
| lebegőpontos |egyetlen precíziós lebegőpontos szám |
| long |hosszú egész szám |
| int8\_t |8 bites egész szám |
| int16\_t |16 bites egész szám |
| int32\_t |32 bites egész szám |
| int64\_t |64 bites egész szám |
| Bool |logikai |
| ascii\_\_char ptr |ASCII karakterlánc |
| EDM\_\_dátumidő-eltolása\_ |dátum időeltolása |
| EDM\_GUID |GUID |
| EDM\_BINÁRIS |binary |
| DECLARE\_STRUCT |összetett adattípus |

Kezdjük az utolsó adattípussal. A **\_DECLARE STRUCT** lehetővé teszi összetett adattípusok definiálását, amelyek más primitív típusok csoportosításai. Ezek a csoportok lehetővé teszik számunkra, hogy meghatározza a modell, amely így néz ki:

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

Modellünk egyetlen **TestType**típusú adateseményt tartalmaz. **A TestType** egy összetett típus, amely több tagot tartalmaz, amelyek együttesen mutatják be a **szerializáló** modellezési nyelv által támogatott primitív típusokat.

Egy ilyen modellel kódot írhatunk az IoT Hubra, amely a következőképpen jelenik meg:

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

Alapvetően a **tesztstruktúra** minden tagjához hozzárendelünk egy értéket, majd meghívjuk a **SendAsync-et,** hogy elküldjük a **Teszt** adatok eseményét a felhőbe. **A SendAsync** egy segítő függvény, amely egyetlen adateseményt küld az IoT Hubnak:

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

Ez a függvény szerializálja a megadott adateseményt, és elküldi azt az IoT Hubnak az **IoTHubClient\_SendEventAsync**használatával. Ez ugyanaz a kód tárgyalt korábbi cikkekben (**SendAsync** magában foglalja a logika egy kényelmes funkció).

Az előző kódban használt másik segítő függvény a **GetDateTimeOffset**. Ez a függvény a megadott időt **EDM\_\_DATE\_TIME OFFSET**típusú értékké alakítja át:

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

Ha futtatja ezt a kódot, a következő üzenetet küldi az IoT Hub:

```C
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Vegye figyelembe, hogy a szerializálás json, amely a **szerializáló** könyvtár által létrehozott formátumban. Azt is vegye figyelembe, hogy a szerializált JSON-objektum minden tagja megegyezik a modellben definiált **TestType** tagjaival. Az értékek pontosan megegyeznek a kódban használt értékekkel is. Ne feledje azonban, hogy a bináris adatok base64-kódolásúak: az "AQID" a(z) {0x01, 0x02, 0x03} base64 kódolása.

Ez a példa bemutatja a **szerializáló** könyvtár használatának előnyeit – lehetővé teszi számunkra, hogy JSON-t küldjünk a felhőbe anélkül, hogy kifejezetten foglalkoznunk kellene a szerializálással az alkalmazásunkban. Mindössze annyit kell aggódnunk, hogy beállítjuk a modellben lévő adatesemények értékeit, majd egyszerű API-kat hívunk meg, hogy ezeket az eseményeket a felhőbe küldjük.

Ezzel az információval olyan modelleket határozhatunk meg, amelyek tartalmazzák a támogatott adattípusok tartományát, beleértve az összetett típusokat is (akár összetett típusokat is beilleszthetünk más összetett típusokba). Azonban a szerializált JSON által generált fenti példa hoz létre egy fontos pontot. *A* **szerializáló** könyvtárral való adatküldés pontosan meghatározza a JSON kialakításának módját. Ez az a pont, amit majd a következő.

## <a name="more-about-serialization"></a>További információk a szerializálásról

Az előző szakasz kiemeli a **szerializáló** könyvtár által létrehozott kimenet egy példát. Ebben a szakaszban bemutatjuk, hogyan szerializálja a könyvtár az adatokat, és hogyan szabályozhatja ezt a viselkedést a szerializálási API-k használatával.

Annak érdekében, hogy előre a vitát a szerializáció, fogunk dolgozni egy új modell alapján a termosztát. Először is, adjunk némi háttér a forgatókönyvpróbálunk foglalkozni.

Azt akarjuk, hogy modell ez a termosztát, amely méri a hőmérséklet és a páratartalom. Minden egyes adat más-más módon lesz elküldve az IoT Hubnak. Alapértelmezés szerint a termosztát 2 percenként egyszer beérkezik a hőmérsékleti eseménybe; 15 percenként egyszer bekell-e frissül esnem. Ha bármelyik esemény be van erősítve, tartalmaznia kell egy időbélyeget, amely megmutatja a megfelelő hőmérséklet vagy páratartalom mérésének idejét.

Ebben a forgatókönyvben bemutatjuk az adatok modellezésének két különböző módját, és elmagyarázzuk, hogy a modellezés milyen hatással van a szerializált kimenetre.

### <a name="model-1"></a>1. minta

A modell első verziója, amely támogatja az előző forgatókönyvet:

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

Vegye figyelembe, hogy a modell két adateseményt tartalmaz: **Hőmérséklet** és **páratartalom**. A korábbi példákkal ellentétben az egyes események típusa declare **\_STRUCT**használatával definiált struktúra. **TemperatureEvent** tartalmaz egy hőmérséklet-mérést és egy időbélyeget; **A HumidityEvent** páratartalom-mérést és időbélyeget tartalmaz. Ez a modell természetes módot ad arra, hogy modellezzük a fent leírt forgatókönyv adatait. Amikor egy eseményt küldünk a felhőbe, vagy hőmérsékletet/időbélyeget vagy páratartalom/időbélyeg párt küldünk.

Hőmérsékleti eseményt küldhetünk a felhőbe a következő kód dal:

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

A mintakódban a hőmérséklet és a páratartalom szigorú értékeit használjuk, de képzeljük el, hogy ezeket az értékeket a termosztát megfelelő érzékelőinek mintavételezésével használjuk be.

A fenti kód a korábban bevezetett **GetDateTimeOffset** segítőt használja. Később világossá vált okok miatt ez a kód kifejezetten elválasztja az esemény szerializálásának és elküldésének feladatát. Az előző kód a hőmérsékleti eseményt pufferbe szerializálja. Ezután **sendMessage** egy segítő függvény (szerepel **simplesample\_amqp),** amely elküldi az eseményt az IoT Hub:

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

Ez a kód az előző szakaszban ismertetett **SendAsync** segítő egy részhalmaza, így itt nem fogjuk újra átmenni.

Amikor futtatjuk az előző kódot a Hőmérséklet esemény elküldéséhez, az esemény szerializált űrlapja az IoT Hubnak kerül elküldésre:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Küldünk egy hőmérsékletet, amely típusú **TemperatureEvent**, és hogy struct tartalmaz **egy hőmérséklet** és **idő** tag. Ez közvetlenül tükröződik a szerializált adatokban.

Hasonlóképpen, küldhetünk egy páratartalom esemény ezzel a kóddal:

```C
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Az IoT Hubnak küldött szerializált űrlap a következőképpen jelenik meg:

```C
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Ismét, ez a várt módon.

Ezzel a modellel el tudja képzelni, hogy további események könnyen hozzáadhatók. További struktúrákat definiál a **\_DECLARE STRUCT**használatával, és a megfelelő eseményt a modellben az WITH **\_DATA**használatával adhatja meg.

Most módosítsa a modellt úgy, hogy ugyanazokat az adatokat tartalmazza, de más struktúrával.

### <a name="model-2"></a>2. minta

Tekintsük ezt az alternatív modellt a fentihez képest:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Ebben az esetben megszüntettük a **\_DECLARE STRUCT** makrókat, és egyszerűen meghatározzuk a forgatókönyvünkből származó adatelemeket egyszerű típusok használatával a modellezési nyelvből.

Csak egy pillanatra, figyelmen kívül hagyja az **Idő** eseményt. Ezzel félretéve, itt a kód, hogy befelé **hőmérséklet:**

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

Ez a kód a következő szerializált eseményt küldi az IoT Hubnak:

```C
{"Temperature":75}
```

És a páratartalom esemény küldésének kódja a következőképpen jelenik meg:

```C
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Ez a kód elküldi ezt az IoT Hubnak:

```C
{"Humidity":45}
```

Eddig még mindig nincsenek meglepetések. Most változtassuk meg, hogyan használjuk a SZERIALIZÁLÁS makrót.

A **SZERIALIZÁLÁS** makró több adateseményt is argumentumként vehet fel. Ez lehetővé teszi számunkra, hogy szerializáljuk a **hőmérséklet** és **a páratartalom** eseményt együtt, és egyetlen hívással elküldjük őket az IoT Hubra:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Azt gondolhatja, hogy a kód eredménye az, hogy két adateseményt küld az IoT Hubnak:

[ {"Hőmérséklet":75}, {"Páratartalom":45} ]

Más szóval, akkor számíthat arra, hogy ez a kód ugyanaz, mint a **hőmérséklet** és **a páratartalom** külön-külön. Ez csak egy kényelmes átadni mindkét esemény **szerializálása** ugyanabban a hívásban. Azonban nem ez a helyzet. Ehelyett a fenti kód elküldi ezt az egyetlen adateseményt az IoT Hubnak:

{"Hőmérséklet":75, "Páratartalom":45}

Ez furcsának tűnhet, mert a modell határozza meg **hőmérséklet** és **páratartalom,** mint két *külön* esemény:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Inkább arra a pontra, mi nem modell ezeket az eseményeket, ahol **a hőmérséklet** és **a páratartalom** ugyanabban a struktúrában:

```C
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Ha ezt a modellt használjuk, könnyebb lenne megérteni, hogy **a hőmérséklet** és **a páratartalom** hogyan lenne elküldve ugyanabban a szerializált üzenetben. Azonban nem lehet egyértelmű, hogy miért működik így, ha adja át mindkét adatesemények **szerializálása** a modell használatával 2.

Ez a viselkedés könnyebben érthető, ha ismeri a feltételezéseket, hogy a **szerializáló** könyvtár teszi. Hogy értelmet adjunk ennek, térjünk vissza a modellünkhöz:

```C
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Gondoljon erre a modellre objektumorientált anamtikában. Ebben az esetben egy fizikai eszközt (termosztátot) modellezünk, és az eszköz olyan attribútumokat tartalmaz, mint **a hőmérséklet** és **a páratartalom**.

A modell teljes állapotát elküldhetjük a következő kódokkal:

```C
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Feltételezve, hogy a hőmérséklet, a páratartalom és az idő értékei be vannak állítva, egy ilyen eseményt láthatunk az IoT Hubnak:

```C
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Előfordulhat, hogy csak a modell *bizonyos* tulajdonságait szeretné a felhőbe küldeni (ez különösen igaz, ha a modell nagy számú adateseményt tartalmaz). Hasznos, ha az adateseményeknek csak egy részhalmazát küldi el, például a korábbi példában:

```C
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Ez pontosan ugyanazt a szerializált eseményt hozza létre, mintha egy **TemperatureEvent-et** határoztunk volna meg **hőmérséklet** és **idő** taggal, ugyanúgy, mint az 1-es modellnél. Ebben az esetben pontosan ugyanazt a szerializált eseményt tudtuk létrehozni egy másik modell (2. modell) használatával, mert más módon hívtuk **a SZERIALIZÁLÁSt.**

A lényeg az, hogy ha több adateseményt ad át **a SZERIALIZÁLÁSnak,** akkor azt feltételezi, hogy minden esemény egy tulajdonság egyetlen JSON-objektumban.

A legjobb megközelítés öntől függ, és hogyan gondolkodik a modellről. Ha "eseményeket" küld a felhőbe, és minden esemény meghatározott tulajdonságokat tartalmaz, akkor az első megközelítés sok értelmet nyer. Ebben az esetben a **DECLARE\_STRUCT** használatával határozza meg az egyes események szerkezetét, majd a MODELLbe is belefoglalja őket a **WITH\_DATA** makróba. Ezután minden eseményt úgy küld, ahogy a fenti első példában tettük. Ebben a megközelítésben csak egyetlen adateseményt ad át **a SZERIALIZÁLÓnak.**

Ha objektumorientált módon gondolkodik a modellről, akkor a második megközelítés megfelelhet Önnek. Ebben az esetben a **WITH\_DATA** használatával definiált elemek az objektum "tulajdonságai". Az események bármilyen részhalmazát **átadhatja a szerializáláshoz,** attól függően, hogy az "objektum" állapotból mennyit szeretne küldeni a felhőbe.

Az alvilági megközelítés jó vagy rossz. Csak legyen tudatában annak, hogyan működik a **szerializáló** könyvtár, és válassza ki a modellezési megközelítés, amely a legjobban megfelel az Ön igényeinek.

## <a name="message-handling"></a>Üzenetkezelés

Ez a cikk eddig csak az IoT Hubra küldött eseményeket tárgyalta meg, és nem foglalkozott a fogadó üzenetekkel. Ennek az az oka, hogy amit az üzenetek fogadásáról tudnunk kell, nagyrészt az [Azure IoT-eszköz C-hez való sdk cikkében](iot-hub-device-sdk-c-intro.md)szerepel. Visszahívás abból a cikkből, amelyet üzenetvisszahívási funkció regisztrálásával dolgoz fel:

```C
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Ezután írja be a visszahívási függvényt, amely et egy üzenet érkezésekor hív meg:

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

Az **IoTHubMessage** ez a megvalósítása meghívja a modell minden egyes műveletének adott függvényét. Ha például a modell határozza meg ezt a műveletet:

```C
WITH_ACTION(SetAirResistance, int, Position)
```

Ezzel az aláírással kell definiálnia egy függvényt:

```C
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

**A SetAirResistance** ekkor jelenik meg, amikor az üzenetet elküldi a készülékre.

Amit még nem magyarázta meg, hogy néz ki az üzenet szerializált verziója. Más szóval, ha **setairresistance** üzenetet szeretne küldeni a készüléknek, hogy néz ki?

Ha üzenetet küld egy eszközre, ezt az Azure IoT szolgáltatás SDK-n keresztül kell megtennie. Továbbra is tudnia kell, hogy milyen karakterláncot kell küldenie egy adott művelet meghívásához. Az üzenet küldésének általános formátuma a következőképpen jelenik meg:

```C
{"Name" : "", "Parameters" : "" }
```

Egy szerializált JSON-objektumot küld két tulajdonsággal: **A név** a művelet (üzenet) neve, a **Paraméterek pedig** a művelet paramétereit.

Például a **SetAirResistance** meghívásához ezt az üzenetet elküldheti egy eszközre:

```C
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

A művelet nevének pontosan meg kell egyeznie a modellben definiált művelettel. A paraméterneveknek is egyezniük kell. Vegye figyelembe a kis- és nagybetűk érzékenységét is. **A név** és **a paraméterek** mindig nagybetűsek. Győződjön meg arról, hogy egyezik az esetben a művelet nevét és paramétereit a modellben. Ebben a példában a művelet neve "SetAirResistance" és nem "setairresistance".

A két másik művelet **TurnFanOn** és **TurnFanOff** lehet hivatkozni küldött ezeket az üzeneteket egy eszközre:

```C
{"Name" : "TurnFanOn", "Parameters" : {}}
{"Name" : "TurnFanOff", "Parameters" : {}}
```

Ez a szakasz mindent leírt, amit tudnia kell, amikor eseményeket küld és üzeneteket fogad a **szerializáló** könyvtárral. Mielőtt továbblépne, fedjük le azokat a paramétereket, amelyek et beállíthatja, hogy mekkora a modell.

## <a name="macro-configuration"></a>Makró konfigurációja

Ha a **Szerializáló** könyvtár at használja az SDK fontos része, hogy tudatában legyen az azure-c-shared-utility library található.

Ha klónozta az Azure-iot-sdk-c tárházat a `git submodule update --init` GitHubról, és kiadta a parancsot, akkor ezt a megosztott segédprogram-könyvtárat itt találja:

```C
.\\c-utility
```

Ha még nem klónozta a könyvtárat, [itt](https://github.com/Azure/azure-c-shared-utility)megtalálja.

A megosztott segédprogram-könyvtárban a következő mappa található:

```C
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Ez a mappa tartalmaz egy Visual Studio megoldás nevű **makró\_utils\_h\_generator.sln**:

  ![Képernyőkép a Visual Studio-megoldás maco_utils_h_generator](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.png)

A megoldásban lévő program létrehozza a **makró\_utils.h** fájlt. Az SDK-ban található egy alapértelmezett makró\_utils.h fájl. Ez a megoldás lehetővé teszi bizonyos paraméterek módosítását, majd a fejlécfájl újbóli létrehozását ezen paraméterek alapján.

A két fő paraméter, hogy az érintett a **nArithmetikai** és **nMacroParameters**,\_amelyek meghatározása a két sor található makró utils.tt:

```C
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>
```

Ezek az értékek az SDK-ban található alapértelmezett paraméterek. Minden paraméter jelentése a következő:

* nMacroParameters – Azt határozza meg, hogy\_hány paraméterrel rendelkezhet egy DECLARE MODEL makródefinícióban.
* nArithmetikus – A modellben engedélyezett tagok teljes számát szabályozza.

Ezek a paraméterek azért fontosak, mert szabályozzák, hogy mekkora lehet a modell. Vegyük például ezt a modelldefiníciót:

```C
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Mint korábban említettük, **declare\_modell** csak egy C makró. A modell neve és a **WITH\_DATA** utasítás (egy másik makró) a DECLARE **\_MODEL**paraméterei. **nMacroParameters** határozza meg, hogy hány paraméter szerepelhet a **\_DECLARE MODEL**. Ez gyakorlatilag azt határozza meg, hogy hány adatesemény- és műveletdeklarációja lehet. Így az alapértelmezett 124-es korláttal ez azt jelenti, hogy körülbelül 60 művelet és adatesemény kombinációjával definiálhat egy modellt. Ha megpróbálja túllépni ezt a korlátot, az ehhez hasonló fordítási hibákat fog kapni:

  ![Képernyőkép a makróparaméterek fordítási hibáiról](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.png)

A **nArithmetikus** paraméter inkább a makrónyelv belső működéséről szól, mint az alkalmazásról.  Azt szabályozza, hogy a modellben hány tag lehet, beleértve **a DECLARE_STRUCT** makrókat is. Ha elkezdi látni fordító hibák, mint ez, akkor meg kell próbálnia növekvő **nArithmetic:**

   ![Az aritmetikai fordítóhibáinak képernyőképe](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.png)

Ha módosítani szeretnénk ezeket a paramétereket,\_módosítsuk a\_makró utils.tt\_\_fájljának értékeit, fordítsuk újra a makrót h generator.sln megoldással, és futtassuk a lefordított programot. Ha így tesz, a\_rendszer új makróutils.h fájlt hoz létre, és a rendszer a bekerül. \\common\\inc könyvtárba.

A makró\_utils.h új verziójának használatához távolítsa el a **NuGet szerializáló** csomagot a megoldásból, és a helyére tartozik a Visual Studio **sorozatosító** projekt. Ez lehetővé teszi, hogy a kód fordítása a szerializáló könyvtár forráskódjával szemben. Ez magában foglalja\_a frissített makró utils.h. Ha ezt **egyszerűminta\_amqp**esetén szeretné elvégezni, először távolítsa el a szerializáló könyvtár NuGet csomagját a megoldásból:

   ![Képernyőkép a szerializáló könyvtár NuGet csomagjának eltávolításáról](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.png)

Ezután adja hozzá ezt a projektet a Visual Studio-megoldáshoz:

> . \\c\\szerializáló\\épít\\windows\\serializer.vcxproj
> 
> 

Ha elkészült, a megoldás nak így kell kinéznie:

   ![Képernyőkép a visual studio simplesample_amqp megoldásáról](media/iot-hub-device-sdk-c-serializer/05-serializer-project.png)

Most, amikor lefordítani a\_megoldást, a frissített makró utils.h szerepel a bináris.

Ne feledje, hogy ezeknek az értékeknek a elég magasra emelése meghaladhatja a fordítói határértékeket. Ezen a ponton az **nMacroParameters** a fő paraméter, amellyel foglalkozni kell. A C99 specifikáció azt határozza meg, hogy legalább 127 paraméter engedélyezett egy makródefinícióban. A Microsoft fordító pontosan követi a specifikációt (és 127-es korláttal rendelkezik), így nem tudja növelni az **nMacroParameters értékeket** az alapértelmezettnél túl. Más fordítók is lehetővé tehetik ezt (például a GNU fordító támogatja a magasabb korlátot).

Eddig már lefedett csak arról szól, mindent meg kell tudni, hogyan kell írni kódot a **szerializáló** könyvtár. Lezárása előtt, nézzük újra néhány témát a korábbi cikkeket, hogy lehet, hogy kíváncsi.

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k
A minta alkalmazás, amelyre ez a cikk összpontosított **egyszerűminta\_amqp**. Ez a minta a magasabb szintű (a nem**LL)** API-kat használja az események küldéséhez és az üzenetek fogadásához. Ha ezeket az API-kat használja, egy háttérszál fut, amely gondoskodik az események küldéséről és az üzenetek fogadásáról. Az alacsonyabb szintű (LL) API-k segítségével azonban megszüntetheti ezt a háttérszálat, és explicit módon szabályozhatja, hogy mikor küld eseményeket vagy fogad üzeneteket a felhőből.

Az előző [cikkben](iot-hub-device-sdk-c-iothubclient.md)leírtak szerint a magasabb szintű API-kból álló függvények készlete:

* IoTHubClient\_CreateFromConnectionString
* IoTHubClient\_SendEventAsync
* IoTHubClient\_SetMessageCallback
* IoTHubClient\_megsemmisítés

Ezeket az API-kat **egyszerűminta\_amqp**mutatja be.

Van is egy hasonló sor alacsonyabb szintű API-k.

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL megsemmisítés

Vegye figyelembe, hogy az alacsonyabb szintű API-k pontosan ugyanúgy működnek, mint az előző cikkekben leírtak. Az API-k első készletét akkor használhatja, ha azt szeretné, hogy egy háttérszál kezelje az események küldését és az üzenetek fogadását. Az API-k második készletét használja, ha explicit módon szeretné szabályozni az IoT Hubból történő adatok küldésének és fogadásának egyenlítését. Az API-k bármelyike egyformán jól működik a **szerializáló** könyvtárral.

Egy példa, hogy az alacsonyabb szintű API-k a **szerializáló** könyvtár, tekintse meg a **\_simplesample http** alkalmazás.

## <a name="additional-topics"></a>További témakörök
Néhány további említésre méltó témakör a tulajdonságkezelés, az alternatív eszközhitelesítő adatok használata és a konfigurációs beállítások. Ezek mind olyan témák, amelyek et egy [korábbi cikkben tárgyaltak.](iot-hub-device-sdk-c-iothubclient.md) A lényeg az, hogy ezek a funkciók ugyanúgy működnek a **szerializáló** könyvtár, mint az **IoTHubClient** könyvtár. Ha például tulajdonságokat szeretne csatolni egy modellből származó eseményhez, az **IoTHubMessage\_tulajdonságokat** és a **Map**\_**AddorUpdate-ot**ugyanúgy használja, mint korábban:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Nem számít, hogy az eseményt a **szerializáló** könyvtárból vagy manuálisan az **IoTHubClient-könyvtár** használatával hozták-e létre.

Az alternatív eszköz hitelesítő adataihoz az **IoTHubClient\_LL\_Create** ugyanúgy működik, mint az **IoTHubClient\_CreateFromConnectionString** az **IOTHUB\_ÜGYFÉLHANDLE\_** lefoglalásához.

Végül, ha a **szerializáló** könyvtárat használja, az **IoTHubClient\_\_LL SetOption** beállítási beállításait ugyanúgy megadhatja, mint az **IoTHubClient könyvtár** használatakor.

A **szerializáló** könyvtár egyedi szolgáltatása az inicializálási API-k. Mielőtt elkezdené a könyvtárral való munkát, meg kell **hívnia a\_szerializáló init:**

```C
serializer_init(NULL);
```

Ez közvetlenül az **IoTHubClient\_CreateFromConnectionString**hívása előtt történik.

Hasonlóképpen, ha végzett a könyvtárral, az utolsó hívás, amit csinál, a **szerializáló\_deinit:**

```C
serializer_deinit();
```

Ellenkező esetben a fent felsorolt összes többi szolgáltatás ugyanúgy működik a **szerializáló** könyvtárban, mint az **IoTHubClient** könyvtárban. A témakörök bármelyikéről a sorozat [előző cikkében](iot-hub-device-sdk-c-iothubclient.md) olvashat bővebben.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti az **Azure IoT-eszköz C-hez sdk-ban**található **szerializáló** könyvtár egyedi szempontjait. A megadott információk kal kell egy jó megértése, hogyan használhatja modellek események küldése és üzenetek fogadása az IoT Hub.

Ezzel arra is következtet, hogy miként fejleszthet alkalmazásokat az **Azure IoT-eszköz C-hez sdk-jével.** Ennek elegendő információnak kell lennie ahhoz, hogy ne csak a kezdéshez, hanem az API-k működésének alapos megértéséhez is. További információkért van néhány minta az SDK-ban, amely nem tartozik ide. Ellenkező esetben az [Azure IoT SDK dokumentáció](https://github.com/Azure/azure-iot-sdk-c) egy jó forrás további információkért.

Ha többet szeretne megtudni az IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK-k című témakört.](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további [megismeréséhez tekintse meg az AI üzembe helyezése az Azure IoT Edge-eszközök höz című témakört.](../iot-edge/tutorial-simulate-device-linux.md)