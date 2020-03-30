---
title: Az Azure Machine Learning forgatókönyveinek azonosítása – Csapatadat-elemzési folyamat
description: Válassza ki a megfelelő forgatókönyveket a csapatadat-elemzési folyamattal végzett speciális prediktív elemzésekhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251621"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Speciális elemzési forgatókönyvek az Azure Machine Learning rendszerben
Ez a cikk a [csoportadat-elemzési folyamat (TDSP)](overview.md)által kezelhető mintaadatforrások és célforgatókönyvek sokféleségét ismerteti. A TDSP szisztematikus megközelítést biztosít a csapatok számára az intelligens alkalmazások kiépítésében való együttműködéshez. Az itt bemutatott forgatókönyvek az adatfeldolgozási munkafolyamatban elérhető lehetőségeket mutatják be, amelyek az azure-beli adatok jellemzőitől, forráshelyétől és céltártól függenek.

A **döntési fa** kiválasztásához a minta forgatókönyvek, amelyek megfelelnek az adatok és a cél az utolsó szakaszban jelenik meg.

A következő szakaszok mindegyike egy mintaforgatókönyvet jelenít meg. Minden forgatókönyv, egy lehetséges adatelemzési vagy speciális elemzési folyamat, és támogatja az Azure-erőforrások listája.

> [!NOTE]
> **Az alábbi esetekmindegyikéhez a következőket kell megteteni:**
> <br/>
> 
> * [Tárfiók létrehozása](../../storage/common/storage-account-create.md)
>   <br/>
> * [Azure Machine Learning-munkaterület létrehozása](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>1. forgatókönyv: \#Kis- és közepes méretű táblázatos adatkészlet helyi fájlokban
![Kis és közepes helyi fájlok][1]

#### <a name="additional-azure-resources-none"></a>További Azure-erőforrások: Nincs
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Adatkészlet feltöltése.
1. Azure Machine Learning-kísérletfolyamat létrehozása a feltöltött adatkészletekkel kezdve.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>2. forgatókönyv: \#Feldolgozást igénylő helyi fájlok kis- és közepes méretű adatkészlete
![Kis és közepes méretű helyi fájlok feldolgozással][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython notebook-kiszolgáló)
1. Hozzon létre egy IPython-jegyzetfüzetet futtató Azure virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az IPython-jegyzetfüzetben az adatok előzetes feldolgozása és leépítése az Azure Storage-tárolóból való hozzáféréssel.
1. Az adatok átalakítása megtisztított táblázatos formává.
1. Az átalakított adatokmentése az Azure-blobokban.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Az [Adatok importálása][import-data] modul használatával olvassa el az Azure-blobok adatait.
1. Azure Machine Learning-kísérletfolyamat létrehozása a bevitt adatkészletekkel kezdve.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>3. forgatókönyv: \#Helyi fájlok nagy adatkészlete, az Azure Blobok célzása
![Nagy méretű helyi fájlok][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure virtuális gép (IPython notebook-kiszolgáló)
1. Hozzon létre egy IPython-jegyzetfüzetet futtató Azure virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az IPython-jegyzetfüzetben előre feldolgozható és tiszta adatok, az Azure-blobokból származó adatok elérése.
1. Szükség esetén alakítsa át az adatokat megtisztított táblázatos formává.
1. Fedezze fel az adatokat, és szükség szerint hozzon létre funkciókat.
1. Kis-és közepes méretű adatminta kinyerése.
1. Mentse a mintavételezett adatokat az Azure-blobokban.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Az [Adatok importálása][import-data] modul használatával olvassa el az Azure-blobok adatait.
1. Azure Machine Learning-kísérlet folyamata a bevitt adatkészlet(ek) kezdő.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>4. forgatókönyv: \#Helyi fájlok kis- és közepes adatkészlete, amely az SQL Servert célozza meg egy Azure virtuális gépen
![Kis és közepes helyi fájlok sql db-ra az Azure-ban][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure Virtual Machine (SQL Server / IPython notebook-kiszolgáló)
1. Hozzon létre egy SQL Server + IPython notebook szolgáltatást futtató Azure virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure Storage-tárolóban az IPython-jegyzetfüzet használatával elődolgozás és tiszta adatok.
1. Szükség esetén alakítsa át az adatokat megtisztított táblázatos formává.
1. Adatok mentése vm-en ként megadott fájlokba (az IPython Notebook virtuális gépen fut, a helyi meghajtók virtuálisgép-meghajtókra hivatkoznak).
1. Adatok betöltése az Azure-beli virtuális gépen futó SQL Server-adatbázisba.
   
   1. lehetőség: \#Az SQL Server Management Studio használata.
   
   * Bejelentkezés az SQL Server virtuális gépbe
   * Futtassa az SQL Server Management Studio alkalmazást.
   * Hozzon létre adatbázis- és céltáblákat.
   * Használja az egyik tömeges importálási módszerek betöltése az adatokat a virtuális gép-helyi fájlokat.
   
   2. lehetőség: \#Az IPython Notebook használata – nem ajánlott közepes és nagyobb adatkészletekhez
   
   <!-- -->    
   * Az ODBC kapcsolati karakterlánc használatával érheti el az SQL Server t a virtuális gépen.
   * Hozzon létre adatbázis- és céltáblákat.
   * Használja az egyik tömeges importálási módszerek betöltése az adatokat a virtuális gép-helyi fájlokat.
1. Fedezze fel az adatokat, hozzon létre funkciókat, ha szükséges. A szolgáltatásokat nem kell az adatbázistáblákban materializálni. Csak vegye figyelembe a létrehozásukhoz szükséges lekérdezést.
1. Szükség és/vagy kívánt adatminta méretének eldöntése.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Olvassa be az adatokat közvetlenül az SQL Serverből az [Adatok importálása][import-data] modul segítségével. Illessze be a szükséges lekérdezést, amely mezőket bont ki, szolgáltatásokat hoz létre, és szükség esetén adatokat mintavételez közvetlenül az [Adatok importálása][import-data] lekérdezésbe.
1. Azure Machine Learning-kísérlet folyamata a bevitt adatkészlet(ek) kezdő.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>5. forgatókönyv: \#Nagy adatkészlet helyi fájlokban, sql server célzás az Azure VM-ben
![Nagy méretű helyi fájlok az SQL DB-hez az Azure-ban][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure Virtual Machine (SQL Server / IPython notebook-kiszolgáló)
1. Hozzon létre egy SQL Server és IPython notebook-kiszolgálót futtató Azure virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. (Nem kötelező) Előfeldolgozás és tiszta adatok.
   
    a.  Az IPython-jegyzetfüzetben előre feldolgozható és tiszta adatok, az Azure-blobokból származó adatok elérése.
   
    b.  Szükség esetén alakítsa át az adatokat megtisztított táblázatos formává.
   
    c.  Adatok mentése vm-en ként megadott fájlokba (az IPython Notebook virtuális gépen fut, a helyi meghajtók virtuálisgép-meghajtókra hivatkoznak).
1. Adatok betöltése az Azure-beli virtuális gépen futó SQL Server-adatbázisba.
   
    a.  Jelentkezzen be az SQL Server virtuális gépbe.
   
    b.  Ha az adatok még nem mentve, töltse le az adatokat az Azure storage tárolóból a helyi virtuális gép mappába.
   
    c.  Futtassa az SQL Server Management Studio alkalmazást.
   
    d.  Hozzon létre adatbázis- és céltáblákat.
   
    e.  Az adatok betöltéséhez használja a tömeges importálási módszerek egyikét.
   
    f.  Ha táblaillesztések szükségesek, hozzon létre indexeket az illesztések felgyorsítása érdekében.
   
   > [!NOTE]
   > A nagy adatméretek gyorsabb betöltése érdekében ajánlott particionált táblákat létrehozni, és az adatokat párhuzamosan tömegesen importálni. További információt a [Párhuzamos adatok importálása SQL particionált táblákba című témakörben talál.](parallel-load-sql-partitioned-tables.md)
   > 
   > 
1. Fedezze fel az adatokat, hozzon létre funkciókat, ha szükséges. A szolgáltatásokat nem kell az adatbázistáblákban materializálni. Csak vegye figyelembe a létrehozásukhoz szükséges lekérdezést.
1. Szükség és/vagy kívánt adatminta méretének eldöntése.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Olvassa be az adatokat közvetlenül az SQL Serverből az [Adatok importálása][import-data] modul segítségével. Illessze be a szükséges lekérdezést, amely mezőket bont ki, szolgáltatásokat hoz létre, és szükség esetén adatokat mintavételez közvetlenül az [Adatok importálása][import-data] lekérdezésbe.
1. Egyszerű Azure Machine Learning-kísérletfolyamat a feltöltött adatkészlettel kezdve

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>6. forgatókönyv: \#Nagy adatkészlet egy helyszíni SQL Server-adatbázisban, az SQL Server célzása egy Azure virtuális gépen
![Nagy SQL DB az SQL DB-hoz az Azure-ban][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure Virtual Machine (SQL Server / IPython notebook-kiszolgáló)
1. Hozzon létre egy SQL Server és IPython notebook-kiszolgálót futtató Azure virtuális gépet.
1. Az adatok SQL Server kiszolgálóról történő exportálásához használja az adatexportálásegyikmódszerét a fájlok kiírásához.
   
   > [!NOTE]
   > Ha úgy dönt, hogy az összes adatot áthelyezi a helyszíni adatbázisból, egy másik (gyorsabb) módszerrel helyezze át a teljes adatbázist az SQL Server-példányaz Azure-ban. Hagyja ki az adatok exportálásának, az adatbázis létrehozásának, valamint az adatok nak a céladatbázisba való be- és importálásának lépéseit, és kövesse az alternatív módszert.
   > 
   > 
1. Memóriakép fájlok feltöltése az Azure Storage tárolóba.
1. Töltse be az adatokat egy Azure virtuális gépen futó SQL Server-adatbázisba.
   
   a.  Jelentkezzen be az SQL Server virtuális gépbe.
   
   b.  Adatfájlok letöltése egy Azure Storage-tárolóból a helyi virtuális gép mappába.
   
   c.  Futtassa az SQL Server Management Studio alkalmazást.
   
   d.  Hozzon létre adatbázis- és céltáblákat.
   
   e.  Az adatok betöltéséhez használja a tömeges importálási módszerek egyikét.
   
   f.  Ha táblaillesztések szükségesek, hozzon létre indexeket az illesztések felgyorsítása érdekében.
   
   > [!NOTE]
   > A nagy adatméretek gyorsabb betöltése érdekében hozzon létre particionált táblákat, és tömegesen importálja az adatokat párhuzamosan. További információt a [Párhuzamos adatok importálása SQL particionált táblákba című témakörben talál.](parallel-load-sql-partitioned-tables.md)
   > 
   > 
1. Fedezze fel az adatokat, hozzon létre funkciókat, ha szükséges. A szolgáltatásokat nem kell az adatbázistáblákban materializálni. Csak vegye figyelembe a létrehozásukhoz szükséges lekérdezést.
1. Szükség és/vagy kívánt adatminta méretének eldöntése.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Olvassa be az adatokat közvetlenül az SQL Serverből az [Adatok importálása][import-data] modul segítségével. Illessze be a szükséges lekérdezést, amely mezőket bont ki, szolgáltatásokat hoz létre, és szükség esetén adatokat mintavételez közvetlenül az [Adatok importálása][import-data] lekérdezésbe.
1. Egyszerű Azure Machine Learning-kísérlet folyamat a feltöltött adatkészlettel kezdve.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>A teljes adatbázis helyszíni SQL Server kiszolgálóról az Azure SQL Database-be történő másolásának alternatív módja
![A helyi adatbázis leválasztása és csatolása az SQL DB-hez az Azure-ban][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure Virtual Machine (SQL Server / IPython notebook-kiszolgáló)
A teljes SQL Server-adatbázis sql Server virtuális gépben történő replikálásához az adatbázist egyik helyről/kiszolgálóról a másikra kell másolnia, feltéve, hogy az adatbázis ideiglenesen offline állapotba helyezhető. Használhatja az SQL Server Management Studio Object Explorert, vagy használhatja az ezzel egyenértékű Transact-SQL parancsokat.

1. Válassza le az adatbázist a forráshelyen. További információt az [Adatbázis leválasztása](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)című témakörben talál.
1. A Windows Intézőben vagy a Windows parancssori ablakban másolja a leválasztott adatbázisfájlt vagy -fájlokat, illetve a naplófájlt vagy -fájlokat az Azure-beli SQL Server virtuális gép célhelyére.
1. Csatolja a másolt fájlokat a cél SQL Server-példányhoz. További információt az [Adatbázis csatolása](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)című témakörben talál.

[Adatbázis áthelyezése leválasztással és csatolással (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>7. forgatókönyv: \#Helyi fájlokban lévő big data, Hive-adatbázis célzása az Azure HDInsight Hadoop-fürtökben
![Big data a helyi célhive-ban][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure HDInsight Hadoop cluster és Azure Virtual Machine (IPython notebook-kiszolgáló)
1. Hozzon létre egy IPython notebook-kiszolgálót futtató Azure virtuális gépet.
1. Hozzon létre egy Azure HDInsight Hadoop-fürtöt.
1. (Nem kötelező) Előfeldolgozás és tiszta adatok.
   
   a.  Adatok feldolgozása és letisztítása az IPython-jegyzetfüzetben, az Azure-ból származó adatok elérése
   
       blobs.
   
   b.  Szükség esetén alakítsa át az adatokat megtisztított táblázatos formává.
   
   c.  Adatok mentése vm-en ként megadott fájlokba (az IPython Notebook virtuális gépen fut, a helyi meghajtók virtuálisgép-meghajtókra hivatkoznak).
1. Adatok feltöltése a 2.
1. Adatok betöltése a Hive-adatbázisba az Azure HDInsight Hadoop-fürtben.
   
   a.  Bejelentkezés a Hadoop-fürt főcsomópontjára
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Írja be a Hive `cd %hive_home%\bin` gyökérkönyvtárát a Hadoop parancssorába parancssal.
   
   d.  Futtassa a Hive-lekérdezéseket adatbázis és táblák létrehozásához, és töltse be az adatokat a blobstorage-ból a Hive-táblákba.
   
   > [!NOTE]
   > Ha az adatok nagyok, a felhasználók létrehozhatják a Hive-táblát partíciókkal. Ezután a felhasználók `for` a főcsomópont Hadoop parancssorában lévő hurkot használhatnak az adatok betöltéséhez a partíció által particionált Hive-táblába.
   > 
   > 
1. Fedezze fel az adatokat, és hozzon létre funkciókat szükség szerint a Hadoop parancssorában. A szolgáltatásokat nem kell az adatbázistáblákban materializálni. Csak vegye figyelembe a létrehozásukhoz szükséges lekérdezést.
   
   a.  Bejelentkezés a Hadoop-fürt főcsomópontjára
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Írja be a Hive `cd %hive_home%\bin` gyökérkönyvtárát a Hadoop parancssorába parancssal.
   
   d.  Futtassa a Hive-lekérdezéseket a Hadoop parancssorában a Hadoop-fürt főcsomópontján az adatok feltárásához és szükség szerint hozzon létre szolgáltatásokat.
1. Szükség esetén és/vagy kívánt módon, az Azure Machine Learning Studio-ban való elférő adatokból.
1. Jelentkezzen be az [Azure Machine Learning Studio-ba.](https://studio.azureml.net/)
1. Olvassa be az `Hive Queries` adatokat közvetlenül az [Adatok importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely mezőket bont ki, szolgáltatásokat hoz létre, és szükség esetén adatokat mintavételez közvetlenül az [Adatok importálása][import-data] lekérdezésbe.
1. Egyszerű Azure Machine Learning-kísérlet folyamat a feltöltött adatkészlettel kezdve.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Döntési fa a forgatókönyv kiválasztásához
---
Az alábbi ábra összefoglalja a fent leírt forgatókönyveket, valamint a speciális elemzési folyamat és technológia által hozott döntéseket, amelyek az egyes tételes forgatókönyvekhez visznek. Az adatfeldolgozás, a feltárás, a szolgáltatástervezés és a mintavételezés egy vagy több módszerben/környezetben történhet – a forrás-, köztes és/vagy célkörnyezetekben –, és szükség szerint iteratív módon folytathatja. Az ábra csak néhány lehetséges folyamat szemléltetéseként szolgál, és nem nyújt teljes körű felsorolást.

![Minta DS folyamat forgatókönyvek][8]

### <a name="advanced-analytics-in-action-examples"></a>Speciális analitika akcióban példák
A részletes encikett adatkészleteket használó Azure Machine Learning-forgatókönyveket a következő témakörökben láthatja:

* [Csapatadatelemzési folyamat működés közben: az SQL Server használata](sql-walkthrough.md).
* [Csapatadat-elemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md).

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
