---
title: Referenciaadatok használata az Azure Stream Analytics-ben a keresgélésekhez
description: Ez a cikk ismerteti, hogyan használhatja a referenciaadatokat az Azure Stream Analytics-feladat lekérdezési tervében lévő adatok kereséséhez vagy korrelációjához.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267286"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Referenciaadatok használata a Stream Analytics-ben lévő keresgélő műveletekhez

A referenciaadatok (más néven lookup table) egy véges adatkészlet, amely statikus vagy lassan változó jellegű, és amelyet a keresés hez vagy az adatfolyamok bővítéséhez használnak. Egy IoT-forgatókönyvben például tárolhatja az érzékelők metaadatait (amelyek nem változnak gyakran) a referenciaadatokban, és valós idejű IoT-adatfolyamokkal összekapcsolhatja azokat. Az Azure Stream Analytics betölti a referenciaadatokat a memóriában az alacsony késésű adatfolyam-feldolgozás elérése érdekében. A referenciaadatok az Azure Stream Analytics-feladatban, általában egy [referencia-adatillesztés](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) a lekérdezésben. 

A Stream Analytics támogatja az Azure Blob storage-ot és az Azure SQL Database-t a referenciaadatok tárolási rétegeként. Az Azure Data Factory ból blobtárolóba is átalakíthatja és/vagy másolhatja a referenciaadatokat, hogy [tetszőleges számú felhőalapú és helyszíni adattárakat használhasson.](../data-factory/copy-activity-overview.md)

## <a name="azure-blob-storage"></a>Azure Blob Storage

A referenciaadatok a blobok sorozataként vannak modellezve (a bemeneti konfigurációban definiálva) a blob nevében megadott dátum/idő növekvő sorrendjében. **Csak** akkor támogatja a hozzáadást a sorozat végére a sorozat utolsó blobja által megadottnál **nagyobb** dátum/idő használatával.

### <a name="configure-blob-reference-data"></a>Blob referenciaadatainak konfigurálása

A referenciaadatok konfigurálásához először referenciaadatok típusú bevitelt kell **létrehoznia.** Az alábbi táblázat ismerteti az egyes tulajdonságokat, amelyeket meg kell adnia a referenciaadatok bevitelének létrehozásakor a leírással:

|**Tulajdonság neve**  |**Leírás**  |
|---------|---------|
|Input Alias (Bemeneti alias)   | A feladatlekérdezésben a bemenetre való hivatkozáshoz használt rövid név.   |
|Tárfiók   | Annak a tárfióknak a neve, ahol a blobok találhatók. Ha ugyanabban az előfizetésben van, mint a Stream Analytics-feladat, kiválaszthatja azt a legördülő menüből.   |
|Tárfiók kulcsa   | A tárfiókhoz társított titkos kulcs. Ez automatikusan kitöltődik, ha a tárfiók ugyanabban az előfizetésben van, mint a Stream Analytics-feladat.   |
|Tároló tároló   | A tárolók logikai csoportosítást biztosítanak a Microsoft Azure Blob szolgáltatásban tárolt blobok számára. Amikor feltölt egy blobot a Blob szolgáltatásba, meg kell adnia egy tárolót az adott blobhoz.   |
|Path Pattern (Elérésiút-minta)   | A blobok a megadott tárolón belüli megkereséséhez használt elérési út. Az elérési úton belül a következő két változó egy vagy több példányát adhatja meg:<BR>{date}, {time}<BR>1. példa: termékek/{date}/{time}/product-list.csv<BR>2. példa: termékek/{date}/product-list.csv<BR>3. példa: terméklista.csv<BR><br> Ha a blob nem létezik a megadott elérési úton, a Stream Analytics-feladat megvárja a korlátlan ideig a blob elérhetővé válik.   |
|Dátumformátum [nem kötelező]   | Ha a megadott elérési útmintán belül {date} elemet használt, akkor kiválaszthatja azt a dátumformátumot, amelyben a blobok a támogatott formátumok legördülő legördülő legördülő menüből vannak rendezve.<BR>Példa: YYYY/HH/DD, MM/DD/YYYY stb.   |
|Időformátum [nem kötelező]   | Ha a megadott elérési útmintán belül {time} programot használt, akkor kiválaszthatja azt az időformátumot, amelyben a blobok a támogatott formátumok legördülő legördülő menüből vannak rendezve.<BR>Példa: HH, HH/mm vagy HH-mm.  |
|Eseményszeresítési formátum   | Annak érdekében, hogy a lekérdezések a várt módon működjenek, a Stream Analytics-nek tudnia kell, hogy milyen szerializálási formátumot használ a bejövő adatfolyamokhoz. A referenciaadatok esetében a támogatott formátumok a CSV és a JSON.  |
|Encoding   | Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum.  |

### <a name="static-reference-data"></a>Statikus referenciaadatok

Ha a referenciaadatok várhatóan nem változnak, akkor a statikus referenciaadatok támogatása statikus elérési út megadásával engedélyezhető a bemeneti konfigurációban. Az Azure Stream Analytics felveszi a blobot a megadott elérési útról. {date} és {time} helyettesítő tokenek nem szükségesek. Mivel a referenciaadatok nem módosíthatók a Stream Analytics szolgáltatásban, nem ajánlott felülírni egy statikus referencia-adatblobot.

### <a name="generate-reference-data-on-a-schedule"></a>Hivatkozási adatok létrehozása ütemezés szerint

Ha a referenciaadatok lassan változó adatkészlet, akkor a referenciaadatok frissítésének támogatása engedélyezve van a ({date} és {time} helyettesítő tokenek használatával a bemeneti konfigurációban egy elérési út minta megadásával. A Stream Analytics felveszi a frissített referenciaadat-definíciókat ezen elérési út minta alapján. Például egy `sample/{date}/{time}/products.csv` **"YYYY-MM-DD"** dátumformátumú és **"HH-mm"** időformátumú minta arra utasítja a `sample/2015-04-16/17-30/products.csv` Stream Analytics-et, hogy 2015.

Az Azure Stream Analytics egy perces időközzel automatikusan megkeresi a frissített referenciaadat-blobokat. Ha egy blob időbélyeg 10:30:00 feltöltése kis késéssel (például 10:30:30), észre fogja venni, egy kis késés a Stream Analytics feladat hivatkozva ez a blob. Az ilyen esetek elkerülése érdekében ajánlott a blob feltöltése korábban, mint a cél érvényességi idő (10:30:00 ebben a példában), hogy a Stream Analytics-feladat elegendő időt felderítése és betöltése a memóriába, és műveletekvégrehajtása. 

> [!NOTE]
> Jelenleg a Stream Analytics-feladatok csak akkor keresik a blob frissítését, ha a gép időbeli ütemezése a blob nevében kódolt időhöz. Például a feladat a `sample/2015-04-16/17-30/products.csv` lehető leghamarabb, de legkorábban 17:30-kor, 2015. Soha *nem* fog keresni egy blob egy kódolt idő korábbi, mint az utolsó, amely felfedezett.
> 
> Ha például a feladat `sample/2015-04-16/17-30/products.csv` megtalálja a blobot, figyelmen kívül hagyja azokat a fájlokat, amelyek kódolt dátuma 2015. `sample/2015-04-16/17-25/products.csv`
> 
> Hasonlóképpen, `sample/2015-04-16/17-30/products.csv` ha csak 2015.
> 
> Ez alól kivételt képez, ha a feladatnak újra fel kell dolgoznia az adatokat az időben, vagy amikor a feladat először elindul. Kezdési időpontban a feladat a feladat megadott kezdési időpontja előtt létrehozott legújabb blobot keresi. Ez annak biztosítására szolgál, hogy a feladat indításakor egy **nem üres referencia-adatkészlet** legyen. Ha nem található, a feladat a `Initializing input without a valid reference data blob for UTC time <start time>`következő diagnosztikát jeleníti meg: .

[Az Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) segítségével vezénylheti a Stream Analytics által a referencia-adatdefiníciók frissítéséhez szükséges frissített blobok létrehozásának feladatát. A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory támogatja a [nagyszámú felhőalapú és helyszíni adattárakhoz való csatlakozást,](../data-factory/copy-activity-overview.md) valamint az adatok egyszerű, a megadott rendszeres ütemezés szerint történő áthelyezését. További információkért és lépésről lépésre, hogyan állíthat be egy Data Factory folyamat ot a Stream Analytics, amely egy előre meghatározott ütemezés szerint frissül, tekintse meg ezt a [GitHub-mintát.](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs)

### <a name="tips-on-refreshing-blob-reference-data"></a>Tippek a blobreferencia-adatok frissítéséhez

1. Ne írja felül a referencia-adatblobokat, mivel azok nem módosíthatók.
2. A referenciaadatok frissítésének ajánlott módja a következő:
    * {date}/{time} használata az elérési út mintájában
    * Új blob hozzáadása a feladat bemenetében definiált azonos tároló és elérési út mintájával
    * Használjon a sorozat utolsó blobja által megadottnál **nagyobb** dátumot/időpontot.
3. A referencia-adatblobokat **nem** a blob "Utolsó módosítás" ideje rendezi, hanem csak a blob nevében megadott időpontés dátum szerint a(z) {date} és {time} helyettesítések használatával.
3. Annak elkerülése érdekében, hogy nagy számú blobok, fontolja meg a nagyon régi blobok, amelyek feldolgozása már nem történik meg. Vegye figyelembe, hogy az ASA előfordulhat, hogy bizonyos esetekben, például újraindítás esetén újra fel kell dolgoznia egy kis mennyiséget.

## <a name="azure-sql-database"></a>Azure SQL Database

Az Azure SQL Database referenciaadatait a Stream Analytics-feladat lekéri, és a memóriában pillanatképként tárolja a feldolgozáshoz. A referenciaadatok pillanatképét is tárolja egy tárolóban egy tárfiókban, amely a konfigurációs beállításokban megadott. A tároló automatikusan létrejön, amikor a feladat elindul. Ha a feladat levan állítva, vagy hibás állapotba kerül, az automatikusan létrehozott tárolók törlődnek a feladat újraindításakor.  

Ha a referenciaadatok lassan változó adatkészlet, rendszeresen frissítenie kell a feladatban használt pillanatképet. A Stream Analytics lehetővé teszi a frissítési gyakoriság beállítását az Azure SQL Database bemeneti kapcsolat konfigurálásakor. A Stream Analytics futásidejű lekérdezi az Azure SQL-adatbázis a frissítési gyakoriság által megadott időközönként. A támogatott leggyorsabb frissítési gyakoriság percenként egyszer érhető el. Minden frissítéshez a Stream Analytics egy új pillanatképet tárol a megadott tárfiókban.

A Stream Analytics két lehetőséget biztosít az Azure SQL-adatbázis lekérdezéséhez. A pillanatkép-lekérdezés kötelező, és minden feladatban szerepelnie kell. A Stream Analytics rendszeresidőközönként futtatja a pillanatkép-lekérdezést a frissítési időköz alapján, és a lekérdezés (a pillanatkép) eredményét használja referenciaadatkészletként. A pillanatkép-lekérdezésnek a legtöbb forgatókönyvnek megfelelőnek kell lennie, de ha nagy adatkészletekkel és gyors frissítési gyakorisággal merül fel, használhatja a különbözeti lekérdezést. A referencia-adatkészlet visszaadásához 60 másodpercnél tovább igénybe vehet lekérdezések időhosszabbítást eredményeznek.

A különbözeti lekérdezési beállítással a Stream Analytics először futtatja a pillanatkép-lekérdezést, hogy lepelegy alapszintű referencia-adatkészletet. Után a Stream Analytics rendszeresen futtatja a különbözeti lekérdezést a frissítési időköz alapján a növekményes módosítások lekéréséhez. Ezek a növekményes módosítások folyamatosan alkalmazzák a referenciaadatkészletet, hogy naprakészek maradjanak. A különbözeti lekérdezés használata csökkentheti a tárolási költségeket és a hálózati I/O-műveleteket.

### <a name="configure-sql-database-reference"></a>SQL-adatbázis hivatkozásának konfigurálása

Az SQL Database referenciaadatainak konfigurálásához először létre kell **hoznia a referenciaadatok** bevitelét. Az alábbi táblázat ismerteti az egyes tulajdonságokat, amelyeket meg kell adnia, miközben létrehozza a referencia-adatbevitelt a leírásával. További információ: [Referenciaadatok használata SQL-adatbázisból egy Azure Stream Analytics-feladathoz](sql-reference-data.md)című témakörben talál.

Használhatja [az Azure SQL Database felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) referencia-adatbevitelként. Konfigurálnia kell [a nyilvános végpontot az Azure SQL Database felügyelt példányában,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) majd manuálisan kell konfigurálnia a következő beállításokat az Azure Stream Analytics-ben. Az SQL Servert futtató, adatbázist tartalmazó virtuális gépet az alábbi beállítások manuális konfigurálásával is támogatja.

|**Tulajdonság neve**|**Leírás**  |
|---------|---------|
|Bemeneti alias|A feladatlekérdezésben a bemenetre való hivatkozáshoz használt rövid név.|
|Előfizetés|Válassza ki az előfizetését|
|Adatbázis|Az Azure SQL-adatbázis, amely tartalmazza a referenciaadatokat. Az Azure SQL Database felügyelt példánya esetén meg kell adnia a 3342-es portot. Például *sampleserver.public.database.windows.net,3342*|
|Felhasználónév|Az Azure SQL-adatbázishoz társított felhasználónév.|
|Jelszó|Az Azure SQL-adatbázishoz társított jelszó.|
|Rendszeres frissítés|Ezzel a beállítással frissítési gyakoriságot választhat. Ha a "Be" lehetőséget választja, megadhatja a frissítési gyakoriságot a DD:ÓÓ:PP-ben.|
|Pillanatkép-lekérdezés|Ez az alapértelmezett lekérdezési beállítás, amely beolvassa a referenciaadatokat az SQL-adatbázisból.|
|Különbözeti lekérdezés|A speciális forgatókönyvek nagy adatkészletek és a rövid frissítési gyakoriság, válassza ki, hogy adjunk hozzá egy különbözeti lekérdezést.|

## <a name="size-limitation"></a>Méretkorlátozás

A Stream Analytics **300 MB-os maximális méretű**referenciaadatokat támogat. A referenciaadatok maximális méretének 300 MB-os határa csak egyszerű lekérdezésekkel érhető el. Mivel a lekérdezés összetettsége az állapotalapú feldolgozást is magában foglalja, például az ablakos aggregátumokat, az időbeli illesztéseket és az időbeli analitikus függvényeket, a referenciaadatok maximális támogatott mérete várhatóan csökken. Ha az Azure Stream Analytics nem tudja betölteni a referenciaadatokat, és összetett műveleteket végrehajtani, a feladat elfogy a memória, és nem sikerül. Ilyen esetekben az SU % kihasználtsági mutató eléri a 100%-ot.    

|**Streamelési egységek száma**  |**Kb. Maximális méret támogatott (MB-ban)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 és azon túl   |300   |

A 6-ot meghaladó számú streamelési egység növelése nem növeli a referenciaadatok maximális támogatott méretét.

A hivatkozási adatokhoz nem áll rendelkezésre a tömörítés támogatása. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
