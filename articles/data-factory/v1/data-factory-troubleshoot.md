---
title: Az Azure Data Factory-hibák elhárítása
description: Ismerje meg az Azure Data Factory használatával kapcsolatos problémák elhárítása.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 053cabfa29bd5e436fecd922e4bcdbca9483d25a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455683"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory-hibák elhárítása
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. 

Ez a cikk hibaelhárítási tippek az Azure Data Factory használata során problémák nyújt. Ez a cikk nem tartalmazza a lehetséges problémákat, a szolgáltatás használatához, de bizonyos problémákat és az általános hibaelhárítási tippek lefedi.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Hiba: Az előfizetés nincs regisztrálva a "Microsoft.DataFactory" névtér
Amennyiben ezt a hibaüzenetet kapja, az Azure Data Factory erőforrás-szolgáltató nincs regisztrálva a számítógépén. Tegye a következőket:

1. Indítsa el az Azure PowerShellt.
2. Jelentkezzen be az Azure-fiókjába a következő paranccsal.

    ```powershell
    Connect-AzAccount
    ```
3. Futtassa a következő parancsot az Azure Data Factory-szolgáltató regisztrálásához.

    ```powershell        
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probléma: A Data Factory-parancsmagok futtatásakor jogosulatlan hiba
Valószínűleg nem a megfelelő Azure-fiókot vagy előfizetést használja az Azure PowerShell futtatásához. Az alábbi parancsmagokkal válassza ki a megfelelő Azure-fiókot és előfizetést az Azure PowerShell használatához.

1. Connect-AzAccount – használja a megfelelő felhasználói Azonosítót és jelszót
2. Get-AzSubscription – a fiókhoz tartozó előfizetések megtekintéséhez.
3. SELECT-AzSubscription &lt;előfizetésnevet&gt; – válassza ki a megfelelő előfizetést. Használja ugyanazt, adat-előállító létrehozása az Azure Portal használatával.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probléma: Nem sikerült elindítani az adatkezelési átjáró expressz telepítését az Azure Portalról
Az adatkezelési átjáró expressz telepítéséhez az Internet Explorer vagy egy Microsoft ClickOnce-kompatiblis webböngésző szükséges. Amennyiben az expressz telepítés nem indul el, tegye a következők egyikét:

* Használja az Internet Explorer vagy egy Microsoft ClickOnce-kompatibilis böngészőt.

    Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.

    Tegye meg ugyanezt a Firefox (install-bővítmény). Kattintson az eszköztár Menü megnyitása gombjára (három vízszintes csík a jobb felső sarokban). Kattintson a Kiegészítők menüpontra, keressen a „ClickOnce” kulcsszóra, jelöljön ki egy ClickOnce-bővítmény, és telepítse azt.
* Használja a **manuális telepítés** a portál ugyanezen paneljén látható hivatkozásra. Ez a módszer használatával töltse le a telepítőfájlt, és manuálisan futtathatja. Miután a telepítés sikeres, megjelenik az adatkezelési átjáró konfigurációs párbeszédpanel. Másolja ki a **kulcsot** a Portál képernyőről, és a konfigurációkezelőben ezt használva regisztrálja az átjárót a szolgáltatásban.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Probléma: Nem sikerült csatlakozni a helyszíni SQL Server
Indítsa el a **Data Management Gateway Configuration Manager** az átjárót tartalmazó számítógépen, és használja a **hibaelhárítás** fülre, és tesztelje a kapcsolatot az SQL Server, az átjárót tartalmazó számítógépen. Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Probléma: A bemeneti szeletek örökké várakozó állapotban vannak
A szeletek lehetnek **Várakozás** állapot különböző okok miatt. Egyik gyakori oka, hogy a **külső** tulajdonság értéke nem **igaz**. Az Azure Data Factory hatókörén kívül létrejött összes adatkészletet kell megjelölni **külső** tulajdonság. Ez a tulajdonság azt jelzi, hogy az adatok külső, és nem készült biztonsági másolat által bármely belül az adat-előállító folyamatok-e. Az adatszeletek **Készként** vannak jelölve, amint elérhetőek az adatok a megfelelő tárban.

Tekintse meg a következő példát az **external** tulajdonság használatáról. Opcionálisan megadhat **externalData*** beállításakor external tulajdonság igaz értékű.

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

### <a name="problem-hybrid-copy-operation-fails"></a>Probléma: Hibrid másolási művelet sikertelen lesz.
Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) lépések végrehajtásával kapcsolatos hibaelhárítás és- tárolókról a helyszíni adatok másolása tárolja az adatkezelési átjáró segítségével.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probléma: Igény szerinti HDInsight-kiépítés sikertelen
HDInsightOnDemand típusú társított szolgáltatás használata esetén adja meg a linkedServiceName, amely egy Azure Blob Storage kell. A Data Factory szolgáltatás ezt a tárolót használja az igény szerinti HDInsight-fürt naplófájljainak és a kapcsolódó fájloknak a tárolásához.  Néha az igény szerinti HDInsight-fürt kiépítése meghiúsul, és a következő hibaüzenet jelenik meg:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Ez a hiba többnyire azt jelzi, hogy a linkedServiceName szolgáltatásban megadott tárfiók nem ugyanazon az adatközpont-helyen található, ahol a HDInsight kiépítése történik. Példa: Ha a data factory az USA nyugati adatközpontjába és az Azure storage az USA keleti régiójában, az igény szerinti kiépítés sikertelen lesz az USA nyugati RÉGIÓJA.

Az additionalLinkedServiceNames egy másik JSON-tulajdonság, amelyben további tárfiókok adhatók meg az igény szerinti HDInsight szolgáltatáshoz. Ezeket a további társított storage-fiókokat a HDInsight-fürt ugyanazon a helyen kell lennie, vagy a ugyanazon hiba miatt sikertelen.

### <a name="problem-custom-net-activity-fails"></a>Probléma: Egy egyéni .NET-tevékenység meghiúsul
Lásd: [egyéni tevékenységet a folyamat hibakereséshez](data-factory-use-custom-activities.md#troubleshoot-failures) a részletes lépéseket.

## <a name="use-azure-portal-to-troubleshoot"></a>Hibaelhárítás az Azure portal használatával
### <a name="using-portal-blades"></a>Portal paneljeinek használatával
Lásd: [folyamat figyelése](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline) lépéseit.

### <a name="using-monitor-and-manage-app"></a>A Megfigyelés és kezelés alkalmazás használata
Lásd: [figyelése és adat-előállító folyamatok Monitor and Manage Appot használata kezelheti](data-factory-monitor-manage-app.md) részleteiről.

## <a name="use-azure-powershell-to-troubleshoot"></a>Hibaelhárítás az Azure PowerShell használatával
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Egy hiba elhárítása az Azure PowerShell használatával
Lásd: [figyelő Data Factory-folyamatok az Azure PowerShell-lel](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) részleteiről.

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
