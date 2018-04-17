---
title: A Power BI Embedded áttelepítési eszközzel |} Microsoft Docs
description: A Power BI Embedded áttelepítési eszköz segítségével a jelentések másolása a Power BI munkaterület gyűjtemények a Power BI Embedded.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 4f76b1efb509745653bfde0926f56032030f7d47
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>A Power BI Embedded áttelepítési eszköz használata

A Power BI Embedded áttelepítési eszköz segítségével a jelentések másolása a Power BI munkaterület gyűjtemények a Power BI Embedded.

A munkaterület-gyűjtemények a tartalmat telepít át a Power BI szolgáltatás az aktuális megoldás párhuzamosan végezhető, és nem igényel leállási.

## <a name="limitations"></a>Korlátozások

* Megnyomott adatkészletek nem lehet letölteni, és újból létre kell hozni a Power BI REST API-k használata a Power BI szolgáltatáshoz kell.
* PBIX-fájlt importálva, mielőtt a 2016. November 26. nem lesz letölthető.

## <a name="download"></a>Letöltés

Letöltheti az áttelepítési eszköz mintát [GitHub](https://github.com/Microsoft/powerbi-migration-sample). A tárház zip vagy letöltheti, vagy helyileg tud klónozni. Miután letöltötte, megnyithatja *Power bi-áttelepítés – sample.sln* össze és futtathatja az áttelepítési eszköz Visual studióban.

## <a name="migration-plans"></a>Áttelepítési tervek

Az áttelepítési terv, amely a tartalom a Power BI munkaterület gyűjtemények és a Power BI Embedded közzététele módját katalogizálása csak metaadatokat.

### <a name="start-with-a-new-migration-plan"></a>Indítsa el az új áttelepítési terv

Áttelepítési terv a Power BI munkaterület kívánt gyűjteményeket majd a Power BI Embedded átvitele a rendelkezésre álló elemek metaadatokat. Az áttelepítési terv XML-fájlként tárolódik.

Először hozzon létre egy új áttelepítési tervet érdemes. Hozzon létre egy új áttelepítési tervet, tegye a következőket.

1. Válassza ki **fájl** > **új áttelepítési terv**.

    ![Új áttelepítési terve](media/migrate-tool/migrate-tool-plan.png)

2. Az a **válassza ki a Power BI munkaterület gyűjtemények erőforráscsoport** párbeszédpanel, érdemes válassza ki a **környezet** legördülő menüből, és válassza a termék.

3. Kérni fogja a bejelentkezéshez. Azure-előfizetés bejelentkezési adatait fogja használni.

    > [!IMPORTANT]
    > Ez az **nem** az Office 365 a Power BI-bA szervezeti fiókjával.

4. Válassza ki az Azure-előfizetés, amely tárolja a Power BI munkaterület gyűjtemények erőforrás.

    ![Válassza ki az Azure-előfizetéshez](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Az előfizetés listája alatt válassza ki a **erőforráscsoport** , amely tartalmazza a munkaterület-gyűjteményeket, és válassza **válasszon**.

    ![Az erőforráscsoport kiválasztása](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Válassza ki **elemzése**. Így megkapja a cikkek az Azure-előfizetéshez, hogy a csomag kezdő leltár.

    ![Elemzés gomb kiválasztása](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Az elemzés folyamat munkaterület gyűjtemények és mennyi tartalom megtalálható a munkaterület-csoportok számától függően néhány percig eltarthat.

7. Ha **elemzés** van kész, akkor arra fogja kérni az áttelepítési terv mentését.

Ezen a ponton az áttelepítési terv csatlakozott az Azure-előfizetéshez. Olvassa el az alábbi használata az áttelepítési terv áramló megértése. Ez magában foglalja az elemzés & áttelepítés megtervezése, letöltés, csoportok létrehozása és feltöltése.

### <a name="save-your-migration-plan"></a>Az áttelepítési terv mentése

Az áttelepítési terv későbbi használatra takaríthat meg. Ezzel létrehoz egy XML-fájlt az áttelepítési terv lévő összes adatot.

Ha az áttelepítési terv, tegye a következőket.

1. Válassza ki **fájl** > **áttelepítési terv mentése**.

    ![Mentse az áttelepítési terv menüpont](media/migrate-tool/migrate-tool-save-plan.png)

2. Nevezze el a fájlt, vagy használja a létrehozott fájl nevét, és válasszon **mentése**.

### <a name="open-an-existing-migration-plan"></a>Nyisson meg egy meglévő áttelepítési terv

Az áttelepítés használatának folytatására mentett áttelepítési terv nyithatja meg.

Nyissa meg a meglévő áttelepítési terv, tegye a következőket.

1. Válassza ki **fájl** > **nyissa meg a meglévő áttelepítési terv**.

    ![Nyissa meg a meglévő áttelepítési terv menüpont](media/migrate-tool/migrate-tool-open-plan.png)

2. Válassza ki az áttelepítési fájl, majd **nyitott**.

## <a name="step-1-analyze-and-plan-migration"></a>1. lépés: Elemzése és áttelepítés tervezése

A **elemzés & az áttelepítési terv** lap lehetővé teszi az jelenleg az Azure-előfizetéshez erőforráscsoportban nézetét.

![Elemezze & terv áttelepítési lap](media/migrate-tool/migrate-tool-step1.png)

Követően áttekintjük a *SampleResourceGroup* példaként.

### <a name="paas-topology"></a>A PaaS topológia

Ez a lista tartalmazza az a *erőforráscsoport > munkaterület gyűjtemények > munkaterületek*. Az erőforrás-csoport és a munkaterület gyűjteményeket jeleníti meg egy rövid nevet. A munkaterületek egy GUID Azonosítót jeleníti meg.

A listában lévő elemeket is megjeleníti színt és egy számot (#/ #) formátumban. Ez azt jelzi, hogy letölthető-jelentések száma.

Egy fekete szín azt jelenti, hogy minden jelentés tölthető le. Piros szín azt jelenti, hogy egyes jelentések nem lehet letölteni. A bal oldali számot jelzi, a letölthető jelentések teljes száma. A jobb oldali szám azt jelzi, hogy csoportosításon belül jelentések teljes száma.

A PaaS-topológia a jelentések megjelenítéséhez a jelentések szakaszban található elem kiválasztása

### <a name="reports"></a>Jelentések

A jelentések szakaszban, a rendelkezésre álló jelentések megjelennek, és azt jelzi, hogy letölthető-e.

![A Power BI munkaterület gyűjtemények belül jelentések listája](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Célstruktúra

A **céloz struktúra** van, ahol az eszköz mondja el ahol dolgot letöltődik és hogyan tölthet fel őket.

#### <a name="download-plan"></a>Töltse le a terv

Egy elérési utat a rendszer automatikusan létrehozza az Ön. Ha kívánja, módosíthatja az elérési út. Ha módosítja az elérési út, akkor jelölje ki **útvonalak frissítése**.

**Ez nem ténylegesen a letöltéshez.** Ez a struktúra, ahol a jelentések letöltődnek a csak meg.

#### <a name="upload-plan"></a>Töltse fel a terv

Itt adhatja meg az alkalmazás munkaterületek belül a Power BI-ban létrehozott használt előtag. Miután az előtag a GUID azonosítóját. a munkaterület szereplő Azure lesz.

![Adja meg a csoport nevének előtagja](media/migrate-tool/migrate-tool-upload-plan.png)

**Ez nem hoz létre a csoportokat a Power BI-ban.** Ez a csoportok elnevezési struktúra csak határozza meg.

Ha megváltoztatja az előtag, akkor válasszon **terv készítése feltöltése**.

Kattintson jobb gombbal a csoport, és válassza a csoporton belül a feltöltési terv közvetlenül, átnevezése, ha szükséges.

![Nevezze át a csoport helyi menüjét.](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Neve a *csoport* nem tartalmazhat szóközöket vagy érvénytelen karaktereket tartalmaz.

## <a name="step-2-download"></a>2. lépés: letöltés

Az a **letöltése** lapon megjelenik a lista a jelentések és a kapcsolódó metaadatokkal. Láthatja, mi Exportálás állapota korábbi exportálás állapotával együtt.

![Töltse le a lap](media/migrate-tool/migrate-tool-download-tab.png)

Két választási lehetősége van.

* Válassza ki az adott jelentések, majd **kijelölt letöltése**
* Válassza ki **letöltheti az összes**.

![Töltse le a kiválasztott gomb](media/migrate-tool/migrate-tool-download-options.png)

Sikeres letöltéséhez, látni fogja a állapotot *végzett* és arról, hogy létezik-e a PBIX-fájlt fogja tartalmazni.

A letöltés befejezése után válassza ki a **csoportok létrehozása** fülre.

## <a name="step-3-create-groups"></a>3. lépés: csoportok létrehozása

Miután letöltötte az elérhető jelentéseket, akkor lépjen a **csoportok létrehozása** fülre. Ezen a lapon hoz létre az alkalmazás munkaterületek létrehozott áttelepítési tervet a Power BI szolgáltatáson belül. A megadott nevű hoz létre az alkalmazások munkaterület a **feltöltése** lapon belül **elemzés & az áttelepítési terv**.

![Csoportok lapon létrehozása](media/migrate-tool/migrate-tool-create-groups.png)

Az alkalmazás munkaterületek létrehozni, akkor ki lehet **kijelölt csoportok létrehozása** vagy **összes hiányzó csoportok létrehozása**.

Ha valamelyik lehetőséget választja, kérni fogja a bejelentkezéshez. *A hitelesítő adatait a Power BI-ban, amelyet az alkalmazás munkaterületek létrehozásához használni kívánt.*

![A Power BI bejelentkezési képernyő](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Ekkor létrejön az alkalmazás munkaterület belül a Power BI-ban. Ez az alkalmazás munkaterületet nem tölthet fel a jelentéseket.

Ellenőrizheti, hogy az alkalmazások munkaterület bejelentkezni a Power bi-ban, és ellenőrzi, hogy létezik-e a munkaterület hozta létre. Megfigyelheti, hogy semmi sem szerepel a munkaterületen.

![Alkalmazás munkaterület belül Power BI-ban](media/migrate-tool/migrate-tool-app-workspace.png)

A munkaterület létrehozását követően alakzatot áthelyezheti a **feltöltése** fülre.

## <a name="step-4-upload"></a>4. lépés: töltse fel

Az a **feltöltése** lapon ez fel kell töltenie a jelentéseket a Power BI szolgáltatásba. Az áttelepítési terv alapján a célcsoport neve mellett a letöltési lapon látni fogja töltöttük le a jelentések listáját.

![Töltse fel a lap](media/migrate-tool/migrate-tool-upload-tab.png)

Feltöltheti a kijelölt jelentéseket, vagy a jelentések lehetett feltölteni. Visszaállíthatja a feltöltési állapotot újra feltölteni az elemeket is.

Akkor is a beállítás kiválasztásával, mi a teendő, ha létezik egy jelentés ezzel a névvel. Választhat **megszakítás**, **figyelmen kívül hagyása** és **felülírása**.

![Mi a teendő, ha létezik jelentés lehetőség legördülő](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Töltse fel a kijelölt eredmények](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Ismétlődő nevek

Ha a jelentést, amely ugyanaz a neve, de tudja, hogy azt egy másik jelentés, szüksége lesz módosítása a **TargetName** a jelentés. Az áttelepítési terv XML manuális szerkesztésével módosíthatja a nevét.

Zárja be az áttelepítési eszköz végezheti el a módosítást, és nyissa meg újból az eszköz és az áttelepítési terv kell.

A fenti példában a klónozott jelentések egyikét nem sikerült egy jelentés ezzel a névvel már létezett jelző. Ha azt nyissa meg az áttelepítési terv XML, a következő lesz látható.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

A sikertelen elem a SaaSTargetReportName nevét módosíthatja azt.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

A Microsoft majd nyissa meg újra a tervet, az áttelepítési eszköz, és töltse fel a meghiúsult jelentéskészítő.

A Power bi-ba, ha visszalép, láthatja, hogy a jelentések és adatkészletek fel lett töltve az alkalmazások munkaterületen.

![A jelentéslista belül a Power BI-ban](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Helyi PBIX-fájl feltöltése

Feltöltheti a Power BI Desktop fájl helyi verzióját. Zárja be az eszközt, az XML-fájl szerkesztése és használatba a helyi pbix-fájlt a teljes elérési útja kell, a **PbixPath** tulajdonság.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Az XML-fájl módosítása után nyissa meg újra a tervet az áttelepítési eszköz belül, és töltse fel a jelentést.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery jelentések

A kapcsolati karakterlánc DirectQuery jelentések frissítéséhez frissíteni kell. Ezt megteheti belül *powerbi.com webhelyen*, vagy programozott módon kérdezhetők le a kapcsolati karakterláncot a Power BI Embedded (Paas). Egy vonatkozó példáért lásd: [kibontása DirectQuery kapcsolati karakterlánc PaaS jelentésből](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Ezután frissítse a kapcsolati karakterlánc az adatkészlet belül a Power BI-ban, és állítsa be a hitelesítő adatokat az adatforrás. Ennek módjáról a következő példákat is megtekinthetik.

* [DirectQuery kapcsolati karakterláncot a Power BI Embedded frissítése](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [DirectQuery a hitelesítő adatokat a Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>További lépések

Most, hogy a jelentéseket a Power BI Embedded áttelepítette a Power BI munkaterület gyűjtemények, mostantól frissítse az alkalmazás és a app munkaterületen a jelentések beágyazás megkezdéséhez.

További információkért lásd: [hogyan telepítheti át a Power BI-Munkaterületcsoport tartalom a Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)