---
title: Kimenő adatait az Azure Stream Analytics ismertetése
description: Ez a cikk ismerteti az adatok kimeneti beállításai az Azure Stream Analytics, mint a Power BI elemzési eredmények érhető el.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: 03871c3f3627e85cc2af2f05a5fba38bd8069a15
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609489"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Kimenő adatait az Azure Stream Analytics ismertetése
Ez a cikk ismerteti az elérhető az Azure Stream Analytics-feladat kimeneti típusait. Kimenetek segítségével tárolhatja, és a Stream Analytics-feladat eredményének mentése. A kimeneti adatok használatával teheti további üzleti elemzés és az adattárház az adatokat.

Amikor a Stream Analytics-lekérdezés, tekintse meg a kimeneti neve használatával a [be záradék](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Feladatonként egy egyetlen kimeneti vagy streamelési feladat (Ha szüksége van rá) több INTO záradék a lekérdezés megadásával több kimenetek is használhatja.

Létrehozása, módosítása és tesztelése a Stream Analytics-feladat kimenete, használhatja a [az Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell-lel](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API-val](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), és [a Visual Studio](stream-analytics-quick-create-vs.md).

Néhány kimenetek típusok támogatása [particionálás](#partitioning). [Kimeneti köteg méretek](#output-batch-size) eltérőek lehetnek a teljesítmény optimalizálása.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics támogatja [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Az Azure Data Lake Store egy vállalati szintű, nagy kapacitású adattár a big data-elemzési célokra. Data Lake Store segítségével bármilyen méretű, típusú és műveleti és felderítési jellegű feldolgozási sebességét a adatait tárolja. Stream Analytics a Data Lake Store jogosultsággal rendelkezik.

A Stream Analytics az Azure Data Lake Store-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

### <a name="authorize-an-azure-data-lake-store-account"></a>Az Azure Data Lake Store-fiók engedélyezése

1. Data Lake Store mint kimenet az Azure Portalon válassza ki, ha kéri, hogy engedélyezze a kapcsolatot egy meglévő Data Lake Store-példány.

   ![Engedélyezze a Data Lake Store-kapcsolatot](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Ha már rendelkezik hozzáféréssel a Data Lake Store, válassza ki a **engedélyezése most**. Oldal megjelenik, és azt jelzi, hogy **engedélyezési átirányítása**. Miután engedélyezési sikeres, megjelenik az oldal, amely lehetővé teszi, hogy a Data Lake Store kimenetének konfigurálásához.

3. A Data Lake Store-fiók hitelesítése után a tulajdonságokat a Data Lake Store kimeneti konfigurálható.

   ![Data Lake Store meghatározni a Stream Analytics-kimenetben](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

A következő táblázat felsorolja a tulajdonságnevek és a hozzájuk tartozó leírások a Data Lake Store kimenetének konfigurálásához.   

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A Data Lake Store lekérdezés kimenete a lekérdezésekben használt rövid név. |
| Fióknév | A Data Lake Store-fiókot, ahol küldése folyamatban van a kimenetben neve. Megjelenik egy legördülő listából válassza ki az előfizetésében elérhető Data Lake Store-fiókok. |
| Elérési út előtagmintája | A fájl elérési útja, amely a fájlok a megadott Data Lake Store-fiókon belül írására szolgál. A {date} egy vagy több példányát adhatja meg, és {time} változókat:<br /><ul><li>1. példa: mappa1/naplók / {dátum} / {idő}</li><li>2. példa: mappa1/naplók / {dátum}</li></ul><br />A létrehozott mappaszerkezetet időbélyegzőjét nem helyi idő és követi.<br /><br />Ha a fájl elérési út mintája nem tartalmaz egy záró perjellel (/), a legutóbbi minta a fájl elérési útját a fájl neve előtag számít. <br /><br />Új fájlok jönnek létre ilyen körülmények között:<ul><li>A kimeneti séma módosítása</li><li>Külső vagy belső indítsa újra a feladat</li></ul> |
| Dátumformátum | Választható. Ha a dátum jogkivonat azon előtag elérési útja, kiválaszthatja a dátumformátum, amelyben a fájlok vannak rendszerezve. Példa: ÉÉÉÉ/HH/NN |
|Időformátum | Választható. Ha az idő jogkivonat azon előtag elérési útja, adja meg az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | A kimeneti adatokat szerializálási formátuma. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott.|
| Encoding | Ha a CSV vagy JSON formátumot használja, egy kódolást kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.|
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. Stream Analytics egy közös kihagyni kívánt határolók száma támogatja a CSV-adatokat szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra.|
| Formátum | Csak a JSON-szerializálás alkalmazható. **Sorral elválasztott** Megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. **Tömb** Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű használjon JSON-sor elválasztott, mert nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben.|

### <a name="renew-data-lake-store-authorization"></a>Data Lake Store engedély megújítása
A Data Lake Store-fiók hitelesítse magát újra, ha a jelszó megváltozott, mivel a feladat létrehozásának vagy utolsó hitelesített kell. Ha Ön nem újrahitelesítéséhez, a feladat nem állít elő a kimeneti eredmények, és akkor jelez hibát, amely azt jelzi, hogy a művelet naplók reauthorization van szükség. 

Jelenleg a hitelesítési jogkivonat kell manuálisan frissíteni kell a Data Lake Store-kimenet az összes feladat 90 naponta. Ez a korlátozás által szel kiküszöbölhetők [hitelesítéshez felügyelt identitás (előzetes verzió)](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls).

Engedély megújítása:

1. Válassza ki **leállítása** leállítani a feladatot.
1. Nyissa meg a Data Lake Store kimeneti, és válassza ki a **engedély megújítása** hivatkozásra.

   Egy rövid ideig, egy előugró jelzi **engedélyezési átirányítása**. Ha az engedélyezési művelet sikeres, az oldal azt jelzi, hogy **engedélyezési sikeresen megújítva** és automatikusan bezáródik. 
   
1. Válassza ki **mentése** az oldal alján. Ezután indítsa újra a feladatot a **leállt utoljára** adatvesztés elkerülése érdekében.

![A kimenet a Data Lake Store engedély megújítása](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
Használhat [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) kimeneteként, amely a relációs jellegű adatokhoz vagy alkalmazásokhoz, amelyek egy relációs adatbázisban szolgáltatott tartalmaktól függnek. Stream Analytics-feladatok írni egy létező táblázat, SQL Database-ben. A következő tábla sémáját pontosan egyeznie kell a mezőket és azok típusát, a feladat kimenetének. Azt is megadhatja [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) keresztül az SQL Database kimenetként kimenetet. Írási teljesítmény javítására kapcsolatos további információkért tekintse meg a [Stream Analytics az Azure SQL Database kimenetként](stream-analytics-sql-output-perf.md) cikk. 

A következő táblázat felsorolja a tulajdonságnevek és a egy SQL Database-kimenet létrehozása leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete ehhez az adatbázishoz a lekérdezésekben használt rövid név. |
| Adatbázis | Ahol a kimeneti küld az adatbázis neve. |
| Kiszolgálónév | Az SQL Database-kiszolgáló neve. |
| Felhasználónév | A felhasználónév, amely írási hozzáféréssel rendelkezik az adatbázishoz. Stream Analytics csak az SQL-hitelesítést támogatja. |
| Jelszó | A jelszó az adatbázishoz való csatlakozáshoz. |
| Tábla | A tábla neve, ahol a kimeneti íródik. A tábla neve a kis-és nagybetűket. Ez a tábla sémája pontosan egyeznie kell a mezőket és azok típusát, amely létrehozza a feladat kimenetének számát. |
|Partícióséma öröklése| Örökli az előző lekérdezési lépésre, a particionálási séma az a tábla több írók teljes párhuzamos topológia engedélyezése beállítást. További információkért lásd: [az Azure SQL Database az Azure Stream Analytics-kimenetet](stream-analytics-sql-output-perf.md).|
|Egyezés kötegek száma| A javasolt korlát küldi el minden tömeges rekordok száma a tranzakciós beszúrása.|

> [!NOTE]
> Jelenleg a Stream Analytics-feladat kimenete az Azure SQL Database-ajánlat esetén támogatott. Az Azure virtuális gépként csatolt adatbázist tartalmazó SQL Server szoftvert futtató nem támogatott. Ez a jövőbeni kiadásokban változhat.
>

## <a name="blob-storage"></a>Blob Storage
Az Azure Blob storage nagy mennyiségű strukturálatlan adat tárolása a felhőben egy költséghatékony és méretezhető megoldást kínál. A Blob storage és a használatát bemutató, lásd: [blobok használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Az alábbi táblázat a tulajdonság nevét és a hozzájuk tartozó leírások létrehozásához egy blob kimeneti.

| Tulajdonság neve       | Leírás                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Kimeneti alias        | A lekérdezés kimenete az a blob storage-lekérdezésekben használt rövid név. |
| Tárfiók     | A tárfiókot, ahol a kimeneti küld neve.               |
| Tárfiók kulcsa | A storage-fiókhoz társított titkos kulcs.                              |
| Tároló   | Az Azure Blob service-ben tárolt blobok logikai jellegű csoportosítását. A Blob szolgáltatáshoz feltölt egy blobot, ha meg kell adnia egy adott blob-tárolót. |
| Elérésiút-minta | Választható. A fájl elérésiút-minta, amely a megadott tárolóban található blobok írást. <br /><br /> A dátum és idő változó egy vagy több példányát adhatja meg, amelyek a blobok írt használandó választhat az elérésiút-minta: <br /> {date}, {time} <br /><br />Egyéni blob particionálás használatával adjon meg egy egyéni {mező} neve az esemény adatokból a partíció blobokhoz. A mező neve alfanumerikus és szóközöket, kötőjeleket és aláhúzásjeleket tartalmazhatnak. Egyéni mezők korlátozásai a következők: <ul><li>A mező nevei nem kis-és nagybetűket. Például a szolgáltatás nem tudja megkülönböztetni a oszlop "ID" és "azonosítójú oszlophoz."</li><li>Beágyazott mezői nem engedélyezettek. Ehelyett használjon egy alias a feladat lekérdezésben "simítják" mező.</li><li>Kifejezések nem használható a mező nevét.</li></ul> <br />Ez a funkció lehetővé teszi, hogy egyéni dátum és idő formátumú specifikátor konfigurációk az elérési út használata. Egyéni dátum és idő formátumban kell lennie a megadott egyenként, kapcsos a {dátum és idő:\<specifikátor >} kulcsszót. Megengedett bemenetek \<specifikátor >: éééé, MM, M, nn, d, HH, H, mm, m, ss vagy s. A {dátum és idő:\<specifikátor >} kulcsszó használható többször az elérési út egyéni dátum/idő konfigurációk alkotnak. <br /><br />Példák: <ul><li>1. példa: fürt1/naplók / {dátum} / {idő}</li><li>2. példa: fürt1/naplók / {dátum}</li><li>3. példa: cluster1 / {client_id} / {dátum} / {idő}</li><li>4. példa: cluster1 / {datetime:ss} / {myField} amennyiben a lekérdezés végrehajtása: Válassza ki data.myField, a bemeneti; myField</li><li>5. példa: cluster1/év = {datetime:yyyy} / hónap = {datetime:MM} / nap = {datetime:dd}</ul><br />A létrehozott mappaszerkezetet időbélyegzőjét nem helyi idő és követi.<br /><br />Az alábbi konvenció fájlelnevezésnél használja: <br /><br />{Elérési út előtagja Pattern}/schemaHashcode_Guid_Number.extension<br /><br />A példában a kimeneti fájlok:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />Ezen szolgáltatásáról kapcsolatos további információkért lásd: [Azure Stream Analytics egyéni blob kimeneti particionálás](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Dátumformátum | Választható. Ha a dátum jogkivonat azon előtag elérési útja, kiválaszthatja a dátumformátum, amelyben a fájlok vannak rendszerezve. Példa: ÉÉÉÉ/HH/NN |
| Időformátum | Választható. Ha az idő jogkivonat azon előtag elérési útja, adja meg az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding    | Ha a CSV vagy JSON formátumot használja, egy kódolást kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter   | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. Stream Analytics egy közös kihagyni kívánt határolók száma támogatja a CSV-adatokat szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum      | Csak a JSON-szerializálás alkalmazható. **Sorral elválasztott** Megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. **Tömb** Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű használjon JSON-sor elválasztott, mert nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben. |

A Blob storage használja kimenetként, amikor új fájl létrehozása a blob a következő esetekben:

* Ha a fájl meghaladja a maximális engedélyezett blokkok (jelenleg 50 000 blokk). Blokkok maximálisan megengedett számának előfordulhat, hogy eléri a maximális megengedett blob mérete elérése nélkül. Például ha a kimeneti sebessége nagy, láthatja, hogy további bájtok száma letiltása, és a fájl mérete nagyobb. Ha a kimeneti aránya alacsony, minden egyes rendelkezik kevesebb adatot, és a fájl mérete kisebb.
* Ha egy séma módosul, a kimenetben, és a kimeneti formátum rögzített sémát (fürt megosztott kötetei szolgáltatás és az Avro) van szükség.
* Ha a feladat újraindításakor kívülről, leállíthatja és elindíthatja azt egy felhasználó vagy belső rendszer-karbantartási vagy a hiba helyreállítási.
* Ha a lekérdezés teljes particionálva van, valamint egy új fájlt hoznak létre az egyes kimeneti partíció.
* Ha a felhasználó töröl egy fájlt vagy egy tárolót a storage-fiók.
* Ha a kimenet az idő, az elérési út előtagmintája használ, és a egy új blob használatos, ha a lekérdezés helyezi át a következő órában.
* Ha a kimenet egy egyéni mező particionálva van, és új blob jön partíció kulcs if / nem létezik.
* Ha a kimeneti particionált által egy egyéni mezőt, ahol a partíció kulcs a számosság meghaladja a 8000-re, és a egy új blob jön partíció kulcsonként.

## <a name="event-hubs"></a>Event Hubs
A [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatás egy nagymértékben méretezhető közzétételi és előfizetési Eseménygyűjtő szolgáltatás. Másodpercenként több millió begyűjthesse. Egy eseményközpont kimenetként használata a bemenet egy másik folyamatos átviteli feladat válásakor a Stream Analytics-feladat kimenetét.

Adatfolyamok az event hubs konfigurálása kimenetként néhány paramétert kell.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A lekérdezés kimenete az eseményközpont lekérdezésekben használt rövid név. |
| Eseményközpont-névtér |Egy az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor egy eseményközpont-névtér is létrejött. |
| Event Hubs neve | Az event hub kimeneti neve. |
| Eseményközpont szabályzatának neve | A megosztott elérési házirendet, amely az event hubs hozhatók létre **konfigurálása** fülre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| Eseményközpont házirendjének kulcsa | A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést az eseményközpont-névteret. |
| Partíciókulcs oszlopa | Választható. Az event hub kimeneti partíciókulcsot tartalmazó oszlop. |
| Eseményszerializációs formátum | A kimeneti adatokat szerializálási formátuma. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding | A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum | Csak a JSON-szerializálás alkalmazható. **Sorral elválasztott** Megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. **Tömb** Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű használjon JSON-sor elválasztott, mert nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben. |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok csatolandó felhasználói tulajdonságként a kimenő üzenet helyett az adattartalomban. Ez a szolgáltatás további információ a szakaszban van [egyéni metaadat-tulajdonságot kimeneti](#custom-metadata-properties-for-output). |

## <a name="power-bi"></a>Power BI
Használhat [Power BI](https://powerbi.microsoft.com/) , az elemzési eredmények biztosított részletes megjelenítések élményt biztosít a Stream Analytics-feladat kimenetét. Ez a funkció a operatív irányítópultokat, a jelentéskészítéshez és a metrika adatvezérelt újságírás használható.

A Stream Analytics a Power BI-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

### <a name="authorize-a-power-bi-account"></a>Engedélyezze a Power BI-fiókkal
1. Az Azure Portalon kimenetként kiválasztásakor a Power BI, kér egy meglévő Power BI-felhasználók engedélyezése vagy egy új Power BI-fiók létrehozásához.
   
   ![A Power BI-felhasználók kimenetének konfigurálásához engedélyezése](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Hozzon létre egy új fiókot, ha Ön az még nem rendelkezik ilyennel, és válassza **engedélyezése most**. A következő oldal jelenik meg:
   
   ![A Power bi-bA az Azure-fiók hitelesítése](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Adja meg a munkahelyi vagy iskolai fiókkal, amelyek engedélyezik a Power BI-kimenet. Ha Ön még nem jelentkezett a Power bi-hoz, válassza ki a **regisztráció**. Az Azure-előfizetése fiókját, hogy Ön már bejelentkezett a munkahelyi vagy iskolai fiók, a Power bi-hoz használt eltérhet.

### <a name="configure-the-power-bi-output-properties"></a>A Power BI-kimenet tulajdonságainak konfigurálása
Miután a Power BI-fiókja hitelesített, a Power BI-kimenet konfigurálhatja a tulajdonságait. A következő táblázat felsorolja a tulajdonságnevek és a hozzájuk tartozó leírások, a Power BI-kimenet konfigurálása.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Adjon meg egy rövid nevet a lekérdezés kimenete a Power BI-kimenet lekérdezésekben használt. |
| Csoportos munkaterület |Ahhoz, hogy más Power BI-felhasználókkal rendelkező megosztási adatokat, válassza ki a csoportokat a Power BI-fiók belül vagy válasszon **saját munkaterület** Ha nem szeretne írni egy csoportot. Meglévő csoport frissítése a Power BI-hitelesítés megújítása igényel. |
| Adatkészlet neve |Adja meg a Power BI-kimenet használni kívánt adatkészlet nevét. |
| Tábla neve |Adja meg az adatkészletet a Power BI-kimenet alatt tábla nevét. A Stream Analytics-feladatok a Power BI-kimenet jelenleg csak egy tábla egy adatkészlet rendelkezhet. |

A Power BI-kimenet és az irányítópult konfigurálásának, olvassa az [Azure Stream Analytics és a Power BI](stream-analytics-power-bi-dashboard.md) cikk.

> [!NOTE]
> Nem kifejezetten az adatkészlet és tábla létrehozása a Power BI-irányítópulton. Az adatkészlet és a táblázat rendszer automatikusan feltölti, amikor a feladat elindult, és a feladat kimeneti szivattyútelepek elindítja a Power BI-bA. A feladat lekérdezési eredmény nem generál, ha az adatkészlet és a táblázat hozhatók létre. Ha a Power bi-ban már volt egy adatkészletet, és ezzel a névvel, a Stream Analytics-feladat a megadott tábla, a rendszer felülírja a meglévő adatokat.
>

### <a name="create-a-schema"></a>Hozzon létre egy sémát
Az Azure Stream Analytics egy Power BI adatkészlet és a táblasémát a felhasználó hoz létre, ha azok még nem léteznek. Minden más esetben a tábla frissül új értékek. Jelenleg csak egy tábla egy adatkészleten belül létezhet. 

A Power BI az első, érkezési idősorrendben történő (FIFO) megőrzési szabályzat használja. Egy tábla adatokat gyűjt 200 000 sort beolvasásig.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Konvertálja az adattípust a Stream Analytics a Power bi-bA
Az Azure Stream Analytics frissíti az adatmodell dinamikusan, futásidőben, ha a kimeneti sémával megváltozik. Oszlop neve megváltozik, oszlopok adattípus-módosítások, és hozzáadását és eltávolítását, az oszlopok összes nyomon követett.

Ez a táblázat ismerteti az adatok típuskonverziók a [Stream Analytics adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx) a Power bi-bA [Entity Data Model (EDM) típusok](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/entity-data-model), ha egy Power BI-adatkészlet és a tábla nem létezik.

A Stream Analytics | A Power bi-bA
-----|-----
bigint | Int64
típus: nvarchar(max) | Karakterlánc
dátum/idő | Dátum és idő
lebegőpontos | Dupla
Rekord tömb | A karakterlánc típusú, állandó érték "IRecord" vagy "IArray"

### <a name="update-the-schema"></a>A séma frissítése
Stream Analytics kikövetkezteti a kimenetben szereplő események első készlete alapján adatokat modellsémát. Később Ha szükséges, az adatok modellsémát frissül bejövő eseményeket, amelyek esetleg nem illik az eredeti séma befogadásához.

Kerülje a `SELECT *` lekérdezést, hogy a dinamikus séma frissítése a sorok között. Lehetséges teljesítményre gyakorolt hatása mellett, az eredményeket az idő a bizonytalanság eredményezhet. Válassza ki a pontos mezők jelenjenek meg a Power BI-irányítópulton. Ezenkívül az adatértékek felelnek meg a kiválasztott adatok típusa lehet.


Előző és aktuális | Int64 | Karakterlánc | Dátum és idő | Dupla
-----------------|-------|--------|----------|-------
Int64 | Int64 | Karakterlánc | Karakterlánc | Dupla
Dupla | Dupla | Karakterlánc | Karakterlánc | Dupla
Karakterlánc | Karakterlánc | Karakterlánc | Karakterlánc | Karakterlánc 
Dátum és idő | Karakterlánc | Karakterlánc |  Dátum és idő | Karakterlánc


### <a name="renew-power-bi-authorization"></a>A Power BI engedély megújítása
Ha a Power BI-fiókja jelszavát megváltozik, miután a Stream Analytics-feladat létrehozásának vagy utolsó hitelesített, hitelesítse magát újra a Stream Analytics szeretne. Ha az Azure multi-factor Authentication konfigurálva van az Azure Active Directory (Azure AD) bérlő, is újítsa meg a Power BI engedélyezési kéthetente kell. A probléma tünete nincs feladatkimenet és a "hitelesítés felhasználói hibája" a műveletnaplók:

  ![A Power BI hitelesítés felhasználói hiba](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

A probléma megoldásához állítsa le a futó feladatot, és nyissa meg a Power BI-kimenet. Válassza ki a **engedély megújítása** hivatkozásra, és indítsa újra a feladatot a **leállt utoljára** adatvesztés elkerülése érdekében.

  ![A kimenet a Power BI engedély megújítása](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Az Azure Table storage](../storage/common/storage-introduction.md) rugalmasan méretezhető, magas rendelkezésre állású tárolási kínál, hogy az alkalmazás automatikusan skálázhatja a felhasználói igények. A TABLE storage a séma kevesebb korlátozásaihoz a strukturált adatok számára is használhatja a Microsoft NoSQL kulcs-/ attribútumtárat store. Az Azure Table storage segítségével tárolja az adatok megőrzését és a hatékony lekéréséhez.

Az alábbi táblázat a tulajdonság nevét és a hozzájuk tartozó leírások létrehozásához egy tábla kimeneti.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete ezen táblatároló lekérdezésekben használt rövid név. |
| Tárfiók |A tárfiókot, ahol a kimeneti küld neve. |
| Tárfiók kulcsa |A storage-fiókhoz társított hozzáférési kulcs. |
| Tábla neve |A tábla neve. Ha még nem létezik a tábla jön létre. |
| Partíciókulcs |A partíciókulcsot tartalmazó kimeneti oszlop neve. A partíciókulcs az egyedi azonosító a partíció egy entitás elsődleges kulcsának első részét képező táblán belül. Fontos, hogy legfeljebb 1 KB méretű karakterláncérték. |
| Sorkulcs |A sorkulcsot tartalmazó kimeneti oszlop neve. A sorkulcs pedig egy partíción belül egy entitás egyedi azonosítója. Egy entitás elsődleges kulcsának második részét képezi. A sorkulcs pedig egy karakterláncérték, amely legfeljebb 1 KB méretű is lehet. |
| Köteg mérete |A kötegelt műveleti rekordok száma. Az alapértelmezett (100) érték elegendők a legtöbb feladatot. Tekintse meg a [kötegelt művelet specifikációja](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Ez a beállítás módosításával kapcsolatos további részletekért. |

## <a name="service-bus-queues"></a>Service Bus-üzenetsorok
[Service Bus-üzenetsorok](https://msdn.microsoft.com/library/azure/hh367516.aspx) kínálnak a FIFO üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Az üzenetek általában kapott és dolgozza fel a hozzáadásuk historikus, amelyben hozzá lettek adva a várólistát. Minden üzenetet kapott, és csak egy üzenetfogyasztó által feldolgozott.

Az alábbi táblázat a tulajdonság nevét és a hozzájuk tartozó leírások létrehozásához egy üzenetsor kimeneti.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete a Service Bus-üzenetsorba lekérdezésekben használt rövid név. |
| Service Bus-névtér |Egy az üzenetküldési entitások készletének tárolója. |
| Üzenetsor neve |A Service Bus-üzenetsor neve. |
| Sor házirendjének neve |Amikor létrehoz egy üzenetsort, akkor is létrehozhat megosztott elérési házirendeket az üzenetsor **konfigurálása** fülre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| Sor házirendjének kulcsa |A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést a Service Bus-névteret. |
| Eseményszerializációs formátum |A kimeneti adatokat szerializálási formátuma. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding |A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum |Csak az JSON típusú érvényes. **Sorral elválasztott** Megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. **Tömb** Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok csatolandó felhasználói tulajdonságként a kimenő üzenet helyett az adattartalomban. Ez a szolgáltatás további információ a szakaszban van [egyéni metaadat-tulajdonságot kimeneti](#custom-metadata-properties-for-output). |

A partíciók száma [a Service Bus-Termékváltozat és a mérete alapján](../service-bus-messaging/service-bus-partitioning.md). Partíciókulcs egyedi egész szám érték minden egyes partícióhoz.

## <a name="service-bus-topics"></a>Service Bus-témák
Service Bus-üzenetsorok, adjon meg egy-az-egyhez kommunikációs módszer a küldő a fogadó. [Service Bus-üzenettémakörök](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy-a-többhöz típusú kommunikációt biztosítanak.

A következő táblázat felsorolja a tulajdonságnevek és a hozzájuk tartozó leírások létrehozásához egy témakör-kimenet.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete a Service Bus-témakörbe lekérdezésekben használt rövid név. |
| Service Bus-névtér |Egy az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor egy Service Bus-névtér is létrejött. |
| Téma neve |A témakörök üzenetküldési entitások, hasonlók az event hubs és üzenetsorok. Az eszközök és szolgáltatások szolgáltatásból lettek kialakítva. A témakör létrehozását követően is rendelkezzen egy adott névvel. A témakörbe küldött üzenetek nem érhetők el, kivéve, ha egy előfizetés jön létre, ezért győződjön meg arról van egy vagy több előfizetés témakör alatt. |
| Téma házirendjének neve |Amikor létrehoz egy témát, akkor is létrehozhat megosztott elérési házirendeket a témakör a **konfigurálása** fülre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| Téma házirendjének kulcsa |A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést a Service Bus-névteret. |
| Eseményszerializációs formátum |A kimeneti adatokat szerializálási formátuma. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding |Ha a CSV vagy JSON formátumot használja, egy kódolást kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Tulajdonságoszlopok | Választható. Vesszővel tagolt oszlopok csatolandó felhasználói tulajdonságként a kimenő üzenet helyett az adattartalomban. Ez a szolgáltatás további információ a szakaszban van [egyéni metaadat-tulajdonságot kimeneti](#custom-metadata-properties-for-output). |

A partíciók száma [a Service Bus-Termékváltozat és a mérete alapján](../service-bus-messaging/service-bus-partitioning.md). A partíciós kulcs egyedi egész szám érték minden egyes partícióhoz.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) egy globálisan elosztott adatbázis-szolgáltatás, amely korlátlan, rugalmas skálázhatóságát szerte a világon, funkciókban gazdag lekérdezési és Automatikus indexelés sémafüggetlen adatmodellek keresztül biztosít. Azure Cosmos DB-hez gyűjtési beállítások a Stream Analytics kapcsolatos további információkért tekintse meg a [Stream Analytics az Azure Cosmos DB kimeneti](stream-analytics-documentdb-output.md) cikk.

A Stream Analytics az Azure Cosmos DB-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

> [!Note]
> Jelenleg az Azure Stream Analytics csak kapcsolatot támogat az Azure Cosmos DB az SQL API használatával.
> Más Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB-fiókokhoz létrehozott más API-kkal, az adatok esetleg nem megfelelően tárolni.

Az alábbi táblázat ismerteti a tulajdonságait, egy Azure Cosmos DB kimeneti létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Tekintse meg ezt a Stream Analytics-lekérdezés kimenete egy alias. |
| Sink (Fogadó) | Azure Cosmos DB. |
| Importálási beállítás | Válasszon **válassza ki Cosmos DB-előfizetéséből** vagy **adja meg a Cosmos DB-beállítások manuális**.
| Fiókazonosító | A neve vagy URI-t az Azure Cosmos DB-fiók végpontját. |
| Fiókkulcs | A közös hozzáférési kulcs az Azure Cosmos DB-fiókot. |
| Adatbázis | Az Azure Cosmos DB-adatbázis nevét. |
| Gyűjteménynévminta | A gyűjtemény nevét, vagy a használandó gyűjtemények minta. <br />A gyűjteménynév-formátum az opcionális {partition} token használatával, ahol a partíciók 0-tól kezdődnek hozhatnak létre. Két példa:  <br /><ul><li> _MyCollection_: Egy gyűjtemény "MyCollection" nevű léteznie kell.</li>  <li> _{Partition} MyCollection_: A particionálási oszlop alapján.</li></ul> A particionálási oszlop gyűjteményeknek létezniük kell: "MyCollection0," "MyCollection1", "MyCollection2," és így tovább. |
| Partíciókulcs | Választható. Ez szükséges csak akkor, ha a gyűjteménynévminta {partition} token használ.<br /> A partíciós kulcs pedig a kimeneti eseményekben a kimenet gyűjtemények közötti particionálására szolgáló kulcs megadásához használt mező neve.<br /> A kimenet egyetlen gyűjtemény bármilyen tetszőleges kimeneti oszlop is használhatja. Ilyen például, PartitionID azonosítóval. |
| Dokumentumazonosító |Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek alapulnak az elsődleges kulcs megadásához használt mező neve.

## <a name="azure-functions"></a>Azure Functions
Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerinti explicit kiépítésére vagy infrastruktúra kezelése nélkül futtathat. Lehetővé teszi az Azure-ban vagy a partner szolgáltatásokban bekövetkező események által aktivált kódok implementálhatók. Az Azure Functions a triggerekre való válaszadási képessége lehetővé teszi a természetes kimeneti az Azure Stream Analytics. A kimeneti adapter lehetővé teszi a felhasználók számára a Stream Analytics és az Azure Functions összekapcsolása, és futtathat szkripteket vagy kódrészleteket különféle eseményekre válaszként.

A Stream Analytics az Azure Functions-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

Az Azure Stream Analytics az Azure Functions via HTTP-eseményindító hív meg. Az Azure Functions kimeneti adapter érhető el a következő konfigurálható tulajdonsággal:

| Tulajdonság neve | Leírás |
| --- | --- |
| Függvényalkalmazás |Az Azure Functions-alkalmazás neve. |
| Függvény |Az Azure Functions-alkalmazásban a függvény neve. |
| Kulcs |Ha szeretné használni az Azure-függvény, egy másik előfizetésből, megteheti azáltal, hogy a kulcs a függvény eléréséhez. |
| Maximális kötegméret |Olyan tulajdonságot, amely lehetővé teszi az egyes kimeneti kötegek az Azure-függvény küldött maximális méretének beállítása. A bemeneti mértékegysége (bájt). Alapértelmezés szerint ez az érték a 262 144 bájt (256 KB). |
| Maximális kötegszám  |A tulajdonság, amely lehetővé teszi az Azure Functions küldött egyes kötegekben lévő események maximális számának megadása. Az alapértelmezett érték 100. |

Ha az Azure Stream Analytics kap egy 413 ("HTTP-kérelem túl nagy") egy Azure-függvény kivételt, csökkenti az Azure Functions részére küldött kötegek méretét. Az Azure-függvény kódját a kivétel használatával győződjön meg arról, hogy az Azure Stream Analytics nem küld e túl nagy kötegeket. Győződjön meg arról, hogy a függvényben használt maximális száma és mérete értékek konzisztens a Stream Analytics-portálon megadott értékekkel.

Olyan helyzet is, az nem található olyan esemény, ideje ablakban üzenetsorokra, ha nincs kimenet jön létre. Ennek eredményeképpen a **computeResult** függvény nincs neve. A beépített ablakos összesítő függvényekben összhangban az ezt a viselkedést.

## <a name="custom-metadata-properties-for-output"></a>Kimeneti metaadatok egyéni tulajdonságok 

Csatlakoztathat lekérdezés oszlopok felhasználó tulajdonságai, a kimenő üzenetek. Ezekben az oszlopokban nem nyissa meg a hasznos adatok. A tulajdonságok szerepelnek egy szótárban, a kimeneti üzenetek formájában. *Kulcs* oszlop neve és *érték* a szolgáltatástulajdonságok szótárába oszlop értéke. Minden Stream Analytics-adattípusok kivéve rekord és a tömb támogatottak.  

Támogatott kimenetek: 
* Service Bus-üzenetsor 
* Service Bus-téma 
* Eseményközpont 

A következő példában hozzáadjuk a két mező `DeviceId` és `DeviceStatus` metaadat. 
* Lekérdezés: `select *, DeviceId, DeviceStatus from iotHubInput`
* Kimeneti konfiguráció: `DeviceId,DeviceStatus`

![Tulajdonságoszlopok](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

Az alábbi képernyőképen látható kimeneti keretében az EventHub ellenőrzött üzenettulajdonságok [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer).

![Egyéni esemény tulajdonságai](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>Particionálás

A következő táblázat összefoglalja a partíció-támogatás és a kimeneti írók az egyes kimeneti száma:

| Kimenet típusa | Particionálási támogatása | Partíciókulcs  | Kimeneti írók száma |
| --- | --- | --- | --- |
| Azure Data Lake Store | Igen | Használja {a date} és {time} az elérési út előtagmintája tokeneket. Válassza ki a dátum formátuma éééé/hh/nn, nn/hh/éééé vagy hh-nn-éééé. Az időformátum ÓÓ használható. | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Igen | A PARTITION BY záradék a lekérdezés alapján. | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). További információ eléréséhez jobban írni átviteli teljesítmény tölt be adatokat az Azure SQL Database-be című témakörben talál [az Azure SQL Database az Azure Stream Analytics-kimenetet](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Igen | Használja {a date} és {time} token az esemény mezőiből az elérésiút-minta. Válassza ki a dátum formátuma éééé/hh/nn, nn/hh/éééé vagy hh-nn-éééé. Az időformátum ÓÓ használható. Kimeneti BLOB lehet particionálni egy egyéni esemény egyetlen attribútum {fieldname} vagy {dátum és idő:\<specifikátor >}. | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). |
| Azure Event Hubs | Igen | Igen | Partíció igazítás függően változik.<br /> A partíciókulcs event hub kimeneti egyaránt igazodik a felsőbb rétegbeli (korábbi) lekérdezési lépésre, írók száma esetén ugyanaz, mint az event hub kimeneti partíciók száma. Minden egyes író használja a [EventHubSender osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) eseményeket küldhet az adott partíció. <br /> Event hub kimeneti a partíciókulcs nem igazodik a felsőbb rétegbeli (korábbi) lekérdezés lépést, ha írók száma megegyezik a korábbi lépésben partíciók száma. Minden egyes író használja a [SendBatchAsync osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) a **EventHubClient** az események küldése az összes kimeneti partíciót. |
| Power BI | Nem | None | Nem alkalmazható. |
| Azure Table Storage | Igen | Minden olyan kimeneti oszlop.  | A bemeneti particionálási követi [teljes mértékben a lekérdezések párhuzamosíthatók](stream-analytics-scale-jobs.md). |
| Az Azure Service Bus-témakörbe | Igen | Automatikusan kiválasztja. A partíciók száma alapján a [Service Bus-Termékváltozat és a méret](../service-bus-messaging/service-bus-partitioning.md). A partíciós kulcs egyedi egész szám érték minden egyes partícióhoz.| Ugyanaz, mint a kimenet a témakör a partíciók száma.  |
| Az Azure Service Bus-üzenetsorba | Igen | Automatikusan kiválasztja. A partíciók száma alapján a [Service Bus-Termékváltozat és a méret](../service-bus-messaging/service-bus-partitioning.md). A partíciós kulcs egyedi egész szám érték minden egyes partícióhoz.| Ugyanaz, mint a kimeneti várólistában lévő partíciók száma. |
| Azure Cosmos DB | Igen | Használja a {partition} token a gyűjteménynévminta. A {partition} értéket a PARTITION BY záradék a lekérdezés alapul. | A bemeneti particionálási követi [teljes mértékben a lekérdezések párhuzamosíthatók](stream-analytics-scale-jobs.md). |
| Azure Functions | Nem | None | Nem alkalmazható. |

A konzolkimeneti adapter nincs particionálva, ha az egyik bemeneti partíciók adatainak hiánya miatt késői érkezési mennyi ideig akár késést. Ezekben az esetekben a kimeneti egyetlen író, amely szűk keresztmetszeteket okozhat a folyamatban, az egyesített. Késedelmes érkezési házirenddel kapcsolatos további tudnivalókért lásd: [Azure Stream Analytics esemény rendelés szempontok](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Kimeneti kötegmérete
Az Azure Stream Analytics használ a változó méretű kötegekben kimenetek írása és dolgozza fel. A Stream Analytics-motor általában egy üzenet nem írni egy időben, és kötegeket használja a hatékonyság. Ha a kimenő és bejövő események száma túl magas, a Stream Analytics nagyobb kötegek használ. Ha a kilépő üzenetek gyakorisága alacsony, kisebb kötegekben használ tartani alacsony késést.

Az alábbi táblázat ismerteti azokat a kimeneti kötegelés szempontjai:

| Kimenet típusa | Maximális üzenetméret | Köteg mérete optimalizálása |
| :--- | :--- | :--- |
| Azure Data Lake Store | Lásd: [korlátozza a Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits). | Használjon legfeljebb 4 MB / írási művelet. |
| Azure SQL Database | 10 000 sorok maximális száma kiszolgálónként egyetlen tömeges beszúrás.<br />100 minimális sorok száma egyetlen tömeges beszúrás. <br />Lásd: [korlátozza az Azure SQL](../sql-database/sql-database-resource-limits.md). |  Minden batch először tömeges köteg maximális mérete együtt. Akkor is feloszthatja a batch megfelezése (amíg el nem éri a minimális köteg mérete) – Újrapróbálkozást lehetővé tevő hiba esetén az SQL-alapú. |
| Azure Blob Storage | Lásd: [korlátozza az Azure Storage](../azure-subscription-service-limits.md#storage-limits). | A maximális blob blokkblob mérete 4 MB.<br />A maximális blobok bock száma 50 000. |
| Azure Event Hubs  | Egy üzenet 256 KB. <br />Lásd: [korlátozza az Event Hubs](../event-hubs/event-hubs-quotas.md). |  Bemeneti/kimeneti a particionálás nem igazítva, ha minden egyes esemény egyenként a csomagolt **EventData** és a egy köteg maximális mérete (prémium szintű termékváltozat 1 MB) legfeljebb küldi el. <br /><br />  Bemeneti/kimeneti particionálás van igazítva, ha több esemény van elhelyezve egy **EventData** példányt, az üzenetek maximális mérete legfeljebb és küldi el.  |
| Power BI | Lásd: [Power BI Rest API-korlátok](https://msdn.microsoft.com/library/dn950053.aspx). |
| Azure Table Storage | Lásd: [korlátozza az Azure Storage](../azure-subscription-service-limits.md#storage-limits). | Az alapértelmezett érték 100 entitást egyetlen tranzakciónként. Egy kisebb értékre, igény szerint konfigurálható. |
| Az Azure Service Bus-üzenetsorba   | Egy üzenet 256 KB.<br /> Lásd: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md). | Egy üzenet egy egyszeri esemény használja. |
| Az Azure Service Bus-témakörbe | Egy üzenet 256 KB.<br /> Lásd: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md). | Egy üzenet egy egyszeri esemény használja. |
| Azure Cosmos DB   | Lásd: [Azure Cosmos DB korlátozza](../azure-subscription-service-limits.md#azure-cosmos-db-limits). | Kötegméret írási és olvasási gyakorisága dinamikusan módosulnak az Azure Cosmos DB-válaszok alapján. <br /> A Stream Analytics nincs előre meghatározott korlátozások is. |
| Azure Functions   | | Az alapértelmezett batch mérete 262 144 bájt (256 KB). <br /> Az alapértelmezett események száma kötegenkénti érték 100. <br /> A Köteg mérete nem konfigurálható, és növelhető vagy csökkenthető, a Stream Analytics [kimeneti beállítások](#azure-functions).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> 
> [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
