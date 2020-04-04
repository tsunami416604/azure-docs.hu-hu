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
ms.openlocfilehash: 0e69a11ab81406f650049e3c7d4d446b6628b29b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657549"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

2. A **Közzétételi cél választása párbeszédpanelen**használja az alábbi táblázatban megadott közzétételi beállításokat: 

    | Beállítás      | Leírás                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions fogyasztási terv** | Hozzon létre egy függvényalkalmazást egy Azure-beli felhőkörnyezetben, amely [egy felhasználási tervben](../articles/azure-functions/functions-scale.md#consumption-plan)fut. Ha egy felhasználási sémát használ, csak a függvények alkalmazás végrehajtásáért kell fizetnie. Más tárhelytervek magasabb költségekkel járnak. Ha nem felhasználási tervben futtat, akkor a [függvényalkalmazás méretezését kell kezelnie.](../articles/azure-functions/functions-scale.md)| 
    | **Új létrehozása** | Egy új függvényalkalmazás, a kapcsolódó erőforrások, jön létre az Azure-ban. <br/>Ha a Meglévő kijelölése lehetőséget **választja,** az Azure-beli meglévő függvényalkalmazás összes fájlját felülírják a helyi projekt fájljai. Ezt a beállítást csak akkor használja, ha egy meglévő függvényalkalmazás frissítéseit újra közzéteszi. |
    | **Futtatás csomagfájlból** | A függvényalkalmazás a [Zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) használatával van telepítve, és engedélyezve van [a "Run-From-Package"](../articles/azure-functions/run-functions-from-deployment-package.md) mód. Ez a központi telepítés, amely jobb teljesítményt eredményez, a funkciók futtatásának ajánlott módja. <br/>Ha nem használja ezt a beállítást, győződjön meg arról, hogy állítsa le a függvényalkalmazás-projekt helyileg fut, mielőtt közzéteszi az Azure-ban. |

    ![Közzétételi cél kiválasztása](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Válassza a **Profil létrehozása** lehetőséget. Ha még nem jelentkezett be azure-fiókjába a Visual Studio-ból, válassza **a Bejelentkezés**lehetőséget. Ingyenes Azure-fiókot is létrehozhat.

4. Az **App Service-ben: Hozzon létre újat,** használja az alábbi táblázatban megadott értékeket:

    | Beállítás      | Érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Fogadja el ezt a nevet, vagy írjon be egy új nevet. Az érvényes `a-z`karakterek `0-9`a `-`következők: , , és . |
    | **Előfizetés** | Az Ön előfizetése | A használandó előfizetés. Fogadja el ezt az előfizetést, vagy válasszon egy újat a legördülő listából. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | Az erőforráscsoport neve |  Az erőforráscsoport, amelyben a függvényalkalmazást létre szeretné hozni. Jelöljön ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **Új** lehetőséget új erőforráscsoport létrehozásához.|
    | **[Tárhelyterv](../articles/azure-functions/functions-scale.md)** | A tárhelycsomag neve | A kiszolgáló nélküli csomag konfigurálásához válassza az **Új** lehetőséget. Ügyeljen arra, hogy a **Felhasználás** csoportban válassza a **Felhasználás lehetőséget.** Amikor a projektet egy [felhasználási tervben](../articles/azure-functions/functions-scale.md#consumption-plan)futó függvényalkalmazásban teszi közzé, csak a függvényalkalmazás végrehajtásáért kell fizetnie. Más tárhelytervek magasabb költségekkel járnak. Ha nem **a Felhasználás**tervben futtat, akkor a függvényalkalmazás méretezését kell [kezelnie.](../articles/azure-functions/functions-scale.md)  |
    | **Helyen** | Az alkalmazásszolgáltatás helye | Válasszon **egy helyet** egy önhöz közeli [régióban,](https://azure.microsoft.com/regions/) vagy más szolgáltatásokat, amelyekhez a funkciók hozzáférhetnek. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Általános célú tárfiók | A Functions futásidejű Azure Storage-fiók szükséges. Válassza az **Új** lehetőséget az általános célú tárfiók konfigurálásához. Kiválaszthat egy meglévő fiókot is, amely megfelel a [tárfiók követelményeinek.](../articles/azure-functions/functions-scale.md#storage-account-requirements)  |

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Válassza **a Létrehozás** lehetőséget, ha ezekkel a beállításokkal létrehozhat egy függvényalkalmazást és a hozzá kapcsolódó erőforrásokat az Azure-ban, és üzembe szeretné helyezni a függvényprojekt-kódot. 

6. Válassza a Közzététel lehetőséget, és a központi telepítés befejezése után jegyezze fel a **webhely URL-értékét,** amely a függvényalkalmazás címe az Azure-ban.

    ![Sikeres közzétételt jelző üzenet](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
