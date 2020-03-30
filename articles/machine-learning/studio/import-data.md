---
title: Betanítási adatok importálása
titleSuffix: ML Studio (classic) - Azure
description: Hogyan importálhatja az adatokat az Azure Machine Learning Studio (klasszikus) különböző adatforrásokból. További információ a támogatott adattípusokról és adatformátumokról.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217984"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>A betanítási adatok importálása az Azure Machine Learning Studio -ba (klasszikus) különböző adatforrásokból

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ha saját adatait szeretné használni a Machine Learning Studio-ban (klasszikus) egy prediktív elemzési megoldás kifejlesztéséhez és betanításához, a következő adatokat használhatja: 

* **Helyi fájl** – Helyi adatok betöltése idő előtt a merevlemezről adatkészletmodul létrehozásához a munkaterületen
* **Online adatforrások** – Az [Adatok importálása][import-data] modullal a kísérlet futása közben több online forrásból származó adatok hozhatja meg a hozzáférést
* **Machine Learning Studio (klasszikus) kísérlet** – Adatkészletként mentett adatok használata a Machine Learning Studio-ban (klasszikus)
* [**Helyszíni SQL Server-adatbázis**](use-data-from-an-on-premises-sql-server.md) – Adatok használata helyszíni SQL Server-adatbázisból anélkül, hogy manuálisan kellene másolnia az adatokat

> [!NOTE]
> Számos minta adatkészletek érhetők el a Machine Learning Studio (klasszikus), amely a betanítási adatok. Ezekről [a mintaadatkészletek használata az Azure Machine Learning Studioban (klasszikus) című témakörben](use-sample-datasets.md)talál.

## <a name="prepare-data"></a>Adatok előkészítése

A Machine Learning Studio (klasszikus) úgy van kialakítva, hogy négyszögletes vagy táblázatos adatokkal, például adatbázisból deszelt vagy strukturált adatokkal működjön, bár bizonyos körülmények között nem téglalap alakú adatok használhatók.

A legjobb, ha az adatok viszonylag tiszta, mielőtt importálja őket studio (klasszikus). Például érdemes gondoskodni a problémákról, például a nem jegyzett karakterláncokkal.

Vannak azonban olyan modulok a Studio (klasszikus), amelyek lehetővé teszik bizonyos manipuláció az adatok a kísérleten belül az adatok importálása után. A gépi tanulási algoritmusok használata, előfordulhat, hogy el kell döntenie, hogyan fogja kezelni az adatok strukturális problémák, mint például a hiányzó értékek és ritka adatok, és vannak modulok, amelyek segíthetnek ebben. Keresse meg a modulpaletta **Adattranszformáció** szakaszában az okat ellátó modulokat.

A kísérlet bármely pontján megtekintheti vagy letöltheti a modul által létrehozott adatokat a kimeneti portra kattintva. A modultól függően különböző letöltési lehetőségek állnak rendelkezésre, vagy a webböngészőben lévő adatokat a Studio (klasszikus) alkalmazásban is megjelenítheti.

## <a name="supported-data-formats-and-data-types"></a>Támogatott adatformátumok és adattípusok

Számos adattípust importálhat a kísérletbe attól függően, hogy milyen mechanizmust használ az adatok importálásához, és honnan származik:

* Egyszerű szöveg (.txt)
* Vesszővel tagolt értékek (CSV) fejléccel (.csv) vagy anélkül (.nh.csv)
* Tabulátorral tagolt értékek (TSV) fejléccel (.tsv) vagy anélkül (.nh.tsv)
* Excel-fájl
* Azure-tábla
* Hive tábla
* SQL adatbázistábla
* OData-értékek
* SVMLight-adatok (.svmlight) (a formátumra vonatkozó információkat lásd az [SVMLight definíciójában)](http://svmlight.joachims.org/)
* Attribútumkapcsolati fájlformátum (ARFF) adatai (.arff) (a formátumra vonatkozó információkat lásd az [ARFF definíciójában)](https://weka.wikispaces.com/ARFF)
* Zip-fájl (.zip)
* R objektum vagy munkaterületi fájl (. RData)

Ha az adatokat metaadatokat tartalmazó formátumban, például ARFF formátumban importálja, a Studio (klasszikus) ezt a metaadatot használja az egyes oszlopok fejlécének és adattípusának meghatározásához.

Ha olyan adatokat importál, mint például a TSV vagy a CSV formátum, amely nem tartalmazza ezeket a metaadatokat, a Studio (klasszikus) az adatok mintavételezésével következtet az egyes oszlopok adattípusára. Ha az adatok nem tartalmaznak oszlopfejléceket, a Studio (klasszikus) alapértelmezett neveket ad meg.

A [Metaadatok szerkesztése][edit-metadata] modul segítségével explicit módon megadhatja vagy módosíthatja az oszlopok címsorait és adattípusait.

A következő adattípusokat ismeri fel a Studio (klasszikus):

* Sztring
* Egész szám
* Double
* Logikai
* DateTime
* időtartam

A Studio egy ***adattábla*** nevű belső adattípust használ az adatok modulok közötti átadására. Az adatokat explicit módon adattábla formátumba konvertálhatja a [Convert to Dataset][convert-to-dataset] module segítségével.

Minden olyan modul, amely az adattáblától eltérő formátumokat fogad el, az adatokat csendesen adattáblává konvertálja, mielőtt átadná a következő modulnak.

Szükség esetén az adattábla-formátumot más konverziós modulok használatával visszakonvertálhatja CSV, TSV, ARFF vagy SVMLight formátumba.
Keresse meg a modulpaletta **Adatformátum-átalakítások** szakaszában az okat ellátó modulokat.

## <a name="data-capacities"></a>Adatkapacitások

A Machine Learning Studio (klasszikus) moduljai akár 10 GB sűrű numerikus adatadatkészletet is támogatnak a gyakori használati esetekhez. Ha egy modul egynél több bemenetből fogad adatokat, a bemenet összesített mérete nem haladhatja meg a 10 GB-ot. A Hive vagy az Azure SQL Database lekérdezései használatával mintát vehet a nagyobb adatkészletekből, vagy használhatja a Learning by Count előfeldolgozást az adatok importálása előtt.  

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

A néhány GB-nél nagyobb adatkészletek esetén töltse fel az adatokat az Azure Storage-ba vagy az Azure SQL Database-be, vagy használja az Azure HDInsight-ot, ahelyett, hogy közvetlenül egy helyi fájlból töltene fel.

A képadatokkal kapcsolatos információkat a [Képek importálása](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) modul hivatkozásában találja.

## <a name="import-from-a-local-file"></a>Importálás helyi fájlból

Az adatfájlt feltöltheti a merevlemezről, hogy betanítási adatként használhassa a Studio (klasszikus) programban. Adatfájl importálásakor létrehoz egy adatkészletmodult, amely készen áll a munkaterületi kísérletekben való használatra.

Ha adatokat szeretne importálni egy helyi merevlemezről, tegye a következőket:

1. Kattintson a Studio (klasszikus) ablak alján a **+ÚJ** gombra.
2. Válassza **az ADATKÉSZLET** és a HELYI FÁJL **ELEMET.**
3. Az **Új adatkészlet feltöltése** párbeszédpanelen keresse meg a feltölteni kívánt fájlt.
4. Adjon meg egy nevet, azonosítsa az adattípust, és adja meg a leírást. A leírás ajánlott - lehetővé teszi, hogy rögzítse az adatokkal kapcsolatos olyan jellemzőket, amelyeket az adatok jövőbeni használatakor szeretne megjegyezni.
5. A **jelölőnégyzet: Ez egy meglévő adatkészlet új verziója** lehetővé teszi egy meglévő adatkészlet új adatokkal való frissítését. Ehhez kattintson erre a jelölőnégyzetre, majd írja be egy meglévő adatkészlet nevét.

![Új adatkészlet feltöltése](./media/import-data/upload-dataset-from-local-file.png)

A feltöltési idő az adatok méretétől és a szolgáltatáshoz való csatlakozás sebességétektől függ. Ha tudja, hogy a fájl hosszú időt vesz igénybe, akkor a várakozás ideje alatt más dolgokat is megtehet a Studio (klasszikus) belsejében. Azonban az adatfeltöltés befejezése előtt bezárja a böngészőt, és a feltöltés sikertelen lesz.

Az adatok feltöltése után egy adatkészletmodultárolja őket, és a munkaterület bármely kísérlete számára elérhetővé válik.

Amikor szerkeszt egy kísérletet, megtalálhatja a feltöltött adatkészletek adatkészletek **listájában** a modulpaletta **Mentett adatkészletek** listájában. Az adatkészletet a kísérlet vászonra húzhatja, ha az adatkészletet további elemzésekhez és gépi tanuláshoz szeretné használni.

## <a name="import-from-online-data-sources"></a>Importálás online adatforrásokból

Az [Adatok importálása][import-data] modul használatával a kísérlet a kísérlet futása közben különböző online adatforrásokból importálhat adatokat.

> [!NOTE]
> Ez a cikk általános információkat tartalmaz az [Adatok importálása][import-data] modulról. Az adatok eléréséhez, formátumaihoz, paramétereinek és a gyakori kérdésekre adott válaszoknak a részletesebb tudnivalói az [Adatok importálása][import-data] modul modultémakörében talál további információt.

[Az Adatok importálása][import-data] modul használatával a kísérlet futása közben számos online adatforrás egyikéből érheti el az adatokat:

* Webes URL HTTP-t használó
* Hadoop a HiveQL használatával
* Azure Blob Storage
* Azure-tábla
* Azure SQL-adatbázis vagy SQL Server az Azure VM-en
* Helyszíni SQL Server-adatbázis
* Adatcsatorna-szolgáltató, az OData jelenleg
* Azure Cosmos DB

Mivel ez a betanítási adatok a kísérlet futása közben érhetők el, csak abban a kísérletben érhető el. Összehasonlításképpen, az adatkészletmodulban tárolt adatok a munkaterület bármely kísérlete számára elérhetők.

Ha a Studio (klasszikus) kísérletben szeretne online adatforrásokat elérni, adja hozzá az [Adatok importálása][import-data] modult a kísérlethez. Ezután válassza **az Adatok importálása varázsló indítása** lehetőséget a **Tulajdonságok csoportban** az adatforrás kijelöléséhez és konfigurálásához. Másik lehetőségként manuálisan is kiválaszthatja **az Adatforrás** t **a Tulajdonságok** csoportban, és megadhatja az adatok eléréséhez szükséges paramétereket.

A támogatott online adatforrások tételes ek az alábbi táblázatban találhatók. Ez a táblázat összefoglalja a támogatott fájlformátumokat és az adatok eléréséhez használt paramétereket is.

> [!IMPORTANT]
> Jelenleg az [Adatok importálása][import-data] és [az adatok exportálása][export-data] modulok csak az Azure storage-ból hozhat létre a klasszikus telepítési modell segítségével. Más szóval az új Azure Blob Storage-fiók típusa, amely egy gyakori elérésű tárolási szint vagy ritka elérésű tárolási hozzáférési szint még nem támogatott.
>
> Általában a szolgáltatásbeállítás elérhetővé vált elérhetővé vált Azure storage-fiókok at általában nem érinti.
> Ha új fiókot kell létrehoznia, válassza a **Klasszikus** lehetőséget a központi telepítési modellhez, vagy használja az Erőforrás-kezelőt, és válassza **az Általános célú** lehetőséget a **Fióktípus Blob storage** **helyett.**
>
> További információ: [Azure Blob Storage: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Támogatott online adatforrások
Az Azure Machine Learning Studio (klasszikus) **Import Data** modul a következő adatforrásokat támogatja:

| Adatforrás | Leírás | Paraméterek |
| --- | --- | --- |
| Webes URL HTTP-n keresztül |Az adatokat vesszővel tagolt értékekben (CSV), tabulátorral tagolt értékekben (TSV), attribútumkapcsolatú fájlformátumban (ARFF) és Support Vector Machines (SVM-light) formátumban olvassa be az adatokat a HTTP-t használó webes URL-címekről |<b>URL</b>: Megadja a fájl teljes nevét, beleértve a webhely URL-címét és a fájlnevét, bármilyen kiterjesztéssel. <br/><br/><b>Adatformátum</b>: A támogatott adatformátumok egyikét adja meg: CSV, TSV, ARFF vagy SVM-light. Ha az adatok fejlécsorral rendelkeznek, oszlopnevek hozzárendelésére szolgál. |
| Hadoop/HDFS |Adatokat olvas fel a Hadoop elosztott tárolójából. A kívánt adatokat a HiveQL SQL-szerű lekérdezési nyelv használatával adhatja meg. HiveQL is használható adatok összesítésére és adatszűrés végrehajtására, mielőtt hozzáadja az adatokat a Studio (klasszikus). |<b>Hive adatbázis-lekérdezés:</b>Megadja az adatok létrehozásához használt Hive lekérdezést.<br/><br/><b>HCatalog-kiszolgáló URI:</b> A fürt nevét a * &lt;&gt;fürtnevének .azurehdinsight.net formátumával adta meg.*<br/><br/><b>Hadoop felhasználói fiók neve</b>: Megadja a fürt kiépítéséhez használt Hadoop felhasználói fiók nevét.<br/><br/><b>Hadoop felhasználói fiók jelszava</b> : Megadja a fürt kiépítésekor használt hitelesítő adatokat. További információ: [Hadoop-fürtök létrehozása a HDInsightban](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)című témakörben talál.<br/><br/><b>Kimeneti adatok helye:</b>Megadja, hogy az adatok egy Hadoop elosztott fájlrendszerben (HDFS) vagy az Azure-ban tárolódnak.Location of output data: Specifies whether the data is stored in a Hadoop distributed file system (HDFS) or in Azure. <br/><ul>Ha a kimeneti adatokat HDFS-ben tárolja, adja meg a HDFS-kiszolgáló URI-ját. (Ügyeljen arra, hogy a HDInsight-fürt nevét a HTTPS:// előtag nélkül használja). <br/><br/>Ha a kimeneti adatokat az Azure-ban tárolja, meg kell adnia az Azure storage-fiók nevét, storage-hozzáférési kulcs és storage-tároló nevét.</ul> |
| SQL-adatbázis |Az Azure SQL-adatbázisban vagy egy Azure virtuális gépen futó SQL Server-adatbázisban tárolt adatok olvasása. |<b>Adatbázis-kiszolgáló neve</b>: Annak a kiszolgálónak a nevét adja meg, amelyen az adatbázis fut.<br/><ul>Az Azure SQL Database adja meg a létrehozott kiszolgáló nevét. Általában a * &lt;&gt;generated_identifier .database.windows.net.* <br/><br/>Az Azure virtuális gépen üzemeltetett SQL-kiszolgáló esetén adja meg a *tcp:&lt;Virtual Machine DNS Name ( virtuális gép&gt;DNS-neve ) 1433*</ul><br/><b>Adatbázis neve </b>: Megadja a kiszolgálón lévő adatbázis nevét. <br/><br/><b>Kiszolgálói felhasználói fiók neve</b>: Az adatbázishoz hozzáférési engedéllyel rendelkező fiók felhasználónevét adja meg. <br/><br/><b>Kiszolgálói felhasználói fiók jelszava</b>: Megadja a felhasználói fiók jelszavát.<br/><br/><b>Adatbázis-lekérdezés</b>:Adjon meg egy SQL utasítást, amely leírja az elolvasni kívánt adatokat. |
| Helyszíni SQL-adatbázis |A helyszíni SQL-adatbázisban tárolt adatok olvasása. |<b>Adatátjáró</b>: Megadja a számítógépre telepített adatkezelési átjáró nevét, amelyen hozzáférhet az SQL Server adatbázisához. Az átjáró beállításáról a [Speciális elemzések végrehajtása az Azure Machine Learning Studio -val (klasszikus) helyszíni SQL-kiszolgálóadatainak használatával című témakörben](use-data-from-an-on-premises-sql-server.md)talál.<br/><br/><b>Adatbázis-kiszolgáló neve</b>: Annak a kiszolgálónak a nevét adja meg, amelyen az adatbázis fut.<br/><br/><b>Adatbázis neve </b>: Megadja a kiszolgálón lévő adatbázis nevét. <br/><br/><b>Kiszolgálói felhasználói fiók neve</b>: Az adatbázishoz hozzáférési engedéllyel rendelkező fiók felhasználónevét adja meg. <br/><br/><b>Felhasználónév és jelszó:</b>Kattintson <b>az Értékek megadása</b> elemre az adatbázis hitelesítő adatainak megadásához. A Helyszíni SQL Server konfigurálásától függően használhatja a Windows integrált hitelesítést vagy az SQL Server-hitelesítést.<br/><br/><b>Adatbázis-lekérdezés</b>:Adjon meg egy SQL utasítást, amely leírja az elolvasni kívánt adatokat. |
| Azure-tábla |Adatokolvasás a Table szolgáltatás az Azure Storage-ban.<br/><br/>Ha ritkán nagy mennyiségű adatot olvas, használja az Azure Table Service. Rugalmas, nem relációs (NoSQL), masszívan méretezhető, olcsó és magas rendelkezésre állású tárolási megoldást biztosít. |Az Adatok **importálása párbeszédpanel** beállításai attól függően változnak, hogy nyilvános adatokhoz vagy bejelentkezési hitelesítő adatokat igénylő magántárfiókhoz fér-e hozzá. Ezt a <b>hitelesítéstípusa</b> határozza meg, amelynek értéke "PublicOrSAS" vagy "Account", amelyek mindegyike saját paraméterkészletet rendelkezik. <br/><br/><b>Nyilvános vagy megosztott hozzáférésű aláírás (SAS) URI</b>: A paraméterek a következők:<br/><br/><ul><b>Táblázat URI</b>: Megadja a tábla nyilvános vagy SAS URL-címét.<br/><br/><b>Megadja a tulajdonságneveket keresgélésre váró sorokat:</b>Az értékek <i>TopN</i> a megadott számú sor beolvasásához, vagy a <i>ScanAll</i> érték a táblázat összes sorának beolvasásához. <br/><br/>Ha az adatok homogének és kiszámíthatóak, ajánlott a *TopN* lehetőséget választani, és az N számot megadni. Nagy táblák esetén ez gyorsabb olvasási időt eredményezhet.<br/><br/>Ha az adatok a táblázat mélységétől és pozíciójától függően változó tulajdonságkészletekkel vannak felépítve, válassza a *ScanAll* lehetőséget az összes sor betöltéséhez. Ez biztosítja az eredményül kapott tulajdonság és metaadat-átalakítás integritását.<br/><br/></ul><b>Privát tárfiók:</b>A paraméterek a következők: <br/><br/><ul><b>Fiók név</b>: Megadja annak a fióknak a nevét, amely az olvasandó táblát tartalmazza.<br/><br/><b>Fiókkulcs</b>: A fiókhoz társított tárolókulcs megadása.<br/><br/><b>Tábla neve</b> : Megadja az olvasandó adatokat tartalmazó tábla nevét.<br/><br/><b>A tulajdonságneveket keresve keresandó sorok</b>: Az értékek <i>TopN</i> a megadott számú sor beolvasásához, vagy a <i>ScanAll</i> a táblázat összes sorának beolvasásához.<br/><br/>Ha az adatok homogének és kiszámíthatóak, azt javasoljuk, hogy válassza a *TopN* lehetőséget, és adjon meg egy számot az N-hez. Nagy táblák esetén ez gyorsabb olvasási időt eredményezhet.<br/><br/>Ha az adatok a táblázat mélységétől és pozíciójától függően változó tulajdonságkészletekkel vannak felépítve, válassza a *ScanAll* lehetőséget az összes sor betöltéséhez. Ez biztosítja az eredményül kapott tulajdonság és metaadat-átalakítás integritását.<br/><br/> |
| Azure Blob Storage |Az Azure Storage Blob szolgáltatásában tárolt adatok olvasása, beleértve a képeket, a strukturálatlan szöveget vagy a bináris adatokat.<br/><br/>A Blob szolgáltatás használatával nyilvánosan elérhetővé tehet adatokat, vagy az alkalmazásadatok privát tárolására. Az adatokat bárhonnan elérheti HTTP- vagy HTTPS-kapcsolatok használatával. |Az Adatok **importálása** modul beállításai attól függően változnak, hogy nyilvános adatokhoz vagy bejelentkezési hitelesítő adatokat igénylő magántárfiókhoz fér-e hozzá. Ezt a hitelesítés típusa határozza <b>meg,</b> amelynek értéke "PublicOrSAS" vagy "Account".<br/><br/><b>Nyilvános vagy megosztott hozzáférésű aláírás (SAS) URI</b>: A paraméterek a következők:<br/><br/><ul><b>URI</b>: Megadja a nyilvános vagy SAS URL-címet a tárolási blobhoz.<br/><br/><b>Fájlformátum:</b>Megadja az adatok formátumát a Blob szolgáltatásban. A támogatott formátumok a CSV, a TSV és az ARFF.<br/><br/></ul><b>Privát tárfiók:</b>A paraméterek a következők: <br/><br/><ul><b>Fióknév</b>: Megadja az olvasni kívánt blobot tartalmazó fiók nevét.<br/><br/><b>Fiókkulcs</b>: A fiókhoz társított tárolókulcs megadása.<br/><br/><b>A tároló, a könyvtár vagy</b> a blob elérési útja: Megadja az olvasandó adatokat tartalmazó blob nevét.<br/><br/><b>Blob fájlformátum:</b>Megadja az adatok formátumát a blob szolgáltatásban. A támogatott adatformátumok a CSV, A TSV, AZ ARFF, a megadott kódolású CSV és az Excel. <br/><br/><ul>Ha a formátum CSV vagy TSV, mindenképpen jelezze, hogy a fájl tartalmaz-e fejlécsort.<br/><br/>Az Excel beállítással exceles munkafüzetekből olvashat adatokat. Az <i>Excel adatformátum-formátumában</i> adja meg, hogy az adatok Excel-munkalaptartományban vagy Excel-táblázatban vannak-e. Az <i>Excel-lapon vagy </i>a beágyazott táblázatban adja meg annak a lapnak vagy táblázatnak a nevét, amelyből olvasni szeretne.</ul><br/> |
| Adatcsatorna-szolgáltató |Egy támogatott hírcsatorna-szolgáltatótól olvas be adatokat. Jelenleg csak az Open Data Protocol (OData) formátum támogatott. |<b>Adattartalom-típus</b>: Megadja az OData formátumot.<br/><br/><b>Forrás URL</b>-címe : Megadja az adatcsatorna teljes URL-címét. <br/>Például a következő URL-cím olvasható a Northwind mintaadatbázisból:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importálás másik kísérletből

Lesznek olyan esetek, amikor egy kísérlet köztes eredményét szeretné használni egy másik kísérlet részeként. Ehhez mentse a modult adatkészletként:

1. Kattintson annak a modulnak a kimenetére, amelyet adatkészletként szeretne menteni.
2. Kattintson **a Mentés adatkészletként gombra.**
3. Amikor a rendszer kéri, adjon meg egy nevet és egy leírást, amely lehetővé teszi az adatkészlet egyszerű azonosítását.
4. Kattintson az **OK** gombra.

Amikor a mentés befejeződik, az adatkészlet a munkaterületen bármely kísérletben használható lesz. Ez a modulpaletta **Mentett adatkészletek** listájában található.

## <a name="next-steps"></a>További lépések

[Az Adatok importálása és az adatexportálásmodulokat használó Azure Machine Learning Studio webszolgáltatások üzembe helyezése](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
