---
title: Azure Stream Analytics kimeneteinek ismertetése
description: Ez a cikk ismerteti az elérhető Azure Stream Analytics, beleértve a Power bi-ban az elemzés eredményeinek kimeneti lehetőségeket.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2018
ms.openlocfilehash: 030af72951e226d3484706e627bc8b74d5469670
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics kimeneteinek ismertetése
Ez a cikk ismerteti a különböző típusú kimenetek egy Azure Stream Analytics-feladat érhető el. Kimenetek lehetővé teszik, hogy tárolja, és mentse a Stream Analytics-feladat eredményét. A kimeneti adatok használatával, elvégezhető a további üzleti elemzések és az adatraktározás terén, az adatok. 

A Stream Analytics lekérdezési tervezésekor tekintse meg a kimenethez használt nevét a [történő záradék](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Egy feladat / egyetlen kimeneti vagy egy folyamatos átviteli feladat, ha szüksége több INTO záradék a lekérdezés megadásával több kimenet is használhatja.

Létrehozása, szerkesztése és tesztelése a Stream Analytics-feladat kimenetében, használhatja a [Azure-portálon](stream-analytics-quick-create-portal.md#configure-output-to-the-job), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.Net API](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-output), és [Visual Studio](stream-analytics-tools-for-visual-studio.md).

Néhány kimenetek típusok támogatási [particionálás](#partitioning), és [kötegelt méretek kimeneti](#output-batch-size) eltérők lehetnek a teljesítmény optimalizálása érdekében.


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Stream Analytics támogatja [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Az Azure Data Lake Store egy vállalati szintű, nagy kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Data Lake Store lehetővé teszi, hogy a műveleti és felderítési jellegű bármilyen méretű, típusú és feldolgozási sebességű adatok. A Stream Analytics a Data Lake Store jogosultsággal rendelkezik.

### <a name="authorize-an-azure-data-lake-store-account"></a>Egy Azure Data Lake Store-fiók engedélyezése

1. Data Lake tárolási kiválasztásakor az Azure portálon kimenetként kéri egy kapcsolat egy meglévő Data Lake Store hitelesítéséhez.  

   ![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Ha már rendelkezik hozzáféréssel a Data Lake Store, válassza ki a **engedélyezik most** és egy lap jelenik meg, amely jelzi, **engedélyezési átirányítása**. Miután engedélyezési sikeres, lehetősége lesz a lap, amely lehetővé teszi a Data Lake Store kimeneti konfigurálását.

3. Miután a Data Lake Store-fiók hitelesítését, a Data Lake Store kimeneti megadhatja a tulajdonságait. Az alábbi táblázat pedig a tulajdonság nevét és azok leírását a Data Lake Store kimeneti konfigurálása.

   ![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| Tulajdonság neve | Leírás | 
| --- | --- |
| Kimeneti alias | Egy rövid nevet a lekérdezés kimenete a Data Lake Store a lekérdezésekben használt. | 
| Fióknév | A Data Lake-tárfiókra ahol küldendő a kimeneti neve. A legördülő listából válassza ki a Data Lake Store-fiókok az előfizetésben elérhető jelenik meg. |
| Elérési út előtagmintája | A megadott Data Lake Store-fiók található a fájl írásához használt elérési. A {date} egy vagy több példányát adhatja meg, és {time} változók.</br><ul><li>1. példa: mappa1/logs / {date} / {time}</li><li>2. példa: mappa1/logs / {date}</li></ul>Ha a fájl elérési út mintája nem tartalmaz egy záró "/", a rendszer a legutóbbi minta elérési útjának fájlnév előtag. </br></br>Új fájlok jönnek létre ilyen körülmények között:<ul><li>A kimeneti sémával módosítása</li><li>Külső vagy belső indítsa újra a feladatok.</li></ul> |
| Dátumformátum | Választható. Ha a dátum jogkivonat a előtag elérési útját, válassza a dátumformátum, amelyben a fájlok vannak rendezve. . Példa: Éééé/hh/nn |
|Időformátum | Választható. Ha a idő jogkivonat előtag elérési, adja meg az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | A kimeneti adatok szerializálási formátum. JSON, CSV és az avro-hoz támogatott.| 
| Encoding | Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.|
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogatja a CSV-adatok szerializálása során. Támogatott értékei, vesszővel, pontosvesszővel válassza el, lemezterület, lapon, majd függőleges vonal.|
| Formátum | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben.|

### <a name="renew-data-lake-store-authorization"></a>Újítsa meg a Data Lake Store engedélyezési
Újból hitelesítésre a Data Lake Store-fiókot, ha a jelszó megváltozott a feladat meg lett létrehozva, vagy utolsó hitelesített kell. Nem újból hitelesítésre, ha a feladat nem eredményt kimeneti, és a hiba a műveletnaplók az újbóli engedélyezés szükség szerint jeleníti meg. Jelenleg egy korlátozás amikor a hitelesítési jogkivonat kell manuálisan frissíteni kell a Data Lake Store kimenet összes feladat 90 naponta. 

Hitelesítés megújítása **leállítása** a feladat > lépjen a Data Lake Store kimeneti > kattintson a **újra a portálon** hivatkozásra, és egy rövid ideig lap jelenik meg, amely jelzi, **átirányítása engedélyezési...** . A lap automatikusan bezárja, és jelzi, ha sikeres, **engedélyezési sikeresen megújítva**. Meg kell kattintania **mentése** a lap alján, és csak ezután folytatható, ha a feladatot újraindítja a **feladat utolsó befejezési időpontja** adatvesztés elkerülése érdekében.

![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) használható kimenetként a relációs jellegű adatokhoz, vagy olyan alkalmazásnál, amely egy relációs adatbázisban szolgáltatott tartalmaktól függnek. Stream Analytics-feladatok írni a meglévő tábla Azure SQL-adatbázisban.  A következő tábla sémáját pontosan meg kell egyeznie a mezők és azok típusát, a feladat kimenete alatt. Egy [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) keresztül, valamint az SQL-adatbázis output paraméter kimenetként is megadható. Az alábbi táblázat felsorolja a tulajdonság nevét és egy SQL-adatbázis kimenet létrehozása leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a lekérdezés kimenete az adatbázist a lekérdezésekben használt. |
| Adatbázis | Az adatbázis, ahol küldendő a kimeneti neve. |
| Kiszolgálónév | Az SQL Database kiszolgáló neve. |
| Felhasználónév | A felhasználónév, amely hozzáfér a databas írni. |
| Jelszó | A jelszót a databas.e |
| Tábla | A tábla neve, ahol a kimeneti írása. A táblanév nem kis-és nagybetűket, és ezt a táblázatot a séma mezőket és a rendszer a feladat kimenetére által létrehozott típusok száma annak pontosan meg kell egyeznie. |

> [!NOTE]
> Jelenleg az Azure SQL Database elérhető egy Stream Analytics-feladat kimenetet használata támogatott. Csatolt egy adatbázist az SQL Server rendszert futtató Azure virtuális gépeket azonban nem támogatott. Ez az a jövőbeni kiadásokban változhat.
> 

## <a name="blob-storage"></a>Blob Storage
A BLOB storage egy nagy mennyiségű strukturálatlan adatok tárolása a felhőben költséghatékony, méretezhető megoldást kínál.  A dokumentációban a témakörben megismerkedhet az Azure Blob storage és a használatát, [Blobs használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a blob kimenet létrehozása.

| Tulajdonság neve | Leírás | 
| --- | --- |
| A kimeneti Alias | Egy rövid nevet a blob storage a lekérdezés kimenete a lekérdezésekben használt. |
| Tárfiók | A tárfiók, ahol küldendő a kimeneti neve. |
| Tárfiók kulcsa | A storage-fiókjához tartozó titkos kulcsot. |
| A tároló | Tárolók adja meg a Microsoft Azure Blob szolgáltatásban tárolt blobok logikai csoportosítását. Amikor egy blob feltöltése a Blob szolgáltatás, meg kell adnia, hogy a blob tárolója. |
| Elérésiút-minta | Választható. A fájl elérési út mintája a megadott tárolóban található blobok írásához használt. </br></br> Az elérési út mintája dönthet a dátum-idő változó egy vagy több példányát használja a blobok írt gyakorisága: </br> a {date}, {time} </br> </br>Megadhat egy egyéni {mező} neve az esemény adataiból particionálásához blobokat, ha a mező neve alfanumerikus, és szóközöket, kötőjeleket és aláhúzásjeleket tartalmazhat. Egyéni mezők korlátozásai a következők: <ul><li>(Másik oszlop "ID" és "id" oszlop között nem) iránti érzéketlensége eset</li><li>Beágyazott mezői nem engedélyezettek (helyette használja alias a feladat lekérdezésben szereplő "egybesimítására" mező)</li><li>A mezőnév nem használhatók kifejezések</li></ul>Példák: <ul><li>1. példa: cluster1/logs / {date} / {time}</li><li>2. példa: cluster1/logs / {date}</li><li>3. példa: cluster1 / {client_id} / {date} / {time}</li><li>4. példa: cluster1 / {myField} ahol ez: SELECT data.myField, myField a bemeneti;</li></ul><BR> A következő egyezmény fájlelnevezésnél követi: </br> {Elérési előtag Pattern}/schemaHashcode_Guid_Number.extension </br></br> Példa kimeneti fájlok: </br><ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li><li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul><br/>
| Dátumformátum | Választható. Ha a dátum jogkivonat a előtag elérési útját, válassza a dátumformátum, amelyben a fájlok vannak rendezve. . Példa: Éééé/hh/nn |
| Időformátum | Választható. Ha a idő jogkivonat előtag elérési, adja meg az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték HH. |
| Eseményszerializációs formátum | A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott.
| Encoding | Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogatja a CSV-adatok szerializálása során. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |
| Formátum | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben. |

A blob storage használata kimenetként, egy új fájl jön létre a BLOB a következő esetekben:

* Ha a fájl túllépte az engedélyezett blokkok (jelenleg 50 000) maximális számát. Előfordulhat, hogy a maximális engedélyezett száma nem érte el a maximális megengedett blob mérete érhető el. Például ha a kimeneti sebessége nagy, láthatja, hogy több bájt / blokk, és a fájl mérete nagyobb. Ha a kimeneti arány alacsony, valamennyi blokkja kevesebb adatot tartalmaz, és a fájl mérete kisebb.
* Ha a séma módosítva lett a kimenetben, és a kimeneti formátum igényel rögzített sémájába (fürt megosztott kötetei szolgáltatás és az Avro).  
* Ha a feladat újraindítása, külsőleg azt indítása és leállítása, felhasználó által vagy belső rendszer-karbantartási vagy a hiba helyreállítási.  
* Ha a lekérdezés teljes particionálása, minden egyes kimeneti partíció új fájl jön létre a rendszer.  
* Ha a felhasználó egy fájl vagy egy tárolót a tárfiók törlése.  
* A kimenet az idő az elérési út előtag mintát használ, ha egy új blob használatos, ha a lekérdezés helyezi át a következő órában.
* Ha a kimeneti particionálva van egy egyéni mező, egy új blob partíciós kulcs / létrejön, ha nem létezik.
*   A kimenet egy egyéni mező, ahol a partíciós kulcs számossága meghaladja 8000 particionálva van, ha egy új blob / partíciókulcs lehet létrehozni.

## <a name="event-hub"></a>Eseményközpont
A [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatása jól skálázható közzétételi-feliratkozási eseménybetöltőnek. Gyűjthet, hogy a több millió esemény / másodperc. Egy Eseményközpont kimenetként használata a Stream Analytics-feladat eredményének válásakor a bemeneti adatfolyam egy másik feladat.

Nincsenek, amelyek szükségesek ahhoz, hogy konfigurálta az Event Hubs adatfolyamokat kimenetként néhány paramétert.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias | Egy rövid nevet a lekérdezés kimenete az Event Hubs a lekérdezésekben használt. |
| Eseményközpont-névtér |Egy Eseményközpont-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Eseményközpont-névtér is létrejött. |
| Eseményközpont neve | Az Event Hubs kimeneti neve. |
| Eseményközpont szabályzatának neve | A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| Eseményközpont házirendjének kulcsa | A megosztott hozzáférési kulcs való hitelesítéshez szükséges hozzáférés az Event Hubs-névtérhez. |
| Partíciókulcs-oszlopot [opcionális] | Ez az oszlop a partíciókulcs az Event Hubs a kimenet tartalmazza. |
| Eseményszerializációs formátum | A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
| Encoding | A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum. |
| Elválasztó karakter | Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |
| Formátum | Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben. |

## <a name="power-bi"></a>Power BI
[A Power BI](https://powerbi.microsoft.com/) elemzés eredményeinek gazdag képi megjelenítés élmény biztosításához a Stream Analytics-feladat kimenetként használható. Ez a funkció működési irányítópultok jelentéskészítésre és jelentéskészítési folyamat metrika használható.

### <a name="authorize-a-power-bi-account"></a>Engedélyezze a Power BI-fiókja
1. A Power BI kiválasztásakor az Azure portálon kimenetként kéri egy meglévő Power BI felhasználó hitelesítése, vagy hozzon létre egy új Power BI-fiókot.  
   
   ![Engedélyezze a Power BI-felhasználó](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. Hozzon létre egy új fiókot, ha nem még egy, majd engedélyezze most kattintson.  A következő oldalon jelennek meg:
   
   ![Azure-fiók Power bi-ban](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. Ebben a lépésben adja meg a munkahelyi vagy iskolai fiókkal, amelyek engedélyezik a Power BI-kimenet. Ha még vannak nem jelentkezett a Power BI, válasszon jelentkezzen. Lehet, hogy a munkahelyi vagy iskolai fiókkal, használhatja a Power BI eltér az Azure-előfizetés fiók, amely is van jelenleg bejelentkezve.

### <a name="configure-the-power-bi-output-properties"></a>A Power BI-kimenet tulajdonságainak konfigurálása
Miután a Power BI-fiók hitelesítését, a Power BI-kimenet megadhatja a tulajdonságait. Az alábbi táblázat a tulajdonságnevek listáját és azok leírása a Power BI-kimenet konfigurálása.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a lekérdezés kimenete a Power bi-kimenetet a lekérdezésekben használt. |
| Csoportos munkaterület |Más Power BI-felhasználókkal való megosztás adatok engedélyezéséhez jelölje ki a csoportokat a Power BI-fiókjába belül, vagy válassza a "Saját munkaterület", ha nem szeretné, hogy egy csoport írni.  A Power BI hitelesítés megújítása meglévő csoport frissítése szükséges. |
| Adatkészlet neve |Adja meg a DataSet adatkészlet neve, amely a Power BI kimeneti használatához szükséges |
| Tábla neve |Adjon meg egy tábla nevét, a Power BI kimeneti adatkészlet alapján. Jelenleg a Power BI kimenetét a Stream Analytics-feladatok csak rendelkezhet egy tábla egy adatkészlet |

A Power BI-kimenet és az irányítópult konfigurálásának segédlet, tekintse meg a [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) cikk.

> [!NOTE]
> Explicit módon létrehozni az adatkészlet és a tábla a Power BI-irányítópultot. A DataSet adatkészlet és a tábla automatikusan kitölti a rendszer a feladat elindult, és a feladat szivattyúzó kimeneti elindul a Power BI-bA. Vegye figyelembe, hogy a beállítást, ha a feladat lekérdezési eredmények, a dataset nem készítése és táblát nem hozza létre. Vegye figyelembe, hogy ha a Power bi-ban már egy adatkészlet és a tábla neve megegyezik a egy olyan, a Stream Analytics-feladat, a felülírják a meglévő adatokat.
> 

### <a name="schema-creation"></a>Séma létrehozása
Az Azure Stream Analytics egy Power BI adatkészlet és a felhasználó nevében táblázatot hoz létre, ha egy nem létezik. A tábla minden más esetben frissül új értékeket. Jelenleg nincs korlátozása, hogy csak egy tábla egy adatkészleten belül létezhet.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Az adattípus átalakítása Stream Analytics Power bi-bA
Az Azure Stream Analytics frissíti az adatokat az adatmodellbe dinamikusan futásidőben, ha a kimeneti sémával megváltozik. Oszlop neve megváltozik, oszlop megváltozik, és hozzáadását és eltávolítását az oszlopok összes nyomon követni.

Ez a táblázat ismerteti az adatok típusú konverzió [Stream Analytics adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx) az energiagazdálkodási BIs [Entity Data Model (EDM) típusok](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) a POWER BI adatkészlet és a tábla nem léteznek.

A Stream Analytics | A Power bi-hoz
-----|-----|------------
bigint | Int64
típus: nvarchar(max) | Karakterlánc
dátum/idő | Dátum és idő
lebegőpontos | Dupla
Rekord tömb | Karakterlánc típus, konstans érték "IRecord" vagy "IArray"

### <a name="schema-update"></a>Séma frissítése
A Stream Analytics arra következtet, hogy az első kimenet események alapján adatok modellsémát. Később Ha szükséges, az adatok modellsémát frissül bejövő események, amelyek lehet, hogy nem felelnek meg az eredeti séma befogadásához.

A `SELECT *` lekérdezés kerülni kell a dinamikus sémafrissítés megakadályozása sorok között. Lehetséges teljesítményre gyakorolt hatása mellett azt is eredményezheti bizonytalanság a eredményeinek idő. A pontos a Power BI-irányítópulton megjelenítendő mezők ki kell jelölni. Emellett az adatértékek megfelelőnek a választott adattípussal kell.


Előző/aktuális | Int64 | Karakterlánc | Dátum és idő | Dupla
-----------------|-------|--------|----------|-------
Int64 | Int64 | Karakterlánc | Karakterlánc | Dupla
Dupla | Dupla | Karakterlánc | Karakterlánc | Dupla
Karakterlánc | Karakterlánc | Karakterlánc | Karakterlánc |  | Karakterlánc | 
Dátum és idő | Karakterlánc | Karakterlánc |  Dátum és idő | Karakterlánc


### <a name="renew-power-bi-authorization"></a>Újítsa meg a Power BI engedélyezési
Ha a Power BI-fiókja jelszavát a Stream Analytics-feladat lett létrehozva vagy utolsó hitelesítése után módosítja, a Stream Analytics újból hitelesítésre szeretné. Ha multi-factor Authentication (MFA) az Azure Active Directory (AAD) bérlő van beállítva, akkor is meg kell újítsa meg a Power BI engedélyezési kéthetente. A probléma tünete nem feladatkiemenetét és a "hitelesítés felhasználói hiba" műveletet rögzít:

  ![A Power BI frissítési jogkivonat hiba](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

A probléma megoldásához állítsa le a futó feladat, és nyissa meg a Power BI-kimenet.  Válassza ki a **újra a portálon** hivatkozásra, és indítsa újra a feladatot a **feladat utolsó befejezési időpontja** adatvesztés elkerülése érdekében.

  ![A Power BI megújítja a hitelesítési](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Az Azure Table storage](../storage/common/storage-introduction.md) kínál a magas rendelkezésre állású, nagymértékben méretezhető tárolót, így az alkalmazás automatikusan át tudja méretezni a felhasználói igényeknek. A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár, azt, amelyiket a séma kevesebb megkötések kihasználhatják a strukturált adatok. Az Azure Table storage segítségével tárolja az adatok megőrzése és hatékony lekérdezéséhez.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a tábla kimenet létrehozása.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a lekérdezés kimenete az a table storage a lekérdezésekben használt. |
| Tárfiók |A tárfiók, ahol küldendő a kimeneti neve. |
| Tárfiók kulcsa |A tárfiók tartozó hozzáférési kulcsot. |
| Táblanév |A tábla neve. A tábla lekérdezi jön létre, ha nem létezik. |
| Partíciókulcs |A partíciókulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs egy egyedi azonosítót a partíció egy entitás elsődleges kulcsának első részét egy adott táblán belül. Egy lehet, hogy legfeljebb 1 KB méretű karakterláncérték. |
| Sorkulcs |A sorkulcs tartalmazó kimeneti oszlop neve. A sorkulcs egy adott partíció egy entitás egyedi azonosítója. Egy entitás elsődleges kulcsának második részét képezi. A sorkulcs egy lehet, hogy legfeljebb 1 KB méretű karakterláncérték. |
| Köteg mérete |A kötegelt művelet rekordok száma. Az alapértelmezett (100) is a legtöbb feladatok használhatók. Tekintse meg a [tábla kötegelt művelet spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Ez a beállítás módosításával kapcsolatos további részletekért. |
 
## <a name="service-bus-queues"></a>Service Bus által kezelt üzenetsorok
[Service Bus-üzenetsorok](https://msdn.microsoft.com/library/azure/hh367516.aspx) kínálnak a First In, First Out (, FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Általában üzenetek kell fogad és dolgoz fel a fogadók a historikus sorrendben, amelyben addig adták hozzá, a várakozási sorba, és minden üzenetet kapott, és csak egy üzenetfogyasztó által feldolgozott várt.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a várólista kimenet létrehozása.

| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a lekérdezés a Service Bus-üzenetsorba kimenete a lekérdezésekben használt. |
| Service Bus-névtér |A Service Bus-névtér az üzenetküldési entitások készletének tárolója. |
| Üzenetsor neve |A Service Bus-üzenetsorba neve. |
| Sor házirendjének neve |Amikor létrehoz egy sort, a sor konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| Sor házirendjének kulcsa |A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt |
| Eseményszerializációs formátum |A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
| Encoding |A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |
| Formátum |Csak érvényes JSON-típus. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. |

A partíciók száma [a Service Bus SKU és mérete alapján](../service-bus-messaging/service-bus-partitioning.md). Partíciós kulcs egyedi egész szám érték minden partíció esetében.

## <a name="service-bus-topics"></a>Service Bus-üzenettémák
Service Bus-üzenetsorok adjon meg egy-egy kommunikációs módszer a küldőtől fogadóra, amíg [Service Bus-üzenettémakörök](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy egy-a-többhöz típusú kommunikációt biztosítanak.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a tábla kimenet létrehozása.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid nevet a Service Bus-témakörbe a lekérdezés kimenete a lekérdezésekben használt. |
| Service Bus-névtér |A Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrejött |
| Téma neve |Témák üzenetküldési entitások, hasonlók az eseményközpontokhoz és a várólisták. Úgy lettek kialakítva, hogy több különféle eszközről és a szolgáltatások szolgáltatásból összegyűjtsék az eseményfolyamokat. A témakör létrehozásakor is kap egy egyedi nevet. Egy témakörbe küldött üzenetek nem érhető el, kivéve, ha az előfizetés jön létre, ezért figyeljen oda arra egy vagy több előfizetés témakör alatt van |
| Téma házirendjének neve |Amikor létrehoz egy témát, a témakör konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal van |
| Téma házirendjének kulcsa |A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt |
| Eseményszerializációs formátum |A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
| Encoding |Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |

A partíciók száma [a Service Bus SKU és mérete alapján](../service-bus-messaging/service-bus-partitioning.md). Partíciós kulcs egyedi egész szám érték minden partíció esetében.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) van egy globálisan elosztott, több modellre adatbázis szolgáltatást, hogy a földgolyó méretét, a gazdag lekérdezési és az automatikus indexeléshez keresztül séma-független adatmodellekben, rugalmasan méretezhető korlátlan ajánlatok garantált alacsony késéssel és iparágvezető átfogó SLA-k. A Stream Analytics Cosmos DB adatgyűjtési beállítások kapcsolatos információkért tekintse meg a [Cosmos DB kimenetként a Stream Analytics](stream-analytics-documentdb-output.md) cikk.

> [!Note]
> Ilyenkor Azure Stream Analytics csak kapcsolatot támogat CosmosDB használatával **SQL API**.
> Egyéb Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB fiókok létrehozása, más API-khoz, az adatok nem megfelelően tárolódhat. 

A következő táblázat létrehozása az Azure Cosmos DB kimeneti tulajdonságait ismerteti.
| Tulajdonság neve | leírás |
| --- | --- |
| Kimeneti alias | Tekintse meg ezt a kimenetet a Stream Analytics lekérdezési aliast. |
| Fogadó | Cosmos DB |
| Importálási beállítás | Válassza a "Cosmos DB kiválasztása az előfizetésből", vagy "Adjon meg Cosmos adatbázis beállításainak manuális".
| Fiókazonosító | A név vagy a végpont URI-azonosítója a Cosmos DB fiók. |
| Fiókkulcs | A megosztott elérési kulcsot az Cosmos DB fiókhoz. |
| Adatbázis | A Cosmos DB adatbázis neve. |
| Gyűjteménynévminta | A gyűjtemény nevét vagy a használandó gyűjtemények gyűjteménynévmintája. <br/>A gyűjteménynév-formátum az opcionális {partition} token használatával, ahol a partíciók 0-tól kezdődnek lehet létrehozni. Két példa látható:  <br/>1. _MyCollection_ – már léteznie kell egy gyűjteményt a következő "MyCollection" néven.  <br/>2. _{Partition} MyCollection_ – a particionálási oszlop alapján. <br/>A particionáló oszlop gyűjteményeknek létezniük kell – "MyCollection0", "MyCollection1", "MyCollection2" és így tovább. |
| Partíciókulcs | Választható. Ez csak akkor szükséges, ha a gyűjtemény mintát használ egy {partition} token.<br/> A partíciós kulcs a kimeneti eseményekben a kimenet gyűjtemények közötti particionálására szolgáló kulcs megadásához használt mező neve.<br/> Egyetlen gyűjtemény kimeneti bármilyen tetszőleges kimeneti oszlop használható. Például PartitionId. |
| Dokumentumazonosító |Választható. A kimeneti eseményekben a mely Beszúrás vagy frissítés műveletek alapuló elsődleges kulcs megadásához használt mező neve.  

## <a name="azure-functions"></a>Azure Functions
Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerint, külön infrastruktúra üzembe helyezése és kezelése nélkül futtathat kódokat. Lehetővé teszi, amely az Azure vagy harmadik féltől származó szolgáltatással téve események által kiváltott kód megvalósítását.  Ez a lehetőség az Azure Functions eseményindítók válaszolni teszi természetes kimeneti számára egy Azure Stream Analytics. A konzolkimeneti adapter lehetővé teszi a felhasználóknak a Stream Analytics csatlakozni az Azure Functions, futtassa a parancsfájlt vagy kódrészletek, számos esemény adott válaszként.

Az Azure Stream Analytics az Azure Functions via HTTP-eseményindító hív meg. Az új Azure-függvény konzolkimeneti adapter áll rendelkezésre a következő konfigurálható tulajdonságokkal:

| Tulajdonság neve | leírás |
| --- | --- |
| Függvényalkalmazás |Az Azure Functions alkalmazás neve |
| Függvény |A függvény az Azure Functions alkalmazás neve |
| Kulcs |Ha szeretné használni az Azure-függvény egy másik előfizetésből, akkor megteheti a kulcsot a funkció elérésének biztosításával |
| Maximális kötegméret |Ez a tulajdonság segítségével minden egyes kimeneti kötegelt, az Azure-függvény küldött maximális méretének beállítása. Alapértelmezés szerint ennek az értéknek a 256 KB-os |
| Maximális kötegszám  |Azt jelzi, a neve, mint ez a tulajdonság lehetővé teszi, hogy adható meg az események maximális száma az egyes kötegekben, amely az Azure Functions számára küldi el. Az alapértelmezett maximális száma értéke 100 |

Azure Stream Analytics 413 (http-kérelmek entitás túl nagy) kivétel megkapja az Azure-függvény, csökkentik az Azure Functions küld a kötegek mérete. Az Azure-függvény kódjában ez a kivétel segítségével győződjön meg arról, hogy Azure Stream Analytics nem küld túlméretes kötegek. Emellett győződjön meg arról, hogy a maximális száma és mérete a függvényben értékei konzisztensek legyenek a Stream Analytics-portálon megadott értékek. 

Emellett helyzetben fennáll semmilyen esetben egy olyan időkeretet üzenetsorokra, nincs kimenet jön létre. Ennek eredményeképpen computeResult függvény nem hívják meg. Ez a viselkedés a beépített ablakos összesítő függvényekben összhangban.

## <a name="partitioning"></a>Particionálás

A következő táblázat összefoglalja a partíció támogatása és az egyes kimeneti kimeneti írók száma:

| Kimenet típusa | Particionálás támogatása | Partíciókulcs  | Kimeneti írók száma | 
| --- | --- | --- | --- |
| Azure Data Lake Store | Igen | Használja {a date} és {time} jogkivonatok az elérési út előtag mintában. Válassza ki a dátum formátumban, például éééé/hh/nn, nn/hh/éééé, hh-nn-éééé formátumra Konvertálni. Az időformátum ÓÓ használható. | A bemeneti particionálás a következő [teljesen párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). | 
| Azure SQL Database | Nem | None | Nem alkalmazható. | 
| Azure Blob Storage | Igen | Használja {a date} és {time} jogkivonatok és az elérési út mintája a esemény mezői alapján egyetlen {fieldname}. Válassza ki a dátum formátumban, például éééé/hh/nn, nn/hh/éééé, hh-nn-éééé formátumra Konvertálni. Az időformátum ÓÓ használható. | A bemeneti particionálás a következő [teljesen párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md). | 
| Azure-eseményközpont | Igen | Igen | Partíció igazítás függ.</br> Az Event Hubs partíciókulcs egyaránt igazodik a felsőbb rétegbeli (korábbi) lekérdezés lépést, írók száma megegyezik a számát kimenetét kimenethez Eseményközpont partíciókat. Minden egyes író használja az EventHub tartozó [EventHubSender osztály](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) is küldi az eseményeket az adott partícióra. </br> Ha a felsőbb rétegbeli (korábbi) lekérdezés lépést, írók száma nem igazodik partíciókulcs Eseményközpont kimenete megegyezik a korábbi lépésben partíciók száma. Minden egyes író használ EventHubClient [SendBatchAsync osztály](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) eseményt küldeni a kimeneti partíciókat. |
| Power BI | Nem | None | Nem alkalmazható. | 
| Azure Table Storage | Igen | A kimeneti oszlop.  | A bemeneti particionálás a következő [teljesen a lekérdezések párhuzamos működésű](stream-analytics-scale-jobs.md). | 
| Az Azure Service Bus-témakörbe | Igen | Automatikusan választott. A partíciók számának alapul a [Service Bus SKU és mérete](../service-bus-messaging/service-bus-partitioning.md). Partíciós kulcs egyedi egész szám érték minden partíció esetében.| Ugyanaz, mint a kimeneti témakörben található partíciók száma.  |
| Az Azure Service Bus-üzenetsorba | Igen | Automatikusan választott. A partíciók számának alapul a [Service Bus SKU és mérete](../service-bus-messaging/service-bus-partitioning.md). Partíciós kulcs egyedi egész szám érték minden partíció esetében.| Ugyanaz, mint a kimeneti várólistában lévő partíciók száma. |
| Azure Cosmos DB | Igen | {Partition} token gyűjteménynévmintája használja. {partition} érték a PARTITION BY záradék a lekérdezés alapul. | A bemeneti particionálás a következő [teljesen a lekérdezések párhuzamos működésű](stream-analytics-scale-jobs.md). |
| Azure Functions | Nem | None | Nem alkalmazható. | 

## <a name="output-batch-size"></a>Kimeneti kötegmérete
Az Azure Stream Analytics változó méretű kötegekben feldolgozni az eseményeket és kimenetek írásához használja. Általában a Stream Analytics-motor nem írási egy üzenet egyszerre és a hatékonyság kötegek használja. Ha mind a bejövő és kimenő események sebessége nagy nagyobb kötegek használ. Ha a kimenő forgalom sebessége alacsony, tartsa alacsony késleltetésű kisebb kötegekben használ. 

A következő táblázat ismerteti azokat a kimeneti kötegelés szempontok:

| Kimenet típusa | Maximális üzenetméret | Kötegelt méretoptimalizálás |
| :--- | :--- | :--- | 
| Azure Data Lake Store | Lásd: [korlátozza a Data Lake-tároló](../azure-subscription-service-limits.md#data-lake-store-limits) | 4 MB írási művelet |
| Azure SQL Database | 10 000 maximális sorszám egyetlen tömeges beszúrás</br>Egyetlen tömeges beszúrás sorszám 100 perc </br>Lásd még: [Azure SQL korlátozza.](../sql-database/sql-database-resource-limits.md) |  Minden kötegelt kezdetben maximális kötegmérete beszúrt tömeges, és előfordulhat, hogy ossza fel kötegelt fele (csak minimális kötegméret) alapú az SQL-Újrapróbálkozást lehetővé tevő hiba esetén. |
| Azure Blob Storage | Lásd: [Azure Storage korlátozza.](../azure-subscription-service-limits.md#storage-limits) | Maximális Blob blokk mérete 4 MB</br>Maximális Blob bock leképezésszáma 50000 |
| Azure-eseményközpont   | Egy üzenet 256 KB </br>Lásd még: [korlátozza az Event Hubs](../event-hubs/event-hubs-quotas.md) |    Bemeneti particionálás nem igazítása, ha minden esemény egy EventData külön-külön csomagolni és küldött egy kötegben, akár a maximális üzenetméret (Premium termékváltozat 1 MB). </br></br>  Bemeneti / kimeneti particionálás igazodik, amikor több esemény be egy maximális üzenetméret legfeljebb egyetlen EventData csomagolt és küldött.    |
| Power BI | Lásd: [korlátozza a Power BI Rest API-n](https://msdn.microsoft.com/library/dn950053.aspx) |
| Azure Table Storage | Lásd: [Azure Storage korlátozza.](../azure-subscription-service-limits.md#storage-limits) | Alapértelmezett egyetlen tranzakciónként 100 entitást, és beállítható úgy, hogy szükség esetén a kisebb értékek. |
| Az Azure Service Bus-üzenetsorba   | Egy üzenet 256 KB</br> Lásd még: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md) | Egy üzenet egyszeri esemény |
| Az Azure Service Bus-témakörbe | Egy üzenet 256 KB</br> Lásd még: [korlátozza a Service Bus](../service-bus-messaging/service-bus-quotas.md) | Egy üzenet egyszeri esemény |
| Azure Cosmos DB   | Lásd: [Azure Cosmos DB korlátozza.](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | Kötegelt méretezés és írni a gyakoriság módosított dinamikusan CosmosDB válaszokat. </br> A Stream Analytics nincs előre meghatározott korlátozás. |
| Azure Functions   | | Alapértelmezett köteg mérete 246 KB. </br> Alapértelmezett eseményszám kötegenként érték 100. </br> A Köteg mérete konfigurálható, és növelhető vagy csökkenthető, a Stream Analytics [kimeneti beállítások](#azure-functions). 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors üzembe helyezés: A Stream Analytics-feladat létrehozása az Azure portál használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
