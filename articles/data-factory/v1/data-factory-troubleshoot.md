---
title: Az Azure Data Factory problémáinak elhárítása
description: Ismerje meg, hogyan háríthatja el az Azure Data Factory használatával kapcsolatos problémákat.
services: data-factory
documentationcenter: ''
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
robots: noindex
ms.openlocfilehash: 81ae5c3c702108d854e4dfde93001d5c99875666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931584"
---
# <a name="troubleshoot-data-factory-issues"></a>Data Factory-hibák elhárítása
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. 

Ez a cikk hibaelhárítási tippeket tartalmaz az Azure Data Factory használata során felmerülő problémákhoz. Ez a cikk nem sorolja fel az összes lehetséges problémát a szolgáltatás használata során, de néhány problémát és általános hibaelhárítási tippeket tartalmaz.   

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Hiba: Az előfizetés nincs regisztrálva a Microsoft.DataFactory névtér használatára
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

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Probléma: Adatfeldolgozó parancsmag futtatásakor nem engedélyezett hiba
Valószínűleg nem a megfelelő Azure-fiókot vagy előfizetést használja az Azure PowerShell futtatásához. Az alábbi parancsmagokkal válassza ki a megfelelő Azure-fiókot és előfizetést az Azure PowerShell használatához.

1. Connect-AzAccount - A megfelelő felhasználói azonosító és jelszó használata
2. Get-AzSubscription – A fiók összes előfizetésének megtekintése.
3. Select-AzSubscription &lt;előfizetés&gt; neve – Válassza ki a megfelelő előfizetést. Használja ugyanazt, amelyet az Azure Portalon egy adatgyár létrehozásához használ.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Probléma: Nem sikerült elindítani a Data Management Gateway Express telepítőjét az Azure Portalról
Az adatkezelési átjáró expressz telepítéséhez az Internet Explorer vagy egy Microsoft ClickOnce-kompatiblis webböngésző szükséges. Amennyiben az expressz telepítés nem indul el, tegye a következők egyikét:

* Használja az Internet Explorert vagy a Microsoft ClickOnce kompatibilis webböngészőt.

    Amennyiben Chrome böngészőt használ, keresse fel a [Chrome webáruházát](https://chrome.google.com/webstore/), keressen rá a „ClickOnce” kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, majd telepítse azt.

    Tegye ugyanezt a Firefox (install add-in). Kattintson az eszköztár Menü megnyitása gombjára (három vízszintes csík a jobb felső sarokban). Kattintson a Kiegészítők menüpontra, keressen a „ClickOnce” kulcsszóra, jelöljön ki egy ClickOnce-bővítmény, és telepítse azt.
* Használja a portál on ugyanazon a panelen látható **Kézi beállítás** hivatkozást. Ezzel a módszersel letöltheti a telepítőfájlt, és manuálisan futtathatja. A telepítés sikeres befejezése után megjelenik az Adatkezelési átjáró konfigurációja párbeszédpanel. Másolja ki a **kulcsot** a Portál képernyőről, és a konfigurációkezelőben ezt használva regisztrálja az átjárót a szolgáltatásban.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Probléma: Nem sikerült csatlakozni a helyszíni SQL Server kiszolgálóhoz
Indítsa el az **Adatkezelési átjáró konfigurációkezelőjét** az átjárógépen, és a **Hibaelhárítás** lapon tesztelje az SQL Server-kiszolgálóval való kapcsolatot az átjárógépről. A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Probléma: A bemeneti szeletek örökké várakozó állapotban vannak
A szeletek különböző okok miatt **várakozó** állapotban lehetnek. Ennek egyik gyakori oka az, hogy a **külső** tulajdonság értéke nincs **true**értékre állítva. Az Azure Data Factory hatókörén kívül előállított adatkészleteket **külső** tulajdonsággal kell megjelölni. Ez a tulajdonság azt jelzi, hogy az adatok külső, és nem támogatja az adat-előállító folyamatok. Az adatszeletek **Készként** vannak jelölve, amint elérhetőek az adatok a megfelelő tárban.

Tekintse meg a következő példát az **external** tulajdonság használatáról. Megadhatja a **külsőadatok*** értéket, ha a külső értéket true értékre állítja be.

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

### <a name="problem-hybrid-copy-operation-fails"></a>Probléma: A hibrid másolási művelet sikertelen
Lásd: [Az átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) az adatkezelési átjáró használatával a helyszíni adattárba történő másolással és-tárolásból történő másolással kapcsolatos problémák elhárításához.

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Probléma: Az igény szerinti HDInsight-kiépítés sikertelen
HDInsightOnDemand típusú társított szolgáltatás használata esetén olyan linkedServiceName nevet kell megadni, amely egy Azure Blob Storage-ra mutat. A Data Factory szolgáltatás ezt a tárolót használja az igény szerinti HDInsight-fürt naplófájljainak és a kapcsolódó fájloknak a tárolásához.  Néha az igény szerinti HDInsight-fürt kiépítése meghiúsul, és a következő hibaüzenet jelenik meg:

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

Ez a hiba többnyire azt jelzi, hogy a linkedServiceName szolgáltatásban megadott tárfiók nem ugyanazon az adatközpont-helyen található, ahol a HDInsight kiépítése történik. Példa: ha az adatgyár az USA nyugati részén található, és az Azure-tároló az USA keleti részén található, az igény szerinti kiépítés sikertelen az USA nyugati részén.

Az additionalLinkedServiceNames egy másik JSON-tulajdonság, amelyben további tárfiókok adhatók meg az igény szerinti HDInsight szolgáltatáshoz. Ezek a további csatolt tárfiókok kell lennie ugyanazon a helyen, mint a HDInsight-fürt, vagy ez sikertelen ugyanazzal a hibával.

### <a name="problem-custom-net-activity-fails"></a>Probléma: Az egyéni .NET tevékenység sikertelen
A részletes lépésekről a [Folyamat hibakeresése egyéni tevékenységgel című](data-factory-use-custom-activities.md#troubleshoot-failures) témakörben található.

## <a name="use-azure-portal-to-troubleshoot"></a>Hibaelhárítás az Azure Portal használatával
### <a name="using-portal-blades"></a>Portállapok használata
A lépéseket a [Folyamat figyelése](data-factory-monitor-manage-pipelines.md) témakörben található.

### <a name="using-monitor-and-manage-app"></a>A Megfigyelés és kezelés alkalmazás használata
A részletekért tekintse meg az [adatfeldolgozó folyamatok figyelése és kezelése a Figyelő és az Alkalmazás kezelése használatával](data-factory-monitor-manage-app.md) című témakört.

## <a name="use-azure-powershell-to-troubleshoot"></a>Hibaelhárítás az Azure PowerShell használatával
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Hiba elhárítása az Azure PowerShell használatával
A részletekért tekintse meg [az Adatok gyári folyamatok figyelése az Azure PowerShell használatával.](data-factory-monitor-manage-pipelines.md)

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: https://go.microsoft.com/fwlink/?LinkId=516971

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
