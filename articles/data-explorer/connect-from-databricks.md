---
title: Kapcsolódás az Azure Adatkezelőhoz a Azure Databricks a Python használatával
description: Ebből a témakörből megtudhatja, hogyan használhatja a Azure Databricks található Python-függvénytárat az Adatkezelő Azure-ból származó adatok eléréséhez a két hitelesítési módszer egyikének használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985679"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Kapcsolódás az Azure Adatkezelőhoz a Azure Databricks a Python használatával

A [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) egy Apache Spark-alapú elemzési platform, amely a Microsoft Azure platformra van optimalizálva. Ebből a cikkből megtudhatja, hogyan használhatja a Azure Databricksban található Python-függvénytárat az Azure-Adatkezelő adatainak eléréséhez. Az Azure Adatkezelő több módon is hitelesíthető, beleértve az eszköz felhasználónevét és egy Azure Active Directory (Azure AD) alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Azure adatkezelő-fürtöt és-adatbázist](/azure/data-explorer/create-cluster-database-portal).
- [Hozzon létre egy Azure Databricks munkaterületet](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). A **Azure Databricks szolgáltatás**alatt, a **díjszabási szint** legördülő listában válassza a **prémium**lehetőséget. Ez a beállítás lehetővé teszi, hogy Azure Databricks titkokat használjon a hitelesítő adatok tárolásához, és azok jegyzetfüzetekben és feladatokban való megadásához.

- [Hozzon létre egy fürtöt](https://docs.azuredatabricks.net/user-guide/clusters/create.html) Azure Databricks a következő specifikációkkal (a minta jegyzetfüzetek futtatásához szükséges minimális beállításokkal):

   ![A fürt létrehozására vonatkozó előírások](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>A Python-könyvtár telepítése a Azure Databricks-fürtön

A Python- [könyvtár](/azure/kusto/api/python/kusto-python-client-library) telepítése a Azure Databricks-fürtön:

1. Nyissa meg a Azure Databricks munkaterületet, és [hozzon létre egy könyvtárat](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Python PyPI-csomag vagy Python-tojás feltöltése](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Töltse fel, telepítse és csatolja a könyvtárat a Databricks-fürthöz.
   - Adja meg a PyPi nevét: **Azure-kusto--** .

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Kapcsolódás az Azure Adatkezelőhoz eszköz-bejelentkezés használatával

[Importáljon egy jegyzetfüzetet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) a [query-ADX-Device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notebook használatával. Ezután a hitelesítő adataival csatlakozhat az Azure Adatkezelőhoz.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Kapcsolódás ADX egy Azure AD-alkalmazás használatával

1. Hozzon létre Azure AD-alkalmazást [egy Azure ad-alkalmazás üzembe](/azure/kusto/management/access-control/how-to-provision-aad-app)helyezésével.
1. A következőképpen biztosítson hozzáférést az Azure AD-alkalmazáshoz az Azure Adatkezelő-adatbázisban:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | az adatbázis neve |
    | ```AAD App ID``` | Azure AD-alkalmazás azonosítója |
    | ```AAD Tenant ID``` | Az Azure AD-bérlő azonosítója |

### <a name="find-your-azure-ad-tenant-id"></a>Az Azure AD-bérlő AZONOSÍTÓjának megkeresése

Egy alkalmazás hitelesítéséhez az Azure Adatkezelő az Azure AD-bérlői AZONOSÍTÓját használja. A bérlő AZONOSÍTÓjának megkereséséhez használja a következő URL-címet. Helyettesítse be tartományát a *SajátTartomány*számára.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Válassza ki ezt az URL-címet az eredmények megtekintéséhez. Az első sor a következő: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlő azonosítója `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Az Azure AD-alkalmazás AZONOSÍTÓjának és kulcsának tárolása és biztonságossá tétele 

Az Azure AD-alkalmazás AZONOSÍTÓját és kulcsát az alábbi módon Azure Databricks [Secrets](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) használatával tárolhatja és biztonságossá teheti:
1. [Állítsa be a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)-t.
1. [Telepítse a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)-t. 
1. A [hitelesítés beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurálja a [titkokat](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) a következő minta-parancsok használatával:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Jegyzetfüzet importálása
[Importáljon egy jegyzetfüzetet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) a [query-ADX-HRE-app](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) notebook használatával az Azure Adatkezelőhoz való kapcsolódáshoz. Frissítse a helyőrző értékeket a fürt nevére, az adatbázis nevére és az Azure AD-bérlői AZONOSÍTÓra.
