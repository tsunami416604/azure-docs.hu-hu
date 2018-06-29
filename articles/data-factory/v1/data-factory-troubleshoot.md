---
title: Azure Data Factory elhárítása
description: További tudnivalók az Azure Data Factory használatával kapcsolatos problémák elhárítása.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: cbd51c48ec5b1801062e7aaf0b77e7b347018b31
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051865"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory-hibák elhárítása
> [!NOTE]
> Ez a cikk az Azure Data Factory 1 verziójára vonatkozik. 

Ez a cikk biztosítja a hibaelhárítási tippekért problémák Azure Data Factory használatával. Ez a cikk nem szerepel a lehetséges problémák, a szolgáltatás használata esetén, de bizonyos problémák és általános hibaelhárítási tippek magában foglalja.   

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Hiba: Az előfizetés nincs regisztrálva a Microsoft.DataFactory névtér használatára
Amennyiben ezt a hibaüzenetet kapja, az Azure Data Factory erőforrás-szolgáltató nincs regisztrálva a számítógépén. Tegye a következőket:

1. Indítsa el az Azure PowerShellt.
2. Jelentkezzen be az Azure-fiókjával az alábbi parancs segítségével.

    ```powershell
    Connect-AzureRmAccount
    ```
3. A következő parancsot az Azure Data Factory-szolgáltató regisztrálásához.

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probléma: Nem engedélyezett hiba a Data Factory parancsmag futtatásakor
Valószínűleg nem a megfelelő Azure-fiókot vagy előfizetést használja az Azure PowerShell futtatásához. Az alábbi parancsmagokkal válassza ki a megfelelő Azure-fiókot és előfizetést az Azure PowerShell használatához.

1. Connect-AzureRmAccount - használja a megfelelő felhasználói Azonosítót és jelszót
2. Get-AzureRmSubscription – a fiókhoz tartozó előfizetések megtekintéséhez.
3. SELECT-AzureRmSubscription &lt;előfizetés neve&gt; -válassza ki a megfelelő előfizetést. Használja ugyanazt, adat-előállító létrehozása az Azure portál használatával.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probléma: Nem sikerült indítsa el az adatok Management Gateway Express telepítése Azure-portálon
Az adatkezelési átjáró expressz telepítéséhez az Internet Explorer vagy egy Microsoft ClickOnce-kompatiblis webböngésző szükséges. Amennyiben az expressz telepítés nem indul el, tegye a következők egyikét:

* Az Internet Explorer vagy a Microsoft ClickOnce kompatibilis böngésző használata.

    Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.

    Tegye meg ugyanezt a Firefox (install-bővítmény). Kattintson az eszköztár Menü megnyitása gombjára (három vízszintes csík a jobb felső sarokban). Kattintson a Kiegészítők menüpontra, keressen a „ClickOnce” kulcsszóra, jelöljön ki egy ClickOnce-bővítmény, és telepítse azt.
* Használja a **manuális telepítés** a portálon azonos panelen látható hivatkozásra. Ezt a módszert használja a telepítőfájl letöltéséhez, és manuális futtatása. A telepítés befejezését követően az adatok adatkezelési átjáró konfigurálása párbeszédpanel jelenik meg. Másolja ki a **kulcsot** a Portál képernyőről, és a konfigurációkezelőben ezt használva regisztrálja az átjárót a szolgáltatásban.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Probléma: Nem sikerült kapcsolódni a helyszíni SQL Server
Indítsa el **az adatkezelési átjáró konfigurációkezelőjének** az átjárót működtető gépen, és használja a **hibaelhárítás** lapon, a kapcsolat tesztelése az SQL-kiszolgálón az átjárót működtető gépen. Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Probléma: Ha valaha is várakozó állapotban vannak bemeneti szeletek
A szeletek lehet a **Várakozás** állapot különféle okok miatt. A gyakori okai, hogy a **külső** tulajdonság értéke nem **igaz**. Bármely Azure Data Factory hatókörén kívül előállított dataset fel kell tüntetni **külső** tulajdonság. Ez a tulajdonság jelzi, hogy az adatok külső és nem mentett adat-előállító belül bármely folyamatok által-e. Az adatszeletek **Készként** vannak jelölve, amint elérhetőek az adatok a megfelelő tárban.

Tekintse meg a következő példát az **external** tulajdonság használatáról. Opcionálisan megadhat **externalData*** beállításakor külső igaz értékre.

A tulajdonságról az [adatkészleteket](data-factory-create-datasets.md) ismertető cikkben talál további információt.

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

A hiba megoldásához adja hozzá az **external** tulajdonságot és a választható **externalData** szakaszt a bemeneti tábla JSON-definíciójához, és hozza létre ismét a táblát.

### <a name="problem-hybrid-copy-operation-fails"></a>Probléma: Hibrid másolás nem lehetséges
Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) lépéseket elhárítása onnan egy a helyszíni adatok másolása az adatkezelési átjáró használatával tárolja.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probléma: Igény szerinti HDInsight kiépítése sikertelen
HDInsightOnDemand típusú társított szolgáltatás használata esetén meg kell adnia egy linkedServiceName, amely egy Azure Blob Storage mutat. A Data Factory szolgáltatás ezt a tárolót használja az igény szerinti HDInsight-fürt naplófájljainak és a kapcsolódó fájloknak a tárolásához.  Néha az igény szerinti HDInsight-fürt kiépítése meghiúsul, és a következő hibaüzenet jelenik meg:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Ez a hiba többnyire azt jelzi, hogy a linkedServiceName szolgáltatásban megadott tárfiók nem ugyanazon az adatközpont-helyen található, ahol a HDInsight kiépítése történik. Példa: Ha a data factory USA nyugati régiója és az Azure storage egy, az USA keleti régiója, az igény szerinti kiépítés sikertelen lesz az USA nyugati régiója.

Az additionalLinkedServiceNames egy másik JSON-tulajdonság, amelyben további tárfiókok adhatók meg az igény szerinti HDInsight szolgáltatáshoz. Ezen további társított tárfiókokat és a HDInsight-fürt ugyanazon a helyen kell lennie, vagy a azonos hiba miatt sikertelen.

### <a name="problem-custom-net-activity-fails"></a>Probléma: Egyéni .NET tevékenység sikertelen
Lásd: [folyamat hibakeresése egyéni tevékenységeket](data-factory-use-custom-activities.md#troubleshoot-failures) a részletes lépéseket.

## <a name="use-azure-portal-to-troubleshoot"></a>Az Azure portál használatával hibáinak elhárítása
### <a name="using-portal-blades"></a>Portál paneleken használatával
Lásd: [figyelő folyamat](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) lépéseket.

### <a name="using-monitor-and-manage-app"></a>A Megfigyelés és kezelés alkalmazás használata
Lásd: [figyelése és kezelése a data factory folyamatok figyelése és kezelése App](data-factory-monitor-manage-app.md) részleteket.

## <a name="use-azure-powershell-to-troubleshoot"></a>Hibaelhárítás az Azure PowerShell használatával
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Egy hiba elhárítása az Azure PowerShell használatával
Lásd: [képernyő adat-előállító folyamatok, az Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) részleteiről.

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
