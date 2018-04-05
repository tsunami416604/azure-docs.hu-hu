---
title: Az Azure IoT-eszközök SDK c - IoTHubClient |} Microsoft Docs
description: Hogyan használható a IoTHubClient könyvtár, az Azure IoT eszközben C-hez készült SDK kommunikáló eszközön futó alkalmazások létrehozásához az IoT-központ számára.
services: iot-hub
documentationcenter: ''
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 828cf2bf-999d-4b8a-8a28-c7c901629600
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: 0fa07b2a785bd7d1a26924918e4428fd420e1547
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Az Azure IoT-eszközök SDK c – további információ az IoTHubClient
A [először a következő cikket:](iot-hub-device-sdk-c-intro.md) a sorozat bevezette a **C-hez készült SDK Azure IoT-eszközök**. A cikk alapján, hogy nincsenek-e két architekturális rétegek SDK. Az alap van a **IoTHubClient** könyvtárban, amely közvetlenül kezeli az IoT-központ folytatott kommunikáció. Szerepel továbbá a **szerializáló** könyvtár épülő a szerializálási szolgáltatások biztosításához. Ebben a cikkben a következőkhöz nyújtunk további részletek a a **IoTHubClient** könyvtárban.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az előző cikkben leírt használata a **IoTHubClient** könyvtár események küldése az IoT-központ és az üzenetek fogadásához. Ez a cikk e kiterjeszti pontosabban kezelése ismertető *amikor* küldött és fogadott adatok bemutatása, hogy a **alacsonyabb szintű API-k**. Azt is ismertetjük tulajdonságok csatolható események (és üzenetek lekérése őket) szolgáltatások kezelése tulajdonság használatával a **IoTHubClient** könyvtárban. Végül az IoT-központ fogadott üzenetek kezelésének különböző módjai további magyarázata a következőkhöz nyújtunk.

A cikk arra a következtetésre jut, vegyes témaköreit, beleértve több hitelesítő adatai és működésének módosításához néhány kiterjed a **IoTHubClient** konfigurációs lehetőségek között.

Fogjuk használni a **IoTHubClient** SDK minták az alábbi témakörök ismertetik. Ha azt szeretné, követéséhez, tekintse meg a **IOT hubbal\_ügyfél\_minta\_http** és **IOT hubbal\_ügyfél\_minta\_amqp** , amely az Azure IoT-eszközök SDK meg a c mindent az alábbi szakaszok ismertetik ezeket a mintákat a bemutatott alkalmazások.

Megtalálhatja az [ **C-hez készült SDK Azure IoT-eszközök** ](https://github.com/Azure/azure-iot-sdk-c) GitHub tárház és nézet adatai az API-nak a [C API-referencia](https://azure.github.io/azure-iot-sdk-c/index.html).

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k
Az előző cikkben leírt alapszintű működését a **IotHubClient** kontextusában a **IOT hubbal\_ügyfél\_minta\_amqp** alkalmazás. Például azt a könyvtárban, a kód használatával inicializálásával ismertetése.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Azt is ismerteti, hogyan küldhet e függvényhívás eseményeket.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

A cikk azt is ismerteti, hogyan regisztrálja a visszahívási függvény üzeneteket fogadni.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

A cikk is bemutatta, hogyan lehet felszabadítani az erőforrásokat, például az alábbi kód használatával.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Azonban ezen API-k mindegyikének kiegészítő funkciók vannak:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Ezek a függvények minden "R" szerepeljenek az API-név. Eltérő ezek a paraméterek megegyeznek mint a nem le. Azonban ezeket a funkciókat a viselkedés eltér egy fontos módon.

A hívás esetén **IoTHubClient\_CreateFromConnectionString**, a mögöttes tárak, amelyek a háttérben fut, új szálat létrehozni. Ebből a szálból küldi az eseményeket, és az IoT-központ fogadja az üzeneteket. Nincs ilyen szál jön létre, az "R" API-k használatakor. A háttérben futó szál létrehozásának a fejlesztők a könnyebb elérhetőség érdekében. Nem rendelkezik explicit módon események üzenetek küldése és fogadása az IoT Hub – automatikusan történik a háttérben foglalkoznia. Ezzel szemben a "r" API-k biztosítanak explicit ellenőrzése alatt tartja a kommunikáció a IoT-központot, ha esetleg szükség lenne rá.

Ez jobb megértéséhez, nézzük például:

A hívás esetén **IoTHubClient\_SendEventAsync**, mi ténylegesen végzett helyezi az esemény a pufferben. Létre, ha meghívja a háttérszálon **IoTHubClient\_CreateFromConnectionString** folyamatosan figyeli az adott puffer és a benne található adatokat küld az IoT hubhoz. Ez akkor fordul elő a háttérben, hogy a fő szálnak működik-e más feladatok egy időben.

Hasonlóképpen, az üzenetek visszahívási függvény regisztrálásakor **IoTHubClient\_SetMessageCallback**, az SDK kell rendelkeznie a visszahívási függvény meghívása, ha egy üzenetet kapott, a fő szálnak független háttérszál most utasítja.

A "r" API-k ne hozzon létre egy háttérszálon. Ehelyett egy új API-t meg kell hívni explicit módon adatokat küldeni és fogadni az IoT-központot. A következő példa ezt mutatják.

A **IOT hubbal\_ügyfél\_minta\_http** alkalmazás, amely tartalmazza az SDK az alacsonyabb szintű API-k mutatja be. A minta nem küldeni események IoT-központ a következő kóddal:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Az első három sort, hozza létre az üzenetet, és az utolsó sort elküldi az esemény. Azonban amint azt korábban említettük, "küldő" az esemény azt jelenti, hogy az adatok egyszerűen kerülnek a pufferben. Nem történik adatátvitel a hálózaton, hívása **IoTHubClient\_inden\_SendEventAsync**. Ahhoz, hogy ténylegesen az adatokat az IoT-központ érkező, meg kell hívnia **IoTHubClient\_inden\_DoWork**, a példában szereplő:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ez a kód (a a **IOT hubbal\_ügyfél\_minta\_http** alkalmazás) egymás után többször hívja **IoTHubClient\_inden\_DoWork**. Minden alkalommal, amikor **IoTHubClient\_inden\_DoWork** van neve, a puffer az IoT-központ küldené egyes események, és lekérdezi az eszközre küldött aszinkron üzenet. Ez utóbbi esetben azt jelenti, hogy ha a jelenleg regisztrált üzenetek visszahívási függvény, majd a visszahívás meghívták (feltéve, hogy minden üzenet sorba vannak). Azt kellene regisztrált visszahívási függvény a következő kódot:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Az OK, amely **IoTHubClient\_inden\_DoWork** gyakran nevezik hurok, amely minden alkalommal, amikor azt nevezzük, elküldi *néhány* eseményeket az IoT-központ, pufferelt és beolvassa a *a következő* üzenet sorba az eszköz. Minden hívás nem garantált is küldi az összes pufferelt eseményeket, vagy beolvasni az összes várólistára helyezett üzenetek. Ha szeretné a pufferben lévő összes eseményt küldeni, és folytassa a más feldolgozás a lecserélheti Ez a ciklus a következő kódot:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ez a kód **IoTHubClient\_inden\_DoWork** mindaddig, amíg az IoT hubhoz elküldése a pufferben lévő összes eseményt. Megjegyzés: Ez nem feltétlenül is jelenti, hogy az összes várólistára helyezett üzenetek fogadott. Ennek oka részét képezi, hogy "all" üzenetek ellenőrzése a nem a determinisztikus művelet. Mi történik, ha visszaállíthatja az "összes" üzenetet, de majd egy másikat küld az eszköz után azonnal? Jobb módja foglalkozik, amely programozott időkorlát. Például az üzenet visszahívási függvény sikerült alaphelyzetbe állítani egy számlálót minden alkalommal, amikor meghívták. Majd írhat logika folytatódik a feldolgozás, ha például nem érkeztek üzenetek a legutóbbi *X* másodperc.

Ha végzett ingressing események van, és fogadja az üzeneteket, ügyeljen arra, hogy a megfelelő függvény erőforrások karbantartása.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Nincs alapvetően csak egy készletét API-k a háttérszálon és API-k, amelyet az ugyanaz a háttérszálon nélkül egy másik készlet adatokat küldeni és fogadni. Nagy mennyiségű fejlesztők előfordulhat, hogy inkább a nem - inden API-k, de az alacsonyabb szintű API-k akkor hasznos, ha a fejlesztői szeretne rendelni a hálózati átvitelt explicit vezérelheti. Például bizonyos eszközök begyűjtik az adatokat idő és a csak érkező jelzések (például óra egy alkalommal vagy naponta egyszer) meghatározott időközönként. Az alacsonyabb szintű API-k tudatjuk a felhasználókkal, lehetővé teszi, explicit módon vezérlő küld és fogad adatokat az IoT-központot. Mások egyszerűen, főként a az egyszerűség, amelyek az alacsonyabb szintű API-k biztosítanak. Minden egyes munka azonban a háttérben, hanem a fő szálnak történik.

Bármelyik modellt választja, ne felejtse el konzisztens mely API-kat használ. Ha először meghívásával **IoTHubClient\_inden\_CreateFromConnectionString**, győződjön meg a megfelelő alacsonyabb szintű API-kat csak használ követő munka:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

Ellenkező is igaz. Ha először **IoTHubClient\_CreateFromConnectionString**, majd használja a nem - inden API-k minden további feldolgozásra.

Az Azure IoT-eszközök SDK C-hez, olvassa el a **IOT hubbal\_ügyfél\_minta\_http** alkalmazás az alacsonyabb szintű API-k átfogó példát. A **IOT hubbal\_ügyfél\_minta\_amqp** alkalmazás teljes például az a nem - inden API-k lehet hivatkozni.

## <a name="property-handling"></a>Tulajdonság kezelése
Amennyiben azt korábban leírt adatokat küldő, amikor azt korábban már hivatkozik az üzenet törzsét. Tegyük fel ezt a kódot:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Ez a példa egy üzenetet küld az IoT-központ szöveget a "Hello World". Azonban az IoT-központ lehetővé teszi tulajdonságok az egyes üzeneteket kell csatolni. A rendszer az üzenethez csatolt név/érték párok tulajdonságokat. Módosíthatja például azt az előző kód csatolni egy tulajdonság az üzenet:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Először meghívásával **IoTHubMessage\_tulajdonságok** , és átadja azt a leíró az üzenet. Mi azt vissza van egy **térkép\_KEZELNI** hivatkozás, amely lehetővé teszi indíthatja el a tulajdonságokat. Meghívásával elvégzéséhez **térkép\_AddOrUpdate**, így tovább is hivatkozni kell a Térképen\_leíró, a tulajdonság nevét és a tulajdonság értéke. Ez az API-t hozzá lehessen adni annyi tulajdonságai, például azt.

Ha az esemény olvasni **Event Hubs**, a fogadó a tulajdonságok enumerálása, és a hozzájuk tartozó értékek beolvasása. Például a .NET ez szeretné végezni a fér hozzá a [tulajdonsággyűjteményében a EventData objektum](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Az előző példában azt csatol tulajdonságok nem küldeni az IoT-központ eseményre. Az IoT-központ érkező üzenetek is csatolható tulajdonságok. Tulajdonságok lekérése üzenet szeretnénk, ha azt az üzenet visszahívási függvény a következő kódot használhatja:

```
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

A hívás **IoTHubMessage\_tulajdonságok** adja vissza a **térkép\_KEZELNI** hivatkozás. Azt, akkor továbbítja a hivatkozó **térkép\_GetInternals** a név/érték párok (valamint a tulajdonságok száma) tömbje mutató hivatkozás beszerzése. Ezen a ponton azt szeretnénk, ha az értékek gombra tulajdonságainak egyszerű kérdése.

Tulajdonságok használata az alkalmazás nem rendelkezik. Azonban, ha szeretné-e meg őket a események vagy kérheti le azokat az üzeneteket, a **IoTHubClient** könyvtár megkönnyíti.

## <a name="message-handling"></a>Üzenetkezelés
Ahogy korábban is hangsúlyoztuk, amikor érkező üzenetek IoT-központ a **IoTHubClient** könyvtár regisztrált visszahívási függvény meghívása válaszol. Ez a funkció, amely további rövid érdemel visszatérési paraméterének van. Ez a visszahívási függvény cikkből a **IOT hubbal\_ügyfél\_minta\_http** mintaalkalmazást:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Vegye figyelembe, hogy a visszatérési típus **IOTHUBMESSAGE\_törlése\_eredmény** és az adott esetben a rendszer visszaadja a **IOTHUBMESSAGE\_elfogadott**. Nincsenek más értékek azt adhatja vissza a függvény, amely módosíthatja az **IoTHubClient** könyvtár reagálás az üzenet visszahívási. Az alábbiakban a beállításokat.

* **IOTHUBMESSAGE\_elfogadott** – az üzenet feldolgozása sikeresen megtörtént. A **IoTHubClient** könyvtár nem indítja el a visszahívási függvény újra ugyanazt az üzenetet.
* **IOTHUBMESSAGE\_elutasítva** – az üzenet nem lett feldolgozva, és tudni az ehhez a jövőben van. A **IoTHubClient** könyvtár nem kell meghívnia a visszahívási függvény újra ugyanazt az üzenetet.
* **IOTHUBMESSAGE\_ABANDONED** – az üzenet nem feldolgozása sikeresen megtörtént, de a **IoTHubClient** könyvtárban kell meghívnia a visszahívási függvény újra ugyanazt az üzenetet.

Az első két visszatérési kódok, a **IoTHubClient** könyvtár üzenetet küld az IoT-központ, amely jelzi, hogy az üzenet törlődik az eszköz üzenetsorból kell-e, és nem kézbesíteni újra. A nettó hatást megegyezik (az üzenet törlődik az eszköz várólista), de e a elfogadta vagy visszautasított továbbra is rögzíti.  Ezt a különbséget rögzítése célszerű az üzenet feladók, akik a visszajelzés figyelésére és megtudhatja, ha egy eszköz elfogadta vagy egy üzenet visszautasította.

Az utolsó esetben egy üzenet is lett van küldve IoT-központot, de azt jelzi, hogy az üzenet újból kézbesítve a kell. Általában egy üzenetet fog abandon, ha néhány hibát, de az üzenet feldolgozására újra megpróbálhatja. Ezzel szemben elutasítása üzenet akkor megfelelő ha helyrehozhatatlan hibát észlel (vagy egyszerűen döntenie nem tudja feldolgozni az üzenetet).

Mindenképpen figyelembe vennie a különböző visszatérési kódokat, hogy a kívánt viselkedés akkor is kér a **IoTHubClient** könyvtárban.

## <a name="alternate-device-credentials"></a>Alternatív hitelesítő adatai
Ahogy korábban, az első lépés az használatakor a **IoTHubClient** könyvtár az beszerzése egy **IOT HUBBAL\_ügyfél\_KEZELNI** például a következő hívással:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Az argumentumok **IoTHubClient\_CreateFromConnectionString** az eszköz kapcsolati karakterláncát, és egy paraméter, amely jelzi a protokoll használatával kommunikáljon az IoT hubbal. Az eszköz kapcsolati karakterlánc formátuma a következőképpen jelenik meg:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Ez a karakterlánc a négy adatra van: az IoT-központ nevét, az IoT-központ utótag, Eszközazonosító és megosztott elérési kulcsot. A teljesen minősített tartománynevét (FQDN), az IoT-központ kaphat az Azure-portálon az IoT hub-példány létrehozásakor – ez lehetővé teszi az IoT-központnév (első része a teljes Tartományneve) és az IoT hub utótagot (az FQDN a többi). Kapott az Eszközazonosító és a megosztott elérési kulcsot az eszköz regisztrálása az IoT-központ (lásd: a [előző cikkben](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** lehetővé teszi az egyik módja a kódtár inicializálása. Ha jobban szeret, hozhat létre egy új **IOT HUBBAL\_ügyfél\_KEZELNI** az eszköz kapcsolati karakterlánc helyett ezen egyéni paraméterek használatával. Ez úgy érhető el, az alábbi kódra:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ezt a feladatot el ugyanaz, mint **IoTHubClient\_CreateFromConnectionString**.

Nyilvánvaló, hogy volna használni kívánt tűnhet **IoTHubClient\_CreateFromConnectionString** ahelyett, hogy ez a részletesebb módszer az inicializálás. Ne feledje, azonban, hogy amikor regisztrál egy eszközt az IoT-központ tartalmával egy Eszközazonosító és eszközkulcs (kapcsolati karakterlánc). A *eszköz explorer* SDK eszköz bevezetett a [előző cikkben](iot-hub-device-sdk-c-intro.md) a-tárakat használ a **Azure IoT szolgáltatás SDK** az eszköz kapcsolati karakterlánc létrehozása a Eszközazonosító eszközkulcs és az IoT-központ állomásnevet. Így hívása **IoTHubClient\_inden\_létrehozása** lehet, hogy előnyösebb egy kapcsolati karakterlánc létrehozása a lépés menti azt. Használja az kényelmes bármelyik módszert.

## <a name="configuration-options"></a>Konfigurációs beállítások
Amennyiben mindent ismertetett beállításairól a **IoTHubClient** könyvtár works tükrözi az alapértelmezett viselkedés. Van azonban néhány lehetőség, amely lehet módosítani a szalagtár működése. Mindez, ami a **IoTHubClient\_inden\_SetOption** API. Vegye figyelembe az ebben a példában:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Van néhány gyakran használt beállításokat:

* **SetBatching** (logikai) – Ha **igaz**, majd az IoT-központ küldött adatok küldése kötegek. Ha **hamis**, majd üzenetküldés külön-külön. Az alapértelmezett érték **hamis**. Vegye figyelembe, hogy a **SetBatching** beállítás csak akkor érvényes, a HTTPS protokollt, és nem a MQTT vagy az AMQP protokollt.
* **Időtúllépés** (előjel nélküli egész) – Ez az érték ezredmásodpercben szerepel. Ha egy HTTPS-kérést vagy kap választ ennél hosszabb ideje tart, akkor a kapcsolat időtúllépés miatt küldi.

Fontos a kötegelési lehetőséget. Alapértelmezés szerint a szalagtár ingresses események külön-külön (egy adott eseményhez bármilyen át a **IoTHubClient\_inden\_SendEventAsync**). Ha a kötegelési beállítás **igaz**, a könyvtár (akár a maximális méretét, amely elfogadja az IoT-központ) pufferből használhatja annyi eseményeit gyűjti.  Az esemény kötegelt IoT Hub (az egyes események, egy JSON-tömb be vannak becsomagolva) egyetlen HTTPS hívás érkezik. Általában kötegelés engedélyezését eredményezi nagy teljesítménynövekedéshez óta hálózati üzenetváltások most csökkentése. Mivel egy esemény-es HTTPS fejlécek egy készletét, nem pedig a minden egyes esemény fejléc készlettel küldi is jelentősen csökkenti a sávszélesség. Ha valamiért másképp nincs, általában érdemes engedélyezni a kötegelés.

## <a name="next-steps"></a>További lépések
Ez a cikk ismerteti részletesen a működését a **IoTHubClient** függvénytár szerepel a **C-hez készült SDK Azure IoT-eszközök**. Az információ funkcióinak beható ismerete rendelkeznie kell a **IoTHubClient** könyvtárban. A [tovább cikk](iot-hub-device-sdk-c-serializer.md) hasonló részletesen ismerteti a a **szerializáló** könyvtár.

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k][lnk-sdks].

Az IoT-központ képességeit további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
