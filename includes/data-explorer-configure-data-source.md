---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305016"
---
## <a name="configure-the-data-source"></a>Az adatforrás konfigurálása

A következő lépések végrehajtásával konfigurálhatja az Azure Adatkezelő-t az irányítópult-eszköz adatforrásaként. Ezeket a lépéseket részletesebben ismertetjük ebben a szakaszban:

1. Hozzon létre egy Azure Active Directory (Azure AD) egyszerű szolgáltatást. A szolgáltatásnevet az irányítópult-eszköz használja az Azure Adatkezelő szolgáltatás eléréséhez.

1. Adja hozzá az Azure AD egyszerű szolgáltatást az Azure Adatkezelő-adatbázis *megjelenítői* szerepköréhez.

1. Adja meg az irányítópult-eszköz kapcsolatok tulajdonságait az Azure AD egyszerű szolgáltatásnév adatai alapján, majd tesztelje a kapcsolatokat.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatásnév a [Azure Portalben](#azure-portal) vagy az [Azure CLI](#azure-cli) parancssori felületének használatával hozható létre. Függetlenül attól, hogy melyik módszert használja, a létrehozás után négy, a későbbi lépésekben használni kívánt kapcsolattípus-tulajdonság értékét kapja meg.

#### <a name="azure-portal"></a>Azure Portal

1. Az egyszerű szolgáltatás létrehozásához kövesse az [Azure Portal dokumentációjának](/azure/active-directory/develop/howto-create-service-principal-portal)utasításait.

    1. Az [alkalmazás társítása szerepkörhöz](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) szakaszban rendeljen hozzá egy **olvasó** szerepkör-típust az Azure adatkezelő-fürthöz.

    1. A [beolvasási értékek beolvasása](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszban másolja a következő három tulajdonság értékét: **könyvtár-azonosító** (bérlői azonosító), **alkalmazás-azonosító**és **jelszó**.

1. A Azure Portal válassza az **előfizetések** elemet, majd másolja ki annak az előfizetésnek az azonosítóját, amelyben létrehozta a szolgáltatásnevet.

    ![Előfizetés azonosítója – portál](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Egyszerű szolgáltatásnév létrehozása. Állítson be egy megfelelő hatókört és egy szerepkör-típust `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    További információ: Azure-beli [szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

1. A parancs az alábbihoz hasonló eredményhalmazt ad vissza. Másolja a három tulajdonság értékét: **appID**, **Password**és **bérlő**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Az előfizetések listájának beolvasása.

    ```azurecli
    az account list --output table
    ```

    Másolja a megfelelő előfizetés-azonosítót.

    ![Előfizetés azonosítója – parancssori felület](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Szolgáltatásnév hozzáadása a megjelenítők szerepkörhöz

Most, hogy rendelkezik egy egyszerű szolgáltatással, adja hozzá a *megjelenítői* szerepkörhöz az Azure adatkezelő adatbázisban. Ezt a feladatot a Azure Portal **engedélyei** alatt, vagy a **lekérdezés** alatt, a felügyeleti parancs használatával hajthatja végre.

#### <a name="azure-portal---permissions"></a>Azure Portal – engedélyek

1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt.

1. Az **Áttekintés** szakaszban válassza ki a StormEvents tartalmazó adatbázist.

    ![Adatbázis kiválasztása](media/data-explorer-configure-data-source/select-database.png)

1. Válassza az **engedélyek** lehetőséget, majd **adja hozzá**a elemet.

    ![Adatbázis-engedélyek](media/data-explorer-configure-data-source/database-permissions.png)

1. Az **adatbázis-engedélyek hozzáadása**területen válassza ki a **megjelenítői** szerepkört, majd **válassza a rendszerbiztonsági tag elemet**.

    ![Adatbázis-engedélyek hozzáadása](media/data-explorer-configure-data-source/add-permission.png)

1. Keresse meg az Ön által létrehozott szolgáltatásnevet. Válassza ki a résztvevőt, majd **válassza a elemet**.

    ![Engedélyek kezelése a Azure Portalban](media/data-explorer-configure-data-source/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Engedélyek kezelése a Azure Portalban](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Felügyeleti parancs – lekérdezés

1. A Azure Portal nyissa meg az Azure Adatkezelő-fürtöt, és válassza a **lekérdezés**lehetőséget.

    ![Lekérdezés](media/data-explorer-configure-data-source/query.png)

1. Futtassa a következő parancsot a lekérdezési ablakban. Használja az alkalmazás AZONOSÍTÓját és a bérlő AZONOSÍTÓját a Azure Portal vagy a parancssori felületről.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    A parancs az alábbihoz hasonló eredményhalmazt ad vissza. Ebben a példában az első sor az adatbázis egy meglévő felhasználója, a második sor pedig az imént hozzáadott egyszerű szolgáltatásnév.

    ![Eredményhalmaz](media/data-explorer-configure-data-source/result-set.png)
