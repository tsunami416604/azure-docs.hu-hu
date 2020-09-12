---
title: Üzembe helyezési hitelesítő adatok konfigurálása
description: Megtudhatja, milyen típusú központi telepítési hitelesítő adatok vannak Azure App Serviceban, és hogyan konfigurálhatja és használhatja azokat.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 739325f66594667c6973df356e2bcf26a3eb056d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300272"
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

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure App Services központi telepítési központból.](./media/app-service-deployment-credentials/access-no-git.png)

    Ha már konfigurálta a git-telepítést, válassza a **app Services**  >  ** &lt; any_app>**  >  **központi telepítési központ**  >  **FTP/hitelesítő adatokat**.

    ![Bemutatja, hogyan választhatja ki az FTP-irányítópultot az Azure App Services üzembe helyezési központból a beállított git-telepítéshez.](./media/app-service-deployment-credentials/access-with-git.png)

2. Válassza ki a **felhasználói hitelesítő adatokat**, konfigurálja a felhasználónevet és a jelszót, majd válassza a **hitelesítő adatok mentése**lehetőséget.

Miután beállította az üzembe helyezési hitelesítő adatokat, megkeresheti a *git* üzembe helyezési felhasználónevet az alkalmazás **Áttekintés** lapján.

![Bemutatja, hogyan keresheti meg a git üzembe helyezési felhasználónevet az alkalmazás Áttekintés oldalán.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Ha a git üzembe helyezése be van állítva, akkor a lap a **git/Deployment felhasználónevet**jeleníti meg; egyéb esetben egy **FTP-/telepítési Felhasználónév**.

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói szintű központi telepítési jelszót. Ha elfelejti a jelszót, az ebben a szakaszban ismertetett lépéseket követve alaphelyzetbe állíthatja a hitelesítő adatait.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Felhasználói szintű hitelesítő adatok használata FTP/FTPS

Hitelesítés egy FTP-/FTPS-végponthoz felhasználói szintű hitelesítő adatokkal, amelyek a felhasználónevet a következő formátumban használják: `<app-name>\<user-name>`

Mivel a felhasználói szintű hitelesítő adatok a felhasználóhoz kapcsolódnak, nem pedig egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie ahhoz, hogy a bejelentkezési műveletet a megfelelő alkalmazás-végpontra irányítsa.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Alkalmazás szintű hitelesítő adatok beolvasása és alaphelyzetbe állítása
Az alkalmazás szintű hitelesítő adatok beszerzése:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **app Services**  >  ** &lt; any_app>**  >  **központi telepítési központ**  >  **FTP/hitelesítő adatok**lehetőséget.

2. Válassza ki az **alkalmazás hitelesítő adatait**, és a **Másolás** hivatkozásra kattintva másolja a felhasználónevet vagy a jelszót.

Az alkalmazás szintű hitelesítő adatok alaphelyzetbe állításához válassza a **hitelesítő adatok alaphelyzetbe állítása** ugyanabban a párbeszédablakban lehetőséget.

## <a name="disable-basic-authentication"></a>Alapszintű hitelesítés letiltása

Néhány szervezetnek meg kell felelnie a biztonsági követelményeknek, és inkább le kell tiltania az FTP-n vagy a webtelepítésen keresztüli hozzáférést Így a szervezet tagjai csak az Azure Active Directory (Azure AD) által vezérelt API-kkal érhetik el a App Services.

### <a name="ftp"></a>FTP

Ha le szeretné tiltani az FTP-hozzáférést a helyhez, futtassa az alábbi CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoport és a webhely nevére. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak ellenőrzéséhez, hogy az FTP-hozzáférés le van-e tiltva, megpróbálhat hitelesítést végezni egy FTP-ügyfél, például a FileZilla használatával. A közzétételi hitelesítő adatok lekéréséhez lépjen a webhely áttekintés paneljére, és kattintson a közzétételi profil letöltése elemre. A hitelesítéshez használja a fájl FTP-állomásnevét, felhasználónevét és jelszavát, és egy 401-es hibaüzenetet kap, amely azt jelzi, hogy Ön nem jogosult.

### <a name="webdeploy-and-scm"></a>Webdeploy és SCM

Ha le szeretné tiltani az alapszintű hitelesítési hozzáférést a webtelepítési porthoz és az SCM-helyhez, futtassa az alábbi CLI-parancsot. Cserélje le a helyőrzőket az erőforráscsoport és a webhely nevére. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Annak ellenőrzéséhez, hogy a közzétételi profil hitelesítő adatai le vannak-e tiltva a webtelepítésben, próbáljon meg [közzétenni egy webalkalmazást a Visual Studio 2019 használatával](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

### <a name="disable-access-to-the-api"></a>Az API elérésének letiltása

Az előző szakaszban található API egy Azure szerepköralapú Access Control (RBAC), ami azt jelenti, hogy [létrehozhat egy egyéni szerepkört](https://docs.microsoft.com/azure/role-based-access-control/custom-roles#steps-to-create-a-custom-role) , és alacsonyabb priveldged felhasználókat rendelhet hozzá a szerepkörhöz, így az alapszintű hitelesítés nem engedélyezhető egyetlen helyen sem. Az egyéni szerepkör konfigurálásához [kövesse az alábbi utasításokat](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

A [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) használatával is naplózhatja a sikeres hitelesítési kérelmeket, és a [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) használatával kényszerítheti ki ezt a konfigurációt az előfizetésben található összes webhelyre.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás [helyi git](deploy-local-git.md) -ből való üzembe helyezéséhez, vagy [FTP/S](deploy-ftp.md)használatával.
