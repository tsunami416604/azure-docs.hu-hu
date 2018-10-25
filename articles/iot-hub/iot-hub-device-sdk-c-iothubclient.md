---
title: Az Azure IoT eszközoldali SDK-t a C – Iothubclientről |} A Microsoft Docs
description: Hogyan használható az Iothubclientről erőforrástárhoz, az Azure IoT eszközoldali c SDK-t a IoT hub-kommunikációs eszköz alkalmazások létrehozásához.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: yizhon
ms.openlocfilehash: dd3b693271326c85688a275a65b67ad6257220e3
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024764"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Az Azure IoT eszközoldali SDK-t a C – további információ az Iothubclientről

[Az Azure IoT eszközoldali SDK-t a c nyelvhez készült](iot-hub-device-sdk-c-intro.md) az első a sorozat bevezetéséről szóló cikk a **Azure IoT eszközoldali SDK-t a c nyelvhez készült**. A cikk részletesen, hogy nincsenek-e két architekturális réteg az SDK-t. Alap van a **Iothubclientről** kódtár, amelyet közvetlenül kezeli a kommunikációt az IoT hubbal. Emellett van a **szerializáló** kódtár, amely összeállítja a szerializálási szolgáltatások biztosításához. Ebben a cikkben biztosítunk, amellyel további részleteket a a **Iothubclientről** könyvtár.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az előző cikkben leírt használata a **Iothubclientről** könyvtár eseményeket küld az IoT Hub és az üzenetek fogadásához. Ez a cikk elmagyarázza a pontosabb kezelése terjeszti ki, hogy a hozzászólás *amikor* küldött és fogadott adatok bemutatása, hogy a **alacsonyabb szintű API-k**. Azt is ismertetjük tulajdonságok csatolása események (és kérheti le azokat az üzeneteket) a szolgáltatások a kezelési tulajdonságot az **Iothubclientről** könyvtár. Végül biztosítunk, amellyel többféle módon kezelje az IoT hub felől fogadott üzenetek további magyarázatot.

A cikkben arra a következtetésre jut, vegyes témaköreit, beleértve, hogy az további információ az eszköz hitelesítő adatai, és hogyan változtathatja meg néhány kiterjed a **Iothubclientről** konfigurációs lehetőségek között.

Használjuk a **Iothubclientről** SDK-minták az alábbi témakörök ismertetik. Ha azt szeretné, hogy követni tudja, tekintse meg a **iothub\_ügyfél\_minta\_http** és **iothub\_ügyfél\_minta\_amqp**alkalmazásokat, amelyek az Azure IoT eszközoldali SDK szerepelnek, a c mindent a következő szakaszokban ismertetett mutatják be ezeket a mintákat.

Annak a [ **a c nyelvhez készült Azure IoT eszközoldali SDK** ](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárházat és a nézet részletei az API-t a [C API-referencia](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>Az alacsonyabb szintű API-k

Az előző cikkben leírt alapszintű működését a **Iothubclientről** kontextusában a **iothub\_ügyfél\_minta\_amqp** alkalmazás. Például azt magyarázza inicializálásával a könyvtárban, a kód használatához.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Azt is ismerteti, hogyan küldhet eseményeket, a függvény hívásához szükséges használatával.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

A cikk az üzenetek fogadása egy visszahívási függvény regisztrálásával is ismerteti.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

A cikk is bemutatta, hogyan ingyenes erőforrások, például a következő kód használatával.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Nincsenek kiegészítő funkciók ezen API-k mindegyikéhez:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* Iothubclientről\_LL\_megszüntetése

Ezek a függvények minden beágyazott **LL** az API-név. Más a **LL** része a név, ezek közül minden függvény paraméterei azonosak, mint a nem LL. Azonban ezek a függvények viselkedését különbözik a egyik fontos módja.

Meghívásakor **Iothubclientről\_CreateFromConnectionString**, az alapul szolgáló kódtárak, hozzon létre egy új hozzászólásláncot a háttérben futó. Ez a hozzászóláslánc küldi az eseményeket, és fogadja az üzeneteket az IoT hubról. Nincs ilyen szál jön létre, ha dolgozik a **LL** API-k. A háttérben futó szál létrehozása a fejlesztők a könnyebb. Nem kell aggódnia a explicit módon eseményt üzenetek küldése és fogadása az IoT Hub – a háttérben automatikusan megtörténik. Ezzel szemben a **LL** API-k lehetővé teszik, explicit keresztüli kommunikáció az IoT hubbal, ha szüksége lesz rá.

A fogalom jobb megértéséhez, lássunk erre egy példát:

Meghívásakor **Iothubclientről\_SendEventAsync**, amit ténylegesen végzett helyezi az esemény a pufferben. Jön létre, amikor hívása a háttérszálból **Iothubclientről\_CreateFromConnectionString** folyamatosan figyeli ezt a puffert, és a benne található adatokat küld az IoT hubnak. Ez a háttérben történik, hogy a fő szálnak van-e más munkát végez egy időben.

Ehhez hasonlóan Ha regisztrál egy visszahívási függvény használatával üzeneteket **Iothubclientről\_SetMessageCallback**, hogy ezt az SDK-val rendelkezik, a háttérszálon a visszahívási függvény hívása, amikor egy üzenet fogadott, függetlenül a fő szálnak.

A **LL** API-k ne hozzon létre egy háttérszálból történik. Ehelyett egy új API-t kell meghívni, explicit módon adatokat küldeni és fogadni az IoT hubról. Ez a következő példa mutatja be.

A **iothub\_ügyfél\_minta\_http** alkalmazás, amely tartalmazza az SDK-t az alacsonyabb szintű API-k mutatja be. A minta azt események küldése az IoT hub például a következő kóddal:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Az első három sorát, hozzon létre az üzenetet, és az utolsó sort elküldi az eseményt. Azonban amint azt korábban említettük, küldése az esemény azt jelenti, hogy az adatok egyszerűen kerülnek, a pufferben. Nem történik adatátvitel a hálózaton, amikor nevezzük **Iothubclientről\_LL\_SendEventAsync**. Az érdekében, hogy ténylegesen bejövő forgalom az adatokat az IoT hubhoz, meg kell hívni **Iothubclientről\_LL\_DoWork**, amint az ebben a példában:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód (az a **iothub\_ügyfél\_minta\_http** alkalmazás) többször is meghívja **Iothubclientről\_LL\_DoWork**. Minden alkalommal, amikor **Iothubclientről\_LL\_DoWork** van nevű, a buffer szolgáltatásból származó küld néhány esemény az IoT Hub és a egy üzenetsorban található üzenet az eszköznek küldött lekéri. Az utóbbi esetben azt jelenti, hogy ha egy visszahívási függvény üzenetek regisztrált, majd a visszahívás hív (feltéve, hogy bármely üzenetei). Mi lenne regisztrált egy visszahívási függvény például a következő kóddal:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Az OK, amely **Iothubclientről\_LL\_DoWork** gyakran nevezik hurkot, hogy minden alkalommal, amikor azt nevezzük, küld *néhány* az eseményeket az IoT hubra, valamint lekéri pufferelt*a következő* az eszköz várólistán lévő üzenetek. Minden hívás nem garantált, hogy minden pufferelt eseményeket küldeni, vagy lekérdezheti az összes sorba állított üzenetek. Ha szeretné az összes esemény küldése a pufferben lévő, és folytassa a további feldolgozás a lecserélheti a hurok például a következő kódot:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Ez a kód meghívja **Iothubclientről\_LL\_DoWork** mindaddig, amíg a pufferben lévő összes eseményt az IoT hubnak küldött. Vegye figyelembe, hogy ez nem is jelenti, hogy az összes várólistán lévő üzenetek megkaptuk. Része ennek az az oka az, hogy "all" üzenetek ellenőrzése nem determinisztikus, egy műveletet. Mi történik, ha, de kérheti le az "összes" üzenetet, majd egy másik küld az eszköz azonnal után? Hatékony megoldást foglalkozik, amely programozott időkorlát. Például az üzenet visszahívást függvény sikerült alaphelyzetbe állítani egy számlálót minden alkalommal, amikor indítva. Folytatódik a feldolgozás, ha a például nem üzenet nem érkezett az utolsó logikai majd írhat *X* másodperc.

Amikor kész ingressing események használ, és fogadja az üzeneteket, ügyeljen arra, hogy a megfelelő függvény az erőforrások törlése.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Nincs alapvetően csak egy API-készlet, küldése és fogadása egy háttérszálból történik, és a egy másik hárompéldányos készletet API-kat a háttérszálon nélkül ugyanezt teszi az adatok. A fejlesztők sok előfordulhat, hogy inkább a nem – z összes API-k, de az alacsonyabb szintű API-k hasznosak, ha a fejlesztő szeretné explicit szabályozhatja a hálózati adatátvitel. Ha például bizonyos eszközök begyűjtik az adatokat idő függvényében, és csak a belépő események megadott időközönként (például egy újabb órájába egyszer vagy naponta egyszer). Az alacsonyabb szintű API-k lehetővé teszik, explicit módon szabályozhatja során küldött és fogadott adatok az IoT hubról. Mások egyszerűen részesíti előnyben, amelyek az alacsonyabb szintű API-k nyújtanak az egyszerűség. Minden egyes munkahelyi azonban a háttérben, hanem a fő szálnak történik.

Bármelyik lehetőséget választja, modell mindenképpen legyen, következetesen melyik API-k használata. Ha meghívásával **Iothubclientről\_LL\_CreateFromConnectionString**, győződjön meg, csak a megfelelő alacsonyabb szintű API-k használatával követő munka:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* Iothubclientről\_LL\_megszüntetése
* Iothubclientről\_LL\_DoWork

Ennek az ellenkezője is igaz. Ha először a **Iothubclientről\_CreateFromConnectionString**, majd a nem – z összes API-k használatával bármilyen további feldolgozást.

Az Azure IoT eszközoldali SDK-t a c nyelvhez készült, tekintse meg a **iothub\_ügyfél\_minta\_http** alkalmazás az alacsonyabb szintű API-k teljes példát. A **iothub\_ügyfél\_minta\_amqp** egy teljes példa a nem – z összes API-alkalmazás lehet hivatkozni.

## <a name="property-handling"></a>Vlastnost kezelése

Eddig azt korábban leírt adatokat küldő, amikor azt már lett hivatkozik az üzenet törzsében. Vegyük példaként ezt a kódot:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Ebben a példában egy üzenetet küld az IoT Hub a szöveg a "Hello World." Azonban az IoT Hub lehetővé teszi tulajdonságok minden üzenet van csatlakoztatva. A tulajdonságok akkor az üzenethez csatolt név/érték párok. Például módosíthatja azt a tulajdonságot az üzenethez csatolni az előző kód:

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Kezdődik meg meghívásával **IoTHubMessage\_tulajdonságok** majd továbbítani azt a leíró, az üzenet. Amit kapunk vissza a rendszer egy **térkép\_KEZELNI** hivatkozás, amely lehetővé teszi számunkra, hogy kezdje el hozzáadni a tulajdonságai. Meghívásával gazdaplatformként **térkép\_AddOrUpdate**, térkép egy hivatkozást, amely veszi\_leíró, a tulajdonság nevét és a tulajdonság értéke. Ez az API-val azt szeretnénk, annyi tulajdonságokat adhat hozzá.

Ha az esemény eseményforrásból történő **az Event Hubs**, a fogadó enumerálása, és a hozzájuk tartozó értékek lekéréséhez. Ha például a .NET-ben Ez azok megvalósítására elérésével a [az EventData objektum tulajdonsággyűjteményében](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Az előző példában azt Ön csatolása tulajdonságai egy eseményt, amely az IoT Hub küldjük. Tulajdonságok is is csatlakozik az IoT hub felől fogadott üzeneteket. Kívánt tulajdonságok lekérését egy üzenetet, ha az üzenet visszahívási függvény is használja például a következő kódot:

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

A hívást **IoTHubMessage\_tulajdonságok** adja vissza a **térkép\_KEZELNI** hivatkozást. Azt, akkor továbbítja a hivatkozó **térkép\_GetInternals** , szerezzen be egy hivatkozást egy tömb, a név-érték párok (valamint a tulajdonságok számát). Ezen a ponton célszerű egyszerű pár szeretnénk értékek beolvasásához a tulajdonságok enumerálása.

Nem kell tulajdonságok használata az alkalmazásban. Azonban, ha az események állíthat be vagy kérheti le azokat az üzeneteket, a **Iothubclientről** kódtára megkönnyíti.

## <a name="message-handling"></a>Üzenetkezelés

Ahogy korábban is hangsúlyoztuk, ha üzenet érkezik az IoT hubról a **Iothubclientről** könyvtár válaszol egy regisztrált visszahívási függvény meghívásával. Nincs visszatérési paraméter a függvény, amely további magyarázatot hozhatja létre. Íme a visszahívási függvény cikkből szerint a **iothub\_ügyfél\_minta\_http** mintaalkalmazást:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Vegye figyelembe, hogy a typ vrácené hodnoty je **IOTHUBMESSAGE\_DISPOSITION\_eredmény** és az adott esetben értéket adunk vissza **IOTHUBMESSAGE\_ELFOGADVA**. Egyéb értékek azt adhat vissza a függvény, amely módosíthatja a **Iothubclientről** könyvtár reagál az üzenet-visszahívás. Az alábbiakban a beállításokat.

* **IOTHUBMESSAGE\_ELFOGADVA** – az üzenet feldolgozása sikeresen megtörtént. A **Iothubclientről** könyvtár nem indítja el a visszahívási függvény újra ugyanazt az üzenetet.

* **IOTHUBMESSAGE\_elutasítva** – az üzenet nem lett feldolgozva, és ott nem ehhez a jövőben nem törekszik. A **Iothubclientről** könyvtár nem kell meghívni a visszahívási függvény újra ugyanazt az üzenetet.

* **IOTHUBMESSAGE\_ABANDONED** – az üzenet feldolgozása sikeres, nem volt, de a **Iothubclientről** könyvtár kell meghívnia a visszahívási függvény újra ugyanazt az üzenetet.

Az első két visszatérési kódok, a **Iothubclientről** könyvtár üzenetet küld az IoT Hub jelzi, hogy az üzenet az eszközön üzenetsorból törölni kell-e, majd újra nem lett kézbesítve. Az eredő hatás (a program törli az eszköz üzenetsorból) azonos, de e az üzenet elfogad vagy elutasít továbbra is rögzíti.  Ezt a különbséget rögzítése hasznos az üzenet küldők, akik visszajelzést figyelését és ismerje meg, ha egy eszköz elfogadta vagy egy adott üzenetet visszautasította.

Az utolsó esetben egy üzenetet is küld az IoT hubhoz, de azt jelzi, hogy az üzenet újbóli kézbesítése a kell. Általában egy üzenetet fog abandon, ha valamilyen hiba lép fel, de megpróbálja feldolgozni az üzenetet újra. Ezzel szemben az üzenetet elutasító akkor megfelelő Ha helyreállíthatatlan hibát észlel (vagy ha egyszerűen nem kívánja feldolgozni az üzenetet).

Mindenképpen vegye figyelembe a különböző visszatérési kódokat, hogy Ön is elhárítja a kívánt viselkedésre a **Iothubclientről** könyvtár.

## <a name="alternate-device-credentials"></a>Alternatív hitelesítő adatai

Amint azt korábban, az első teendő használatakor a **Iothubclientről** könyvtár, hogy lekérjük a **IOTHUB\_ügyfél\_KEZELNI** például a következő hívással:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Az argumentumok **Iothubclientről\_CreateFromConnectionString** az eszköz kapcsolati karakterláncának és a egy paramétert, amely azt jelzi, hogy a protokollt az IoT hubbal való kommunikációhoz használjuk. Az eszköz kapcsolati karakterláncának formátuma a következőképpen jelenik meg:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Nincsenek négy információt a következő karakterláncot: az IoT Hub nevét, az IoT Hub-utótagot, Eszközazonosítót és megosztott elérési kulcsával. A teljesen minősített tartománynevét (FQDN), az IoT hub beszerzett, az Azure Portalon az IoT hub-példány létrehozásakor – ez lehetővé teszi az IoT hub nevére (az FQDN első része) és az IoT hub utótagot (a többi része a teljes tartománynév). Is számíthat az Eszközazonosítót és a közös hozzáférési kulcs regisztrálja az eszközt az IoT Hub (leírtak szerint a [előző cikk](iot-hub-device-sdk-c-intro.md)).

**Iothubclientről\_CreateFromConnectionString** egyik módja a kódtár inicializálása. Igény szerint hozhat létre egy új **IOTHUB\_ügyfél\_KEZELNI** az eszköz kapcsolati karakterláncának helyett ezeket az egyes paraméterek használatával. Ez úgy érhető el, a következő kóddal:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Ez ugyanaz, mint a feladatot el **Iothubclientről\_CreateFromConnectionString**.

Ez nyilvánvalónak tűnhet, hogy szeretne-e használni **Iothubclientről\_CreateFromConnectionString** ahelyett, hogy ez a részletesebb metódus az inicializálás. Vegye figyelembe, azonban, hogy amikor regisztrál egy eszközt az IoT Hub nyújtja a rendszer egy Eszközazonosítót és az eszköz kulcsát (ez nem egy kapcsolati karakterlánc). A *device explorer* eszköz SDK-ben bevezetett a [előző cikk](iot-hub-device-sdk-c-intro.md) a tárakat használ a **Azure IoT szolgáltatás SDK** hozhat létre az eszköz kapcsolati karakterláncát az eszköz azonosítója , eszköz kulcsát, és az IoT Hub-állomásnévvel. Ezért hívása **Iothubclientről\_LL\_létrehozás** előnyösebb lehet, mivel így a kapcsolati karakterlánc létrehozása. lépését. Használja az kényelmes bármelyik módszert.

## <a name="configuration-options"></a>Beállítási lehetőségek

Eddig minden leírt módon kapcsolatban a **Iothubclientről** könyvtár works tükrözi annak alapértelmezett viselkedését. Vannak azonban néhány lehetőség, amely lehet módosítani a szalagtár működése. Mindez kihasználva a **Iothubclientről\_LL\_SetOption** API-t. Ebben a példában, vegye figyelembe:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Van néhány gyakran használt műveletek közül:

* **SetBatching** (logikai) – Ha **igaz**, majd kötegek küldése az IoT hubnak küldött adatokat. Ha **hamis**, majd külön-külön üzeneteket küld. Az alapértelmezett érték **hamis**. Vegye figyelembe, hogy a **SetBatching** lehetőség csak akkor érvényes, a HTTPS protokollt és nem az MQTT vagy AMQP protokollt.

* **Időtúllépés** (előjel nélküli int) – Ez az érték jelenik meg az ezredmásodperc. Ha egy HTTPS-kérelem vagy válasz fogadása hosszabb időt vesz igénybe, mint az ideje, majd a kapcsolat időtúllépés miatt küldi.

A kötegelés beállítás azért fontos. A könyvtár ingresses események alapértelmezés szerint külön-külön (egy adott eseményhez bármilyen adja át a **Iothubclientről\_LL\_SendEventAsync**). Ha a kötegelés beállítás **igaz**, a kódtár annyi eseményeket eltárolni a pufferből (akár a maximális mérete, amely elfogadja az IoT Hub) gyűjti.  Az esemény batch küld az IoT Hub (az egyes események JSON-tömböt be vannak csoportosítva) egy HTTPS-hívással. Engedélyezése a kötegelés általában eredményez, nagy teljesítményre képes, Ön csökkenti a hálózati üzenetváltások óta. Is jelentősen csökkenti a sávszélesség, mivel küldünk egy eseményt a kötegelt HTTPS fejlécek egy készletét helyett a fejléceket minden egyes esemény egy készletét. Ha nincs valamilyen konkrét érv amellett, hogy ne így tegyen, általában érdemes kötegelés engedélyezéséhez.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti részletesen viselkedését a **Iothubclientről** könyvtár található a **Azure IoT eszközoldali SDK-t a c nyelvhez készült**. Ezekkel az információkkal kell érteni képességeit a **Iothubclientről** könyvtár. A sorozat második cikk [Azure IoT eszközoldali SDK-C - szerializáló az](iot-hub-device-sdk-c-serializer.md), amely részletesen hasonló a a **szerializáló** könyvtár.

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Azure IoT SDK-k](iot-hub-devguide-sdks.md).

Részletesebb megismerése az IoT Hub képességeit, tekintse meg [telepítése AI peremhálózati eszközökre az Azure IoT Edge segítségével](../iot-edge/tutorial-simulate-device-linux.md).