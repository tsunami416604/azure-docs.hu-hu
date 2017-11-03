---
title: "A georeplikáció konfigurálása az Azure Redis Cache |} Microsoft Docs"
description: "Útmutató az Azure Redis Cache példány földrajzi régiók közötti replikáció."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: sdanie
ms.openlocfilehash: 332326ce4188385aa6e569c812e16c3daa68bd5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Azure Redis Cache georeplikáció konfigurálása

A georeplikáció lehetővé teszi a csatolás két Premium szint Azure Redis Cache példányt. Egy gyorsítótár az elsődleges társított gyorsítótár, míg a másik a másodlagos csatolt gyorsítótár van kijelölve. A másodlagos csatolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatokat a rendszer replikálja a másodlagos csatolt gyorsítótár. Ez a funkció a gyorsítótár Azure-régiók közötti replikáció használható. A cikkben való georeplikáció beállítása a prémium szint Azure Redis Cache példányt.

## <a name="geo-replication-prerequisites"></a>A georeplikáció Előfeltételek

Két-gyorsítótárak közötti georeplikáció konfigurálásához a következő előfeltételeknek kell teljesülniük:

- Mindkét gyorsítótárak kell [prémium csomagban](cache-premium-tier-intro.md) gyorsítótárazza.
- Mindkét gyorsítótárak azonos Azure-előfizetést kell lennie.
- A másodlagos csatolt gyorsítótár kell lennie, vagy az azonos árképzési szint, vagy az elsődleges társított gyorsítótár-nál nagyobb tarifacsomagot.
- Ha az elsődleges csatolt gyorsítótár fürtszolgáltatás engedélyezve van, a másodlagos csatolt gyorsítótár fürtszolgáltatás ugyanannyi szilánkok, mint az elsődleges csatolt gyorsítótár engedélyezve kell rendelkeznie.
- Mindkét gyorsítótárak kell létrehozni és futó állapotban.
- Adatmegőrzési nem engedélyezhetők vagy gyorsítótár.
- Georeplikálási ugyanazon virtuális-gyorsítótárak közötti támogatott. A más Vnetekről-gyorsítótárak közötti georeplikáció is támogatott, a két Vnetek úgy, hogy a Vnetek erőforrások képesek elérni egymást keresztül TCP-kapcsolatok vannak beállítva.

Georeplikálási konfigurálása után a következő korlátozások vonatkoznak a csatolt gyorsítótár pár:

- A másodlagos csatolt gyorsítótár csak olvasható; érheti el abból, de nem adatírás. 
- A kapcsolat hozzá lett adva a másodlagos csatolt gyorsítótár előtti adatok törlődnek. A georeplikáció azonban azt követően távolítja el, ha a replikált adatokat a másodlagos csatolt gyorsítótárában marad.
- Nem indítható el egy [művelet skálázás](cache-how-to-scale.md) vagy gyorsítótár vagy [szilánkok számának módosítása](cache-how-to-premium-clustering.md) Ha a gyorsítótár fürtszolgáltatás engedélyezve van.
- Nem engedélyezi az adatmegőrzést vagy gyorsítótár.
- Használhatja [exportálása](cache-how-to-import-export-data.md#export) vagy gyorsítótárával, de csak akkor [importálási](cache-how-to-import-export-data.md#import) az elsődleges csatolt gyorsítótárába.
- Csatolt gyorsítótár, vagy az erőforráscsoport, amely tartalmazza azokat, amíg el nem távolítja a georeplikációs hivatkozást nem törölhető. További információkért lásd: [miért volt a művelet sikertelen a csatolt gyorsítótár törlése közben?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Ha a két gyorsítótárak különböző régiókban, a másodlagos csatolt gyorsítótárba régiók között replikált adatok hálózati kilépő díjak vonatkoznak. További információkért lásd: [does mennyibe az adatok Azure-régiók közötti replikáció?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- A másodlagos csatolt gyorsítótárba automatikus feladatátvételi van, ha az elsődleges gyorsítótár (és annak replikája) leáll. Feladatátvételi ügyfélalkalmazások számára sorrendben kellene manuálisan távolítsa el a georeplikációs hivatkozást, és az ügyfélalkalmazások a gyorsítótár, amely korábban a másodlagos csatolt gyorsítótár ponton. További információkért lásd: [hogyan csatolt másodlagos gyorsítótárba feladatátvételét működik?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>A georeplikáció hivatkozás hozzáadása

1. Kapcsolja össze a két premium gyorsítótárak a georeplikációért, kattintson a **georeplikáció** a szándék szerint az elsődleges társított gyorsítótár erőforrás menüből gyorsítótárba, és kattintson **gyorsítótár replikációs hivatkozás hozzáadása** a a **georeplikáció** panelen.

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótárába nevére a **kompatibilis gyorsítótárak** listája. Ha a kívánt gyorsítótárat a listában nem látható, ellenőrizze, hogy a [georeplikáció Előfeltételek](#geo-replication-prerequisites) a kívánt másodlagos gyorsítótár-e. A gyorsítótárak régiónként szűréséhez kattintson a kívánt régiót megjelenítéséhez a gyorsítótárak a térkép a **kompatibilis gyorsítótárak** listája.

    ![A georeplikáció kompatibilis gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    A csatolási folyamat elindítása vagy a másodlagos gyorsítótár részleteinek megtekintése a helyi menü használatával is.

    ![A georeplikáció a helyi menü](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson a **hivatkozás** kapcsolja össze a két gyorsítótárakat és a replikáció megkezdéséhez.

    ![Hivatkozás gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikálási folyamat előrehaladását tekintheti meg a **georeplikáció** panelen.

    ![Hivatkozási állapota](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A hivatkozási állapotát is megtekintheti a **áttekintése** panel az elsődleges és másodlagos gyorsítótárak esetében.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Ha a replikáció befejeződött, a **hivatkozás állapota** vált **sikeres**.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    A csatolási folyamat során az elsődleges csatolt gyorsítótár használható marad, de nem érhető el a másodlagos csatolt gyorsítótár, a csatolási folyamat befejezéséig.

## <a name="remove-a-geo-replication-link"></a>A georeplikáció hivatkozás eltávolítása

1. A két-gyorsítótárak közötti kapcsolat eltávolítása, és állítsa le a georeplikáció, kattintson a **gyorsítótárak leválasztása** a a **georeplikáció** panelen.
    
    ![Gyorsítótárak leválasztása](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Ha a kapcsolat megszüntetése folyamat befejeződik, a másodlagos gyorsítótár érhető mind az Olvasás, mind az írás.

>[!NOTE]
>A georeplikációs hivatkozást a rendszer eltávolítja, ha a replikált adatok az elsődleges csatolt gyorsítótárból a másodlagos gyorsítótárában marad.
>
>

## <a name="geo-replication-faq"></a>A georeplikáció – gyakori kérdések

- [A georeplikáció használható Standard vagy a Basic szint gyorsítótárával?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [A gyorsítótár használható van a hivatkozási vagy a kapcsolat megszüntetése során?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Képes vagyok összeköt két kettőnél több gyorsítótárak?](#can-i-link-more-than-two-caches-together)
- [Két, különböző Azure-előfizetések a gyorsítótárak is kapcsolhat?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [A különböző méretű két gyorsítótárak is kapcsolhat?](#can-i-link-two-caches-with-different-sizes)
- [A georeplikáció használata telepítése fürtözött környezetben?](#can-i-use-geo-replication-with-clustering-enabled)
- [Használhatom a georeplikáció a gyorsítótárak a VNETEN belül?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Mi az a Redis-georeplikációt ütemezést?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Mennyi időt georeplikáció replikációs igénybe?](#how-long-does-geo-replication-replication-take)
- [A replikáció helyreállítási pont garantáltan?](#is-the-replication-recovery-point-guaranteed)
- [Használhatok PowerShell vagy Azure CLI georeplikáció kezeléséhez?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Nem mennyibe az adatok Azure-régiók közötti replikáció?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Miért volt a sikertelen történő a csatolt gyorsítótár törlése közben?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Milyen régióban kell használni a másodlagos csatolt gyorsítótár?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hogyan működik a csatolt másodlagos gyorsítótárba feladatátvételét?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>A georeplikáció használható Standard vagy a Basic szint gyorsítótárával?

A georeplikáció nem, a prémium szintű réteghez gyorsítótárainak csak érhető el.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>A gyorsítótár használható van a hivatkozási vagy a kapcsolat megszüntetése során?

- Ha két gyorsítótárak összekapcsoló a georeplikációért, az elsődleges csatolt gyorsítótár használható marad, de nem érhető el a másodlagos csatolt gyorsítótár, a csatolási folyamat befejezéséig.
- A georeplikáció hivatkozás két-gyorsítótárak közötti eltávolításakor mindkét gyorsítótárak elérhetők maradnak.

### <a name="can-i-link-more-than-two-caches-together"></a>Képes vagyok összeköt két kettőnél több gyorsítótárak?

Nem, georeplikáció használata esetén is csak összeköt két gyorsítótárát.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Két, különböző Azure-előfizetések a gyorsítótárak is kapcsolhat?

Egyetlen, mindkét gyorsítótárak azonos Azure-előfizetést kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>A különböző méretű két gyorsítótárak is kapcsolhat?

Igen, mindaddig, amíg az másodlagos csatolt gyorsítótár mérete nagyobb, mint az elsődleges csatolt gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>A georeplikáció használata telepítése fürtözött környezetben?

Igen, mindaddig, amíg mindkét gyorsítótárak szilánkok azonos számú rendelkezik.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használhatom a georeplikáció a gyorsítótárak a VNETEN belül?

Igen, a Vnetek-gyorsítótárak a georeplikáció támogatottak. 

- Georeplikálási ugyanazon virtuális-gyorsítótárak közötti támogatott.
- A más Vnetekről-gyorsítótárak közötti georeplikáció is támogatott, a két Vnetek úgy, hogy a Vnetek erőforrások képesek elérni egymást keresztül TCP-kapcsolatok vannak beállítva.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi az a Redis-georeplikációt ütemezést?

Már nem fordulhat elő meghatározott ütemezés szerint, a replikáció folyamatos és aszinkron Egytényezős a fő végzett írási műveletek aszinkron azonnal replikálva vannak a másodlagos.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi időt georeplikáció replikációs igénybe?

Replikációs növekményes, aszinkron és folyamatos és igénybe vett idő általában nem sokkal eltér a várakozási régiók között. Bizonyos körülmények között, egy adott időszakban, a másodlagos előfordulhat, hogy kell tegye a teljes szinkronizálás az adatok az elsődleges adatbázisból. A replikáció ideje a jelen esetben ez számos tényezőtől például függ: az elsődleges gyorsítótár, a gyorsítótár gépen rendelkezésre álló sávszélesség terhelése többek régió késés stb. Tegyük fel, ki, hogy egy teljes replikáció ideje találtunk, amelyeknek az egyes tesztekre alapozva georeplikált 53 GB párosítsa-keleti VELÜNK és USA nyugati régiója régiók közötti 5 – 10 perces bárhol lehet.

### <a name="is-the-replication-recovery-point-guaranteed"></a>A replikáció helyreállítási pont garantáltan?

Jelenleg gyorsítótárak georeplikált módban, az adatmegőrzés és az importálási/exportálási funkció ki van kapcsolva. Abban az esetben, ha az ügyfél által kezdeményezett feladatátvételi vagy azokban az esetekben, ahol a replikációs hivatkozás meg lett szakítva, a georeplikált pár között, a másodlagos megőrzi a memória, amíg időpontig szinkronizálva van az elsődleges adatok. Nincs ilyen helyzetekben a megadott helyreállítási pont garancia.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használhatok PowerShell vagy Azure CLI georeplikáció kezeléséhez?

Jelenleg csak az Azure portál használatával a georeplikáció is kezelheti.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Nem mennyibe az adatok Azure-régiók közötti replikáció?

Georeplikáció használata esetén az elsődleges csatolt gyorsítótár adatait a csatolt másodlagos gyorsítótár replikálódik. Ha a két csatolt gyorsítótárak azonos Azure-régióban, nincs az adatátvitel díjmentes. Ha a két csatolt gyorsítótárak különböző Azure-régiókban, a Georeplikáció adatok átvitel kell fizetni sávszélesség költsége adatok replikálását a más Azure-régiót. További információkért lásd: [sávszélesség díjszabás](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért volt a sikertelen történő a csatolt gyorsítótár törlése közben?

Ha két gyorsítótárak egymáshoz kapcsolódó, vagy az erőforráscsoport, amely tartalmazza azokat, amíg el nem távolítja a georeplikációs hivatkozást gyorsítótár nem törölhető. Ha az egyik vagy mindkét a csatolt gyorsítótárak tartalmazó erőforráscsoportot, a további erőforrások az erőforráscsoportban törlődnek, hanem marad az erőforráscsoport a `deleting` állapot és minden társított gyorsítótár erőforráscsoportban maradni a `running` állapotát. Az erőforráscsoport és a hozzá csatolt gyorsítótárak törlésének befejezéséhez csatolását a georeplikáció a [georeplikáció hivatkozás eltávolítása](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Milyen régióban kell használni a másodlagos csatolt gyorsítótár?

Általánosságban ajánlott a gyorsítótárhoz fér hozzá az alkalmazás Azure ugyanabban a régióban léteznie. Ha az alkalmazás egy elsődleges és tartalék régió, az elsődleges és másodlagos gyorsítótár szerepel kell azokban a régiókban. Párhuzamos régiók kapcsolatos további információkért lásd: [gyakorlati tanácsok – Azure párosított régiók](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a csatolt másodlagos gyorsítótárba feladatátvételét?

A georeplikáció eredeti kiadását Azure Redis Cache nem támogatja a automatikus feladatátvétel Azure-régiók között. A georeplikáció elsősorban vész-helyreállítási forgatókönyv használatos. Distater helyreállítás az ügyfelek kell elindítani a teljes alkalmazás tartozó, egy biztonsági mentési régió összehangolt módon ahelyett, hogy eldönthesse, mikor váltson át a biztonsági másolatok a saját egyéni alkalmazás-összetevők. Ez különösen fontos való. A fő előnyei a Redis egyike, hogy a rendszer egy nagyon kis késleltetésű tárolóban. Ha egy alkalmazás által használt Redis átadja a feladatokat egy másik Azure-régió, de a számítási rétegben viszont nem, a hozzáadott visszatérési ideje hatással lenne egy észrevehető teljesítményét. Ezért szeretnénk elkerülése érdekében a Redis hibás keresztül automatikusan miatt átmeneti elérhetőségével kapcsolatos problémákat.

Jelenleg kezdeményezze a feladatátvételt, meg kell georeplikáció hivatkozás eltávolítása az Azure portálon, és módosítsa a kapcsolati végpontot a Redis-ügyfélben a elsődleges csatolt gyorsítótárból (korábbi nevén csatolt) másodlagos gyorsítótárába. A két gyorsítótárak vannak hozzárendelésének megszüntetése, ha a replika egy rendszeres írási és olvasási gyorsítótár ismét válik, és közvetlenül a Redis-ügyfelektől érkező kéréseket fogad.


## <a name="next-steps"></a>Következő lépések

További információ a [Azure Redis Cache prémium szintjének](cache-premium-tier-intro.md).

