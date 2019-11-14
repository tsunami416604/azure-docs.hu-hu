---
title: A Microsoft Azure IoT lehetőségei | Microsoft Docs
description: Válassza ki, hogyan valósítja meg az Azure IoT-megoldást az Azure IoT Central, a IoT megoldás-gyorsító vagy a IoT Hub használatával.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 2ec6e1d00d331a7bb8dd7182dd4b0c91dd3d25a7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049093"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Az Azure IoT Central és az Azure IoT Suite lehetőségeinek összehasonlítása

Microsoft Azure IoT Central és az Azure IoT számos lehetőséget kínál az IoT-megoldások létrehozására. Ezek a lehetőségek az ügyfelek különböző igényeihez szükségesek:

* Az [Azure IoT Central](overview-iot-central.md) egy olyan IoT-alkalmazási platform, amely modellen alapuló megközelítést használ a nagyvállalati szintű IoT megoldások létrehozásához anélkül, hogy szakértelemre lenne szükség a felhőalapú megoldások fejlesztésében.

* Az [Azure IoT megoldás](https://docs.microsoft.com/azure/iot-accelerators/) -gyorsítók olyan nagyvállalati szintű [megoldás-gyorsítók](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) , amelyek az Azure platform szolgáltatásként (Péter) alapuló, az egyéni IoT-megoldások fejlesztésének felgyorsítását lehetővé teszik.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az Azure IoT Hub az alapvető Azure PaaS, amelyet az Azure IoT Central és az Azure IoT megoldásgyorsítók is használnak. IoT Hub támogatja a megbízható és biztonságos kétirányú kommunikációt több millió IoT-eszköz és egy felhőalapú megoldás között. A IoT Hub segít megfelelni a IoT-megvalósítási kihívásoknak, például:

* Nagy mennyiségű eszköz csatlakoztatása és kezelése
* Nagy mennyiségű telemetria-feldolgozás
* Eszközök parancsai és vezérlése
* Eszköz biztonságának kényszerítése

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Az Azure IoT Central és az Azure IoT-megoldásgyorsítók összehasonlítása

Az Azure IoT-termék kiválasztása kritikus része az IoT-megoldás megtervezésének. Az IoT Hub egy önálló Azure-szolgáltatás, amely önmagában nem biztosít teljes körű IoT-megoldást. A IoT Hubt kiindulási pontként használhatja bármely IoT-megoldáshoz. A IoT Hub használatához nem szükséges az Azure IoT megoldás-gyorsító vagy az Azure IoT Central használata. Az IoT-megoldásgyorsítók és az Azure IoT Central is használják az IoT Hubot más Azure-szolgáltatások mellett.

Az alábbi táblázat összegzi az IoT-megoldásgyorsítók és az Azure IoT Central közötti lényeges különbségeket, hogy könnyebben kiválaszthassa a követelményeinek megfelelő rendszert:

|     | Azure IoT Central | Azure IoT-megoldásgyorsítók |
| --- | ----------- | --------- |
| Elsődleges használat                      | Részletes szolgáltatás-testreszabást nem igénylő, egyszerű IoT-megoldások piaci bevezetési idejének lerövidítéséhez.                                                    | A lehető legnagyobb rugalmasságot igénylő, egyéni IoT-megoldás fejlesztésének felgyorsításához.                                                                                                                             |
| Hozzáférés az alapul szolgáló PaaS szolgáltatásokhoz | Mivel ez egy teljes körűen felügyelt megoldás, az alapul szolgáló szolgáltatások nincsenek kitéve.                                                                                            | Hozzáféréssel rendelkezik az alapul szolgáló Azure-szolgáltatásokhoz, felügyelheti, vagy igény szerint lecserélheti őket.                                                                                                                    |
| Rugalmasság                        | Közepes. A beépített böngészőalapú felhasználói felületet használhatja a megoldásmodell és a felhasználói felület aspektusainak testreszabásához. Az infrastruktúra nem testreszabható, mert a különböző összetevők nincsenek kitéve. | Magas. A szolgáltatásprofil programkódja nyílt forráskódú, és bármilyen módon módosítható, ahogy az illik. Ezenkívül az üzembehelyezési infrastruktúra testreszabható.                                               |
| Ismeretek szintje                        | Alacsony. A megoldás testreszabásához modellezési ismeretek szükségesek. Nincs szükség kódolási ismeretekre.                                                                          | Közepes – magas. A megoldás hátterének testre szabásához Java-vagy .NET-ismeretekre van szükség. A megjelenítés testreszabásához JavaScript-ismeretek szükségesek.                                                                       |
| Első lépések – tapasztalat             | Az alkalmazások sablonjai és sablonjai előre elkészített modelleket biztosítanak. Percek alatt üzembe helyezhető.                                                                                                  | Előre konfigurált megoldások implementálnak általános IoT-forgatókönyveket. Percek alatt üzembe helyezhető.                                                                                                                            |
| Díjszabás                            | Egyszerű, kiszámítható díjszabás.                                                                                                                           | Finomhangolhatja a szolgáltatásokat a költségek kézben tartásához.                                                                                                                                                            |

A IoT-megoldás felépítéséhez használt termék a következőktől függ:

* Az üzleti követelmények.
* A létrehozni kívánt megoldás típusa.
* A szervezetében elérhető szaktudás a megoldás összeállításához és hosszú távú karbantartásához.

## <a name="next-steps"></a>Következő lépések

A kiválasztott termék és megközelítés alapján a javasolt következő lépések:

* **Azure IoT Central**: [Mi az az Azure IoT Central?](overview-iot-central.md)
* **IoT megoldás-gyorsítók**: [Mik az Azure IoT megoldás-gyorsítók?](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **IoT hub**: [Mi az az Azure IoT hub?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)