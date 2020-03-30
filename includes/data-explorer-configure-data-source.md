---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78305016"
---
## <a name="configure-the-data-source"></a>Az adatforrás konfigurálása

Az alábbi lépéseket hajthatja végre az Azure Data Explorer adatforrásként való konfigurálásához az irányítópult-eszközhöz. Az alábbi lépéseket részletesebben ismertetjük ebben a szakaszban:

1. Hozzon létre egy Azure Active Directory (Azure AD) egyszerű szolgáltatás. Az egyszerű szolgáltatás az irányítópult-eszköz által az Azure Data Explorer szolgáltatás eléréséhez.

1. Adja hozzá az Azure AD szolgáltatás egyszerű a *megtekintők* szerepkör az Azure Data Explorer adatbázisban.

1. Adja meg az irányítópult-eszköz kapcsolatának tulajdonságait az Azure AD egyszerű szolgáltatásának adatai alapján, majd tesztelje a kapcsolatot.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Létrehozhatja az egyszerű szolgáltatásaz [Azure Portalon](#azure-portal) vagy az [Azure CLI parancssori](#azure-cli) élmény használatával. Függetlenül attól, hogy melyik módszert használja, a létrehozás után négy kapcsolattulajdonság értékeit kapja meg, amelyeket a későbbi lépésekben fog használni.

#### <a name="azure-portal"></a>Azure portál

1. Az egyszerű szolgáltatás létrehozásához kövesse az [Azure Portal dokumentációjában](/azure/active-directory/develop/howto-create-service-principal-portal)található utasításokat.

    1. Az [alkalmazás hozzárendelése egy szerepkörhöz](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) szakaszban rendeljen egy **szerepkörtípusa Reader** az Azure Data Explorer-fürthöz.

    1. A [Bejelentkezési értékek bekerülése szakaszban](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) másolja a következő lépésekben szereplő három tulajdonságértéket: **Címtárazonosító** (bérlőazonosító), **alkalmazásazonosító**és **jelszó**.

1. Az Azure Portalon válassza **az Előfizetések,** majd másolja az azonosítót az előfizetés, amelyben létrehozta a szolgáltatás egyszerű.

    ![Előfizetés-azonosító - portál](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Hozzon létre egy egyszerű szolgáltatás. Állítsa be a megfelelő hatókört és a szerepkörtípusát. `reader`

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    További információ: [Create an Azure service principal with Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. A parancs a következőhez hasonló eredményhalmazt ad vissza. Másolja a három tulajdonságértéket: **appID**, **jelszó**és **bérlő**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Az előfizetések listájának beszerezése.

    ```azurecli
    az account list --output table
    ```

    Másolja a megfelelő előfizetés-azonosítót.

    ![Előfizetés-azonosító - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Az egyszerű szolgáltatás hozzáadása a megtekintők szerepkörhöz

Most, hogy rendelkezik egy egyszerű szolgáltatással, hozzáadja azt az Azure Data Explorer adatbázis *megtekintőszerepköréhez.* Ezt a feladatot az **Azure Portalon az Engedélyek** csoportban, vagy a **Lekérdezés** csoportban felügyeleti paranccsal hajthatja végre.

#### <a name="azure-portal---permissions"></a>Azure Portal – Engedélyek

1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürt.

1. Az **Áttekintés** szakaszban jelölje ki az adatbázist a StormEvents mintaadatokkal.

    ![Adatbázis kiválasztása](media/data-explorer-configure-data-source/select-database.png)

1. Válassza **az Engedélyek** lehetőséget, majd a **Hozzáadás lehetőséget.**

    ![Adatbázis-engedélyek](media/data-explorer-configure-data-source/database-permissions.png)

1. Az **Adatbázis-engedélyek hozzáadása**csoportban válassza a **Megjelenítő** szerepkört, majd válassza ki az **egyszerű felhasználókat**.

    ![Adatbázis-engedélyek hozzáadása](media/data-explorer-configure-data-source/add-permission.png)

1. Keresse meg a létrehozott egyszerű szolgáltatást. Jelölje ki a tagot, majd **a Kijelölés lehetőséget.**

    ![Engedélyek kezelése az Azure Portalon](media/data-explorer-configure-data-source/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Engedélyek kezelése az Azure Portalon](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Management parancs - Lekérdezés

1. Az Azure Portalon nyissa meg az Azure Data Explorer-fürtöt, és válassza a **Lekérdezés**lehetőséget.

    ![Lekérdezés](media/data-explorer-configure-data-source/query.png)

1. Futtassa a következő parancsot a lekérdezésablakban. Használja az alkalmazásazonosítót és a bérlői azonosítót az Azure Portalon vagy a CLI-n.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    A parancs a következőhez hasonló eredményhalmazt ad vissza. Ebben a példában az első sor az adatbázis egy meglévő felhasználójára, a második pedig az imént hozzáadott egyszerű szolgáltatásra.

    ![Eredményhalmaz](media/data-explorer-configure-data-source/result-set.png)
