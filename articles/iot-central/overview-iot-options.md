---
title: A Microsoft Azure IoT lehetőségei | Microsoft Docs
description: Kiválaszthatja, hogyan valósítja meg Azure IoT-megoldását az Azure IoT Central, az IoT Suite vagy az IoT Hub segítségével.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: overview
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 8ed524e87f820f6c1e2e05da0bcbc7241bdd1ec1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201458"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Az Azure IoT Central és az Azure IoT Suite lehetőségeinek összehasonlítása

A Microsoft Azure IoT Central és az Azure IoT számos lehetőséget kínál az IoT-megoldások megvalósításához, amelyek mindegyike különböző felhasználói igényekhez lett igazítva:

* Az [Azure IoT-központ](overview-iot-central.md) egy olyan szolgáltatott szoftver (SaaS) típusú megoldás, amely egy modellalapú megközelítéssel teszi lehetővé vállalati szintű IoT-megoldások létrehozását úgy, hogy nincs szükség felhőmegoldás-fejlesztési szaktudásra.

* Az [Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/) [előre konfigurált megoldások](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions) vállalati szintű gyűjteménye, amely az egyéni IoT-megoldások fejlesztésének felgyorsítását lehetővé tévő szolgáltatásként nyújtott platformon (PaaS) alapul.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az Azure IoT Hub az az alapvető Azure PaaS, amelyet az Azure IoT Central és az Azure IoT Suite is használ. Az Azure IoT Hub megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz és egy felhőalapú megoldás között. Az IoT Hub segít a következő IoT implementációs kihívások leküzdésében:

* Nagy mennyiségű eszköz csatlakoztatása és felügyelete.
* Nagy mennyiségű telemetriaadat feldolgozása.
* Eszközök irányítása és vezérlése.
* Eszközbiztonság kikényszerítése.

## <a name="compare-azure-iot-central-and-azure-iot-suite"></a>Az Azure IoT Central és az Azure IoT Suite összehasonlítása

Az Azure IoT-termék kiválasztása kritikus része az IoT-megoldás megtervezésének. Az IoT Hub egy önálló Azure-szolgáltatás, amely önmagában nem biztosít teljes körű IoT-megoldást. Az IoT Hub bármely IoT-megoldás kiindulási pontjaként használható, és a használatához nincs szükség az Azure IoT Suite vagy az Azure IoT Central használatára. Az IoT Suite és az Azure IoT Central is használja az IoT Hubot, valamint más Azure-szolgáltatásokat. Az alábbi táblázat összegzi az IoT Suite és az Azure IoT Central közötti lényeges különbségeket, hogy könnyebben kiválaszthassa a követelményeinek megfelelő megoldást:

|     | Azure IoT Central | Azure IoT Suite |
| --- | ----------- | --------- |
| Elsődleges használat                      | Részletes szolgáltatás-testreszabást nem igénylő, egyszerű IoT-megoldások piaci bevezetési idejének lerövidítéséhez.                                                    | A lehető legnagyobb rugalmasságot igénylő, egyéni IoT-megoldás fejlesztésének felgyorsításához.                                                                                                                             |
| Hozzáférés az alapul szolgáló PaaS szolgáltatásokhoz | SaaS. Teljes körűen felügyelt megoldás, az alapul szolgáló szolgáltatások nem érhetőek el.                                                                                            | Hozzáféréssel rendelkezik az alapul szolgáló Azure-szolgáltatásokhoz, felügyelheti, vagy igény szerint lecserélheti őket.                                                                                                                    |
| Rugalmasság                        | Közepes. A beépített böngészőalapú felhasználói felületet használhatja a megoldásmodell és a felhasználói felület aspektusainak testreszabásához. Az infrastruktúra nem testreszabható, mert a különböző összetevők nem érhetőek el. | Magas. A mikroszolgáltatások kódja nyílt forráskódú, és tetszés szerint módosítható. Ezenkívül az üzembehelyezési infrastruktúra testreszabható.                                               |
| Ismeretek szintje                        | Alacsony. A megoldás testreszabásához modellezési ismeretek szükségesek. Nincs szükség kódolási ismeretekre.                                                                          | Közepes-magas. A megoldás háttérrendszerének testreszabásához Java- vagy .NET-ismeretek szükségesek. A megjelenítés testreszabásához JavaScript-ismeretek szükségesek.                                                                       |
| Felhasználói élmény az első lépések során             | Az alkalmazás- és eszközsablonok előre felépített modelleket biztosítanak. Percek alatt üzembe helyezhető.                                                                                                  | Előre konfigurált megoldások implementálnak általános IoT-forgatókönyveket. Percek alatt üzembe helyezhető.                                                                                                                            |
| Díjszabás                            | Egyszerű, kiszámítható díjszabás.                                                                                                                           | Finomhangolhatja a szolgáltatásokat a költségek kézben tartásához.                                                                                                                                                            |

A döntést, hogy végül melyik terméket használja az IoT-megoldás létrehozásához, a következők határozzák meg:

* Az üzleti követelmények.
* A létrehozni kívánt megoldás típusa
* A szervezetében elérhető szaktudás a megoldás összeállításához és hosszú távú karbantartásához.

## <a name="next-steps"></a>További lépések

A kiválasztott termék és megközelítés alapján a javasolt következő lépések:

* **Azure IoT-központ**: [Azure IoT-központ](overview-iot-central.md).
* **IoT Suite**: [Mik az Azure IoT előre konfigurált megoldásai?](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-are-preconfigured-solutions)
* **IoT Hub**: [Az Azure IoT Hub szolgáltatás áttekintése](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub).