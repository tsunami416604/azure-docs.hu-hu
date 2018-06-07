---
title: Az Azure IoT Hub magas rendelkezésre állási és vészhelyreállítási recovery |} Microsoft Docs
description: Az Azure és az IoT-központ szolgáltatásait ismerteti, hozhat létre magas rendelkezésre állású Azure IoT-megoldások a vész helyreállítási képességek segítségével.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 428209defa554599c01789e6f2a8b62f155b0f2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633706"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Az IoT-központ magas rendelkezésre állás és katasztrófa-helyreállítás
Az Azure-szolgáltatások, mint az IoT-központ biztosítja a magas rendelkezésre ÁLLÁS létszámcsökkentések használata az Azure-régiót szinten bármit tennie kellene a megoldás által igényelt nélkül. A Microsoft Azure platform olyan vész-helyreállítási képességek vagy kereszt-régiónkénti elérhetőség megoldások létrehozásához szolgáltatásokat is tartalmaz. Ha lehetővé szeretné tenni globális, kereszt-régió eszközöket vagy felhasználókat, a magas rendelkezésre állású Azure vész-Helyreállítási felhasználásokhoz előnyeit. A cikk [Azure üzleti folytonossági műszaki útmutatót](../resiliency/resiliency-technical-guidance.md) az Azure-ban az üzletmenet folytonosságát és vész-Helyreállítási beépített funkcióit mutatja be. A [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások] [ Disaster recovery and high availability for Azure applications] papír architektúra-útmutatót biztosít az Azure-alkalmazások elérése a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási stratégiát.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Mellett intra-régió magas rendelkezésre ÁLLÁSÚ az IoT-központ feladatátvételi mechanizmusok, felhasználói beavatkozás nélküli igénylő vész-helyreállítási valósítja meg. Az IoT Hub vész-Helyreállítási önálló kezdeményezett, és a helyreállítási idő célkitűzése (RTO) 2 – 26 óra, és a következő helyreállítási helyreállításipont-céljai (Rpo):

| Funkció | A HELYREÁLLÍTÁSI IDŐKORLÁT |
| --- | --- |
| A beállításjegyzék és a kommunikációs műveletek a szolgáltatás rendelkezésre állása |CName adatvesztés |
| Azonosító adataihoz az identitásjegyzékhez |0-5 perc adatvesztés |
| Az eszközről a felhőbe irányuló üzenetek |Az összes olvasatlan üzenetek elvesznek. |
| Műveletek üzenetek figyelése |Az összes olvasatlan üzenetek elvesznek. |
| Felhő-eszközre küldött üzenetek |0-5 perc adatvesztés |
| Felhő eszközre a visszajelzési üzenetsor |Az összes olvasatlan üzenetek elvesznek. |
| A két eszközadatok |0-5 perc adatvesztés |
| Szülő- és eszköz feladatok |0-5 perc adatvesztés |

## <a name="regional-failover-with-iot-hub"></a>Az IoT-központ regionális feladatátvétel
Az IoT-megoldások üzembe helyezési topológiájához teljes kezelés van ez a cikk hatókörén kívül. A cikk ismerteti a *regionális feladatátvétel* üzembe helyezési modellel magas rendelkezésre állás és a katasztrófa utáni helyreállítás céljából.

Regionális feladatátvétel modellben a megoldás elsősorban az egyik datacenter helyen end futtatása biztonsági másolatot. Egy másodlagos IoT-központ és háttér datacenter egy másik helyen vannak telepítve. Az IoT-központ az elsődleges adatközpont kimaradás szenved, vagy az elsődleges adatközpont az eszközről a hálózati kapcsolat megszakad, ha az eszközök másodlagos szolgáltatásvégpont használnak. A megoldás rendelkezésre állási javíthatja a alkalmazásával segítse a kereszt-régió feladatátvételi modell helyett egyetlen régión belül marad. 

Magas szinten az IoT-központ, egy regionális feladatátvétel modell megvalósításához szüksége a következők:

* **Egy másodlagos IoT hub és útválasztási logikai eszköz**: Ha az elsődleges régióban szolgáltatás megszakad, eszközöket kell elindítania, a másodlagos régióba csatlakozik. A legtöbb szolgáltatások állapot-kompatibilis jellegéből, esetében gyakori, a megoldás rendszergazdák a helyek közötti régió feladatátvételi folyamat indításához. A legjobb módszer az eszközökre, az új végpont kommunikáció során a folyamat irányítás megőrzéséhez, hogy rendszeresen ellenőrzi azokat egy *segítõ* szolgáltatás az aktuális active végpont. A segítõ szolgáltatás lehet van replikálva, és elérhető-e tartani webalkalmazás DNS-átirányítás technikák segítségével (például az [Azure Traffic Manager][Azure Traffic Manager]).
* **Identitás beállításjegyzék replikációs**: is használható, a másodlagos IoT-központ tartalmaznia kell az összes eszköz identitást, amely csatlakozni tudna a megoldás. A megoldás kell tartani a biztonsági mentések georeplikált eszköz identitások, és a másodlagos IoT hub előtti feltöltésükhöz Váltás az eszközök aktív végpontja. Az IoT Hub eszköz identitása exportálási funkció akkor hasznos, ebben a környezetben. További információkért lásd: [IoT Hub fejlesztői útmutató - identitásjegyzékhez][IoT Hub developer guide - identity registry].
* **Az egyesítés logika**: Ha az elsődleges régióban ismét elérhetővé válik, állapotának és adatainak a másodlagos helyen létrehozott kell áttelepíteni az összes biztonsági az elsődleges régióban. Ez állapotának és adatainak többnyire kapcsolódnak eszköz identitások és az alkalmazás metaadatait, amelyet egyesíteni kell az elsődleges IoT hub és más alkalmazás-specifikus tárolja az elsődleges régióban. Ez a lépés leegyszerűsítése idempotent műveletek kell használnia. Az Idempotent műveletek minimalizálása érdekében a mellékhatással működő a végleges konzisztens terjesztési események, illetve a duplikálás vagy események soron kézbesítését. Emellett az alkalmazáslogikát úgy kell megtervezni, tűrését lehetséges inkonzisztenciát vagy "némileg" elavult állapotban. Ez a helyzet akkor fordulhat elő, miatt a további szükséges időt a rendszer "javítandó" helyreállításipont-céljai (RPO) alapján.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub:

* [Ismerkedés az IoT-központok (Útmutató)][lnk-get-started]
* [Mi az Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
