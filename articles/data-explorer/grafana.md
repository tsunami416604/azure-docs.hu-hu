---
title: A Grafana az Azure Data Explorer adatainak megjelenítése
description: Ebben az útmutatóban megismerheti, hogyan állítsa be az Azure Data Explorer adatforrásként a Grafana, és ezután megjelenítheti egy minta-fürtből származó adatokat.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 5a9684605de9af1cd9006810d595ae846db01661
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975157"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>A Grafana az Azure Data Explorer adatainak megjelenítése

Grafana egy analitikai platform, amely lehetővé teszi, hogy a lekérdezési adatok megjelenítése, majd hozzon létre és oszthat meg irányítópultokat, a Vizualizációk alapján. Grafana biztosít az Azure Data Explorer *beépülő modul*, amely lehetővé teszi, hogy csatlakozik, és az Azure Data Explorer adatainak megjelenítése. Ebből a cikkből megismerheti, hogyan állítsa be az Azure Data Explorer adatforrásként a Grafana, és ezután megjelenítheti egy minta-fürtből származó adatokat.

## <a name="prerequisites"></a>Előfeltételek

Ez annak a következőkre lesz szüksége:

* [Grafana 5.3.0-s vagy újabb](http://docs.grafana.org/installation/) az operációs rendszer

* A [Azure adatkezelő beépülő modul](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) Grafana számára

* A StormEvents mintaadatokat tartalmazó fürt. További információkért lásd: [a rövid útmutató: Azure Data Explorer fürt és -adatbázis létrehozása](create-cluster-database-portal.md) és [mintaadatok betöltése az Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Az adatforrás konfigurálása

Konfigurálása az Azure Data Explorer adatforrásként Grafana az alábbi lépéseket fogja végrehajtani. Ezeket a lépéseket ebben a szakaszban részletesen ismertetjük:

1. Szolgáltatásnév létrehozása az Azure Active Directory (Azure AD). Egyszerű szolgáltatás Grafana használják az Azure Data Explorer szolgáltatás eléréséhez.

1. Adja hozzá a az Azure AD szolgáltatásnevet a *megtekintők* az Azure Data Explorer adatbázis szerepköréhez.

1. Adja meg az Azure AD egyszerű szolgáltatás származó információk alapján a Grafana csatlakozási tulajdonságokat, majd tesztelje a kapcsolatot.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Akkor is az egyszerű szolgáltatás létrehozása a a [az Azure portal](#azure-portal) vagy a [Azure CLI-vel](#azure-cli) parancssori felületet. Függetlenül attól, melyik módszert használja, négy csatlakozási tulajdonságokat fogja használni a későbbi lépések értékeit kap a létrehozása után.

#### <a name="azure-portal"></a>Azure Portal

1. Az egyszerű szolgáltatás létrehozásához kövesse az utasításokat a [Azure portal dokumentációja](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Az a [alkalmazások szerepkörhöz rendeléséhez](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszban, rendelheti hozzá a szerepkör **olvasó** az Azure Data Explorer fürthöz.

    1. Az a [értékek beolvasása bejelentkezés](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) részen ismertetett lépések három tulajdonságot kimásolhatja: **címtár-azonosító** (bérlőazonosító), **Alkalmazásazonosító**, és  **Jelszó**.

1. Az Azure Portalon válassza ki a **előfizetések** majd másolja ki az előfizetést, amelyben létrehozta az egyszerű szolgáltatás azonosítója.

    ![Előfizetés-azonosító – portál](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Hozzon létre egy egyszerű szolgáltatást. Állítsa be a megfelelő hatókört és a egy szerepkör típusát `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    További információkért lásd: [egy Azure-beli szolgáltatásnév létrehozása az Azure CLI-vel](/cli/azure/create-an-azure-service-principal-azure-cli).

1. A parancs beállítása a következőhöz hasonló eredményt adja vissza. Másolja a három tulajdonságértékek: **appID**, **jelszó**, és **bérlői**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Az előfizetések listájának beolvasása.

    ```azurecli
    az account list --output table
    ```

    Másolja ki a megfelelő előfizetés-azonosítójára.

    ![Előfizetés-azonosító – CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adja hozzá az egyszerű szolgáltatás a megtekintők szerepkörhöz

Most, hogy egy egyszerű szolgáltatást, akkor adja hozzá a *megtekintők* az Azure Data Explorer adatbázis szerepköréhez. A feladat végrehajtásához használhatja **engedélyek** az Azure Portalon, vagy a **lekérdezés** kezelés-paranccsal.

#### <a name="azure-portal---permissions"></a>Az Azure portal - engedélyek

1. Az Azure Portalon nyissa meg az Azure Data Explorer fürt.

1. Az a **áttekintése** területen válassza ki az adatbázist a StormEvents mintaadatokkal.

    ![adatbázis kiválasztása](media/grafana/select-database.png)

1. Válassza ki **engedélyek** majd **hozzáadása**.

    ![Adatbázis-engedélyek](media/grafana/database-permissions.png)

1. Alatt **adja hozzá az adatbázis-engedélyek**, jelölje be a **megjelenítő** szerepkör majd **válassza ki a rendszerbiztonsági tagok**.

    ![Adatbázis-engedélyek hozzáadása](media/grafana/add-permission.png)

1. Keresse meg a létrehozott egyszerű szolgáltatás (a példa bemutatja, az egyszerű **mb-grafana**). Válassza ki azokat az egyszerű **kiválasztása**.

    ![Az Azure Portalon engedélyek kezelése](media/grafana/new-principals.png)

1. Kattintson a **Mentés** gombra.

    ![Az Azure Portalon engedélyek kezelése](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Felügyeleti parancs - lekérdezés

1. Az Azure Portalon nyissa meg az Azure Data Explorer fürt, és válassza ki **lekérdezés**.

    ![Lekérdezés](media/grafana/query.png)

1. A lekérdezési ablakban futtassa a következő parancsot. Az Alkalmazásazonosítót használhatja, és a bérlő Azonosítóját az Azure Portalon vagy a parancssori felület.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    A parancs beállítása a következőhöz hasonló eredményt adja vissza. Ebben a példában az első sor van egy meglévő felhasználó az adatbázisban, és a második sor szolgáltatásnév számára újonnan hozzáadott.

    ![Eredményhalmaz](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Adja meg a tulajdonságait, és tesztelje a kapcsolatot

A szolgáltatásnévhez hozzárendelt együtt a *megtekintők* szerepkör, most már a Grafana példányát határozza meg a tulajdonságait, és tesztelje a kapcsolatot az Adatkezelőbe az Azure.

1. A Grafana, a bal oldali menüben kattintson a fogaskerék ikonra majd **adatforrások**.

    ![Adatforrások](media/grafana/data-sources.png)

1. Válassza ki **adatforrás hozzáadása**.

1. Az a **adatforrások / új** lapon adja meg az adatforrás nevét, majd válassza ki a **Azure Data Explorer Datasource**.

    ![Kapcsolat neve és típusa](media/grafana/connection-name-type.png)

1. Adja meg a fürt nevére a képernyő https://{ClusterName}. {Régió: region}. kusto.windows.net. Az Azure portal vagy a parancssori felület a többi értéket adjon meg. A következő táblázat ismerteti a következő képen a hozzárendelés.

    ![Kapcsolat tulajdonságai](media/grafana/connection-properties.png)

    | Grafana felhasználói felület | Azure Portal | Azure CLI |
    | --- | --- | --- |
    | Előfizetési azonosító | ELŐFIZETÉS AZONOSÍTÓJA | SubscriptionId |
    | Bérlő azonosítója | Címtár azonosítója | bérlő |
    | Ügyfél-azonosító | Alkalmazásazonosító | appId |
    | Titkos ügyfélkulcs | Jelszó | jelszó |
    | | | |

1. Válassza ki **mentés és tesztelés**.

    Ha a teszt sikeres, folytassa a következő szakaszban. Ha bármilyen problémát tapasztal, ellenőrizze a Grafana, és tekintse át az előző lépésekben megadott értékeket.

## <a name="visualize-data"></a>Adatok vizualizációja

Most már végzett konfigurálása az Azure Data Explorer adatforrásként a Grafana, ideje adatainak megjelenítéséhez. Bemutatunk egy egyszerű példa, de további sok teheti. Javasoljuk, hogy megnézi [lekérdezéseket írhat az Azure Data Explorer](write-queries.md) példa más lekérdezéseket a minta adatkészlet futtatásához.

1. Grafana, a bal oldali menüben válassza a plusz ikonra majd **irányítópult**.

    ![irányítópult létrehozása](media/grafana/create-dashboard.png)

1. Alatt a **Hozzáadás** lapon jelölje be **Graph**.

    ![Gráf hozzáadása](media/grafana/add-graph.png)

1. A gráf panelen válassza ki **Panel címe** majd **szerkesztése**.

    ![Panel szerkesztése](media/grafana/edit-panel.png)

1. A panel alján válassza **adatforrás** válassza ki a konfigurált adatforrás.

    ![Adatforrás kiválasztása](media/grafana/select-data-source.png)

1. A következő lekérdezést a lekérdezési ablakban másolja, majd válassza ki **futtatása**. A lekérdezésben az események száma időtartamgyűjtők minta adatkészlet naponta.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![A lekérdezés futtatása](media/grafana/run-query.png)

1. A graph nem jelenik meg az eredményt, mivel azt hatókörét alapértelmezés szerint az adatok az elmúlt hat óra. A felső menüben válassza ki a **utolsó 6 óra**.

    ![Utolsó 6 óra](media/grafana/last-six-hours.png)

1. Adjon meg egy egyéni tartományt, amely lefedi 2007, az év a StormEvents minta adatkészlet szerepel. Kattintson az **Alkalmaz** gombra.

    ![Egyéni tartomány](media/grafana/custom-date-range.png)

    A gráf mostantól 2007, naponta bucketed adatait jeleníti meg.

    ![Graph elkészült](media/grafana/finished-graph.png)

1. A felső menüben válassza a Mentés ikonra: ![Mentés ikonra](media/grafana/save-icon.png).

## <a name="next-steps"></a>További lépések

[Lekérdezések írása az Azure Data Explorerhez](write-queries.md)

[Oktatóanyag: Azure Data Explorer a Power bi-ban adatainak megjelenítése](visualize-power-bi.md)