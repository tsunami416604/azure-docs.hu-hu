---
title: Kimenő adatait az Azure Stream Analytics ismertetése
description: Ez a cikk ismerteti az adatok kimeneti beállításai az Azure Stream Analytics, mint a Power BI elemzési eredmények érhető el.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2018
ms.custom: seodec18
ms.openlocfilehash: 805df837d5d33c5f21799e39145c62e71afdb4b5
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231393"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Kimenő adatait az Azure Stream Analytics ismertetése
Ez a cikk bemutatja a kimenetek elérhető az Azure Stream Analytics-feladat különböző típusú. Kimenetek segítségével tárolhatja, és a Stream Analytics-feladat eredményének mentése. A kimeneti adatokat használja, végezhet további üzleti elemzés és az adattárház az adatokat.

A Stream Analytics-lekérdezés tervez, tekintse meg a kimenethez használt nevére a [be záradék](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Feladatonként egy egyetlen kimeneti, vagy ha több INTO záradék a lekérdezés megadásával van szüksége a streamelési feladat több kimenetek is használhatja.

Létrehozása, módosítása és tesztelése a Stream Analytics-feladat kimenete, használhatja a [az Azure portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell-lel](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API-val](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output), és [a Visual Studio](stream-analytics-quick-create-vs.md).

Néhány kimenetek típusok támogatása [particionálás](#partitioning), és [köteg méretek kimeneti](#output-batch-size) eltérőek lehetnek a teljesítmény optimalizálása.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics támogatja [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Az Azure Data Lake Store egy vállalati szintű, nagy kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Data Lake Store lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű működési vagy feltáró jellegű elemzési adatok tárolása. Stream Analytics rendelkezik, azt, hogy a Data Lake Store eléréséhez.

A Stream Analytics az Azure Data Lake Store-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

### <a name="authorize-an-azure-data-lake-store-account"></a>Az Azure Data Lake Store-fiók engedélyezése

1. Data Lake Storage kiválasztásakor az Azure Portalon kimenetként egy kapcsolat egy meglévő Data Lake Store engedélyezésére kéri.

   ![Engedélyezze a Data Lake Store kapcsolatot](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Ha már rendelkezik hozzáféréssel a Data Lake Store, válassza ki a **engedélyezése most** és a egy lapot kap, miszerint jelző **engedélyezési átirányítása**. Miután engedélyezési sikeres, megjelennek az oldal, amely lehetővé teszi, hogy a Data Lake Store kimenetének konfigurálásához.

3. A Data Lake Store-fiók hitelesítése után a Data Lake Store kimeneti konfigurálhatja a tulajdonságait. Az alábbi táblázatban látható a tulajdonság nevét és azok leírását a Data Lake Store kimenetének konfigurálásához listája.

   ![Data Lake Store meghatározni a Stream Analytics-kimenetben](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A lekérdezés kimenete a Data Lake Store lekérdezésekben használt rövid név. |
| Fióknév | A Data Lake Storage-fiókot, ahol a kimeneti küld neve. A legördülő listából válassza ki az előfizetésében elérhető Data Lake Store-fiókok megjelennek. |
| Elérési út előtagmintája | A fájl elérési útja lehet írni a megadott Data Lake Store-fiók fájljait. A {date} egy vagy több példányát adhatja meg, és {time} változókat.<br /><ul><li>1. példa: mappa1/naplók / {dátum} / {idő}</li><li>2. példa: mappa1/naplók / {dátum}</li></ul><br />A létrehozott mappaszerkezetet történő küldés időbélyegzője nem helyi idő és követi.<br /><br />Ha a fájl elérési út mintája nem tartalmaz egy záró "/", a legutóbbi minta a fájl elérési útját a filename előtag számít. <br /><br />Új fájlok jönnek létre ilyen körülmények között:<ul><li>A kimeneti séma módosítása</li><li>Külső vagy belső indítsa újra a feladat.</li></ul> |
| Dátumformátum | Választható. Ha a dátum jogkivonat azon előtag elérési útja, kiválaszthatja a dátumformátum, amelyben a fájlok vannak rendszerezve. Példa: ÉÉÉÉ/HH/NN |
|Időformátum | Választható. Ha az idő jogkivonat azon előtag elérési útja, adja meg az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott.|
| Encoding | Ha a CSV vagy JSON-formátumot használ, egy kódolást meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.|
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. Stream Analytics egy közös kihagyni kívánt határolók száma támogatja a CSV-adatokat szerializálásához. Támogatott értékei a következők vesszővel, pontosvesszővel válassza el, szóközt, lapra, és függőleges sávra.|
| Formátum | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. Tömb Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű, használhatja a parancssorban elválasztott JSON-t, mivel nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben.|

### <a name="renew-data-lake-store-authorization"></a>Data Lake Store engedély megújítása
A Data Lake Store-fiók hitelesítse magát újra, ha a jelszó megváltozott, mivel a feladat létrehozásának vagy utolsó hitelesített kell. Ha Ön nem újrahitelesítéséhez, a feladat nem állít elő a kimeneti eredmények, és akkor jelez hibát jelezve a műveletnaplók a reauthorization van szükség. Jelenleg korlátozott, a hitelesítési jogkivonat kell manuálisan frissíteni kell a Data Lake Store-kimenet az összes feladat 90 naponta.

Engedély megújítása **leállítása** a feladat > Nyissa meg a Data Lake Store-Kimenet > kattintson a **engedély megújítása** hivatkozásra, és a egy rövid ideig lap felugró jelző **átirányítása engedélyezési...** . A lap automatikusan bezárja, és jelzi, ha ez sikeres, **engedélyezési sikeresen megújítva**. Szeretne kattintson **mentése** a lap alján, és folytathatja a feladatot újraindítja a **leállt utoljára** adatvesztés elkerülése érdekében.

![A kimenet a Data Lake Store engedély megújítása](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) használható kimenetként, amely a relációs jellegű adatokhoz vagy alkalmazásokhoz, amelyek egy relációs adatbázisban szolgáltatott tartalmaktól függnek. Stream Analytics-feladatok írni egy létező táblázat egy Azure SQL Database-ben. A következő tábla sémáját pontosan egyeznie kell a mezőket és azok típusát, hogy a feladat kimenete. Egy [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) is megadható kimenetként, valamint az SQL Database output paraméter használatával. Írási teljesítmény javítására kapcsolatos további információkért tekintse meg a [Stream Analytics és az Azure SQL Database kimenetként](stream-analytics-sql-output-perf.md) cikk. Az alábbi táblázat felsorolja a tulajdonságnevek és a egy SQL Database-kimenet létrehozása leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete ehhez az adatbázishoz a lekérdezésekben használt rövid név. |
| Adatbázis | Ahol a kimeneti küld az adatbázis neve. |
| Kiszolgálónév | Az SQL Database-kiszolgáló neve. |
| Felhasználónév | A felhasználónév, amely hozzáfér a írni az adatbázisba. Stream Analytics csak az SQL-hitelesítést támogatja. |
| Jelszó | A jelszó az adatbázishoz való csatlakozáshoz. |
| Tábla | A tábla neve, ahol a kimeneti íródik. A tábla neve a kis-és nagybetűket, és ez a tábla sémája pontosan a kívánt mezőket és azok típusát, a feladat kimenetének által generált egyeznie kell. |
|Partícióséma öröklése| Ez lehetővé teszi, hogy a particionálási séma az előző lekérdezés lépés ahhoz, hogy az a tábla több írók teljes párhuzamos topológia öröklik. További információkért lásd: [az Azure SQL Database az Azure Stream Analytics-kimenetet](stream-analytics-sql-output-perf.md).|
|Egyezés kötegek száma| Rekordok küldi el minden tömeges javasolt felső határ száma szúrja be a tranzakció.|

> [!NOTE]
> Jelenleg a Stream Analytics-feladat kimenete az Azure SQL Database-ajánlat esetén támogatott. Azonban egy Azure virtuális gépet csatolt adatbázist tartalmazó SQL Server szoftvert futtató nem támogatott. Ez a jövőbeni kiadásokban változhat.
>

## <a name="blob-storage"></a>Blob Storage
A BLOB storage nagy mennyiségű strukturálatlan adat tárolása a felhőben egy költséghatékony és méretezhető megoldást kínál. Bevezetés az Azure Blob storage és a használatát, lásd: a dokumentációban a [Blobok használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Az alábbi táblázat felsorolja a tulajdonságnevek és a egy blob kimenet létrehozása leírását.

| Tulajdonság neve       | Leírás                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Output Alias (Kimeneti alias)        | A lekérdezés kimenete az a blob storage-lekérdezésekben használt rövid név. |
| Tárfiók     | A tárfiókot, ahol a kimeneti küld neve.               |
| Tárfiók kulcsa | A storage-fiókhoz társított titkos kulcs.                              |
| Storage-tároló   | Tárolók biztosítják a Microsoft Azure Blob service-ben tárolt blobok logikai jellegű csoportosítását. A Blob szolgáltatáshoz feltölt egy blobot, ha meg kell adnia egy adott blob-tárolót. |
| Path Pattern (Elérésiút-minta) | Választható. A fájl elérési út mintája a megadott tárolóban található blobok írásához használt. <br /><br /> A dátum idő változó egy vagy több példányát adhatja meg, amelyek a blobok írt használandó dönthet az elérésiút-minta: <br /> {date}, {time} <br /><br />Ha ez [az Azure portal-hivatkozás](https://portal.azure.com/?microsoft_azure_streamanalytics_bloboutputpathpartitioning=true&Microsoft_Azure_StreamAnalytics_bloboutputcontainerpartitioning=true) az egyéni blob particionálás az előzetes verzió eléréséhez, megadhat egy egyéni {mező} neve az esemény adatokból partíció blobok. A mező neve alfanumerikus és szóközöket, kötőjeleket és aláhúzásjeleket tartalmazhatnak. Egyéni mezők korlátozásai a következők: <ul><li>Használatieset-iránti érzéketlensége (nem lehet megkülönböztetni a oszlop "ID" és "oszlop"id")</li><li>Beágyazott mezői nem engedélyezettek (inkább alias használata a feladat lekérdezésben "simítják" mező)</li><li>Kifejezések nem használható a mező nevét.</li></ul> <br /><br /> Az előzetes verzió is lehetővé teszi egyéni dátum és idő formátumú specifikátor konfigurációk az elérési út használata. Egyéni dátum és idő formátumban kell lennie a megadott egyenként, kapcsos a {dátum és idő:\<specifikátor >} kulcsszót. Megengedett bemenetek \<specifikátor >: éééé, MM, M, nn, d, HH, H, mm, m, ss vagy s. A {dátum és idő:\<specifikátor >} kulcsszó használható többször az elérési út egyéni dátum/idő konfigurációk alkotnak. <br /><br />Példák: <ul><li>1. példa: fürt1/naplók / {dátum} / {idő}</li><li>2. példa: fürt1/naplók / {dátum}</li><li>(Előzetes verzió). 3. példa: cluster1 / {client_id} / {dátum} / {idő}</li><li>4 (előzetes verzió). példa: cluster1 / {datetime:ss} / {myField} amennyiben a lekérdezés végrehajtása: Válassza ki data.myField, a bemeneti; myField</li><li>5 (előzetes verzió). példa: cluster1/év = {datetime:yyyy} / hónap = {datetime:MM} / nap = {datetime:dd}</ul><br /><br />A létrehozott mappaszerkezetet történő küldés időbélyegzője nem helyi idő és követi.<br /><br />Az alábbi konvenció fájlelnevezésnél követi: <br /><br />{Elérési út előtagja Pattern}/schemaHashcode_Guid_Number.extension<br /><br />A példában a kimeneti fájlok:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Ebben az előzetes verzióban kapcsolatos további információkért látogasson el [egyéni dátum/idő elérésiút-minták az Azure Stream Analytics a blob storage-kimenet (előzetes verzió)](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Dátumformátum | Választható. Ha a dátum jogkivonat azon előtag elérési útja, kiválaszthatja a dátumformátum, amelyben a fájlok vannak rendszerezve. Példa: ÉÉÉÉ/HH/NN |
| Időformátum | Választható. Ha az idő jogkivonat azon előtag elérési útja, adja meg az időformátum, amelyben a fájlok vannak rendszerezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding    | Ha a CSV vagy JSON-formátumot használ, egy kódolást meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter   | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. Stream Analytics egy közös kihagyni kívánt határolók száma támogatja a CSV-adatokat szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum      | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. Tömb Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű, használhatja a parancssorban elválasztott JSON-t, mivel nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben. |

Blob storage-dzsal kimenetként, amikor új fájl létrehozása a blob a következő esetekben:

* Ha a fájl meghaladja a maximális engedélyezett blokkok (jelenleg 50 000 blokk). Maximálisan engedélyezett száma nem érte el a maximális megengedett blob mérete el lehet érni. Például ha a kimeneti sebessége nagy, láthatja, hogy további bájtok száma letiltása, és a fájl mérete nagyobb. Ha a kimeneti aránya alacsony, minden egyes rendelkezik kevesebb adatot, és a fájl mérete kisebb.
* Ha egy séma módosul, a kimenetben, és a kimeneti formátum rögzített sémát (fürt megosztott kötetei szolgáltatás és az Avro) van szükség.
* Ha a feladat újraindításakor kívülről, leállíthatja és elindíthatja azt egy felhasználó vagy belső rendszer-karbantartási vagy a hiba helyreállítási.
* A lekérdezés teljes particionálva van, ha új fájl jön létre minden kimeneti partíció esetében.
* Ha egy fájl vagy egy tárolót a storage-fiók törlődik a felhasználó által.
* Ha a kimenet az idő, az elérési út előtagmintája használ, egy új blob használatos, ha a lekérdezés helyezi át a következő órában.
* Ha a kimenet egy egyéni mező particionálva van, egy új blob partíciókulcson létrejön, ha még nem létezik.
* Ha a kimenet egy egyéni mező, ahol a partíciós kulcs számossága meghaladja a 8000-es particionálva van, egy új blob partíciókulcson lehet létrehozni.

## <a name="event-hub"></a>Eseményközpont
A [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatás egy nagymértékben méretezhető közzétételi és előfizetési Eseménygyűjtő szolgáltatás. Másodpercenként több millió begyűjthesse. Egy Eseményközpont kimenetként használata a bemenet egy másik folyamatos átviteli feladat válásakor a Stream Analytics-feladat kimenetét.

Van néhány olyan paraméterek, melyek szükségesek ahhoz, hogy konfigurálta az Event Hubs-adatfolyamokat kimenetként.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | A lekérdezés kimenete az Eseményközpont lekérdezésekben használt rövid név. |
| Event Hubs-névtér |Az Event Hub-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Eseményközpont-névtér is létrejött. |
| Eseményközpont neve | Az Eseményközpont kimenete neve. |
| Eseményközpont szabályzatának neve | A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon hozható létre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| Eseményközpont házirendjének kulcsa | A közös hozzáférési kulcs segítségével hitelesíti a hozzáférést az Event Hubs-névtér. |
| [Opcionális] partíciós kulcsának oszlopa | Ez az oszlop tartalmazza a partíciókulcs az Eseményközpont kimenete. |
| Eseményszerializációs formátum | Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding | A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. Tömb Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. A tömb le van zárva, csak akkor, ha a feladat leáll vagy a Stream Analytics át lett helyezve a következő alkalommal időszakban. Általában célszerű, használhatja a parancssorban elválasztott JSON-t, mivel nincs szükség semmilyen különleges kezelést továbbra is van a kimeneti fájl írása közben. |

## <a name="power-bi"></a>Power BI
[Power bi-ban](https://powerbi.microsoft.com/) segítségével egy Stream Analytics-feladat kimeneteként az elemzési eredmények biztosított részletes megjelenítések élményt nyújtanak. Ez a funkció a operatív irányítópultokat, a jelentéskészítéshez és a jelentéskészítés driven metrika használható.

A Stream Analytics a Power BI-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

### <a name="authorize-a-power-bi-account"></a>Engedélyezze a Power BI-fiókkal
1. Az Azure Portalon kimenetként kiválasztásakor a Power BI, kéri engedélyezésére egy meglévő Power BI-t, vagy hozzon létre egy új Power BI-fiókot.
   
   ![Kimenet konfigurálása Power BI-felhasználók engedélyezése](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. Hozzon létre egy új fiókot, ha nem, de rendelkezik ilyennel, majd kattintson az engedélyezés most. A következő oldal jelenik meg:
   
   ![A Power bi-bA az Azure-fiók hitelesítése](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. Ebben a lépésben adja meg a munkahelyi vagy iskolai fiókkal, amelyek engedélyezik a Power BI-kimenet. Ha nem már jelentkezett a Power bi-hoz, válassza az Jelentkezzen most ki. A munkahelyi vagy iskolai fiókkal, használhatja a Power bi-hoz az Azure-előfizetés fiókból, amely a jelenleg használt a következővel eltérő lehet.

### <a name="configure-the-power-bi-output-properties"></a>A Power BI-kimenet tulajdonságainak konfigurálása
Miután a Power BI-fiókja hitelesített, a Power BI-kimenet konfigurálhatja a tulajdonságait. Az alábbi táblázat a tulajdonságnevek listáját és azok leírását a Power BI-kimenet konfigurálása.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete a Power bi-kimenet lekérdezésekben használt rövid név. |
| Csoportos munkaterület |Más Power BI-felhasználókkal rendelkező megosztási adatok engedélyezéséhez válassza ki a csoportokat a Power BI-fiókjában található, vagy válassza a "Saját munkaterület", ha nem szeretné írni a csoport. Meglévő csoport frissítése a Power BI-hitelesítés megújítása igényel. |
| Adatkészlet neve |Adjon meg egy adatkészlet neve, amely azt a Power BI-kimenet használatára van szükség |
| Tábla neve |Adja meg az adatkészletet a Power BI-kimenet alatt tábla nevét. Jelenleg a Stream Analytics-feladatok a Power BI-kimenet csak rendelkezhet egy olyan táblát egy adatkészletben |

A Power BI-kimenet és az irányítópult konfigurálásának a lépésenkénti útmutatóért lásd: a [Azure Stream Analytics és a Power BI](stream-analytics-power-bi-dashboard.md) cikk.

> [!NOTE]
> Nem explicit módon létrehozni az adatkészlet és a táblázat a Power BI-irányítópulton. Az adatkészlet és a táblázat a rendszer automatikusan kitölti a feladat elindult, és a feladat kimeneti szivattyútelepek elindítja a Power BI-bA. Vegye figyelembe, hogy a beállítást, ha a feladat lekérdezés nem jönnek létre az eredményt, az adatkészlet és a tábla nem jön létre. Vegye figyelembe, hogy ha a Power bi-ban már volt egy adatkészletet, és ezzel a névvel, mint a Stream Analytics-feladat a megadott tábla, a meglévő adatokat a rendszer felülírja.
>

### <a name="schema-creation"></a>Séma létrehozása
Az Azure Stream Analytics létrehoz egy Power BI-adatkészlethez és a tábla a felhasználó nevében, ha egy nem létezik. Minden más esetben a tábla frissül új értékek. Jelenleg nincs korlátozás csak egy tábla egy adatkészleten belül létezhet. A Power BI a FIFO adatmegőrzési szabályt használ. Engedélyezése esetén adatokat gyűjt egy tábla 200 000 sort beolvasásig.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Adattípus-konverzió a Stream Analytics, a Power bi-bA
Az Azure Stream Analytics frissíti az adatmodell dinamikusan, futásidőben, ha a kimeneti sémával megváltozik. Oszlop neve megváltozik, oszlopok adattípus-módosítások, és hozzáadását és eltávolítását, az oszlopok összes nyomon követett.

Ez a táblázat ismerteti az adatok típuskonverziók a [Stream Analytics adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx) az energiagazdálkodási BIs [Entity Data Model (EDM) típusú](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) Ha egy POWER BI-adatkészlet és a tábla nem létezik.

A Stream Analytics | A Power bi-bA
-----|-----|------------
bigint | Int64
típus: nvarchar(max) | Karakterlánc
dátum/idő | Dátum és idő
lebegőpontos | Dupla
Rekord tömb | A karakterlánc típusú, állandó érték "IRecord" vagy "IArray"

### <a name="schema-update"></a>Séma frissítése
Stream Analytics kikövetkezteti a kimenetben szereplő események első készlete alapján adatokat modellsémát. Később Ha szükséges, az adatok modellsémát frissül bejövő eseményeket, amelyek esetleg nem illik az eredeti séma befogadásához.

A `SELECT *` lekérdezés el kell kerülni, hogy a dinamikus séma frissítése a sorok között. A lehetséges teljesítményre gyakorolt hatása mellett is eredményezhet a bizonytalanság az eredményeket a szükséges idő. A pontos mezők jelennek meg a Power BI-irányítópultra van kiválasztva. Ezenkívül az adatértékek felelnek meg a kiválasztott adatok típusa lehet.


Előző és aktuális | Int64 | Karakterlánc | Dátum és idő | Dupla
-----------------|-------|--------|----------|-------
Int64 | Int64 | Karakterlánc | Karakterlánc | Dupla
Dupla | Dupla | Karakterlánc | Karakterlánc | Dupla
Karakterlánc | Karakterlánc | Karakterlánc | Karakterlánc |  | Karakterlánc |
Dátum és idő | Karakterlánc | Karakterlánc |  Dátum és idő | Karakterlánc


### <a name="renew-power-bi-authorization"></a>A Power BI engedély megújítása
Ha a Power BI-fiókja jelszavát megváltozik, miután a Stream Analytics-feladat létrehozásának vagy utolsó hitelesített, hitelesítse magát újra a Stream Analytics szeretne. Ha a multi-factor Authentication (MFA) az Azure Active Directory (AAD) bérlőre van konfigurálva, is újítsa meg a Power BI engedélyezési kéthetente kell. A probléma tünete nincs feladatkimenet és a "hitelesítés felhasználói hibája" a műveletnaplók:

  ![A Power BI hitelesítés felhasználói hiba](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

A probléma megoldásához állítsa le a futó feladatot, és nyissa meg a Power BI-kimenet. Válassza ki a **engedély megújítása** hivatkozásra, és indítsa újra a feladatot a **leállt utoljára** adatvesztés elkerülése érdekében.

  ![A kimenet a Power BI engedély megújítása](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Az Azure Table storage](../storage/common/storage-introduction.md) rugalmasan méretezhető, magas rendelkezésre állású tárolási kínál, hogy az alkalmazás automatikusan skálázhatja a felhasználói igények. TABLE storage szolgáltatás a Microsoft NoSQL kulcs-/ attribútumtár, melyik a séma kevesebb korlátozásaihoz kihasználhatják a strukturált adatok esetében. Az Azure Table storage segítségével tárolja az adatok megőrzését és a hatékony lekéréséhez.

Az alábbi táblázat felsorolja a tulajdonságnevek és a egy tábla kimenet létrehozása leírását.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete ezen táblatároló lekérdezésekben használt rövid név. |
| Tárfiók |A tárfiókot, ahol a kimeneti küld neve. |
| Tárfiók kulcsa |A storage-fiókhoz társított hozzáférési kulcs. |
| Tábla neve |A tábla neve. A tábla jön létre, ha még nem létezik. |
| Partíciókulcs |A partíciókulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs a partíción belül egy adott táblán egy entitás elsődleges kulcsának első részét képező egyedi azonosítója. Egy karakterláncérték, amely legfeljebb 1 KB méretű lehet. |
| Sorkulcs |A sorkulcsot tartalmazó kimeneti oszlop neve. A sorkulcs pedig egy entitás egy adott partíción belül egyedi azonosítója. Egy entitás elsődleges kulcsának második részét képezi. A sorkulcs pedig egy karakterláncérték, amely legfeljebb 1 KB méretű lehet. |
| Köteg mérete |A kötegelt műveleti rekordok száma. Az alapértelmezett (100) érték elegendők a legtöbb feladatot. Tekintse meg a [kötegelt művelet specifikációja](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Ez a beállítás módosításával kapcsolatos további részletekért. |

## <a name="service-bus-queues"></a>Service Bus által kezelt üzenetsorok
[Service Bus-üzenetsorok](https://msdn.microsoft.com/library/azure/hh367516.aspx) kínál egy First In, első ki (FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Általában üzenetek fogadását és feldolgozását a hozzáadásuk historikus, amelyben hozzá lettek adva az üzenetsorba, és minden üzenetet kapott, és csak egy üzenetfogyasztó által feldolgozott által várt.

Az alábbi táblázat felsorolja a tulajdonságnevek és a egy üzenetsor kimeneti létrehozása leírását.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |A lekérdezés kimenete a Service Bus-üzenetsor lekérdezésekben használt rövid név. |
| Service Bus-névtér |Service Bus-névtér az üzenetküldési entitások készletének tárolója. |
| Üzenetsor neve |A Service Bus-üzenetsor neve. |
| Sor házirendjének neve |Amikor létrehoz egy üzenetsort, az üzenetsor konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. |
| Sor házirendjének kulcsa |A Service Bus-névtérrel való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs |
| Eseményszerializációs formátum |Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding |A fürt megosztott kötetei szolgáltatás és a JSON az UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |
| Formátum |Csak érvényes JSON-típus. Sorral elválasztott beállítás megadja, hogy a formázott megadásával minden JSON-objektum sortöréssel elválasztva. Tömb Megadja, hogy a kimenet JSON-objektumok tömbjeként van formázva. |

A partíciók száma [a Service Bus-Termékváltozat és a mérete alapján](../service-bus-messaging/service-bus-partitioning.md). Partíciókulcs egyedi egész szám érték minden egyes partícióhoz.

## <a name="service-bus-topics"></a>Service Bus-üzenettémák
Habár a fogadó, Service Bus-üzenetsorok biztosít az küldőtől egy-egy kommunikációs módszer [Service Bus-témakörök](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy-a-többhöz típusú kommunikációt biztosítanak.

Az alábbi táblázat felsorolja a tulajdonságnevek és a egy tábla kimenet létrehozása leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a Service Bus-témakör a lekérdezés kimenete a lekérdezésekben használt. |
| Service Bus-névtér |Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor is létrehozott egy Service Bus-névtér |
| Téma neve |A témakörök üzenetküldési entitások, hasonlók az event hubs és üzenetsorok. A különböző eszközökön és szolgáltatásokon számos szolgáltatásból lettek kialakítva. A témakör létrehozását követően is kapnak egy adott névvel. A témakörbe küldött üzenetek nem érhető el, kivéve, ha egy előfizetés jön létre, ezért győződjön meg arról a témakör alatt egy vagy több előfizetés |
| Téma házirendjének neve |Amikor létrehoz egy témát, a témakör konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend neve, engedélyek, hogy állítsa be, és hozzáférési kulcsokkal rendelkezik |
| Téma házirendjének kulcsa |A Service Bus-névtérrel való hozzáférés hitelesítésére használt megosztott hozzáférési kulcs |
| Eseményszerializációs formátum |Szerializálási formátum a kimeneti adatokat. JSON, a fürt megosztott kötetei szolgáltatás és az avro-hoz támogatott. |
| Encoding |Ha a CSV vagy JSON-formátumot használ, egy kódolást meg kell adni. UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei a következők: vesszővel, pontosvesszővel válassza el, lemezterület, lapon és függőleges sávra. |

A partíciók száma [a Service Bus-Termékváltozat és a mérete alapján](../service-bus-messaging/service-bus-partitioning.md). Partíciókulcs egyedi egész szám érték minden egyes partícióhoz.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) van egy globálisan elosztott, többmodelles adatbázis szolgáltatást, hogy az ajánlatok korlátlan, rugalmas skálázhatóságát szerte a világon, funkciókban gazdag lekérdezési és Automatikus indexelés sémafüggetlen adatmodelleket, keresztül garantált alacsony késés és a piacvezető átfogó SLA-k. A Stream Analytics a Cosmos DB adatgyűjtési beállítások kapcsolatos további információkért tekintse meg a [kimenetként a Cosmos DB-vel a Stream Analytics](stream-analytics-documentdb-output.md) cikk.

A Stream Analytics az Azure Cosmos DB-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

> [!Note]
> Jelenleg az Azure Stream Analytics csak kapcsolatot támogat az, cosmos DB használatával **SQL API**.
> Más Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB-fiókokhoz létrehozott más API-kkal, az adatok esetleg nem megfelelően tárolni.

Az alábbi táblázat ismerteti a tulajdonságait, egy Azure Cosmos DB kimeneti létrehozásához.
| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias | Tekintse meg ezt a Stream Analytics-lekérdezés kimenete egy alias. |
| Sink (Fogadó) | Cosmos DB |
| Importálási beállítás | Válassza a "Kijelölése a Cosmos DB-előfizetéséből", vagy "adja meg a Cosmos DB-beállítások manuális".
| Fiókazonosító | A neve vagy URI-ját a Cosmos DB-fiók végpontját. |
| Fiókkulcs | A közös hozzáférési kulcs a Cosmos DB-fiók. |
| Adatbázis | A Cosmos DB-adatbázis neve. |
| Gyűjteménynévminta | A gyűjtemény nevét vagy a minta gyűjtemények használhatók. <br />A gyűjteménynév-formátum az opcionális {partition} token használatával, ahol a partíciók 0-tól kezdődnek lehet létrehozni. Két példa:  <br />1. _MyCollection_ – egy gyűjtemény "MyCollection" nevű léteznie kell.  <br />2. _{Partition} MyCollection_ – a particionálási oszlop alapján. <br />A particionálási oszlop gyűjteményeknek létezniük kell – "MyCollection0", "MyCollection1", "MyCollection2" és így tovább. |
| Partíciókulcs | Választható. Ez csak akkor van szükség, ha a gyűjteménynévminta {partition} token használ.<br /> A partíciós kulcs pedig a kimeneti eseményekben a kimenet gyűjtemények közötti particionálására szolgáló kulcs megadásához használt mező neve.<br /> Egyetlen gyűjtemény kimenet bármilyen tetszőleges kimeneti oszlop használható. Ha például PartitionID azonosítóval. |
| Dokumentumazonosító |Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek alapuló elsődleges kulcs megadásához használt mező neve.

## <a name="azure-functions"></a>Azure Functions
Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerint, külön infrastruktúra üzembe helyezése és kezelése nélkül futtathat kódokat. Lehetővé teszi az Azure-ban vagy külső szolgáltatásokban bekövetkező események által aktivált kódok implementálhatók. Az Azure Functions a triggerekre való válaszadási képessége lehetővé teszi a természetes kimeneti egy Azure Stream Analytics. A kimeneti adaptere segítségével a felhasználók számára a Stream Analytics és az Azure Functions összekapcsolása, és futtathat szkripteket vagy kódrészleteket különféle eseményekre válaszként.

A Stream Analytics az Azure Functions-kimenet jelenleg nem érhető el az Azure China (21Vianet) és az Azure Germany (T-Systems International) régióban.

Az Azure Stream Analytics az Azure Functions via HTTP-eseményindító hív meg. Az új Azure-függvény kimeneti adapter érhető el a következő konfigurálható tulajdonsággal:

| Tulajdonság neve | leírás |
| --- | --- |
| Függvényalkalmazás |Az Azure Functions-alkalmazás neve |
| Függvény |A függvény az Azure Functions-alkalmazás neve |
| Kulcs |Ha szeretné használni az Azure-függvény, egy másik előfizetésből, megteheti azáltal, hogy a kulcs a függvény eléréséhez |
| Maximális kötegméret |Ez a tulajdonság segítségével állítsa be az Azure-függvény küldött kimeneti kötegek maximális méretét. A bemeneti mértékegysége (bájt). Alapértelmezés szerint ez az érték a 262 144 bájt (256 KB) |
| Maximális kötegszám  |Azt jelzi, a neve, ahogy ez a tulajdonság lehetővé teszi Azure Functions küldött egyes kötegekben lévő események maximális számának megadása. Az alapértelmezett maximális kötegelt szám értéke 100 |

Az Azure Stream Analytics 413 (http-kérelem túl nagy) kivételt kap az Azure-függvény, csökkenti a küld az Azure Functions a kötegek mérete. Az Azure-függvény kódját a kivétel használatával győződjön meg arról, hogy az Azure Stream Analytics nem küld e túl nagy kötegeket. Győződjön meg arról, hogy a függvényben használt maximális kötegelt száma és mérete értékek konzisztens a Stream Analytics-portálon megadott értékekkel.

Olyan helyzet is, az nem található olyan esemény, ideje ablakban üzenetsorokra, ha nincs kimenet jön létre. Ennek eredményeképpen computeResult függvény nincs neve. A beépített ablakos összesítő függvényekben összhangban az ezt a viselkedést.

## <a name="partitioning"></a>Particionálás

A következő táblázat összefoglalja a partíció-támogatás és a kimeneti írók az egyes kimeneti száma:

| Kimenet típusa | Particionálási támogatása | Partíciókulcs  | Kimeneti írók száma |
| --- | --- | --- | --- |
| Azure Data Lake Store | Igen | Használja {a date} és {time} az elérési út előtagmintája tokeneket. Válassza ki a dátum formátuma éééé/hh/nn például, nn/hh/éééé-hh-nn-éééé. Az időformátum ÓÓ használható. | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). |
| Azure SQL Database | Igen | A PARTITION BY záradék a lekérdezés alapján | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). További információ eléréséhez jobban írni átviteli teljesítmény tölt be adatokat az SQL Azure Database-be tudnivalókért látogasson el [az Azure SQL Database az Azure Stream Analytics-kimenetet](stream-analytics-sql-output-perf.md). |
| Azure Blob Storage | Igen | Használja {a date} és {time} token az esemény mezőiből az elérésiút-minta. Válassza ki a dátum formátuma éééé/hh/nn például, nn/hh/éééé-hh-nn-éééé. Az időformátum ÓÓ használható. Részeként a [előzetes](https://aka.ms/ASApreview1), blob kimeneti lehet particionálni egy egyéni esemény egyetlen attribútum {fieldname} vagy {dátum és idő:\<specifikátor >}. | A bemeneti particionálási követi [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). |
| Azure-eseményközpont | Igen | Igen | Partíció igazítás függően változik.<br /> Ha a kimeneti partíciós kulccsal egyaránt igazodik a felsőbb rétegbeli (korábbi) lekérdezési lépésre, írók száma az Eseményközpont megegyezik a számát kimeneti Event Hubs-partíciók. Minden egyes író használja az EventHub [EventHubSender osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) eseményeket küldhet az adott partíció. <br /> Ha a partíciókulcs felsőbb rétegbeli (korábbi) lekérdezési lépésre, írók száma nem igazodik az Eseményközpont kimenete ugyanaz, mint a korábbi lépésben partíciók száma. Minden egyes író használ EventHubClient [SendBatchAsync osztály](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) az események küldése az összes kimeneti partíciót. |
| Power BI | Nem | None | Nem alkalmazható. |
| Azure Table Storage | Igen | Minden olyan kimeneti oszlop.  | A bemeneti particionálási követi [teljes mértékben a lekérdezések párhuzamosíthatók](stream-analytics-scale-jobs.md). |
| Az Azure Service Bus-témakörbe | Igen | Automatikusan kiválasztja. A partíciók száma alapján a [Service Bus-Termékváltozat és a méret](../service-bus-messaging/service-bus-partitioning.md). Partíciókulcs egyedi egész szám érték minden egyes partícióhoz.| Ugyanaz, mint a kimenet a témakör a partíciók száma.  |
| Az Azure Service Bus-üzenetsorba | Igen | Automatikusan kiválasztja. A partíciók száma alapján a [Service Bus-Termékváltozat és a méret](../service-bus-messaging/service-bus-partitioning.md). Partíciókulcs egyedi egész szám érték minden egyes partícióhoz.| Ugyanaz, mint a kimeneti várólistában lévő partíciók száma. |
| Azure Cosmos DB | Igen | A gyűjteménynévminta {partition} token használható. {partition} értéket a PARTITION BY záradék a lekérdezés alapul. | A bemeneti particionálási követi [teljes mértékben a lekérdezések párhuzamosíthatók](stream-analytics-scale-jobs.md). |
| Azure Functions | Nem | None | Nem alkalmazható. |

A konzolkimeneti adapter nincs particionálva, ha az egyik bemeneti partíciók adatainak hiánya miatt késői érkezési mennyi ideig akár késést.  Ezekben az esetekben a kimenet a szűk keresztmetszeteket okozhat a folyamatban, amely egyetlen író részeként történik. Késedelmes érkezési házirenddel kapcsolatos további tudnivalókért látogasson el a [Azure Stream Analytics esemény rendelés szempontok](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Kimeneti kötegmérete
Az Azure Stream Analytics használ a változó méretű kötegekben kimenetek írása és dolgozza fel. A Stream Analytics-motor általában egyszerre nem írni egy üzenet, és kötegekben használja hatékonyságát. Ha a bejövő, mind a kimenő események sebessége nagy, nagyobb méretű kötegekben használ. Ha a kilépő üzenetek gyakorisága alacsony, kisebb kötegekben használ tartani alacsony késést.

Az alábbi táblázat ismerteti azokat a kötegelés kimeneti szempontok:

| Kimenet típusa | Maximális üzenetméret | Köteg mérete optimalizálása |
| :--- | :--- | :--- |
| Azure Data Lake Store | Lásd: [korlátozza a Data Lake Storage](../azure-subscription-service-limits.md#data-lake-store-limits) | Legfeljebb 4 MB / írási művelet |
| Azure SQL Database | Max. 10 000 sorosak egyetlen tömeges beszúrás<br />Egyetlen tömeges beszúrás sorosak 100 perc <br />Lásd még: [Azure SQL-korlátozások](../sql-database/sql-database-resource-limits.md) |  Minden batch először tömeges maximális kötegméret együtt, és előfordulhat, hogy ossza fel a batch felét (csak minimális köteg mérete) – Újrapróbálkozást lehetővé tevő hiba esetén az SQL-alapú. |
| Azure Blob Storage | Lásd: [korlátozza az Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Maximális Blob blokkblob mérete 4 MB<br />Blobok bock maximális száma 50000 |
| Azure-eseményközpont   | Egy üzenet 256 KB <br />Lásd még: [korlátozza az Event Hubs](../event-hubs/event-hubs-quotas.md) |   Bemeneti kimeneti particionálás nem igazodnak, ha minden egyes esemény egy EventData külön-külön csomagolni és kötegelt akár a maximális üzenetméret (prémium szintű termékváltozat 1 MB) küldi el. <br /><br />  Bemeneti-kimeneti particionálás van igazítva, ha több esemény maximális mérete legfeljebb egy egyetlen EventData elhelyezve, és küldött.  |
| Power BI | Lásd: [Power BI Rest API-korlátok](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Lásd: [korlátozza az Azure Storage](../azure-subscription-service-limits.md#storage-limits) | Alapértelmezett egyszeri tranzakciónként 100 entitást, és egy kisebb értékre, igény szerint konfigurálható. |
| Az Azure Service Bus-üzenetsorba   | Egy üzenet 256 KB<br /> Lásd még: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md) | Egy üzenet egyszeri esemény |
| Az Azure Service Bus-témakörbe | Egy üzenet 256 KB<br /> Lásd még: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md) | Egy üzenet egyszeri esemény |
| Azure Cosmos DB   | Lásd: [korlátozza az Azure Cosmos DB-hez](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Köteg mérete és az írási gyakorisága korrigált dinamikusan alapú cosmos DB-válaszok. <br /> A Stream Analytics nincs előre meghatározott korlátozás. |
| Azure Functions   | | Alapértelmezett köteg mérete 262 144 bájt (256 KB). <br /> Alapértelmezett események száma kötegenkénti érték 100. <br /> A Köteg mérete nem konfigurálható, és növelhető vagy csökkenthető, a Stream Analytics [kimeneti beállítások](#azure-functions).

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]

> [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure portal használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
