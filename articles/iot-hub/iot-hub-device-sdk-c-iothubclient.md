---
title: Azure IoT-eszköz SDK C - IoTHubClient | Microsoft dokumentumok
description: Az IoTHubClient-kódtár használata az Azure IoT-eszköz SDK For C-ben az IoT-központtal kommunikáló eszközalkalmazások létrehozásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732603"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT-eszköz SDK C-hez – további információk az IoTHubClient-ről

[Az Azure IoT-eszköz SDK For C](iot-hub-device-sdk-c-intro.md) az első cikk ebben a sorozatban, amely bemutatja az **Azure IoT-eszköz SDK For C.** Ez a cikk kifejtette, hogy két architekturális réteg van az SDK-ban. A bázison található az **IoTHubClient** könyvtár, amely közvetlenül kezeli az IoT Hubbal való kommunikációt. Ott van még a **szerializáló** könyvtár, amely arra épül, hogy a szerializálási szolgáltatások. Ebben a cikkben további részleteket az **IoTHubClient könyvtár.**

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az előző cikk ismerteti, hogyan használhatja az **IoTHubClient** könyvtárat események küldésére az IoT Hubba és üzenetek fogadására. Ez a cikk kiterjeszti ezt a vitát azáltal, hogy elmagyarázza, hogyan *lehet* pontosabban kezelni az adatok küldése és fogadása során, bemutatva az alacsonyabb **szintű API-kat.** Azt is bemutatjuk, hogyan csatolhat tulajdonságokat eseményekhez (és lekérheti őket az üzenetekből) az **IoTHubClient** könyvtár tulajdonságkezelési funkcióinak használatával. Végül további magyarázatot adunk az IoT Hubtól kapott üzenetek kezelésének különböző módjairól.

A cikk néhány egyéb témakör lefedésével zárul, többek között az eszköz hitelesítő adatairól és az **IoTHubClient** viselkedésének a konfigurációs beállításokon keresztül történő módosításáról.

Ezeket a témaköröket az **IoTHubClient** SDK-mintákkal fogjuk elmagyarázni. Ha szeretné követni, tekintse meg az **\_iothub-ügyfél\_\_minta http** és **iothub-ügyfél\_\_minta\_amqp-alkalmazások,** amelyek szerepelnek az Azure IoT-eszköz SDK C. A következő szakaszokban leírt minden ezekben a mintákban látható.

Az Azure [**IoT-eszköz SDK-t**](https://github.com/Azure/azure-iot-sdk-c) a C GitHub-tárházhoz, és megtekintheti az API részleteit a [C API referencia.](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k

Az előző cikk az **IotHubClient** alapvető működését ismerteti az **iothub-ügyfélminta\_\_\_amqp-alkalmazás** környezetében. Például elmagyarázta, hogyan inicializálható a tár ezzel a kóddal.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Azt is leírta, hogyan küldhet eseményeket ezzel a függvényhívással.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

A cikk azt is leírta, hogyan fogadhat üzeneteket egy visszahívási függvény regisztrálásával.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

A cikk azt is megmutatta, hogyan szabadítson fel erőforrásokat a következő kód használatával.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Az API-k mindegyikének társfunkciói vannak:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL megsemmisítés

Ezek a függvények tartalmazzák az **LL-t** az API-névben. A név **LL** részében az egyes függvények paraméterei megegyeznek nem LL-megfelelőikkel. Azonban ezeknek a függvényeknek a viselkedése egy fontos módon különbözik.

Az **IoTHubClient\_CreateFromConnectionString**hívásakor az alapul szolgáló függvénytárak egy új szálat hoznak létre, amely a háttérben fut. Ez a szál eseményeket küld, és üzeneteket fogad az IoT Hubtól. Az **LL** API-kkal végzett munka során nem jön létre ilyen szál. Létrehozása a háttér téma egy kényelmes, hogy a fejlesztő. Nem kell aggódnia, hogy kifejezetten eseményeket küld és üzeneteket fogad az IoT Hubról – ez automatikusan megtörténik a háttérben. Ezzel szemben az **LL** API-k explicit vezérlést biztosítanak az IoT Hubbal való kommunikáció felett, ha szüksége van rá.

Ahhoz, hogy jobban megértsük ezt a fogalmat, nézzünk meg egy példát:

Az **IoTHubClient\_SendEventAsync hívásakor**valójában pufferbe helyezi az eseményt. Az **IoTHubClient\_CreateFromConnectionString** hívásakor létrehozott háttérszál folyamatosan figyeli ezt a puffert, és elküldi a benne lévő adatokat az IoT Hubnak. Ez történik a háttérben, ugyanakkor, hogy a fő szál más munkát végez.

Hasonlóképpen, amikor regisztrál egy visszahívási függvényt az **IoTHubClient\_SetMessageCallback**használatával üzenetekhez, utasítja az SDK-t, hogy a háttérszál hívja meg a visszahívási függvényt, amikor egy üzenet érkezik, a fő száltól függetlenül.

Az **LL** API-k nem hoznak létre háttérszálat. Ehelyett egy új API-t kell meghívni az IoT Hubtól származó adatok explicit küldéséhez és fogadásához. Ezt a következő példa szemlélteti.

Az SDK-ban található **\_iothub-ügyfélminta\_\_http-alkalmazás** bemutatja az alacsonyabb szintű API-kat. Ebben a példában eseményeket küldünk az IoT Hubba a következő kóddal:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Az első három sor hozza létre az üzenetet, és az utolsó sor elküldi az eseményt. Azonban, mint korábban említettük, az esemény küldése azt jelenti, hogy az adatok egyszerűen egy pufferbe kerülnek. Az **IoTHubClient\_\_LL SendEventAsync**hívásakor semmi sem lesz továbbítva a hálózaton. Az Adatok IoT Hubba való tényleges beküldéséhez meg kell hívnia az **IoTHubClient\_\_LL DoWork-et,** ahogy ebben a példában:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód (az **iothub-ügyfélminta\_\_\_http** alkalmazásból) ismételten meghívja az **IoTHubClient\_LL\_DoWork-et.** Minden alkalommal, amikor **IoTHubClient\_\_LL DoWork** hívják, küld néhány eseményt a pufferből az IoT Hub, és beolvassa a várólistás üzenetet küldött az eszközre. Az utóbbi eset azt jelenti, hogy ha regisztráltunk egy visszahívási függvényt az üzenetekhez, akkor a visszahívás meghívásra kerül (feltéve, hogy az üzenetek várólistára kerülnek). Egy ilyen visszahívási funkciót regisztráltunk volna a következő kóddal:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Az **IoTHubClient\_LL\_DoWork** gyakran hívják egy hurok, hogy minden egyes alkalommal, amikor a neve, küld *néhány* pufferelt események et Az IoT Hub és beolvassa *a következő* üzenet várólistára az eszköz számára. Nem garantált minden hívás az összes pufferelt esemény elküldése vagy az összes várólistás üzenet beolvasása. Ha az összes eseményt a pufferbe szeretné küldeni, majd folytatni szeretné a többi feldolgozással, akkor ezt a hurkot lecserélheti a következő kódra:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód meghívja **az IoTHubClient\_\_LL DoWork-t,** amíg a pufferben lévő összes esemény el nem lett küldve az IoT Hubnak. Ne feledje, hogy ez nem jelenti azt is, hogy az összes várólistára helyezett üzenet érkezett. Ennek részben az az oka, hogy az "összes" üzenet ellenőrzése nem olyan determinisztikus művelet. Mi történik, ha letölti az üzenetek "összes" részét, de aztán egy másikat azonnal elküld a készülékre? Egy jobb módja annak, hogy kezelni, hogy van egy programozott időout. Például az üzenet visszahívási funkció alaphelyzetbe állíthatja az időzítőt minden alkalommal, amikor meghívják. Ezután a feldolgozás folytatásához logikát írhat, ha például nem érkezett üzenet az elmúlt *X* másodpercben.

Amikor befejezte az események betöltését és az üzenetek fogadását, mindenképpen hívja meg a megfelelő függvényt az erőforrások törléséhez.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Alapvetően csak egy API-készlet van, amelyet egy háttérszálas és egy másik API-készlettel szeretne küldeni és fogadni, ami ugyanazt a dolgot végzi a háttérszál nélkül. Sok fejlesztő előnyben részesítheti a nem LL API-kat, de az alacsonyabb szintű API-k akkor hasznosak, ha a fejlesztő kifejezett encikbe szeretne fonni a hálózati átvitelek felett. Egyes eszközök például idővel adatokat gyűjtenek, és csak meghatározott időközönként (például óránként vagy naponta egyszer) gyűjtik az eseményeket. Az alacsonyabb szintű API-k lehetővé teszik, hogy explicit módon szabályozhassa az IoT Hubról történő adatok küldésének és fogadásának lehetőségét. Mások egyszerűen inkább az egyszerűség, hogy az alacsonyabb szintű API-k nyújtanak. Minden történik a fő téma, nem pedig néhány munka történik a háttérben.

Bármelyik modellt is választja, győződjön meg róla, hogy konzisztens, amelyben API-kat használ. Ha az **IoTHubClient\_LL\_CreateFromConnectionString**hívásával kezdi, győződjön meg arról, hogy csak a megfelelő alacsonyabb szintű API-kat használja a követési munkákhoz:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_\_LL megsemmisítés
* IoTHubClient\_LL\_DoWork

Ennek az ellenkezője is igaz. Ha az **IoTHubClient\_CreateFromConnectionString**karakterláncmal kezdi, akkor a nem LL API-kat használja a további feldolgozáshoz.

Az Azure IoT-eszköz SDK C-hez tekintse meg az **iothub-ügyfél\_\_\_minta http-alkalmazás** egy teljes példa az alacsonyabb szintű API-k. Az **iothub-ügyfél\_minta\_\_amqp** alkalmazás hivatkozhat a nem LL API-k teljes példájára.

## <a name="property-handling"></a>Ingatlankezelés

Eddig, amikor leírtuk az adatok küldését, az üzenet törzsére utaltunk. Vegyük például ezt a kódot:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Ez a példa egy üzenetet küld az IoT Hubnak a "Hello World" szöveggel. Azonban az IoT Hub is lehetővé teszi, hogy a tulajdonságok at kell csatolni az egyes üzenetekhez. A tulajdonságok olyan név-/értékpárok, amelyek az üzenethez csatolhatók. Módosíthatjuk például az előző kódot, hogy tulajdonságot csatolhassunk az üzenethez:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Először hívjuk meg **az IoTHubMessage\_tulajdonságokat,** és adjuk át az üzenetünk kezelőjének. Amit kapunk vissza egy **MAP\_HANDLE** referencia, amely lehetővé teszi számunkra, hogy elkezd hozzá tulajdonságokat. Ez utóbbi a **Map\_AddOrUpdate**hívásával érhető el,\_amely egy MAP HANDLE-re, a tulajdonság nevére és a tulajdonság értékére hivatkozik. Ezzel az API-val annyi tulajdonságot adhatunk hozzá, amennyit csak akarunk.

Amikor az eseményt az **Event Hubs-ból**olvassa be, a fogadó felsorolhatja a tulajdonságokat, és lekérheti a megfelelő értékeket. A .NET objektumban például ez az [EventData objektum Tulajdonságok gyűjteményének](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx)elérésével érhető el.

Az előző példában tulajdonságokat csatolunk egy eseményhez, amelyet az IoT Hubnak küldünk. Tulajdonságok is csatolhatók az IoT Hubtól kapott üzenetekhez. Ha tulajdonságokat szeretnénk lekérni egy üzenetből, az üzenetvisszahívási funkcióban a következőhöz hasonló kódot használhatunk:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Az **IoTHubMessage\_tulajdonságainak hívása** a **MAP\_HANDLE hivatkozást** adja vissza. Ezután átadjuk ezt a hivatkozást a **Map\_GetInternals-nak,** hogy a név/érték párok tömbjére (valamint a tulajdonságok számára) hivatkozzon. Ezen a ponton ez egy egyszerű kérdés a tulajdonságok felsorolása, hogy az értékeket akarunk.

Nem kell használni a tulajdonságokat az alkalmazásban. Ha azonban be kell állítania őket az eseményekre, vagy le kell kérnie őket az üzenetekből, az **IoTHubClient könyvtár** megkönnyíti.

## <a name="message-handling"></a>Üzenetkezelés

Ahogy azt korábban említettük, amikor az üzenetek érkeznek az IoT Hubról, az **IoTHubClient** könyvtár válaszol egy regisztrált visszahívási függvény meghívásával. Ennek a függvénynek van egy visszatérési paramétere, amely további magyarázatot érdemel. Íme egy részlet a visszahívási függvényről az **iothub\_ügyfélminta\_\_http** mintaalkalmazásban:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Ne feledje, hogy a visszatérési típus **\_IOTHUBMESSAGE\_DISPOSITION RESULT,** és ebben az esetben visszaadjuk az **IOTHUBMESSAGE\_ACCEPTED -t.** Vannak más értékek is visszatérhetünk ebből a függvényből, amelyek megváltoztatják az **IoTHubClient könyvtár** reakcióját az üzenetvisszahívásra. Itt vannak a lehetőségek.

* **IOTHUBMESSAGE\_ELFOGADva** – Az üzenet feldolgozása sikeresen megtörtént. Az **IoTHubClient-függvénytár** nem hívja meg újra a visszahívási függvényt ugyanazzal az üzenettel.

* **IOTHUBMESSAGE\_elutasítva** – Az üzenet nem lett feldolgozva, és a jövőben nem vágyik erre. Az **IoTHubClient-függvénytár** nem hívhatja meg újra a visszahívási függvényt ugyanazzal az üzenettel.

* **IOTHUBMESSAGE\_ELHAGYVA** – Az üzenet feldolgozása nem sikerült, de az **IoTHubClient könyvtárnak** újra meg kell hívnia a visszahívási függvényt ugyanazzal az üzenettel.

Az első két visszatérési kód esetében az **IoTHubClient-kód** küld egy üzenetet az IoT Hubnak, jelezve, hogy az üzenetet törölni kell az eszközvárólistából, és nem kell újra kézbesíteni. A nettó hatás ugyanaz (az üzenet törlődik az eszközvárólistából), de az üzenet elfogadása vagy elutasítása továbbra is rögzítésre kerül.  A megkülönböztetés rögzítése hasznos az üzenet feladói számára, akik figyelhetik a visszajelzéseket, és megtudják, hogy egy eszköz elfogadott vagy elutasított-e egy adott üzenetet.

Az utolsó esetben egy üzenetet is küldött az IoT Hub, de azt jelzi, hogy az üzenetet kell kézbesíteni. Általában akkor hagy el egy üzenetet, ha valamilyen hibába ütközik, de újra meg szeretné próbálni feldolgozni az üzenetet. Ezzel szemben az üzenet elutasítása akkor megfelelő, ha helyrehozhatatlan hibába ütközik (vagy ha egyszerűen úgy dönt, hogy nem szeretné feldolgozni az üzenetet).

Minden esetben vegye figyelembe a különböző visszatérési kódokat, hogy kiválthassa a kívánt viselkedést az **IoTHubClient** könyvtárból.

## <a name="alternate-device-credentials"></a>Alternatív eszközhitelesítő adatok

Ahogy azt korábban már kifejtették, az **IoTHubClient** könyvtár használata során először az **\_IOTHUB\_CLIENT HANDLE** beszerzése egy hívással, például a következővel:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Az **IoTHubClient\_CreateFromConnectionString** argumentumai az eszköz kapcsolati karakterlánca és egy paraméter, amely az IoT Hubbal való kommunikációhoz használt protokollt jelzi. Az eszköz kapcsolati karakterláncának formátuma a következőképpen jelenik meg:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Ebben a karakterláncban négy információ található: IoT Hub-név, IoT Hub-utótag, eszközazonosító és megosztott hozzáférési kulcs. Egy IoT-központ teljesen minősített tartománynevét (FQDN) szerzi be, amikor létrehozza az IoT hub-példányt az Azure Portalon – ez megadja az IoT hub nevét (az fqdn első részét) és az IoT hub utótagot (a teljes tartománynév többi részét). Az eszközazonosítót és a megosztott hozzáférési kulcsot akkor kapja meg, amikor regisztrálja az eszközt az IoT Hubban (az [előző cikkben](iot-hub-device-sdk-c-intro.md)leírtak szerint).

**Az IoTHubClient\_CreateFromConnectionString** egy módot ad a tár inicializálására. Ha szeretné, létrehozhat egy új **IOTHUB\_\_ÜGYFÉLHANDLE-t** az eszköz kapcsolati karakterlánca helyett ezekkel az egyedi paraméterekkel. Ez a következő kóddal érhető el:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ez ugyanazt a dolgot valósítja meg, mint az **IoTHubClient\_CreateFromConnectionString**.

Nyilvánvalónak tűnhet, hogy az **IoTHubClient\_CreateFromConnectionString-et** szeretné használni, nem pedig ezt a részletesebb inicializálási módszert. Ne feledje azonban, hogy amikor regisztrál egy eszközt az IoT Hubban, akkor egy eszközazonosítót és egy eszközkulcsot (nem kapcsolati karakterláncot) kap. Az [előző cikkben](iot-hub-device-sdk-c-intro.md) bemutatott *eszközkezelő* SDK-eszköz az **Azure IoT-szolgáltatás SDK-jának** könyvtárait használja az eszközkapcsolati karakterlánc létrehozásához az eszközazonosítóból, az eszközkulcsból és az IoT Hub gazdagépnevéből. Így **az IoTHubClient\_LL\_Create hívása** előnyös lehet, mert a kapcsolati karakterlánc létrehozásának lépése menti meg. Használja azt a módszert, amely kényelmes.

## <a name="configuration-options"></a>Beállítási lehetőségek

Eddig minden leírt arról, ahogy az **IoTHubClient** könyvtár működik tükrözi az alapértelmezett viselkedését. Van azonban néhány beállítás, amely et beállíthatja a könyvtár működésének módosítására. Ez az **IoTHubClient\_\_LL SetOption** API kihasználásával érhető el. Tekintsük ezt a példát:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Van néhány lehetőség, amelyek gyakran használják:

* **SetBatching** (bool) – Ha **igaz,** majd az IoT Hubra küldött adatok kötegekben lesz elküldve. Ha **hamis**, akkor az üzenetek küldése külön-külön lesz. Az alapértelmezett érték **hamis**. Az AMQP / AMQP-WS-en keresztüli kötegelés, valamint a Rendszertulajdonságok hozzáadása a D2C-üzenetekhez támogatott.

* **Időtúltöltés** (aláíratlan int) – Ez az érték ezredmásodpercben jelenik meg. Ha https-kérelem küldése vagy válasz fogadása hosszabb időt vesz igénybe, akkor a kapcsolat túllépi az idődet.

A kötegelési lehetőség fontos. Alapértelmezés szerint a könyvtár egyenként továbbítja az eseményeket (egyetlen esemény az **IoTHubClient\_\_LL SendEventAsync**számára történő átadás). Ha a kötegelési beállítás **igaz,** a könyvtár annyi eseményt gyűjt, amennyit csak tud a pufferből (az IoT Hub által elfogadható maximális üzenetméretig).  Az eseményköteg egyetlen HTTPS-hívással kerül az IoT Hubra (az egyes események egy JSON-tömbbe vannak csomagolva). A kötegelés engedélyezése általában nagy teljesítménynövekedést eredményez, mivel csökkenti a hálózati átutazások számának csökkentését. Ez is jelentősen csökkenti a sávszélességet, mivel küld egy sor HTTPS fejlécek egy esemény köteg helyett egy sor fejlécek minden egyes esemény. Hanincs konkrét oka az ellenkezőjére, általában engedélyezni szeretné a kötegelést.

## <a name="next-steps"></a>További lépések

Ez a cikk részletesen ismerteti az **Azure IoT-eszköz C-hez sdk-jában**található **IoTHubClient-könyvtár** viselkedését. Ezekkel az információkkal kell egy jó megértése az **IoTHubClient** könyvtár képességeit. A sorozat második cikke az [Azure IoT-eszköz SDK C - Serializer,](iot-hub-device-sdk-c-serializer.md)amely hasonló részleteket biztosít a **szerializáló** könyvtár.

Ha többet szeretne megtudni az IoT Hub fejlesztéséről, tekintse meg az [Azure IoT SDK-k című témakört.](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további [megismeréséhez tekintse meg az AI üzembe helyezése az Azure IoT Edge-eszközök höz című témakört.](../iot-edge/tutorial-simulate-device-linux.md)
