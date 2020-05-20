---
title: Felgyorsított adatbázis-helyreállítás
description: A Azure SQL Database egy új funkcióval rendelkezik, amely gyors és konzisztens adatbázis-helyreállítást, azonnali tranzakció-visszaállítást és agresszív naplózási levágást biztosít az önálló adatbázisokhoz és a készletezett adatbázisokhoz Azure SQL Database és Azure SQL Data Warehouse-adatbázisokban.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 05/19/2020
ms.openlocfilehash: f259d423f465d93031c3a72855fd7aac4e320573
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684965"
---
# <a name="accelerated-database-recovery"></a>Gyorsított adatbázis-helyreállítás

A **gyorsított adatbázis-helyreállítás (ADR)** egy SQL Database Engine-szolgáltatás, amely nagy mértékben javítja az adatbázisok rendelkezésre állását, különösen a hosszú ideig futó tranzakciók jelenlétében, az SQL-adatbázismotor helyreállítási folyamatának újratervezésével. Az ADR jelenleg Azure SQL Database egyetlen, rugalmas készlethez és felügyelt példányhoz, valamint Azure SQL Data Warehouse (jelenleg előzetes verzióban elérhető) adatbázisokhoz érhető el. Az ADR elsődleges előnyei a következők:

- **Gyors és konzisztens adatbázis-helyreállítás**

  Az ADR-sel a hosszan futó tranzakciók nem befolyásolják a teljes helyreállítási időt, így a gyors és konzisztens adatbázis-helyreállítást, a rendszer aktív tranzakcióinak számától vagy azok méretétől függetlenül.

- **Tranzakció azonnali visszaállítása**

  Az automatikus központi telepítési szabály használata esetén a tranzakció-visszaállítás azonnal megtörténik, függetlenül attól, hogy a tranzakció Mikor aktív, vagy hogy hány frissítést hajtottak végre.

- **Agresszív naplózási csonkítás**

  Az ADR-sel a tranzakciós naplót a rendszer agresszív módon csonkolja, még akkor is, ha aktív, hosszan futó tranzakciókat is használ, ami megakadályozza, hogy az IT-csoport ne legyen nagyobb a szabályozásban.

## <a name="standard-database-recovery-process"></a>Szabványos adatbázis-helyreállítási folyamat

Az adatbázis-helyreállítás SQL Server az [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) helyreállítási modellt követi, és három szakaszból áll, amelyek az alábbi ábrán láthatók, és részletesebben a diagramot követve jelennek meg.

![jelenlegi helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Elemzési fázis**

  A tranzakciós napló továbbítása a legutóbbi sikeres ellenőrzőpont elejétől (vagy a legrégebbi piszkos oldal LSN) kezdve egészen addig, amíg meg nem határozta az egyes tranzakciók állapotát SQL Server leállítva.

- **Ismétlési fázis**

  Az összes véglegesített művelet visszavonásával továbbíthatja a tranzakciós naplót a legrégebbi, nem véglegesített tranzakcióból egészen addig, amíg az összeomlás időpontjában az adatbázis bekerül az állapotba.

- **Visszavonás fázisa**

  Minden olyan tranzakció esetében, amely az összeomlás időpontjában aktív volt, a napló visszafelé halad, és visszaveszi a tranzakció által végrehajtott műveleteket.

Ezen a terven alapulva az az idő, ameddig az SQL-adatbázismotor nem várt újraindítást igényel, (nagyjából) az összeomlás időpontjában a rendszer leghosszabb aktív tranzakciójának méretével arányos. A helyreállításhoz az összes befejezetlen tranzakció visszaállítására van szükség. A szükséges időtartam a tranzakció által elvégzett munkához és az aktív időponthoz képest arányos. Ezért a SQL Server helyreállítási folyamat hosszú időt is igénybe vehet a hosszú ideig futó tranzakciók (például nagyméretű tömeges beszúrási műveletek vagy index-létrehozási műveletek nagyméretű táblán való létrehozása) esetén.

A nagy tranzakciók ezen terv alapján történő megszakítása/visszaállítása is hosszú időt vehet igénybe, mivel a fentiekben leírtak szerint ugyanazt a visszavonás-helyreállítási szakaszt használja.

Emellett az SQL-adatbázismotor nem tudja lerövidíteni a tranzakciós naplót, ha hosszú ideig futó tranzakció van, mert a helyreállítási és a visszaállítási folyamatokhoz szükség van a megfelelő napló-rekordokra. Az SQL-adatbázismotor ezen kialakításának eredményeképpen egyes ügyfelek felhasználták a problémát, hogy a tranzakciónapló mérete nagyon nagy mértékben növekszik, és nagy mennyiségű lemezterületet igényel.

## <a name="the-accelerated-database-recovery-process"></a>A gyorsított adatbázis-helyreállítási folyamat

Az ADR a fenti problémákra az SQL-adatbázismotor helyreállítási folyamatának teljes újratervezésével foglalkozik:

- Ha nem szeretné, hogy a rendszer a naplót a legrégebbi aktív tranzakció elejétől, akkor ne végezzen állandó időt/pillanatot. Az ADR használata esetén a tranzakciónapló csak az utolsó sikeres ellenőrzőponton (vagy a legrégebbi inkonzisztens oldal naplózási sorszáma (LSN)) lesz feldolgozva. Ennek eredményeképpen a hosszú ideig futó tranzakciók nem befolyásolják a helyreállítási időt.
- Csökkentse a szükséges tranzakciós napló területét, mert már nem kell feldolgoznia a naplót a teljes tranzakcióhoz. Ennek eredményeképpen a tranzakciós naplót agresszíven lehet lerövidíteni ellenőrzőpontok és biztonsági mentések esetén.

Magas szinten az ADR a fizikai adatbázis összes módosításának verziószámozásával gyors adatbázis-helyreállítást tesz lehetővé, és csak a logikai műveleteket végzi el, amelyek korlátozottak, és szinte azonnal visszavonhatók. Minden olyan tranzakció, amely egy összeomlás időpontjában aktív, meg lett szakítva, ezért az ilyen tranzakciók által generált verziók figyelmen kívül hagyhatók egyidejű felhasználói lekérdezések esetén.

Az ADR helyreállítási folyamata a jelenlegi helyreállítási folyamattal megegyező három fázissal rendelkezik. Az alábbi ábrán látható, hogy a fázisok hogyan működnek az ADR-sel, és részletesebben ismerteti a diagramot követő részleteket.

![ADR helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Elemzési fázis**

  A folyamat ugyanaz marad, mint korábban a üt újraépítése és a nem verziószámmal rendelkező műveletek naplófájljainak másolása.
  
- **Ismétlési** fázis

  Két fázisra bontva (P)
  - 1. fázis

      Újra a üt (a legrégebbi, nem véglegesített tranzakció egészen az utolsó ellenőrzőpontig). Az ismétlés egy gyors művelet, mivel csak néhány rekordot kell feldolgoznia a üt-ból.
      
  - 2. fázis

     Visszaállítás a tranzakciónaplóból az utolsó ellenőrzőponttól indul (a legrégebbi nem véglegesített tranzakciók helyett)
     
- **Visszavonás fázisa**

   A visszavonási fázis az ADR használatával szinte azonnal elvégezhető a üt paranccsal a nem verziószámmal ellátott műveletek és a megőrzött verzió-tároló (PVS) visszavonásához a logikai visszaállítással a sor szintű verzió-alapú visszavonás végrehajtásához.

## <a name="adr-recovery-components"></a>ADR helyreállítási összetevők

Az ADR négy fő összetevője a következők:

- **Megőrzött verzió tárolója (PVS)**

  A megőrzött verzió tárolója egy új SQL Database Engine-mechanizmus, amely a hagyományos verziójú tároló helyett maga az adatbázisában előállított sorok verzióinak megőrzését is megőrzi `tempdb` . A PVS lehetővé teszi az erőforrások elkülönítését, valamint javítja az olvasható formátumú másodlagos zónák rendelkezésre állását.

- **Logikai visszaállítása**

  A logikai visszaállítás az aszinkron folyamat, amely az összes verziószámmal ellátott művelet visszavonásának és visszavonásának elvégzéséhez felelős. A logikai visszavertség a következőket hajtja végre:

  - Nyomon követheti az összes megszakított tranzakciót, és láthatatlanként megjelölheti azokat más tranzakciókra. 
  - A visszaállítást a PVS használatával végezheti el az összes felhasználói tranzakcióhoz, nem pedig fizikailag ellenőrzi a tranzakciós naplót, és nem végezheti el a módosításokat.
  - A tranzakciók megszakítása után azonnal felszabadítja az összes zárolást. Mivel a megszakítás egyszerűen megjelöli a memória változásait, a folyamat nagyon hatékony, ezért a zárolásokat nem kell hosszú ideig tárolni.

- **Üt**

  a üt egy másodlagos memóriában tárolt log stream, amely a nem verziószámú műveletek (például a metaadat-gyorsítótár érvénytelenítése, a zárolási beszerzések stb.) rekordjait tárolja. A üt a következőket:

  - Kis mennyiségű és memóriában tárolt
  - Lemez megtartása az ellenőrzőpont-folyamat során szerializálva
  - Időszakosan csonkítva tranzakció véglegesítve
  - A csak a nem verziószámmal rendelkező műveletek feldolgozásával gyorsítja újra és vonja vissza a visszavonást.  
  - Engedélyezi az agresszív tranzakciónaplók csonkítása csak a szükséges naplóbejegyzések megőrzésével

- **Tisztító**

  A tisztító az aszinkron folyamat, amely időről időre felébred, és megtisztítja a szükségtelen lapokat.

## <a name="accelerated-database-recovery-patterns"></a>Gyorsított adatbázis-helyreállítási minták

A következő típusú munkaterhelések leginkább az ADR-ből származnak:

- Hosszú ideig futó tranzakciókkal rendelkező munkaterhelések.
- Olyan munkaterhelések, amelyek olyan eseteket észleltek, amikor az aktív tranzakciók jelentősen növekednek a tranzakciós naplóban.  
- Olyan munkaterhelések, amelyek hosszú ideig tartó adatbázis-elérhetőséget tapasztaltak SQL Server hosszú ideig tartó helyreállítás miatt (például váratlan SQL Server újraindítást vagy manuális tranzakció-visszaállítást).

