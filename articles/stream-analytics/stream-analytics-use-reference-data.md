---
title: Referenciaadatok használata az Azure Stream Analyticsben keresések
description: Ez a cikk ismerteti, hogyan használható a referenciaadatok található, vagy a lekérdezés egy Azure Stream Analytics-feladat adatait.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 4ddbec6b163a939c1663630e39e89140ac6f7efe
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546475"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>A Stream Analytics keresések referenciaadatok használata
Referenciaadatok (más néven egy keresési táblázat) egy véges adatkészlet, amely statikus vagy lassan változó természetű, használja a keresés végrehajtásához vagy korrelációját, ha az adatfolyamban. Például egy IoT-forgatókönyvet, sikerült (amelyek nem változnak gyakran) vonatkozó metaadatokat tárolnak a referenciaadatok és csatlakozik azt a valós idejű IoT-adatfolyamaiból. Az Azure Stream Analytics közel valós idejű adatfolyam-feldolgozás eléréséhez a memóriában referenciaadatok tölti be. Győződjön meg arról, hogy az Azure Stream Analytics-feladat a referenciaadatok, az általában használhat egy [referencia-adatok csatlakozzon](https://msdn.microsoft.com/library/azure/dn949258.aspx) a lekérdezésben. 

Stream Analytics támogatja az Azure Blob storage és Azure SQL Database, a tárolási réteget a Referenciaadatoknál. Akkor is átalakítása, illetve hivatkozás adatokat másol az Azure Data Factory használata a Blob storage [bármely számos felhőalapú és helyszíni adattárak](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referenciaadatok növekvő sorrendben, a dátum/idő, a blob neve (a bemeneti konfigurációjában meghatározott) blobok sorozataként van modellezve. Ez **csak** dátum/idő használatával, a feladatütemezés végén hozzáadását támogatja **nagyobb** fut, a megadott sorrendben az utolsó blob.

### <a name="configure-blob-reference-data"></a>Referenciaadatok blob konfigurálása

A referenciaadatok konfigurálásához először hozzon létre egy bemeneti típusú **referenciaadatok**. Az alábbi táblázat azt ismerteti, hogy minden egyes tulajdonság, meg kell adnia a bemeneti referenciaadatok létrehozásakor, a Leírás:

|**Tulajdonság neve**  |**Leírás**  |
|---------|---------|
|Input Alias (Bemeneti alias)   | A bemeneti hivatkozhat a feladat lekérdezésben használt rövid név.   |
|Tárfiók   | Hol találhatók a BLOB storage-fiók neve. Ha a Stream Analytics-feladatot az azonos előfizetésben, kiválaszthatja a legördülő listából.   |
|Tárfiók kulcsa   | A storage-fiókhoz társított titkos kulcs. Ez automatikusan tölti fel a rendszer-e a tárfiók ugyanahhoz az előfizetéshez tartozik, mint a Stream Analytics-feladatot.   |
|Tároló   | Tárolók biztosítják a Microsoft Azure Blob service-ben tárolt blobok logikai jellegű csoportosítását. A Blob szolgáltatáshoz feltölt egy blobot, ha meg kell adnia egy adott blob-tárolót.   |
|Path Pattern (Elérésiút-minta)   | A megadott tárolóban található blobok helyének azonosításához használt elérési útja. Az elérési útban dönthet úgy, hogy a következő 2 változó egy vagy több példányát adhatja meg:<BR>{date}, {time}<BR>1. példa: products/{date}/{time}/product-list.csv<BR>2. példa: products/{date}/product-list.csv<BR>3. példa: product-list.csv<BR><br> Ha a blob nem létezik a megadott elérési úton, a Stream Analytics-feladat elérhető legyen, a BLOB határozatlan ideig vár.   |
|Dátumformátum: [opcionális]   | Ha a megadott elérési út mintája belül használta {a(z) date}, majd kiválaszthatja a dátumformátum, amelyben a blobok vannak rendszerezve a támogatott formátumok a legördülő listából.<BR>Példa: ÉÉÉÉ/hh/nn, hh/nn/éééé, stb.   |
|[Opcionális] időformátum   | Ha a megadott elérési út mintája belül használt {time}, majd kiválaszthatja az időformátum, amelyben a blobok vannak rendszerezve a támogatott formátumok a legördülő listából.<BR>Példa: HH, ÓÓ/pp vagy HH: mm-es.  |
|Eseményszerializációs formátum   | Annak érdekében, hogy a lekérdezések a várt módon működjenek, a Stream Analyticsnek tudnia kell, melyik szerializálási formátumot használja a bejövő adatfolyamokhoz. A Referenciaadatoknál, a támogatott formátumok a következők CSV és JSON-ban.  |
|Encoding   | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.  |

### <a name="static-reference-data"></a>Statikus referenciaadatok

A referenciaadatok várhatóan nem változik, ha majd támogatása statikus hivatkozás adatokat adjon meg egy statikus útvonalat a bemeneti konfigurációban engedélyezve van. Az Azure Stream Analytics szerzi be a megadott elérési úton található blob. {date} és {Time időponttal}-behelyettesítési jogkivonatok nem szükséges. A referenciaadatok nem módosítható a Stream Analytics. Egy statikus referenciaadatok blob felülírása, ezért nem ajánlott.

### <a name="generate-reference-data-on-a-schedule"></a>Referenciaadatok ütemezés létrehozása

Lassan változó adatkészlet a referenciaadatok esetén majd támogatja a referencia-adatok engedélyezve van a bemeneti konfigurációját a {date} segítségével egy elérésiút-minta megadásával frissítését, és helyettesítési tokenek {idő}. Stream Analytics szerzi be a frissített referenciaadatok adatok definíciókat az elérésiút-minta alapján. Például egy mintát, `sample/{date}/{time}/products.csv` az a dátum formátuma **"Éééé-hh-nn"** és a egy idő formátumát **"HH-mm"** arra utasítja a Stream Analytics a frissített blob csomópontmetrikák `sample/2015-04-16/17-30/products.csv` , 17:30-kor. április 16 , 2015 UTC időzóna szerint.

Az Azure Stream Analytics automatikus vizsgálatokat végez a frissített a referenciaadat blobok számára egy egy perces időközönként.

> [!NOTE]
> Jelenleg Stream Analytics-feladatok keresse meg a blob frissítés csak akkor, ha a gépek idejét az idő a blob nevében kódolású állomásra. Például a feladat megkeresi `sample/2015-04-16/17-30/products.csv` , amint lehetséges, de nem régebbi, mint 5:30-kor 2015. április 16 UTC időzónát. Akkor *soha nem* keresse meg a korábban felderített az utolsót kódolt időt egy blobot.
> 
> Például Miután a feladat megkeresi a blob `sample/2015-04-16/17-30/products.csv` , figyelmen kívül hagyja az 17:30-kor 2015. április 16-nál korábbi kódolt dátum fájlokat, ha egy késői érkező `sample/2015-04-16/17-25/products.csv` blob jön létre ugyanabban a tárolóban a feladat nem használja őket.
> 
> Hasonlóképpen ha `sample/2015-04-16/17-30/products.csv` , 10:03 PM 2015. április 16. csak előállított, de nincs korábbi dátummal blob nem található a tárolóban, a feladat indítása: 10:03 PM 2015. április 16. ezt a fájlt használja, és addig előző referenciaadatokat kell felhasználnia.
> 
> Ez a kivétel a feladatnak szüksége van az adatok újbóli feldolgozása visszaállítja az időben, vagy ha a feladat első elindult. Megadott kezdési időpontban a feladat a legutóbbi blob előállított, mielőtt a feladat kezdési időpont van megadva a keresi. Ez azért történt, győződjön meg arról, hogy van egy **nem üres** referencia-adatkészlete a feladat indításakor. Ha nem talál egy, a feladat jeleníti meg a következő diagnosztika: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Az Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) létrehozásához a Stream Analytics referencia-adatok definíciók frissítéséhez szükséges frissített blobok vezénylésére is használható. A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory támogatja [csatlakozik a nagy mennyiségű felhő alapú és a helyszíni adattárak](../data-factory/copy-activity-overview.md) és az adatok egyszerű áthelyezése megadott rendszeres ütemezés szerint. További információk és részletes útmutatást a Data Factory-folyamatok beállítása a Stream Analytics szolgáltatásnak, amely egy előre meghatározott ütemezés szerint frissíti a referenciaadatokat létrehozásához tekintse meg ezt [GitHub-minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Tippek a referenciaadatok blob frissítése

1. Ne írja felül a referenciaadat blobok, mivel ezek nem módosítható.
2. Az ajánlott módszer a referenciaadatok frissítése, hogy:
    * Használja a {date} / {idő} az elérésiút-minta
    * Adjon hozzá egy új blob tárolót és az elérési út minta a feladat bemeneti meghatározott
    * Egy dátum/idő használata **nagyobb** fut, a megadott sorrendben az utolsó blob.
3. A referenciaadat blobok vannak **nem** a blob "Utolsó módosítás" idő szerint, de kizárólag a rendezett dátumát és időpontját, a blob a megadott nevet használja a {date} és {time} helyettesítések.
3. Ne kelljen lista nagyszámú blobbal, fontolja meg, amelynek feldolgozása már nem történik nagyon régen blobok törlése. Vegye figyelembe, hogy ASA mehet kockapartíció újbóli például újraindítás bizonyos esetekben kis mértékben rendelkezik.

## <a name="azure-sql-database-preview"></a>Az Azure SQL Database (előzetes verzió)

Az Azure SQL Database referenciaadatok a Stream Analytics-feladat által beolvasott és pillanatképet készít a memóriában feldolgozáshoz tárolja. A referenciaadatok pillanatképe is egy tároló, amely a konfigurációs beállításaiban megadott tárfiókban tárolja. A tároló az automatikusan létrehozott a feladat indításakor. Ha a feladat leállt, vagy sikertelen állapotba kerül, az automatikusan létrehozott tárolókat a feladat újraindításakor törli.  

Lassan változó adatkészlet a referenciaadatok esetén időközönként frissíti a pillanatképet, a feladat használt van szükség. Stream Analytics lehetővé teszi az Azure SQL Database bemeneti kapcsolat konfigurálásakor beállított egy frissítési gyakoriság. A Stream Analytics modul az Azure SQL Database lekérdezi a frissítési gyakoriság a megadott időközönként. A leggyorsabb támogatott frissítési gyakoriság percenként egyszer történik. Minden frissítés egy új pillanatkép Stream Analytics a megadott tárfiók tárolja.

Stream Analytics lekérdezése az Azure SQL Database két lehetőséget biztosít. A pillanatkép-lekérdezés megadása kötelező, és szerepelnie kell minden egyes feladat. Stream Analytics rendszeres időközönként a frissítési gyakoriság alapján a pillanatkép-lekérdezést, és a lekérdezés (Pillanatkép) eredményét használja, mint a referencia-adatkészlet. A pillanatkép-lekérdezést kell fér legtöbb forgatókönyvben, de ha nagy méretű adatkészleteket és a gyors frissítés gyakorisága a teljesítményproblémákat tapasztal, használhatja a különbözeti lekérdezés beállítást. Vissza a referencia-adatkészlet több mint 60 másodperc lekérdezések időtúllépés eredményez.

A különbözeti lekérdezés lehetőséggel a Stream Analytics a pillanatkép-lekérdezés kezdetben az alapkonfiguráció referencia-adatkészlet futtat. Után a Stream Analytics futtatja a különbözeti lekérdezés rendszeres időközönként alapján a frissítési időköz beolvasni a növekményes változásokat. Ezek a növekményes változásokat folyamatosan lépnek folyamatosan frissíteni a referencia-adatkészlet. Változáslekérdezések használata segíthet a tárolási költségek csökkentése és a hálózati i/o-műveletek.

### <a name="configure-sql-database-reference"></a>Konfigurálja az SQL Database-referencia

Az SQL Database referenciaadatok konfigurálásához először létrehozásához szükséges **referenciaadatok** bemeneti. Az alábbi táblázat azt ismerteti, hogy minden egyes tulajdonság, meg kell adnia a bemeneti referenciaadatok létrehozásakor, a leírás. További információkért lásd: [referenciaadatok használata az Azure Stream Analytics-feladat egy SQL Database-ből](sql-reference-data.md).

|**Tulajdonság neve**|**Leírás**  |
|---------|---------|
|Bemeneti alias|A bemeneti hivatkozhat a feladat lekérdezésben használt rövid név.|
|Előfizetés|Válassza ki az előfizetését|
|Adatbázis|Az Azure SQL-adatbázis, amely a referencia-adatokat tartalmaz.|
|Felhasználónév|Az Azure SQL Database társított felhasználónév.|
|Jelszó|Az Azure SQL Database társított jelszót.|
|Rendszeres frissítés|Ezzel a beállítással kiválaszthat egy frissítési gyakoriság. "A" kiválasztása lehetővé teszi, hogy adja meg a frissítési gyakoriság DD:HH:MM.|
|Pillanatkép-lekérdezés|Ez a beállítás az alapértelmezett lekérdezés, amely a referenciaadatok lekéri az SQL Database-ből.|
|Változáslekérdezés|Frissítési időköz speciális forgatókönyvek a nagy méretű adatkészleteket és a egy rövid, felvehet egy különbözeti lekérdezés.|

## <a name="size-limitation"></a>Korlátozás

Stream Analytics támogatja a referenciaadatok **mérete 300 MB**. A referenciaadatokkal maximális mérete 300 MB határértéke csak az egyszerű lekérdezési elérhető. Tartalmazza az állapot-nyilvántartó feldolgozó, például az ablakos összesítéseket, az időalapú illesztéseket és historikus elemzési funkciók, növeli a lekérdezések összetettsége, valószínű, hogy a maximális támogatott mérete referencia-adatok csökken. Ha az Azure Stream Analytics nem a referencia-adatok betöltése és összetett műveletek végrehajtására, a feladat elfogy a memória, és sikertelen lesz. Ezekben az esetekben az SU % Utilization metrika eléri a 100 %-os.    

|**A folyamatos átviteli egységek száma**  |**KB. maximális mérete (MB-ban) támogatott**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 és azt követően   |300   |

Egy feladat túl 6 Streamelési egységek számának növelése növeli referenciaadatokkal maximális támogatott mérete.

A tömörítés támogatása nem érhető el a referenciaadatoknál. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
