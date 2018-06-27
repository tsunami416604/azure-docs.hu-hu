---
title: Referenciaadatok használata Azure Stream Analytics keresések
description: Ez a cikk ismerteti a referenciaadatok segítségével lehet megkeresni vagy a lekérdezés egy Azure Stream Analytics-feladat adatainak.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: 25c25a58b4c6eab5419f645e8e916e034e7803dd
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016890"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Referenciaadatok használja a Stream Analytics keresések
Referenciaadatok (más néven keresési tábla) olyan véges adatok statikus vagy lassan módosítása ideiglenesek, keresés végrehajtásához, vagy az adatfolyam függ. Az Azure Stream Analytics betölti a referenciaadatok a memóriában kis késleltetésű adatfolyam feldolgozása eléréséhez. Annak hivatkozás adatok az Azure Stream Analytics-feladat, általában fogja használni a [hivatkozás adatok csatlakozás](https://msdn.microsoft.com/library/azure/dn949258.aspx) a lekérdezésben. A Stream Analytics használ a tárolási réteg a Referenciaadatoknál Azure Blob Storage tárolót, és az Azure Data Factory hivatkozás adatokat át legyenek-e vagy átmásolva az Azure Blob storage referenciaadatok, használni [tetszőleges számú felhőalapú és a helyszíni adattárolókhoz](../data-factory/copy-activity-overview.md). Referenciaadatok növekvő sorrendben az a dátum/idő, a blob neve (a bemeneti konfigurációs meghatározott) blobok sorozataként van modellezve. Az **csak** támogatja a sorozat végére hozzáadása egy dátum/idő használatával **nagyobb** a sorban utolsó blob a megadottól.

A Stream Analytics támogatja a referenciaadatok **maximális mérete 300 MB**. A referenciaadatok maximális mérete 300 MB határértéke elérhető csak az egyszerű lekérdezések. Ha a lekérdezés összetettsége növekszik ablakos összesítéseket, az időalapú illesztéseket és historikus analitikai funkciók, például állapot-nyilvántartó feldolgozási felvenni várható, hogy a legnagyobb támogatott-e a hivatkozási adatokat csökkenő méretét. Azure Stream Analytics nem tölthető be a referenciaadatok és összetett műveleteket hajtson végre, ha a feladat elfogy a memória, és sikertelen lesz. Ilyen esetekben SU % kihasználtsági metrika eléri a 100 %.    

|**Streamelési egységek számát**  |**KB. maximális méretét (megabájtban) támogatott**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 megalapozása   |300   |

Egy feladat túl 6 Streaming Units számát növelése nem növeli referenciaadatok támogatott maximális méretét.

Tömörítés támogatása nem érhető el a referenciaadatoknál. 

## <a name="configuring-reference-data"></a>Referenciaadatok konfigurálása
A referenciaadatok konfigurálásához először szeretne létrehozni, amely típusú bemeneti **referenciaadatok**. Az alábbi táblázat azt ismerteti, hogy minden egyes tulajdonsága, amely meg kell adnia a leírás a referenciaadatok bemeneti létrehozása során:

|**Tulajdonság neve**  |**Leírás**  |
|---------|---------|
|A bemeneti Alias   | Egy rövid nevet használt a feladat lekérdezésben a bemeneti hivatkozást.   |
|Tárfiók   | Hol találhatók a BLOB storage-fiók neve. Ha ugyanazt az előfizetést, a Stream Analytics-feladat, válassza ki azt a a legördülő listán.   |
|Tárfiók kulcsa   | A storage-fiókjához tartozó titkos kulcsot. Ez automatikusan lekérdezi megadni, ha a tárfiók ugyanahhoz az előfizetéshez, mint a Stream Analytics-feladat van.   |
|A tároló   | Tárolók adja meg a Microsoft Azure Blob szolgáltatásban tárolt blobok logikai csoportosítását. Amikor egy blob feltöltése a Blob szolgáltatás, meg kell adnia, hogy a blob tárolója.   |
|Elérési út mintája   | A megadott tárolóban található blobok helyének azonosításához használt elérési utat. Az elérési útban kiválaszthatja a következő 2 változó egy vagy több példányát adhatja meg:<BR>a {date}, {time}<BR>1. példa: products/{date}/{time}/product-list.csv<BR>2. példa: products/{date}/product-list.csv   |
|[Választható] dátumformátum   | Ha a megadott elérési út mintája belül használt {date}, majd választhat, amelyben a blobok vannak rendszerezve dátumformátum a támogatott formátumok legördülő.<BR>Példa: Éééé/hh/nn-éééé/hh/nn, stb.   |
|[Választható] idő formátuma   | Ha a megadott elérési út mintája belül használt {time}, majd választhat az időformátum, amelyben a blobok vannak rendszerezve a támogatott formátumok legördülő.<BR>Például: HH ÓÓ/pp, vagy HH: mm-es.  |
|Esemény szerializálási formátum   | Annak érdekében, hogy a lekérdezések a várt módon működjenek, a Stream Analyticsnek tudnia kell, melyik szerializálási formátumot használja a bejövő adatfolyamokhoz. A Referenciaadatoknál, a támogatott formátumok a következők CSV és JSON-NÁ.  |
|Encoding   | Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.  |

## <a name="generating-reference-data-on-a-schedule"></a>Referenciaadatok ütemezés létrehozása
A lassan változó adatkészletet a referenciaadatok esetén majd adatok engedélyezve van a bemeneti konfigurációját a {date} segítségével egy elérési út mintája megadásával hivatkozás frissítése és támogatása {time} helyettesítés jogkivonatokat. A Stream Analytics szerzi be a frissített adatokat definíciókat az elérési út mintája alapján. Például egy mintát `sample/{date}/{time}/products.csv` a dátumformátummal **"Éééé-hh-nn"** és idő formátuma **"HH-mm"** arra utasítja a Stream Analytics a frissített blob átvételéhez `sample/2015-04-16/17-30/products.csv` 5:30-kor. április 16: , 2015 UTC időzóna.

> [!NOTE]
> Jelenleg Stream Analytics-feladatok keresse meg a blob frissítés csak akkor, ha a számítógép ideje kiadásokban a blob nevének kódolású idejével. Például a feladat keresi `sample/2015-04-16/17-30/products.csv` , amint lehetséges, de nincs korábbi mint 5:30-kor 2015. áprilisi 16 UTC idő zóna. A következőket hajtja végre *soha nem* keresse meg a korábban felderített legutóbb kódolt idővel blob.
> 
> Például Ha a feladat megtalálja a blob `sample/2015-04-16/17-30/products.csv` , figyelmen kívül hagyja az kódolt dátum korábbi, mint 5:30 PM 2015. áprilisi 16 fájlokat, ha a késői érkező `sample/2015-04-16/17-25/products.csv` blob jön létre ugyanabban a tárolóban a feladat nem fogja használni az.
> 
> Hasonlóképpen ha `sample/2015-04-16/17-30/products.csv` du. 10:03 2015. áprilisi 16 csak keletkezik, de nincs korábbi dátummal blob megtalálható-e a tároló, a feladat a kezdő pozíció: 10:03 PM 2015. áprilisi 16 fájlt használja, és előző hivatkozási adatokat arra használni, amíg ez nem sikerül.
> 
> Kivétel ez alól, ha a feladat újbóli feldolgozása adatokat időben, vagy ha a feladat első elindult. A feladat kezdési időpontban a hozott létre, mielőtt a feladat kezdési időpont van megadva a legutóbbi blob keresi. Erre azért van szükség, hogy van egy **nem üres** adatkészlet hivatkozik, a feladat indításakor. Ha az egyik nem található, a feladat megjelenítése a következő diagnosztikai: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Az Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) levezényelni a Stream Analytics hivatkozás adatok definíciók frissítéséhez szükséges frissített blobok létrehozásához használható. A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. Támogatja az adat-előállító [csatlakozik nagyszámú felhő alapú, és a helyszíni adattárolókhoz](../data-factory/copy-activity-overview.md) és egy rendszeres ütemterv alapján a könnyen megköveteli az adatok. További információk és részletes útmutatás a referenciaadatok létrehozására az a Stream Analytics, amelyek egy előre definiált ütemezés szerint frissíti a Data Factory-folyamathoz beállításáról, tekintse meg a [GitHub minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Tippek az hivatkozás adatok frissítése
1. Felülírja a hivatkozás a BLOB-adatobjektumok nem akadályozza meg a Stream Analytics töltse be újra a blob, és egyes esetekben okozhat a feladat sikertelen lesz. Az ajánlott módszer a referenciaadatok módosítása, hogy a feladat bemeneti definiált azonos tárolóhoz és annak elérési útja minta használatával új blob hozzáadhat és használhat egy dátum/idő **nagyobb** a sorban utolsó blob a megadottól.
2. A BLOB-adatobjektumok hivatkozás vannak **nem** a blob "Utolsó módosítás" idő szerint, de csak a blob megadott dátumát és időpontját segítségével a {date} és {time} helyettesítések szempontjából.
3. Kívánja kerülni a listáról a nagy számú blobot, fontolja meg, amelynek feldolgozása már nem kerül sor nagyon régi blobok törlése. Vegye figyelembe, hogy ASA mehet kell újból feldolgozza a kis méretű bizonyos esetekben, például a számítógép újraindítását.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors üzembe helyezés: A Stream Analytics-feladat létrehozása az Azure portál használatával](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
