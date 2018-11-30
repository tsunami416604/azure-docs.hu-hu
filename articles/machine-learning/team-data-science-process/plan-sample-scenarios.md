---
title: Azonosítsa a továbbfejlesztett elemzési forgatókönyvek az Azure Machine Learning |} A Microsoft Docs
description: Válassza ki a megfelelő ezzel a prediktív elemzés a csoportos adatelemzési folyamat a speciális forgatókönyvek.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 16d8457e5605ce0e3c10ed95296b5a883a4c602c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444834"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Speciális elemzési forgatókönyvek az Azure Machine Learning rendszerben
Ez a cikk ismerteti a különböző minta adatforrások és a cél forgatókönyvek tudja kezelni a [csoportos adatelemzési folyamat (TDSP)](overview.md). A TDSP rendszerezett megközelítést biztosít olyan munkacsoportok számára az intelligens alkalmazások készítéséhez. Az itt bemutatott forgatókönyveket mutatnak be lehetőségek az adatok feldolgozása a munkafolyamat, amely a adatjellemzők, az adatforrás helyének és az Azure-beli cél tárházak függ.

A **döntési fa** esetében a mintaként használható jelen forgatókönyvek megfelelő adat-és cél kiválasztása az utolsó szakaszban jelenik meg.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

A következő szakaszok mindegyike megjelenít egy mintaforgatókönyv. Minden forgatókönyvben egy lehetséges adatelemzéshez vagy a fejlett analitikai folyamat és a támogató Azure-erőforrások listáját.

> [!NOTE]
> **Az összes, a következő esetekben kell tennie:**
> <br/>
> 
> * [Tárfiók létrehozása](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Az Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>A forgatókönyv \#1: kicsi, közepes méretű tabulált adatkészlet egy helyi fájlok
![Kis és közepes méretű helyi fájlok][1]

#### <a name="additional-azure-resources-none"></a>További Azure-erőforrások: nincs
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Töltsön fel egy adatkészletet.
1. Az Azure Machine Learning-kísérlet feltöltött adatkészlet(ek) kezdve folyamat létrehozása.

## <a name="smalllocalprocess"></a>A forgatókönyv \#2: kicsi, közepes méretű adatkészlet feldolgozást igénylő helyi fájlok
![Kis és közepes méretű helyi fájlok és a feldolgozás][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép futtatása az IPython Notebook.
1. Adatok feltöltése egy Azure storage-tárolóba.
1. IPython Notebook az Azure storage-tárolóból az adatok elérése az adatok előzetes feldolgozása és.
1. Alakítsa át az adatokat megtisztítani, táblázatos formában.
1. Az átalakított adatok mentése az Azure-blobok.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Azure-blobokat használ az adatok beolvasása a [adatok importálása] [ import-data] modul.
1. Egy Azure Machine Learning-kísérlet betöltött adatkészlet(ek) kezdve folyamat létrehozása.

## <a name="largelocal"></a>A forgatókönyv \#3: nagy méretű adathalmazt a helyi fájlok célzó Azure-Blobok
![Nagy méretű helyi fájlok][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép futtatása az IPython Notebook.
1. Adatok feltöltése egy Azure storage-tárolóba.
1. IPython Notebook az Azure-blobokból az adatok elérése az adatok előzetes feldolgozása és.
1. Adatátalakítás tisztítani, táblázatos formában, ha szükséges.
1. Fedezze fel az adatokat, és szükség szerint funkciók létrehozása.
1. Bontsa ki a kis és közepes méretű adatok minta.
1. A mintavételezett adatok mentése az Azure-blobokat.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Azure-blobokat használ az adatok beolvasása a [adatok importálása] [ import-data] modul.
1. Betöltött adatkészlet(ek) kezdve az Azure Machine Learning-kísérlet folyamat létrehozása.

## <a name="smalllocaltodb"></a>A forgatókönyv \#4: kicsi, közepes méretű adatkészlet a helyi fájlok, SQL Server egy Azure virtuális gép célzó
![Kis és közepes méretű helyi fájlok az Azure SQL DB-hez][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook.
1. Adatok feltöltése egy Azure storage-tárolóba.
1. IPython Notebook használata az Azure storage-tárolót az adatok előzetes feldolgozása és.
1. Adatátalakítás tisztítani, táblázatos formában, ha szükséges.
1. Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. Adatok betöltése az SQL Server-adatbázisból egy Azure virtuális gépen.
   
   A beállítás \#1: az SQL Server Management Studióval.
   
   * Jelentkezzen be az SQL Servert futtató virtuális gép
   * Futtassa az SQL Server Management Studióval.
   * Adatbázis és a céloldali tábla létrehozása.
   * A tömeges közül módszerek az adatok betöltéséhez a virtuális gép helyi fájlok importálása.
   
   A beállítás \#2: használatával IPython Notebook – közepes és nagyobb adatkészletek esetében nem javasolt
   
   <!-- -->    
   * ODBC kapcsolati karakterlánc használata a virtuális Gépen futó SQL Server eléréséhez.
   * Adatbázis és a céloldali tábla létrehozása.
   * A tömeges közül módszerek az adatok betöltéséhez a virtuális gép helyi fájlok importálása.
1. Adatok feltárása, a funkciók létrehozása igény szerint. Vegye figyelembe, hogy az funkciók nem kell is lehet az adatbázistáblák. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Az adatok beolvasása közvetlenül az SQL Server használatával a [adatok importálása] [ import-data] modul. Illessze be a szükséges lekérdezést, amely kinyeri a mezők, szolgáltatásokat hoz létre, és minták adatokat, ha közvetlenül a szükséges a [adatok importálása] [ import-data] lekérdezés.
1. Betöltött adatkészlet(ek) kezdve az Azure Machine Learning-kísérlet folyamat létrehozása.

## <a name="largelocaltodb"></a>A forgatókönyv \#5: nagy méretű adathalmazt a helyi fájlok a cél SQL Server Azure-beli virtuális gépen
![Az SQL DB-hez az Azure-ban nagy méretű helyi fájlok][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook kiszolgálót.
1. Adatok feltöltése egy Azure storage-tárolóba.
1. (Nem kötelező) Adatok előzetes feldolgozása és.
   
   a.  Adatok az IPython Notebook az Azure-ból adatok elérése és előzetes feldolgozása
   
       blobs.
   
   b.  Adatátalakítás tisztítani, táblázatos formában, ha szükséges.
   
   c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. Adatok betöltése az SQL Server-adatbázisból egy Azure virtuális gépen.
   
   a.  Jelentkezzen be az SQL Servert futtató virtuális gép.
   
   b.  Ha adatok nem már mentette, töltse le az adatfájlokat az Azure-ból
   
       storage container to local-VM folder.
   
   c.  Futtassa az SQL Server Management Studióval.
   
   d.  Adatbázis és a céloldali tábla létrehozása.
   
   e.  A tömeges közül importálása módszerek az adatok betöltéséhez.
   
   f.  Ha a JOIN szükség, gyorsíthatja fel az illesztések indexet hoz létre.
   
   > [!NOTE]
   > A gyorsabb betöltési nagy mennyiségű adat méretű, ajánlott, hogy a particionált táblák létrehozása és tömeges adatimportálás párhuzamosan. További információkért lásd: [párhuzamos adatok importálása az SQL-particionált táblák](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Adatok feltárása, a funkciók létrehozása igény szerint. Vegye figyelembe, hogy az funkciók nem kell is lehet az adatbázistáblák. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Az adatok beolvasása közvetlenül az SQL Server használatával a [adatok importálása] [ import-data] modul. Illessze be a szükséges lekérdezést, amely kinyeri a mezők, szolgáltatásokat hoz létre, és minták adatokat, ha közvetlenül a szükséges a [adatok importálása] [ import-data] lekérdezés.
1. Az Azure Machine Learning-kísérlet egyszerű folyamatot kezdve a feltöltött adatkészlet

## <a name="largedbtodb"></a>A forgatókönyv \#6: nagy méretű adatkészlet egy SQL Server adatbázis helyben, SQL Server egy Azure virtuális gép célzó
![Nagy SQL DB – helyszíni SQL-adatbázisba az Azure-ban][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
1. Hozzon létre egy Azure virtuális gép fut az SQL Server és az IPython Notebook kiszolgálót.
1. Az adatok közül export metódus az adatok exportálása az SQL Server memóriaképeket.
   
   > [!NOTE]
   > Ha úgy dönt, hogy minden adat áthelyezése a helyszíni adatbázisból, a teljes adatbázis áthelyezése az Azure-beli SQL Server-példány (gyorsabb) alternatív módszert. Adatok exportálása, hozzon létre adatbázist, és terhelés /-importálási adatok a céladatbázis és kövesse a másik módszer a lépés kihagyható.
   > 
   > 
1. Memóriaképek feltöltése az Azure storage-tárolóba.
1. Az adatok betöltése az Azure virtuális gépen futó SQL Server-adatbázis.
   
   a.  Jelentkezzen be az SQL Server virtuális gép.
   
   b.  Töltse le az Azure storage-tárolókat adatfájlokat a helyi virtuális mappába.
   
   c.  Futtassa az SQL Server Management Studióval.
   
   d.  Adatbázis és a céloldali tábla létrehozása.
   
   e.  A tömeges közül importálása módszerek az adatok betöltéséhez.
   
   f.  Ha a JOIN szükség, gyorsíthatja fel az illesztések indexet hoz létre.
   
   > [!NOTE]
   > A nagy mennyiségű adat méretű gyorsabb betöltése a particionált táblákat hozhat létre, és tömeges adatimportálás párhuzamosan. További információkért lásd: [párhuzamos adatok importálása az SQL-particionált táblák](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Adatok feltárása, a funkciók létrehozása igény szerint. Vegye figyelembe, hogy az funkciók nem kell is lehet az adatbázistáblák. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
1. Ha szükséges, és/vagy a kívánt dönt adatméret minta.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Az adatok beolvasása közvetlenül az SQL Server használatával a [adatok importálása] [ import-data] modul. Illessze be a szükséges lekérdezést, amely kinyeri a mezők, szolgáltatásokat hoz létre, és minták adatokat, ha közvetlenül a szükséges a [adatok importálása] [ import-data] lekérdezés.
1. Egyszerű Azure Machine Learning kísérlet folyamatot feltöltött adatkészlet kezdve.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatív módja teljes adatbázis másolása a helyszíni SQL Serverről az Azure SQL Database
![Válassza le a helyi adatbázis és az Azure SQL DB csatolása][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (az SQL Server / IPython Notebook server)
A teljes SQL Server-adatbázis az SQL Server virtuális gép replikálása, meg kell egy adatbázis másolatát egy hálózatihely-kiszolgáló egy másikba, feltételezve, hogy az adatbázis átmenetileg offline állapotú lehessen állítani. Ehhez az SQL Server Management Studio Object Explorer vagy az azzal egyenértékű Transact-SQL parancsokkal.

1. Válassza le a forráshely adatbázisához. További információkért lásd: [adatbázis leválasztása](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. A Windows Explorer vagy a Windows parancssori ablakban másolja a leválasztott adatbázisban fájl vagy fájlok és a naplófájl vagy naplófájlok cél helye az az SQL Server rendszerű virtuális gép az Azure-ban.
1. A másolt fájlok csatolása a cél SQL Server-példányt. További információkért lásd: [adatbázis csatolása](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Helyezze át egy adatbázis használatával válassza le és csatlakoztassa (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>A forgatókönyv \#7: helyi fájlok, a big Data típusú adatok Hive-adatbázis az Azure HDInsight Hadoop-fürtök cél
![A helyi cél Hive big Data típusú adatok][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: az Azure HDInsight Hadoop-fürt és az Azure virtuális gép (IPython Notebook server)
1. Hozzon létre egy IPython Notebook Servert futtató Azure virtuális gépet.
1. Hozzon létre egy Azure HDInsight Hadoop-fürtöt.
1. (Nem kötelező) Adatok előzetes feldolgozása és.
   
   a.  Adatok az IPython Notebook az Azure-ból adatok elérése és előzetes feldolgozása
   
       blobs.
   
   b.  Adatátalakítás tisztítani, táblázatos formában, ha szükséges.
   
   c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook fut, a virtuális gép, Virtuálisgép-meghajtókat helyi meghajtók tekintse meg).
1. A 2. lépésben kiválasztott Hadoop-fürt alapértelmezett tárolója feltölteni az adatokat.
1. Adatok betöltése az Azure HDInsight Hadoop-fürtben Hive-adatbázis.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjának
   
   b.  Nyissa meg a Hadoop parancssor.
   
   c.  Adja meg a Hive gyökérkönyvtár parancs által `cd %hive_home%\bin` Hadoop parancssor.
   
   d.  Az adatbázis és tábla létrehozása Hive-lekérdezések futtatásához és Hive-tábláihoz beolvassa az adatokat blob storage-ból.
   
   > [!NOTE]
   > Big Data típusú adatok esetén a felhasználók létrehozhatnak a Hive-tábla partícióval. Ezt követően a felhasználók használhatják a `for` ciklus az a Hadoop parancssor a fő csomópontot a táblába való betöltéséhez adatokat a Hive segítségével partíció.
   > 
   > 
1. Adatok feltárása és funkciók létrehozása, a Hadoop parancssor igény szerint. Vegye figyelembe, hogy az funkciók nem kell is lehet az adatbázistáblák. Csak vegye figyelembe a szükséges lekérdezést kell létrehoznia őket.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjának
   
   b.  Nyissa meg a Hadoop parancssor.
   
   c.  Adja meg a Hive gyökérkönyvtár parancs által `cd %hive_home%\bin` Hadoop parancssor.
   
   d.  Futtassa a Hive-lekérdezések a Hadoop parancssor a feltárhatja az adatait, és szükség szerint funkciók létrehozása a Hadoop-fürt főcsomópontjához.
1. Ha szükséges, és/vagy kívánt, mintaadatokkal megfelelően az Azure Machine Learning Studióban.
1. Jelentkezzen be a [az Azure Machine Learning Studio](https://studio.azureml.net/).
1. Olvassa el az adatokat közvetlenül a `Hive Queries` használatával a [adatok importálása] [ import-data] modul. Illessze be a szükséges lekérdezést, amely kinyeri a mezők, szolgáltatásokat hoz létre, és minták adatokat, ha közvetlenül a szükséges a [adatok importálása] [ import-data] lekérdezés.
1. Egyszerű Azure Machine Learning kísérlet folyamatot feltöltött adatkészlet kezdve.

## <a name="decisiontree"></a>Forgatókönyv kiválasztása döntési fája
- - -
A következő ábra összefoglalja a fent leírt forgatókönyveket és a fejlett analitikai folyamat és technológia választott lehetőségek, amelyek az egyes a tételes forgatókönyvek esetében. Vegye figyelembe, hogy az adatokat feldolgozó, feltárása, funkciófejlesztési és mintavételi eltarthat egy vagy több módszer/környezet – a forrásnál, köztes, és/vagy a célzott adathalászatban – helyezze, és szükség szerint iteratív folytathatja. A diagram csak néhány lehetséges folyamatok olyan bemutatásáért funkcionál, és nem biztosít teljes körű enumerálás.

![Minta DS folyamatot bemutató forgatókönyvek][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytics példák működés közben
Teljes körű Azure Machine Learning forgatókönyvek, amelyek a alkalmazni a fejlett analitikai folyamat és technológia nyilvános adatkészleteket használó lásd:

* [Csoportos adatelemzési folyamat működés közben: az SQL-kiszolgáló](sql-walkthrough.md).
* [Csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtöket használó](hive-walkthrough.md).

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
