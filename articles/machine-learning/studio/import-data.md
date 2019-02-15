---
title: Különféle adatforrásokból származó adatok importálása
titleSuffix: Azure Machine Learning Studio
description: Adatok importálása az Azure Machine Learning Studióba különböző adatforrásokból származó adatokat hogyan. Ismerje meg, milyen típusú adatokat és adatformátumok a célnyelven támogatottak.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: bbae6d4b727f3e3dc51bd57e8badbc6e87814a51
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267886"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>A betanítási adatok importálása az Azure Machine Learning Studióba különböző adatforrásokból

A Machine Learning Studio a saját adatok használatával fejlesztése és betanítunk egy prediktív elemzési megoldás, használhatja az adatokat: 

* **Helyi fájl** -időben helyi adatok betöltése a merevlemezen a munkaterületen lévő adatkészlet modul létrehozása
* **Online adatforrásokból** -használatát a [adatok importálása] [ import-data] kísérletét futása közben számos online forrásokból származó adatokhoz való hozzáférésének modul
* **A Machine Learning Studióban végrehajtott kísérletekhez** – a Machine Learning Studióban adatkészletként mentett adatok használata
* [**A helyszíni SQL Server-adatbázis** ](use-data-from-an-on-premises-sql-server.md) – nem kell manuálisan másolja az adatokat egy helyszíni SQL Server-adatbázisból származó adatok használata

> [!NOTE]
> Nincsenek mintaadatkészletek számos Machine Learning studióban, amely a betanítási adatok is használhat. Ezek a további információkért lásd: [az Azure Machine Learning Studio mintaadatkészleteinek használata](use-sample-datasets.md).

## <a name="prepare-data"></a>Adatok előkészítése

A Machine Learning Studio téglalap alakú vagy táblázatos adatok, például a strukturált adatok egy adatbázisból, azonban bizonyos körülmények között nem téglalap adatok használhatók vagy tagolt szöveges adat tervezték.

A legjobb, ha az adatok viszonylag tiszta studióba importálása előtt. Például érdemes például nem jegyzett karakterláncok gondoskodik.

Azonban nincsenek modulok elérhető a Studióban, amelyek lehetővé teszik az adatok a kísérleten belülről néhány kezeléséhez, az adatok importálása után. Attól függően, gépi tanulási algoritmusok fogja használni, szükség lehet annak eldöntése, hogyan fogja kezelni adatok szerkezeti problémák, például a hiányzó értékeket és a ritka adatokhoz, és vannak, amelyek segíthetnek, hogy a modulok. Tekintse meg a **adatátalakítás** a modulpaletta végre ezeket a funkciókat modulokba vonatkozó szakaszában.

A kísérlet során bármikor megtekintheti, és töltse le a kimeneti portra kattintva egy modul által előállított adatok. Attól függően, a modul lehet különböző letöltési beállítások használható, vagy előfordulhat, hogy a jeleníthetik meg az adatokat a Studióban webböngészőből.

## <a name="supported-data-formats-and-data-types"></a>Támogatott formátumok és -adattípusok

Különféle típusú adatokat importálhat is futtathatja a kísérletet, attól függően, hogy milyen mechanizmussal importálni az adatokat, és azt forrását használja:

* Egyszerű szövegfájlt (.txt)
* Vesszővel tagolt (CSV) egy fejlécet (tartalmazó.csv) vagy anélkül (. nh.csv)
* Tabulátorral tagolt értékeket (TSV) fejléc (.tsv) vagy anélkül (. nh.tsv)
* Excel-fájl
* Azure-tábla
* Hive-tábla
* SQL Database tábla
* OData-értékek
* SVMLight adatok (.svmlight) (lásd a [SVMLight definíció](http://svmlight.joachims.org/) formátum információ)
* Attribútum-relációs fájlformátumra (ARFF) adatokat (.arff) (lásd a [ARFF definíció](http://weka.wikispaces.com/ARFF) formátum információ)
* Zip-fájl (.zip)
* R-objektum vagy a munkaterület fájlt (. Rekordadat)

Ha például a metaadatokat tartalmazó ARFF formátumú adatokat importál, a Studio ezeket a metaadatokat használja a fejlécet és az egyes oszlopok adattípusát meghatározásához.

Ha importálja az adatokat, például a TSV- vagy CSV formátum, amely nem tartalmazza ezeket a metaadatokat, Studio kikövetkezteti minden oszlop adattípusát mintavétellel az adatokat. Ha az adatok nem rendelkezik oszlopfejlécekkel, a Studio alapértelmezett nevek biztosít.

Explicit módon adja meg vagy módosítsa a fejlécek és adattípusok használatával oszlopok esetében a [metaadatainak szerkesztése] [ edit-metadata] modul.

A következő adattípusokat felismeri a Studio:

* String
* Egész szám
* Double
* Logikai
* DateTime
* TimeSpan

Studio nevű belső adattípust használ ***adattábla*** adatok átadására a modulok között. Az adatok table formátum használatával az adatok explicit módon alakíthatja a [adatkészlet átalakítása] [ convert-to-dataset] modul.

Adattábla eltérő formátumokban fogad modulok csendes átalakítja az adatokhoz táblázat mielőtt továbbítaná a következő modul számára.

Szükség esetén átválthat visszaimportálni CSV, TSV, ARFF adatok táblázatos formában vagy más átalakítási modulok SVMLight formátumú.
Tekintse meg a **formátum Adatátalakítókat** a modulpaletta végre ezeket a funkciókat modulokba vonatkozó szakaszában.

## <a name="data-capacities"></a>Adatkapacitással

A Machine Learning Studióban található modulok 10 GB-nyi számadatot tartalmazó adatkészletet támogatnak a gyakori alkalmazási esetekben. Ha egy modul egynél több bemenetből fogad adatokat, a bemenet összesített mérete nem haladhatja meg a 10 GB-ot. Is mintát venni a nagyobb adatkészletekből a Hive vagy az Azure SQL Database lekérdezések használatával, vagy használhatja a Learning by Counts alkalmazásával feldolgozza azokat, mielőtt importálja az adatokat.  

A szolgáltatásnormalizálás során a következő, 10 GB alá korlátozott adattípusok bővíthetők nagyobb adatkészletekké:

* Ritka
* Kategorikus
* Sztringek
* Bináris adatok

A következő modulok 10 GB-nál kisebb adatkészletekre vannak korlátozva:

* Ajánló modulok
* SMOTE (Synthetic Minority Oversampling Technique) modul
* Parancsfájlkezelési modulok: R, Python, SQL
* Olyan modulok, amelyeknél a kimeneti adatok mérete meghaladhatja a bemeneti adatok méretét; például az egyesítés vagy a szolgáltatáskivonatolás
* Kereszt-ellenőrzés, modell-hiperparaméterek beállítása, sorszámregresszió és multi-osztályú osztályozás nagyszámú ismétlés esetében

A néhány GB-nál nagyobb adatkészletek esetében az adatfeltöltés az Azure Storage vagy az Azure SQL Database, vagy használja az Azure HDInsight ahelyett, hogy közvetlenül a helyi fájl feltöltése.

Rendszerkép-adatok tájékoztatást talál a [képek importálása](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) modul-hivatkozás.

## <a name="import-from-a-local-file"></a>Egy helyi fájlból importálása

Merevlemez-meghajtóról a betanítási adatok a Studióban adatokként tölthet fel egy fájlt. Amikor importálja a fájlt, hozza létre az adatkészlet modul használatra kész kísérletek a munkaterületén.

Adatokat importálhat a helyi merevlemezen, tegye a következőket:

1. Kattintson a **+ új** a Studio ablakának alján.
2. Válassza ki **ADATKÉSZLET** és **helyi FÁJLBÓL**.
3. Az a **töltse fel egy új adatkészlet** párbeszédpanelen tallózással keresse meg a feltölteni kívánt fájlt.
4. Adjon meg egy nevet, az adattípus azonosításához, és igény szerint adjon meg egy leírást. Leírását javasolt – lehetővé teszi bármely jellemzőinek a jövőben az adatok használatakor vegye figyelembe az adatok rögzítéséhez.
5. A jelölőnégyzet **az új verziója, amelyet egy meglévő adatkészlet** lehetővé teszi, hogy egy meglévő adatkészlet új adatokkal frissülnek. Ehhez kattintson a jelölőnégyzetre, és írja be egy meglévő adatkészlet nevét.

![Töltse fel egy új adatkészlet](./media/import-data/upload-dataset-from-local-file.png)

Feltöltés ideje az adatok méretétől és a szolgáltatás a kapcsolat sebességétől függ. Ha tudja, hogy a fájl egy hosszú időt vesz igénybe, megteheti Studio belül további tevékenység következik, várakozás közben. Azonban az adatok feltöltése befejezése előtt a böngésző bezárásával következtében sikertelen lesz.

Az adatok feltöltését követően egy adatkészlet modulban tárolja, és bármely a kísérletvászonra a munkaterületén érhető el.

Egy kísérlet szerkesztésekor találja az Ön által feltöltött, az adatkészletek a **saját adatkészletek** alatt a **mentett adatkészletek** a modulpaletta listájában. Húzza át, és dobja el az adatkészletet a kísérlet vászonra, ha meg szeretné használni az adatkészlet további elemzés és gépi tanulás.

## <a name="import-from-online-data-sources"></a>Online adatforrásokból származó importálása

Használatával a [adatok importálása] [ import-data] modul, a kísérlethez adatokat importálhat a kísérlet futtatása közben különböző online adatforrásokból.

> [!NOTE]
> Ez a cikk általános információkat tartalmaz a [adatok importálása] [ import-data] modul. Részletesebb információ adattípusokat és érheti el, formátumban, a paraméterek és a gyakori kérdéseket lásd a modul a referencia-témakör a [adatok importálása] [ import-data] modul.

Használatával a [adatok importálása] [ import-data] modul férhet hozzá adataihoz számos online adatforrás közül a kísérlet futtatásakor:

* Egy webes URL-címe, HTTP-n keresztül
* Hadoop HiveQL használatával
* Azure Blob Storage
* Azure-tábla
* Azure SQL database vagy SQL Server Azure virtuális gépen
* A helyszíni SQL Server-adatbázis
* Adatcsatorna-szolgáltató, aktuálisan OData
* Azure Cosmos DB

A kísérlet futtatásakor a betanítási adatok érhető el, mert azt csak akkor használható, hogy a kísérletben. Ezzel egy adatkészlet modulban tárolt adatok bármely a kísérletvászonra a munkaterületén érhető el.

A Studio kísérlet során online adatforrás eléréséhez, adja hozzá a [adatok importálása] [ import-data] modult a kísérletvászonra. Válassza ki **importálása varázsló elindításához** alatt **tulajdonságok** válassza ki, és konfigurálja az adatforrást a lépésenkénti interaktív. Azt is megteheti, hogy manuálisan kiválaszthatja **adatforrás** alatt **tulajdonságok** , és adja meg az adatok eléréséhez szükséges paraméterek.

Az online adatforrás támogatott az alábbi táblázatban vannak esethez. Ez a táblázat is összefoglalja a támogatott formátumok és adatok elérésére használt paraméterek.

> [!IMPORTANT]
> Jelenleg a [adatok importálása] [ import-data] és [adatok exportálása] [ export-data] modulok olvashat és írhat az adatok csak a klasszikus létrehozott Azure storage-ból üzembe helyezési modell. Más szóval az új Azure Blob Storage fióktípus egy gyakran használt adatok tároláselérési rétegében vagy a ritkán használt adatok hozzáférési rétege által még nem támogatott.
>
> Bármely általánosan, Azure storage-fiókok előfordulhat, hogy létrehozott előtt elérhetővé váltak a szolgáltatás a beállítás nem befolyásolja.
> Ha szeretne egy új fiók létrehozásához, válassza ki a **klasszikus** a központi telepítés modell, vagy használhatja a Resource Managert, és válassza a **általános célú** helyett **a Blob storage-** a **Fiók típusa**.
>
> További információkért lásd: [Azure Blob Storage: Gyakori és ritka elérésű tárolási szintet](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Online adatforrás támogatott
Az Azure Machine Learning **adatok importálása** modul támogatja a következő adatforrásokat:

| Adatforrás | Leírás | Paraméterek |
| --- | --- | --- |
| A HTTP Protokollon keresztül, a webes URL-címe |A vesszővel elválasztott értékeket (CSV), a tabulátorral tagolt értékeket (TSV), a attribútum-relációs fájlformátumra (ARFF) és a támogatási vektor gépek (SVM-világos) formátumban, adatokat olvas be minden olyan webes URL-címe, amely a HTTP Protokollt használ |<b>URL-CÍM</b>: Itt adhatja meg, beleértve a webhely URL-címe és a fájlnevet, bármilyen kiterjesztésű fájl teljes nevét. <br/><br/><b>Adatformátum</b>: Itt adhatja meg, a támogatott formátumok egyikét: CSV, TSV, ARFF vagy SVM-világos. Ha az adatok egy fejléc sorra, rendelje hozzá az oszlopok neveit szolgál. |
| Hadoop/HDFS |Beolvassa az adatokat a Hadoop elosztott storage-ból. Azt adja meg a kívánt HiveQL, egy SQL-szerű lekérdezési nyelv segítségével adatokat. HiveQL a adatokat tudnak összesíteni, és végezze el az adatok szűrése, csak az adatokat hozzáadni a Studio is használható. |<b>Adatbázis-lekérdezés Hive</b>: Adja meg a Hive-lekérdezést az adatok létrehozására szolgál.<br/><br/><b>HCatalog kiszolgáló URI </b> : A következő formátumban a fürt nevét megadva  *&lt;a fürt nevét&gt;. azurehdinsight.NET formátumban van.*<br/><br/><b>Hadoop-felhasználói fiók nevét</b>: A fürt üzembe helyezéséhez használt Hadoop-fiók felhasználónevét határozza meg.<br/><br/><b>Hadoop-felhasználói fiók jelszava</b> : Adja meg a fürt üzembe helyezésekor használt hitelesítő adatokat. További információkért lásd: [Hadoop-fürtök létrehozása a HDInsight](../../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Kimeneti adatok helyének</b>: Itt adhatja meg, hogy az adatok tárolását, a Hadoop elosztott fájlrendszer (HDFS) vagy az Azure-ban. <br/><ul>Ha kimeneti adatokat tárolja a HDFS-ben, adja meg a HDFS-kiszolgáló URI-t. (Ügyeljen arra, használja a HDInsight-fürt nevét, a HTTPS:// előtag nélkül). <br/><br/>Ha a kimeneti adatokat tárolja az Azure-ban, adjon meg, hogy az Azure storage-fiók neve, a tárelérési kulcs és a tároló neve.</ul> |
| SQL-adatbázis |Beolvassa vagy egy Azure virtuális gépen futó SQL Server-adatbázis egy Azure SQL database-ben tárolt adatokat. |<b>Adatbázis-kiszolgáló neve</b>: Itt adhatja meg, amelyen az adatbázist futtató kiszolgáló nevét.<br/><ul>Azure SQL Database esetén adja meg a kiszolgáló nevét, amely akkor jön létre. Általában az űrlap rendelkezik  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Egy Azure-beli virtuális gépen üzemeltetett SQL-kiszolgáló esetén adja meg a *tcp:&lt;virtuális gép DNS-név&gt;, 1433*</ul><br/><b>Adatbázis neve </b>: Megadja az adatbázis nevét a kiszolgálón. <br/><br/><b>Felhasználói fiók kiszolgálónév</b>: Adja meg egy felhasználónevet, amely az adatbázis hozzáférési engedélyekkel rendelkezik. <br/><br/><b>Kiszolgáló felhasználói fiók jelszava</b>: Megadja a felhasználói fiókhoz tartozó jelszót.<br/><br/><b>Adatbázis-lekérdezés</b>: Adjon meg egy SQL-utasítást, amely leírja az olvasni kívánt adatokat. |
| Helyszíni SQL Database adatbázis |Beolvassa a helyszíni SQL-adatbázisban tárolt adatokat. |<b>Adatátjáró</b>: Megadja az adatkezelési átjáró telepítve van egy számítógépen, ahol hozzá tudjon férni az SQL Server-adatbázis nevét. Az átjáró beállításával kapcsolatos további információkért lásd: [végezze el a speciális elemzés az Azure Machine Learning-adatok egy helyszíni SQL Serverről](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Adatbázis-kiszolgáló neve</b>: Itt adhatja meg, amelyen az adatbázist futtató kiszolgáló nevét.<br/><br/><b>Adatbázis neve </b>: Megadja az adatbázis nevét a kiszolgálón. <br/><br/><b>Felhasználói fiók kiszolgálónév</b>: Adja meg egy felhasználónevet, amely az adatbázis hozzáférési engedélyekkel rendelkezik. <br/><br/><b>Felhasználónév és jelszó</b>: Kattintson a <b>adja meg az értékeket</b> adatbázis hitelesítő adatait. Használhatja az integrált Windows-hitelesítést vagy SQL Server-hitelesítéssel attól függően, hogy hogyan a helyszíni SQL Server konfigurálva van-e.<br/><br/><b>Adatbázis-lekérdezés</b>: Adjon meg egy SQL-utasítást, amely leírja az olvasni kívánt adatokat. |
| Azure-tábla |Adatokat olvas be a Table service az Azure Storage-ban.<br/><br/>Ha nagy mennyiségű adat ritkán olvasni, használja az Azure Table Service. Biztosít egy rugalmas, nem relációs (NoSQL), rugalmasan méretezhető, költséghatékony és magas rendelkezésre állású tárolási megoldást. |A beállítások a **adatok importálása** attól függően, hogy Ön hozzáfér a nyilvános vagy privát storage-fiók bejelentkezési hitelesítő adatokat igénylő módosítása. Ez határozza meg a <b>hitelesítési típus</b> amelyben lehetnek a saját set paraméterek tartoznak "PublicOrSAS" vagy "Account", érték. <br/><br/><b>Nyilvános vagy közös hozzáférésű Jogosultságkód (SAS) URI</b>: A paraméterek a következők:<br/><br/><ul><b>Tábla URI</b>: Adja meg a nyilvános vagy SAS URL-címet a táblában.<br/><br/><b>Meghatározza a sorok vizsgálatának tulajdonságneveivel</b>: Az értékek a következők <i>legjobb</i> megvizsgálja a megadott számú sort, vagy <i>ScanAll</i> a tábla összes sorát beolvasásához. <br/><br/>Ha az adatok egységes és kiszámítható, javasoljuk, hogy bejelölte *legjobb* , és adjon meg egy számot n Nagy táblák esetében ez gyorsabb olvasó alkalommal eredményezhet.<br/><br/>Ha az adatok különböző tulajdonságokat, amelyek a mélység függően változnak, és a helyére a táblázat felépítése, válassza ki a *ScanAll* lehetőséget az összes sor beolvasása. Ez biztosítja, hogy az eredményül kapott tulajdonság és a metaadatok átalakítási integritását.<br/><br/></ul><b>Privát Tárfiók</b>: A paraméterek a következők: <br/><br/><ul><b>Fiók neve</b>: Itt adhatja meg, amely tartalmazza a táblát olvassa el a fiók nevét.<br/><br/><b>Fiókkulcs</b>: Itt adhatja meg a fiókhoz tartozó tárfiók-kulcsot.<br/><br/><b>Táblanév</b> : Megadja a olvasni az adatokat tartalmazó tábla nevét.<br/><br/><b>Sorok vizsgálatának tulajdonságneveivel</b>: Az értékek a következők <i>legjobb</i> megvizsgálja a megadott számú sort, vagy <i>ScanAll</i> a tábla összes sorát beolvasásához.<br/><br/>Ha az adatok egységes és kiszámítható, azt javasoljuk, hogy bejelölte *legjobb* , és adjon meg egy számot n Nagy táblák esetében ez gyorsabb olvasó alkalommal eredményezhet.<br/><br/>Ha az adatok különböző tulajdonságokat, amelyek a mélység függően változnak, és a helyére a táblázat felépítése, válassza ki a *ScanAll* lehetőséget az összes sor beolvasása. Ez biztosítja, hogy az eredményül kapott tulajdonság és a metaadatok átalakítási integritását.<br/><br/> |
| Azure Blob Storage |Olvassa be az Azure Storage, beleértve a képeket, strukturálatlan szöveges vagy bináris adatok a Blob service-ben tárolt adatokat.<br/><br/>Használhatja a blobszolgáltatás nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat. Az adatok bármely pontjáról elérheti HTTP vagy HTTPS-kapcsolatok használatával. |A beállítások a **adatok importálása** attól függően, hogy Ön hozzáfér a nyilvános vagy privát storage-fiók bejelentkezési hitelesítő adatokat igénylő modul módosítása. Ez határozza meg a <b>hitelesítési típus</b> amelyben lehetnek a "PublicOrSAS" vagy "Account" értéket.<br/><br/><b>Nyilvános vagy közös hozzáférésű Jogosultságkód (SAS) URI</b>: A paraméterek a következők:<br/><br/><ul><b>URI-T</b>: Adja meg a nyilvános vagy SAS URL-címet a storage-blobba.<br/><br/><b>A fájl formátuma</b>: Megadja az adatok formátumát az a Blob Service-ben. A támogatott formátumok a következők: CSV, TSV és ARFF.<br/><br/></ul><b>Privát Tárfiók</b>: A paraméterek a következők: <br/><br/><ul><b>Fiók neve</b>: Adja meg a fiók, amely tartalmazza az olvasni kívánt blob neve.<br/><br/><b>Fiókkulcs</b>: Itt adhatja meg a fiókhoz tartozó tárfiók-kulcsot.<br/><br/><b>Tároló, a könyvtár vagy a blob elérési útja </b> : Itt adhatja meg, amely tartalmazza az adatok olvasásához a blob nevével.<br/><br/><b>BLOB fájlformátum</b>: Megadja az adatok formátumát az a blob Service-ben. A támogatott formátumok a CSV, TSV, ARFF, fürt megosztott kötetei szolgáltatás a megadott kódolás és Excel. <br/><br/><ul>Ha a formátum CSV- vagy TSV, mindenképpen azt jelzik, hogy a fájl tartalmaz egy fejléc sorra.<br/><br/>Az Excel-beállítás segítségével adatokat olvasni az Excel-munkafüzetek. Az a <i>Excel adatformátum</i> lehetőséget, jelezze van-e az adatok a Excel munkalapra tartományban, vagy Excel-táblázatban. Az a <i>Excel-munkalapot vagy beágyazott tábla </i>lehetőségnél adja meg a lap vagy az olvasni kívánt tábla nevét.</ul><br/> |
| Adatcsatorna-szolgáltató |Adatokat olvas be egy támogatott adatcsatorna-szolgáltató. Jelenleg csak az Open Data protokoll (OData) formátum támogatott. |<b>Adatok tartalomtípus</b>: Itt adhatja meg az OData-formátum.<br/><br/><b>Forrás URL-címe</b>: Adja meg az adatcsatorna teljes URL-CÍMÉT. <br/>Ha például a következő URL-cím olvas az adatbázisunk: http://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importálhatja egy másik kísérletből

Mikor érdemes számára egy egy kísérletből köztes eredményt és a egy másik kísérlet részeként használható időpontok lesz. Ehhez a modul adatkészletként mentése:

1. Kattintson a Mentés adatkészletként kívánt modul kimenete.
2. Kattintson a **mentés adatkészletként**.
3. Amikor a rendszer kéri, adja meg egy nevet és egy leírást, amely lehetővé teszi az adatkészlet könnyen azonosíthatja.
4. Kattintson a **OK** pipára.

A mentés után, az adatkészlet lesz a munkaterületen kísérletek parancsmagjaik használhatók. A megtalálja a **mentett adatkészletek** a modulpaletta listájában.

## <a name="next-steps"></a>További lépések

[Az Azure Machine Learning studio-webszolgáltatások adatok importálása és az adatok exportálása modult használó központi telepítése](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
