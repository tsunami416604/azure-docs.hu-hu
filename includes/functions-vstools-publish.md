---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84231556"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel célhelyének kiválasztása**területen használja az alábbi táblázatban megadott közzétételi beállításokat: 

    | Beállítás      | Leírás                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions használati terv** | Hozzon létre egy Function alkalmazást egy olyan Azure-beli felhőalapú környezetben, amely egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban fut. Ha felhasználási tervet használ, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. Ha a programot nem a használati tervből futtatja, akkor a [Function alkalmazás skálázását](../articles/azure-functions/functions-scale.md)kell kezelnie.| 
    | **Új létrehozása** | A kapcsolódó erőforrásokkal rendelkező új Function-alkalmazás az Azure-ban jön létre. <br/>Ha a **meglévő kiválasztása**lehetőséget választja, az Azure-ban meglévő Function alkalmazásban lévő összes fájlt felülírja a helyi projekt fájljai. Ezt a lehetőséget csak akkor használja, ha újra közzéteszi a frissítéseket egy meglévő Function alkalmazásban. |
    | **Futtatás a csomagfájl alapján** | A Function alkalmazás üzembe helyezése a [zip-telepítéssel](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) , a [csomaghoz való futtatási](../articles/azure-functions/run-functions-from-deployment-package.md) móddal engedélyezve. Ez a központi telepítés, amely jobb teljesítményt eredményez, a függvények futtatásának ajánlott módja. <br/>Ha nem használja ezt a kapcsolót, ügyeljen arra, hogy a Function app-projekt helyileg fusson, mielőtt közzéteszi az Azure-ban. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Közzétételi profil létrehozása":::


1. Válassza a **Profil létrehozása** lehetőséget. Ha még nem jelentkezett be Azure-fiókjába a Visual studióból, válassza a **Bejelentkezés**lehetőséget. Létrehozhat egy ingyenes Azure-fiókot is.

1. **App Service: Create New (új létrehozása**), használja a következő táblázatban megadott értékeket:

    | Beállítás      | Érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Fogadja el ezt a nevet, vagy adjon meg egy új nevet. Érvényes karakterek: `a-z` , `0-9` és `-` . |
    | **Előfizetés** | Az Ön előfizetése | A használandó előfizetés. Fogadja el ezt az előfizetést, vagy válasszon újat a legördülő listából. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | Az erőforráscsoport neve |  Az erőforráscsoport, amelyben létre szeretné hozni a Function alkalmazást. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új** lehetőséget egy új erőforráscsoport létrehozásához.|
    | **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)** | A üzemeltetési terv neve | A kiszolgáló nélküli terv konfigurálásához válassza az **új** lehetőséget. Ügyeljen arra, hogy a **méret**területen válassza a **felhasználás** lehetőséget. Ha a projektet egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. Ha nem a **felhasználást**tervezi, akkor a [Function alkalmazás skálázását](../articles/azure-functions/functions-scale.md)kell kezelnie.  |
    | **Hely** | Az App Service helye | Válasszon egy **helyet** a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatásokhoz. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Általános célú Storage-fiók | A functions futtatókörnyezet megköveteli egy Azure Storage-fiók megírását. Válassza az **új** lehetőséget az általános célú Storage-fiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. A **Létrehozás** gombra kattintva hozzon létre egy Function alkalmazást és annak kapcsolódó erőforrásait az Azure-ban ezekkel a beállításokkal, és telepítse a függvény projekt kódját. 

1. Válassza a **Közzététel** lehetőséget, és várja meg, amíg a telepítés befejeződik. 

    Miután az üzembe helyezés befejeződött, az Azure-beli Function alkalmazás gyökerének URL-címe megjelenik a **Közzététel** lapon. 
    
1.  A közzététel lapon válassza a **kezelés a Cloud Explorerben**lehetőséget. Ekkor megnyílik az új Function app Azure-erőforrás a Cloud Explorerben. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Közzétételi profil létrehozása":::
    
    A Cloud Explorer használatával megtekintheti a hely tartalmát, elindíthatja és leállíthatja a Function alkalmazást, és közvetlenül böngészhet az Azure-ban és a Azure Portalban használható alkalmazás-erőforrásokkal. 