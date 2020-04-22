---
title: Replikák olvasása – Azure Database for MariaDB
description: 'Ismerje meg a replikák olvasását a MariaDB Azure Database szolgáltatásában: régiók kiválasztása, replikák létrehozása, replikákhoz való csatlakozás, replikáció figyelése és a replikáció leállítása.'
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b2e8bec18f4f5df6fb78a3c31aa0956ec7333eb5
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770113"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Olvasási replikák az Azure Database for MariaDB-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MariaDB-kiszolgálóról egy csak olvasható kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a MariaDB motor bináris napló (binlog) fájlpozíció-alapú replikációs technológiájával globális tranzakcióazonosítóval (GTID). A binlog replikációról a [binlog replikáció – áttekintés című témakörben olvashat bővebben.](https://mariadb.com/kb/en/library/replication-overview/)

A replikák olyan új kiszolgálók, amelyeket a MariaDB-kiszolgálók hoz való rendszeres Azure Database-hez hasonlóan kezel. Minden egyes olvasási replika, a kiosztott számítási virtuális magok és a tárolás GB/ hó.

A GTID replikációról a [MariaDB replikációs dokumentációjában](https://mariadb.com/kb/en/library/gtid/)olvashat bővebben.

## <a name="when-to-use-a-read-replica"></a>Mikor kell olvasni a kópiát használni?

Az olvasási replika szolgáltatás segít az olvasási igényű munkaterhelések teljesítményének és méretezésének javításában. Olvasási számítási feladatok elkülöníthetők a replikák, míg az írási számítási feladatok a főkiszolgálóra irányítható.

Gyakori forgatókönyv, hogy az üzleti intelligencia és az analitikus számítási feladatok az olvasási replika, mint a jelentési forrás.

Mivel a replikák írásvédettek, nem csökkentik közvetlenül a főkiszolgáló írási kapacitásterheit. Ez a funkció nem az írásigényes számítási feladatok.

Az olvasási replika szolgáltatás aszinkron replikációt használ. A szolgáltatás nem szinkron replikációs forgatókönyvekhez való. Mérhető késleltetés lesz a mester és a replika között. A kópia adatai végül konzisztensek lesznek a főkiszolgálón lévő adatokkal. Ezt a szolgáltatást olyan számítási feladatokhoz használja, amelyek képesek ezt a késleltetést kezelni.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
A főkiszolgálótól eltérő régióban hozhat létre olvasási kópiát. A régiók közötti replikáció hasznos lehet olyan esetekben, mint a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

> [!NOTE]
> A régiók közötti replikáció előzetes verzióban érhető el.

A MariaDB régió bármely [Azure-adatbázisában](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)rendelkezhet főkiszolgálóval.  A főkiszolgáló rendelkezhet egy replika a párosított régióban vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replikarégiók érhetők el a fő régiótól függően.

[![Replikaterületek olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replikaterületek
Az alábbi régiók bármelyikében létrehozhat olvasási kópiát, függetlenül attól, hogy a főkiszolgáló hol található. A támogatott univerzális replikarégiók a következők:

Kelet-Ausztrália, Ausztrália Délkelet, USA középső része, Kelet-Ázsia, USA keleti régiója 2, Japán kelet, Nyugat-Japán, Korea Közép-Korea, Korea South, Észak-Közép-USA, Észak-Európa, Usa középső középső régiója, Délkelet-Ázsia, Egyesült Királyság déli, egyesült királyságbeli nyugati, nyugat-európai, usa nyugati régiója.

*Az USA nyugati régiója 2 átmenetileg nem érhető el régióközi replikahelyként.

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replikarégiók mellett létrehozhat egy olvasási replikát a főkiszolgáló Azure-ban párosított régióban. Ha nem ismeri a régió párját, további információaz [Azure Paired Regions cikkből.](../best-practices-availability-paired-regions.md)

Ha régióközi replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a más régiók helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adatok rezidensségét.  

Vannak azonban olyan korlátozások, amelyeket figyelembe kell venni: 

* Regionális elérhetőség: Az Azure Database for MariaDB az USA nyugati részén 2, Franciaország középső részén, az Egyesült Arab Emírségek északi részén és Németország központjában érhető el. A párosított régiók azonban nem érhetők el.
    
* Egyirányú párok: Egyes Azure-régiók csak egy irányban vannak párosítva. Ezek a régiók közé tartozik Nyugat-India, Brazília déli, és az Egyesült Államok gov Virginia. 
   Ez azt jelenti, hogy a nyugat-indiai főkiszolgáló replikát hozhat létre Dél-Indiában. A dél-indiai főkiszolgáló azonban nem hozhat létre kópiát Nyugat-Indiában. Ez azért van, mert Nyugat-India másodlagos régió Dél-India, de Dél-India másodlagos régió nem Nyugat-India.

## <a name="create-a-replica"></a>Replika létrehozása

Ha a főkiszolgáló nem rendelkezik meglévő replikakiszolgálókkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikációra.

A replika-munkafolyamat létrehozásakor létrejön egy üres Azure-adatbázis a MariaDB-kiszolgálóhoz. Az új kiszolgáló tele van a főkiszolgálón lévő adatokkal. A létrehozási idő a főkiszolgálón lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt időtől függ. Az idő néhány perctől több óráig terjedhet.

> [!NOTE]
> Ha nincs beállítva tárolási riasztás a kiszolgálókon, javasoljuk, hogy tegye meg. A riasztás tájékoztatja, ha a kiszolgáló közeledik a tárolási korláthoz, ami hatással lesz a replikációra.

Ismerje meg, hogyan [hozhat létre olvasási replikát az Azure Portalon.](howto-read-replicas-portal.md)

## <a name="connect-to-a-replica"></a>Csatlakozás kópiához

Létrehozáskor a replika örökli a főkiszolgáló tűzfalszabályait. Ezt követően ezek a szabályok függetlenek a főkiszolgálótól.

A replika örökli a rendszergazdai fiókot a főkiszolgálótól. A főkiszolgáló összes felhasználói fiókja replikálása az olvasási replikákra történik. Az olvasási kópiához csak a főkiszolgálón elérhető felhasználói fiókok használatával lehet csatlakozni.

Csatlakozhat a replikához a gazdagépnevével és egy érvényes felhasználói fiókkal, ugyanúgy, mint egy normál Azure-adatbázis a MariaDB-kiszolgálón. A **myreplica** nevű kiszolgáló esetében a **myadmin**admin felhasználónévvel csatlakozhat a replikához a mysql CLI használatával:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

A kérdésben adja meg a felhasználói fiók jelszavát.

## <a name="monitor-replication"></a>Replikáció monitorozása

A MariaDB-hez az Azure Database for MariaDB biztosítja a **replikációs késés tmásodpercben** metrikus az Azure Monitorban. Ez a metrika csak replikák hoz érhető el.

Ezt a metrikát a `seconds_behind_master` MariaDB `SHOW SLAVE STATUS` parancsában elérhető metrika alapján számítjuk ki.

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

Az olvasási replika új Azure-adatbázisként jön létre a MariaDB-kiszolgálóhoz. Meglévő kiszolgáló nem alakítható át kópiává. Nem hozhat létre más olvasott kópiát.

### <a name="replica-configuration"></a>Replika konfigurációja

A kópia a főkiszolgálóval azonos kiszolgálókonfigurációhasználatával jön létre. A replika létrehozása után a fő kiszolgálótól függetlenül több beállítás is módosítható: számítási generálás, virtuális magok, tárolás, biztonsági mentés megőrzési időszaka és MariaDB motorverziója. A tarifacsomag is lehet változtatni egymástól függetlenül, kivéve, hogy vagy az alapszintű szintről.

> [!IMPORTANT]
> Mielőtt a főkiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

A tűzfalszabályok és a paraméterbeállítások a főkiszolgálótól a kópiához öröklődnek a kópia létrehozásakor. Ezt követően a replika szabályai függetlenek.

### <a name="stopped-replicas"></a>Leállított replikák

Ha leállítja a replikációt a főkiszolgáló és az olvasási kópia között, a leállított replika önálló kiszolgálóvá válik, amely elfogadja az olvasást és az írást is. Az önálló kiszolgáló nem alakítható újra kópiává.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő- és önálló kiszolgálók

A főkiszolgáló törlésekor a replikáció leáll az összes olvasási replikára. Ezek a replikák automatikusan önálló kiszolgálókká válnak, és olvasási és írási műveleteket is fogadnak. Maga a főkiszolgáló törlődik.

### <a name="user-accounts"></a>Felhasználói fiókok

A főkiszolgáló felhasználói replikálódnak az olvasási replikákra. Az olvasási kópiához csak a főkiszolgálón elérhető felhasználói fiókok használatával lehet csatlakozni.

### <a name="server-parameters"></a>Kiszolgálóparaméterek

Az adatszinkronizálás biztosítása és az esetleges adatvesztés vagy -sérülés elkerülése érdekében bizonyos kiszolgálóparaméterek zárolva vannak, hogy ne lehessen őket módosítani olvasási replikák használata során.

A következő kiszolgálóparaméterek a fő- és a replikakiszolgálókon is zárolva vannak:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

A [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) paraméter zárolva van a replikakiszolgálókon.

### <a name="other"></a>Egyéb

- A kópia kópiájának létrehozása nem támogatott.
- A memóriában lévő táblák miatt a kópiák nem lesznek szinkronban. Ez a MariaDB replikációs technológia korlátozása.
- Győződjön meg arról, hogy a főkiszolgálói táblák rendelkeznek elsődleges kulcsokkal. Az elsődleges kulcsok hiánya replikációs késést okozhat a főkiszolgáló és a replikák között.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure Portalhasználatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és REST API használatával](howto-read-replicas-cli.md)
