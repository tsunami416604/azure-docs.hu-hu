---
title: Azure Database for MySQL replikáinak olvasása.
description: Ez a cikk a Azure Database for MySQL tartozó olvasási replikákat ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e0309ac70d39272bf045e49ca7ddecd258cc1f36
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992047"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvasási replikák az Azure Database for MySQL-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MySQL-kiszolgálóról egy csak olvasható kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

> [!IMPORTANT]
> Létrehozhat egy olvasási replikát a főkiszolgálóval megegyező régióban, vagy bármely más, az Ön által választott Azure-régióban is. A régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MySQL-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Ha többet szeretne megtudni a MySQL-replikációs funkciókról és problémákról, tekintse meg a [MySQL-replikáció dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések a főkiszolgálóhoz irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitás terheit a főkiszolgálón. Ez a funkció nem a nagy írási igényű munkaterhelésekre irányul.

Az olvasási replika funkció MySQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A főkiszolgáló és a replika között mérhető késés lesz. A replikán lévő adatok végül konzisztensek maradnak a főkiszolgálón lévő adatokkal. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
Az olvasási replikát a főkiszolgálótól eltérő régióban is létrehozhatja. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

> [!IMPORTANT]
> A régiók közötti replikáció jelenleg nyilvános előzetes verzióban érhető el.

A főkiszolgáló bármely [Azure Database for MySQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)elérhető.  A főkiszolgáló rendelkezhet replikával a párosított régiójában vagy az univerzális replika régiókban.

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiókban bármikor létrehozhat egy olvasási replikát, függetlenül attól, hogy hol található a főkiszolgáló. Ezek az univerzális replika-régiók:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Dél-Korea, Dél-Korea, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója 2.


### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a főkiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról](../best-practices-availability-paired-regions.md)szóló cikkből.

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for MySQL az USA 2. nyugati régiójában, Közép-Franciaországban, Észak-Egyesült Arab Emírségekben és Közép-Németországban érhető el. A párosított régiói azonban nem érhetők el.
    
* UNI-irányú párok: Egyes Azure-régiók csak egy irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiai főkiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai főkiszolgálók azonban nem hozhatnak létre replikát Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.


## <a name="create-a-replica"></a>Replika létrehozása

Ha a főkiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a főkiszolgáló először újraindul a replikálás előkészítéséhez.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MySQL-kiszolgáló. Az új kiszolgáló a főkiszolgálón található adatokkal van feltöltve. A létrehozási idő a főkiszolgálón tárolt adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet.

Minden replika engedélyezve van a tárterület [automatikus növelésére](concepts-pricing-tiers.md#storage-auto-grow). Az automatikus növekedés funkció lehetővé teszi, hogy a replika megőrizze az általa replikált adatmennyiséget, és megakadályozza, hogy a replikálás megszakítása a tárolási hibák miatt meghiúsult.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

Replika létrehozásakor nem örökli a főkiszolgáló tűzfalszabályok vagy VNet szolgáltatási végpontját. Ezeket a szabályokat a replika egymástól függetlenül kell beállítani.

A replika örökli a rendszergazdai fiókot a főkiszolgálóról. A főkiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MySQL-kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin**, a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció figyelése

A Azure Database for MySQL a **replikáció késését** a Azure monitor másodpercben mért metrikája biztosítja. Ez a metrika csak replikák esetében érhető el.

Ezt a mérőszámot a `seconds_behind_master` `SHOW SLAVE STATUS` MySQL parancsában elérhető metrika alapján számítjuk ki.

Állítson be egy riasztást, amely tájékoztatja arról, ha a replikációs késés olyan értéket ér el, amely nem fogadható el a munkaterhelés számára.

## <a name="stop-replication"></a>Replikáció leállítása

Leállíthatja a replikációt egy főkiszolgáló és egy replika között. Miután leállította a replikálást egy főkiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem fog felzárkózni a főkiszolgálóval.

Ha úgy dönt, hogy leállítja a replikálást egy replikára, az elveszíti az előző főkiszolgálóra és más replikára mutató összes hivatkozást. A főkiszolgáló és a replikája között nincs automatikus feladatátvétel.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Megfontolások és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Az olvasási replikák jelenleg csak a általános célú és a memória optimalizált díjszabási szintjein érhetők el.

### <a name="master-server-restart"></a>Főkiszolgáló újraindítása

Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új Azure Database for MySQL-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replikát ugyanazzal a kiszolgáló-konfigurációval hozza létre a rendszer, mint a főkiszolgálót. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület, biztonsági mentés megőrzési ideje és MySQL-motor verziója. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> Mielőtt a főkiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

### <a name="stopped-replicas"></a>Leállított replikák

Ha leállítja a replikálást egy főkiszolgáló és egy olvasási replika között, a leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-master-and-standalone-servers"></a>Törölt fő-és önálló kiszolgálók

Főkiszolgáló törlésekor a replikáció minden olvasási replikára leállt. Ezek a replikák önálló kiszolgálókra válnak. Maga a főkiszolgáló törölve lett.

### <a name="user-accounts"></a>Felhasználói fiókok

A főkiszolgálón lévő felhasználókat a rendszer replikálja az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

### <a name="server-parameters"></a>Kiszolgálói paraméterek

Ha meg szeretné akadályozni, hogy az adatok ne legyenek szinkronban, és elkerülhető legyen a lehetséges adatvesztés vagy-sérülés, egyes kiszolgálói paraméterek nem frissülnek az olvasási replikák használatakor.

A következő kiszolgálói paraméterek a fő-és a replika-kiszolgálókon is zárolva vannak:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

A [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter zárolva van a replika-kiszolgálókon. 

### <a name="other"></a>Egyéb

- A globális tranzakciós azonosítók (GTID-EK) nem támogatottak.
- Replika replikájának létrehozása nem támogatott.
- A memóriában tárolt táblázatok miatt a replikák nem lesznek szinkronban. Ez a MySQL-replikációs technológia korlátozása. További információt a [MySQL dokumentációjában talál](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Győződjön meg arról, hogy a fő kiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a fő és a replikák között.
- A MySQL-dokumentációban található MySQL-replikálási [](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) korlátozások teljes listájának áttekintése

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI használatával](howto-read-replicas-cli.md)
