---
title: Vészhelyreállítás és földrajzi elosztás a Durable Functions – Azure
description: Ismerje meg a vészhelyreállítás és földrajzi elosztás Durable Functions szolgáltatásban.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d999350f309dbd2bf74bbb3d10e74feddf6ee602
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642381"
---
# <a name="disaster-recovery-and-geo-distribution"></a>Vészhelyreállítás és földrajzi elosztás

## <a name="overview"></a>Áttekintés
Az Azure Durable Functions összes állapot rendszer megőrzi az Azure Storage-ban. A [feladat hub](durable-functions-task-hubs.md) vezénylések használt Azure Storage-erőforrások logikai tárolója. Orchestrator, illetve a tevékenység funkciók tartoznak, a tevékenység egy központban csak is kapcsolatba egymással.
Az ismertetett forgatókönyvek javaslatot a központi telepítési beállítások növeli a rendelkezésre állás és a vész-helyreállítási tevékenység közbeni állásidő minimálisra csökkentése.
Fontos, és figyelje meg, hogy ezek a forgatókönyvek alapulnak aktív-passzív konfigurációt igényelnek, mivel azok megismerheti az Azure Storage használatát által. Ez a minta áll egy biztonsági mentési (passzív) függvény alkalmazások üzembe helyezése egy másik régióban. A TRAFFIC Manager figyelni fogja az elsődleges (aktív) függvényalkalmazást a rendelkezésre állás érdekében. Azt feladatátvételt hajt végre a biztonsági mentési függvényalkalmazás, ha az elsődleges meghibásodik. További információkért lásd: [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)a [prioritású forgalom-útválasztási módszer.](../../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- A javasolt aktív / passzív konfigurációt biztosítja, hogy egy ügyfél mindig tudni új vezénylések HTTP Protokollon keresztül aktiválhat. Következtében rendelkező megosztási ugyanazt a tárolót két függvényalkalmazások, azonban a háttérbeli feldolgozás őket, az azonos üzenetsorok üzeneteit versengő között szétosztani. Ez a konfiguráció a hozzáadott kimenő forgalmi költségek a másodlagos függvényalkalmazás tekintetében.
>- Az alapul szolgáló tárolási fiók és a feladat hub jönnek létre az elsődleges régióban, és mindkét függvényalkalmazások által megosztott.
>- Minden függvény üzembe helyezett alkalmazások redundantly, meg kell osztania az azonos függvény hozzáférési kulcsok esetén a HTTP Protokollon keresztül aktiválása folyamatban. A Functions futtatókörnyezete tesz közzé egy [felügyeleti API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , amely lehetővé teszi, hogy a felhasználó számára, hogy programozott módon hozzáadása, törlése és frissítése funkcióbillentyűk.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>1. forgatókönyv – elosztott terhelésű számítási megosztott tárolóval
Ha nem sikerül a számítási infrastruktúra Azure-ban, a függvényalkalmazás elérhetetlenné válhat. Az ilyen lehetőségét minimalizálása érdekében ebben a forgatókönyvben két, különböző régióban üzembe helyezett függvényalkalmazás használja. A TRAFFIC Manager van beállítva, észlelheti a problémákat az elsődleges függvényalkalmazást, és automatikusan átirányítja a forgalmat a másodlagos régió a függvényalkalmazáshoz. A függvényalkalmazás a ugyanazt az Azure Storage-fiókot és a feladat Hub fájlmegosztások. Ezért a függvényalkalmazások állapota nem vesznek el, és általában folytathatja a munkát. Miután állapotának visszaállítása az elsődleges régióba az Azure Traffic Manager a függvényalkalmazás érkező kérések útválasztására automatikusan elindul.


![1. forgatókönyv ábrázoló diagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Több előnye is van a központi telepítési forgatókönyv használata esetén:
- Ha nem sikerül a számítási infrastruktúra, munkahelyi folytathatja a nem a keresztül régió állapot elvesztése nélkül.
- A TRAFFIC Manager automatikusan az automatikus feladatátvételt a kifogástalan állapotú függvényalkalmazáshoz gondoskodik.
- A TRAFFIC Manager automatikusan újra létrehozza az elsődleges függvényalkalmazás-forgalom javítva lett a szolgáltatáskiesés megszüntetése után.

Azonban ez a forgatókönyv segítségével figyelembe vennie:
- A függvényalkalmazás egy dedikált App Service-csomag használatával lett telepítve, ha replikál a feladatátvétel a számítási infrastruktúra adatközpont keresztül növeli a költségeket.
- Ebben a forgatókönyvben a kimaradások, a számítási infrastruktúra ismerteti, de a storage-fiók továbbra is az a függvény alkalmazás meghibásodási pont lehet. Ha a Társzolgáltatás kimaradása, az alkalmazás romlik egy állásidőt.
- Ha a függvényalkalmazás feladatátvétel alatt áll, kell késések óta, hozzá fog férni a tárfiók régióját.
- A storage szolgáltatás eléréséhez egy másik régióban, ahol a szolgáltatás található a hálózati kimenő forgalom miatt költségesebb tekintetében.
- Ebben a forgatókönyvben a Traffic Manager függ. Figyelembe véve [Traffic Manager működése](../../traffic-manager/traffic-manager-how-it-works.md), lehet, amíg egy ügyfélalkalmazás, amely egy tartós függvény kell kérdeznie újra a függvény alkalmazás címe Traffic Managerből. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>2. forgatókönyv – elosztott terhelésű számítási regionális tárolóval
A fenti forgatókönyv csak a kis-és a számítási infrastruktúra hibája foglalkozik. Ha a társzolgáltatás nem sikerül, azt a függvényalkalmazás kimaradás eredményez.
A tartós függvények folyamatos működésének biztosítása érdekében ebben a forgatókönyvben minden régiótól, amelyhez a függvényalkalmazások telepített helyi tárfiókot használ.

![2. forgatókönyv ábrázoló diagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Ez a megközelítés az előző forgatókönyvben a fejlesztéseket:
- Ha a függvényalkalmazás nem sikerül, a Traffic Manager gondoskodik irányuló feladatátvétel a másodlagos régióba. Azonban mivel a függvényalkalmazás a saját tárfiók alapul, a tartós függvények továbbra is működni.
- Egy feladatátvétele során nincs további késleltetés a nem a keresztül régió, mivel a függvényalkalmazást, és a storage-fiókban elhelyezve.
- Hiba a tárolási réteg hibák miatt a tartós függvények, amelyek, viszont aktiválják a feladatátvétel átirányítás keresztül régióban található. Újra mivel a függvényalkalmazás és -tárolás régiónként elkülönített, a tartós függvények továbbra is működni fog.
 
Ebben a forgatókönyvben fontos szempontjai:
- A függvényalkalmazás egy dedikált App Service-csomag használatával lett telepítve, ha replikál a feladatátvétel a számítási infrastruktúra adatközpont keresztül növeli a költségeket.
- Jelenlegi állapotában nem átadta a feladatait, ami azt jelenti, hogy végrehajtása és alkulcsaihoz funkciók sikertelen lesz. Az ügyfélalkalmazásnak újrapróbálkozási/újraindítja a számítógépet a munka van.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>3. forgatókönyv – elosztott terhelésű számítási megosztott GRS tárolóval
Ebben a forgatókönyvben az első forgatókönyv, egy megosztott tárfiókot végrehajtási keresztül módosítás. A fő különbség az, hogy a tárfiók létrejött-e a georeplikáció engedélyezve van.
Funkcionálisan ebben a forgatókönyvben, 1. forgatókönyv ugyanazokat az előnyöket biztosít, de lehetővé teszi a további adatok helyreállítási előnyei:
- Georedundáns tárolás (GRS) és az írásvédett GRS (RA-GRS) tárfiók rendelkezésre állás maximalizálása érdekében.
- Ha egy régió szolgáltatáskimaradás, a storage szolgáltatás, a lehetőségek egyike, hogy az Adatközpont műveletei határozza meg, hogy storage kell végrehajtani a feladatátvételt a másodlagos régióba. Ebben az esetben hozzáférés irányítja transzparens módon georeplikált példányt, a storage-fiókot, felhasználói beavatkozás nélkül.
- Ebben az esetben a tartós függvények állapotának megőrzi az utolsó replikáció, a storage-fiók, néhány percenként legfeljebb.
Csakúgy, mint az egyéb forgatókönyvek a következők fontos szempontok:
- A replika feladatátvételről adatközponti operátor végzi el, és ez eltarthat egy ideig. Csak ezen idő a függvényalkalmazás romlani fog kimaradás.
- Nincs egy nagyobb költséget georeplikált tárfiókok használata esetében.
- GRS aszinkron módon történik. A legutóbbi tranzakciók némelyike elveszhet miatt a késés, a replikációs folyamat.

![3. forgatókönyv ábrázoló diagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>További lépések

További információ [tervezése magas rendelkezésre álló alkalmazások RA-GRS használatával](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
