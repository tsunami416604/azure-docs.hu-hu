---
title: Központi telepítési hitelesítő adatok konfigurálása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure App Service központi telepítési hitelesítő adatokat.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/14/2019
ms.author: cephalin
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 52bbc907d91dd4bf9066daf14d0d5de1b759b92f
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558565"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Központi telepítési hitelesítő adatok konfigurálása Azure App Servicehoz
A [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) kétféle hitelesítő adatot támogat a [helyi git üzembe helyezéséhez](deploy-local-git.md) és az [FTP/S](deploy-ftp.md)környezethez. Ezek a hitelesítő adatok nem egyeznek meg az Azure-előfizetés hitelesítő adataival.

* **Felhasználói szintű hitelesítő adatok**: a teljes Azure-fiókhoz tartozó hitelesítő adatok egy halmaza. Felhasználható arra, hogy a App Service bármely olyan alkalmazáshoz, amely az Azure-fiókhoz való hozzáféréshez szükséges. Ez az alapértelmezett beállítás, amely a portál grafikus felhasználói felületén található (például az alkalmazás [erőforrás](../azure-resource-manager/manage-resources-portal.md#manage-resources)-oldalának **áttekintése** és **tulajdonságai** ). Ha a felhasználó szerepköralapú Access Control (RBAC) vagy a rendszergazdai jogosultságok használatával kap hozzáférést az alkalmazásokhoz, a felhasználó a hozzáférés visszavonása előtt saját felhasználói szintű hitelesítő adatait használhatja. Ne ossza meg ezeket a hitelesítő adatokat más Azure-felhasználókkal.

* **Alkalmazás szintű hitelesítő adatok**: az egyes alkalmazásokhoz tartozó hitelesítő adatok egy halmaza. Ezt csak az alkalmazásra lehet használni. Az egyes alkalmazások hitelesítő adatai automatikusan létrejönnek az alkalmazás létrehozásakor. Nem konfigurálhatók manuálisan, de bármikor visszaállíthatók. Ahhoz, hogy a felhasználók hozzáférést kapjanak az alkalmazás szintű hitelesítő adatokhoz a (RBAC) használatával, a felhasználónak közreműködőnek vagy magasabbnak kell lennie az alkalmazásban. Az olvasók számára nem engedélyezett a közzététel, és nem férhetnek hozzá ezekhez a hitelesítő adatokhoz.

## <a name="userscope"></a>Felhasználói szintű hitelesítő adatok konfigurálása

A felhasználói szintű hitelesítő adatokat bármely alkalmazás [erőforrás-oldalán](../azure-resource-manager/manage-resources-portal.md#manage-resources)konfigurálhatja. Függetlenül attól, hogy melyik alkalmazáshoz konfigurálja ezeket a hitelesítő adatokat, az összes alkalmazásra és az Azure-fiókban lévő összes előfizetésre vonatkozik. 

### <a name="in-the-cloud-shell"></a>A Cloud Shell

Ha a [Cloud Shell](https://shell.azure.com)szeretné konfigurálni a központi telepítési felhasználót, futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot. Cserélje \<le a Felhasználónév \<> és a jelszó > egy üzembe helyezési felhasználónévvel és jelszóval. 

- A felhasználónév Azure-on belül egyedinek kell lennie, és a helyi git leküldés, nem tartalmazhat a "\@" szimbólummal. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet a jelszót `null`jeleníti meg. `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

### <a name="in-the-portal"></a>A portálon

A Azure Portalben legalább egy alkalmazásnak rendelkeznie kell a központi telepítési hitelesítő adatok lap eléréséhez. Felhasználói szintű hitelesítő adatok konfigurálása:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **app Services** >  **\<any_app >**  > **központi telepítési központ** > **FTP** > -**irányítópult**elemet.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ha már konfigurálta a git-telepítést, válassza a **app Services** >  **&lt;any_app >**  > **központi telepítési központ** > **FTP/hitelesítő adatok**lehetőséget.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Válassza ki a **felhasználói hitelesítő adatokat**, konfigurálja a felhasználónevet és a jelszót, majd válassza a **hitelesítő adatok mentése**lehetőséget.

Miután beállította az üzembe helyezési hitelesítő adatokat, megkeresheti a *git* üzembe helyezési felhasználónevet az alkalmazás **Áttekintés** lapján.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Ha a git üzembe helyezése be van állítva, akkor a lap a **git/Deployment felhasználónevet**jeleníti meg; egyéb esetben egy **FTP-/telepítési Felhasználónév**.

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói szintű központi telepítési jelszót. Ha elfelejti a jelszót, az ebben a szakaszban ismertetett lépéseket követve alaphelyzetbe állíthatja a hitelesítő adatait.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Felhasználói szintű hitelesítő adatok használata FTP/FTPS

Hitelesítés egy FTP-/FTPS-végponthoz felhasználói szintű hitelesítő adatokkal, amelyek a felhasználónevet a következő formátumban használják:`<app-name>\<user-name>`

Mivel a felhasználói szintű hitelesítő adatok a felhasználóhoz kapcsolódnak, nem pedig egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie ahhoz, hogy a bejelentkezési műveletet a megfelelő alkalmazás-végpontra irányítsa.

## <a name="appscope"></a>Alkalmazás szintű hitelesítő adatok beolvasása és alaphelyzetbe állítása
Az alkalmazás szintű hitelesítő adatok beszerzése:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **app Services** >  **&lt;any_app >**  > **központi telepítési központ** > **FTP/hitelesítő adatok**lehetőséget.

2. Válassza ki az **alkalmazás hitelesítő adatait**, és a **Másolás** hivatkozásra kattintva másolja a felhasználónevet vagy a jelszót.

Az alkalmazás szintű hitelesítő adatok alaphelyzetbe állításához válassza a **hitelesítő adatok** alaphelyzetbe állítása ugyanabban a párbeszédablakban lehetőséget.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás [helyi git](deploy-local-git.md) -ből való üzembe helyezéséhez, vagy [FTP/S](deploy-ftp.md)használatával.
