---
title: Az Azure IoT Hub magas rendelkezésre állás és vészhelyreállítás recovery |} A Microsoft Docs
description: Az Azure és az IoT Hub szolgáltatásokat, amelyek segítségével hozhat létre magas rendelkezésre állású Azure IoT-megoldások vész-helyreállítási funkciókat ismerteti.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 992c42511f7bc9e9af71ff552ee91bc2472ebcf8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185703"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Az IoT Hub magas rendelkezésre állás és vészhelyreállítás helyreállítási
Azure-szolgáltatásként az IoT Hub magas rendelkezésre ÁLLÁS az Azure-régió szintjén, a megoldás által igényelt minden további munka nélkül használatával redundanciát biztosít. A Microsoft Azure platform olyan funkciókat, hozzon létre megoldásokat a vész-helyreállítási lehetőségei vagy régiók rendelkezésre állása érdekében is. Ha azt szeretné, adja meg a globális, régiók közötti magas rendelkezésre állás az eszközöket vagy felhasználókat, ezeket az Azure-beli Vészhelyreállítási funkciók előnyeit. A cikk [Azure üzleti folytonossági műszaki útmutatóját](../resiliency/resiliency-technical-guidance.md) üzletmenet-folytonosság és Vészhelyreállítás az Azure-ban a beépített szolgáltatásait ismerteti. A [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások] [ Disaster recovery and high availability for Azure applications] tanulmány architektúra útmutatást nyújt az Azure-alkalmazások magas rendelkezésre ÁLLÁS és Vészhelyreállítás megvalósítása stratégiák.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Mellett intra-régió magas rendelkezésre ÁLLÁSÚ az IoT Hub feladatátvételi mechanizmusok, felhasználói beavatkozás nélküli igénylő vész-helyreállítási valósítja meg. IoT Hub DR önálló indul, és a egy helyreállítási időre vonatkozó célkitűzés (RTO) 2 – 26 óra és a következő helyreállításipont-célkitűzések (Rpo):

| Funkció | HELYREÁLLÍTÁSI IDŐKORLÁT |
| --- | --- |
| A beállításjegyzék és kommunikációs műveleteket a szolgáltatás rendelkezésre állása |CName adatvesztés |
| Azonosító adataihoz az eszközidentitás-jegyzék |0 – 5 perc az adatvesztést |
| Az eszközről a felhőbe irányuló üzenetek |Az összes olvasatlan üzenetek elvesznek. |
| Műveletek figyelése üzenetek |Az összes olvasatlan üzenetek elvesznek. |
| Felhőből az eszközre irányuló üzenetek |0 – 5 perc az adatvesztést |
| Felhőből az eszközre visszajelzési üzenetsort |Az összes olvasatlan üzenetek elvesznek. |
| Eszközadatok ikereszköz |0 – 5 perc az adatvesztést |
| Feladatok szülő és eszköz |0 – 5 perc az adatvesztést |

## <a name="regional-failover-with-iot-hub"></a>Az IoT Hub regionális feladatátvétel
Egy teljes körű IoT-megoldások üzembe helyezési topológiák kezelése nem ez a cikk foglalkozik. A cikk ismerteti a *regionális feladatátvétel* üzemi modell magas rendelkezésre állás és vészhelyreállítás helyreállítás céljából.

Egy regionális feladatátvétel modellben a megoldás biztonsági célból futtatások elsősorban az egyik adatközpont-helyen. Egy másodlagos IoT hubot és a háttérrendszer egy másik adatközpontba helyen vannak telepítve. Az IoT hub az elsődleges adatközpont romlik a kimaradás vagy a hálózati kapcsolat az eszköz és az elsődleges adatközpont megszakad, ha az eszközök egy másodlagos végpontot használja. A megoldás rendelkezésre állási helyett egy adott régión belül tartózkodó régiók közötti feladatátvétel modell alkalmazásával növelheti. 

Magas szintű, az IoT hubbal, egy regionális feladatátvétel modell megvalósításához a következők szükségesek:

* **Egy másodlagos IoT hub és az Útválasztás logikai eszköz**: Ha az elsődleges régióban szolgáltatás megszakad, eszközöket kell elindítania, csatlakozás a másodlagos régióhoz. A legtöbb szolgáltatások állapotközpontú természetéből szokás megoldás segítségével a rendszergazdák a régiók közötti feladatátvétel folyamat aktiválásához. A folyamat kézben tarthassa az eszközökre, az új végpont kommunikálni a legjobb módszer az, hogy rendszeresen ellenőrzi őket egy *recepciószolgálata* szolgáltatásba az aktuális aktív végpontot. A recepciószolgálata szolgáltatás lehet egy webalkalmazást, amely replikálja, és elérhető marad DNS-átirányítás módszerrel (például [Azure Traffic Manager][Azure Traffic Manager]).
* **Identitás adatbázis replikációja**: használhatóvá válik, a másodlagos IoT hub tartalmaznia kell az összes eszközidentitások, amely a megoldás képes kapcsolódni. A megoldás kell megőrizni a biztonsági mentések georeplikált eszközidentitások, és feltölti őket a másodlagos IoT hub az eszközök aktív végpontja váltása előtt. Az IoT Hub eszköz identitás exportálási funkció akkor hasznos, ebben a környezetben. További információkért lásd: [az IoT Hub fejlesztői útmutató – eszközidentitás-jegyzék][IoT Hub developer guide - identity registry].
* **Logikai egyesítésével**: Ha az elsődleges régió újra elérhetővé válik, állapota és adatai, a másodlagos helyen létrehozott kell áttelepíteni az összes biztonsági másolatot az elsődleges régióba. Ez állapota és adatai többnyire kapcsolódnak eszközidentitások és -alkalmazás metaadatait, amelyet egyesíteni kell az elsődleges IoT hub és egyéb alkalmazásspecifikus tárolja az elsődleges régióban. Ebben a lépésben leegyszerűsítése idempotens műveleteket kell használnia. Idempotens műveletek minimalizálása érdekében a mellékhatásai modul, az események a végleges konzisztens terjesztési, illetve az ismétlődések vagy out soron kívüli kézbesíti az eseményeket. Emellett az alkalmazáslogika úgy kell megtervezni, elviselni esetleges inkonzisztenciákat vagy elavult "némileg" állapotú. Ez a helyzet akkor fordulhat elő, miatt a további szükséges időt a rendszer "javítandó" helyreállításipont-célkitűzések (RPO) alapján.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozásokból tudhat meg többet az Azure IoT hubbal:

* [Ismerkedés az IoT hubok (oktatóanyag)][lnk-get-started]
* [Mi az Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
