---
title: Azure Machine Learning – csoportos adatelemzési folyamat forgatókönyvei
description: Válassza ki a megfelelő forgatókönyveket a csoportos adatelemzési folyamat fejlett prediktív elemzéséhez.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710491"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Speciális elemzési forgatókönyvek az Azure Machine Learning rendszerben
Ez a cikk a különböző típusú adatforrások és a [csoportos adatelemzési folyamat (TDSP)](overview.md)által kezelhető célzott forgatókönyvek körét ismerteti. A TDSP egy szisztematikus módszert biztosít a csapatok számára, hogy működjenek együtt az intelligens alkalmazások létrehozásán. Az itt bemutatott forgatókönyvek az adatfeldolgozási munkafolyamatban elérhető lehetőségeket illusztrálják, amelyek az Azure-ban az adatok jellemzőitől, a forrás helyeitől és a cél adattáraktól függenek.

Az adatokhoz és a célkitűzéshez megfelelő minta-forgatókönyvek kiválasztásának **döntési fáját** az utolsó szakaszban mutatjuk be.

A következő részekben egy minta forgatókönyvet talál. Az egyes forgatókönyvek esetében a rendszer a lehetséges adatelemzési vagy fejlett elemzési folyamatokat és az Azure-erőforrások támogatását tartalmazza.

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
![Kis-és közepes helyi fájlok][1]

#### <a name="additional-azure-resources-none"></a>További Azure-erőforrások: nincs
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Adatkészlet feltöltése.
1. A feltöltött adatkészlet (ek) től kezdődően hozzon létre egy Azure Machine Learning kísérlet folyamatát.

## <a name="smalllocalprocess"></a>2. forgatókönyv \#: a feldolgozást igénylő helyi fájlok kis és közepes méretű adatkészlete
![Kis-és közepes méretű helyi fájlok feldolgozással][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (IPython notebook Server)
1. Hozzon létre egy IPython notebookot futtató Azure-beli virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure Storage-tárolóból származó adatokhoz való hozzáférés a IPython Notebookban előre feldolgozható és törölhető.
1. Az adatokat egy tisztított táblázatos űrlapra alakíthatja át.
1. Az átalakított adatfájlok mentése az Azure-blobokban.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Az Azure-blobokból származó adatok beolvasása az [adatok importálása][import-data] modul használatával.
1. Egy Azure Machine Learning kísérlet folyamatának létrehozása a betöltött adatkészlet (ek) től kezdve.

## <a name="largelocal"></a>3. forgatókönyv \#: helyi fájlok nagyméretű adatkészlete, Azure-Blobok célzása
![Nagyméretű helyi fájlok][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (IPython notebook Server)
1. Hozzon létre egy IPython notebookot futtató Azure-beli virtuális gépet.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure-blobokból származó adatokhoz való hozzáférés előtt a IPython notebookon megjelenő adatok előfeldolgozása és tisztítása.
1. Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
1. Ismerje meg az adatelemzést, és szükség szerint hozzon létre szolgáltatásokat.
1. Kis-és közepes adatminta kibontása.
1. Mentse a mintául szolgáló adatkészletet az Azure-blobokban.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Az Azure-blobokból származó adatok beolvasása az [adatok importálása][import-data] modul használatával.
1. A betöltött adatkészlet (ek) től kezdve Azure Machine Learning kísérlet folyamatának kiépítése.

## <a name="smalllocaltodb"></a>4. forgatókönyv \#: kis-és közepes méretű adatkészletek helyi fájlokhoz, az Azure-beli virtuális gépeken megcélzott SQL Server
![Kis-és közepes méretű helyi fájlok az Azure-beli SQL-ADATBÁZISba][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (SQL Server/IPython notebook Server)
1. SQL Server + IPython notebookot futtató Azure-beli virtuális gép létrehozása.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Az Azure Storage-tárolóban az IPython notebook használatával előre feldolgozható és törölhető az adat.
1. Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
1. Az adatmentést a virtuális gép helyi fájljaiba (a IPython notebook virtuális gépen fut, a helyi meghajtók a virtuálisgép-meghajtókra vonatkoznak).
1. Az Azure-beli virtuális gépen futó SQL Server adatbázis betöltése.
   
   \#1. lehetőség: SQL Server Management Studio használata.
   
   * Bejelentkezés SQL Server VM
   * SQL Server Management Studio futtatása.
   * Adatbázis és céltábla létrehozása
   * A tömeges importálási módszerek egyikével betöltheti az adatok virtuális helyi fájlokból való betöltését.
   
   \#2. lehetőség: a IPython notebook használata – nem ajánlott közepes és nagyobb adatkészletekhez
   
   <!-- -->    
   * Az ODBC-kapcsolati karakterlánc használatával férhet hozzá SQL Server a virtuális gépen.
   * Adatbázis és céltábla létrehozása
   * A tömeges importálási módszerek egyikével betöltheti az adatok virtuális helyi fájlokból való betöltését.
1. Ismerje meg az adatelemzést, szükség szerint hozzon létre szolgáltatásokat. A funkciók nem szükségesek az adatbázis tábláiban. Csak jegyezze fel a szükséges lekérdezést a létrehozásához.
1. Döntse el, hogy szükség van-e az adatminta méretére, ha szükséges és/vagy kívánatos.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. A betöltött adatkészlet (ek) től kezdve Azure Machine Learning kísérlet folyamatának kiépítése.

## <a name="largelocaltodb"></a>5. forgatókönyv \#: nagyméretű adathalmaz helyi fájlokban, cél SQL Server az Azure-beli virtuális gépen
![Nagyméretű helyi fájlok az Azure-beli SQL-ADATBÁZIShoz][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (SQL Server/IPython notebook Server)
1. SQL Server-és IPython notebook-kiszolgálót futtató Azure-beli virtuális gép létrehozása.
1. Adatok feltöltése egy Azure Storage-tárolóba.
1. Választható Előre feldolgozható és megtisztított adathalmazok.
   
    a.  Az Azure-blobokból származó adatokhoz való hozzáférés előtt a IPython notebookon megjelenő adatok előfeldolgozása és tisztítása.
   
    b.  Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
   
    c.  Az adatmentést a virtuális gép helyi fájljaiba (a IPython notebook virtuális gépen fut, a helyi meghajtók a virtuálisgép-meghajtókra vonatkoznak).
1. Az Azure-beli virtuális gépen futó SQL Server adatbázis betöltése.
   
    a.  Jelentkezzen be SQL Server VMba.
   
    b.  Ha az adatok nem lettek mentve, töltse le az adatfájlokat az Azure Storage-tárolóból a helyi virtuális gép mappájába.
   
    c.  SQL Server Management Studio futtatása.
   
    d.  Adatbázis és céltábla létrehozása
   
    e.  Az betöltéshez használja az egyik tömeges importálási módszert.
   
    f.  Ha táblázat-illesztésekre van szükség, hozzon létre indexeket az illesztések felgyorsításához.
   
   > [!NOTE]
   > A nagyméretű adatméretek gyorsabb betöltéséhez ajánlott particionált táblákat létrehozni és párhuzamosan importálni az adatmennyiséget. További információ: [párhuzamos adatok importálása az SQL particionált táblákba](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Ismerje meg az adatelemzést, szükség szerint hozzon létre szolgáltatásokat. A funkciók nem szükségesek az adatbázis tábláiban. Csak jegyezze fel a szükséges lekérdezést a létrehozásához.
1. Döntse el, hogy szükség van-e az adatminta méretére, ha szükséges és/vagy kívánatos.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Az egyszerű Azure Machine Learning kísérlet folyamata a feltöltött adatkészlet kezdetével

## <a name="largedbtodb"></a>\#6. forgatókönyv: nagy adathalmaz egy SQL Server-adatbázisban a helyszínen, az Azure-beli virtuális gépeken SQL Server célzása
![Nagy méretű SQL-adatbázis az Azure-beli SQL-ADATBÁZIShoz][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (SQL Server/IPython notebook Server)
1. SQL Server-és IPython notebook-kiszolgálót futtató Azure-beli virtuális gép létrehozása.
1. Az adatexportálási módszerek egyikével exportálhatja az SQL Serverról a fájlok kiírására szolgáló adatokból.
   
   > [!NOTE]
   > Ha úgy dönt, hogy áthelyezi az összes adatát a helyszíni adatbázisból, egy alternatív (gyorsabb) metódust helyez át a teljes adatbázist az Azure SQL Server-példányára. Hagyja ki a lépéseket az adatexportáláshoz, az adatbázis létrehozásához, valamint a céladatbázis betöltéséhez/importálásához, és kövesse az alternatív módszert.
   > 
   > 
1. Memóriakép-fájlok feltöltése az Azure Storage-tárolóba.
1. Az Azure-beli virtuális gépen futó SQL Server adatbázisba tölti be az adatgyűjtést.
   
   a.  Jelentkezzen be a SQL Server VMba.
   
   b.  Töltse le az adatfájlokat egy Azure Storage-tárolóból a helyi virtuális gép mappájába.
   
   c.  SQL Server Management Studio futtatása.
   
   d.  Adatbázis és céltábla létrehozása
   
   e.  Az betöltéshez használja az egyik tömeges importálási módszert.
   
   f.  Ha táblázat-illesztésekre van szükség, hozzon létre indexeket az illesztések felgyorsításához.
   
   > [!NOTE]
   > A nagyméretű adatméretek gyorsabb betöltéséhez particionált táblákat kell létrehozni, és párhuzamosan importálni kell az adatmennyiséget. További információ: [párhuzamos adatok importálása az SQL particionált táblákba](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Ismerje meg az adatelemzést, szükség szerint hozzon létre szolgáltatásokat. A funkciók nem szükségesek az adatbázis tábláiban. Csak jegyezze fel a szükséges lekérdezést a létrehozásához.
1. Döntse el, hogy szükség van-e az adatminta méretére, ha szükséges és/vagy kívánatos.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a SQL Server az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Az egyszerű Azure Machine Learning kísérlet folyamata a feltöltött adatkészlettől kezdve.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternatív módszer egy teljes adatbázis másolására helyszíni SQL Serverról Azure SQL Database
![Helyi adatbázis leválasztása és csatolása az Azure-beli SQL-ADATBÁZIShoz][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>További Azure-erőforrások: Azure-beli virtuális gép (SQL Server/IPython notebook Server)
Ha a teljes SQL Server adatbázist szeretné replikálni a SQL Server VMban, akkor az adatbázist az egyik helyről/kiszolgálóról a másikra kell másolni, feltéve, hogy az adatbázis átmenetileg offline állapotba kerül. Használhatja SQL Server Management Studio Object Explorer vagy a megfelelő Transact-SQL-parancsokat.

1. Válassza le az adatbázist a forrás helyén. További információ: [adatbázis leválasztása](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. A Windows Intézőben vagy a Windows parancssorablakban másolja a leválasztott adatbázisfájlt vagy fájlokat, illetve naplófájlokat vagy fájlokat a célhelyre az Azure-beli SQL Server VM.
1. Csatolja a másolt fájlokat a cél SQL Server-példányhoz. További információ: [adatbázis csatolása](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Adatbázis áthelyezése a leválasztás és a csatolás (Transact-SQL) használatával](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>7. forgatókönyv: nagy mennyiségű, helyi fájlokban lévő \#, Azure HDInsight Hadoop-fürtökben található kaptár-adatbázis
![Big-adatmennyiség a helyi cél struktúrában][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>További Azure-erőforrások: Azure HDInsight Hadoop-fürt és az Azure virtuális gép (IPython notebook Server)
1. Hozzon létre egy IPython notebook-kiszolgálót futtató Azure-beli virtuális gépet.
1. Hozzon létre egy Azure HDInsight Hadoop fürtöt.
1. Választható Előre feldolgozható és megtisztított adathalmazok.
   
   a.  Adatok előfeldolgozása és tisztítása IPython-jegyzetfüzetben, adatok elérése az Azure-ból
   
       blobs.
   
   b.  Szükség esetén alakítsa át az adatokat egy tisztított táblázatos űrlapra.
   
   c.  Az adatmentést a virtuális gép helyi fájljaiba (a IPython notebook virtuális gépen fut, a helyi meghajtók a virtuálisgép-meghajtókra vonatkoznak).
1. Töltse fel az adatok a 2. lépésben kiválasztott Hadoop-fürt alapértelmezett tárolójába.
1. Az Azure HDInsight Hadoop-fürtben lévő struktúra-adatbázisba tölti be az adatgyűjtést.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjára.
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Adja meg a kaptár gyökérkönyvtárát a Hadoop parancssorban `cd %hive_home%\bin` parancs használatával.
   
   d.  A kaptár-lekérdezések futtatásával adatbázisokat és táblákat hozhat létre, és betöltheti az adatait a blob Storage-ból a kaptár tábláiba.
   
   > [!NOTE]
   > Ha az adatmennyiség nagy, a felhasználók létrehozhatják a kaptár táblát partíciókkal. Ezt követően a felhasználók használhatnak egy `for` hurkot a Hadoop parancssorában a fő csomóponton, hogy az adatkészletet a partíciók particionált particionálási táblájában tárolja.
   > 
   > 
1. Ismerje meg az adatelemzést, és szükség szerint hozzon létre szolgáltatásokat a Hadoop parancssorban. A funkciók nem szükségesek az adatbázis tábláiban. Csak jegyezze fel a szükséges lekérdezést a létrehozásához.
   
   a.  Jelentkezzen be a Hadoop-fürt fő csomópontjára.
   
   b.  Nyissa meg a Hadoop parancssort.
   
   c.  Adja meg a kaptár gyökérkönyvtárát a Hadoop parancssorban `cd %hive_home%\bin` parancs használatával.
   
   d.  Futtassa a kaptár lekérdezéseit a Hadoop parancssorában a Hadoop-fürt fő csomópontján az adatelemzéshez és a szolgáltatások igény szerinti létrehozásához.
1. Ha szükséges és/vagy szükség van rá, a Azure Machine Learning Studionek megfelelőnek kell lennie.
1. Jelentkezzen be a [Azure Machine learning Studioba](https://studio.azureml.net/).
1. Olvassa el közvetlenül a `Hive Queries` az adatok [importálása][import-data] modul használatával. Illessze be a szükséges lekérdezést, amely kibontja a mezőket, létrehozza a szolgáltatásokat és mintákat, ha szükséges, közvetlenül az [adatimportálási][import-data] lekérdezésben.
1. Az egyszerű Azure Machine Learning kísérlet folyamata a feltöltött adatkészlettől kezdve.

## <a name="decisiontree"></a>Döntési fa a forgatókönyvek kiválasztásához
---
A következő ábra összefoglalja a fent ismertetett forgatókönyveket, valamint a speciális elemzési folyamatokat és a technológia azon lehetőségeit, amelyek az egyes részletezett forgatókönyvekre épülnek. Az adatfeldolgozás, a feltárás, a szolgáltatások és a mintavételezés egy vagy több módszerrel/környezetben, a forrás, a köztes és/vagy a cél környezetekben is elvégezhető, és szükség esetén folytathatja a iteratív. A diagram csak néhány lehetséges folyamat szemléltetését szolgálja, és nem biztosít teljes enumerálást.

![Példa a DS folyamat-forgatókönyvekre][8]

### <a name="advanced-analytics-in-action-examples"></a>Speciális elemzési példák a műveletekre
A fejlett elemzési folyamatot és technológiát használó, teljes körű Azure Machine Learning bemutatókat a nyilvános adatkészletek használatával tekintheti meg:

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
