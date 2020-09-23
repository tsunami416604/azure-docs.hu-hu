---
title: A kiszolgáló csoport skálázása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 5b1a5da688b162c85d2be8580e29dc6ee9db6d40
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906477"
---
# <a name="server-group-size"></a>Kiszolgálócsoport mérete

Az nagy kapacitású (Citus) telepítési beállítás az együttműködő adatbázis-kiszolgálókat használja a lekérdezés-végrehajtás integrálással és további adattároláshoz. A "size" kiszolgálócsoport a kiszolgálók számát és az egyes erőforrások hardveres erőforrásait is jelenti.

## <a name="picking-initial-size"></a>Válogatás kezdeti mérete

Egy kiszolgálócsoport mérete, a csomópontok száma és a hardver kapacitása alapján könnyen módosítható ([lásd alább](#scale-a-hyperscale-citus-server-group)). Azonban továbbra is ki kell választania egy új kiszolgálócsoport kezdeti méretét. Íme néhány tipp az ésszerű választáshoz.

### <a name="multi-tenant-saas-use-case"></a>Több-bérlős SaaS-használati eset

Ha a nagy kapacitású (Citus) áttelepítést végez egy meglévő egycsomópontos PostgreSQL-adatbázis-példányból, javasoljuk, hogy válasszon olyan fürtöt, amelyben a feldolgozó virtuális mag és a RAM mennyisége megegyezik az eredeti példány értékével. Ilyen helyzetekben 2 – 3 – 4 teljesítménybeli javulás látható, mivel a horizontális felskálázás javítja az erőforrások kihasználtságát, és lehetővé teszi a kisebb indexek használatát stb.

A koordinátori csomóponthoz szükséges virtuális mag száma a meglévő munkaterheléstől (írási/olvasási sebesség) függ. A koordinátori csomópont nem igényel annyi RAM-t, mint a feldolgozó csomópontok, de a RAM-foglalás a virtuális mag száma alapján van meghatározva (a [nagy kapacitású konfigurációs beállításaiban](concepts-hyperscale-configuration-options.md)leírtak szerint), így a virtuális mag száma lényegében a valódi döntés.

### <a name="real-time-analytics-use-case"></a>Valós idejű elemzési használati eset

Teljes virtuális mag: Ha a munkamennyiség a RAM-ban található, akkor a nagy kapacitású (Citus) lineáris teljesítményének növelése várható a munkavégző magok számával arányosan. Az igényeinek megfelelő számú virtuális mag meghatározásához vegye figyelembe az egycsomópontos adatbázisban lévő lekérdezések aktuális késését, valamint a nagy kapacitású (Citus) szükséges késését. Ossza el az aktuális kését a kívánt késéssel, majd kerekítse az eredményt.

Feldolgozó RAM: a legjobb eset elegendő memória biztosítása lenne, amelyben a munkakészlet többsége elfér. Az alkalmazás által használt lekérdezések típusa befolyásolja a memória követelményeit. Az ELEMZÉSek MAGYARÁZATának futtatásával határozhatja meg, hogy mennyi memóriát igényel. Ne feledje, hogy a virtuális mag és a RAM méretezése a [nagy kapacitású konfigurációs beállítások](concepts-hyperscale-configuration-options.md) című cikkben leírtak szerint történik.

## <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását és a meglévő csomópontok virtuális mag növelését. A csomópontok hozzáadása nem eredményez állásidőt, és még a szegmensek áthelyezését is az új csomópontokra (a szegmensek közötti [újraelosztásnak](#rebalance-shards)nevezzük) a lekérdezések megszakítása nélkül történik.

### <a name="add-worker-nodes"></a>Munkavégző csomópontok hozzáadása

Csomópontok hozzáadásához nyissa meg a nagy kapacitású-(Citus-) kiszolgálócsoport **számítás + tárolás** lapját.  Ha a **munkavégző csomópontok száma** fölé húzza a csúszkát, az érték módosul.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Erőforrás-csúszkák":::

Kattintson a **Save (Mentés** ) gombra a módosított érték életbe léptetéséhez.

> [!NOTE]
> A növekedés és a mentés után a munkavégző csomópontok száma nem csökkenthető a csúszka használatával.

#### <a name="rebalance-shards"></a>Szegmensek kiegyensúlyozása

Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell osztania az [elosztott tábla](concepts-hyperscale-distributed-data.md#shards)szegmenseit, ami azt jelenti, hogy a meglévő csomópontokból származó szegmensek áthelyezése az újakra történik. Először ellenőrizze, hogy az új feldolgozók sikeresen befejezték-e az üzembe helyezést. Ezután indítsa el a szegmens-újraelosztást a psql-mel és a-t futtató fürt-koordinátor csomóponthoz való csatlakozással:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

A függvény a (z `rebalance_table_shards` ) argumentumban megnevezett tábla együttes [elhelyezés](concepts-hyperscale-colocation.md) csoportjában lévő összes táblát újraegyenlíti. Így nem kell minden elosztott táblához meghívnia a függvényt, csak egy reprezentatív táblán kell meghívnia az egyes közös helyek csoportjából.

### <a name="increase-or-decrease-vcores-on-nodes"></a>Virtuális mag növelése vagy csökkentése a csomópontokon

> [!NOTE]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ha módosítani szeretné a virtuális mag lévő csomópontok módosítását, vegye fel a [kapcsolatot az Azure ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Az új csomópontok hozzáadásán kívül növelheti a meglévő csomópontok képességeit is. A számítási kapacitás felfelé és lefelé való beállítása hasznos lehet a teljesítmény-kísérleteknél, valamint a forgalmi igények rövid vagy hosszú távú változásaihoz.

Ha módosítani szeretné az összes munkavégző csomópont virtuális mag, állítsa be a **virtuális mag** csúszkát a **konfiguráció alatt (munkavégző csomópont)**. A koordinátor csomópontjának virtuális mag egymástól függetlenül is módosítható. Állítsa be a **virtuális mag** csúszkát a  **konfiguráció (koordinátor csomópont)** alatt.

## <a name="next-steps"></a>Következő lépések

- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
