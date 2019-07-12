---
title: Az Azure Functions – folyamatos üzembe helyezés |} A Microsoft Docs
description: Az Azure App Service folyamatos üzembe helyezés funkcióit használja a függvények közzétételéhez.
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594537"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions – folyamatos üzembe helyezés

Az Azure Functions segítségével folyamatos üzembe helyezés a kód használatával [verziókövetés integrálása](functions-deployment-technologies.md#source-control). Forráskezelés integrálása tesz lehetővé, amelyben a kód frissítése aktivál az üzembe helyezés az Azure-bA. Ha most ismerkedik az Azure Functions, első lépésként tekintse át a [Azure Functions áttekintő](functions-overview.md).

Folyamatos üzembe helyezés egy jó választás, több integrálása és a hozzájárulások gyakran használják. Folyamatos üzembe helyezés használatakor a kódban, amely lehetővé teszi, hogy a munkacsoportok számára könnyen karbantartása egy egyetlen hiteles forrásaként. Folyamatos üzembe helyezés az Azure Functions a következő forrás kód helyekről konfigurálható:

* [Az Azure-Adattárakkal](https://azure.microsoft.com/services/devops/repos/)
* [GitHubon](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Az Azure functions üzembe helyezés mértékegysége a függvényalkalmazást. A függvényalkalmazás a függvények egy időben vannak telepítve. Miután engedélyezte a folyamatos üzembe helyezés, a függvény kódját az Azure Portalon való hozzáférés van konfigurálva, *csak olvasható* , mert a forrás hiteles van beállítva, máshol.

## <a name="requirements-for-continuous-deployment"></a>Folyamatos üzembe helyezési követelményei

A folyamatos üzembe helyezés befejeződik a könyvtárstruktúra, amelyeket az Azure Functions vár alapszintű mappastruktúra kompatibilisnek kell lennie.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Folyamatos üzembe helyezés beállítása

Folyamatos üzembe helyezés egy meglévő függvényalkalmazás konfigurálásához hajtsa végre ezeket a lépéseket. A lépések bemutatják egy GitHub-adattár-integráció, de hasonló lépéseket követve az Azure-Adattárakkal vagy más forráskódtárházak érvényesek.

1. A függvényalkalmazásban, az a [az Azure portal](https://portal.azure.com)válassza **platformfunkciók** > **Telepítőközpontot**.

    ![Üzembe helyezési központ megnyitása](./media/functions-continuous-deployment/platform-features.png)

2. A **üzembe helyezési központ**válassza **GitHub**, majd válassza ki **engedélyezés**. Ha a GitHub már Ön által engedélyezett, jelölje be **Folytatás**. 

    ![Azure App Service Deployment Center](./media/functions-continuous-deployment/github.png)

3. A github webhelyen, jelölje ki a **engedélyezése AzureAppService** gombra. 

    ![Engedélyezze az Azure App Service-ben](./media/functions-continuous-deployment/authorize.png)
    
    A **üzembe helyezési központ** az Azure Portalon válassza ki a **Folytatás**.

4. A következő build szolgáltatók közül választhat:

    * **Az App Service-szolgáltatás készítése**: Ajánlott, ha már nincs szükség a build, vagy ha egy általános hozhat létre.
    * **Az Azure folyamatok (előzetes verzió)** : Ha a build több kell szabályozhatóbbá ajánlott. Ez a szolgáltató jelenleg előzetes verzióban érhető el.

    ![Válasszon ki egy build-szolgáltatót](./media/functions-continuous-deployment/build.png)

5. A megadott forrásvezérlési lehetőségét-re vonatkozó információk konfigurálása. A GitHub, adja meg vagy válassza ki az értékeket **szervezet**, **tárház**, és **ág**. Az értékeket a kód helyét alapulnak. Ezután válassza ki **Folytatás**.

    ![Konfigurálja a Githubon](./media/functions-continuous-deployment/github-specifics.png)

6. Tekintse át az összes részletes adatait, és válassza ki **Befejezés** az üzembe helyezési konfiguráció befejezéséhez.

    ![Összegzés](./media/functions-continuous-deployment/summary.png)

Amikor a folyamat befejeződött, a megadott forrás minden kód üzembe helyezése az alkalmazáshoz. Ezen a ponton a módosításokat a központi telepítési forrás aktiválása ezeket a módosításokat a függvényalkalmazáshoz az Azure-beli üzembe helyezése.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Helyezze át a meglévő funkciók folyamatos üzembe helyezés

Ha korábban már beírt funkciók a [az Azure portal](https://portal.azure.com) szeretne, folyamatos üzembe helyezés, mielőtt, töltse le az alkalmazás tartalmát, és nyissa meg a **áttekintése** a függvényalkalmazás lapján. Válassza ki a **alkalmazás tartalmának letöltése** gombra.

![Alkalmazás tartalmának letöltése](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Miután beállította a folyamatos integráció, a forrásfájlokat, a Functions portálon már nem szerkesztheti.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – ajánlott eljárások](functions-best-practices.md)
