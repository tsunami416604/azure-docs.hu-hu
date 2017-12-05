---
title: "Adatok importálása a Machine Learning Studio online adatforrásokból |} Microsoft Docs"
description: "A betanítási adatok Azure Machine Learning Studio különböző online forrásokból importálásának módját."
keywords: "adatok, adatformátum, adattípusok, adatforrások, betanítási adatok importálása"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 701b93fe-765b-4d15-a1cf-9b607f17add6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: 4c699a8e5a9fafa0fec10bcb731f9ba533e3d283
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>Adatok importálása az Azure Machine Learning Studióba különböző online adatforrásokból az Adatok importálása modullal
Ez a cikk ismerteti a támogatás online adatimportálási különböző forrásokból és a források tárolt adatok mozgatása az Azure Machine Learning kísérlet szükséges információkat.

> [!NOTE]
> Ez a cikk általános információkat tartalmaz a [és adatokat importálhat] [ import-data] modul. Részletesebb információ a típusú adatokhoz próbál hozzáférni, formátumok, a paraméterek és a gyakori kérdésekre adott válaszok tekintse meg a modul referencia-témakör a [és adatokat importálhat] [ import-data] modul.
> 
> 

<!-- -->

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>Bevezetés
Használatával a [és adatokat importálhat] [ import-data] modulban lehet elérni az adatokat több online adatok forrásokból származó kísérletbe futása [Azure Machine Learning Studio](https://studio.azureml.net/Home):

* Egy webes URL-Címéhez HTTP-n keresztül
* Hadoop HiveQL használatával
* Azure Blob Storage
* Azure-tábla
* Az Azure SQL database vagy az SQL Server Azure virtuális gépen
* A helyszíni SQL Server-adatbázis
* Egy adatcsatornát jelenleg OData-szolgáltató
* Az Azure CosmosDB (korábbi nevén DocumentDB)

A Studio kísérletben online adatforrások eléréséhez vegye fel a [és adatokat importálhat] [ import-data] modult a, válassza ki a **adatforrás**, és adja meg a hozzáféréshez szükséges paraméterek az adatokat. Az alábbi táblázat a támogatott online adatforrások van felsorolva. A táblázat összefoglalja, a támogatott formátumok és adatok eléréséhez használt paraméterek is.

Vegye figyelembe, hogy a betanítási adatok kísérletbe futása közben érhető el, mert az csak érhető el, hogy a kísérletben. Képest egy adatkészlet modulban tárolt adatok érhetők el a kísérletvászonra a munkaterületen.

> [!IMPORTANT]
> Jelenleg a [és adatokat importálhat] [ import-data] és [adatok exportálása] [ export-data] modulok olvashatja, és csak az létre a klasszikus Azure storage adatok írása üzembe helyezési modellben. Ez azt jelenti az új Azure Blob Storage fiók típusa egy gyakran használt adatok hozzáférési rétege vagy a ritkán használt adatok hozzáférési rétege által jelenleg nem támogatott. 
> 
> Általában minden Azure storage-fiókok, hogy előfordulhat, hogy létrehozta előtt a service kapcsoló váltak elérhetővé nem befolyásolja. 
> Ha új fiók létrehozásához szükséges, válassza ki **klasszikus** a telepítési modell, vagy erőforrás-kezelővel, és válassza ki **általános célú** helyett **Blob-tároló** a **Fiók kind**. 
> 
> További információkért lásd: [Azure Blob Storage: gyakran és ritkán használt adatok tárolási rétegek](../../storage/blobs/storage-blob-storage-tiers.md).
> 
> 

## <a name="supported-online-data-sources"></a>Támogatott online adatforrások
Az Azure Machine Learning **és adatokat importálhat** modul támogatja a következő adatforrások:

| Adatforrás | Leírás | Paraméterek |
| --- | --- | --- |
| A HTTP Protokollon keresztül, a webhely URL-címe |Olvassa be az adatokat vesszővel tagolt (CSV), a lapon elválasztott értékeket (TSV), a attribútum-relációs fájlformátumra (ARFF) és a támogatási vektoros gépek (SVM-világos) formátumban, minden webes URL-Címéhez HTTP Protokollt használó |<b>URL-cím</b>: a fájl, beleértve a webhely URL-címe és a fájl nevét, kiterjesztés teljes nevét adja meg. <br/><br/><b>Az adatformátum</b>: határozza meg, a támogatott adatok egy formátumok: CSV, TSV, ARFF vagy SVM-világos. Ha az adatokat a fejlécsor, oszlopnevek hozzárendelni szolgál. |
| Hadoop/HDFS |A Hadoop elosztott tárolókban olvassa be az adatokat. Megadja a HiveQL, egy SQL-szerű lekérdező nyelv használatával kívánt adatokat. HiveQL összesíteni az adatokat, és végezze el az adatok szűrése, csak az adatokat hozzáadni a Machine Learning Studio használatával is lehet. |<b>Adatbázis-lekérdezés Hive</b>: Adja meg a Hive lekérdezés létrehozza az adatokat.<br/><br/><b>HCatalog kiszolgáló URI azonosítója </b> : a következő formátumban a fürt nevét megadva  *&lt;a fürt neve&gt;. azurehdinsight.net.*<br/><br/><b>Hadoop felhasználói fiók nevét</b>: a fürt létrehozásához használt Hadoop-fiók felhasználónevét határozza meg.<br/><br/><b>Hadoop felhasználói fiók jelszavát</b> : Adja meg a fürt létesítésekor használt hitelesítő adatokat. További információkért lásd: [Hadoop létrehozása a HDInsight-fürtök](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Az kimeneti adatok</b>: meghatározza, hogy az adatok tárolását, a Hadoop elosztott fájlrendszer (HDFS) vagy az Azure-ban. <br/><ul>Ha a kimeneti adatok HDFS-ben vannak tárolva, adja meg a HDFS kiszolgáló URI azonosítója. (Ügyeljen arra, hogy használja a HDInsight-fürt nevét a HTTPS:// előtag nélkül). <br/><br/>Ha a kimeneti adatok az Azure-ban tárolja, adjon meg, hogy az az Azure storage-fiók neve, a tárelérési kulcs és a tárolási tároló nevét.</ul> |
| SQL-adatbázis |Olvassa be az Azure SQL adatbázis vagy egy Azure virtuális gépen futó SQL Server-adatbázisban tárolt adatokat. |<b>Adatbázis-kiszolgáló neve</b>: a kiszolgáló, amelyen fut az adatbázis neve.<br/><ul>Az Azure SQL Database esetén adja meg a létrehozott kiszolgáló nevét. Általában az űrlap rendelkezik  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Adjon meg egy Azure virtuális gépeken futó SQL server esetén *tcp:&lt;virtuális gép DNS-név&gt;, 1433*</ul><br/><b>Az adatbázisnév </b>: az adatbázis nevét adja meg a kiszolgálón. <br/><br/><b>Felhasználói fiók kiszolgálónév</b>: a hozzáférési engedélyekkel rendelkezik az adatbázisban a fiók felhasználónevét határozza meg. <br/><br/><b>Kiszolgáló felhasználói fiók jelszavát</b>: a felhasználói fiók jelszavát adja meg.<br/><br/><b>Adatbázis-lekérdezés</b>: Adjon meg egy SQL-utasítást, amely leírja azokat az olvasni kívánt adatokat. |
| Helyszíni SQL Database adatbázis |A helyszíni SQL-adatbázisban tárolt adatok beolvasása. |<b>Az átjáró</b>: az adatkezelési átjáró egy számítógépre, ahol az SQL Server adatbázis férhetnek telepített nevét adja meg. Az átjáró beállításával kapcsolatos információkért lásd: [advanced analytics egy helyszíni SQL server adatait használó Azure Machine Learning segítségével végezze el](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Adatbázis-kiszolgáló neve</b>: a kiszolgáló, amelyen fut az adatbázis neve.<br/><br/><b>Az adatbázisnév </b>: az adatbázis nevét adja meg a kiszolgálón. <br/><br/><b>Felhasználói fiók kiszolgálónév</b>: a hozzáférési engedélyekkel rendelkezik az adatbázisban a fiók felhasználónevét határozza meg. <br/><br/><b>Felhasználónév és jelszó</b>: kattintson a <b>adja meg az értékeket</b> adatbázis hitelesítő adatait. Használhatja az integrált Windows-hitelesítést vagy SQL Server-hitelesítés a helyszíni SQL Server konfigurációtól függően.<br/><br/><b>Adatbázis-lekérdezés</b>: Adjon meg egy SQL-utasítást, amely leírja azokat az olvasni kívánt adatokat. |
| Azure-tábla |A Table szolgáltatás az Azure Storage olvassa be az adatokat.<br/><br/>Ha nagy mennyiségű adat ritkán olvasni, használja az Azure Table szolgáltatás. Biztosít egy rugalmas, nem relációs (NoSQL), nagymértékben méretezhető, alacsony költségű és magas rendelkezésre állású tárolási megoldást. |A beállítások a **és adatokat importálhat** attól függően, hogy nyilvános információt vagy a privát storage-fiók bejelentkezési hitelesítő adatokat igénylő elérése módosítása. Ez határozza meg a <b>hitelesítési típus</b> amelyben lehetnek értéke "PublicOrSAS" vagy "Fiók", amelyek mindegyikének saját beállítása olyan paraméterek összessége. <br/><br/><b>Nyilvános vagy közös hozzáférésű Jogosultságkód (SAS) URI</b>: paraméterei:<br/><br/><ul><b>Tábla URI</b>: nyilvános vagy SAS URL-címet adja meg a következő táblázatban.<br/><br/><b>Meghatározza a sorok szolgáltatást a tulajdonságnevek</b>: az értékek a következők <i>TopN</i> megvizsgálja a megadott számú sort, vagy <i>ScanAll</i> a tábla összes sorát eléréséhez. <br/><br/>Ha az adatok homogén és előre jelezhető, javasoljuk, hogy kiválassza *TopN* és N. meg Nagy táblák Ez gyorsabb olvasási idő eredményezhet.<br/><br/>Ha a mélység függően változhat a tulajdonságok beállítása és a helyére a táblázat felépítése az adatokat, válassza ki azt a *ScanAll* beállítás megvizsgálja az összes sort. Ez biztosítja, hogy az eredményül kapott tulajdonság és a metaadatok átalakítás integritása.<br/><br/></ul><b>Saját Tárfiók</b>: paraméterei: <br/><br/><ul><b>Fióknév</b>: a fiók, amely tartalmazza a tábla olvasni nevét adja meg.<br/><br/><b>A fiókkulcsot</b>: Adja meg a fiókhoz tartozó kulcsot.<br/><br/><b>Táblanév</b> : olvassa el az adatokat tartalmazó tábla neve.<br/><br/><b>A tulajdonságnevek vizsgálatához sorok</b>: az értékek a következők <i>TopN</i> megvizsgálja a megadott számú sort, vagy <i>ScanAll</i> a tábla összes sorát eléréséhez.<br/><br/>Ha az adatok homogén és előre jelezhető, azt javasoljuk, hogy kiválassza *TopN* és N. meg Nagy táblák Ez gyorsabb olvasási idő eredményezhet.<br/><br/>Ha a mélység függően változhat a tulajdonságok beállítása és a helyére a táblázat felépítése az adatokat, válassza ki azt a *ScanAll* beállítás megvizsgálja az összes sort. Ez biztosítja, hogy az eredményül kapott tulajdonság és a metaadatok átalakítás integritása.<br/><br/> |
| Azure Blob Storage |A Blob szolgáltatás az Azure Storage, beleértve a képeket, strukturálatlan szöveges vagy bináris adatok tárolt adatokat olvas.<br/><br/>A Blob szolgáltatással nyilvánosan elérhetővé az adatokat, vagy közvetlenül a Microsoftnak az alkalmazás adatainak tárolásához. Hozzáférhet az adatokhoz, bárhonnan HTTP vagy HTTPS-kapcsolatokon keresztül. |A beállítások a **és adatokat importálhat** modul módosítás attól függően, hogy nyilvános információt vagy a privát storage-fiók bejelentkezési hitelesítő adatokat igénylő elérésére. Ez határozza meg a <b>hitelesítési típus</b> amely "PublicOrSAS" vagy "Fiók" értéke lehet.<br/><br/><b>Nyilvános vagy közös hozzáférésű Jogosultságkód (SAS) URI</b>: paraméterei:<br/><br/><ul><b>URI</b>: nyilvános vagy SAS URL-címet adja meg a tárolási BLOB.<br/><br/><b>Fájlformátum</b>: Adja meg az adatok formátumának a Blob szolgáltatás. A támogatott formátumok a következők: CSV, TSV és ARFF.<br/><br/></ul><b>Saját Tárfiók</b>: paraméterei: <br/><br/><ul><b>Fióknév</b>: a fiók, amely tartalmazza az olvasni kívánt blob neve.<br/><br/><b>A fiókkulcsot</b>: Adja meg a fiókhoz tartozó kulcsot.<br/><br/><b>Tároló, a könyvtár vagy a blob elérési </b> : olvassa el az adatokat tartalmazó blob neve.<br/><br/><b>A BLOB Formátum</b>: Adja meg az adatok formátumának a blob szolgáltatás. A támogatott adatobjektum-formátumok a következők CSV, TSV, ARFF, fürt megosztott kötetei szolgáltatás a megadott kódolás és Excel. <br/><br/><ul>Ha a formátum CSV vagy TSV, ügyeljen arra, hogy azt jelzi, hogy a fájl tartalmazza-e a fejlécsor.<br/><br/>Az Excel-beállítás segítségével adatokat olvasni az Excel-munkafüzetekhez. Az a <i>Excel adatformátum</i> lehetőséget, adja meg, van-e az adatok a egy Excel-munkalap esik, vagy egy Excel-táblázatban. Az a <i>Excel-táblában vagy beágyazott tábla </i>lehetőségre, adja meg a lap vagy az olvasni kívánt tábla nevét.</ul><br/> |
| Adatcsatorna-szolgáltató |A támogatott adatcsatorna szolgáltató olvassa be az adatokat. Jelenleg csak a Megnyitás Data (OData) protokollnak formátum támogatott. |<b>Adatok tartalomtípus</b>: meghatározza az OData-formátumot.<br/><br/><b>Forrás URL-címe</b>: Adja meg a teljes adatcsatorna URL-címet. <br/>Például a következő URL-cím olvassa be az adatbázisunk: http://services.odata.org/northwind/northwind.svc/ |

## <a name="next-steps"></a>Következő lépések

[Adatok importálása és az adatok exportálása modulokat használó Azure ML web szolgáltatások telepítése](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
