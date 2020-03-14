---
title: Forgatókönyvek azonosítása az Azure Machine Learning - csoportos adatelemzési folyamat
description: Válassza ki a megfelelő ezzel a prediktív elemzés a csoportos adatelemzési folyamat a speciális forgatókönyvek.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251621"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Speciális elemzési forgatókönyvek az Azure Machine Learning rendszerben
Ez a cikk a különböző típusú adatforrások és a [csoportos adatelemzési folyamat (TDSP)](overview.md)által kezelhető célzott forgatókönyvek körét ismerteti. A TDSP rendszerezett megközelítést biztosít olyan munkacsoportok számára az intelligens alkalmazások készítéséhez. Az itt bemutatott forgatókönyveket mutatnak be lehetőségek az adatok feldolgozása a munkafolyamat, amely a adatjellemzők, az adatforrás helyének és az Azure-beli cél tárházak függ.

Az adatokhoz és a célkitűzéshez megfelelő minta-forgatókönyvek kiválasztásának **döntési fáját** az utolsó szakaszban mutatjuk be.

A következő szakaszok mindegyike megjelenít egy mintaforgatókönyv. Minden forgatókönyvben egy lehetséges adatelemzéshez vagy a fejlett analitikai folyamat és a támogató Azure-erőforrások listáját.

> [!NOTE]
> **Az alábbi forgatókönyvek mindegyike esetében a következőket kell tennie:**
> <br/>
> 
> * [Tárfiók létrehozása](../../storage/common/storage-account-create.md)
>   <br/>
> * [Azure Machine Learning munkaterület létrehozása](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>1. forgatókönyv \#: kis-közepes táblázatos adatkészlet helyi fájlokban
![Kis és közepes méretű helyi fájlok][1]

#### <a name="additional-azure-resources-none"></a>További Azure-erőforrások: nincs
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Töltsön fel egy adatkészletet.
1. Az Azure Machine Learning-kísérlet feltöltött adatkészlet(ek) kezdve folyamat létrehozása.

## <a name="smalllocalprocess"></a>2. forgatókönyv \#: a feldolgozást igénylő helyi fájlok kis és közepes méretű adatkészlete
![Kis és közepes méretű helyi fájlok és a feldolgozás][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép futtatása az IPython Notebook.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure Storage-tárolóból származó adatokhoz való hozzáférés a IPython Notebookban előre feldolgozható és törölhető.
1. Az adatokat egy tisztított táblázatos űrlapra alakíthatja át.
1. Az átalakított adatok mentése az Azure-blobok.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Az Azure-blobokból származó adatok beolvasása az [adatok importálása][import-data] modul használatával.
1. Egy Azure Machine Learning-kísérlet betöltött adatkészlet(ek) kezdve folyamat létrehozása.

## <a name="largelocal"></a>3. forgatókönyv \#: helyi fájlok nagyméretű adatkészlete, Azure-Blobok célzása
![Nagy méretű helyi fájlok][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép futtatása az IPython Notebook.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. IPython Notebook az Azure-blobokból az adatok elérése az adatok előzetes feldolgozása és.
1. Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
1. Fedezze fel az adatokat, és szükség szerint funkciók létrehozása.
1. Bontsa ki a kis és közepes méretű adatok minta.
1. A mintavételezett adatok mentése az Azure-blobokat.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Az Azure-blobokból származó adatok beolvasása az [adatok importálása][import-data] modul használatával.
1. Betöltött adatkészlet(ek) kezdve az Azure Machine Learning-kísérlet folyamat létrehozása.

## <a name="smalllocaltodb"></a>4. forgatókönyv \#: kis-és közepes méretű adatkészletek helyi fájlokhoz, az Azure-beli virtuális gépeken megcélzott SQL Server
![Kis és közepes méretű helyi fájlok az Azure SQL DB-hez][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure Storage-tárolóban az IPython notebook használatával előre feldolgozható és törölhető az adat.
1. Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
1. Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. Adatok betöltése az SQL Server-adatbázisból egy Azure virtuális gépen.
   
   \#1. lehetőség: SQL Server Management Studio használata.
   
   * Bejelentkezés SQL Server VM
   * Futtassa az SQL Server Management Studióval.
   * Adatbázis és a céloldali tábla létrehozása.
   * A tömeges közül módszerek az adatok betöltéséhez a virtuális gép helyi fájlok importálása.
   
   \#2. lehetőség: a IPython notebook használata – nem ajánlott közepes és nagyobb adatkészletekhez
   
   <!-- -->    
   * ODBC kapcsolati karakterlánc használata a virtuális Gépen futó SQL Server eléréséhez.
   * Adatbázis és a céloldali tábla létrehozása.
   * A tömeges közül módszerek az adatok betöltéséhez a virtuális gép helyi fájlok importálása.
1. Adatok feltárása, a funkciók létrehozása igény szerint. A funkciók nem szükségesek az adatbázis tábláiban. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Betöltött adatkészlet(ek) kezdve az Azure Machine Learning-kísérlet folyamat létrehozása.

## <a name="largelocaltodb"></a>5. forgatókönyv \#: nagyméretű adathalmaz helyi fájlokban, cél SQL Server az Azure-beli virtuális gépen
![Az SQL DB-hez az Azure-ban nagy méretű helyi fájlok][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook kiszolgálót.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. (Nem kötelező) Adatok előzetes feldolgozása és.
   
    a.  IPython Notebook az Azure-blobokból az adatok elérése az adatok előzetes feldolgozása és.
   
    b.  Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
   
    c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. Adatok betöltése az SQL Server-adatbázisból egy Azure virtuális gépen.
   
    a.  Jelentkezzen be SQL Server VMba.
   
    b.  Ha az adatok nem lettek mentve, töltse le az adatfájlokat az Azure Storage-tárolóból a helyi virtuális gép mappájába.
   
    c.  Futtassa az SQL Server Management Studióval.
   
    d.  Adatbázis és a céloldali tábla létrehozása.
   
    e.  A tömeges közül importálása módszerek az adatok betöltéséhez.
   
    f.  Ha a JOIN szükség, gyorsíthatja fel az illesztések indexet hoz létre.
   
   > [!NOTE]
   > A gyorsabb betöltési nagy mennyiségű adat méretű, ajánlott, hogy a particionált táblák létrehozása és tömeges adatimportálás párhuzamosan. További információ: [párhuzamos adatok importálása az SQL particionált táblákba](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Adatok feltárása, a funkciók létrehozása igény szerint. A funkciók nem szükségesek az adatbázis tábláiban. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Az Azure Machine Learning-kísérlet egyszerű folyamatot kezdve a feltöltött adatkészlet

## <a name="largedbtodb"></a>\#6. forgatókönyv: nagy adathalmaz egy SQL Server-adatbázisban a helyszínen, az Azure-beli virtuális gépeken SQL Server célzása
![Nagy SQL DB – helyszíni SQL-adatbázisba az Azure-ban][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook kiszolgálót.
1. Az adatok közül export metódus az adatok exportálása az SQL Server memóriaképeket.
   
   > [!NOTE]
   > Ha úgy dönt, hogy áthelyezi az összes adatát a helyszíni adatbázisból, egy alternatív (gyorsabb) metódust helyez át a teljes adatbázist az Azure SQL Server-példányára. Adatok exportálása, hozzon létre adatbázist, és terhelés /-importálási adatok a céladatbázis és kövesse a másik módszer a lépés kihagyható.
   > 
   > 
1. Memóriakép-fájlok feltöltése az Azure Storage-tárolóba.
1. Az adatok betöltése az Azure virtuális gépen futó SQL Server-adatbázis.
   
   a.  Jelentkezzen be a SQL Server VMba.
   
   b.  Töltse le az adatfájlokat egy Azure Storage-tárolóból a helyi virtuális gép mappájába.
   
   c.  Futtassa az SQL Server Management Studióval.
   
   d.  Adatbázis és a céloldali tábla létrehozása.
   
   e.  A tömeges közül importálása módszerek az adatok betöltéséhez.
   
   f.  Ha a JOIN szükség, gyorsíthatja fel az illesztések indexet hoz létre.
   
   > [!NOTE]
   > A nagy mennyiségű adat méretű gyorsabb betöltése a particionált táblákat hozhat létre, és tömeges adatimportálás párhuzamosan. További információ: [párhuzamos adatok importálása az SQL particionált táblákba](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Adatok feltárása, a funkciók létrehozása igény szerint. A funkciók nem szükségesek az adatbázis tábláiban. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Egyszerű Azure Machine Learning kísérlet folyamatot feltöltött adatkészlet kezdve.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatív módja teljes adatbázis másolása a helyszíni SQL Serverről az Azure SQL Database
![Válassza le a helyi adatbázis és az Azure SQL DB csatolása][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
A teljes SQL Server-adatbázis az SQL Server virtuális gép replikálása, meg kell egy adatbázis másolatát egy hálózatihely-kiszolgáló egy másikba, feltételezve, hogy az adatbázis átmenetileg offline állapotú lehessen állítani. Használhatja SQL Server Management Studio Object Explorer vagy a megfelelő Transact-SQL-parancsokat.

1. Válassza le a forráshely adatbázisához. További információ: [adatbázis leválasztása](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. A Windows Explorer vagy a Windows parancssori ablakban másolja a leválasztott adatbázisban fájl vagy fájlok és a naplófájl vagy naplófájlok cél helye az az SQL Server rendszerű virtuális gép az Azure-ban.
1. A másolt fájlok csatolása a cél SQL Server-példányt. További információ: [adatbázis csatolása](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Adatbázis áthelyezése a leválasztás és a csatolás (Transact-SQL) használatával](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>7. forgatókönyv: nagy mennyiségű, helyi fájlokban lévő \#, Azure HDInsight Hadoop-fürtökben található kaptár-adatbázis
![A helyi cél Hive big Data típusú adatok][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: az Azure HDInsight Hadoop-fürt és az Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy IPython Notebook Servert futtató Azure virtuális gépet.
1. Hozzon létre egy Azure HDInsight Hadoop-fürtöt.
1. (Nem kötelező) Adatok előzetes feldolgozása és.
   
   a.  Adatok az IPython Notebook az Azure-ból adatok elérése és előzetes feldolgozása
   
       blobs.
   
   b.  Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
   
   c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. A 2. lépésben kiválasztott Hadoop-fürt alapértelmezett tárolója feltölteni az adatokat.
1. Adatok betöltése az Azure HDInsight Hadoop-fürtben Hive-adatbázis.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjának
   
   b.  Nyissa meg a Hadoop parancssor.
   
   c.  Adja meg a kaptár gyökérkönyvtárát a Hadoop parancssorban `cd %hive_home%\bin` parancs használatával.
   
   d.  Az adatbázis és tábla létrehozása Hive-lekérdezések futtatásához és Hive-tábláihoz beolvassa az adatokat blob storage-ból.
   
   > [!NOTE]
   > Big Data típusú adatok esetén a felhasználók létrehozhatnak a Hive-tábla partícióval. Ezt követően a felhasználók használhatnak egy `for` hurkot a Hadoop parancssorában a fő csomóponton, hogy az adatkészletet a partíciók particionált particionálási táblájában tárolja.
   > 
   > 
1. Adatok feltárása és funkciók létrehozása, a Hadoop parancssor igény szerint. A funkciók nem szükségesek az adatbázis tábláiban. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjának
   
   b.  Nyissa meg a Hadoop parancssor.
   
   c.  Adja meg a kaptár gyökérkönyvtárát a Hadoop parancssorban `cd %hive_home%\bin` parancs használatával.
   
   d.  Futtassa a Hive-lekérdezések a Hadoop parancssor a feltárhatja az adatait, és szükség szerint funkciók létrehozása a Hadoop-fürt főcsomópontjához.
1. Ha szükséges, és/vagy kívánt, mintaadatokkal megfelelően az Azure Machine Learning Studióban.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a `Hive Queries` az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Egyszerű Azure Machine Learning kísérlet folyamatot feltöltött adatkészlet kezdve.

## <a name="decisiontree"></a>Döntési fa a forgatókönyvek kiválasztásához
---
A következő ábra összefoglalja a fent leírt forgatókönyveket és a fejlett analitikai folyamat és technológia választott lehetőségek, amelyek az egyes a tételes forgatókönyvek esetében. Az adatfeldolgozás, a feltárás, a szolgáltatások és a mintavételezés egy vagy több módszerrel/környezetben, a forrás, a köztes és/vagy a cél környezetekben is elvégezhető, és szükség esetén folytathatja a iteratív. A diagram csak néhány lehetséges folyamatok olyan bemutatásáért funkcionál, és nem biztosít teljes körű enumerálás.

![Minta DS folyamatot bemutató forgatókönyvek][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytics példák működés közben
Teljes körű Azure Machine Learning forgatókönyvek, amelyek a alkalmazni a fejlett analitikai folyamat és technológia nyilvános adatkészleteket használó lásd:

* [Csoportos adatelemzési folyamat működés közben: SQL Server használata](sql-walkthrough.md).
* [Csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
