---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 8a4fbb3803f2eed4f961792a29a6bf8a327ea208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657549"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

2. A **Közzététel célhelyének kiválasztása**területen használja az alábbi táblázatban megadott közzétételi beállításokat: 

    | Beállítás      | Leírás                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure-függvényalkalmazás** | Function-alkalmazás létrehozása Azure-beli felhőalapú környezetben. | 
    | **Új létrehozása** | A kapcsolódó erőforrásokkal rendelkező új Function-alkalmazás az Azure-ban jön létre. <br/>Ha a **meglévő kiválasztása**lehetőséget választja, az Azure-ban meglévő Function alkalmazásban lévő összes fájlt felülírja a helyi projekt fájljai. Ezt a lehetőséget csak akkor használja, ha újra közzéteszi a frissítéseket egy meglévő Function alkalmazásban. |
    | **Futtatás a csomagfájl alapján** | A Function alkalmazás üzembe helyezése a [zip-telepítéssel](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) , a [csomaghoz való futtatási](../articles/azure-functions/run-functions-from-deployment-package.md) móddal engedélyezve. Ez a központi telepítés, amely jobb teljesítményt eredményez, a függvények futtatásának ajánlott módja. <br/>Ha nem használja ezt a kapcsolót, ügyeljen arra, hogy a Function app-projekt helyileg fusson, mielőtt közzéteszi az Azure-ban. |

    ![Közzétételi cél kiválasztása](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Kattintson a **Publish** (Közzététel) elemre. Ha még nem jelentkezett be Azure-fiókjába a Visual studióból, válassza a **Bejelentkezés**lehetőséget. Létrehozhat egy ingyenes Azure-fiókot is.

4. **Azure app Service: Create New (új létrehozása**), használja a következő táblázatban megadott értékeket:

    | Beállítás      | Érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Fogadja el ezt a nevet, vagy adjon meg egy új nevet. Érvényes karakterek: `a-z`, `0-9`és. `-` |
    | **Előfizetés** | Az Ön előfizetése | A használandó előfizetés. Fogadja el ezt az előfizetést, vagy válasszon újat a legördülő listából. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | Az erőforráscsoport neve |  Az erőforráscsoport, amelyben létre szeretné hozni a Function alkalmazást. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új** lehetőséget egy új erőforráscsoport létrehozásához.|
    | **[Üzemeltetési csomag](../articles/azure-functions/functions-scale.md)** | A üzemeltetési terv neve | A kiszolgáló nélküli terv konfigurálásához válassza az **új** lehetőséget. Ügyeljen arra, hogy a **méret**területen válassza a **felhasználás** lehetőséget. Ha a projektet egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtásához kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. Ha nem a **felhasználást**tervezi, akkor a [Function alkalmazás skálázását](../articles/azure-functions/functions-scale.md)kell kezelnie. Válasszon egy **helyet** a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatásokhoz.  |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Általános célú Storage-fiók | A functions futtatókörnyezet megköveteli egy Azure Storage-fiók megírását. Válassza az **új** lehetőséget az általános célú Storage-fiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. A **Létrehozás** gombra kattintva hozzon létre egy Function alkalmazást és annak kapcsolódó erőforrásait az Azure-ban ezekkel a beállításokkal, és telepítse a függvény projekt kódját. 

6. Válassza a közzététel lehetőséget, majd az üzembe helyezés befejezése után jegyezze fel a **webhely URL-** címét, amely az Azure-beli Function alkalmazás címe.

    ![Sikeres közzétételt jelző üzenet](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
