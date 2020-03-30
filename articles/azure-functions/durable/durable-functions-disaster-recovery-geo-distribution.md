---
title: Katasztrófa utáni helyreállítás és geodisztribúció az Azure Durable Functions
description: Ismerje meg a vész-helyreállítási és a geo-eloszlás a tartós funkciók.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433329"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Katasztrófa utáni helyreállítás és földrajzi eloszlás az Azure Durable Functions szolgáltatásban

A tartós függvények, az összes állapot megmarad az Azure Storage-ban. A [feladatközpont](durable-functions-task-hubs.md) az Azure Storage-erőforrások, amelyek vezénylési használt logikai tároló. Az Orchestrator és a tevékenységfüggvények csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladatközponthoz tartoznak.
A leírt forgatókönyvek telepítési lehetőségeket javasolnak a rendelkezésre állás növelése és az állásidő minimalizálása érdekében a vész-helyreállítási tevékenységek során.

Fontos figyelembe venni, hogy ezek a forgatókönyvek aktív-passzív konfigurációkon alapulnak, mivel az Azure Storage használata vezérli őket. Ez a minta egy biztonsági mentési (passzív) függvényalkalmazás központi telepítéséből áll egy másik régióba. A Traffic Manager figyelni fogja az elsődleges (aktív) függvényalkalmazást a rendelkezésre álláshoz. A biztonsági mentési függvény alkalmazás, ha az elsődleges sikertelen lesz. További információ: [Traffic Manager's](https://azure.microsoft.com/services/traffic-manager/) [Priority Traffic-Routing Method.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - A javasolt aktív-passzív konfiguráció biztosítja, hogy az ügyfél mindig képes-hoz ravaszt új vezénylések HTTP-n keresztül. Ennek következtében azonban két függvényalkalmazás ugyanazt a tárolót osztja meg, a háttérfeldolgozás mindkét fél között lesz elosztva, és ugyanazon várólistákon lévő üzenetekért verseng. Ez a konfiguráció a másodlagos függvényalkalmazás hozzáadott kimenő forgalom költségeiben merül fel.
> - Az alapul szolgáló tárfiók és a feladatközpont az elsődleges régióban jönnek létre, és mindkét függvényalkalmazás megosztja őket.
> - Minden olyan függvényalkalmazásnak, amely redundánsan telepítve van, ugyanazt a függvényhozzáférési kulcsot kell megosztania, ha HTTP-n keresztül aktiválódik. A Functions Runtime elérhetővé tesz egy [felügyeleti API-t,](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) amely lehetővé teszi a felhasználók számára a funkciókulcsok programozott hozzáadását, törlését és frissítését.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>1. forgatókönyv – Kiegyensúlyozott terhelésű számítás megosztott tárhellyel

Ha a számítási infrastruktúra az Azure-ban meghibásodik, a függvényalkalmazás elérhetetlenné válhat. Az ilyen állásidő lehetőségének minimalizálása érdekében ez a forgatókönyv két függvényalkalmazást használ a különböző régiókban üzembe helyezve.
Traffic Manager van beállítva, hogy észlelje a problémákat az elsődleges függvény alkalmazás, és automatikusan átirányítja a forgalmat a függvényalkalmazás a másodlagos régióban. Ez a függvényalkalmazás ugyanazt az Azure Storage-fiókot és a Feladatközpontot osztja meg. Ezért a függvényalkalmazások állapota nem vész el, és a munka a szokásos módon folytatódhat. Miután az állapot helyreállt az elsődleges régióban, az Azure Traffic Manager automatikusan elindítja az adott függvényalkalmazáshoz irányuló útválasztási kérelmeket.

![Az 1.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Ennek a telepítési forgatókönyvnek a használata számos előnnyel jár:

- Ha a számítási infrastruktúra meghibásodik, a munka állapotvesztés nélkül folytatódhat a feladatátvételi régióban.
- Traffic Manager gondoskodik az automatikus feladatátvételt a kifogástalan állapotú függvény alkalmazás automatikusan.
- A Traffic Manager automatikusan újra létrehozza a forgalmat az elsődleges függvényalkalmazásba a kimaradás kijavítása után.

Ennek a forgatókönyvnek a használata azonban a következőket veszi figyelembe:

- Ha a függvényalkalmazás dedikált App Service-csomag használatával van telepítve, a számítási infrastruktúra replikálása az adatközpontban növeli a költségeket.
- Ez a forgatókönyv a számítási infrastruktúra kimaradások, de a tárfiók továbbra is az egyetlen hibapont a függvény alkalmazás. Ha van egy tárolási kimaradás, az alkalmazás állásidőt szenved.
- Ha a függvényalkalmazás feladatátvételt, nem lesz nagyobb késés, mivel a tárfiók régiók közötti eléréséhez.
- A tárolási szolgáltatás elérése egy másik régióból, ahol található, a hálózati kimenő forgalom miatt magasabb költségek merülnek fel.
- Ez a forgatókönyv a Traffic Managertől függ. Figyelembe véve, [hogyan működik a Traffic Manager,](../../traffic-manager/traffic-manager-how-it-works.md)lehet, hogy egy ideig, amíg egy ügyfélalkalmazás, amely felhasználja a tartós függvény kell lekérdezni újra a függvény alkalmazás címét traffic manager.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>2. forgatókönyv – Terheléselosztásos számítás regionális tárhellyel

Az előző forgatókönyv csak a számítási infrastruktúra meghibásodásának esetére vonatkozik. Ha a tárolási szolgáltatás meghibásodik, a függvényalkalmazás kimaradást eredményez.
A tartós függvények folyamatos működésének biztosítása érdekében ez a forgatókönyv egy helyi tárfiókot használ minden régióban, amelyre a függvényalkalmazások telepítve vannak.

![A 2.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Ez a megközelítés tovább javítja az előző forgatókönyvet:

- Ha a függvényalkalmazás meghibásodik, a Traffic Manager gondoskodik a másodlagos régió feladatainak ellátásáról. Mivel azonban a függvényalkalmazás a saját tárfiókra támaszkodik, a tartós függvények továbbra is működnek.
- Feladatátvétel során nincs további késés a feladatátvételi régióban, mivel a függvényalkalmazás és a tárfiók közös en találhatók.
- A tárolási réteg meghibásodása hibákat okoz a tartós függvényekben, ami viszont átirányítást indít a feladatátvételi régióba. Ismét, mivel a függvény alkalmazás és a tároló régiónként elkülönített, a tartós függvények továbbra is működni fog.

Fontos szempontok a forgatókönyvhöz:

- Ha a függvényalkalmazás dedikált AppService-csomag használatával van telepítve, a számítási infrastruktúra replikálása az adatközpontban növeli a költségeket.
- A jelenlegi állapot nem feladatátvétel, ami azt jelenti, hogy a végrehajtás és az ellenőrzőpontok függvények sikertelen lesz. Az ügyfélalkalmazástól kell újrapróbálkoznia/újraindítania a munkát.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>3. forgatókönyv – Kiegyensúlyozott terhelésű számítás GRS megosztott tárolóval

Ez a forgatókönyv egy módosítás az első forgatókönyv, egy megosztott tárfiók megvalósítása. A fő különbség, hogy a tárfiók jön létre a georeplikáció engedélyezve van.
Funkcionálisan ez a forgatókönyv ugyanazokat az előnyöket nyújtja, mint az 1.

- Georedundáns tárolás (GRS) és olvasási hozzáférésű GRS (RA-GRS) maximalizálja a tárfiók rendelkezésre állását.
- Ha a tárolási szolgáltatás nak van egy régiókimaradása, az egyik lehetőség az, hogy az adatközpont-műveletek határozzák meg, hogy a tárolót át kell adni a másodlagos régiónak. Ebben az esetben a tárfiók-hozzáférés átlátható annektálva lesz átirányítva a tárfiók georeplikált példányára, felhasználói beavatkozás nélkül.
- Ebben az esetben a tartós függvények állapota a tárfiók utolsó replikációjáig megmarad, amely néhány percenként történik.

A többi forgatókönyvhöz is fontos szempontok at kell figyelembe venni:

- A kópia átvétele az adatközpont-operátorok által történik, és eltarthat egy ideig. Addig a függvényalkalmazás kimaradást szenved.
- A georeplikált tárfiókok használatának költsége megnő.
- GrS fordul elő aszinkron módon. A legutóbbi tranzakciók némelyike elveszhet a replikációs folyamat késése miatt.

![A 3.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>További lépések

A [ra-GRS használatával elérhető alkalmazások tervezéséről](../../storage/common/storage-designing-ha-apps-with-ragrs.md) olvashat bővebben
