---
title: Telemetriai adatok folyamatos exportálása az Application Insightsból | Microsoft dokumentumok
description: Diagnosztikai és használati adatokexportálása a Microsoft Azure-beli tárolóba, és onnan letöltheti azadatokat.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298285"
---
# <a name="export-telemetry-from-application-insights"></a>Telemetria exportálása az Application Insightsból
Szeretné megtartani a telemetriai adatok hosszabb ideig, mint a standard megőrzési időszak? Vagy feldolgozni valamilyen speciális módon? A folyamatos exportálás ideális erre. Az Application Insights portálon látható események json formátumban exportálhatók a Microsoft Azure-ban tárolt tárhelyre. Innen letöltheti az adatokat, és bármilyen kódot írhat a feldolgozáshoz.  

A folyamatos exportálás beállítása előtt érdemes figyelembe venni néhány alternatívát:

* A mérőszámok vagy a keresés lap tetején található Exportálás gombbal táblázatokat és diagramokat vihet át egy Excel-számolótáblába.

* [Az Analytics](../../azure-monitor/app/analytics.md) hatékony lekérdezési nyelvet biztosít a telemetriai adatokhoz. Az eredményeket is exportálhatja.
* Ha a Power [BI-ban szeretné megvizsgálni az adatokat,](../../azure-monitor/app/export-power-bi.md )ezt a Folyamatos exportálás használata nélkül is megteheti.
* Az [adat-hozzáférési REST API](https://dev.applicationinsights.io/) lehetővé teszi a telemetriai programokkal való hozzáférést.
* A telepítő folyamatos exportálása a [Powershellen keresztül](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)is elérhető.

Miután a Folyamatos exportálás átmásolja az adatokat a tárolóba (ahol addig maradhat, ameddig csak szeretne), továbbra is elérhető az Application Insightsban a szokásos [megőrzési időszakra.](../../azure-monitor/app/data-retention-privacy.md)

## <a name="continuous-export-advanced-storage-configuration"></a>Folyamatos exportálás speciális tárolási konfiguráció

A folyamatos exportálás **nem támogatja** a következő Azure-tárolási szolgáltatásokat/konfigurációkat:

* [VNET/Azure Storage-tűzfalak](https://docs.microsoft.com/azure/storage/common/storage-network-security) használata az Azure Blob storage-szal együtt.

* [Nem módosítható tárhely](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) az Azure Blob storage számára.

* [Az Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Folyamatos exportálás létrehozása

1. Az Alkalmazásinsights-erőforrásban a bal oldalon konfigurálás alatt nyissa meg a Folyamatos exportálás lehetőséget, és válassza a **Hozzáadás lehetőséget:**

2. Válassza ki az exportálni kívánt telemetriai adattípusokat.

3. Hozzon létre vagy válasszon ki egy [Azure-tárfiókot,](../../storage/common/storage-introduction.md) ahol az adatokat tárolni szeretné. A tárolási díjszabási lehetőségekkel kapcsolatos további információkért látogasson el a [hivatalos árképzési oldalra.](https://azure.microsoft.com/pricing/details/storage/)

     Kattintson a Hozzáadás, Cél exportálása, Tárfiók elemre, majd hozzon létre egy új tárolót, vagy válasszon egy meglévő tacélt.

    > [!Warning]
    > Alapértelmezés szerint a tárolási hely lesz beállítva, hogy ugyanazt a földrajzi régiót, mint az Application Insights-erőforrás. Ha egy másik régióban tárol, átmozgatási díjak merülhetnek fel.

4. Hozzon létre vagy jelöljön ki egy tárolót a tárolóban.

Miután létrehozta az exportálást, elindul. Csak az exportálás létrehozása után érkező adatokat kapja meg.

Körülbelül egy órával az adatok megkerülése a tárolóban késleltethető lehet.

Miután az első exportálás befejeződött, az Azure Blob-tárolóban a következőhöz hasonló struktúrát talál: (Ez a gyűjtött adatoktól függően változik.)

|Név | Leírás |
|:----|:------|
| [Rendelkezésre állás](export-data-model.md#availability) | A [rendelkezésre állási webes tesztek ről](../../azure-monitor/app/monitor-web-app-availability.md)számol be.  |
| [Esemény](export-data-model.md#events) | A [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)által létrehozott egyéni események. 
| [Kivételek](export-data-model.md#exceptions) |[Kivételeket](../../azure-monitor/app/asp-net-exceptions.md) jelent a kiszolgálón és a böngészőben.
| [Üzenetek](export-data-model.md#trace-messages) | A [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)és a [naplózási adapterek](../../azure-monitor/app/asp-net-trace-logs.md)által küldött .
| [Mutatókat](export-data-model.md#metrics) | Metrika API-hívások által generált.
| [Teljesítményszámlálók](export-data-model.md) | Az Application Insights által gyűjtött teljesítményszámlálók.
| [Kérelmek](export-data-model.md#requests)| Által küldött [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). A szabványos modulok ezt használják a kiszolgáló natérben mért válaszidejének jelentésére.| 

### <a name="to-edit-continuous-export"></a>Folyamatos exportálás szerkesztése

Kattintson a folyamatos exportálásra, és válassza ki a szerkesztni kívánt tárfiókot.

### <a name="to-stop-continuous-export"></a>A folyamatos exportálás leállítása

Az exportálás leállításához kattintson a Letiltás gombra. Ha ismét az Engedélyezés gombra kattint, az exportálás új adatokkal újraindul. Nem kapja meg a portálra érkező adatokat, amíg az exportálás le van tiltva.

Az exportálás végleges leállításához törölje azt. Ezzel nem törli az adatokat a tárolóból.

### <a name="cant-add-or-change-an-export"></a>Nem lehet exportot hozzáadni vagy módosítani?
* Az exportálás hozzáadásához vagy módosításához tulajdonosi, közreműködői vagy Application Insights közreműködői hozzáférési jogokra van szükség. [További információ a szerepkörökről.][roles]

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Milyen eseményeket kapsz?
Az exportált adatok az alkalmazástól kapott nyers telemetriai adatok, azzal a különbséggel, hogy helyadatokat adunk hozzá, amelyeket az ügyfél IP-címéből számítunk ki.

A [mintavétellel](../../azure-monitor/app/sampling.md) elvetett adatok nem szerepelnek az exportált adatokban.

Más számított metrikák nem szerepelnek. Például nem exportáljuk az átlagos CPU-kihasználtságot, de exportáljuk a nyers telemetriai adatokat, amelyekből az átlag ot számítják ki.

Az adatok a beállított [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) eredményeit is tartalmazzák.

> [!NOTE]
> **Mintavételi.** Ha az alkalmazás sok adatot küld, a mintavételi funkció működhet, és a létrehozott telemetriai adatoknak csak egy töredékét küldheti el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Az adatok vizsgálata
A tárolót közvetlenül a portálon ellenőrizheti. Kattintson haza a bal szélső menüben, a tetején, ahol azt mondja: "Azure-szolgáltatások" válassza **storage-fiókok**, válassza ki a tárfiók nevét, az áttekintő lapon válassza **blobok** a szolgáltatások alatt, és végül válassza ki a tároló nevét.

Az Azure-tárhely Visual Studio-ban való vizsgálatához nyissa meg a **Nézet**, **Felhőkezelő ablakot.** (Ha nem rendelkezik ezzel a menüparancával, telepítenie kell az Azure SDK:Nyissa meg az **Új projekt** párbeszédpanelt, bontsa ki a Visual C#/Cloud csomópontot, és válassza a Microsoft **Azure SDK beszereznie a .NET-hez**lehetőséget.)

Amikor megnyitja a blob store, megjelenik egy tároló blob fájlok készletével. Az egyes fájlok URI-ja az Application Insights-erőforrás nevéből, a instrumentation kulcsából, a telemetriai típusból/dátumból/időből származik. (Az erőforrás neve kisbetűs, és a műszerezési kulcs kihagyja a kötőjeleket.)

![A blob áruház ellenőrzése megfelelő eszközzel](./media/export-telemetry/04-data.png)

A dátum és az idő UTC, és amikor a telemetriai adatok letétbe kerültek az üzletben - nem az idő jött létre. Tehát, ha írsz kódot letölteni az adatokat, akkor lineárisan mozoghat az adatokközött.

Itt van a formája az út:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Ahol

* `blobCreationTimeUtc`az az időpont, amikor a blob a belső átmeneti tárolóban jött létre
* `blobDeliveryTimeUtc`Az az időpont, amikor a blob ot az exportcél tárolóba másolja

## <a name="data-format"></a><a name="format"></a>Adatformátum
* Minden blob egy szövegfájl, amely több '\n'-elválasztott sort tartalmaz. Ez tartalmazza a telemetriai feldolgozásnagyjából fél perc alatt feldolgozott.
* Minden sor egy telemetriai adatpontot, például egy kérést vagy oldalnézetet jelöl.
* Minden sor egy formázatlan JSON-dokumentum. Ha ülni és bámulni szeretné, nyissa meg a Visual Studióban, és válassza a Szerkesztés, Speciális, Formátumfájl parancsot:

![A telemetria megtekintése megfelelő eszközzel](./media/export-telemetry/06-json.png)

Az időtartamok kullancsokban vannak, ahol 10 000 kullancs = 1 ms. Ezek az értékek például 1 ezres időt mutatnak a böngészőtől érkező kérelem küldésére, 3 ezs a fogadásra, és 1,8 s az oldal böngészőben történő feldolgozására:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Részletes adatmodell-hivatkozás a tulajdonságtípusokra és értékekre vonatkozóan.](export-data-model.md)

## <a name="processing-the-data"></a>Az adatok feldolgozása
Kis léptékben írhat valamilyen kódot, hogy szétszedje az adatokat, beolvassa őket egy táblázatba, és így tovább. Példa:

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

A nagyobb kódmintát lásd: [feldolgozói szerepkör használata.][exportasa]

## <a name="delete-your-old-data"></a><a name="delete"></a>Régi adatok törlése
Ön felelős a tárolókapacitás kezeléséért és szükség esetén a régi adatok törléséért.

## <a name="if-you-regenerate-your-storage-key"></a>Ha újragenerálja a tárolókulcsot...
Ha módosítja a tároló kulcsát, a folyamatos exportálás leáll. Megjelenik egy értesítés az Azure-fiókjában.

Nyissa meg a Folyamatos exportálás lapot, és szerkesztse az exportálást. Az exportálási cél szerkesztése, de hagyja ugyanazt a tárhelyet kiválasztva. A megerősítéshez kattintson az OK gombra.

A folyamatos exportálás újraindul.

## <a name="export-samples"></a>Exportminták

* [SQL-exportálás a Stream Analytics használatával][exportasa]
* [Stream Analytics minta 2](export-stream-analytics.md)

Nagyobb léptékben fontolja meg a [HDInsight](https://azure.microsoft.com/services/hdinsight/) – Hadoop-fürtöket a felhőben. A HDInsight számos technológiát kínál a big data kezeléséhez és elemzéséhez, és használhatja az Application Insightsból exportált adatok feldolgozására.

## <a name="q--a"></a>Kérdések és válaszok
* *De én csak egy egyszeri letöltést akarok a kórlapról.*  

    Igen, meg tudod csinálni. A lap tetején kattintson az **Adatok exportálása gombra.**
* *Beállítottam egy exportálást, de nincs adat a boltomban.*

    Az Alkalmazáselemzési adatok kapott telemetriai adatokat az alkalmazásból az exportálás beállítása óta? Csak új adatokat fog kapni.
* *Megpróbáltam beállítani egy exportálást, de megtagadták a hozzáférést*

    Ha a fiók a szervezet tulajdonában van, akkor a tulajdonosok vagy közreműködők csoportjának tagjának kell lennie.
* *Exportálhatok egyenesen a saját helyszíni üzletembe?*

    Nem, sajnálom. Az exportmotor jelenleg csak az Azure storage jelenleg működik.  
* *Korlátozva van az üzletemben megadott adatok mennyisége?*

    Nem. Addig folytatjuk az adatok lenyomását, amíg nem törli az exportálást. Megállunk, ha elérjük a blob-tárolás külső határait, de ez elég nagy. Ez rajtad múlik, hogy ellenőrizzék, hogy mennyi tárhelyet használ.  
* *Hány blobot kell látni a tárolóban?*

  * Minden exportálandó adattípushoz percenként új blob jön létre (ha rendelkezésre állnak adatok).
  * Emellett a nagy forgalmú alkalmazások esetében további partícióegységek vannak lefoglalva. Ebben az esetben minden egység percenként létrehoz egy blobot.
* *Újrageneráltam a tároló kulcsát, vagy megváltoztattam a tároló nevét, és most az exportálás nem működik.*

    Az exportálás szerkesztése és az exportcél lap megnyitása. Az exportálás újraindul. Ha a változás az elmúlt napokban történt, nem fog adatokat veszíteni.
* *Szüneteltethetem az exportálást?*

    Igen. Kattintson a Letiltás lehetőségre.

## <a name="code-samples"></a>Kódminták

* [Stream Analytics-minta](export-stream-analytics.md)
* [SQL-exportálás a Stream Analytics használatával][exportasa]
* [Részletes adatmodell-hivatkozás a tulajdonságtípusokra és értékekre vonatkozóan.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
