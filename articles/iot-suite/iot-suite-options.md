---
title: "A Microsoft Azure IoT lehetőségei | Microsoft Docs"
description: "Kiválaszthatja, hogyan valósítja meg IoT-megoldását az Azure IoT Suite, a Microsoft IoT-központ vagy az Azure IoT Hub segítségével."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e00dd828ef24b9bc97665f0f5fe8d7e14f4e4630
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="compare-azure-iot-options"></a>Az Azure IoT lehetőségeinek összehasonlítása

Az [Azure és az eszközök internetes hálózata](iot-suite-what-is-azure-iot.md) című cikk egy, az alábbi rétegekkel rendelkező tipikus IoT-megoldást ismertet:

* Eszközök csatlakoztatása és felügyelete
* Adatfeldolgozás és -elemzés
* Megjelenítés és üzleti integráció

Ennek az architektúrának a megvalósítására az Azure IoT számos lehetőséget kínál a különböző felhasználói igényekhez:

* Az [Azure IoT Suite](index.md) [előre konfigurált megoldások](iot-suite-what-are-preconfigured-solutions.md) vállalati szintű gyűjteménye, amely az egyéni IoT-megoldások fejlesztésének felgyorsítását lehetővé tévő szolgáltatásként nyújtott platformon alapul.

* A [Microsoft IoT-központ](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) egy olyan SaaS megoldás, amely egy modellalapú megközelítéssel teszi lehetővé vállalati szintű IoT-megoldások létrehozását úgy, hogy nincs szükség felhőmegoldás-fejlesztési szaktudásra.

## <a name="azure-iot-hub"></a>Azure IoT Hub

Az Azure IoT Hub az alapvető Azure-szolgáltatásként nyújtott platform, amelyet a Microsoft IoT-központ és az Azure IoT Suite is használ. Az Azure IoT Hub megbízható és biztonságos kétirányú kommunikációt tesz lehetővé több millió IoT-eszköz és egy felhőalapú megoldás között. Az IoT Hub segít a következő IoT implementációs kihívások leküzdésében:

* Nagy mennyiségű eszköz csatlakoztatása és felügyelete.
* Nagy mennyiségű telemetriaadat feldolgozása.
* Eszközök irányítása és vezérlése.
* Eszközbiztonság kikényszerítése.

## <a name="compare-azure-iot-suite-and-microsoft-iot-central"></a>Az Azure IoT Suite és a Microsoft IoT-központ összehasonlítása

Az Azure IoT-termék kiválasztása kritikus része az IoT-megoldás megtervezésének. Az IoT Hub egy önálló Azure-szolgáltatás, amely önmagában nem biztosít teljes körű IoT-megoldást. Az IoT Hub bármely IoT-megoldás kiindulási pontjaként használható, és a használatához nincs szükség az Azure IoT Suite vagy a Microsoft IoT-központ használatára. Az Azure IoT Suite és a Microsoft IoT-központ is használja az IoT Hubot más Azure-szolgáltatások mellett. Az alábbi táblázat összegzi az Azure IoT Suite és a Microsoft IoT-központ közötti lényeges különbségeket, hogy könnyebben kiválaszthassa a követelményeinek megfelelő rendszert:

|                        | Azure IoT Suite | Microsoft IoT-központ |
| ---------------------- | --------- | ----------- |
| Elsődleges használat | A lehető legnagyobb rugalmasságot igénylő, egyéni IoT-megoldás fejlesztésének felgyorsításához. | Részletes szolgáltatás-testreszabást nem igénylő, egyszerű IoT-megoldások piaci bevezetési idejének lerövidítéséhez. |
| Hozzáférés az alapul szolgáló PaaS szolgáltatásokhoz          | Hozzáféréssel rendelkezik az alapul szolgáló Azure-szolgáltatásokhoz, felügyelheti, vagy igény szerint lecserélheti őket. | SaaS. Teljes körűen felügyelt megoldás, az alapul szolgáló szolgáltatások nem érhetőek el. |
| Rugalmasság            | Magas. A mikroszolgáltatások kódja nyílt forráskódú, és tetszés szerint módosítható. Ezenkívül az üzembehelyezési infrastruktúra testreszabható.| Közepes. A beépített böngészőalapú felhasználói felületet használhatja a megoldásmodell és a felhasználói felület aspektusainak testreszabásához. Az infrastruktúra nem testreszabható, mert a különböző összetevők nem érhetőek el.|
| Ismeretek szintje                 | Közepes-magas. A megoldás háttérrendszerének testreszabásához Java- vagy .NET-ismeretek szükségesek. A megjelenítés testreszabásához JavaScript-ismeretek szükségesek. | Alacsony. A megoldás testreszabásához modellezési ismeretek szükségesek. Nincs szükség kódolási ismeretekre. |
| Felhasználói élmény az első lépések során | Előre konfigurált megoldások implementálnak általános IoT-forgatókönyveket. Percek alatt üzembe helyezhető. | Sablonok előre felépített modelleket biztosítanak. Percek alatt üzembe helyezhető. |
| Díjszabás                | Finomhangolhatja a szolgáltatásokat a költségek kézben tartásához. | Egyszerű, kiszámítható díjszabás. |

A döntést, hogy végül melyik terméket használja az IoT-megoldás létrehozásához, a következők határozzák meg:

* Az üzleti követelmények.
* A létrehozni kívánt megoldás típusa
* A szervezetében elérhető szaktudás a megoldás összeállításához és hosszú távú karbantartásához.

## <a name="next-steps"></a>Következő lépések

A kiválasztott termék és megközelítés alapján a javasolt következő lépések:

* **Azure IoT Suite**: [Mik az Azure IoT előre konfigurált megoldásai?](iot-suite-what-are-preconfigured-solutions.md)
* **Microsoft IoT-központ**: [Microsoft IoT-központ](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **IoT Hub**: [Az Azure IoT Hub szolgáltatás áttekintése](../iot-hub/iot-hub-what-is-iot-hub.md).
