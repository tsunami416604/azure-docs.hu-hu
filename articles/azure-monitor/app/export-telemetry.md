---
title: A folyamatos Exportálás az Application Insights telemetria |} A Microsoft Docs
description: Diagnosztikai és használati adatok exportálása a Microsoft Azure Storage, és töltse le innen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: c6323a4b8243791cc7a9f847c7d08e5bd5946e96
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025266"
---
# <a name="export-telemetry-from-application-insights"></a>Application insights telemetria exportálása
Szeretné megtartani a telemetriát azokhoz szabványos megőrzési idejénél hosszabb? Vagy specializált módon dolgozza fel? A folyamatos exportálás ideális ehhez. Az eseményeket az Application Insights portálon látható a JSON-formátumban a Microsoft Azure storage exportálhatók. Innen letöltheti az adatokat és bármilyen kódot írni kell őket feldolgoznia.  

A folyamatos exportálás beállítása előtt van néhány más érdemes figyelembe venni:

* Metrikák vagy Keresés a panel tetején az Exportálás gomb lehetővé teszi a táblákat át, és a egy Excel-táblázatban a diagram.

* [Analytics](../../azure-monitor/app/analytics.md) egy hatékony lekérdezési nyelvet biztosít a telemetriai adatokat. Exportálhatja eredményeket is.
* Ha szeretne [feltárhatja az adatait a Power bi-ban](../../application-insights/app-insights-export-power-bi.md), azt megteheti a folyamatos exportálás használata nélkül.
* A [adatelérési REST API](https://dev.applicationinsights.io/) a telemetriai adatok programozott módon hozzáférhet.
* A telepítő is elérhető [Powershellen keresztül a folyamatos exportálás](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

Miután az adatok folyamatos exportálása másolja át a Storage (amennyiben azt is marad mindaddig, amíg Ön, mint), érhető el továbbra is az Application Insightsban a szokásos módon [megőrzési időszak](../../azure-monitor/app/data-retention-privacy.md).

## <a name="setup"></a> Hozzon létre egy folyamatos exportálás
1. Az Application Insights-erőforrást az alkalmazáshoz, nyissa meg a folyamatos exportálást, és válassza **Hozzáadás**:

2. Válassza ki az exportálni kívánt adattípusok a telemetriát.

3. Hozzon létre vagy válasszon egy [Azure storage-fiók](../../storage/common/storage-introduction.md) hol szeretné tárolni az adatokat. További információk a storage díjszabása a [díjszabását ismertető lapon hivatalos](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Alapértelmezés szerint a tárolási hely állítja be az Application Insights-erőforrás és ugyanabban a földrajzi régióban. Ha egy másik régióban tárolja, adatforgalmi díjat számítanak fel.

    ![Kattintson a Hozzáadás, exportálás cél tárfiók, és ezután létrehoz egy új vagy egy meglévő tároló kiválasztása](./media/export-telemetry/02-add.png)

4. Hozzon létre, vagy kiválaszthatja a tárolót a storage-ban:

    ![Kattintson a Kiválasztás eseménytípusok](./media/export-telemetry/create-container.png)

Ha létrehozta az exportálás, elindítja a történik. Csak kap az Exportálás létrehozása után érkező adatokat.

Kapcsolatos adatok jelenjenek meg a storage-ban egy órás késés is lehet.

### <a name="to-edit-continuous-export"></a>A folyamatos exportálás szerkesztése

Ha meg szeretné változtatni az eseménytípusok később, az Exportálás szerkesztésével:

![Kattintson a Kiválasztás eseménytípusok](./media/export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>A folyamatos Exportálás leállítása

Az Exportálás leállításához kattintson letiltása. Engedélyezze ismét kattintunk, az Exportálás újraindul az új adatokkal. Az adatokat, amíg az Exportálás le lett tiltva a portálon érkező nem jelenik meg.

Az Exportálás véglegesen megszüntetéséhez törölje azt. Így nem, törölje a adatait storage-ból.

### <a name="cant-add-or-change-an-export"></a>Nem adhat hozzá, és módosítsa az export?
* Adja hozzá, vagy módosítsa az export, tulajdonos, közreműködő vagy Application Insights-közreműködői jogokkal kell. [További információ a szerepkörökről][roles].

## <a name="analyze"></a> Milyen eseményeket be?
Az exportált adatok a az alkalmazásból érkező nyers telemetriát, azzal a különbséggel, hogy a helyadatok, amelyek kiszámítása adjuk hozzá az ügyfél IP-címről.

Adatok, amelyek szerint el lett vetve [mintavételi](../../azure-monitor/app/sampling.md) nem szerepel az exportált adatok.

Egyéb számított metrika nem szerepelnek. Például nem nem exportálni átlagos CPU-kihasználtság, de nem exportálni a nyers telemetriát, amelyről átlagát számítja ki.

Az adatokat is tartalmaz az eredmények bármely [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) , amely meg van adva.

> [!NOTE]
> **Mintavétel.** Ha az alkalmazás nagy mennyiségű adatot küld, a mintavételezés funkció működjön, és csak töredéke alatt létrehozott telemetriát küldjön. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Az adatok vizsgálata
A storage közvetlenül a portálon ellenőrizheti. Kattintson a **Tallózás**, válassza ki a tárfiókját, és nyissa meg **tárolók**.

Vizsgálja meg az Azure storage a Visual Studióban, nyissa meg a **nézet**, **Cloud Explorer**. (Ha a parancs nem rendelkezik, az Azure SDK-t telepítenie kell: Nyissa meg a **új projekt** párbeszédpanelen bontsa ki a Vizualizáció C#/Cloud, majd **a Microsoft Azure SDK for .NET**.)

A blobtár megnyitásakor látni fogja a blob-fájlokat tároló. Az URI-t az egyes fájlok származik, az Application Insights-erőforrás neve, a kialakítási kulcsot, a telemetria-típus és dátum/idő. (Az erőforrás neve kisbetűkkel, és kihagyja a kialakítási kulcsot a kötőjelek szerepelhetnek.)

![Vizsgálja meg a blob-tároló megfelelő eszközzel](./media/export-telemetry/04-data.png)

A dátum és idő (UTC) és amikor a telemetria - tárolójában volt letétbe nem hozta létre idejét. Ezért ír kódot, hogy az adatok letöltése, ha azt is lineárisan haladjon át az adatokat.

A következő formájában az elérési út:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Ahol

* `blobCreationTimeUtc` a blob a belső létrehozásának ideje átmeneti tárolási
* `blobDeliveryTimeUtc` az az idő, amikor blob lett másolva a Exportálás célhelye

## <a name="format"></a> Adatformátum
* Minden egyes blob egy több tartalmazó szövegfájlt "\n'-separated sorok. A telemetriai adatok feldolgozása körülbelül felét egy perces időszakra tartalmazza.
* Minden sor egy kérelem vagy oldalmegtekintés nézet például telemetriai adatok pontot jelöli.
* Minden egyes sor egy nem formázott JSON-dokumentumot. Ha található, és látta stare, nyissa meg a Visual Studio, és válassza a Szerkesztés, speciális, formátumfájl:

![A megfelelő eszköz telemetriai adatok megtekintése](./media/export-telemetry/06-json.png)

Idő időtartamok vannak órajel során végbemenő, ha 10 000-re ölések = 1 ms. Például jeleníti meg ezeket az értékeket az időszak 1 ms-kérést küldhet a böngészőből, 3 ms és az 1.8-as s feldolgozni a lap a böngészőben:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Részletes adatmodell-referencia a tulajdonság típusát és értékét.](export-data-model.md)

## <a name="processing-the-data"></a>Az adatok feldolgozása
Egy kis léptékű a írhat némi kódot vannak az adatok beolvasását, olvassa el, egy számolótáblába és így tovább. Példa:

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

Egy nagyobb kódmintát talál [feldolgozói szerepkör használatával][exportasa].

## <a name="delete"></a>A régi adatok törlése
Vegye figyelembe, hogy Ön felelős a tárolási kapacitás kezelése és a régi adatok törlése, ha szükséges.

## <a name="if-you-regenerate-your-storage-key"></a>Ha Ön a kulcs újragenerálása...
Ha módosítja a kulcsot a storage, a folyamatos exportálás fognak tovább működni. Az Azure-fiók megjelenik egy értesítés.

A folyamatos exportálás panel megnyitásához, és az exportálás. Az Exportálás célhelye szerkesztheti, de ne változtassa meg a kiválasztott ugyanazt a tárhelyet. Kattintson az OK gombra.

![Nyílt forráskódú, és zárja be a folyamatos exportálás szerkesztése ezekkel exportálás célhelye.](./media/export-telemetry/07-resetstore.png)

A folyamatos exportálás újra fog indulni.

## <a name="export-samples"></a>A minták exportálása

* [Stream Analytics használata SQL-exportálás][exportasa]
* [Stream Analytics minta 2](export-stream-analytics.md)

A nagyobb méretek, érdemes lehet [HDInsight](https://azure.microsoft.com/services/hdinsight/) – Hadoop-fürtök a felhőben. HDInsight biztosít számos különböző technológiával kezelése és big Data típusú adatok elemzésére szolgáló, és használhatja az Application Insights exportált adatok feldolgozásához.

## <a name="q--a"></a>Kérdések és válaszok
* *Azonban az összes kívánt diagram egyszeri letöltési.*  

    Igen, akkor teheti meg. Kattintson a panel tetején **adatok exportálása**.
* *Az Exportálás beállítása, de nem szerepel megjeleníthető adat a saját tárolóban.*

    Az Application Insights kapott semmilyen telemetriai adatot az alkalmazásból, mert az Exportálás beállítása? Csak kap új adatokat.
* *E-exportálás beállítása próbált, de a rendszer megtagadta a hozzáférést*

    Ha a fiók a szervezete tulajdonában van, akkor a tulajdonosai vagy közreműködői csoport tagjának lennie.
* *E exportálhatja egyenesen a saját helyszíni adattár?*

    Nem, Sajnáljuk. Az exportálási motor jelenleg csak együttműködik az Azure storage jelenleg.  
* *Van-e a saját tárolóban helyezi adatok mennyisége korlátozott?*

    Nem. Hogy fog tartani küld adatokat, amíg nem törli az exportálás. Azt fogjuk le, ha azt nyomja le a külső korlátok a blob Storage, de ez elég nagy. Az szabályozható, hogy mennyi tárhelyet használ van.  
* *Hány blobok kell lásd a storage-ban?*

  * Minden adattípus az exportálandó kiválasztott egy új blob jön létre percenként (ha elérhető adatok).
  * Nagy forgalmú alkalmazásokhoz, emellett további partíció egység van lefoglalva. Ebben az esetben minden egység egy blobot hoz létre percenként.
* *Használom a tárolás, a kulcs újragenerálása, vagy a tároló neve megváltozott, és most az exportálás nem működik.*

    Az exportálás és az Exportálás célhelye panel megnyitásához. Hagyja kiválasztva, mint korábban ugyanazt a tárhelyet, és kattintson az OK gombra. Exportálás újra fog indulni. Ha a módosítás az elmúlt néhány napon belül volt, adatok nem vesznek el.
* *Szüneteltetheti az Exportálás?*

    Igen. Kattintson a Letiltás elemre.

## <a name="code-samples"></a>Kódminták

* [Stream Analytics-minta](export-stream-analytics.md)
* [Stream Analytics használata SQL-exportálás][exportasa]
* [Részletes adatmodell-referencia a tulajdonság típusát és értékét.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
