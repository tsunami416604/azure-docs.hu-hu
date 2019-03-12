---
title: A Microsoft Azure IoT lehetőségei | Microsoft Docs
description: Válassza ki az Azure IoT-megoldás megvalósítása az Azure IoT Central, az IoT-megoldásgyorsítók vagy az IoT Hub használatával.
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 571c703609bac2e4414baf19edc39cf5e9f8820a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759776"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Az Azure IoT Central és az Azure IoT Suite lehetőségeinek összehasonlítása

A Microsoft Azure IoT Central és az Azure IoT kínálnak számos lehetőség közül választhat az IoT-megoldásának létrehozását. Ezek a lehetőségek közül választhat a különböző felhasználói igényekhez:

* [Az Azure IoT Central](overview-iot-central.md) olyan szolgáltatott (SaaS) szolgáltatottszoftver-megoldás, amely egy modellalapú megközelítéssel használja annak érdekében, hogy a vállalati szintű IoT-megoldások létrehozását anélkül, hogy nincs szükség a felhőmegoldás fejlesztési szaktudásra.

* [Az Azure IoT-megoldásgyorsítók](https://docs.microsoft.com/azure/iot-accelerators/) egy vállalati szintű gyűjteménye, amelyek [megoldásgyorsítók](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) (PaaS) szolgáltatás, amely segít az Azure platformra épülő gyorsíthatja fel az egyéni IoT-megoldások fejlesztését.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az Azure IoT Hub az alapvető Azure PaaS, amelyet az Azure IoT Central és az Azure IoT megoldásgyorsítók is használnak. Az IoT Hub több millió IoT-eszközök és a egy felhőalapú megoldás közötti megbízható és biztonságos kétirányú kommunikációt támogatja. Az IoT Hub IoT implementációs kihívások például megfelelnek nyújt segítséget:

* Nagy mennyiségű eszköz csatlakoztatása és felügyelete
* Nagy mennyiségű telemetriai adatok gyűjtése
* Parancs és vezérlés eszközök
* Eszközbiztonság kikényszerítése

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Az Azure IoT Central és az Azure IoT-megoldásgyorsítók összehasonlítása

Az Azure IoT-termék kiválasztása kritikus része az IoT-megoldás megtervezésének. Az IoT Hub egy önálló Azure-szolgáltatás, amely önmagában nem biztosít teljes körű IoT-megoldást. Használhatja az IoT Hub kiindulási pontként bármely IoT-megoldás esetében. Akkor is nem kell az Azure IoT-megoldásgyorsítók vagy az Azure IoT Central használatával használja az IoT hubot. Az IoT-megoldásgyorsítók és az Azure IoT Central is használják az IoT Hubot más Azure-szolgáltatások mellett.

Az alábbi táblázat összegzi az IoT-megoldásgyorsítók és az Azure IoT Central közötti lényeges különbségeket, hogy könnyebben kiválaszthassa a követelményeinek megfelelő rendszert:

|     | Azure IoT Central | Azure IoT-megoldásgyorsítók |
| --- | ----------- | --------- |
| Elsődleges használat                      | Részletes szolgáltatás-testreszabást nem igénylő, egyszerű IoT-megoldások piaci bevezetési idejének lerövidítéséhez.                                                    | A lehető legnagyobb rugalmasságot igénylő, egyéni IoT-megoldás fejlesztésének felgyorsításához.                                                                                                                             |
| Hozzáférés az alapul szolgáló PaaS szolgáltatásokhoz | SaaS. Mivel a szolgáltatás teljes körűen felügyelt megoldás, az alapul szolgáló szolgáltatások nem érhetőek el.                                                                                            | Hozzáféréssel rendelkezik az alapul szolgáló Azure-szolgáltatásokhoz, felügyelheti, vagy igény szerint lecserélheti őket.                                                                                                                    |
| Rugalmasság                        | Közepes. A beépített böngészőalapú felhasználói felületet használhatja a megoldásmodell és a felhasználói felület aspektusainak testreszabásához. Az infrastruktúra nem testreszabható, mert a különböző összetevők nem érhetőek el. | Magas. A mikroszolgáltatások kódja nyílt forráskódú, és semmilyen módon, igény szerint módosíthatja. Ezenkívül az üzembehelyezési infrastruktúra testreszabható.                                               |
| Ismeretek szintje                        | Alacsony. A megoldás testreszabásához modellezési ismeretek szükségesek. Nincs szükség kódolási ismeretekre.                                                                          | Közepesen magas. A megoldás háttérrendszerének testreszabásához Java vagy .NET képességek van szüksége. A megjelenítés testreszabásához JavaScript-ismeretek szükségesek.                                                                       |
| Első lépések élmény             | Alkalmazás- és eszközsablonok előre elkészített modelleket biztosítanak. Percek alatt üzembe helyezhető.                                                                                                  | Előre konfigurált megoldások implementálnak általános IoT-forgatókönyveket. Percek alatt üzembe helyezhető.                                                                                                                            |
| Díjszabás                            | Egyszerű, kiszámítható díjszabás.                                                                                                                           | Finomhangolhatja a szolgáltatásokat a költségek kézben tartásához.                                                                                                                                                            |

Melyik terméket használja az IoT-megoldás összeállításához függ:

* Az üzleti követelmények.
* A megoldás típusát kíván létrehozni.
* A szervezetében elérhető szaktudás a megoldás összeállításához és hosszú távú karbantartásához.

## <a name="next-steps"></a>További lépések

A kiválasztott termék és megközelítés alapján a javasolt következő lépések:

* **Az Azure IoT Central**: [Mi az az Azure IoT Central?](overview-iot-central.md)
* **IoT-megoldásgyorsítók**: [Mik az Azure IoT-megoldásgyorsítók?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **Az IoT Hub**: [Mi az Azure IoT Hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)