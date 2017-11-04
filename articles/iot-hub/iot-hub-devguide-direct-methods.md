---
title: "Azure IoT Hub közvetlen módszerek megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - használjon közvetlen módszerek meghívni a kódot az eszközök egy szolgáltatás alkalmazásból."
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d23bf20e4483b102fe5d946cb017dce1769b39a1
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Ismertetés és az IoT-központ közvetlen metódusok
Az IoT-központ lehetővé teszi a felhőből eszközök közvetlen módszerek meghívására. Közvetlen módszerek határoz meg egy kérelem-válasz interakció egy HTTP-hívás hasonló eszközökkel abban, hogy sikeres legyen, vagy közvetlenül (felhasználó által meghatározott időtúllépési) után sikertelen. Ez a megközelítés forgatókönyvekben, ahol azonnali lépéseket, attól függően, hogy képesek válaszolni, például egy SMS ébresztési küld egy eszközt, ha egy eszköz kapcsolat nélküli (SMS drágább, mint egy metódus hívása folyamatban) volt-e az eszköz különböző érdemes használni.

Minden eszköz metódus egyetlen eszközt célozza. [Feladatok] [ lnk-devguide-jobs] nyújtanak olyan közvetlen metódusok több eszközön, és ütemezés szerinti metódushívás leválasztott eszközökhöz.

Bárki, aki **service csatlakozás** IoT-központ engedélyeinek indít el egy metódust az eszközön.

Közvetlen módszerek hajtsa végre a kérelem-válasz mintát, és úgy van kialakítva, az eredmény, az eszköz, például egy ventilátor bekapcsolása általában interaktív vezérléshez azonnali megerősítését igénylő kommunikációhoz.

Tekintse meg [felhő eszközre kommunikációs útmutatást] [ lnk-c2d-guidance] bizonytalan kívánt tulajdonságai között, ha a közvetlen módszer vagy a felhő-eszközre küldött üzenetek.

## <a name="method-lifecycle"></a>Módszer életciklusa
Közvetlen módszerek valósíthatók meg az eszközön, és előfordulhat, hogy a módszer hasznos megfelelően példányosítani nulla vagy több bemeneti adatok. A közvetlen módszer keresztül elérhető szolgáltatás URI indításakor (`{iot hub}/twins/{device id}/methods/`). Egy eszköz megkapja közvetlen metódusok eszközspecifikus MQTT témakör keresztül (`$iothub/methods/POST/{method name}/`). Előfordulhat, hogy támogatjuk közvetlen módszerek a további eszközoldali hálózati protokollok a jövőben.

> [!NOTE]
> Az eszközön közvetlen metódus meghívásakor nevét és értékeit csak tartalmazhat US-ASCII nyomtatható alfanumerikus, kivéve a következő set: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Szinkron módszerek és vagy sikeres közvetlen vagy követően az időkorlát (alapértelmezett: 30 másodperces, állítható be 3600 másodperc). Közvetlen módszereket hasznos interaktív olyan esetekben, ahol azt szeretné, hogy az eszköz csak, ha az eszköz nem online és a fogadó parancsok, például egy világos telefonos bekapcsolásával jár el. Ezekben az esetekben meg szeretné tekinteni az azonnali sikeres vagy sikertelen volt, a felhőalapú szolgáltatás működhet-e az eredmény a lehető leghamarabb. Az eszköz néhány üzenettörzs metódus miatt előfordulhat, hogy vissza, de ez nem szükséges ehhez a metódushoz. Van a rendezés nem garantálja, vagy a metódushívások bármely párhuzamossági szemantikáját.

Közvetlen módszerek HTTPS csak a felhő oldalon, és a MQTT vagy az AMQP eszköz oldaláról.

A hasznos módszer kérelmeit és válaszait, egy JSON-dokumentuma legfeljebb 8 KB-os.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>A háttér-alkalmazásból közvetlen metódus
### <a name="method-invocation"></a>A metódushívás
Az eszközön a közvetlen módszer meghívásához HTTPS hívások alkotó:

* A *URI* eszközhöz tartozó (`{iot hub}/twins/{device id}/methods/`)
* A POST *módszer*
* *Fejlécek* , amely tartalmaz az engedélyezési, a kérelem azonosítója, tartalomtípus, és a tartalom kódolása
* A transzparens JSON *törzs* a következő formátumban:

   ```
   {
       "methodName": "reboot",
       "responseTimeoutInSeconds": 200,
       "payload": {
           "input1": "someInput",
           "input2": "anotherInput"
       }
   }
   ```

Időtúllépés másodpercben van. Ha időtúllépési nincs megadva, alapértelmezés szerint 30 másodperc.

### <a name="response"></a>Válasz
A háttér-alkalmazást, amely magában választ kap:

* *HTTP-állapotkód*, amellyel az IoT Hub érkező hibák, beleértve az eszközök 404 hibaüzenetet jelenleg nem csatlakoztatott
* *Fejlécek* , amely tartalmazza az ETag, a kérelem azonosítója, tartalomtípus, és a tartalom kódolása
* A JSON *törzs* a következő formátumban:

   ```
   {
       "status" : 201,
       "payload" : {...}
   }
   ```

   Mindkét `status` és `body` az eszköz által biztosított és a használt szeretne válaszolni az eszköz saját állapotkód és/vagy a leírását.

## <a name="handle-a-direct-method-on-a-device"></a>Kezeli az eszközön a közvetlen módszer
### <a name="method-invocation"></a>A metódushívás
Eszköz megkapja a MQTT témakör közvetlen metódusú kérelmeket:`$iothub/methods/POST/{method name}/?$rid={request id}`

A szervezet, amely az eszköz megkapja a következő formátumban kell megadni:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

A metóduskérelmek QoS 0.

### <a name="response"></a>Válasz
Az eszköz küld válaszokat `$iothub/methods/res/{status}/?$rid={request id}`, ahol:

* A `status` tulajdonság metódus végrehajtása a megadott eszköz állapotát.
* A `$rid` tulajdonsága az IoT-központ kapott metódushívás a kérelem azonosítója.

A szervezet az eszköz állítja be, és bármely állapota lehet.

## <a name="additional-reference-material"></a>További referenciaanyag
Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* [IoT-központok végpontjai] [ lnk-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvótákat, az IoT-központ szolgáltatás és a sávszélesség-szabályozási viselkedését történik, ha a szolgáltatás használatához.
* [Az Azure IoT eszköz és a szolgáltatás SDK-k] [ lnk-sdks] felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* [Az IoT-központ lekérdezési nyelv eszköz twins, feladatok és üzenet útválasztási] [ lnk-query] az IoT-központ lekérdezési nyelv segítségével adatok lekérését az IoT-központ az eszköz twins és feladatok ismertetése.
* [Az IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] IoT-központ támogatásával kapcsolatos további információkat biztosít a MQTT protokoll.

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik megtudta, hogyan közvetlen módszereket használja, a következő cikkben IoT Hub fejlesztői útmutató érdekelt esetleg:

* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Ha azt szeretné, hogy próbálja ki azokat a jelen cikkben ismertetett fogalmakat, esetleg megváltozása a következő IoT Hub-oktatóanyag:

* [Közvetlen módszerekkel][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
