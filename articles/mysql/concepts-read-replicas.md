---
title: Replikák olvasása – Azure Database for MySQL.
description: 'Ismerje meg a replikák olvasását az Azure Database for MySQL-ben: régiók kiválasztása, replikák létrehozása, replikákhoz való csatlakozás, replikáció figyelése és a replikáció leállítása.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 98461928e465a103f73761afce5270234224fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167349"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvasási replikák az Azure Database for MySQL-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MySQL-kiszolgálóról egy csak olvasható kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A binlog replikációról a [MySQL-adatok replikációjának áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakörben olvashat bővebben.

A replikák olyan új kiszolgálók, amelyeket a MySQL-kiszolgálók hoz való rendszeres Azure Database-hez hasonlóan kezel. Minden egyes olvasási replika, a kiosztott számítási virtuális magok és a tárolás GB/ hó.

Ha többet szeretne megtudni a MySQL replikációs szolgáltatásairól és problémáiról, olvassa el a [MySQL replikációs dokumentációját.](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="when-to-use-a-read-replica"></a>Mikor kell olvasni a kópiát használni?

Az olvasási replika szolgáltatás segít az olvasási igényű munkaterhelések teljesítményének és méretezésének javításában. Olvasási számítási feladatok elkülöníthetők a replikák, míg az írási számítási feladatok a főkiszolgálóra irányítható.

Gyakori forgatókönyv, hogy az üzleti intelligencia és az analitikus számítási feladatok az olvasási replika, mint a jelentési forrás.

Mivel a replikák írásvédettek, nem csökkentik közvetlenül a főkiszolgáló írási kapacitásterheit. Ez a funkció nem az írásigényes számítási feladatok.

Az olvasási replika szolgáltatás a MySQL aszinkron replikációt használja. A szolgáltatás nem szinkron replikációs forgatókönyvekhez való. Mérhető késleltetés lesz a mester és a replika között. A kópia adatai végül konzisztensek lesznek a főkiszolgálón lévő adatokkal. Ezt a szolgáltatást olyan számítási feladatokhoz használja, amelyek képesek ezt a késleltetést kezelni.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
A főkiszolgálótól eltérő régióban hozhat létre olvasási kópiát. A régiók közötti replikáció hasznos lehet olyan esetekben, mint a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A master kiszolgáló bármely [Azure Database for MySQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)használható.  A főkiszolgáló rendelkezhet egy replika a párosított régióban vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replikarégiók érhetők el a fő régiótól függően.

[![Replikaterületek olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replikaterületek
Az alábbi régiók bármelyikében létrehozhat olvasási kópiát, függetlenül attól, hogy a főkiszolgáló hol található. A támogatott univerzális replikarégiók a következők:

Kelet-Ausztrália, Ausztrália Délkelet, USA középső része, Kelet-Ázsia, USA keleti régiója 2, Japán kelet, Nyugat-Japán, Korea Közép-Korea, Korea South, Észak-Közép-USA, Észak-Európa, Usa középső középső régiója, Délkelet-Ázsia, Egyesült Királyság déli, egyesült királyságbeli nyugati, nyugat-európai, usa nyugati régiója.

*Az USA nyugati régiója 2 átmenetileg nem érhető el régióközi replikahelyként.


### <a name="paired-regions"></a>Párosított régiók
Az univerzális replikarégiók mellett létrehozhat egy olvasási replikát a főkiszolgáló Azure-ban párosított régióban. Ha nem ismeri a régió párját, további információaz [Azure Paired Regions cikkből.](../best-practices-availability-paired-regions.md)

Ha régióközi replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a más régiók helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adatok rezidensségét.  

Vannak azonban olyan korlátozások, amelyeket figyelembe kell venni: 

* Regionális elérhetőség: Az Azure Database for MySQL az USA nyugati részén 2, Franciaország középső részén, az Egyesült Arab Emírségek északi részén és Németország központi részén érhető el. A párosított régiók azonban nem érhetők el.
    
* Egyirányú párok: Egyes Azure-régiók csak egy irányban vannak párosítva. Ezek a régiók közé tartozik Nyugat-India, Brazília déli, és az Egyesült Államok gov Virginia. 
   Ez azt jelenti, hogy a nyugat-indiai főkiszolgáló replikát hozhat létre Dél-Indiában. A dél-indiai főkiszolgáló azonban nem hozhat létre kópiát Nyugat-Indiában. Ez azért van, mert Nyugat-India másodlagos régió Dél-India, de Dél-India másodlagos régió nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása

Ha a főkiszolgáló nem rendelkezik meglévő replikakiszolgálókkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikációra.

A replika-munkafolyamat létrehozásakor létrejön egy üres Azure-adatbázis a MySQL-kiszolgálóhoz. Az új kiszolgáló tele van a főkiszolgálón lévő adatokkal. A létrehozási idő a főkiszolgálón lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt időtől függ. Az idő néhány perctől több óráig terjedhet.

Minden replika engedélyezve van a tároló [automatikus növekedéséhez.](concepts-pricing-tiers.md#storage-auto-grow) Az automatikus növekedés funkció lehetővé teszi, hogy a replika lépést tartson a replikált adatokkal, és megakadályozza a replikáció megszakítását a tárolón kívüli hibák miatt.

Ismerje meg, hogyan [hozhat létre olvasási replikát az Azure Portalon.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Csatlakozás kópiához

Létrehozáskor a replika örökli a főkiszolgáló tűzfalszabályait vagy virtuális hálózati szolgáltatásvégpontját. Ezt követően ezek a szabályok függetlenek a főkiszolgálótól.

A replika örökli a rendszergazdai fiókot a főkiszolgálótól. A főkiszolgáló összes felhasználói fiókja replikálása az olvasási replikákra történik. Az olvasási kópiához csak a főkiszolgálón elérhető felhasználói fiókok használatával lehet csatlakozni.

Csatlakozhat a replikához a gazdagépnevével és egy érvényes felhasználói fiókkal, ugyanúgy, mint egy normál Azure-adatbázis a MySQL-kiszolgálóhoz. A **myreplica** nevű kiszolgáló esetében a **myadmin**admin felhasználónévvel csatlakozhat a replikához a mysql CLI használatával:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A kérdésben adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>Replikáció monitorozása

Az Azure Database for MySQL biztosítja a **replikációs késés másodpercben** metrika az Azure Monitorban. Ez a metrika csak replikák hoz érhető el.

Ezt a metrikát a `seconds_behind_master` MySQL `SHOW SLAVE STATUS` parancsában elérhető metrika alapján számítjuk ki.

Állítson be egy riasztást, amely tájékoztatja, ha a replikációs késés eléri a számítási feladatok hoz nem elfogadható értéket.

## <a name="stop-replication"></a>Replikáció leállítása

A főkiszolgáló és a kópia közötti replikáció leállíthatja. A főkiszolgáló és az olvasási kópia közötti replikáció leállítása után a replika önálló kiszolgálóvá válik. Az önálló kiszolgálón lévő adatok azok az adatok, amelyek a replikáció leállításakor a replikán rendelkezésre állnak. Az önálló kiszolgáló nem éri el a főkiszolgálót.

Ha úgy dönt, hogy leállítja a replikák replikációját, az elveszíti az összes csatolást az előző kópiához és más kópiákhoz. A főkiszolgáló és a kópia között nincs automatikus feladatátvétel.

> [!IMPORTANT]
> Az önálló kiszolgáló nem alakítható újra kópiává.
> Mielőtt leállítja a replikációt egy olvasási kópián, győződjön meg arról, hogy a replika rendelkezik az összes szükséges adatkal.

További információ a [kópia replikációjának leállításáról.](howto-read-replicas-portal.md)

## <a name="considerations-and-limitations"></a>Szempontok és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Az olvasási replikák jelenleg csak az általános cél és a memória optimalizált tarifacsomagok érhetők el.

### <a name="master-server-restart"></a>Főkiszolgáló újraindítása

Ha olyan mesterkópiához hoz létre kópiát, amelynem rendelkezik meglévő kópiákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikációra. Ezt vegye figyelembe, és végezze el ezeket a műveleteket csúcsidőn kívüli időszakban.

### <a name="new-replicas"></a>Új kópiák

Az olvasási replika új Azure-adatbázisként jön létre a MySQL-kiszolgálóhoz. Meglévő kiszolgáló nem alakítható át kópiává. Nem hozhat létre más olvasott kópiát.

### <a name="replica-configuration"></a>Replika konfigurációja

A kópia a főkiszolgálóval azonos kiszolgálókonfigurációhasználatával jön létre. A replika létrehozása után számos beállítás módosítható a fő kiszolgálótól függetlenül: számítási generálás, virtuális magok, tárolás és biztonsági mentés megőrzési időszaka. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.

> [!IMPORTANT]
> Mielőtt a főkiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

A tűzfalszabályok, a virtuális hálózati szabályok és a paraméterbeállítások a kópia létrehozásakor a főkiszolgálótól a kópiához öröklődnek. Ezt követően a replika szabályai függetlenek.

### <a name="stopped-replicas"></a>Leállított replikák

Ha leállítja a replikációt a főkiszolgáló és az olvasási kópia között, a leállított replika önálló kiszolgálóvá válik, amely elfogadja az olvasást és az írást is. Az önálló kiszolgáló nem alakítható újra kópiává.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és önálló kiszolgálók

A főkiszolgáló törlésekor a replikáció leáll az összes olvasási replikára. Ezek a replikák automatikusan önálló kiszolgálókká válnak, és olvasási és írási műveleteket is fogadnak. Maga a főkiszolgáló törlődik.

### <a name="user-accounts"></a>Felhasználói fiókok

A főkiszolgáló felhasználói replikálódnak az olvasási replikákra. Az olvasási kópiához csak a főkiszolgálón elérhető felhasználói fiókok használatával lehet csatlakozni.

### <a name="server-parameters"></a>Kiszolgálóparaméterek

Az adatszinkronizálás biztosítása és az esetleges adatvesztés vagy -sérülés elkerülése érdekében bizonyos kiszolgálóparaméterek zárolva vannak, hogy ne lehessen őket módosítani olvasási replikák használata során.

A következő kiszolgálóparaméterek a fő- és a replikakiszolgálókon is zárolva vannak:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

A [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter zárolva van a replikakiszolgálókon. 

### <a name="other"></a>Egyéb

- A globális tranzakcióazonosítók (GTID) nem támogatottak.
- A kópia kópiájának létrehozása nem támogatott.
- A memóriában lévő táblák miatt a kópiák nem lesznek szinkronban. Ez a MySQL replikációs technológia korlátozása. További információt a [MySQL referenciadokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) talál.
- Győződjön meg arról, hogy a főkiszolgálói táblák rendelkeznek elsődleges kulcsokkal. Az elsődleges kulcsok hiánya replikációs késést okozhat a főkiszolgáló és a replikák között.
- Tekintse át a MySQL replikációs korlátozások teljes listáját a [MySQL dokumentációjában](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure Portalhasználatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és REST API használatával](howto-read-replicas-cli.md)
