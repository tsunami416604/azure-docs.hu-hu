---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008187"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **cél** lapon válassza az **Azure** - :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="cél kiválasztása"::: lehetőséget.

1. **Adott cél** esetében válassza az **Azure függvényalkalmazás (Windows) lehetőséget.**

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Azure-függvényalkalmazás kiválasztása":::

1. A **Function példányban** válassza az **új Azure-függvény létrehozása...** lehetőséget, majd használja a következő táblázatban megadott értékeket:

    | Beállítás      | Érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Fogadja el ezt a nevet, vagy adjon meg egy új nevet. Érvényes karakterek: `a-z` , `0-9` és `-` . |
    | **Előfizetés** | Az Ön előfizetése | A használandó előfizetés. Fogadja el ezt az előfizetést, vagy válasszon újat a legördülő listából. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | Az erőforráscsoport neve |  Az erőforráscsoport, amelyben létre szeretné hozni a Function alkalmazást. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új** lehetőséget egy új erőforráscsoport létrehozásához.|
    | **[Csomag típusa](../articles/azure-functions/functions-scale.md)** | Használatalapú | Ha a projektet egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. |
    | **Hely** | Az App Service helye | Válasszon egy **helyet** a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatásokhoz. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Általános célú Storage-fiók | A functions futtatókörnyezet megköveteli egy Azure Storage-fiók megírását. Válassza az **új** lehetőséget az általános célú Storage-fiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Válassza a **Létrehozás** lehetőséget, és hozzon létre egy Function alkalmazást és kapcsolódó erőforrásait az Azure-ban. 
1. Győződjön meg arról, hogy a **functions példányban** a **Futtatás a csomagból fájl** van bejelölve. A Function alkalmazás üzembe helyezése a [zip-telepítéssel](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) , a [csomaghoz való futtatási](../articles/azure-functions/run-functions-from-deployment-package.md) móddal engedélyezve. Ez az ajánlott üzembe helyezési módszer a functions-projekthez, mert jobb teljesítményt eredményez. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Profil létrehozásának befejezése":::

1. Válassza a **Befejezés** lehetőséget, majd a közzététel lapon válassza a **Közzététel** lehetőséget, hogy az Azure-ban lévő új Function alkalmazásba telepítse a Project-fájlokat tartalmazó csomagot. 

    Miután az üzembe helyezés befejeződött, az Azure-beli Function alkalmazás gyökerének URL-címe megjelenik a **Közzététel** lapon. 
    
1.  A közzététel lapon válassza a **kezelés a Cloud Explorerben** lehetőséget. Ekkor megnyílik az új Function app Azure-erőforrás a Cloud Explorerben. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Sikeres közzétételt jelző üzenet":::
    
    A Cloud Explorer használatával megtekintheti a hely tartalmát, elindíthatja és leállíthatja a Function alkalmazást, és közvetlenül böngészhet az Azure-ban és a Azure Portalban használható alkalmazás-erőforrásokkal. 
