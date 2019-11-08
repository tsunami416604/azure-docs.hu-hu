---
title: Gyorsított adatbázis-helyreállítás – Azure SQL Database
description: A Azure SQL Database egy új funkcióval rendelkezik, amely gyors és konzisztens adatbázis-helyreállítást, azonnali tranzakció-visszaállítást és agresszív naplózási lecsonkítt biztosít az önálló adatbázisokhoz és a készletezett adatbázisokhoz Azure SQL Database és az Azure SQL-alapú adatbázisokban Adatraktár.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: cff481c7c2e09da1dc8c8e2f971d9adb164d54da
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796120"
---
# <a name="accelerated-database-recovery"></a>Gyorsított adatbázis-helyreállítás

A **gyorsított adatbázis-helyreállítás (ADR)** egy új SQL Database motor-szolgáltatás, amely nagy mértékben javítja az adatbázisok rendelkezésre állását, különösen a hosszú ideig futó tranzakciók jelenlétében, az SQL-adatbázismotor helyreállítási folyamatának újratervezésével. Az ADR jelenleg egyazon adatbázisok esetében érhető el, és a Azure SQL Databaseban található készletezett adatbázisok, valamint Azure SQL Data Warehouse (jelenleg nyilvános előzetes verzióban elérhető) adatbázisok. Az ADR elsődleges előnyei a következők:

- **Gyors és konzisztens adatbázis-helyreállítás**

  Az ADR-sel a hosszan futó tranzakciók nem befolyásolják a teljes helyreállítási időt, így a gyors és konzisztens adatbázis-helyreállítást, a rendszer aktív tranzakcióinak számától vagy azok méretétől függetlenül.

- **Tranzakció azonnali visszaállítása**

  Az automatikus központi telepítési szabály használata esetén a tranzakció-visszaállítás azonnal megtörténik, függetlenül attól, hogy a tranzakció Mikor aktív, vagy hogy hány frissítést hajtottak végre.

- **Agresszív naplózási csonkítás**

  Az automatikus központi telepítési művelettel a tranzakciós naplót a rendszer agresszív módon csonkítja, még az aktív, hosszan futó tranzakciók jelenlétében is, ami megakadályozza, hogy az IT-csoport ne legyen a szabályozásban.

## <a name="the-current-database-recovery-process"></a>Az adatbázis jelenlegi helyreállítási folyamata

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

Emellett az SQL-adatbázismotor nem tudja lerövidíteni a tranzakciónaplót, ha hosszú ideig futó tranzakciók vannak, mert a helyreállítási és a visszaállítási folyamatokhoz a megfelelő naplóbejegyzések szükségesek. Az SQL-adatbázismotor ezen kialakításának eredményeképpen egyes ügyfelek szembesülnek azzal a problémával, hogy a tranzakciónapló mérete nagyon nagy mértékben növekszik, és nagy mennyiségű lemezterületet használ fel.

## <a name="the-accelerated-database-recovery-process"></a>A gyorsított adatbázis-helyreállítási folyamat

Az ADR a fenti problémákra az SQL-adatbázismotor helyreállítási folyamatának teljes újratervezésével foglalkozik:

- Ha nem szeretné, hogy a rendszer a naplót a legrégebbi aktív tranzakció elejétől, akkor ne végezzen állandó időt/pillanatot. Az ADR használata esetén a tranzakciónapló csak az utolsó sikeres ellenőrzőponton (vagy a legrégebbi inkonzisztens oldal naplózási sorszáma (LSN)) lesz feldolgozva. Ennek eredményeképpen a hosszú ideig futó tranzakciók nem befolyásolják a helyreállítási időt.
- Csökkentse a szükséges tranzakciós napló területét, mert már nem kell feldolgoznia a naplót a teljes tranzakcióhoz. Ennek eredményeképpen a tranzakciós naplót agresszíven lehet lerövidíteni ellenőrzőpontok és biztonsági mentések esetén.

Magas szinten az ADR a fizikai adatbázis összes módosításának verziószámozásával gyors adatbázis-helyreállítást tesz lehetővé, és csak a logikai műveleteket végzi el, amelyek korlátozottak, és szinte azonnal visszavonhatók. Minden olyan tranzakció, amely egy összeomlás időpontjában aktív, meg lett szakítva, ezért az ilyen tranzakciók által generált verziók figyelmen kívül hagyhatók egyidejű felhasználói lekérdezések esetén.

Az ADR helyreállítási folyamata a jelenlegi helyreállítási folyamattal megegyező három fázissal rendelkezik. Az alábbi ábrán látható, hogy a fázisok hogyan működnek az ADR-sel, és részletesebben ismerteti a diagramot követő részleteket.

![ADR helyreállítási folyamat](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Elemzési fázis**

  A folyamat továbbra is ugyanaz marad, mint a még ma, és nem a verzióval ellátott műveletek naplófájljainak másolásával.
  
- **Ismétlési** fázis

  Két fázisra bontva (P)
  - 1\. fázis

      Újra a üt (a legrégebbi, nem véglegesített tranzakció egészen az utolsó ellenőrzőpontig). Az ismétlés egy gyors művelet, mivel csak néhány rekordot kell feldolgoznia a üt-ból.
      
  - 2\. fázis

     Visszaállítás a tranzakciónaplóból az utolsó ellenőrzőponttól indul (a legrégebbi nem véglegesített tranzakciók helyett)
     
- **Visszavonás fázisa**

   A visszavonási fázis az ADR használatával szinte azonnal elvégezhető a üt paranccsal a nem verziószámmal ellátott műveletek és a megőrzött verzió-tároló (PVS) visszavonásához a logikai visszaállítással a sor szintű verzió-alapú visszavonás végrehajtásához.

## <a name="adr-recovery-components"></a>ADR helyreállítási összetevők

Az ADR négy fő összetevője a következők:

- **Megőrzött verzió tárolója (PVS)**

  A megőrzött verzió tároló egy új SQL adatbázismotor-mechanizmus, amellyel megőrizhető az adatbázisból előállított, a hagyományos `tempdb` verzió-áruház helyett létrehozott sor verziója. A PVS lehetővé teszi az erőforrások elkülönítését, valamint javítja az olvasható formátumú másodlagos zónák rendelkezésre állását.

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

## <a name="who-should-consider-accelerated-database-recovery"></a>Kinek érdemes megfontolnia a gyorsított adatbázis-helyreállítást

A következő típusú ügyfeleknek érdemes megfontolniuk az ADR engedélyezését:

- A hosszú ideig futó tranzakciókkal rendelkező munkaterhelésekkel rendelkező ügyfelek.
- Azok az ügyfelek, amelyek olyan eseteket észleltek, amikor az aktív tranzakciók jelentősen növekednek a tranzakciós naplóban.  
- Azok az ügyfelek, akik hosszú ideig tartó adatbázis-elérhetőséget tapasztaltak SQL Server hosszú ideig tartó helyreállítás miatt (például váratlan SQL Server újraindítást vagy manuális tranzakció-visszaállítást).

