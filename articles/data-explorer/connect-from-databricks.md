---
title: Csatlakozás az Azure adatkezelő Azure databricksből Python használatával
description: Ez a témakör bemutatja, hogyan érheti el adatait az Azure Data Explorer két hitelesítési módszerek egyikének használatával egy Python-kódtár az Azure Databricks használatával.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 1101a89fd4ddb0e020d0bac237e6119b137fa978
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017497"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Csatlakozás az Azure adatkezelő Azure databricksből Python használatával

[Az Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) egy Apache Spark-alapú elemzési platform, amely a Microsoft Azure platform van optimalizálva. Ez a cikk bemutatja, hogyan érheti el adatait az Azure Data Explorer egy Python-kódtár az Azure Databricks használatával. Többféleképpen is az Azure az adatkezelőt, beleértve egy bejelentkezés az eszközön és a egy Azure Active Directory (Azure AD-) alkalmazás-hitelesítést.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Azure Data Explorer fürt és az adatbázis](/azure/data-explorer/create-cluster-database-portal).
- [Az Azure Databricks-munkaterület létrehozása](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). A **Azure Databricks szolgáltatás**, a a **Tarifacsomag** legördülő listában válassza **prémium**. Ez a beállítás lehetővé teszi az Azure Databricks titkos kulcsok használatával tárolja a hitelesítő adatait, és hivatkozni tudjon rájuk a jegyzetfüzetek és a feladatok.

- [Hozzon létre egy fürtöt](https://docs.azuredatabricks.net/user-guide/clusters/create.html) az Azure Databricksben (a minta jegyzetfüzetek futtatásához szükséges minimális beállításai) az alábbi beállításokkal:

   ![A fürt létrehozásának](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Telepítse a Python-kódtár az Azure Databricks-fürtön

Telepítése a [Python-kódtár](/azure/kusto/api/python/kusto-python-client-library) az Azure Databricks-fürtön:

1. Nyissa meg az Azure Databricks-munkaterület és [hozzon létre egy könyvtárat](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Töltse fel a Python PyPI csomag vagy a tojás Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Töltse fel, telepítése és a kódtár csatlakoztatása a Databricks-fürt.
   - Adja meg a PyPi nevét: **azure kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Csatlakozás az Azure Data Explorer egy eszközbejelentkezés használatával

[Importálja a Jegyzetfüzet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) használatával a [lekérdezés-ADX-eszközbejelentkezés](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notebook. Majd csatlakozhat az Azure Data Explorer a hitelesítő adataival.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Csatlakozás az Azure AD-alkalmazás használatával ADX

1. Hozzon létre az Azure AD-alkalmazás által [kiépítése az Azure AD-alkalmazást](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Hozzáférést biztosít az Azure AD-alkalmazás az Azure Data Explorer adatbázisban, az alábbiak szerint:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | az adatbázis neve |
    | ```AAD App ID``` | az Azure AD-alkalmazás azonosítója |
    | ```AAD Tenant ID``` | az Azure AD-bérlő azonosítója |

### <a name="find-your-azure-ad-tenant-id"></a>Keresse meg az Azure AD-bérlő azonosítója

Alkalmazás hitelesítéséhez, az Azure Data Explorer használja az Azure AD-bérlő azonosítója. A bérlő Azonosítójának megkereséséhez használja a következő URL-címet. Helyettesítse be a tartomány *tartomanynev*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Válassza ki az URL-cím, az eredmények megtekintéséhez. Az első sor a következőképpen történik: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlői azonosító `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store és az Azure AD-Alkalmazásazonosító és a kulcs védelme 

Store és biztonságossá tétele az Azure AD alkalmazás Azonosítóját és kulcsát az Azure Databricks használatával [titkok](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) módon:
1. [A parancssori felület beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [A parancssori felület telepítése](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Hitelesítés beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurálja a [titkok](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) az alábbi Példaparancsok használatával:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>A notebook importálása
[Importálja a Jegyzetfüzet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) használatával a [lekérdezés-ADX – AAD-alkalmazás](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) notebook szeretne csatlakozni az Azure Data Explorer. A helyőrző értékeket frissítse a fürt nevét, adatbázis neve és az Azure AD-bérlő azonosítója.
