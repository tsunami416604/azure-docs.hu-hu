---
title: Vész-helyreállítási és földrajzi terjesztési tartós funkciókkal – Azure
description: Ismerje meg a vész-helyreállítási és földrajzi terjesztési tartós funkciókkal.
services: functions
author: MS-Santi
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 8eb42a60045304416ec6aa1099a84b1e264c692d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="disaster-recovery-and-geo-distribution"></a>Vész-helyreállítási és földrajzi terjesztési

## <a name="overview"></a>Áttekintés
Azure tartós funkciókkal minden állapotát az Azure Storage megőrződjenek. A [feladat hub](durable-functions-task-hubs.md) álló üzenettípusok összehangolását használt Azure Storage-erőforrások logikai tárolója. Az orchestrator- és tevékenységkezelési funkciók tevékenység egy központban tartoznak, csak is kapcsolatba egymással.
Az ismertetett forgatókönyvek javaslatot központi telepítési beállítások magasabb rendelkezésre állását, és minimalizálják az állásidőt vész-helyreállítási tevékenységek során.
Fontos, hogy ezek a forgatókönyvek alapuló aktív-passzív konfigurációk, mivel azok is vezérelheti az Azure Storage használatát láthatja. Ebben a mintában áll egy másik régióban üzembe helyezni a egy biztonsági mentési (passzív) funkciót alkalmazást. A TRAFFIC Manager figyeli az elsődleges (aktív) függvény alkalmazást, a rendelkezésre állás érdekében. Az hajt végre feladatátvételt a biztonsági mentési funkciót alkalmazásba Ha az elsődleges meghibásodik. További információkért lásd: [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)tartozó [prioritású virtuális gép forgalom-útválasztási módszert.](../traffic-manager/traffic-manager-routing-methods.md#a-name--priorityapriority-traffic-routing-method)


>[!NOTE]
>- A javasolt aktív-passzív konfigurációs biztosítja, hogy egy ügyfél mindig tudja elindítani az új álló üzenettípusok összehangolását HTTP Protokollon keresztül. Azonban következtében, hogy két függvény alkalmazások megosztása ugyanazt a tárhelyet, háttérben történő feldolgozás sor kerül helyezni őket, használják a azonos üzenetsorok üzenetek között. Ez a konfiguráció azt eredményezi azok háromszorosa a másodlagos függvény alkalmazáshoz hozzáadott kilépő költségek.
>- Az alapul szolgáló tárolási fiók és a feladat központ az elsődleges régióban jönnek létre, és mindkét funkció alkalmazások által használt megosztott.
>- Minden függvény telepített alkalmazásokhoz redundantly, közös függvény azonos hívóbetűk HTTP Protokollon keresztül aktiválása esetén. A Functions futtatókörnyezete közzétesz egy [felügyeleti API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) , amely lehetővé teszi, hogy a fogyasztók programozott módon hozzáadása, törlése, és frissítheti a funkcióbillentyűket.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>1 - forgatókönyv elosztott terhelésű számítási megosztott tárolóval
Ha nem sikerül a számítási infrastruktúrától az Azure-ban, a függvény app elérhetetlenné válnának. Az ilyen üzemszünetek lehetőségét minimalizálása érdekében ebben a forgatókönyvben két, különböző régiókban telepített függvény alkalmazást használja. Problémák észlelése az elsődleges funkciója alkalmazásban, és automatikusan átirányítja a függvény alkalmazást a másodlagos régióban forgalom a TRAFFIC Manager van beállítva. A függvény app osztja meg a azonos Azure Storage-fiók és a feladatütemezés központi. Ezért a függvény alkalmazások állapotát nem vesznek el, és általában folytathatja a munkát. Állapotfigyelő visszaállította az elsődleges régióban, miután Azure Traffic Manager útválasztási kérelmek függvény alkalmazás automatikusan elindul.


![1. forgatókönyv ábrázoló diagram.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Több előnye is van ez a telepítési forgatókönyv használata esetén:
- Ha nem sikerül a számítási infrastruktúrától, munkahelyi folytathatja a nem a keresztül régió állapot elvesztése nélkül.
- A TRAFFIC Manager automatikusan a megfelelő függvény alkalmazás automatikus feladatait gondoskodik.
- A TRAFFIC Manager automatikusan újból létrehozza az elsődleges funkciója alkalmazás felé irányuló forgalom javítva lett a szolgáltatáskimaradás elhárítása után.

Azonban ez a forgatókönyv segítségével figyelembe venni:
- A függvény alkalmazás telepítve van a kijelölt App Service-csomag használata, ha a számítási infrastruktúrát a nem a datacenter keresztüli replikálása növeli a költségeket.
- Ebben a forgatókönyvben a számítási infrastruktúrát a kimaradások foglalkozik, de a tárfiók a hibaérzékeny pontok kialakulását, a függvény alkalmazás továbbra is. Egy tároló leállás esetén az alkalmazás romlik egy állásidő.
- A függvény app feladatátvételt, ha nem lesznek nagyobb késéseket óta fogja elérni a tárfiók régiók között.
- A tároló szolgáltatás eléréséhez, ahol van egy másik régióban található azt eredményezi azok háromszorosa a kilépő forgalmat miatt magasabb költsége.
- Ebben a forgatókönyvben a Traffic Manager függ. Annak eldöntéséhez, hogy [Traffic Manager működése](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), lehet, hogy egy ideig, amíg egy ügyfélalkalmazást, amely egy tartós függvény akkor kell kérdeznie ismét a függvényt webalkalmazás URL-címe a Traffic Manager. 


## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>2. forgatókönyv: az elosztott terhelésű számítási regionális tárolóval
A fenti forgatókönyv csak a kis-és hiba történt a számítási infrastruktúrát a foglalkozik. Ha a társzolgáltatás nem sikerül, a függvény alkalmazás kimaradás okoz.
A tartós funkciók folyamatos működés érdekében ez a forgatókönyv használ minden egyes régió, amelyre a függvény alkalmazások vannak telepítve a helyi storage-fiók.

![2. forgatókönyv ábrázoló diagram.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Ez a megközelítés fejlesztései az előző példában ad hozzá:
- Ha a függvény alkalmazás sikertelen, a Traffic Manager gondoskodik a másodlagos régióba feladatátvételét. Azonban mivel a függvény alkalmazást a saját tárfiók alapul, a tartós funkciók továbbra is működik.
- Egy átkapcsolás során nincs további késleltetés a nem a keresztül régió, mert a függvény alkalmazás és a tárfiók egy helyen találhatók.
- Sikertelen volt-e a tárolási réteg hibák miatt a tartós függvény, amely, akkor indul el, a nem átirányítás terület felett. Újra mert a függvény alkalmazás és a tárolási régiónként elkülönített, a tartós funkciók továbbra is működni fog.
 
Ebben a forgatókönyvben szempontokat:
- A függvény alkalmazás telepítve van a kijelölt App Service-csomagot használ, a számítási infrastruktúrát a nem a datacenter keresztüli replikálása növeli a költségeket.
- Jelenlegi állapotában nem átadja a feladatokat, ami azt jelenti, hogy végrehajtása és alkulcsaihoz funkciók sikertelen lesz. Az ügyfélalkalmazás újrapróbálkozási/újraindítja a számítógépet a munkahelyi van.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>3. forgatókönyv – elosztott terhelésű számítási Georedundáns megosztott tárolóval
Ebben a forgatókönyvben az első esetben egy megosztott tárfiókot végrehajtási keresztül módosítása. A fő különbség, hogy a tárfiók létrehozása a georeplikációt engedélyezve van.
Működését tekintve ez a forgatókönyv előnyökkel, 1. forgatókönyv, de lehetővé teszi, hogy további adatok helyreállítási előnyeit:
- Georedundáns tárolás (GRS) és az írásvédett Georedundáns (RA-GRS) a tárfiók rendelkezésre állásának maximalizálását.
- A storage szolgáltatás egy régió leállás esetén a lehetőségek egyike, hogy az Adatközpont működési határozza meg, hogy tárolási kell a feladatátvételt a másodlagos régióba. Ebben az esetben tárolási felhasználóifiók-hozzáférés irányítja transzparens módon a tárfiókot, felhasználói beavatkozás nélkül georeplikált példányát.
- Ebben az esetben a tartós funkciók állapotának a tárfiók, amely néhány percenként kerül sor a legutóbbi replikáció legfeljebb megőrződnek.
Csakúgy, mint az egyéb forgatókönyvek, számos fontos tudnivalók találhatók.
- A replika átkapcsolás adatközponti operátor végezhető el, és hosszabb időt vehet igénybe. Mindaddig a függvény app romlani fog kimaradás.
- Egy nagyobb költsége georeplikált tárfiókok használatával van.
- Georedundáns aszinkron módon történik. A legújabb tranzakciók esetleg elvesznek a replikálási folyamat késleltetése miatt.

![3. forgatókönyv ábrázoló diagram.](media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)


## <a name="next-steps"></a>További lépések

További tudnivalók [tervezése magas rendelkezésre álló alkalmazások RA-GRS használatával](../storage/common/storage-designing-ha-apps-with-ragrs.md)
