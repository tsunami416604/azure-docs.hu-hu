---
title: Üzembe helyezési hitelesítő adatok konfigurálása
description: Megtudhatja, milyen típusú központi telepítési hitelesítő adatok vannak Azure App Serviceban, és hogyan konfigurálhatja és használhatja azokat.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83649116"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Központi telepítési hitelesítő adatok konfigurálása Azure App Servicehoz
A [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) kétféle hitelesítő adatot támogat a [helyi git üzembe helyezéséhez](deploy-local-git.md) és az [FTP/S](deploy-ftp.md)környezethez. Ezek a hitelesítő adatok nem egyeznek meg az Azure-előfizetés hitelesítő adataival.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Felhasználói szintű hitelesítő adatok konfigurálása

A felhasználói szintű hitelesítő adatokat bármely alkalmazás [erőforrás-oldalán](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)konfigurálhatja. Függetlenül attól, hogy melyik alkalmazáshoz konfigurálja ezeket a hitelesítő adatokat, az összes alkalmazásra és az Azure-fiókban lévő összes előfizetésre vonatkozik. 

### <a name="in-the-cloud-shell"></a>A Cloud Shell

Ha a [Cloud Shell](https://shell.azure.com)szeretné konfigurálni a központi telepítési felhasználót, futtassa az az [WebApp Deployment User set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancsot. Cserélje \<username> le \<password> a és a értékét egy üzembe helyezési felhasználói felhasználónévre és jelszóra. 

- A felhasználónévnek egyedinek kell lennie az Azure-ban, a helyi git-leküldések esetében pedig nem tartalmazhatja a "@" szimbólumot. 
- A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON-kimenet a jelszót jeleníti meg `null` . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

### <a name="in-the-portal"></a>A portálon

A Azure Portalben legalább egy alkalmazásnak rendelkeznie kell a központi telepítési hitelesítő adatok lap eléréséhez. Felhasználói szintű hitelesítő adatok konfigurálása:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza ki **app Services**  >  **\<any_app>**  >  **központi telepítési központ**  >  **FTP**-  >  **irányítópultot**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ha már konfigurálta a git-telepítést, válassza a **app Services**  >  ** &lt; any_app>**  >  **központi telepítési központ**  >  **FTP/hitelesítő adatokat**.

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

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Alkalmazás szintű hitelesítő adatok beolvasása és alaphelyzetbe állítása
Az alkalmazás szintű hitelesítő adatok beszerzése:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **app Services**  >  ** &lt; any_app>**  >  **központi telepítési központ**  >  **FTP/hitelesítő adatok**lehetőséget.

2. Válassza ki az **alkalmazás hitelesítő adatait**, és a **Másolás** hivatkozásra kattintva másolja a felhasználónevet vagy a jelszót.

Az alkalmazás szintű hitelesítő adatok alaphelyzetbe állításához válassza a **hitelesítő adatok alaphelyzetbe állítása** ugyanabban a párbeszédablakban lehetőséget.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás [helyi git](deploy-local-git.md) -ből való üzembe helyezéséhez, vagy [FTP/S](deploy-ftp.md)használatával.
