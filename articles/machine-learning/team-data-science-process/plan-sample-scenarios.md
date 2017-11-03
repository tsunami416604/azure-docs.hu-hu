---
title: "Az Azure Machine Learning speciális elemzésekre forgatókönyveinek |} Microsoft Docs"
description: "Válassza ki a megfelelő forgatókönyvek speciális az Team tudományos folyamat a prediktív elemzés megteheti."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 1dbc47b8a56fb2d295adfea0920b7eea45be69a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Speciális elemzési forgatókönyvek az Azure Machine Learning rendszerben
Ez a cikk ismerteti a különböző minta adatforrások és a cél-szolgáltatásokat, amelyek kezelhetik a [Team adatok tudományos folyamat (TDSP)](overview.md). A TDSP munkacsoportok számára az intelligens alkalmazások rendszeres megközelítését ismerteti. Az itt bemutatott esetekben bemutatják lehetőségeit adatok feldolgozása a munkafolyamatban, amely a adatjellemzők, az adatforrás helyének és a cél tárházak találhatók, az Azure-ban függ.

A **döntési fa** a kiválasztása a mintaforgatókönyvek megfelelő az adatok és a cél számára jelenik meg az utolsó szakaszban.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

A következő szakaszok mindegyikének megadja egy mintaforgatókönyv. Az egyes forgatókönyvek esetében, egy lehetséges adattudomány vagy speciális elemzésekre folyamata és a támogató Azure-erőforrások találhatók.

> [!NOTE]
> **Az összes a következő esetekben kell:**
> <br/>
> 
> * [A storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [Az Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>A forgatókönyv \#1: kicsi, közepes méretű táblázatos adatkészlet egy helyi fájlok
![Kis-és közepes méretű helyi fájlok][1]

#### <a name="additional-azure-resources-none"></a>További Azure-erőforrások: nincs
1. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
2. Töltse fel a DataSet adatkészlet.
3. Hozzon létre egy Azure Machine Learning kísérlet folyamat feltöltött adatkészlet(ek) kezdve.

## <a name="smalllocalprocess"></a>A forgatókönyv \#2: kis, közepes méretű DataSet feldolgozást igénylő helyi fájlok
![Kis-és közepes méretű helyi fájlok feldolgozása][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook kiszolgáló)
1. Hozzon létre egy Azure virtuális gépen futó IPython Notebook.
2. Adatok feltöltése egy Azure storage-tárolóhoz.
3. Előre feldolgozzák a, és az adatok elérése az Azure storage tárolóból IPython Notebook adatait.
4. Alakítsa át adatokat a tisztítás, táblázatos formában.
5. Az Azure-blobokat átalakított adatok mentése.
6. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
7. Az adatokat olvasni az Azure-blobokat használ a [és adatokat importálhat] [ import-data] modul.
8. Hozzon létre egy Azure Machine Learning kísérlet folyamat feldolgozott adatkészlet(ek) kezdve.

## <a name="largelocal"></a>A forgatókönyv \#3: a helyi fájloknak, Azure-Blobokkal célzó nagy adatkészlet
![Nagy helyi fájlok][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython Notebook kiszolgáló)
1. Hozzon létre egy Azure virtuális gépen futó IPython Notebook.
2. Adatok feltöltése egy Azure storage-tárolóhoz.
3. Előre feldolgozzák, és az adatok elérése az Azure-blobokat IPython Notebook adatait.
4. Alakítsa át adatokat a tisztítás, táblázatos formában, szükség esetén.
5. Adatokba, és szükség szerint funkciók létrehozása.
6. Bontsa ki a kis és közepes méretű mintáját.
7. Mentse a mintaadatokat az Azure-blobokat.
8. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Az adatokat olvasni az Azure-blobokat használ a [és adatokat importálhat] [ import-data] modul.
10. Build Azure Machine Learning kísérlet folyamata feldolgozott adatkészlet(ek) kezdve.

## <a name="smalllocaltodb"></a>A forgatókönyv \#4: kis, közepes méretű DataSet helyi fájlok, SQL Server egy Azure virtuális gép célzó
![Kis-és közepes méretű helyi fájlok az Azure SQL Adatbázishoz][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (SQL Server / IPython Notebook kiszolgáló)
1. Hozzon létre egy Azure virtuális gépen fut az SQL-kiszolgáló + IPython Notebookot.
2. Adatok feltöltése egy Azure storage-tárolóhoz.
3. Előre feldolgozzák, és az Azure storage tárolóban IPython Notebook használatával adatait.
4. Alakítsa át adatokat a tisztítás, táblázatos formában, szükség esetén.
5. Adatok mentése a virtuális gép helyi fájlok (IPython Notebook a virtuális gép fut, helyi meghajtók tekintse meg a VM-meghajtók).
6. Egy Azure virtuális Gépen futó SQL Server-adatbázis az adatok betöltése.
   
   A beállítás \#1: SQL Server Management Studio használatával.
   
   * Jelentkezzen be az SQL Server rendszerű virtuális Géphez
   * Futtassa az SQL Server Management Studio eszközt.
   * Adatbázis és a célként megadott táblák létrehozása.
   * Használja a tömeges importálásához módszerek a virtuális gép helyi fájlok az adatok betöltésére.
   
   A beállítás \#2: használatával IPython Notebook – közepes és nagyobb adatkészletek esetében nem ajánlott
   
   <!-- -->    
   * A virtuális gép az SQL Server eléréséhez használja az ODBC kapcsolati karakterlánc.
   * Adatbázis és a célként megadott táblák létrehozása.
   * Használja a tömeges importálásához módszerek a virtuális gép helyi fájlok az adatok betöltésére.
7. Adatokba, és szolgáltatások igény szerint. Ne feledje, hogy a szolgáltatások nem kell az adatbázis táblázatokban materializált. Csak jegyezze fel a szükséges lekérdezést kell létrehoznia őket.
8. Döntse el, az adatok mintájának méretét, ha szükséges, és/vagy a szükséges.
9. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
10. Közvetlenül olvassák be az adatokat az SQL Server használja a [és adatokat importálhat] [ import-data] modul. Illessze be a szükséges lekérdezést, mely bontja ki a mezőket, szolgáltatások létrehozza, és minták adatok közvetlenül szükség esetén a [és adatokat importálhat] [ import-data] lekérdezés.
11. Build Azure Machine Learning kísérlet folyamata feldolgozott adatkészlet(ek) kezdve.

## <a name="largelocaltodb"></a>A forgatókönyv \#5: a helyi fájlok nagy dataset cél SQL Server Azure virtuális gépen
![Nagy helyi fájlok az Azure SQL Adatbázishoz][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (SQL Server / IPython Notebook kiszolgáló)
1. Hozzon létre egy Azure virtuális gépen futó SQL Server IPython Notebook kiszolgáló.
2. Adatok feltöltése egy Azure storage-tárolóhoz.
3. (Választható) Előre feldolgozzák és adatait.
   
   a.  Előre feldolgozzák és adatait IPython jegyzetfüzet adatok elérése az Azure-ból
   
       blobs.
   
   b.  Alakítsa át adatokat a tisztítás, táblázatos formában, szükség esetén.
   
   c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook a virtuális gép fut, helyi meghajtók tekintse meg a VM-meghajtók).
4. Egy Azure virtuális Gépen futó SQL Server-adatbázis az adatok betöltése.
   
   a.  Bejelentkezés az SQL Server rendszerű virtuális Géphez.
   
   b.  Ha az adatok nem már mentve, adatfájlok le az Azure-ból
   
       storage container to local-VM folder.
   
   c.  Futtassa az SQL Server Management Studio eszközt.
   
   d.  Adatbázis és a célként megadott táblák létrehozása.
   
   e.  Használja a tömeges importálásához módszerek az adatok betöltésére.
   
   f.  Ha táblákra szükség, illesztések elősegítésére indexek létrehozása.
   
   > [!NOTE]
   > Nagy adatmennyiség gyorsabb betöltését, javasolt, hogy a particionált táblák létrehozása, és tömeges az adatimportálás párhuzamosan. További információkért lásd: [párhuzamos importálhat SQL particionált táblák](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Adatokba, és szolgáltatások igény szerint. Ne feledje, hogy a szolgáltatások nem kell az adatbázis táblázatokban materializált. Csak jegyezze fel a szükséges lekérdezést kell létrehoznia őket.
6. Döntse el, az adatok mintájának méretét, ha szükséges, és/vagy a szükséges.
7. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Közvetlenül olvassák be az adatokat az SQL Server használja a [és adatokat importálhat] [ import-data] modul. Illessze be a szükséges lekérdezést, mely bontja ki a mezőket, szolgáltatások létrehozza, és minták adatok közvetlenül szükség esetén a [és adatokat importálhat] [ import-data] lekérdezés.
9. Egyszerű Azure Machine Learning kísérlet folyamata feltöltött dataset kezdődően

## <a name="largedbtodb"></a>A forgatókönyv \#6: nagy adatkészlet egy SQL Server adatbázis a helyszínen, célzás SQL Server egy Azure virtuális gépen
![Nagy SQL DB a helyszínen az Azure SQL Adatbázishoz][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (SQL Server / IPython Notebook kiszolgáló)
1. Hozzon létre egy Azure virtuális gépen futó SQL Server IPython Notebook kiszolgáló.
2. Használja az adatok exportálása az adatok exportálása az SQL Server memóriaképek módszerek.
   
   > [!NOTE]
   > Ha úgy dönt, hogy minden adat áthelyezése a helyszíni adatbázis, a teljes adatbázis áthelyezése az Azure SQL Server-példány (gyorsabb) alternatív módszert. Hagyja ki a lépéseket, exportálhatja az adatokat hozzon létre adatbázis, és a céladatbázis adatok betöltése vagy importálása, és kövesse az alternatív módszert.
   > 
   > 
3. Biztonsági másolat fájlok feltöltése az Azure storage tárolóba.
4. Egy Azure virtuális gépen futó SQL Server-adatbázishoz az adatok betöltésére.
   
   a.  Bejelentkezés az SQL Server virtuális gép.
   
   b.  Adatfájlok le az Azure storage-tárolójából a helyi-VM mappába.
   
   c.  Futtassa az SQL Server Management Studio eszközt.
   
   d.  Adatbázis és a célként megadott táblák létrehozása.
   
   e.  Használja a tömeges importálásához módszerek az adatok betöltésére.
   
   f.  Ha táblákra szükség, illesztések elősegítésére indexek létrehozása.
   
   > [!NOTE]
   > A nagy méretű gyorsabb betöltése particionált táblák létrehozása, és tömeges adatimportálás párhuzamosan. További információkért lásd: [párhuzamos importálhat SQL particionált táblák](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Adatokba, és szolgáltatások igény szerint. Ne feledje, hogy a szolgáltatások nem kell az adatbázis táblázatokban materializált. Csak jegyezze fel a szükséges lekérdezést kell létrehoznia őket.
6. Döntse el, az adatok mintájának méretét, ha szükséges, és/vagy a szükséges.
7. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Közvetlenül olvassák be az adatokat az SQL Server használja a [és adatokat importálhat] [ import-data] modul. Illessze be a szükséges lekérdezést, mely bontja ki a mezőket, szolgáltatások létrehozza, és minták adatok közvetlenül szükség esetén a [és adatokat importálhat] [ import-data] lekérdezés.
9. Egyszerű Azure Machine Learning kísérlet folyamata feltöltött dataset kezdve.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatív módszert egy helyi SQL Server az Azure SQL Database egy teljes adatbázis másolása
![Válassza le a helyi adatbázis és az Azure SQL-adatbázis csatlakoztatása][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (SQL Server / IPython Notebook kiszolgáló)
A teljes SQL Server-adatbázist az SQL Server virtuális gépen replikálni, másolja egy adatbázis egy hálózatihely-kiszolgáló a a másikra, feltéve, hogy az adatbázis átmenetileg offline állapotú lehet tenni. Ehhez az SQL Server Management Studio Object Explorer vagy a megfelelő Transact-SQL-parancsok használatával.

1. Válassza le a forráshely adatbázisához. További információkért lásd: [egy adatbázis leválasztásához](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
2. A Windows Explorer vagy a Windows parancssori ablakban másolja a adatbázisát fájl vagy fájlokat és a naplófájl vagy naplófájlok az SQL Server virtuális gépen, az Azure-ban a célhelyre.
3. A másolt fájlok csatolása a célként megadott SQL Server-példány. További információkért lásd: [adatbázis csatolása](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Helyezze át egy adatbázis használatával válassza le, és csatolja (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>A forgatókönyv \#7: helyi fájlok Big Data típusú adatok cél Hive-adatbázisban az Azure HDInsight Hadoop-fürtök
![A helyi cél Hive big Data típusú adatok][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: az Azure HDInsight Hadoop-fürt és az Azure virtuális gép (IPython Notebook kiszolgáló)
1. Hozzon létre egy IPython Notebook Servert futtató Azure virtuális gépen.
2. Hozzon létre egy Azure HDInsight Hadoop-fürt.
3. (Választható) Előre feldolgozzák és adatait.
   
   a.  Előre feldolgozzák és adatait IPython jegyzetfüzet adatok elérése az Azure-ból
   
       blobs.
   
   b.  Alakítsa át adatokat a tisztítás, táblázatos formában, szükség esetén.
   
   c.  Adatok mentése a virtuális gép helyi fájlok (IPython Notebook a virtuális gép fut, helyi meghajtók tekintse meg a VM-meghajtók).
4. Az alapértelmezett tároló, a Hadoop-fürt a 2. lépésben kiválasztott feltölteni az adatokat.
5. Az Azure HDInsight Hadoop-fürt Hive database adatok betöltése.
   
   a.  Jelentkezzen be a Hadoop-fürt átjárócsomópontjához
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Adja meg a Hive gyökérkönyvtár parancs `cd %hive_home%\bin` Hadoop parancssor futtatása.
   
   d.  Adatbázis és a táblák létrehozásához a Hive-lekérdezések futtatása, és az adatok betöltése az blob storage Hive táblákat.
   
   > [!NOTE]
   > Ha az adatok nagy, a felhasználók létrehozhatják a Hive tábla partíciókat. Ezt követően a felhasználók használhatják a `for` hurok a Hadoop parancssori az átjárócsomópont az adatok betöltése a Hive tábla particionálva partíció által az.
   > 
   > 
6. Adatokba, és szükség esetén a Hadoop parancssori funkciók létrehozása. Ne feledje, hogy a szolgáltatások nem kell az adatbázis táblázatokban materializált. Csak jegyezze fel a szükséges lekérdezést kell létrehoznia őket.
   
   a.  Jelentkezzen be a Hadoop-fürt átjárócsomópontjához
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Adja meg a Hive gyökérkönyvtár parancs `cd %hive_home%\bin` Hadoop parancssor futtatása.
   
   d.  A Hadoop parancssor az a Hive-lekérdezések futtatása az adatokba, és szükség szerint funkciók létrehozása a Hadoop-fürt központi csomópontján.
7. Ha szükséges, és/vagy szükséges, példa a az adatok az Azure Machine Learning Studióban.
8. Jelentkezzen be a [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Olvassa el az adatok közvetlenül a `Hive Queries` használatával a [és adatokat importálhat] [ import-data] modul. Illessze be a szükséges lekérdezést, mely bontja ki a mezőket, szolgáltatások létrehozza, és minták adatok közvetlenül szükség esetén a [és adatokat importálhat] [ import-data] lekérdezés.
10. Egyszerű Azure Machine Learning kísérlet folyamata feltöltött dataset kezdve.

## <a name="decisiontree"></a>A forgatókönyv kiválasztása döntési fája
- - -
Az alábbi ábra a fent leírt forgatókönyvek és a speciális Analytics folyamat és a technológia választások kattintva az egyes részletezett forgatókönyv foglalja össze. Vegye figyelembe, hogy az adatok feldolgozása, a feltárása, a szolgáltatás műszaki osztály és a mintavételi is igénybe vehet egy vagy több módszer/környezet – a forrás, a köztes, és/vagy a cél környezetekben – helyezze, és szükség szerint ismételt folytathatja. A diagram csak néhány lehetséges adatfolyamok szemléltetésére szolgál, és nem biztosít teljes körű enumerálást.

![A minta DS folyamat bemutató forgatókönyvek][8]

### <a name="advanced-analytics-in-action-examples"></a>Speciális elemzés a művelet példák
Végpontok közötti Azure Machine Learning forgatókönyvek alkalmazó szoftverbiztonsági a Advanced Analytics folyamat és a nyilvános adatkészleteket használó technológia lásd:

* [Vonja össze az adatokat tudományos folyamat működés közben: SQL Server használatával](sql-walkthrough.md).
* [Vonja össze az adatokat tudományos folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md).

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
