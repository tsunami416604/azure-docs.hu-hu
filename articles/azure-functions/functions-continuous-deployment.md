---
title: Azure Functions – folyamatos üzembe helyezés
description: Használja az Azure App Service folyamatos üzembe helyezési funkcióit a függvények közzétételéhez.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277023"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés

Az Azure Functions segítségével folyamatosan telepítheti a kódot a [forrásvezérlési integráció](functions-deployment-technologies.md#source-control)használatával. A forrásvezérlésintegráció lehetővé teszi, hogy egy munkafolyamat, amelyben a kódfrissítés elindítja az Azure-ba való telepítést. Ha most ismerkedik az Azure Functions szolgáltatással, tekintse át az [Azure Functions áttekintését.](functions-overview.md)

A folyamatos üzembe helyezés jó választás olyan projektekhez, ahol több és gyakori hozzájárulást integrál. Ha folyamatos üzembe helyezést használ, egyetlen igazságforrást tart fenn a kódhoz, amely lehetővé teszi a csapatok számára, hogy könnyen együttműködjenek. Az Azure Functions folyamatos üzembe helyezését a következő forráskódhelyekről állíthatja be:

* [Azure-repók](https://azure.microsoft.com/services/devops/repos/)
* [Github](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Az Azure-beli függvények üzembe helyezésének egysége a függvényalkalmazás. A függvényalkalmazás összes függvénye egyszerre van telepítve. Miután engedélyezte a folyamatos üzembe helyezést, a függvénykódhoz való hozzáférés az Azure *Portalon írásvédettként* van konfigurálva, mivel az igazság forrása máshol van beállítva.

## <a name="requirements-for-continuous-deployment"></a>A folyamatos telepítésre vonatkozó követelmények

A folyamatos üzembe helyezés sikeres működéséhez a könyvtárstruktúrának kompatibilisnek kell lennie az Azure Functions által elvárt alapvető mappastruktúrával.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A folyamatos üzembe helyezés még nem támogatott a felhasználási csomagon futó Linux-alkalmazások esetében. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Folyamatos üzembe helyezés beállítása

Ha egy meglévő függvényalkalmazás folyamatos központi telepítését szeretné konfigurálni, hajtsa végre az alábbi lépéseket. A lépések bemutatják a GitHub-tárházzal való integrációt, de hasonló lépések vonatkoznak az Azure Repos-okra vagy más forráskód-adattárakra.

1. Az [Azure Portalon](https://portal.azure.com)a függvényalkalmazásban válassza a **Platform szolgáltatások** > **központi telepítését.**

    ![A Központi telepítési központ megnyitása](./media/functions-continuous-deployment/platform-features.png)

2. A **Deployment Centerben**válassza a **GitHub**lehetőséget, majd az **Engedélyezés**lehetőséget. Ha már engedélyezte a GitHubot, válassza a **Folytatás**lehetőséget. 

    ![Azure App Service Deployment Center](./media/functions-continuous-deployment/github.png)

3. A GitHubon válassza az **AzureAppService engedélyezése** gombot. 

    ![Az Azure App Service engedélyezése](./media/functions-continuous-deployment/authorize.png)
    
    Az Azure Portal **Deployment Center** területén válassza a **Continue (Folytatás) lehetőséget.**

4. Válasszon az alábbi buildszolgáltatók közül:

    * **App Service build szolgáltatás:** A legjobb, ha nincs szüksége egy build, vagy ha szüksége van egy általános build.
    * **Azure-folyamatok (előzetes verzió)**: Akkor a legjobb, ha több szabályozásra van szüksége a build felett. Ez a szolgáltató jelenleg előzetes verzióban érhető el.

    ![Buildszolgáltató kiválasztása](./media/functions-continuous-deployment/build.png)

5. A megadott forrásvezérlő beállításra vonatkozó adatok konfigurálása. A GitHub esetében meg kell adnia vagy ki kell választania a **Szervezet,** **a Tárház**és az **Ág**értékeit. Az értékek a kód helyétől függenek. Ezután válassza a **Folytatás gombot.**

    ![A GitHub konfigurálása](./media/functions-continuous-deployment/github-specifics.png)

6. Tekintse át az összes részletet, majd válassza a **Befejezés** lehetőséget a központi telepítési konfiguráció befejezéséhez.

    ![Összefoglalás](./media/functions-continuous-deployment/summary.png)

Amikor a folyamat befejeződött, a megadott forrásból származó összes kód telepítve lesz az alkalmazásba. Ezen a ponton a központi telepítési forrás változásai elindítják a módosítások üzembe helyezését a függvényalkalmazásban az Azure-ban.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Meglévő függvények áthelyezése folyamatos üzembe helyezésre

Ha már írt függvényeket az [Azure Portalon,](https://portal.azure.com) és szeretné letölteni az alkalmazás tartalmát, mielőtt átvált a folyamatos üzembe helyezés, lépjen a funkcióalkalmazás **Áttekintés lapjára.** Válassza az **Alkalmazás tartalmának letöltése** gombot.

![Alkalmazástartalom letöltése](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> A folyamatos integráció konfigurálása után már nem szerkesztheti a forrásfájlokat a Functions portálon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
