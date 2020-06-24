---
title: Replikák olvasása – Azure Database for MariaDB
description: 'Ismerkedjen meg a Azure Database for MariaDB olvasási replikákkal: régiók kiválasztása, replikák létrehozása, replikák csatlakoztatása, replikáció figyelése és replikáció leállítása.'
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: f7606e2cbe6655801903de62ff9080eba8a0dd53
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708025"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Olvasási replikák az Azure Database for MariaDB-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MariaDB-kiszolgálóról egy csak olvasható kiszolgálóra. A főkiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikákat a rendszer aszinkron módon frissíti a MariaDB motor bináris naplójának (BinLog) fájljának pozíció-alapú replikációs technológiájának használatával globális tranzakciós AZONOSÍTÓval (GTID). A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MariaDB-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

A GTID replikálásával kapcsolatos további tudnivalókért tekintse meg a [MariaDB-replikáció dokumentációját](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések a főkiszolgálóhoz irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitás terheit a főkiszolgálón. Ez a funkció nem a nagy írási igényű munkaterhelésekre irányul.

Az olvasási replika funkció aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A főkiszolgáló és a replika között mérhető késés lesz. A replikán lévő adatok végül konzisztensek maradnak a főkiszolgálón lévő adatokkal. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
Az olvasási replikát a főkiszolgálótól eltérő régióban is létrehozhatja. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A főkiszolgáló bármely [Azure Database for MariaDB régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)elérhető.  A főkiszolgáló rendelkezhet replikával a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el a fő régiótól függően.

[![Replika-régiók olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiók bármelyikében létrehozhat egy olvasási replikát, függetlenül attól, hogy hol található a főkiszolgáló. A támogatott univerzális replika-régiók a következők:

Kelet-Ausztrália, Délkelet-Ausztrália, USA középső régiója, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Közép-Korea, Dél-Brazília, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa, USA nyugati régiója.

* Az USA 2. nyugati régiója átmenetileg nem érhető el a régiók közötti replika helyeként.

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a főkiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for MariaDB az USA 2. nyugati régiójában, Közép-Franciaország, Észak-Európa és Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.
    
* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiai főkiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai főkiszolgálók azonban nem hozhatnak létre replikát Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.

## <a name="create-a-replica"></a>Replika létrehozása

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a főkiszolgáló a fenti díjszabási szintek egyikében van.

Ha a főkiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a főkiszolgáló először újraindul a replikálás előkészítéséhez.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MariaDB-kiszolgáló. Az új kiszolgáló a főkiszolgálón található adatokkal van feltöltve. A létrehozási idő a főkiszolgálón tárolt adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet.

> [!NOTE]
> Ha nincs beállítva tárolási riasztás a kiszolgálókon, javasoljuk, hogy tegye meg ezt a szolgáltatást. A riasztás akkor értesíti, ha egy kiszolgáló eléri a tárolási korlátot, ami hatással lesz a replikálásra.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

A létrehozáskor a replika örökli a főkiszolgáló tűzfalszabályok szabályait. Ezt követően ezek a szabályok függetlenek a főkiszolgálótól.

A replika örökli a rendszergazdai fiókot a főkiszolgálóról. A főkiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a főkiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MariaDB-kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin**, a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása

A Azure Database for MariaDB a **replikáció késését a Azure monitor másodpercben** mért metrikája biztosítja. Ez a metrika csak replikák esetében érhető el.

Ezt a metrikát a `seconds_behind_master` MariaDB parancsában elérhető metrika alapján számítjuk ki `SHOW SLAVE STATUS` .

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

> [!NOTE]
> A replika-kiszolgáló futtatásának díja azon a régión alapul, ahol a replika-kiszolgáló fut.

### <a name="master-server-restart"></a>Főkiszolgáló újraindítása

Ha olyan mesteralakzathoz hoz létre replikát, amely nem rendelkezik meglévő replikákkal, a főkiszolgáló először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új Azure Database for MariaDB-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replikát ugyanazzal a kiszolgáló-konfigurációval hozza létre a rendszer, mint a főkiszolgálót. A replika létrehozása után több beállítás is módosítható a főkiszolgálótól függetlenül: számítási generáció, virtuális mag, tárterület, biztonsági másolat megőrzési ideje és MariaDB motor verziója. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

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
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

A [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) paraméter zárolva van a replika-kiszolgálókon.

Ha frissíteni szeretné a fenti paraméterek egyikét a főkiszolgálón, törölje a replika-kiszolgálókat, frissítse a paraméter értékét a főkiszolgálón, és hozza létre újból a replikákat.

### <a name="other"></a>Egyéb

- Replika replikájának létrehozása nem támogatott.
- A memóriában tárolt táblázatok miatt a replikák nem lesznek szinkronban. Ez a MariaDB replikációs technológia korlátozása.
- Győződjön meg arról, hogy a fő kiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a fő és a replikák között.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és a REST API használatával](howto-read-replicas-cli.md)
