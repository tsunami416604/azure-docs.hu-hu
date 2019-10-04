---
title: A IoT megvalósítása Plug and Play előnézeti modell felderítése | Microsoft Docs
description: Megoldás fejlesztőként megismerheti, hogyan valósítható meg a IoT Plug and Play modell felderítése a megoldásban.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: c37446fd5a0cdc986044405a9aa3da32462d9c04
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114277"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>A IoT Plug and Play előnézeti modell felderítésének implementálása egy IoT-megoldásban

Ez a cikk azt ismerteti, hogyan lehet megoldás-fejlesztőként megvalósítani a IoT Plug and Play előnézeti modell felderítését egy IoT-megoldásban.  A IoT Plug and Play a modell felderítése, hogy a IoT Plug and Play-eszközök hogyan azonosítják a támogatott képességi modelljeit és felületeit, valamint azt, hogy egy IoT-megoldás hogyan kéri le ezeket a képességeket

A IoT-megoldás két nagy kategóriája létezik: olyan célzott megoldás, amely a IoT Plug and Play-eszközök ismert készletével működik együtt, valamint olyan modell-vezérelt megoldásokkal, amelyek bármilyen IoT Plug and Play eszközzel működnek.

Ez a fogalom azt ismerteti, hogyan valósítható meg a modell felderítése mindkét típusú megoldásban.

## <a name="model-discovery"></a>Modellfelderítés

Amikor egy IoT Plug and Play eszköz először csatlakozik az IoT hub-hoz, egy telemetria-üzenetet küld. Ez az üzenet tartalmazza az eszköz által megvalósított felületek azonosítóit. Ahhoz, hogy a megoldás működjön az eszközzel, fel kell oldania ezeket az azonosítókat, és le kell kérnie az egyes illesztőfelületek definícióit.

Az alábbi lépéseket követve megtekintheti a IoT Plug and Play eszközét, amikor az eszköz kiépítési szolgáltatását (DPS) használja egy hubhoz való csatlakozáshoz:

1. Ha az eszköz be van kapcsolva, a globális végponthoz csatlakozik a DPS-hez, és a hitelesítés az engedélyezett módszerek egyikével történik.
1. A DPS ezután hitelesíti az eszközt, és megkeresi azt a szabályt, amely közli, hogy az eszköz melyik IoT-hubhoz van hozzárendelve. A DPS ezután regisztrálja az eszközt az adott hubhoz.
1. A DPS egy IoT Hub kapcsolódási karakterláncot ad vissza az eszköznek.
1. Az eszköz ezután egy felderítési telemetria üzenetet küld a IoT Hubnak. A felderítési telemetria üzenet tartalmazza az eszköz által megvalósított felületek azonosítóit.
1. A IoT Plug and Play eszköz most már készen áll az IoT hub-t használó megoldással való együttműködésre.

Ha az eszköz közvetlenül csatlakozik az IoT hub-hoz, az az eszköz kódjába ágyazott kapcsolati sztringet használva csatlakozik. Az eszköz ezután egy felderítési telemetria üzenetet küld a IoT Hubnak.

Tekintse meg a [ModelInformation](concepts-common-interfaces.md) felületét, és ismerkedjen meg a modell információi telemetria üzenettel.

### <a name="purpose-built-iot-solutions"></a>Célra épülő IoT-megoldások

A IoT-megoldás egy ismert IoT-készlettel működik, amely az eszköz képességeinek és felületének ismert együttesét Plug and Play.

Az eszközhöz a megoldáshoz való csatlakozáshoz szükséges képesség modell és felületek tartoznak. A megoldás előkészítéséhez kövesse az alábbi lépéseket:

1. Tárolja az Azure-beli interfész JSON-fájljait egy olyan helyen, ahol a megoldás olvasni tudja őket.
1. A IoT-megoldásban a várt IoT Plug and Play képesség modellek és felületek alapján írhat logikát.
1. Fizessen elő a megoldás által használt IoT hub értesítéseire.

Amikor értesítést kap egy új eszköz-kapcsolatban, kövesse az alábbi lépéseket:

1. Olvassa el a felderítési telemetria üzenetet az eszköz által megvalósított képesség modell és felületek azonosítóinak beolvasásához.
1. Hasonlítsa össze a képesség modell AZONOSÍTÓját az idő előtt tárolt képességi modellek azonosítói alapján.
1. Most már tudja, milyen típusú eszköz kapcsolódott. A korábban írt logikával lehetővé teheti a felhasználók számára, hogy megfelelően használják az eszközt.

### <a name="model-driven-solutions"></a>Modellen alapuló megoldások

A modellen alapuló IoT-megoldások bármilyen IoT Plug and Play eszközzel működhetnek. A modellen alapuló IoT-megoldás létrehozása összetettebb, de az előnye, hogy a megoldás a jövőben hozzáadott eszközökkel működik.

A modellre épülő IoT-megoldás létrehozásához létre kell hoznia egy logikát a IoT Plug and Play felületi primitívek esetében: telemetria, Properties és parancsok. A IoT-megoldás logikája olyan eszközt képvisel, amely több telemetria, tulajdonságot és parancs-képességet egyesít.

A megoldásnak az általa használt IoT hub értesítéseire is elő kell fizetnie.

Ha a megoldás értesítést kap egy új eszköz kapcsolatáról, kövesse az alábbi lépéseket:

1. Olvassa el a felderítési telemetria üzenetet az eszköz által megvalósított képesség modell és felületek azonosítóinak beolvasásához.
1. Az egyes AZONOSÍTÓk esetében olvassa el a teljes JSON-fájlt az eszköz képességeinek megkereséséhez.
1. Ellenőrizze, hogy vannak-e olyan gyorsítótárban található összes interfész, amelyet a megoldás által korábban lekért JSON-fájlok tárolására készített.
1. Ezután ellenőrizze, hogy az adott AZONOSÍTÓval rendelkező felület szerepel-e a nyilvános modell adattárában. További információ: [nyilvános modell tárháza](howto-manage-models.md).
1. Ha az illesztőfelület nem szerepel a nyilvános modell adattáran, próbálja meg a céges modellekben a megoldáshoz tartozó adattárakban keresni. A vállalati modell tárházának eléréséhez kapcsolati sztringre van szükség. További információ: céges [modell tárháza](howto-manage-models.md).
1. Ha nem találja az összes illesztőfelületet a nyilvános modell adattárában vagy a vállalati modell adattárában, megtekintheti, hogy az eszköz képes-e az illesztőfelület-definíció megadására. Az eszközök a szabványos [ModelDefinition](concepts-common-interfaces.md) felületet implementálják, hogy az adott paranccsal hogyan lehet lekérdezni a csatoló fájljait egy parancs használatával.
1. Ha a JSON-fájlokat az eszköz által megvalósított minden egyes csatolóhoz megtalálta, akkor az eszköz képességeit enumerálhatja. A korábban írt logikával engedélyezheti a felhasználók számára az eszköz használatát.
1. A digitális Twins API-t bármikor meghívhatja, hogy beolvassa az eszközre vonatkozó képesség-modell AZONOSÍTÓját és a csatoló azonosítóit.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Model Discovery egy IoT-megoldását, tudjon meg többet az [Azure IoT platformról](overview-iot-plug-and-play.md) a megoldás más képességeinek kihasználása érdekében.
