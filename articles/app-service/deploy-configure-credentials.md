---
title: Üzembe helyezési hitelesítő adatok konfigurálása
description: Ismerje meg, hogy milyen típusú üzembe helyezési hitelesítő adatok találhatók az Azure App Service-ben, és hogyan konfigurálhatja és használhatja őket.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266077"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Az Azure App Service telepítési hitelesítő adatainak konfigurálása
[Az Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) kétféle hitelesítő adatot támogat a [helyi Git-telepítéshez](deploy-local-git.md) és [az FTP/S telepítéshez.](deploy-ftp.md) Ezek a hitelesítő adatok nem egyeznek meg az Azure-előfizetés hitelesítő adataival.

* **Felhasználói szintű hitelesítő adatok:** egy hitelesítő adatok a teljes Azure-fiókhoz. Az App Service-ben bármely olyan alkalmazáshoz, bármely előfizetésben telepíthető, amelyhez az Azure-fiók rendelkezik hozzáféréssel. Ez az alapértelmezett készlet, amely megjelenik a portál felhasználói felületén (például az alkalmazás [erőforráslapjának](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) **áttekintése** és **tulajdonságai).** Ha egy felhasználó szerepköralapú hozzáférés-vezérléssel (RBAC) vagy társrendszergazdai engedélyekkel kap hozzáférést, a felhasználó a saját felhasználói szintű hitelesítő adatait használhatja, amíg a hozzáférést vissza nem vonják. Ne ossza meg ezeket a hitelesítő adatokat más Azure-felhasználókkal.

* **Alkalmazásszintű hitelesítő adatok:** minden alkalmazáshoz egy hitelesítő adatkészlet tartozik. Csak az adott alkalmazásra telepíthető. Az egyes alkalmazások hitelesítő adatai automatikusan létrejönnek az alkalmazás létrehozásakor. Nem konfigurálhatók manuálisan, de bármikor visszaállíthatók. Ahhoz, hogy egy felhasználó hozzáférést kapjon az alkalmazásszintű hitelesítő adatokhoz (RBAC), a felhasználónak közreműködőnek vagy magasabb szintűnek kell lennie az alkalmazásban (beleértve a Webhelyfelelős beépített szerepkörét is). Az olvasók nem tehetők közzé, és nem férhetnek hozzá ezekhez a hitelesítő adatokhoz.

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Felhasználói szintű hitelesítő adatok konfigurálása

Felhasználói szintű hitelesítő adatokat bármely alkalmazás [erőforráslapján](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)konfigurálhat. Függetlenül attól, hogy melyik alkalmazásban konfigurálja ezeket a hitelesítő adatokat, az az Azure-fiók összes alkalmazására és előfizetésére vonatkozik. 

### <a name="in-the-cloud-shell"></a>A felhőhéjban

A központi telepítési felhasználó konfigurálásához a [Felhőrendszerhéjban](https://shell.azure.com)futtassa az [az webapp központi telepítési felhasználói készlet](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) parancs. Cserélje \<le a \<felhasználónevet> és jelszó> egy központi telepítési felhasználónévre és jelszóra. 

- A felhasználónév egyedinek kell lennie az Azure-ban, és a helyi Git leküldések, nem tartalmazhat a "@" szimbólum. 
- A jelszónak legalább nyolc karakter hosszúnak kell lennie, a következő három elem közül kettővel: betűk, számok és szimbólumok. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A JSON kimenet a `null`jelszót a . `'Conflict'. Details: 409` hibaüzenet esetén változtassa meg a felhasználónevet. `'Bad Request'. Details: 400` hibaüzenet esetén használjon erősebb jelszót. 

### <a name="in-the-portal"></a>A portálon

Az Azure Portalon legalább egy alkalmazással kell rendelkeznie, mielőtt hozzáférhetne a központi telepítési hitelesítő adatok laphoz. A felhasználói szintű hitelesítő adatok konfigurálása:

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali **menüben**az App Services > **\<any_app>**  >  **Telepítési központ** > **FTP-irányítópultja** > **lehetőséget.**

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ha már konfigurálta a Git központi telepítését, válassza **az App Services** > **&lt;any_app>**  > **ftp/credentials** **Deployment center** > lehetőséget.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Válassza **a Felhasználói hitelesítő adatok**lehetőséget, konfigurálja a felhasználónevet és a jelszót, majd válassza a Hitelesítő adatok **mentése**lehetőséget.

Miután beállította a telepítési hitelesítő adatokat, megtalálhatja a *Git* központi telepítési felhasználónevét az alkalmazás **Áttekintés lapján,**

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Ha a Git-telepítés konfigurálva van, a lapon egy **Git/deployment felhasználónév**látható; ellenkező esetben **FTP/központi telepítési felhasználónév**.

> [!NOTE]
> Az Azure nem jeleníti meg a felhasználói szintű központi telepítési jelszót. Ha elfelejti a jelszót, az ebben a szakaszban ismertetett lépésekkel alaphelyzetbe állíthatja a hitelesítő adatokat.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Felhasználói szintű hitelesítő adatok használata FTP/FTPS-sel

FTP/FTPS-végpontra hitelesítés felhasználói szintű hitelesítő adatokkal a következő formátumban igénylő felhasználónevet igényel:`<app-name>\<user-name>`

Mivel a felhasználói szintű hitelesítő adatok a felhasználóhoz vannak kapcsolva, és nem egy adott erőforráshoz, a felhasználónévnek ebben a formátumban kell lennie, hogy a bejelentkezési műveletet a megfelelő alkalmazásvégpontra irányítsa.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Alkalmazásszintű hitelesítő adatok beésése és alaphelyzetbe állítása
Az alkalmazásszintű hitelesítő adatok beszerezése:

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali **menüben** > az App Services**&lt;any_app>**  > **FTP/Credentials** **Deployment center** > lehetőséget.

2. Válassza **az Alkalmazáshitelesítő adatok**lehetőséget, majd a **Felhasználónév** vagy jelszó másolásához válassza a Másolás hivatkozást.

Az alkalmazásszintű hitelesítő adatok alaphelyzetbe állításához válassza a **Hitelesítő adatok visszaállítása** lehetőséget ugyanebben a párbeszédpanelen.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja ezeket a hitelesítő adatokat az alkalmazás [helyi Git-ből](deploy-local-git.md) vagy FTP/S használatával történő üzembe [helyezéséhez.](deploy-ftp.md)
