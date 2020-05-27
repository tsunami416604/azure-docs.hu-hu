---
title: Replikák olvasása – Azure Database for MySQL.
description: 'Ismerkedjen meg a Azure Database for MySQL olvasási replikákkal: régiók kiválasztása, replikák létrehozása, replikák csatlakoztatása, replikáció figyelése és replikáció leállítása.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/4/2020
ms.openlocfilehash: d9d600b4ac34e4608b7747bee0e0a704ad2ab3be
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846052"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvasási replikák az Azure Database for MySQL-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MySQL-kiszolgálóról egy csak olvasható kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MySQL-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Ha többet szeretne megtudni a MySQL-replikációs funkciókról és problémákról, tekintse meg a [MySQL-replikáció dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések a főkiszolgálóhoz irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitás terheit a főkiszolgálón. Ez a funkció nem a nagy írási igényű munkaterhelésekre irányul.

Az olvasási replika funkció MySQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A főkiszolgáló és a replika között mérhető késés lesz. A replikán lévő adatok végül konzisztensek maradnak a főkiszolgálón lévő adatokkal. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
Az olvasási replikát a főkiszolgálótól eltérő régióban is létrehozhatja. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A főkiszolgáló bármely [Azure Database for MySQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)elérhető.  A főkiszolgáló rendelkezhet replikával a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el a fő régiótól függően.

[![Replika-régiók olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiók bármelyikében létrehozhat egy olvasási replikát, függetlenül attól, hogy hol található a főkiszolgáló. A támogatott univerzális replika-régiók a következők:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Közép-Korea, Dél-Brazília, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója.

* Az USA 2. nyugati régiója átmenetileg nem érhető el a régiók közötti replika helyeként.

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a főkiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for MySQL az USA 2. nyugati régiójában, Közép-Franciaország, Észak-Európa és Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.
    
* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiai főkiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai főkiszolgálók azonban nem hozhatnak létre replikát Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.

## <a name="create-a-replica"></a>Replika létrehozása

Ha a főkiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a főkiszolgáló először újraindul a replikálás előkészítéséhez.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MySQL-kiszolgáló. Az új kiszolgáló a főkiszolgálón található adatokkal van feltöltve. A létrehozási idő a főkiszolgálón tárolt adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet. A replika-kiszolgáló mindig ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a főkiszolgáló. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) .

Minden replika engedélyezve van a tárterület [automatikus növelésére](concepts-pricing-tiers.md#storage-auto-grow). Az automatikus növekedés funkció lehetővé teszi, hogy a replika megőrizze az általa replikált adatmennyiséget, és megakadályozza a tárolási hibák miatti megszakítást a replikálásban.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

A létrehozáskor a replika örökli a főkiszolgáló tűzfalszabályok szabályait. Ezt követően ezek a szabályok függetlenek a főkiszolgálótól.

A replika örökli a rendszergazdai fiókot a főkiszolgálóról. A főkiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MySQL-kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin**, a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása

A Azure Database for MySQL a **replikáció késését a Azure monitor másodpercben** mért metrikája biztosítja. Ez a metrika csak replikák esetében érhető el.

Ezt a mérőszámot a `seconds_behind_master` MySQL parancsában elérhető metrika alapján számítjuk ki `SHOW SLAVE STATUS` .

Állítson be egy riasztást, amely tájékoztatja arról, ha a replikációs késés olyan értéket ér el, amely nem fogadható el a munkaterhelés számára.

## <a name="stop-replication"></a>Replikáció leállítása

Leállíthatja a replikációt egy főkiszolgáló és egy replika között. Miután leállította a replikálást egy főkiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem fog felzárkózni a főkiszolgálóval.

Ha úgy dönt, hogy leállítja a replikálást egy replikára, az elveszíti az előző főkiszolgálóra és más replikára mutató összes hivatkozást. A főkiszolgáló és a replikája között nincs automatikus feladatátvétel.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Az olvasási replikák jelenleg csak a általános célú és a memória optimalizált díjszabási szintjein érhetők el.

### <a name="master-server-restart"></a>Főkiszolgáló újraindítása

Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új Azure Database for MySQL-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replikát ugyanazzal a kiszolgáló-konfigurációval hozza létre a rendszer, mint a főkiszolgálót. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentés megőrzési ideje. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> Mielőtt a főkiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

A rendszer a replika létrehozásakor örökli a tűzfalszabályok és a paraméterek beállításait a főkiszolgálóról a replikára. Ezt követően a replika szabályai függetlenek.

### <a name="stopped-replicas"></a>Leállított replikák

Ha leállítja a replikálást egy főkiszolgáló és egy olvasási replika között, a leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő-és önálló kiszolgálók

Főkiszolgáló törlésekor a replikáció minden olvasási replikára leállt. Ezek a replikák automatikusan önálló kiszolgálókra válnak, és elfogadhatják az olvasási és írási műveleteket is. Maga a főkiszolgáló törölve lett.

### <a name="user-accounts"></a>Felhasználói fiókok

A főkiszolgálón lévő felhasználókat a rendszer replikálja az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

### <a name="server-parameters"></a>Kiszolgálóparaméterek

Az adatszinkronizálás biztosítása és az esetleges adatvesztés vagy -sérülés elkerülése érdekében bizonyos kiszolgálóparaméterek zárolva vannak, hogy ne lehessen őket módosítani olvasási replikák használata során.

A következő kiszolgálói paraméterek a fő-és a replika-kiszolgálókon is zárolva vannak:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

A [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter zárolva van a replika-kiszolgálókon. 

Ha frissíteni szeretné a fenti paraméterek egyikét a főkiszolgálón, törölje a replika-kiszolgálókat, frissítse a paraméter értékét a főkiszolgálón, és hozza létre újból a replikákat.

### <a name="other"></a>Egyéb

- A globális tranzakciós azonosítók (GTID-EK) nem támogatottak.
- Replika replikájának létrehozása nem támogatott.
- A memóriában tárolt táblázatok miatt a replikák nem lesznek szinkronban. Ez a MySQL-replikációs technológia korlátozása. További információt a [MySQL dokumentációjában talál](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Győződjön meg arról, hogy a fő kiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a fő és a replikák között.
- A MySQL- [dokumentációban](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) található MySQL-replikálási korlátozások teljes listájának áttekintése

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és a REST API használatával](howto-read-replicas-cli.md)
