---
title: JavaScript-alkalmazások forrástérkép-támogatása – Azure Monitor Application Insights
description: Megtudhatja, hogyan tölthet fel forrásleképezéseket a saját tárfiókblob-tárolóba az Application Insights használatával.
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474883"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript-alkalmazások forrástérkép-támogatása

Az Application Insights támogatja a forrásleképezések feltöltését a saját storage-fiók blob tárolóba.
A forrásleképezések segítségével nem lehet visszavenni a tranzakció részleteinek végpontja oldalon található híváshalmokat. A [JavaScript SDK][ApplicationInsights-JS] vagy a [Node.js SDK][ApplicationInsights-Node.js] által küldött kivételek nem múlva képezhetik a forrásleképezéseket.

![A hívásverem nemlehet csatolható tárfiókhoz](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>Új tárfiók és Blob-tároló létrehozása

Ha már rendelkezik egy meglévő tárfiókkal vagy blob tárolóval, kihagyhatja ezt a lépést.

1. [Új tárfiók létrehozása][create storage account]
2. [Hozzon létre egy blob tárolót][create blob container] a tárfiókon belül. Ügyeljen arra, hogy a "Nyilvános `Private`hozzáférési szint" beállítását állítsa a beállításra, hogy a forrástérképek ne legyenek nyilvánosan elérhetők.

> [!div class="mx-imgBorder"]
>![A tároló hozzáférési szintjét privátra kell állítani](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>A forrásleképezések leküldése a Blob-tárolóba

Integrálnia kell a folyamatos üzembe helyezési folyamatot a tárfiókkal, úgy konfigurálva, hogy automatikusan töltse fel a forrásleképezéseket a konfigurált Blob-tárolóba. Ne töltse fel a forrásleképezéseket a Blob-tároló egyik almappájába; jelenleg a forrástérkép csak a gyökérmappából lesz behívva.

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>Forrásleképezések feltöltése az Azure-folyamatokon keresztül (ajánlott)

Ha az Azure Pipelines segítségével folyamatosan létrehozza és telepíti az alkalmazást, adjon hozzá egy [Azure File Copy][azure file copy] feladatot a folyamathoz a forrásleképezések automatikus feltöltéséhez.

> [!div class="mx-imgBorder"]
> ![Azure File Copy feladat hozzáadása a folyamathoz a forrásleképezések Azure Blob Storage-ba való feltöltéséhez](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>Az Application Insights-erőforrás konfigurálása forrásleképezési tárfiókkal

### <a name="from-the-end-to-end-transaction-details-page"></a>A végpontok között a tranzakció részletei lapon

A végpontok között a tranzakciók részletei lapon az *Unminify gombra* kattinthat, és megjelenik egy kérdés a konfigurálásra, ha az erőforrás nincs konfigurálva.

1. A portálon tekintse meg a minizált kivétel részleteit.
2. Kattintson *unminify*
3. Ha az erőforrás nincs konfigurálva, egy üzenet jelenik meg, amely a konfigurálásra kéri.

### <a name="from-the-properties-page"></a>A tulajdonságok lapról

Ha szeretné konfigurálni vagy módosítani a tárfiókot vagy blob tárolót, amely az Application Insights-erőforráshoz kapcsolódik, megteheti az Application Insights erőforrás *tulajdonságai* lapján.

1. Nyissa meg az Application Insights-erőforrás *Tulajdonságok* lapját.
2. Kattintson a *Forrástérkép blobtároló módosítása gombra.*
3. Válasszon ki egy másik Blob-tárolót forrásleképezési tárolóként.
4. Kattintson a `Apply` gombra.

> [!div class="mx-imgBorder"]
> ![Konfigurálja újra a kiválasztott Azure Blob-tárolót a Tulajdonságok panelre navigálva](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Szükséges szerepköralapú hozzáférés-vezérlési (RBAC) beállítások a Blob tárolóban

A portálon ezt a funkciót használó bármely felhasználónak legalább [storage Blob-adatolvasóként][storage blob data reader] hozzá kell rendelnie a Blob-tárolóhoz. Ezt a szerepkört hozzá kell rendelnie bárki máshoz, aki a forrástérképeket fogja használni ezen a funkción keresztül.

> [!NOTE]
> A tároló létrehozásának módjától függően előfordulhat, hogy ez nem lett automatikusan hozzárendelve Önhöz vagy a csapatához.

### <a name="source-map-not-found"></a>A forrástérkép nem található

1. Ellenőrizze, hogy a megfelelő forrásleképezés a megfelelő blobtárolóba van-e feltöltve
2. Ellenőrizze, hogy a forrástérképfájl neve a javascript-fájl ról `.map`kapta-e, amelyhez hozzá van rendelve, és a segítségével van-e utótag.
    - Például `/static/js/main.4e2ca5fa.chunk.js` megkeresi a nevű blobot`main.4e2ca5fa.chunk.js.map`
3. Ellenőrizze a böngésző konzolján, hogy vannak-e hibák naplózása. Ezt adja meg minden támogatási jegynek.

## <a name="next-steps"></a>Következő lépések

* [Azure fájlmásolási feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme