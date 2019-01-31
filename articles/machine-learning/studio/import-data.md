---
Cím: Import data into Machine Learning Studio titleSuffix: Azure Machine Learning Studio description: Adatok importálása az Azure Machine Learning Studióba különböző adatforrásokból származó adatokat hogyan. Ismerje meg, milyen típusú adatokat és adatformátumok a célnyelven támogatottak.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.subservice: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 11/29/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>A betanítási adatok importálása az Azure Machine Learning Studióba különböző adatforrásokból

A Machine Learning Studio a saját adatok használatával fejlesztése és betanítunk egy prediktív elemzési megoldás, használhatja az adatokat: 

* A [ **helyi fájl** ](import-data-from-local-file.md) -időben helyi adatok betöltése a merevlemezen a munkaterületen lévő adatkészlet modul létrehozása
* [**Online adatforrásokból** ](import-data-from-online-sources.md) -használatát a [adatok importálása] [ import-data] kísérletét futása közben számos online forrásokból származó adatokhoz való hozzáférésének modul
* [**A Machine Learning Studióban végrehajtott kísérletekhez** ](import-data-from-an-experiment.md) – a Machine Learning Studióban adatkészletként mentett adatok használata
* [**A helyszíni SQL Server-adatbázis** ](use-data-from-an-on-premises-sql-server.md) – nem kell manuálisan másolja az adatokat egy helyszíni SQL Server-adatbázisból származó adatok használata

> [!NOTE]
> Nincsenek mintaadatkészletek számos Machine Learning studióban, amely a betanítási adatok is használhat. Ezek a további információkért lásd: [az Azure Machine Learning Studio mintaadatkészleteinek használata](use-sample-datasets.md)).
> 
> 

Ez a bevezető cikk is ismerteti, hogyan olvashat be adatokat használatra kész a Machine Learning Studióban, és ismerteti, hogy mely adatformátumok a célnyelven és adattípusok használata támogatott.

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Az Azure Machine Learning Studióban használatra kész adatok lekérése
A Machine Learning Studio téglalap alakú vagy táblázatos adatok, például a strukturált adatok egy adatbázisból, azonban bizonyos körülmények között nem téglalap adatok használhatók vagy tagolt szöveges adat tervezték.

A legjobb, ha az adatok viszonylag tiszta. Ez azt jelenti, hogy szeretné problémákat, például nem jegyzett karakterláncok gondoskodik az adatok feltöltése a kísérlet előtt.

Azonban nincsenek modulok elérhető a Machine Learning Studióban, amelyek lehetővé teszik az adatok a kísérleten belülről néhány kezeléséhez. Attól függően, gépi tanulási algoritmusok fogja használni, szükség lehet annak eldöntése, hogyan fogja kezelni adatok szerkezeti problémák, például a hiányzó értékeket és a ritka adatokhoz, és vannak, amelyek segíthetnek, hogy a modulok. Tekintse meg a **adatátalakítás** a modulpaletta végre ezeket a funkciókat modulokba vonatkozó szakaszában.

A kísérlet során bármikor megtekintheti, és töltse le a kimeneti portra kattintva egy modul által előállított adatok. Attól függően, a modul lehet különböző letöltési beállítások használható, vagy előfordulhat, hogy a jeleníthetik meg az adatokat a Machine Learning Studióban webböngészőből.

## <a name="data-formats-and-data-types-supported"></a>Támogatott formátumok és adattípusok
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

Ha például a metaadatokat tartalmazó ARFF formátumú adatokat importál, a Machine Learning Studio ezeket a metaadatokat használja meghatározásához a fejlécet és az egyes oszlopok adattípusát.

Ha importálja az adatokat, például a TSV- vagy CSV formátum, amely nem tartalmazza ezeket a metaadatokat, a Machine Learning Studio minden oszlop adattípusát kikövetkezteti által az adatok mintavételezésének. Ha az adatok nem rendelkezik oszlopfejlécekkel, a Machine Learning Studio alapértelmezett nevek biztosítja.

Explicit módon adja meg vagy módosítsa a fejlécek és adattípusok használatával oszlopok esetében a [metaadatainak szerkesztése][edit-metadata].

A következő **adattípusok** felismeri a Machine Learning Studio:

* String
* Egész szám
* Double
* Logikai
* DateTime
* TimeSpan

A Machine Learning Studio nevű belső adattípust használ ***adattábla*** adatok átadására a modulok között. Adattábla formátum használatával az explicit módon átalakíthatja az adatokat a [adatkészlet átalakítása] [ convert-to-dataset] modul.

Adattábla eltérő formátumokban fogad modulok konvertálja az adatok adattábla csendes mielőtt továbbítaná a következő modul számára.

Szükség esetén átválthat adatok táblázatos formában visszaimportálni CSV, TSV, ARFF vagy más átalakítási modulok SVMLight formátumú.
Tekintse meg a **formátum Adatátalakítókat** a modulpaletta végre ezeket a funkciókat modulokba vonatkozó szakaszában.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
