---
title: "Az Application Insights telemetria a folyamatos exportálás |} Microsoft Docs"
description: "Diagnosztikai és használati adatok exportálása Microsoft Azure-tárhelyre, és töltse le innen."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: bwren
ms.openlocfilehash: 6ac3bda5101593b5ca66b4c9035e2fdac9d1e833
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="export-telemetry-from-application-insights"></a>Az Application Insights telemetria exportálása
Szeretné megőrizni a telemetriai adat hosszabb, mint a szokásos megőrzési időszakot? Vagy valamilyen speciális módon dolgozza fel? A folyamatos exportálás ideális ehhez. Az eseményeket az Application Insights portáljáról látható JSON formátumban a Microsoft Azure storage exportálhatja. Ott töltse le az adatok és bármit kódját, írási kell feldolgozni azt.  

Használja a folyamatos exportálás járhat egy kell külön fizetni. Ellenőrizze a [fizetési modell](http://azure.microsoft.com/pricing/details/application-insights/).

A folyamatos exportálás beállítása előtt van néhány más-érdemes figyelembe venni:

* A metrikák vagy keresési panel tetején az Exportálás gomb lehetővé teszi a táblák átviteli, és Excel-táblázatba diagramokat.

* [Elemzés](app-insights-analytics.md) hatékony lekérdezésnyelvet biztosít a telemetriai adatokat. Exportálhatja eredmények.
* Ha a keresett [az adatokba a Power BI](app-insights-export-power-bi.md), azt teheti a folyamatos exportálás használata nélkül.
* A [adatelérési REST API](https://dev.applicationinsights.io/) programozott hozzáférést a telemetriai adatok.

A folyamatos exportálás nem másolta az adatok (Ha azt is marad mindaddig, amíg tetszés) tárhelyre, továbbra is rendelkezésre áll az Application Insightsban a szokásos [megőrzési időszak](app-insights-data-retention-privacy.md).

## <a name="setup"></a>A folyamatos exportálás létrehozása
1. Az Application Insights-erőforrást az alkalmazáshoz, nyissa meg a folyamatos exportálás, és válassza a **Hozzáadás**:

    ![Görgessen le, majd kattintson a folyamatos exportálás](./media/app-insights-export-telemetry/01-export.png)

2. Válassza ki az exportálni kívánt adattípusok telemetriai adatok.

3. Hozzon létre vagy válasszon egy [Azure storage-fiók](../storage/common/storage-introduction.md) hol szeretné tárolni az adatokat.

    > [!Warning]
    > Alapértelmezés szerint a tárolóhely állítja be az Application Insights-erőforrás és ugyanabban a földrajzi régióban. Ha egy másik régióban vannak tárolva, az átviteli további költségekkel járhat.

    ![Kattintson a Hozzáadás gombra, exportálás cél tárfiók, és majd létrehoz egy új vagy egy meglévő tároló kiválasztása](./media/app-insights-export-telemetry/02-add.png)

4. Hozzon létre vagy jelöljön ki egy tárolót a tárolóban:

    ![Válasszon eseménytípust kattintson](./media/app-insights-export-telemetry/create-container.png)

Ha létrehozta az exportálás, fog kezdődik. Miután létrehozta az Exportálás érkező adatok csak kap.

Körülbelül egy óra alatt jelenik meg adat tárolására előtt várnia lehet.

### <a name="to-edit-continuous-export"></a>A folyamatos exportálás szerkesztése

Ha módosítani szeretné a eseménytípusok később, az Exportálás módosításával:

![Válasszon eseménytípust kattintson](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>A folyamatos Exportálás leállítása

Az Exportálás leállításához kattintson a letiltása. Engedélyezze újra kattint, az Exportálás újraindul az új adatokat. Az Exportálás le lett tiltva, amíg a portálon érkező adatokat nem jelenik meg.

Véglegesen állítsa le az exportálást, törölje azt. Így nem törlése az adatok tárolására.

### <a name="cant-add-or-change-an-export"></a>Nem adható hozzá, vagy módosítsa az export?
* Adja hozzá, vagy módosítsa a kivitel, tulajdonos, közreműködő vagy Application Insights közreműködői jogokkal kell. [További tudnivalók a szerepkörök][roles].

## <a name="analyze"></a>Milyen eseményeket kapott?
Az exportált adatok a nyers telemetriaadatok az alkalmazásból érkező, azzal a különbséggel, hogy az ügyfél IP-címről érkező helyadatok, amelyek Microsoft jelenleg felvenni.

Adatok, amelyek szerint el lett vetve [mintavételi](app-insights-sampling.md) nem szerepel az exportált adatokat.

Más számított metrikák nem érhetők el. Például azt átlagos CPU-felhasználása ne exportálja, de a nyers telemetriaadatok, amelyből az átlagos számított exportálása.

Az adatokat is tartalmaz, az eredmények közül [webteszt rendelkezésre állási](app-insights-monitor-web-app-availability.md) , amely meg van adva.

> [!NOTE]
> **Mintavételi.** Az alkalmazás nagy mennyiségű adatot küld, ha a mintavételi szolgáltatás működik, és csak töredéke alatt a generált telemetriai adatot küldeni. [További tudnivalók a mintavételezésről.](app-insights-sampling.md)
>
>

## <a name="get"></a>Vizsgálja meg az adatok
A tárolási közvetlenül a portálon vizsgálhatja meg. Kattintson a **Tallózás**, válassza ki a tárfiók, és nyisson **tárolók**.

Vizsgálja meg az Azure storage a Visual Studio, nyissa meg a **nézet**, **Cloud Explorer**. (Ha még nem rendelkezik az adott parancs, szeretné-e az Azure SDK telepítése: Nyissa meg a **új projekt** párbeszédpanelen bontsa ki a Visual C# / felhőalapú, és válassza **Microsoft Azure SDK for .NET**.)

A blob-tároló megnyitásakor látni fogja a blob-fájlokat a tárolóban. Az egyes fájlok származtatva a Application Insights-erőforrás nevét, a instrumentation kulcs, a telemetria-típus vagy dátum/idő URI. (Az erőforrás neve kisbetű szerepelhet, és a instrumentation kulcs kihagyja a kötőjel.)

![Vizsgálja meg a megfelelő eszköz blob tárolóba](./media/app-insights-export-telemetry/04-data.png)

A dátum és idő UTC és amikor a telemetria - tárolójában lett letétbe nem hozta létre idejét. Így ha az adatok letöltése kódot ír, áthelyezheti azt lineárisan keresztül az adatok.

Az elérési utat a következő:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Ha

* `blobCreationTimeUtc`van a belső blob létrehozásának időpontja átmeneti tárolási
* `blobDeliveryTimeUtc`az az idő, amikor blob lett másolva a exportálási cél

## <a name="format"></a>Az adatformátum
* Minden egyes blob egy szövegfájlt, amely több tartalmazza az "\n'-separated sorokat. A telemetriai adatok feldolgozása körülbelül fél perc időszakra tartalmaz.
* Minden egyes sorára például egy kérelem vagy a lap nézet telemetriai adatpont jelöli.
* Minden egyes sorára nem formázott JSON-dokumentumhoz. Elhelyezkedik, és azt a stare, nyissa meg a Visual Studio és válassza a Szerkesztés, speciális formátumú fájlba:

![A megfelelő eszköz telemetriai adatok megtekintése](./media/app-insights-export-telemetry/06-json.png)

Idő időtartamok vannak ütés, ahol a 10 000-re ölések = 1 MS. Ezek az értékek például a kérést küldeni a böngészőből, 3ms azt, és a lap a böngészőben feldolgozni 1.8s 1 MS idő megjelenítése:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[A részletes adatok modell útmutató a tulajdonság típusát és értékét.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Az adatok feldolgozása
Egy kis méretű írhat néhány kódot egymástól az adatok lekérésére, olvassa el, egy számolótáblába, és így tovább. Példa:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

A nagyobb kódminta, lásd: [feldolgozói szerepkörök használatával][exportasa].

## <a name="delete"></a>A régi adatok törlése
Vegye figyelembe, hogy Ön felelősséggel tartozik a tárolási kapacitás kezelése és a régi adatok törlése, ha szükséges.

## <a name="if-you-regenerate-your-storage-key"></a>Ha a tárolási kulcs újragenerálása...
Ha módosítja a kulcsot a tároló, a folyamatos exportálás nem fog tovább működni. Az Azure-fiókjába, megjelenik egy értesítés.

A folyamatos exportálás panel megnyitásához, és az Exportálás szerkesztése. Az Exportálás cél szerkesztése, de ne változtassa meg a kiválasztott ugyanazt a tárhelyet. Kattintson az OK gombra a megerősítéshez.

![Szerkesztés a folyamatos exportálás, és ezekkel a cél exportálása.](./media/app-insights-export-telemetry/07-resetstore.png)

A folyamatos exportálás újraindul.

## <a name="export-samples"></a>Exportálás minták

* [A Stream Analytics használ SQL exportálása][exportasa]
* [Stream Analytics minta 2](app-insights-export-stream-analytics.md)

Nagyobb skálán, érdemes lehet [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-fürtök a felhőben. HDInsight számos technológiák kezelése, és a big Data típusú adatok elemzésére, és használhat fel az Application Insights exportált adatokat.

## <a name="q--a"></a>Kérdések és válaszok
* *Azonban az összes kívánt egyszeri letölteni egy diagram.*  

    Igen, akkor teheti meg. Kattintson a panel tetején **adatok exportálása**.
* *Exportálás beállítva, de nincs adat a saját tárolóban.*

    Az Application Insights kapta meg összes telemetriai adat az alkalmazás óta állít be az Exportálás? Csak kap új adatokat.
* *I exportálás beállítása próbált, de a rendszer megtagadta a hozzáférést*

    Ha a fiók a szervezete tulajdonában van, akkor a tulajdonos vagy közreműködő szerepkörrel rendelkező személyek csoportok tagjai.
* *Is exportálásakor rögtön saját helyszíni tárolási?*

    Nem, sajnos. Az Exportálás motor jelenleg csak akkor működik az Azure storage most.  
* *A saját tárolóban lévő adatok bármely korlátozva van?*

    Nem. Azt fogja tartsa kérdez le adatokat exportálás törléséig. Ha azt a külső korlátozhatja a blob-tároló találati, de ez közérthető hatalmas lesz leállítása. Ez legfeljebb lesz vezérlését mennyi tárhelyre, használja.  
* *Hány blobok kell lásd a tárolóban lévő?*

  * Minden egyes exportálandó kiválasztott adattípus használatához új blob jön létre percenként (ha érhetők el adatok).
  * Emellett nagy forgalmú alkalmazások esetén további partíció egységek foglal le. Ebben az esetben tárolóegységekhez hoz létre egy blobot percenként.
* *Lehet, a kulcs újragenerálása a tárhely, vagy a tároló neve megváltozott, és az exportálás nem működik.*

    Az Exportálás módosításával, és nyissa meg az Exportálás cél panelt. Hagyja a választott azt korábban ugyanazt a tárhelyet, és kattintson az OK gombra a megerősítéshez. Exportálás újraindul. Ha módosította az elmúlt néhány napon belül, adatok nem vesznek el.
* *Az exportálás is szüneteltetése?*

    Igen. Kattintson a letiltása.

## <a name="code-samples"></a>Kódminták

* [Stream Analytics-minta](app-insights-export-stream-analytics.md)
* [A Stream Analytics használ SQL exportálása][exportasa]
* [A részletes adatok modell útmutató a tulajdonság típusát és értékét.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
