---
title: IoT Plug and Play Preview modellfelderítés megvalósítása | Microsoft dokumentumok
description: Megoldásfejlesztőként ismerje meg, hogyan valósíthatja meg az IoT Plug and Play modellfelderítést a megoldásában.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 94e5e6fb826528aab1a358d44cb51bfcd6219032
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673595"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>IoT Plug and Play előzetes verziómodell-felderítés megvalósítása IoT-megoldásban

Ez a cikk bemutatja, hogyan valósíthatja meg az IoT Plug and Play preview modellfelderítést egy IoT-megoldásban, mint megoldásfejlesztő.  Az IoT Plug and Play modellfelderítés azt ismerteti, hogy az IoT Plug and Play-eszközök hogyan azonosítják a támogatott képességmodelleket és -felületeket, és hogyan olvassa le az IoT-megoldások ezeket a képességmodelleket és felületeket.

Az IoT-megoldásoknak két fő kategóriája van: a célorientált megoldások, amelyek az IoT Plug and Play eszközök ismert készletével működnek, és a modellalapú megoldások, amelyek bármilyen IoT Plug and Play eszközzel működnek.

Ez a koncepciócikk ismerteti, hogyan valósítható meg a modellfelderítés mindkét típusú megoldás.

## <a name="model-discovery"></a>Modellfelderítés

Amikor egy IoT Plug and Play-eszköz először csatlakozik az IoT hubhoz, modelladatokat azonosító telemetriai üzenetet küld. Ez az üzenet tartalmazza az eszköz által megvalósított összeköttetések azonosítóit. Ahhoz, hogy a megoldás működjön az eszközzel, meg kell oldania ezeket az azonosítókat, és le kell kérnie az egyes összeköttetések definícióit.

Az IoT Plug and Play eszköz által az eszközkiépítési szolgáltatás (DPS) segítségével a hubhoz való csatlakozássorán az alábbi lépéseket kell megtenni:

1. Ha az eszköz be van kapcsolva, csatlakozik a DPS globális végpontjához, és az engedélyezett módszerek egyikével hitelesíti magát.
1. A DPS ezután hitelesíti az eszközt, és megkeresi azt a szabályt, amely megmondja, hogy melyik IoT hubhoz rendelje az eszközt. A DPS ezután regisztrálja az eszközt az adott elosztóval.
1. A DPS egy IoT Hub-kapcsolati karakterláncot ad vissza az eszköznek.
1. Az eszköz ezután egy felderítési telemetriai üzenetet küld az IoT Hub. A felderítéstelemetriai üzenet az eszköz által megvalósított összeköttetések azonosítóit tartalmazza.
1. Az IoT Plug and Play eszköz most már készen áll az IoT hubot használó megoldással való használatra.

Ha az eszköz közvetlenül csatlakozik az IoT hubhoz, az eszközkódba ágyazott kapcsolati karakterlánc használatával csatlakozik. Az eszköz ezután egy felderítési telemetriai üzenetet küld az IoT Hub.

Tekintse meg a [ModelInformation](concepts-common-interfaces.md) felület et, ha többet szeretne megtudni a modelladatok telemetriai üzenetéről.

### <a name="purpose-built-iot-solutions"></a>Célirányosan készített IoT-megoldások

A célirányosan létrehozott IoT-megoldás az IoT Plug and Play eszközképességi modellek és interfészek ismert készletével működik.

A megoldáshoz előre csatlakozó eszközök képességmodelljével és felületeivel fog rendelkezni. A megoldás előkészítéséhez kövesse az alábbi lépéseket:

1. Tárolja a felület JSON fájlokat egy [modell tárházban,](./howto-manage-models.md) ahol a megoldás tudja olvasni őket.
1. Írási logika az IoT-megoldásban a várt IoT Plug and Play képességmodellek és felület alapján.
1. Iratkozzon fel a megoldás által használt IoT hubról érkező értesítésekre.

Amikor értesítést kap egy új eszközkapcsolatról, kövesse az alábbi lépéseket:

1. Olvassa el a felderítéstelemetriai üzenetet a képességmodell és az eszköz által megvalósított felületek azonosítóinak lekéréséhez.
1. Hasonlítsa össze a képességmodell azonosítóját az előre tárolt képességmodellek azonosítóival.
1. Most már tudja, hogy milyen típusú eszköz van csatlakoztatva. Használja a korábban írt logikát, hogy a felhasználók megfelelően kommunikálhassanak az eszközzel.

### <a name="model-driven-solutions"></a>Modellvezérelt megoldások

A modellalapú IoT-megoldások bármilyen IoT Plug and Play eszközzel működhetnek. Egy modell alapú IoT-megoldás létrehozása összetettebb, de a előnye, hogy a megoldás a jövőben hozzáadott eszközökkel működik.

Modellalapú IoT-megoldás létrehozásához logikát kell építenie az IoT Plug and Play felület primitívek: telemetriai adatok, tulajdonságok és parancsok alapján. Az IoT-megoldás logikája egy eszközt jelöl több telemetriai, tulajdonság- és parancsképességek kombinálásával.

A megoldásnak elő kell fizetnie az általa használt IoT hubról érkező értesítésekre is.

Amikor a megoldás értesítést kap egy új eszközkapcsolatról, kövesse az alábbi lépéseket:

1. Olvassa el a felderítéstelemetriai üzenetet a képességmodell és az eszköz által megvalósított felületek azonosítóinak lekéréséhez.
1. Minden azonosítóhoz olvassa el a teljes JSON-fájlt az eszköz képességeinek megkereséséhez.
1. Ellenőrizze, hogy minden kapcsolat jelen van-e a megoldás által korábban lekért JSON-fájlok tárolására épített gyorsítótárakban.
1. Ezután ellenőrizze, hogy az azonosítóval rendelkező felület szerepel-e a nyilvános modelltárházban. További információ: [Public model repository](howto-manage-models.md).
1. Ha a felület nem található a nyilvános modell tárházban, próbálja meg keresni a megoldás által ismert bármely vállalati modell tárházban. A vállalati modelltártárház eléréséhez kapcsolati karakterláncra van szükség. További információ: [Vállalati modelltárház.](howto-manage-models.md)
1. Ha nem találja az összes felületet sem a nyilvános modell tárházban, sem egy vállalati modell tárházban, ellenőrizheti, hogy az eszköz képes-e biztosítani a felület definícióját. Az eszköz megvalósíthatja a szabványos [ModelDefinition](concepts-common-interfaces.md) felületet, hogy információkat tegyen közzé arról, hogyan lehet beolvasni a felületfájlokat egy paranccsal.
1. Ha megtalálta a JSON fájlokat az eszköz által megvalósított minden felülethez, akkor felsorolhatja az eszköz képességeit. Használja a korábban írt logikát, hogy a felhasználók kommunikálhassanak az eszközzel.
1. Bármikor meghívhatja a digitális twins API-t a képességmodell-azonosító és az eszköz kapcsolatazonosítóinak lekéréséhez.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az IoT-megoldás modellfelderítésével, tudjon meg többet az [Azure IoT Platformról,](overview-iot-plug-and-play.md) hogy más képességeket is kihasználhasson a megoldáshoz.
