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
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991577"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Georeplikáció konfigurálása az Azure Cache Redis

A georeplikáció lehetővé teszi a két prémium szintű Azure Cache Redis-példány csatolása. Egy gyorsítótár az elsődleges társított gyorsítótár, a másik pedig a csatolt másodlagos gyorsítótáraként választja. A másodlagos összekapcsolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatok replikálódnak a másodlagos összekapcsolt gyorsítótár. Ez a funkció segítségével gyorsítótár replikálása az Azure-régiók között. Ez a cikk ismerteti egy útmutató, amellyel georeplikáció konfigurálása a Redis-példány a prémium szintű Azure Cache.

## <a name="geo-replication-prerequisites"></a>Georeplikáció – Előfeltételek

Georeplikáció konfigurálása két gyorsítótárak között, a következő előfeltételeknek kell teljesülniük:

- Mindkét gyorsítótárak [prémium szintű](cache-premium-tier-intro.md) gyorsítótárazza.
- Mindkét gyorsítótárak vannak az Azure-előfizetéshez.
- A másodlagos összekapcsolt gyorsítótár ugyanazon a gyorsítótár méretét vagy a gyorsítótár az elsődleges társított gyorsítótár-nál nagyobb méretű.
- Mindkét-EK létrehozása és a egy működőképes állapotba.

Egyes funkciók nem támogatottak a georeplikáció:

- Adatmegőrzés a georeplikáció nem támogatott.
- Fürtszolgáltatás támogatott, ha mindkét gyorsítótárak rendelkezik a fürtözés engedélyezve van, és a szegmensek azonos számú.
- Gyorsítótárak ugyanabban a vnetben támogatottak.
- Gyorsítótárak eltérő virtuális hálózatokba tartozó korlátozásokkal támogatottak. Lásd: [is használhatják a georeplikáció a gyorsítótárak egy virtuális hálózaton?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) további információt.

Georeplikáció konfigurálása után az alábbi korlátozások vonatkoznak az összekapcsolt gyorsítótár pár:

- A másodlagos összekapcsolt gyorsítótár a csak olvasható; Tudjon meg azt a, de semmilyen adatot nem írhatnak abba. 
- A másodlagos összekapcsolt gyorsítótár a korábban a hivatkozás hozzáadása előtti adatokat a rendszer eltávolítja. Ha a georeplikáció későbbi eltávolítása azonban a replikált adatok marad, a másodlagos összekapcsolt gyorsítótár.
- Nem lehet [méretezési](cache-how-to-scale.md) vagy gyorsítótár, míg a gyorsítótárak van csatolva.
- Nem lehet [módosítsa a szegmensek száma](cache-how-to-premium-clustering.md) Ha a gyorsítótár tartalmaz a fürtözés engedélyezve van.
- Adatmegőrzés vagy-gyorsítótár nem engedélyezhető.
- Is [exportálása](cache-how-to-import-export-data.md#export) vagy a gyorsítótárból.
- Nem lehet [importálás](cache-how-to-import-export-data.md#import) a csatolt másodlagos gyorsítótárba.
- Összekapcsolt gyorsítótár vagy az erőforráscsoport, amely tartalmazza őket, amíg, mivel a gyorsítótárak leválasztása nem törölhető. További információkért lásd: [miért volt a művelet sikertelen társított gyorsítótár törlése során?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Mivel a gyorsítótárak különböző régiókban találhatók, az adatok régiók közötti áthelyezése hálózati adatkimeneti költségek vonatkoznak. További információkért lásd: [mennyi költséggel jár a saját adatok replikálása az Azure-régióban?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatikus feladatátvétel nem következik be, az elsődleges és másodlagos összekapcsolt gyorsítótár között. További információk és feladatátvételi ügyfélalkalmazás információk [a másodlagos társított gyorsítótár-ba irányuló feladatátvétel működése?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>A georeplikációs hivatkozás hozzáadása

1. A két gyorsítótárak összekapcsolása a georeplikációhoz, fist kattintson **georeplikációs** a gyorsítótár Resource menüből, hogy szeretne-e az elsődleges kell társított gyorsítótár. Ezután kattintson **gyorsítótár-replikációs hivatkozás hozzáadása** származó a **georeplikációs** panelen.

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótár nevére a **kompatibilis gyorsítótárak** listája. Ha a másodlagos gyorsítótár nem jelenik meg a listában, ellenőrizze, hogy a [georeplikációs Előfeltételek](#geo-replication-prerequisites) a másodlagos gyorsítótár teljesülnek. A gyorsítótárak régiónként szűréséhez kattintson a régió, a térképen megjelenítendő a gyorsítótárak az **kompatibilis gyorsítótárak** listája.

    ![Kompatibilis gyorsítótárak Georeplikáció útján](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    A csatolási folyamat elindításához is, vagy a másodlagos gyorsítótár részleteinek megtekintése a helyi menü használatával.

    ![Georeplikáció helyi menü](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson a **hivatkozás** kapcsolja össze a két gyorsítótárakat és a replikáció megkezdéséhez.

    ![Hivatkozás gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikálási folyamat előrehaladását a megtekintheti a **georeplikációs** panelen.

    ![Csatolás állapota](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A hivatkozási állapotát is megtekintheti a **áttekintése** panel az elsődleges és másodlagos gyorsítótárak esetében.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    A replikálási folyamat befejeződése után a **hivatkozás állapota** vált **sikeres**.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Az elsődleges társított gyorsítótár használata elérhető marad a csatolási folyamat során. A másodlagos összekapcsolt gyorsítótár nem érhető el, amíg a csatolási folyamat befejeződött.

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

- Linking, ha az elsődleges társított gyorsítótár elérhető marad a csatolási folyamat végrehajtása során.
- Ha csatolja, a másodlagos összekapcsolt gyorsítótár nem érhető el, a csatolási folyamat befejezéséig.
- Leválasztásakor, mindkét gyorsítótárak elérhetők maradnak a leválasztása folyamat végrehajtása során.

### <a name="can-i-link-more-than-two-caches-together"></a>Is lehet hivatkozni több mint két gyorsítótárak együtt?

Nem, csak kapcsolat két gyorsítótárak együtt.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Összekapcsolhatja a különböző Azure-előfizetésekből két gyorsítótárakhoz?

Nem, mindkét gyorsítótárak az Azure-előfizetéshez kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Összekapcsolhatja a különböző méretű két gyorsítótárakhoz?

Igen, mindaddig, amíg a másodlagos összekapcsolt gyorsítótár mérete nagyobb, mint az elsődleges társított gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Használható a fürtözés engedélyezve van a georeplikáció?

Igen, mindaddig, amíg mindkét gyorsítótárak szegmensek azonos számú rendelkezik.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használható a saját virtuális hálózaton gyorsítótárak georeplikáció?

Igen, a virtuális hálózatok gyorsítótárak georeplikációs támogatott szem előtt a következőket:

- Georeplikáció útján az azonos virtuális hálózaton található gyorsítótárak között támogatott.
- Eltérő virtuális hálózatokba tartozó gyorsítótárak közötti georeplikáció használata is támogatott.
  - Ha a virtuális hálózat között ugyanabban a régióban, csatlakoztathatja őket használatával [virtuális hálózatok közötti társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) vagy egy [VPN-átjáró VNET – VNET kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Ha a virtuális hálózatok különböző régiókban, georeplikációt használ a virtuális hálózatok közötti társviszony-létesítés nem támogatott, a belső terheléselosztók alapszintű megkötés miatt. Virtuális hálózatok közötti társviszony-létesítési megkötések kapcsolatos további információkért lásd: [Vnet - Társviszony - követelmények és korlátozások](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Az ajánlott megoldás, hogy a VPN-átjáró a virtuális hálózatok közötti kapcsolat.

Használatával [az Azure-sablon](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), gyorsan üzembe helyezhet egy vnetbe, VPN-átjáró VNET – VNET kapcsolattal csatlakozó két georeplikált gyorsítótárak.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi az a replikáció ütemezését Redis georeplikáció?

Replikáció folyamatos és aszinkron, és a egy meghatározott ütemezés szerint nem történik. Az elsődleges végzett az összes írási művelet a rendszer azonnal, és aszinkron módon replikálja a másodlagos.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi georeplikációs replikációs időt igénybe?

Replikációs növekményes, a aszinkron és a folyamatos és az igénybe vett idő jelentősen különbözik a késés nem régiók között elosztva. Bizonyos körülmények esetén a másodlagos gyorsítótár is szükség ehhez az adatok a teljes szinkronizálás az elsődleges kiszolgálóról. A replikációs időt ebben az esetben az adott nyelvtől függ számos tényezőtől, például: az elsődleges gyorsítótár, a rendelkezésre álló hálózati sávszélességet és a régiók közötti késleltetés terhelését. Replikáció ideje a teljes 53 GB-os georeplikált párhoz bárhol lehetnek 5-10 perc között található.

### <a name="is-the-replication-recovery-point-guaranteed"></a>A replikáció helyreállítási pont garantáltan?

Az egy georeplikált módban gyorsítótárakhoz az adatmegőrzés le van tiltva. Nem összekapcsolt, például az ügyfél által kezdeményezett feladatátvételt egy georeplikált pár esetén a másodlagos összekapcsolt gyorsítótár tartja a szinkronizált adatok időpontig. Egyetlen helyreállítási pont garantálja az ilyen helyzetekben.

Egy helyreállítási pontot beszerzése [exportálása](cache-how-to-import-export-data.md#export) vagy a gyorsítótárból. Később is [importálás](cache-how-to-import-export-data.md#import) a társított elsődleges gyorsítótárba.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használható PowerShell vagy az Azure CLI-vel kezelheti a georeplikáció?

Igen, a georeplikáció kezelhetők az Azure portal, PowerShell vagy az Azure CLI használatával. További információkért lásd: a [PowerShell docs](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) vagy [Azure parancssori Felülettel kapcsolatos dokumentumok](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>IP-címek fenntartási költséggel jár a saját adatok replikálása az Azure-régióban?

Ha georeplikációt használ, az elsődleges társított gyorsítótárban lévő adatok replikálása a másodlagos összekapcsolt gyorsítótár. Nem jár az adatok átvitele a Ha a két társított gyorsítótárak ugyanabban a régióban. Ha a két társított gyorsítótárak különböző régiókban található, az adatforgalmi díjjal hálózati kimenő forgalom mennyibe kerül a adatok bármelyik régió között. További információkért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért volt a sikertelen társított gyorsítótár törlése során?

Georeplikált gyorsítótárakat és a csoporthoz tartozó erőforrás nem törölhető, amíg társított, amíg nem törli a georeplikációs hivatkozás. Törölje az erőforráscsoportot, amely tartalmazza az egyik vagy mindkét a társított gyorsítótárak kísérli meg, ha a többi erőforrást az erőforráscsoport törlése, de az erőforráscsoport marad a `deleting` erőforráscsoporthoz tartozik, az állami és minden társított gyorsítótárak maradnak a `running`állapota. Teljesen törölni az erőforráscsoportot és a benne lévő társított gyorsítótárak, leválasztani a gyorsítótárakat leírtak szerint [georeplikációs hivatkozás eltávolítása](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Milyen régióban érdemes használni a saját másodlagos összekapcsolt gyorsítótár?

Általános ajánlott a gyorsítótár és az azt elérő alkalmazás Azure ugyanabban a régióban található. Az önálló elsődleges és tartalék régió alkalmazások ajánlott az elsődleges és másodlagos gyorsítótárak szerepel ezekben a régiókban. Párosított régiók kapcsolatos további információkért lásd: [ajánlott eljárások – Azure párosított régiók](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a másodlagos társított gyorsítótár-ba irányuló feladatátvétel?

Azure-régiók között az Automatikus feladatátvétel georeplikált gyorsítótárak esetében nem támogatott. A vész-helyreállítási helyzet ügyfelek kell viszi, megjelenik az egész alkalmazáscsoportokat koordinált módon a biztonsági mentési régióban. Egyéni alkalmazás-összetevők döntse el, hogy mikor váltson át a biztonsági mentések saját negatívan befolyásolhatja a teljesítményt. Egyik fő előnye, Redis, hogy azt egy rendkívül alacsony késésű tárolóban. Ha az ügyfél fő alkalmazás egy másik régióban, mint a gyorsítótárban, az üzenetváltási időt kellene észrevehető hatása a teljesítményre. Ezért azt elkerülése érdekében automatikusan feladatátvétele átmeneti rendelkezésre állási problémák miatt.

Egy ügyfél által kezdeményezett feladatátvétel indításához először a gyorsítótárak leválasztása. Majd módosíthatja a Redis-ügyfél a kapcsolati végpont a (korábbi nevén társított) másodlagos gyorsítótár használatára. Ha a két gyorsítótárak leválasztása, a másodlagos gyorsítótár egy szokásos írási-olvasási gyorsítótár ismét válik, és közvetlenül a Redis-ügyfelektől érkező kéréseket fogad.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a [Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md).
