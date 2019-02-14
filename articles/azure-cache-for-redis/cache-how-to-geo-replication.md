---
title: Georeplikáció konfigurálása az Azure Cache Redis |} A Microsoft Docs
description: Ismerje meg, hogyan replikálhat az Azure Cache a Redis-példány több földrajzi régióban.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: yegu
ms.openlocfilehash: e5e60e3370cc813685403cc979e6ef8dc043b7ac
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233268"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Georeplikáció konfigurálása az Azure Cache Redis

A georeplikáció lehetővé teszi a két prémium szintű Azure Cache Redis-példány csatolása. Egy gyorsítótár az elsődleges társított gyorsítótár, a másik pedig a csatolt másodlagos gyorsítótáraként van kijelölve. A másodlagos összekapcsolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatok replikálódnak a másodlagos összekapcsolt gyorsítótár. Ez a funkció segítségével gyorsítótár replikálása az Azure-régiók között. Ez a cikk ismerteti egy útmutató, amellyel georeplikáció konfigurálása a Redis-példány a prémium szintű Azure Cache.

## <a name="geo-replication-prerequisites"></a>Georeplikáció – Előfeltételek

Georeplikáció konfigurálása két gyorsítótárak között, a következő előfeltételeknek kell teljesülniük:

- Mindkét gyorsítótárakat kell [prémium szintű](cache-premium-tier-intro.md) gyorsítótárazza.
- Mindkét gyorsítótárak az Azure-előfizetéshez kell lennie.
- A másodlagos összekapcsolt gyorsítótár ugyanabba a tarifacsomagba tartozó vagy az elsődleges társított gyorsítótár-nál nagyobb méretű tarifacsomag kell lennie.
- Ha az elsődleges társított gyorsítótár fürtözés engedélyezve van, a másodlagos összekapcsolt gyorsítótár fürtözés engedélyezve van az elsődleges társított gyorsítótár azonos számú szegmensek kell rendelkeznie.
- Mindkét gyorsítótárakat kell létrehoznia, és a egy működőképes állapotba.
- Adatmegőrzés engedélyezve vagy-gyorsítótár nem kell.
- Georeplikáció útján az azonos virtuális hálózaton található gyorsítótárak között támogatott. 
- Gyorsítótárak az azonos régión belüli társviszonyban lévő virtuális hálózatok közötti georeplikációs szolgáltatás jelenleg előzetes verziójú funkció. A két virtuális hálózat úgy, hogy a virtuális hálózat erőforrásaihoz képesek legyenek elérni egymást keresztül TCP-kapcsolatok konfigurálni kell.
- Gyorsítótárak a különböző régiókban lévő két társviszonyban álló virtuális hálózatok közötti georeplikáció még nem támogatott, de hamarosan előzetes verzióban érhető el.

Georeplikáció konfigurálása után az alábbi korlátozások vonatkoznak az összekapcsolt gyorsítótár pár:

- A másodlagos összekapcsolt gyorsítótár a csak olvasható; Tudjon meg azt a, de semmilyen adatot nem írhatnak abba. 
- A másodlagos összekapcsolt gyorsítótár a korábban a hivatkozás hozzáadása előtti adatokat a rendszer eltávolítja. Ha a georeplikáció azonban azt követően távolítja el, a replikált adatokat a másodlagos összekapcsolt gyorsítótár marad.
- Nem kezdeményezhető egy [skálázási művelet](cache-how-to-scale.md) vagy-gyorsítótár vagy [módosítsa a szegmensek száma](cache-how-to-premium-clustering.md) Ha a gyorsítótár tartalmaz a fürtözés engedélyezve van.
- Adatmegőrzés vagy-gyorsítótár nem engedélyezhető.
- Használhatja [exportálása](cache-how-to-import-export-data.md#export) vagy a gyorsítótárhoz, de csak [importálás](cache-how-to-import-export-data.md#import) a társított elsődleges gyorsítótárba.
- Összekapcsolt gyorsítótár vagy az erőforráscsoport, amely tartalmazza őket, amíg nem törli a georeplikációs hivatkozás nem törölhető. További információkért lásd: [miért volt a művelet sikertelen társított gyorsítótár törlése során?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Ha a két gyorsítótárak különböző régiókban, az adatok replikálása a másodlagos társított gyorsítótár-régiók közötti hálózati adatkimeneti költségek vonatkozik. További információkért lásd: [mennyi költséggel jár a saját adatok replikálása az Azure-régióban?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Nincs automatikus feladatátvételt a másodlagos összekapcsolt gyorsítótár van, ha az elsődleges gyorsítótár (és annak replikája) leáll. Ahhoz, feladatátvételi ügyfélalkalmazások számára kell manuálisan távolítsa el a georeplikációs hivatkozás, és az ügyfélalkalmazások a gyorsítótárhoz, amely korábban volt a másodlagos összekapcsolt gyorsítótár mutasson. További információkért lásd: [a másodlagos társított gyorsítótár-ba irányuló feladatátvétel működése?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>A georeplikációs hivatkozás hozzáadása

1. Kapcsolja össze a két prémium gyorsítótárak esetében georeplikációt, kattintson a **georeplikációs** az erőforrás menüben a szándék szerint az elsődleges társított gyorsítótár gyorsítótárazza, és kattintson **gyorsítótár-replikációs hivatkozás hozzáadása** a a **georeplikációs** panelen.

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótár nevére a **kompatibilis gyorsítótárak** listája. Ha a kívánt gyorsítótárat nem jelenik meg a listában, ellenőrizze, hogy a [georeplikációs Előfeltételek](#geo-replication-prerequisites) a kívánt másodlagos gyorsítótár teljesülnek. A gyorsítótárak régiónként szűréséhez kattintson a kívánt régiót, a térképen megjelenítendő a gyorsítótárak az **kompatibilis gyorsítótárak** listája.

    ![Kompatibilis gyorsítótárak Georeplikáció útján](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    A csatolási folyamat elindításához vagy a másodlagos gyorsítótár részleteinek megtekintése a helyi menü használatával is.

    ![Georeplikáció helyi menü](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson a **hivatkozás** kapcsolja össze a két gyorsítótárakat és a replikáció megkezdéséhez.

    ![Hivatkozás gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikálási folyamat előrehaladását a megtekintheti a **georeplikációs** panelen.

    ![Csatolás állapota](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A hivatkozási állapotát is megtekintheti a **áttekintése** panel az elsődleges és másodlagos gyorsítótárak esetében.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    A replikálási folyamat befejeződése után a **hivatkozás állapota** vált **sikeres**.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    A csatolási folyamat során az elsődleges társított gyorsítótár használható marad, de a másodlagos társított gyorsítótár nem érhető el, amíg a csatolási folyamat befejeződött.

## <a name="remove-a-geo-replication-link"></a>A georeplikációs hivatkozás eltávolítása

1. Távolítsa el a két gyorsítótárak közötti kapcsolat, és állítsa le a georeplikáció, kattintson a **gyorsítótárakat** származó a **georeplikációs** panelen.
    
    ![Gyorsítótárak leválasztása](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    A csatolás megszüntetése folyamat befejeződése után a másodlagos gyorsítótár érhető el az olvasásokat és az írásokat.

>[!NOTE]
>A georeplikációs hivatkozás eltávolítása után a replikált adatokat az elsődleges társított gyorsítótárból a másodlagos gyorsítótár marad.
>
>

## <a name="geo-replication-faq"></a>Georeplikáció – gyakori kérdések

- [Használható a gyorsítótárat Standard vagy Basic szint georeplikáció?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [A gyorsítótár használatra érhetők el a hivatkozási vagy leválasztása során?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Is lehet hivatkozni több mint két gyorsítótárak együtt?](#can-i-link-more-than-two-caches-together)
- [Összekapcsolhatja a különböző Azure-előfizetésekből két gyorsítótárakhoz?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Összekapcsolhatja a különböző méretű két gyorsítótárakhoz?](#can-i-link-two-caches-with-different-sizes)
- [Használható a fürtözés engedélyezve van a georeplikáció?](#can-i-use-geo-replication-with-clustering-enabled)
- [Használható a saját virtuális hálózaton gyorsítótárak georeplikáció?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Mi az a replikáció ütemezését Redis georeplikáció?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Mennyi georeplikációs replikációs időt igénybe?](#how-long-does-geo-replication-replication-take)
- [A replikáció helyreállítási pont garantáltan?](#is-the-replication-recovery-point-guaranteed)
- [Használható PowerShell vagy az Azure CLI-vel kezelheti a georeplikáció?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [IP-címek fenntartási költséggel jár a saját adatok replikálása az Azure-régióban?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Miért volt a sikertelen társított gyorsítótár törlése során?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Milyen régióban érdemes használni a saját másodlagos összekapcsolt gyorsítótár?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hogyan működik a másodlagos társított gyorsítótár-ba irányuló feladatátvétel?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Használható a gyorsítótárat Standard vagy Basic szint georeplikáció?

Nem, a georeplikáció csak akkor használható, prémium szintű gyorsítótárak esetében.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>A gyorsítótár használatra érhetők el a hivatkozási vagy leválasztása során?

- Amikor két gyorsítótárakhoz összekapcsolásának együtt georeplikáció, az elsődleges társított gyorsítótár használható marad, de a másodlagos társított gyorsítótár nem érhető el, amíg a csatolási folyamat befejeződött.
- Ha eltávolítja a georeplikációs hivatkozás két gyorsítótárak között, mindkét gyorsítótárak használható marad.

### <a name="can-i-link-more-than-two-caches-together"></a>Is lehet hivatkozni több mint két gyorsítótárak együtt?

Nem, a georeplikáció használata esetén csak kapcsolat két gyorsítótárak együtt.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Összekapcsolhatja a különböző Azure-előfizetésekből két gyorsítótárakhoz?

Nem, mindkét gyorsítótárak az Azure-előfizetéshez kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Összekapcsolhatja a különböző méretű két gyorsítótárakhoz?

Igen, mindaddig, amíg a másodlagos összekapcsolt gyorsítótár mérete nagyobb, mint az elsődleges társított gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Használható a fürtözés engedélyezve van a georeplikáció?

Igen, mindaddig, amíg mindkét gyorsítótárak szegmensek azonos számú rendelkezik.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használható a saját virtuális hálózaton gyorsítótárak georeplikáció?

Igen, a virtuális hálózatok gyorsítótárak georeplikációs támogatottak. 

- Georeplikáció útján az azonos virtuális hálózaton található gyorsítótárak között támogatott.
- Eltérő virtuális hálózatokba tartozó gyorsítótárak közötti georeplikáció is támogatott, mindaddig, amíg a két virtuális hálózat úgy, hogy a virtuális hálózat erőforrásaihoz képesek legyenek elérni egymást TCP-kapcsolatok keresztül vannak konfigurálva.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi az a replikáció ütemezését Redis georeplikáció?

Replikáció nem megy végbe egy adott ütemezés szerint, a folyamatos és aszinkron azaz az elsődleges végzett az összes írási művelet azonnal aszinkron módon replikálódnak a másodlagos.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi georeplikációs replikációs időt igénybe?

Replikációs növekményes, aszinkron és a folyamatos és igénybe vett idő általában nem sokkal eltér a késés régiók között elosztva. Bizonyos körülmények esetén bizonyos időpontokban, a másodlagos fiókdíjat kell hajtsa végre az adatok a teljes szinkronizálás az elsődleges kiszolgálóról. A replikációs időt ebben az esetben az adott nyelvtől függ számos tényezőtől, például: az elsődleges gyorsítótár, a gyorsítótár gépen elérhető sávszélesség terhelése díjakhoz régió késés stb. Tegyük fel, egyes tesztelési munkák található, a replikációs időt a teljes alapján 53 GB feletti georeplikált párosítsa keleti régiójában velünk a KAPCSOLATOT, és USA nyugati régiójában bárhol lehetnek 5-10 perc között.

### <a name="is-the-replication-recovery-point-guaranteed"></a>A replikáció helyreállítási pont garantáltan?

Jelenleg egy georeplikált módban gyorsítótárakhoz, az adatmegőrzés és az importálási/exportálási funkció le van tiltva. Abban az esetben, ha egy ügyfél által kezdeményezett feladatátvétel vagy azokban az esetekben, ahol a replikációs kapcsolat megszakadt a georeplikált pár között, a másodlagos megőrzi a memória, amíg időpontig szinkronizálva van az elsődleges kiszolgálóról adatokat. Nincs ilyen helyzetekben a megadott helyreállítási pont garancia arra.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használható PowerShell vagy az Azure CLI-vel kezelheti a georeplikáció?

Jelenleg csak georeplikáció az Azure portal használatával is kezelheti.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>IP-címek fenntartási költséggel jár a saját adatok replikálása az Azure-régióban?

Ha georeplikációt használ, az elsődleges társított gyorsítótárban lévő adatok replikálása a másodlagos összekapcsolt gyorsítótár. A két társított gyorsítótárak Fájlmegosztáséval azonos régióban találhatók, van-e az adatátvitel számítunk fel díjat. Ha a két társított gyorsítótárak különböző Azure-régióban, a Georeplikáció adatforgalmi díjjal az adatok az egyéb Azure-régióban való replikálásához használt sávszélesség költségét. További információkért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért volt a sikertelen társított gyorsítótár törlése során?

Ha két gyorsítótárak össze vannak kapcsolva, cache vagy az erőforráscsoport, amely tartalmazza őket, amíg nem törli a georeplikációs hivatkozás nem törölhető. Törölje az erőforráscsoportot, amely tartalmazza az egyik vagy mindkét a társított gyorsítótárak kísérli meg, ha a többi erőforrást az erőforráscsoport törlése, de az erőforráscsoport marad a `deleting` erőforráscsoporthoz tartozik, az állami és minden társított gyorsítótárak maradnak a `running`állapota. Az erőforráscsoport és a benne lévő társított gyorsítótárak törlésének befejezéséhez felosztása a georeplikációs hivatkozás leírtak szerint [georeplikációs hivatkozás eltávolítása](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Milyen régióban érdemes használni a saját másodlagos összekapcsolt gyorsítótár?

Általános ajánlott a gyorsítótár és az azt elérő alkalmazás Azure ugyanabban a régióban található. Ha az alkalmazás egy elsődleges és tartalék régió, majd az elsődleges és másodlagos gyorsítótárak ezekben a régiókban kell létezik. Párosított régiók kapcsolatos további információkért lásd: [ajánlott eljárások – Azure párosított régiók](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a másodlagos társított gyorsítótár-ba irányuló feladatátvétel?

A georeplikáció, az eredeti kiadásban Azure Cache redis nem támogatja az Automatikus feladatátvétel Azure-régióban. Georeplikáció elsősorban a vész-helyreállítási helyzetekre szolgál. Distater helyreállítás ügyfelek kell viszi, megjelenik egy biztonsági mentési régióban az egész alkalmazáscsoportokat az koordinált módon ahelyett, hogy az egyes alkalmazás-összetevők megállapítani, hogy mikor váltson át a saját biztonsági másolatokat. Ez különösen fontos a Redis. Egyik fő előnye, Redis, hogy azt egy rendkívül alacsony késésű tárolóban. Ha az alkalmazás a Redis átadja a feladatokat más Azure-régióban, de a számítási kapacitás nem létezik, a hozzáadott ideje kellene észrevehető hatása a teljesítményre. Ebből kifolyólag szeretnénk kerülni a Redis futtatása sikertelen keresztül automatikusan átmeneti rendelkezésre állási problémák miatt.

Jelenleg kezdeményezze a feladatátvételt, kell a georeplikációs hivatkozás eltávolítása az Azure Portalon, és lépjen be a kapcsolati végpontot, a Redis-ügyfél a társított elsődleges-gyorsítótárból (korábbi nevén társított) másodlagos gyorsítótárba. Amikor a rendszer a két gyorsítótárak azok a tárfióktárolók, a replika egy szokásos írási-olvasási gyorsítótár válik újra, és közvetlenül a Redis-ügyfelektől érkező kéréseket fogad.


## <a name="next-steps"></a>További lépések

Tudjon meg többet a [Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md).

