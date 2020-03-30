---
title: Felgyorsított adatbázis-helyreállítás
description: Az Azure SQL Database egy új funkcióval rendelkezik, amely gyors és konzisztens adatbázis-helyreállítást, pillanatnyi tranzakció-visszaállítást, valamint agresszív naplócsonkolást biztosít az Azure SQL Database egyes adatbázisaihoz és készletezetlen adatbázisaihoz, valamint az Azure SQL Data-ban lévő adatbázisokhoz Raktár.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238663"
---
# <a name="accelerated-database-recovery"></a>Gyorsított adatbázis-helyreállítás

**Az Accelerated Database Recovery (ADR)** egy SQL adatbázis-motorszolgáltatás, amely nagymértékben javítja az adatbázis rendelkezésre állását, különösen hosszú ideig futó tranzakciók esetén, az SQL adatbázismotor helyreállítási folyamatának újratervezésével. Az ADR jelenleg elérhető az Azure SQL Database egy-, rugalmas készlet és felügyelt példány, valamint adatbázisok az Azure SQL Data Warehouse (jelenleg előzetes verzióban). Az ADR elsődleges előnyei a következők:

- **Gyors és konzisztens adatbázis-helyreállítás**

  Az ADR esetében a hosszú ideig futó tranzakciók nem befolyásolják a teljes helyreállítási időt, ami lehetővé teszi a gyors és konzisztens adatbázis-helyreállítást, függetlenül a rendszerben lévő aktív tranzakciók számától vagy méretüktől.

- **Azonnali tranzakció-visszaállítás**

  Az ADR-rel a tranzakció visszaállítása azonnali, függetlenül attól, hogy a tranzakció mikor volt aktív, vagy a végrehajtott frissítések száma.

- **Agresszív naplócsonkolás**

  Az ADR-rel a tranzakciós napló agresszíven csonkolódik, még aktív, hosszú ideig futó tranzakciók jelenlétében is, ami megakadályozza, hogy ellenőrizhetetlenné váltson.

## <a name="the-current-database-recovery-process"></a>A jelenlegi adatbázis-helyreállítási folyamat

Az SQL Server adatbázis-helyreállítása az [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) helyreállítási modelljét követi, és három fázisból áll, amelyeket az alábbi ábra szemléltet, és részletesebben ismertet az ábrát követően.

![jelenlegi helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Elemzési fázis**

  A tranzakciónapló továbbítása az utolsó sikeres ellenőrzőpont elejétől (vagy a legrégebbi piszkos lap LSN-től) a végéig, az egyes tranzakciók állapotának meghatározásához az SQL Server leállításakor.

- **Fázis ismétlése**

  Továbbítsa a tranzakciós napló vizsgálatát a legrégebbi nem véglegesített tranzakcióról a végéig, hogy az adatbázis az összeomlás idején volt az összes véglegesített művelet újbóli létrehozásával.

- **Fázis visszavonása**

  Minden olyan tranzakció esetében, amely az összeomlás időpontjától aktív volt, a naplót visszafelé járja be, és visszaírja a tranzakció által végrehajtott műveleteket.

Ezen a terv alapján az SQL-adatbázis-motor nak a váratlan újraindításból való helyreállításhoz szükséges ideje (nagyjából) arányos a rendszer leghosszabb aktív tranzakciójának méretével az összeomlás idején. A helyreállítás hoz az összes befejezetlen tranzakció visszaállításához. A szükséges idő arányos a tranzakció által végzett munkával és az aktív munkával. Ezért az SQL Server helyreállítási folyamat hosszú ideig tarthat a hosszú ideig futó tranzakciók (például nagy tömeges beszúrási műveletek vagy index build műveletek egy nagy tábla) jelenlétében.

Emellett egy nagy tranzakció visszavonása/visszaállítása a terv alapján is hosszú időt vehet igénybe, mivel ugyanazt a visszavonási helyreállítási fázist használja, mint a fent leírt.

Ezenkívül az SQL-adatbázismotor nem tudja csonkolni a tranzakciós naplót, ha hosszú ideig futó tranzakciók vannak, mert a megfelelő naplórekordok szükségesek a helyreállítási és visszaállítási folyamatokhoz. Ennek eredményeként a design az SQL adatbázis-motor, egyes ügyfelek használják, hogy szembenézzen a problémával, hogy a tranzakciós napló mérete nő nagyon nagy, és fogyaszt hatalmas mennyiségű meghajtó helyet.

## <a name="the-accelerated-database-recovery-process"></a>A gyorsított adatbázis-helyreállítási folyamat

Az ADR a fenti problémákat úgy oldja meg, hogy teljesen újratervezi az SQL adatbázismotor helyreállítási folyamatát a következőkre:

- Legyen állandó idő / azonnali elkerülve kelljen beszkelni a naplót / elejére a legrégebbi aktív tranzakció. Az ADR-rel a tranzakciónapló feldolgozása csak az utolsó sikeres ellenőrzőpontról (vagy a legrégebbi piszkos oldal naplósorszámról (LSN)) történik. Ennek eredményeképpen a hosszú ideig futó tranzakciók nem befolyásolják a helyreállítási időt.
- Minimalizálja a szükséges tranzakciónapló-területet, mivel már nincs szükség a teljes tranzakció naplójának feldolgozására. Ennek eredményeképpen a tranzakciós napló agresszíven csonkolható ellenőrzőpontok és biztonsági mentések esetén.

Magas szinten az ADR gyors adatbázis-helyreállítást ér el az összes fizikai adatbázis-módosítás verziószámozásával, és csak a logikai műveletek visszavonásával, amelyek korlátozottak és szinte azonnal visszacsinálhatók. Minden olyan tranzakció, amely az összeomlás időpontjában aktív volt, megszakítottként van megjelölve, ezért az ezek a tranzakciók által létrehozott verziók at egyidejű felhasználói lekérdezések figyelmen kívül hagyhatják.

Az ADR-helyreállítási folyamat nak ugyanaz a három fázisa van, mint a jelenlegi helyreállítási folyamatnak. Az alábbi ábrán látható, hogy ezek a fázisok hogyan működnek az ADR-rel, és részletesebben ismertetjük.

![ADR helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Elemzési fázis**

  A folyamat ugyanaz marad, mint korábban a nem verziózott műveletek sLog és a naplórekordok másolása hozzáadásával.
  
- **Fázis ismétlése**

  Két fázisra bontva (P)
  - 1. fázis

      Ismétlés a sLog -tól (legrégebbi nem véglegesített tranzakció az utolsó ellenőrzőpontig). Az ismétlés gyors művelet, mivel csak néhány rekordot kell feldolgoznia a sLogból.
      
  - 2. fázis

     Ismétlés a tranzakciónaplóból az utolsó ellenőrzőponttól indul (a legrégebbi nem véglegesített tranzakció helyett)
     
- **Fázis visszavonása**

   Az ADR-rel rendelkező Visszavonás fázis szinte azonnal befejeződik az sLog használatával a nem verziózott műveletek visszavonásához és a megőrzött verziótároló (PVS) logikai visszaállítással a sorszintű verzióalapú visszavonás végrehajtásához.

## <a name="adr-recovery-components"></a>ADR helyreállítási összetevők

Az ADR négy fő összetevője a következő:

- **A megőrzött verziótároló (PVS)**

  A megőrzött verziótároló egy új SQL-adatbázis-motormechanizmus, amely a hagyományos `tempdb` verziótároló helyett az adatbázisban létrehozott sorverziók megőrzésére alkalmas. A PVS lehetővé teszi az erőforrások elkülönítését, valamint javítja az olvasható másodlagos adatok elérhetőségét.

- **Logikai visszaállítás**

  A logikai visszaállítás a sorszintű verzióalapú visszavonás végrehajtásáért felelős aszinkron folyamat, amely azonnali tranzakció-visszaállítást és visszavonást biztosít az összes verziószámozott művelethez. A logikai visszaállítás a következő kérhető:

  - Az összes megszakított tranzakció nyomon követése és más tranzakciók számára láthatatlanná tétele. 
  - Visszaállítás végrehajtása PVS használatával az összes felhasználói tranzakcióhoz, ahelyett, hogy fizikailag beszkéselné a tranzakciónaplót, és egyenként visszavonhatja a módosításokat.
  - Az összes zárolás feloldása a tranzakció megszakítása után azonnal. Mivel a megszakítás magában foglalja a memória változásainak egyszerű jelölését, a folyamat nagyon hatékony, ezért a zárakat nem kell hosszú ideig tartani.

- **Üt**

  Az sLog egy másodlagos memóriabeli naplóadatfolyam, amely nem verziózott műveletek naplórekordjait tárolja (például a metaadat-gyorsítótár érvénytelenítése, zárolás-beszerzések és így tovább). Az sLog a következő:

  - Kis hangerő és memória
  - Az ellenőrzőpont-folyamat során szerializált lemezen maradt
  - Tranzakcióvéglegesítésként rendszeresen csonkolva
  - Felgyorsítja a ismétlést és a visszavonást, ha csak a nem verziózott műveleteket dolgozza fel  
  - Agresszív tranzakciónapló-csonkolás engedélyezése csak a szükséges naplórekordok megőrzésével

- **Tisztító**

  A tisztító az aszinkron folyamat, amely rendszeresen felébred, és tisztítja a nem szükséges oldalverziókat.

## <a name="accelerated-database-recovery-patterns"></a>Gyorsított adatbázis-helyreállítási minták

Az ADR a következő típusú számítási feladatokból származik a legjobban:

- Hosszú ideig futó tranzakciókkal rendelkező munkaterhelések.
- Számítási feladatok, amelyek olyan eseteket láttak, ahol az aktív tranzakciók miatt a tranzakciós napló jelentősen növekedett.  
- Olyan számítási feladatok, amelyek az SQL Server hosszú ideig tartó helyreállítása (például váratlan SQL Server-újraindítás vagy manuális tranzakció-visszaállítás) miatt az adatbázis hosszú ideig nem volt elérhető.

