---
title: Az Azure Functions – folyamatos üzembe helyezés |} A Microsoft Docs
description: A folyamatos üzembe helyezés eszközökkel, az Azure App Service használatával elvégezhető a közzététel a függvényeket.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068397"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés

Az Azure Functions lehetővé teszi a folyamatos és kód üzembe helyezése [verziókövetés integrálása](functions-deployment-technologies.md#source-control). Ez lehetővé teszi egy munkafolyamatot, ahol a kód frissíti az eseményindító üzembe helyezés az Azure-bA. Ha most ismerkedik az Azure Functions, – első lépések a [Azure Functions áttekintő](functions-overview.md).

Folyamatos üzembe helyezés egy remek választás hol vagyunk integrálása több és a hozzájárulások gyakran használják. Lehetővé teszi egyetlen adatforrás a függvénykódban hiteles karbantartása. Folyamatos üzembe helyezés az Azure Functions a következő forrás kód helyekről konfigurálható:

* [Az Azure-Adattárakkal](https://azure.microsoft.com/services/devops/repos/)
* [GitHubon](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Az Azure functions üzembe helyezés mértékegysége a függvényalkalmazást. Ez azt jelenti, hogy a függvényalkalmazás a függvények telepítve vannak-e egy időben. Folyamatos üzembe helyezés engedélyezését követően a függvénykódot az Azure Portalon való hozzáférés van konfigurálva, *csak olvasható*, mert a forrás hiteles máshol kell van beállítva.

## <a name="requirements-for-continuous-deployment"></a>Folyamatos üzembe helyezési követelményei

A folyamatos üzembe helyezés befejeződik, a könyvtárstruktúra kompatibilisnek kell lennie a következő alapvető gyökérmappa-szerkezetében, amelyeket az Azure Functions vár:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Folyamatos üzembe helyezés beállítása

Ezzel az eljárással meglévő függvényalkalmazással – folyamatos üzembe helyezés konfigurálása. Ezeket a lépéseket egy GitHub-tárházat az integráció bemutatásához, de hasonló lépésekkel az Azure-Adattárakkal vagy más forráskódtárházak érvényesek.

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **telepítőközpontot**.

    ![Az üzembe helyezési központ megnyitása](./media/functions-continuous-deployment/platform-features.png)

2. Az a **üzembe helyezési központ**válassza **GitHub** a **verziókövetés** > **engedélyezés**.

    ![Üzembe helyezési központ](./media/functions-continuous-deployment/github.png)

3. Válassza ki **AzureAppService engedélyezése** > **továbbra is**.

    ![Engedélyező](./media/functions-continuous-deployment/authorize.png)

4. A következő build szolgáltatók közül választhat:

    * **Az App Service-szolgáltatás készítése** – ajánlott, ha már nincs szükség a build, vagy ha egy általános hozhat létre.
    * **Az Azure folyamatok (előzetes verzió)** – ajánlott, amikor szüksége van a build jobban szabályozhatja. Ez a szolgáltató jelenleg előzetes verzióban érhető el.

    ![A build-szolgáltató kiválasztása](./media/functions-continuous-deployment/build.png)

5. A megadott forrásvezérlési lehetőségét-re vonatkozó információk konfigurálása. A GitHub, meg kell adnia a **szervezet**, **tárház**, és **ág** ahol a kód él. Ezután válassza ki **Folytatás**.

    ![GitHub konfigurálása](./media/functions-continuous-deployment/github-specifics.png)

6. Végül tekintse át az összes részletes adatait, és válassza ki **Befejezés** az üzembe helyezési konfiguráció befejezéséhez.

    ![Összefoglalás](./media/functions-continuous-deployment/summary.png)

A folyamat befejeződése után a megadott forrás minden kód üzembe helyezése az alkalmazáshoz. Ezen a ponton a módosításokat a központi telepítési forrás aktiválása ezeket a módosításokat a függvényalkalmazáshoz az Azure-beli üzembe helyezése.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Helyezze át a meglévő funkciók folyamatos üzembe helyezés

Ha korábban már beírt függvények a [az Azure portal](https://portal.azure.com) és az alkalmazás tartalmának letöltéséhez a folyamatos üzembe helyezés váltása előtt, akkor kell megkeresnie a **áttekintése** a függvény lapon az alkalmazás, és kattintson a **alkalmazás tartalmának letöltése** gombra.

![Alkalmazás tartalmának letöltése](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Miután beállította a folyamatos integráció, a forrásfájlokat, a Functions portálon már nem szerkesztheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
