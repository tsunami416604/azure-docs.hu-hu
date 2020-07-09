---
title: Vész-helyreállítási és földrajzi eloszlású Azure Durable Functions
description: Ismerje meg a vész-helyreállítást és a Geo-eloszlást Durable Functionsban.
author: MS-Santi
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 7951f216143bef0d48a6b751beff3f8f4316b9bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75433329"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Vész-helyreállítás és geo-eloszlás az Azure-ban Durable Functions

A Durable Functionsban az összes állapot az Azure Storage-ban marad. A [Task hub](durable-functions-task-hubs.md) olyan Azure Storage-erőforrások logikai tárolója, amelyeket a rendszer az előkészítéshez használ. A Orchestrator és a Activity függvények csak akkor kommunikálhatnak egymással, ha ugyanahhoz a feladathoz tartozó hubhoz tartoznak.
Az ismertetett forgatókönyvek javaslatot tesznek a rendelkezésre állás növelésére és az állásidő minimalizálására a vész-helyreállítási tevékenységek során.

Fontos megjegyezni, hogy ezek a forgatókönyvek az aktív-passzív konfigurációkon alapulnak, mivel ezeket az Azure Storage használata vezérli. Ez a minta egy biztonsági mentési (passzív) függvény alkalmazásának egy másik régióba való telepítését jelenti. A Traffic Manager az elsődleges (aktív) function alkalmazást fogja figyelni a rendelkezésre álláshoz. Ha az elsődleges művelet meghiúsul, a rendszer átadja a feladatokat a Backup Function alkalmazásnak. További információ: [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) [prioritású forgalom – útválasztási módszer.](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)

>[!NOTE]
>
> - A javasolt aktív-passzív konfiguráció biztosítja, hogy az ügyfél mindig képes legyen új, HTTP-n keresztül kiváltani a folyamatokat. Ha azonban két Function apps azonos tárterületet használ, a háttérben történő feldolgozás a kettő között oszlik meg egymás között, és az ugyanazon a várólistán lévő üzenetek versengenek egymással. Ez a konfiguráció a másodlagos függvény alkalmazásának kimenő költségeinek kilépését eredményezi.
> - Az alapul szolgáló Storage-fiók és a Task hub az elsődleges régióban jön létre, és mindkét Function-alkalmazásban meg van osztva.
> - A redundánsan telepített összes Function apps esetében ugyanazt a függvény-hozzáférési kulcsokat kell megosztania, ha HTTP-n keresztül aktiválva van. A functions futtatókörnyezet olyan [felügyeleti API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) -t tesz elérhetővé, amely lehetővé teszi a felhasználóknak a funkcióbillentyűk programozott hozzáadását, törlését és frissítését.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>1. eset – elosztott terhelésű számítás megosztott tárolóval

Ha az Azure-beli számítási infrastruktúra meghibásodik, előfordulhat, hogy a Function alkalmazás elérhetetlenné válik. Ha csökkenteni szeretné az ilyen állásidők lehetőségét, ez a forgatókönyv két, különböző régiókban üzembe helyezett függvény alkalmazást használ.
Traffic Manager úgy van konfigurálva, hogy észlelje a problémákat az elsődleges Function alkalmazásban, és automatikusan átirányítsa a forgalmat a másodlagos régióban található Function alkalmazásba. Ez a Function-alkalmazás ugyanazt az Azure Storage-fiókot és-feladatot osztja meg. Ezért a Function apps állapota nem vész el, és a munka a szokásos módon folytatódik. Ha az állapotot visszaállítja az elsődleges régióba, az Azure Traffic Manager automatikusan elindítja az útválasztási kérelmeket az adott függvény alkalmazásnak.

![Az 1. forgatókönyvet ábrázoló diagram](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

Ennek a telepítési forgatókönyvnek a használata számos előnnyel jár:

- Ha a számítási infrastruktúra meghibásodik, a feladat az állami adatvesztés nélkül folytathatja a feladatátvételt a régióban.
- A Traffic Manager automatikusan átveszi az automatikus feladatátvételt az egészséges Function alkalmazásba.
- Traffic Manager automatikusan újra létrehozza a forgalmat az elsődleges Function alkalmazásnak a leállás kijavítása után.

Ebben a forgatókönyvben azonban a következőket érdemes figyelembe venni:

- Ha a Function alkalmazást dedikált App Service-csomag használatával telepíti, a számítási infrastruktúra replikálása a feladatátvételi adatközpontban növeli a költségeket.
- Ez a forgatókönyv a számítási infrastruktúra leállásait fedi le, de a Storage-fiók továbbra is a Function alkalmazás egyetlen meghibásodási pontja. Ha van tárolási leállás, az alkalmazás leállást szenved.
- Ha a Function alkalmazás feladatátvételt végez, a rendszer megnöveli a késést, mivel a több régióban is hozzáfér a Storage-fiókjához.
- A Storage szolgáltatást egy másik régióból érheti el, ahol a hálózati forgalom miatt magasabb költségekkel jár.
- Ez a forgatókönyv a Traffic Managertól függ. Tekintettel arra, [hogy Traffic Manager működik](../../traffic-manager/traffic-manager-how-it-works.md)-e, lehet, hogy egy olyan ügyfélalkalmazás, amely nem használ tartós függvényt, újra le kell kérdezni a Function app-címeket a Traffic Managerról.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>2. forgatókönyv – elosztott terhelésű számítás regionális tárterülettel

Az előző forgatókönyv csak a számítási infrastruktúra meghibásodásának esetét tárgyalja. Ha a tárolási szolgáltatás meghibásodik, akkor a függvény alkalmazás leállás miatt leáll.
A tartós függvények folyamatos működésének biztosítása érdekében ez a forgatókönyv egy helyi Storage-fiókot használ minden olyan régióban, amelyhez a Function apps telepítve van.

![A 2. forgatókönyvet bemutató diagram](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

Ez a megközelítés javítja az előző forgatókönyvet:

- Ha a Function alkalmazás meghibásodik, Traffic Manager gondoskodik a másodlagos régióba való feladatátvételről. Mivel azonban a Function alkalmazás a saját Storage-fiókjára támaszkodik, a tartós függvények továbbra is működőképesek maradnak.
- A feladatátvétel során nincs további késés a feladatátvételi régióban, mivel a Function alkalmazás és a Storage-fiók közös helyen található.
- A tárolási réteg meghibásodása hibát okoz a tartós függvényeknél, ami viszont egy átirányítást indít a feladatátvételi régióba. Mivel a Function app és a Storage régiónként elkülönített, a tartós függvények továbbra is működni fognak.

A forgatókönyvhöz kapcsolódó fontos szempontok:

- Ha a Function alkalmazás egy dedikált AppService-csomag használatával van üzembe helyezve, a számítási infrastruktúra replikálása a feladatátvételi adatközpontban növeli a költségeket.
- A jelenlegi állapot nem halad át, ami azt jelenti, hogy a végrehajtás és az ellenőrzőponttal nem rendelkező függvények sikertelenek lesznek. Az ügyfélalkalmazás a feladat újrapróbálkozására/újraindítására használható.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>3. forgatókönyv – elosztott terhelésű számítás GRS megosztott tárolóval

Ez a forgatókönyv az első forgatókönyv módosítása, egy megosztott Storage-fiók implementálása. A fő különbség, hogy a Storage-fiók a Geo-replikáció engedélyezve beállítással jön létre.
Funkcionálisan ez a forgatókönyv ugyanazokat az előnyöket biztosítja, mint az 1. forgatókönyv, de lehetővé teszi a további adat-helyreállítási előnyöket:

- A Geo-redundáns tárolás (GRS) és az olvasási hozzáférésű GRS (RA-GRS) teljes körű rendelkezésre állást biztosít a Storage-fiók számára.
- Ha a tárolási szolgáltatás a régió meghibásodása miatt leáll, az egyik lehetőség az, hogy az adatközpont műveletei azt határozzák meg, hogy a tárolónak feladatátvételt kell végrehajtania a másodlagos régióba. Ebben az esetben a Storage-fiók hozzáférését a rendszer a felhasználói beavatkozás nélkül átirányítja a Storage-fiók geo-replikált példányára.
- Ebben az esetben a tartós függvények állapotát a Storage-fiók utolsó replikálásával fogja megőrizni, ami néhány percenként történik.

A többi forgatókönyvhöz hasonlóan fontos szempontokat is figyelembe kell venni:

- A replika feladatátvételét az adatközpont-kezelők végzik, és ez eltarthat egy ideig. A Function alkalmazás addig nem fog kiesést szenvedni.
- A földrajzilag replikált Storage-fiókok használatának megnövelt díja.
- A GRS aszinkron módon történik. A replikálási folyamat késése miatt előfordulhat, hogy a legújabb tranzakciók némelyike elvész.

![A 3. forgatókönyvet bemutató diagram.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>További lépések

További információk a [magasan elérhető alkalmazások a ra-GRS használatával történő tervezéséről](../../storage/common/storage-designing-ha-apps-with-ragrs.md)
