---
title: A gyorsított adatbázis-helyreállítás – Azure SQL Database |} A Microsoft Docs
description: Az Azure SQL Database új funkció gyors és következetes adatbázis helyreállítását, azonnali tranzakció visszaállítása és agresszív rendszernapló csonkolása biztosít az önálló adatbázisok és a készletezett adatbázisok Azure SQL Database-ben, és az adatbázisok az Azure SQL Data rendelkezik Az adatraktár.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6d962a40fe0e1a7658c0d5ac30c7fd04bfb7fb0f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475448"
---
# <a name="accelerated-database-recovery-preview"></a>Gyorsított adatbázis-helyreállítás (előzetes verzió)

**A gyorsított adatbázis-helyreállítás (ADR)** egy új SQL database engine szolgáltatás, amely jelentősen csökkenti az adatbázis-elérhetőségi, különösen hosszú folytonosságát fut-e, tranzakciók, az SQL database engine helyreállítást újratervezése. Automatikus központi telepítési szabály már érhető el az önálló adatbázisok és a készletezett adatbázisok Azure SQL Database-ben, és az Azure SQL Data Warehouse-adatbázisok. Az automatikus központi telepítési szabály elsődleges előnyei a következők:

- **Gyors és következetes adatbázis-helyreállítás**

  Az automatikus központi telepítési szabály a hosszú ideig futó tranzakció nem érinti a teljes helyreállítási ideje, attól függetlenül, az aktív tranzakciók száma a gyors és következetes az adatbázis helyreállításának engedélyezése a rendszer vagy azok méretét.

- **Azonnali tranzakció visszaállítása**

  Az automatikus központi telepítési szabály a tranzakció visszaállítása nem azonnali, attól függetlenül, az idő, amelynek a tranzakció már aktív, vagy a frissítések száma, amelyek hajtott végre.

- **Agresszív rendszernapló csonkolása**

  Az automatikus központi telepítési szabály a tranzakciós napló agresszív csonkolva van, még akkor is folytonosságát aktív hosszú ideig futó tranzakció, amely megakadályozza az egyre növekvő nem ellenőrizhető.

## <a name="the-current-database-recovery-process"></a>Az aktuális adatbázis helyreállítási folyamat

Az SQL Server adatbázis-helyreállítás követi a [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) helyreállítási modell és a következő ábra szemlélteti, és részletesen a diagram a következő három szakaszból áll.

![aktuális helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Elemzési fázis**

  Vizsgálat a tranzakciós napló továbbíthatja az utolsó sikeres ellenőrzőpont (vagy a legrégebbi lap: LSN) lejáratáig, minden egyes tranzakció jelenleg az SQL Server leállítja az állapot meghatározásához kezdetétől fogva.

- **Fázis visszaállítása**

  Vizsgálat a tranzakciós napló továbbításához lejáratáig, ahhoz, hogy az állapot szerint minden művelet megismétlése az összeomlás időpontjában volt az adatbázis legrégebbi nem véglegesített tranzakció.

- **Fázis visszavonása**

  Minden egyes az összeomlás időpontjában aktív tranzakció esetében is járja a napló visszamenőleges, a tranzakció által végrehajtott műveletek visszavonása.

Ez a kialakítás alapján, az SQL-adatbázismotor váratlan újraindítást helyreállítása szükséges idő a leghosszabb aktív tranzakció méretével arányos (körülbelül) a rendszer az összeomlás időpontjában. Visszaállítás az összes befejezetlen tranzakció van szüksége. Szükséges idő hossza arányos munkát végzett el a tranzakciót, és az idő volt aktív. Ezért az SQL Server helyreállítást is igénybe vehet egy hosszú ideig hosszú ideig futó tranzakció jelenléte (mint például a nagyméretű tömeges beszúrás műveletek vagy index build kapcsolatos művelet-végrehajtási egy nagy méretű tábla).

Ezenkívül megszakítása vagy egy nagy tranzakció visszaállítása alapján ez a kialakítás is is időbe telik használja az ugyanazon a visszavonás helyreállítási fázist a fent leírtak szerint.

Emellett az SQL-adatbázismotor nem tudja csonkolni a tranzakciós napló Ha hosszú tranzakciók fut, mert a megfelelő rekordok naplózása szükséges ahhoz, hogy a helyreállítási és a folyamatokat. Ezzel a kialakítással az SQL-adatbázismotor eredményeként egyes ügyfeleknek szembe a problémát, hogy a tranzakciós napló méretétől nagyon nagy növekedésével, illetve a hatalmas mennyiségű naplózási helyet használ fel.

## <a name="the-accelerated-database-recovery-process"></a>A gyorsított adatbázis helyreállítási folyamat

Automatikus központi telepítési szabály által teljesen újratervezése az SQL database motor helyreállítást, a fenti problémák címek:

- Győződjön meg arról, hogy állandó idő/azonnali lehetőleg ne kelljen vizsgálata a log-ből vagy a legrégebbi aktív tranzakció kezdete. Az automatikus központi telepítési szabály a tranzakciós napló csak feldolgozása az utolsó sikeres ellenőrzőpont (vagy a legrégebbi inkonzisztencia lap Log feladatütemezési Number(LSN). Ennek eredményeképpen a helyreállítási idő nem befolyásolják hosszú ideig futó tranzakció.
- Minimalizálja a szükséges területét, mivel már nem szükséges a napló a teljes tranzakció feldolgozását. Ennek eredményeképpen a tranzakciós napló agresszív, ellenőrzőpontok levágása, és készüljön biztonsági másolat.

Magas szinten automatikus központi telepítési szabály éri el a gyors helyreállítás versioning által minden fizikai adatbázis módosítását, és csak visszavonása logikai műveletek, amelyek korlátozott, és szinte azonnal lehet visszavonni. Megszakított bármely az összeomlás időpontjában aktív tranzakció jelöli meg, és ezért ezek a tranzakciók által generált az összes olyan verzió figyelmen kívül hagyható az egyidejű felhasználói lekérdezések.

Az automatikus központi telepítési szabály helyreállítást ugyanazon mindhárom fázist, az aktuális helyreállítási folyamat rendelkezik. Hogyan ezeket a fázisokat működik az automatikus központi telepítési szabály a következő ábra mutatja be, és a diagram következő részletesen.

![Automatikus központi telepítési szabály a helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Elemzési fázis**

  A folyamat továbbra is ugyanaz, mint a mai sLog helyreállítás, valamint másolására az OPS verzióval ellátott rekordok naplózása igény szerinti hozzáadásával.
- **Mégis** fázis

  Hibás két fázisra (P)
  - 1. fázis

      Ismételje meg a sLog (legrégebbi nem véglegesített tranzakció utolsó ellenőrzési pont legfeljebb). Ismétlés egy gyors művelet, csak a sLog néhány rekordjainak feldolgozása igényeinek megfelelően.
  - 2. fázis

     Ismételje meg a tranzakciós napló kezdődik (helyett a legrégebbi nem véglegesített tranzakció) az utolsó ellenőrzőponttól
- **Fázis visszavonása**

   A visszavonási fázis az automatikus központi telepítési szabály szinte azonnal fájlkivonatát műveletek visszavonásához sLog használatával befejeződött, és a megőrzött verzió Store (PVS) rendelkező logikai visszaállítás elvégzéséhez szintű verzió-alapú visszavonási sor.

## <a name="adr-recovery-components"></a>Automatikus központi telepítési szabály recovery-összetevők

Automatikus központi telepítési szabály négy fő összetevői a következők:

- **Megőrzött verzió Store (PVS)**

  Az állandó tároló egy új SQL database motor mechanizmust megőrzése a sor verziók maga az adatbázis helyett a hagyományos létrehozott `tempdb` verziótároló. PVS lehetővé teszi az erőforrások elkülönítését, valamint javítja a olvasható másodlagos példánnyal rendelkezésre állását.

- **Logikai visszaállítása**

  A logikai visszaállítás felelős az aszinkron folyamat visszavonás – azonnali tranzakció visszaállítása és a visszavonás megadása minden rendszerverzióval ellátott műveletek végrehajtása a sor-szintű verziója alapján.

  - Nyomon követi az összes megszakított tranzakciók száma
  - Hajt végre PVS használatával az összes felhasználói tranzakció visszaállítása
  - Kiadások összes zárolás után azonnal tranzakció megszakítása

- **sLog**

  sLog egy másodlagos memórián belüli naplóstreamet, hogy a tárolók fájlkivonatát műveletekhez (például a metaadat-gyorsítótárának érvénytelenítésére, a zárolás beszerzések, és így tovább) rekordok naplózására. A sLog van:

  - Kevés, és a memóriában
  - Megőrzi a lemezen által éppen szerializált során az ellenőrzőpont-folyamat
  - Rendszeres időközönként csonkolva, a tranzakció véglegesítése
  - Gyorsítja visszaállítása, és csak a rendszerverzióval ellátott műveleteket feldolgozásával visszavonása  
  - Lehetővé teszi, hogy agresszív tranzakció rendszernapló csonkolása által csak a szükséges naplórekordok megőrzése

- **Tisztább**

  A tisztítási vizsgálat az aszinkron folyamat, amely rendszeres időközönként felébred, és törli a lap verziók nem szükséges.

## <a name="who-should-consider-accelerated-database-recovery"></a>Kinek érdemes megfontolnia a gyorsított adatbázis-helyreállítás

A következő típusú ügyfelek fontolja meg automatikus központi telepítési szabály engedélyezése:

- Számítási feladatokkal és rendelkező ügyfelek számára hosszú ideig futó tranzakció.
- Ügyfeleink számára, akik látott esetekben, ahol aktív tranzakciók okozzák a tranzakciós napló jelentősen megnő.  
- Olyan ügyfelek, amelyek hosszú ideig tartó adatbázis elérhetetlensége miatt (mint például a nem várt SQL Server újraindítása vagy manuális tranzakció visszaállítása) helyreállítási hosszú ideig futó SQL Server lépett fel.

## <a name="to-enable-adr-during-this-preview-period"></a>Automatikus központi telepítési szabály engedélyezése az előzetes verzió időszaka során

E-mail küldése a szolgáltatás az előzetes verzió ideje alatt [ adr@microsoft.com ](mailto:adr@microsoft.com) további, és próbálja ki a gyorsított adatbázis helyreállítási (automatikus központi telepítési szabály). Az e-mailben az SQL Database-kiszolgáló neve (az önálló adatbázisok és a készletezett adatbázisok SQL Database-ben és az adatbázisok az Azure Data Warehouse) is. Mivel ez egy előzetes verziójú funkció, a tesztelési kiszolgáló lehet egy nem éles üzemi kiszolgáló.
