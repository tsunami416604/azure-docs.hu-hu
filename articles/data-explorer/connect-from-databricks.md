---
title: Csatlakozás az Azure DataBricks-ből az Azure Databricks-ből a Python használatával
description: Ez a témakör bemutatja, hogyan használhatja a Python-kódtár az Azure Databricks adatok eléréséhez az Azure Data Explorer két hitelesítési módszerek használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76985679"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Csatlakozás az Azure DataBricks-ből az Azure Databricks-ből a Python használatával

[Az Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) egy Apache Spark-alapú elemzési platform, amely a Microsoft Azure platformra van optimalizálva. Ez a cikk bemutatja, hogyan használhatja a Python-kódtár az Azure Databricks adatok eléréséhez az Azure Data Explorer. Az Azure Data Explorer használatával többféleképpen is hitelesíthető, például egy eszközbejelentkezés és egy Azure Active Directory (Azure AD) alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Azure Data Explorer-fürtöt és -adatbázist.](/azure/data-explorer/create-cluster-database-portal)
- [Hozzon létre egy Azure Databricks-munkaterületet.](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace) Az **Azure Databricks Szolgáltatás**csoportban a **Díjcsomag** legördülő listában válassza a **Prémium lehetőséget.** Ez a beállítás lehetővé teszi, hogy az Azure Databricks titkos kulcsok használatával tárolja a hitelesítő adatait, és hivatkozzon rájuk a jegyzetfüzetekben és a feladatokban.

- [Hozzon létre egy fürtöt](https://docs.azuredatabricks.net/user-guide/clusters/create.html) az Azure Databricks-ben a következő specifikációkkal (a mintajegyzetfüzetek futtatásához szükséges minimális beállítások):

   ![Fürt létrehozásának műszaki előírásai](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>A Python-könyvtár telepítése az Azure Databricks-fürtre

A [Python-kódtár](/azure/kusto/api/python/kusto-python-client-library) telepítése az Azure Databricks-fürtre:

1. Nyissa meg az Azure Databricks-munkaterületet, és [hozzon létre egy könyvtárat.](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Töltsön fel python PyPI csomagot vagy Python Egg csomagot.](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
   - Töltse fel, telepítse és csatolja a tár a Databricks-fürthöz.
   - Írja be a PyPi nevét: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Csatlakozás az Azure Data Explorerhez egy eszközbejelentkezéshasználatával

[Jegyzetfüzet importálása](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) a [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notebook használatával. Ezután a hitelesítő adatok használatával csatlakozhat az Azure Data Explorerhez.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Csatlakozás az ADX-hez egy Azure AD-alkalmazás használatával

1. Hozzon létre Azure AD-alkalmazást [egy Azure AD-alkalmazás kiépítésével.](/azure/kusto/management/access-control/how-to-provision-aad-app)
1. Az Azure Data Explorer-adatbázisban az alábbiak szerint adhat hozzáférést az Azure AD-alkalmazáshoz:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | az adatbázis neve |
    | ```AAD App ID``` | az Azure AD alkalmazás azonosítója |
    | ```AAD Tenant ID``` | az Azure AD-bérlői azonosítója |

### <a name="find-your-azure-ad-tenant-id"></a>Az Azure AD-bérlői azonosító megkeresése

Egy alkalmazás hitelesítéséhez az Azure Data Explorer az Azure AD-bérlői azonosítót használja. A bérlői azonosító megkereséséhez használja a következő URL-címet. Helyettesítse a *domainyourdomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Az eredmények megtekintéséhez jelölje ki ezt az URL-címet. Az első sor a következő: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlőazonosítója `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Az Azure AD-alkalmazásazonosító és kulcs tárolása és biztonságossá tétele 

Az Azure [Databricks-titkok](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) használatával az alábbiak szerint tárolhatja és biztonságossá teszi az Azure AD-alkalmazásazonosítóját és kulcsát:
1. [Állítsa be a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Telepítse a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)- 
1. [A hitelesítés beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurálja a [titkos kulcsokat](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) a következő mintaparancsokkal:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Jegyzetfüzet importálása
[Jegyzetfüzet importálása](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) a [Query-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) notebook segítségével az Azure Data Explorerhez való csatlakozáshoz. Frissítse a helyőrző értékeket a fürt nevével, az adatbázis nevével és az Azure AD-bérlői azonosítóval.
