---
title: Az Azure Cache for Redis georeplikációjának beállítása | Microsoft dokumentumok
description: Ismerje meg, hogyan replikálhatja az Azure-gyorsítótárat a Redis-példányok földrajzi régiók között.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129431"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>A georeplikáció beállítása az Azure Cache for Redis számára

A georeplikáció két prémium szintű Azure-gyorsítótár redis-példányok összekapcsolására szolgáló mechanizmust biztosít. Az egyik gyorsítótár az elsődleges csatolt gyorsítótár, a másik pedig másodlagos csatolt gyorsítótár. A másodlagos csatolt gyorsítótár írásvédetté válik, és az elsődleges gyorsítótárba írt adatok replikálódnak a másodlagos csatolt gyorsítótárba. Ez a funkció a gyorsítótár Replikálására használható az Azure-régiók között. Ez a cikk egy útmutatót a georeplikáció konfigurálása a prémium szintű Azure-gyorsítótár Redis-példányok.

## <a name="geo-replication-prerequisites"></a>Georeplikáció előfeltételei

A két gyorsítótár közötti georeplikáció konfigurálásához a következő előfeltételeket kell teljesíteni:

- Mindkét gyorsítótár [prémium szintű](cache-premium-tier-intro.md) gyorsítótár.
- Mindkét gyorsítótár ugyanabban az Azure-előfizetésben van.
- A másodlagos csatolt gyorsítótár vagy azonos gyorsítótárméretű, vagy nagyobb a gyorsítótár mérete, mint az elsődleges csatolt gyorsítótár.
- Mindkét gyorsítótár jön létre, és egy futó állapotban.

Egyes funkciókat a georeplikáció nem támogat:

- A helypersistncia nem támogatott a georeplikáció.
- A fürtözés akkor támogatott, ha mindkét gyorsítótár fürtözése engedélyezve van, és azonos számú szegmensek.
- Az azonos virtuális hálózatgyorsítótárai támogatottak.
- A különböző Virtuális és tiszta- és nagygépek gyorsítótárait kikötések támogatják. További információ: [Használhatja a georeplikációt a gyorsítótáraimmal a virtuális hálózatban?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

A georeplikáció konfigurálása után a következő korlátozások vonatkoznak a csatolt gyorsítótárpárra:

- A másodlagos csatolt gyorsítótár írásvédett; olvashat róla, de nem írhat rá adatokat. 
- A másodlagos csatolt gyorsítótárban a hivatkozás hozzáadása előtt lévő adatok törlődnek. Ha azonban a georeplikációt később eltávolítja, a replikált adatok a másodlagos csatolt gyorsítótárban maradnak.
- Egyik gyorsítótár [scale](cache-how-to-scale.md) at sem skálázhatja, amíg a gyorsítótárak össze vannak kapcsolva.
- A [szegmensek száma](cache-how-to-premium-clustering.md) nem módosítható, ha a gyorsítótár fürtözése engedélyezve van.
- Egyik gyorsítótárban sem engedélyezhető az adatmegőrzés.
- Mindkét gyorsítótárból [exportálható.](cache-how-to-import-export-data.md#export)
- Nem [importálható](cache-how-to-import-export-data.md#import) a másodlagos csatolt gyorsítótárba.
- A gyorsítótárak leválasztásáig nem törölheti sem a csatolt gyorsítótárat, sem az azokat tartalmazó erőforráscsoportot. További információ: [Miért nem sikerült a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Ha a gyorsítótárak különböző régiókban találhatók, a hálózati kimenő forgalom költségei a régiók között áthelyezett adatokra vonatkoznak. További információ: [Mennyibe kerül az adataim replikálása az Azure-régiókközött?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Az elsődleges és a másodlagos csatolt gyorsítótár között nem történik automatikus feladatátvétel. Az ügyfélalkalmazások feladatátvételével kapcsolatos további információkért és a [Másodlagos csatolt gyorsítótár feladatátvételi munkáról](#how-does-failing-over-to-the-secondary-linked-cache-work) című témakörben talál további információt?

## <a name="add-a-geo-replication-link"></a>Georeplikációs hivatkozás hozzáadása

1. Ha két gyorsítótárat szeretne összekapcsolni a földrajzi replikációhoz, kattintson az első számú, elsődleges csatolt gyorsítótár Erőforrás **menüjében** lévő Georeplikáció parancsra. Ezután kattintson **a Gyorsítótár-replikáció hozzáadása hivatkozása** a **georeplikációs** panelen.

    ![Hivatkozás hozzáadása](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kattintson a kívánt másodlagos gyorsítótár nevére a **Kompatibilis gyorsítótárak** listában. Ha a másodlagos gyorsítótár nem jelenik meg a listában, ellenőrizze, hogy a [georeplikációs előfeltételei](#geo-replication-prerequisites) a másodlagos gyorsítótár teljesülnek. A gyorsítótárak régió szerinti szűréséhez kattintson a térképen lévő területre, ha csak a Kompatibilis gyorsítótárak listában lévő **gyorsítótárakat** szeretné megjeleníteni.

    ![Georeplikációval kompatibilis gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    A csatolási folyamatot a helyi menü segítségével is elindíthatja, vagy megtekintheti a másodlagos gyorsítótár részleteit.

    ![Georeplikáció helyi menüje](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kattintson a **Hivatkozás** gombra a két gyorsítótár összekapcsolására és a replikációs folyamat megkezdésére.

    ![Hivatkozás-gyorsítótárak](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. A replikációs folyamat előrehaladását a **georeplikációs** panelen tekintheti meg.

    ![Csatolási állapot](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    A csatolási állapot az **áttekintő** panelen is megtekintheti az elsődleges és a másodlagos gyorsítótárakat.

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    A replikációs folyamat befejezése után a **Kapcsolat állapota** Sikeres állapotra **változik.**

    ![Gyorsítótár állapota](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Az elsődleges csatolt gyorsítótár továbbra is használható a csatolási folyamat során. A másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat befejeződik.

## <a name="remove-a-geo-replication-link"></a>Georeplikációs hivatkozás eltávolítása

1. A két gyorsítótár közötti kapcsolat eltávolításához és a georeplikáció leállításához kattintson a **gyorsítótárak leválasztása** a **georeplikációs** panelről elemre.
    
    ![Gyorsítótárak leválasztása](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Amikor a leválasztási folyamat befejeződik, a másodlagos gyorsítótár olvasási és írási célokra is elérhető.

>[!NOTE]
>A georeplikációs kapcsolat eltávolításakor az elsődleges csatolt gyorsítótárreplikált adatai a másodlagos gyorsítótárban maradnak.
>
>

## <a name="geo-replication-faq"></a>Georeplikáció – gyakori kérdések

- [Használhatom a georeplikációt standard vagy alapszintű gyorsítótárral?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [A gyorsítótár használható a csatolási vagy lekapcsolási folyamat során?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Kettőnél több gyorsítótárat is összekapcsolhatok?](#can-i-link-more-than-two-caches-together)
- [Összekapcsolhatok két gyorsítótárat különböző Azure-előfizetésekből?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Összekapcsolhatok két különböző méretű gyorsítótárat?](#can-i-link-two-caches-with-different-sizes)
- [Használhatom a georeplikációt fürtözés engedélyezésével?](#can-i-use-geo-replication-with-clustering-enabled)
- [Használhatom a georeplikációt a gyorsítótáraimmal egy virtuális hálózatban?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Mi a Redis georeplikációreplikációjának replikációs ütemezése?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Mennyi ideig tart a georeplikáció replikációja?](#how-long-does-geo-replication-replication-take)
- [Garantált a replikációs helyreállítási pont?](#is-the-replication-recovery-point-guaranteed)
- [Használhatom a PowerShellt vagy az Azure CLI-t a georeplikáció kezeléséhez?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Mennyibe kerül az adataim replikálása az Azure-régiókközött?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Miért nem sikerült a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Melyik régiót használjam a másodlagos csatolt gyorsítótárhoz?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Hogyan működik a másodlagos csatolt gyorsítótár rakoncáta?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Használhatom a georeplikációt standard vagy alapszintű gyorsítótárral?

Nem, a georeplikáció csak a prémium szintű gyorsítótárak hoz érhető el.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>A gyorsítótár használható a csatolási vagy lekapcsolási folyamat során?

- Csatoláskor az elsődleges csatolt gyorsítótár elérhető marad, amíg a csatolási folyamat befejeződik.
- Csatoláskor a másodlagos csatolt gyorsítótár nem érhető el, amíg a csatolási folyamat be nem fejeződik.
- A csatolás leválasztásakor mindkét gyorsítótár elérhető marad, amíg a leválasztási folyamat befejeződik.

### <a name="can-i-link-more-than-two-caches-together"></a>Kettőnél több gyorsítótárat is összekapcsolhatok?

Nem, csak két gyorsítótárat kapcsolhat össze.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Összekapcsolhatok két gyorsítótárat különböző Azure-előfizetésekből?

Nem, mindkét gyorsítótárnak ugyanabban az Azure-előfizetésben kell lennie.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Összekapcsolhatok két különböző méretű gyorsítótárat?

Igen, mindaddig, amíg a másodlagos csatolt gyorsítótár nagyobb, mint az elsődleges csatolt gyorsítótár.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Használhatom a georeplikációt fürtözés engedélyezésével?

Igen, mindaddig, amíg mindkét gyorsítótárak azonos számú szilánkok.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Használhatom a georeplikációt a gyorsítótáraimmal egy virtuális hálózatban?

Igen, a Virtuálisés-gyorsítótárak georeplikációját a következő kikötésekkel támasztja alá:

- Az azonos virtuális hálózat gyorsítótárai közötti georeplikáció támogatott.
- A különböző virtuális értékek gyorsítótárai közötti georeplikáció is támogatott.
  - Ha a VNET-k ugyanabban a régióban vannak, virtuális [hálózat-társviszony-létesítés](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) vagy [VPN-átjáró virtuálishálózat-vnet-kapcsolat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)használatával csatlakoztathatja őket.
  - Ha a VNET-ek különböző régiókban vannak, a virtuális hálózat-társviszony-létesítést használó georeplikáció nem támogatott, mert az alapszintű belső terheléselosztók megszorítása van. A virtuális hálózati társviszony-létesítési korlátozásokról a [Virtuális hálózat - társviszony-létesítés – követelmények és korlátozások](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)című témakörben talál további információt. Az ajánlott megoldás a VPN-átjáró vnet-vnet-kapcsolat használata.

[Ezzel az Azure-sablonnal](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)gyorsan üzembe helyezhet két georeplikált gyorsítótárat egy VPN-átjáró vnet-vnet-kapcsolattal csatlakoztatott virtuális hálózatba.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Mi a Redis georeplikációreplikációjának replikációs ütemezése?

A replikáció folyamatos és aszinkron, és nem történik meg egy adott ütemezés szerint. Az elsődleges en végzett összes írás azonnal és aszinkron módon replikálódik a másodlagos.

### <a name="how-long-does-geo-replication-replication-take"></a>Mennyi ideig tart a georeplikáció replikációja?

A replikáció növekményes, aszinkron és folyamatos, és a szükséges idő nem sokban különbözik a régiók közötti késéstől. Bizonyos körülmények között előfordulhat, hogy a másodlagos gyorsítótárra van szükség az elsődleges adatok teljes szinkronizálásához. A replikációs idő ebben az esetben számos tényezőtől függ, például: az elsődleges gyorsítótár terhelése, a rendelkezésre álló hálózati sávszélesség és a régiók közötti késés. Egy teljes 53 GB-os georeplikált pár replikációs idejét találtuk 5 és 10 perc között.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Garantált a replikációs helyreállítási pont?

Georeplikált módban lévő gyorsítótárak esetén az adatmegőrzés le van tiltva. Ha egy georeplikált pár nincs csatolva, például egy ügyfél által kezdeményezett feladatátvétel, a másodlagos csatolt gyorsítótár megtartja a szinkronizált adatokat addig az időpontig. Ilyen helyzetekben nem garantálható a helyreállítási pont.

A helyreállítási pont beszerzéséhez [exportáljon](cache-how-to-import-export-data.md#export) bármelyik gyorsítótárból. Később [importálhatja](cache-how-to-import-export-data.md#import) az elsődleges csatolt gyorsítótárba.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Használhatom a PowerShellt vagy az Azure CLI-t a georeplikáció kezeléséhez?

Igen, a georeplikáció kezelhető az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül. További információt a [PowerShell-dokumentumok](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ban vagy az [Azure CLI-dokumentumokban](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest)talál.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Mennyibe kerül az adataim replikálása az Azure-régiókközött?

Georeplikáció használataesetén az elsődleges csatolt gyorsítótárból származó adatok replikálódnak a másodlagos csatolt gyorsítótárba. Nincs díj az adatátvitelért, ha a két csatolt gyorsítótár ugyanabban a régióban van. Ha a két csatolt gyorsítótár különböző régiókban található, az adatátviteli díj a két régión átkelő adatok hálózati kimenő forgalom költsége. További információt a [Sávszélesség-díjszabás részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Miért nem sikerült a művelet, amikor megpróbáltam törölni a csatolt gyorsítótárat?

A földrajzireplikált gyorsítótárak és azok erőforráscsoportjai nem törölhetők a csatolás közben, amíg el nem távolítja a georeplikációs kapcsolatot. Ha megpróbálja törölni az egyik vagy mindkét csatolt gyorsítótárat tartalmazó erőforráscsoportot, az erőforráscsoport többi erőforrása törlődik, de az erőforráscsoport állapotban `deleting` `running` marad, és az erőforráscsoport bármely csatolt gyorsítótára állapotban marad. Az erőforráscsoport és a benne lévő csatolt gyorsítótárak teljes törléséhez szüntesse meg a gyorsítótárak csatolásának leválasztását a [Georeplikációs kapcsolat eltávolítása című](#remove-a-geo-replication-link)részben leírtak szerint.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Melyik régiót használjam a másodlagos csatolt gyorsítótárhoz?

Általában azt javasoljuk, hogy a gyorsítótár ugyanabban az Azure-régióban létezzen, mint az azt elérő alkalmazás. A külön elsődleges és tartalék régiókkal rendelkező alkalmazások esetében ajánlott az elsődleges és másodlagos gyorsítótárak léteznek ugyanabban a régiókban. A párosított régiókról további információt az [Ajánlott eljárások – Azure párosított régiók című témakörben talál.](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Hogyan működik a másodlagos csatolt gyorsítótár rakoncáta?

Az Azure-régiók közötti automatikus feladatátvétel nem támogatott a georeplikált gyorsítótárak esetében. Vész-helyreállítási forgatókönyv esetén az ügyfelek nek a teljes alkalmazásverem összehangolt módon kell a biztonsági mentési régióban. Ha az egyes alkalmazás-összetevők eldöntik, hogy mikor váltsanak a biztonsági mentésekre, az negatívan befolyásolhatja a teljesítményt. A Redis egyik legfontosabb előnye, hogy ez egy nagyon alacsony késésű üzlet. Ha az ügyfél fő alkalmazása a gyorsítótártól eltérő régióban van, a hozzáadott oda-vissza idő észrevehető hatással lenne a teljesítményre. Emiatt elkerüljük, hogy az átmeneti rendelkezésre állási problémák miatt automatikusan felboruljon.

Az ügyfél által kezdeményezett feladatátvétel elindításához először kapcsolja le a gyorsítótárak összekapcsolására. Ezután módosítsa a Redis-ügyfelet a (korábban csatolt) másodlagos gyorsítótár kapcsolatvégpontjának használatára. Ha a két gyorsítótár nincs csatolva, a másodlagos gyorsítótár ismét rendszeres írási-olvasási gyorsítótárvá válik, és közvetlenül a Redis-ügyfelektől fogadja a kérelmeket.

## <a name="next-steps"></a>További lépések

További információ az [Azure Cache for Redis Premium tier- ről.](cache-premium-tier-intro.md)
