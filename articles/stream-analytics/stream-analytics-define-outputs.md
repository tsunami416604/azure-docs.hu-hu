---
title: 'A Stream Analytics kimenetek: tárolási, elemzés lehetőségei |} Microsoft Docs'
description: További információk a Stream Analytics-adatok kimenetek beállításai, többek között a Power BI analysis eredmények célzó.
keywords: adatok átalakítása, az elemzés eredményeinek, adatok tárolási lehetőségek
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/18/2017
ms.author: sngun
ms.openlocfilehash: ae8c4daf6b4beff3b1baf7ef5a5f3b84d706401a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>A Stream Analytics kimenetek: tárolási, elemzés lehetőségei
A Stream Analytics-feladat szerzői, vegye figyelembe a kapott adatokban felhasznált hogyan. Hogyan, az eredmények megtekinthetők a Stream Analytics-feladat, és képes tárolására?

Ahhoz, hogy az alkalmazás minták számos, az Azure Stream Analytics rendelkezik kimeneti tárolja, és az elemzés eredményeinek megtekintése különböző lehetőségek közül. Ez megkönnyíti a feladat kimenetére megtekintése és adatraktározási vagy más célból a használat és a feladat kimenete a rugalmasságot biztosít. Kimenetet a feldolgozás konfigurált már léteznie kell a feladat elindult és események start továbbítására. Például a Blob storage kimenetként használatakor a feladat nem hoz létre egy tárfiókot automatikusan. Hozzon létre egy tárfiókot, a Stream Analytics-feladat indítása előtt.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store
A Stream Analytics támogatja [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Ez a tároló lehetővé teszi, hogy a műveleti és felderítési jellegű bármilyen méretű, típusú és feldolgozási sebességű adatok. További, Stream Analyticsnek tudnia kell a Data Lake Store jogosultsággal. Engedélyezési és (ha szükséges), a Data Lake Store a feliratkozás ismerteti a [Data Lake kimeneti cikk](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Egy Azure Data Lake Store engedélyezése
Data Lake tárolási kiválasztásakor az Azure portálon kimenetként kéri egy kapcsolat egy meglévő Data Lake Store hitelesítéséhez.  

![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Az alább látható módon Ezután adja meg a Data Lake Store kimeneti tulajdonságai:

![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Az alábbi táblázat felsorolja a tulajdonság nevét és a leírás, egy Data Lake Store kimenet létrehozásához szükséges.

<table>
<tbody>
<tr>
<td><B>TULAJDONSÁG NEVE</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>A kimeneti Alias</td>
<td>Egy rövid nevet a lekérdezés kimenete a Data Lake Store a lekérdezésekben használt.</td>
</tr>
<tr>
<td>Fiók neve</td>
<td>A Data Lake-tárfiókra ahol küldendő a kimeneti neve. A legördülő listából válassza ki, amelyhez a portál bejelentkezett felhasználó hozzáfér a Data Lake Store-fiókok jelenik meg.</td>
</tr>
<tr>
<td>Elérési út előtag mintája</td>
<td>A következő egyezmény fájlelnevezésnél követi: <BR>{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension <BR> <BR>Példa kimeneti fájlok:<BR>Myoutput/20170901/00/45434_gguid_1.csv <BR>Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR>Itt is az olyan helyzetekben, ahol egy új fájl jön létre:<BR>1. A kimeneti sémával módosítása <BR>2. Külső vagy belső indítsa újra a feladatok<BR><BR>Továbbá ha a fájl elérési út mintája nem tartalmaz egy záró "/", a rendszer a legutóbbi minta elérési útjának fájlnév előtag.<BR><BR>Példa:<BR>Az az elérési út mintája: mappa1/logs/HH, a létrehozott fájl nézhet ki: folder1/logs/02_134343_gguid_1.csv</td>
</tr>
<tr>
<td>Dátum formátumban [<I>választható</I>]</td>
<td>Ha a dátum jogkivonat a előtag elérési útját, válassza a dátumformátum, amelyben a fájlok vannak rendezve. . Példa: Éééé/hh/nn</td>
</tr>
<tr>
<td>Idő formátumot [<I>választható</I>]</td>
<td>Ha a idő jogkivonat előtag elérési, adja meg az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték HH.</td>
</tr>
<tr>
<td>Esemény szerializálási formátum</td>
<td>A kimeneti adatok szerializálási formátum. JSON, CSV és az avro-hoz támogatott.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.</td>
</tr>
<tr>
<td>Elválasztó karakter</td>
<td>Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogatja a CSV-adatok szerializálása során. Támogatott értékei, vesszővel, pontosvesszővel válassza el, lemezterület, lapon, majd függőleges vonal.</td>
</tr>
<tr>
<td>Formátum</td>
<td>Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Újítsa meg a Data Lake Store engedélyezési
Újból hitelesítésre a Data Lake Store-fiókot, ha a jelszó megváltozott a feladat meg lett létrehozva, vagy utolsó hitelesített kell.

![Data Lake Store engedélyezése](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Az Azure SQL Database](https://azure.microsoft.com/services/sql-database/) használható kimenetként a relációs jellegű adatokhoz, vagy olyan alkalmazásnál, amely egy relációs adatbázisban szolgáltatott tartalmaktól függnek. Stream Analytics-feladatok írni a meglévő tábla Azure SQL-adatbázisban.  A következő tábla sémáját pontosan meg kell egyeznie a mezők és azok típusát, a feladat kimenete alatt. Egy [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) az SQL-adatbázis output paraméter is (Ez egy előzetes verziójú funkciók) keresztül kimenetként is megadható. Az alábbi táblázat felsorolja a tulajdonság nevét és egy SQL-adatbázis kimenet létrehozása leírását.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a lekérdezés kimenete az adatbázist a lekérdezésekben használt. |
| Adatbázis |Annak a ahol küldendő a kimeneti adatbázisnak a nevét |
| Kiszolgáló neve |Az SQL adatbázis-kiszolgáló neve |
| Felhasználónév |A felhasználónév, amelynek hozzáférési írni az adatbázisba |
| Jelszó |A jelszó az adatbázishoz való kapcsolódáshoz |
| Tábla |A tábla neve, ahol a kimeneti írása. A táblanév nem kis-és nagybetűket, és ezt a táblázatot a séma mezőket és a rendszer a feladat kimenetére által létrehozott típusok száma annak pontosan meg kell egyeznie. |

> [!NOTE]
> Jelenleg az Azure SQL Database elérhető egy Stream Analytics-feladat kimenetet használata támogatott. Csatolt egy adatbázist az SQL Server rendszert futtató Azure virtuális gépeket azonban nem támogatott. Ez az a jövőbeni kiadásokban változhat.
> 
> 

## <a name="blob-storage"></a>Blob Storage
A BLOB storage egy nagy mennyiségű strukturálatlan adatok tárolása a felhőben költséghatékony, méretezhető megoldást kínál.  A dokumentációban a témakörben megismerkedhet az Azure Blob storage és a használatát, [Blobs használata](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a blob kimenet létrehozása.

<table>
<tbody>
<tr>
<td>TULAJDONSÁG NEVE</td>
<td>LEÍRÁS</td>
</tr>
<tr>
<td>A kimeneti Alias</td>
<td>Egy rövid nevet a blob storage a lekérdezés kimenete a lekérdezésekben használt.</td>
</tr>
<tr>
<td>Tárfiók</td>
<td>A tárfiók, ahol küldendő a kimeneti neve.</td>
</tr>
<tr>
<td>Tárfiók kulcsa</td>
<td>A storage-fiókjához tartozó titkos kulcsot.</td>
</tr>
<tr>
<td>A tároló</td>
<td>Tárolók adja meg a Microsoft Azure Blob szolgáltatásban tárolt blobok logikai csoportosítását. Amikor egy blob feltöltése a Blob szolgáltatás, meg kell adnia, hogy a blob tárolója.</td>
</tr>
<tr>
<td>Elérési út előtag mintája [opcionális]</td>
<td>A fájl elérési út mintája a megadott tárolóban található blobok írásához használt. <BR> Az elérési út mintája választhatja a következő 2 változó egy vagy több példányát használja a blobok írt gyakorisága: <BR> {date}, {time} <BR> 1. példa: cluster1/logs / {date} / {time} <BR> 2. példa: cluster1/logs / {date} <BR> <BR> A következő egyezmény fájlelnevezésnél követi: <BR> {Path Prefix Pattern}/schemaHashcode_Guid_Number.extension <BR> <BR> Példa kimeneti fájlok: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Itt is az olyan helyzetekben, ahol egy új fájl jön létre: <BR> 1. Aktuális-fájl túllépi a maximálisan megengedett száma (jelenleg 50 000) <BR> 2. A kimeneti sémával módosítása <BR> 3. Külső vagy belső indítsa újra a feladatok  </td>
</tr>
<tr>
<td>[Választható] dátumformátum</td>
<td>Ha a dátum jogkivonat a előtag elérési útját, válassza a dátumformátum, amelyben a fájlok vannak rendezve. . Példa: Éééé/hh/nn</td>
</tr>
<tr>
<td>[Választható] idő formátuma</td>
<td>Ha a idő jogkivonat előtag elérési, adja meg az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték HH.</td>
</tr>
<tr>
<td>Esemény szerializálási formátum</td>
<td>A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.</td>
</tr>
<tr>
<td>Elválasztó karakter</td>
<td>Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogatja a CSV-adatok szerializálása során. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal.</td>
</tr>
<tr>
<td>Formátum</td>
<td>Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben.</td>
</tr>
</tbody>
</table>

A blob storage használata kimenetként, egy új fájl jön létre a BLOB a következő esetekben:

* Ha a fájl meghaladja az engedélyezett blokkok (vegye figyelembe, hogy a megengedett maximális száma eléri a maximális megengedett blobméretével elérése nélkül. Például ha a kimeneti sebessége nagy, láthatja, hogy több bájt / blokk, és a fájl mérete nagyobb. Ha a kimeneti arány alacsony, minden blokk kevesebb adatot tartalmaz, és a fájl mérete kisebb,.)  
* Ha a séma módosítva lett a kimenetben, és a kimeneti formátum igényel rögzített sémájába (fürt megosztott kötetei szolgáltatás és az Avro).  
* Ha a feladat újraindításakor vagy külső vagy belső újra kell indítani a feladatot.  
* Ha a lekérdezés teljes particionálása, minden egyes kimeneti partíció új fájl jön létre a rendszer.  
* Ha a felhasználó egy fájl vagy egy tárolót a tárfiók törlése.  
* A kimenet az idő az elérési út előtag mintát használ, ha egy új blob használatos, ha a lekérdezés helyezi át a következő órában.

## <a name="event-hub"></a>Eseményközpont
[Az Event Hubs](https://azure.microsoft.com/services/event-hubs/) egy kiválóan méretezhető közzétételi-feliratkozási eseménybetöltőnek. Gyűjthet, hogy a több millió esemény / másodperc. Egy Eseményközpont kimenetként használata a Stream Analytics-feladat eredményének válásakor a bemeneti adatfolyam egy másik feladat.

Nincsenek, amelyek szükségesek ahhoz, hogy konfigurálta az Event Hubs adatfolyamokat kimenetként néhány paramétert.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a lekérdezés kimenete az Event Hubs a lekérdezésekben használt. |
| Service Bus-névtér |A Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrejött |
| Eseményközpont |Az Event Hubs kimeneti neve |
| Eseményközpont házirend neve |A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal neve van. |
| Event Hub házirend kulcs |A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt |
| [Választható] partíciós kulcs típusú oszlop |Ez az oszlop a partíciókulcs az Event Hubs a kimenet tartalmazza. |
| Esemény szerializálási formátum |A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
| Encoding |A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |
| Formátum |Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. A tömb csak akkor, ha a feladat leáll vagy a Stream Analytics rendelkezik áthelyezni a következő alkalommal ablakra van zárva. Általában célszerű sor használandó elválasztott JSON, mivel nem igényel semmilyen különleges kezelést való továbbra is van a kimeneti fájl írása közben. |

## <a name="power-bi"></a>Power BI
[A Power BI](https://powerbi.microsoft.com/) elemzés eredményeinek gazdag képi megjelenítés élmény biztosításához a Stream Analytics-feladat kimenetként használható. Ez a funkció működési irányítópultok jelentéskészítésre és jelentéskészítési folyamat metrika használható.

### <a name="authorize-a-power-bi-account"></a>Engedélyezze a Power BI-fiókja
1. A Power BI kiválasztásakor az Azure portálon kimenetként kéri egy meglévő Power BI felhasználó hitelesítése, vagy hozzon létre egy új Power BI-fiókot.  
   
   ![Engedélyezze a Power BI-felhasználó](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Hozzon létre egy új fiókot, ha nem még egy, majd engedélyezze most kattintson.  Egy a következőhöz hasonló képernyő áll rendelkezésre.  
   
   ![Azure-fiók Power bi-ban](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. Ebben a lépésben adja meg a munkahelyi vagy iskolai fiókkal, amelyek engedélyezik a Power BI-kimenet. Ha még vannak nem jelentkezett a Power BI, válasszon jelentkezzen. Lehet, hogy a munkahelyi vagy iskolai fiókkal, használhatja a Power BI eltér az Azure-előfizetés fiók, amely is van jelenleg bejelentkezve.

### <a name="configure-the-power-bi-output-properties"></a>A Power BI-kimenet tulajdonságainak konfigurálása
Miután a Power BI-fiók hitelesítését, a Power BI-kimenet megadhatja a tulajdonságait. Az alábbi táblázat a tulajdonságnevek listáját és azok leírása a Power BI-kimenet konfigurálása.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a lekérdezés kimenete a Power bi-kimenetet a lekérdezésekben használt. |
| Csoport munkaterület |Más Power BI-felhasználókkal való megosztás adatok engedélyezéséhez jelölje ki a csoportokat a Power BI-fiókjába belül, vagy válassza a "Saját munkaterület", ha nem szeretné, hogy egy csoport írni.  A Power BI hitelesítés megújítása meglévő csoport frissítése szükséges. |
| A DataSet neve |Adja meg a DataSet adatkészlet neve, amely a Power BI kimeneti használatához szükséges |
| Táblanév |Adjon meg egy tábla nevét, a Power BI kimeneti adatkészlet alapján. Jelenleg a Power BI kimenetét a Stream Analytics-feladatok csak rendelkezhet egy tábla egy adatkészlet |

A Power BI-kimenet és az irányítópult konfigurálásának segédlet, ellenőrizze a [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) cikk.

> [!NOTE]
> Explicit módon létrehozni az adatkészlet és a tábla a Power BI-irányítópultot. A DataSet adatkészlet és a tábla automatikusan kitölti a rendszer a feladat elindult, és a feladat szivattyúzó kimeneti elindul a Power BI-bA. Vegye figyelembe, hogy a beállítást, ha a feladat lekérdezési eredmények, a dataset nem készítése és táblát nem hozza létre. Vegye figyelembe, hogy ha a Power bi-ban már egy adatkészlet és a tábla neve megegyezik a egy olyan, a Stream Analytics-feladat, a felülírják a meglévő adatokat.
> 
> 

### <a name="schema-creation"></a>Séma létrehozása
Az Azure Stream Analytics egy Power BI adatkészlet és a felhasználó nevében táblázatot hoz létre, ha egy nem létezik. A tábla minden más esetben frissül új értékeket. Jelenleg nincs korlátozása, hogy csak egy tábla egy adatkészleten belül létezhet.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Az adattípus átalakítása Stream Analytics Power bi-bA
Az Azure Stream Analytics frissíti az adatokat az adatmodellbe dinamikusan futásidőben, ha a kimeneti sémával megváltozik. Oszlop neve megváltozik, oszlop megváltozik, és hozzáadását és eltávolítását az oszlopok összes nyomon követni.

Ez a táblázat ismerteti az adatok típusú konverzió [Stream Analytics adattípusok](https://msdn.microsoft.com/library/azure/dn835065.aspx) az energiagazdálkodási BIs [Entity Data Model (EDM) típusok](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) a POWER BI adatkészlet és a tábla nem léteznek.


A Stream Analytics | To Power BI
-----|-----|------------
bigint | Int64
típus: nvarchar(max) | Karakterlánc
dátum/idő | Dátum és idő
Lebegőpontos | Dupla
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
Újból hitelesítésre a Power BI-fiókjába, ha a jelszó megváltozott a feladat meg lett létrehozva, vagy utolsó hitelesített kell. Ha a multi-factor Authentication (MFA) az Azure Active Directory (AAD) bérlő szükség újítsa meg a Power BI engedélyezési kéthetente van konfigurálva. A probléma tünete nem feladatkiemenetét és a "hitelesítés felhasználói hiba" műveletet rögzít:

  ![A Power BI frissítési jogkivonat hiba](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

A probléma megoldásához állítsa le a futó feladat, és nyissa meg a Power BI-kimenet.  A "Megújítási engedélyezési" hivatkozásra, és indítsa újra a feladatot az adatvesztés elkerülése érdekében utolsó befejezési időpontja.

  ![A Power BI megújítja a hitelesítési](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Az Azure Table storage](../storage/common/storage-introduction.md) kínál a magas rendelkezésre állású, nagymértékben méretezhető tárolót, így az alkalmazás automatikusan át tudja méretezni a felhasználói igényeknek. A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár, azt, amelyiket a séma kevesebb megkötések kihasználhatják a strukturált adatok. Az Azure Table storage segítségével tárolja az adatok megőrzése és hatékony lekérdezéséhez.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a tábla kimenet létrehozása.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a lekérdezés kimenete az a table storage a lekérdezésekben használt. |
| Tárfiók |A tárfiók, ahol küldendő a kimeneti neve. |
| Tárfiók kulcsa |A tárfiók tartozó hozzáférési kulcsot. |
| Táblanév |A tábla neve. A tábla lekérdezi jön létre, ha nem létezik. |
| Partíciókulcs |A partíciókulcsot tartalmazó kimeneti oszlop neve. A partíciós kulcs egy egyedi azonosítót a partíció egy entitás elsődleges kulcsának első részét egy adott táblán belül. Egy lehet, hogy legfeljebb 1 KB méretű karakterláncérték. |
| Sorkulcsa |A sorkulcs tartalmazó kimeneti oszlop neve. A sorkulcs egy adott partíció egy entitás egyedi azonosítója. Egy entitás elsődleges kulcsának második részét képezi. A sorkulcs egy lehet, hogy legfeljebb 1 KB méretű karakterláncérték. |
| Köteg mérete |A kötegelt művelet rekordok száma. Általában az alapértelmezett érték elegendő az legtöbb feladatok, tekintse meg a [tábla kötegelt művelet spec](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) Ez a beállítás módosításával kapcsolatos további részletekért. |
 
## <a name="service-bus-queues"></a>Service Bus által kezelt üzenetsorok
[Service Bus-üzenetsorok](https://msdn.microsoft.com/library/azure/hh367516.aspx) kínálnak a First In, First Out (, FIFO) üzenetküldést biztosítanak egy vagy több versengő fogyasztó számára. Általában üzenetek kell fogad és dolgoz fel a fogadók a historikus sorrendben, amelyben addig adták hozzá, a várakozási sorba, és minden üzenetet kapott, és csak egy üzenetfogyasztó által feldolgozott várt.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a várólista kimenet létrehozása.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a lekérdezés a Service Bus-üzenetsorba kimenete a lekérdezésekben használt. |
| Service Bus-névtér |A Service Bus-névtér az üzenetküldési entitások készletének tárolója. |
| Várólista neve |A Service Bus-üzenetsorba neve. |
| Várólista házirend neve |Amikor létrehoz egy sort, a sor konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. |
| Várólista házirend kulcs |A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt |
| Esemény szerializálási formátum |A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
| Encoding |A fürt megosztott kötetei szolgáltatás és a JSON UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |
| Formátum |Csak érvényes JSON-típus. Sorral elválasztott beállítás megadja, hogy a formázott azzal, hogy minden JSON-objektum sortöréssel elválasztva. A tömb határozza meg, hogy a formázott JSON-objektumokat tömbként. |

## <a name="service-bus-topics"></a>Service Bus-üzenettémák
Service Bus-üzenetsorok adjon meg egy-egy kommunikációs módszer a küldőtől fogadóra, amíg [Service Bus-üzenettémakörök](https://msdn.microsoft.com/library/azure/hh367516.aspx) egy egy-a-többhöz típusú kommunikációt biztosítanak.

Az alábbi táblázat felsorolja a tulajdonságnevek és azok leírását a tábla kimenet létrehozása.

| Tulajdonság neve | Leírás |
| --- | --- |
| A kimeneti Alias |Egy rövid nevet a Service Bus-témakörbe a lekérdezés kimenete a lekérdezésekben használt. |
| Service Bus-névtér |A Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrejött |
| Témakör neve |Témák üzenetküldési entitások, hasonlók az eseményközpontokhoz és a várólisták. Úgy lettek kialakítva, hogy több különféle eszközről és a szolgáltatások szolgáltatásból összegyűjtsék az eseményfolyamokat. A témakör létrehozásakor is kap egy egyedi nevet. Egy témakörbe küldött üzenetek nem érhető el, kivéve, ha az előfizetés jön létre, ezért figyeljen oda arra egy vagy több előfizetés témakör alatt van |
| A témakör házirend neve |Amikor létrehoz egy témát, a témakör konfigurálása lapon is létrehozhat a megosztott elérési házirendeket. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal van |
| A témakör házirend kulcs |A megosztott elérési kulcsot a Service Bus-névtér való hozzáférés hitelesítéséhez használt |
| Esemény szerializálási formátum |A kimeneti adatok szerializálási formátum.  JSON, CSV és az avro-hoz támogatott. |
 | Encoding |Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumban, kódolással meg kell adni. Az UTF-8 jelenleg az egyetlen támogatott kódolási formátum |
| Elválasztó karakter |Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogat az adatok CSV formátumban történő szerializálásához. Támogatott értékei vesszővel, pontosvesszővel válassza el, hely, lap és függőleges vonal. |

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Az Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) van egy globálisan elosztott, több modellre adatbázis szolgáltatást, hogy a földgolyó méretét, a gazdag lekérdezési és az automatikus indexeléshez keresztül séma-független adatmodellekben, rugalmasan méretezhető korlátlan ajánlatok garantált alacsony késéssel és iparágvezető átfogó SLA-k.

Az alábbi lista részletesen a tulajdonságnevek és azok leírását egy Azure Cosmos DB kimenet létrehozása.

* **A kimeneti Alias** – egy aliast tekintse meg a kimenetet a Stream Analytics-lekérdezés  
* **A fiók neve** – a név vagy a végpont URI-azonosítója a Cosmos DB fiók.  
* **Kulcs fiók** – Cosmos DB fiók a megosztott elérési kulcsot.  
* **Adatbázis** – Cosmos-adatbázis az adatbázis nevét.  
* **Gyűjteménynévmintája** – a gyűjtemény nevét vagy a használandó gyűjtemények gyűjteménynévmintája. A gyűjteménynév-formátum az opcionális {partition} token használatával, ahol a partíciók 0-tól kezdődnek lehet létrehozni. Minta érvényes bemenetei a következők:  
  1\) MyCollection – egy gyűjteményt a következő "MyCollection" néven már léteznie kell.  
  2\) MyCollection {partition} – ilyen gyűjteményeknek létezniük kell – "MyCollection0", "MyCollection1", "MyCollection2" és így tovább.  
* **Kulcs partícióazonosító** – nem kötelező. Ez csak akkor van szükség, ha a gyűjteménynévmintája partíció jogkivonatot használ. A kimeneti eseményekben a kimenet gyűjtemények közötti particionálására szolgáló kulcs megadásához használt mező neve. Egyetlen gyűjtemény kimeneti például a PartitionId bármilyen tetszőleges kimeneti oszlop használható.  
* **Dokumentálja azonosító** – nem kötelező. A kimeneti eseményekben a mely Beszúrás vagy frissítés műveletek alapuló elsődleges kulcs megadásához használt mező neve.  

## <a name="azure-functions-in-preview"></a>Az Azure Functions (az előzetes verzió)
Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, amellyel igény szerint, külön infrastruktúra üzembe helyezése és kezelése nélkül futtathat kódokat. Lehetővé teszi, amely az Azure vagy harmadik féltől származó szolgáltatással téve események által kiváltott kód megvalósítását.  Ez a lehetőség az Azure Functions eseményindítók válaszolni teszi természetes kimeneti számára egy Azure Stream Analytics. A konzolkimeneti adapter lehetővé teszi a felhasználóknak a Stream Analytics csatlakozni az Azure Functions, futtassa a parancsfájlt vagy kódrészletek, számos esemény adott válaszként.

Az Azure Stream Analytics az Azure Functions via HTTP-eseményindító hív meg. Az új Azure-függvény konzolkimeneti adapter áll rendelkezésre a következő konfigurálható tulajdonságokkal:

| Tulajdonság neve | Leírás |
| --- | --- |
| Függvényalkalmazás |Az Azure Functions alkalmazás neve |
| Függvény |A függvény az Azure Functions alkalmazás neve |
| Maximális kötegméret |Ez a tulajdonság segítségével minden egyes kimeneti kötegelt, az Azure-függvény küldött maximális méretének beállítása. Alapértelmezés szerint ennek az értéknek a 256 KB-os |
| Maximális kötegek száma  |Azt jelzi, a neve, mint ez a tulajdonság lehetővé teszi, hogy adható meg az események maximális száma az egyes kötegekben, amely az Azure Functions számára küldi el. Az alapértelmezett maximális száma értéke 100 |
| Kulcs |Ha szeretné használni az Azure-függvény egy másik előfizetésből, akkor megteheti a kulcsot a funkció elérésének biztosításával |

Vegye figyelembe, hogy Azure Stream Analytics 413 (http-kérelmek entitás túl nagy) kivétel megkapja az Azure-függvény, csökkentik az Azure Functions küld a kötegek mérete. Az Azure-függvény kódjában ez a kivétel segítségével győződjön meg arról, hogy Azure Stream Analytics nem küld túlméretes kötegek. Emellett győződjön meg arról, hogy a maximális száma és mérete a függvényben értékei konzisztensek legyenek a Stream Analytics-portálon megadott értékek. 

Emellett helyzetben fennáll semmilyen esetben egy olyan időkeretet üzenetsorokra, nincs kimenet jön létre. Ennek eredményeképpen computeResult függvény nem hívják meg. Ez a viselkedés a beépített ablakos összesítő függvényekben összhangban.


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
Bemutattuk Önnek a Stream Analytics felügyelt szolgáltatást, amely streamelő elemzéseket biztosít az eszközök internetes hálózatáról (IoT) származó adatokon. További információk a szolgáltatásról:

* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
