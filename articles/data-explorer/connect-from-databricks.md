---
title: Csatlakozás Azure adatkezelő a Python használatával Azure Databricks
description: Ez a témakör bemutatja, hogyan Python-kódtár az Azure Databricks használatával érheti el adatait az Azure Data Explorer (ADX) két hitelesítési módszerek egyikének használatával.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428521"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Csatlakozás Azure adatkezelő a Python használatával Azure Databricks

Az [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) a Microsoft Azure Cloud Services platformra optimalizált Apache Spark-alapú elemzési platform. Ez a cikk bemutatja, hogyan Python-kódtár az Azure Databricks használatával érheti el adatait az Azure Data Explorer (ADX). Többféleképpen is lehet többek között a bejelentkezés az eszközön és az Azure Active Directory (Azure AD-) alkalmazás ADX a hitelesítéshez.

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure Data Explorer fürt és adatbázis létrehozása](/azure/data-explorer/create-cluster-database-portal)
- [Az Azure Databricks-munkaterület létrehozása](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    A **Azure Databricks szolgáltatás**, a a **Tarifacsomag** legördülő menüben válassza **prémium**. Ez lehetővé teszi, hogy az Azure Databricks titkos kulcsok használatával tárolja a hitelesítő adatait, és hivatkozni tudjon rájuk a jegyzetfüzetek és a feladatok.

- [Hozzon létre egy fürtöt](https://docs.azuredatabricks.net/user-guide/clusters/create.html) az Azure Databricksben (a minta jegyzetfüzetek futtatásához szükséges minimális beállításai) az alábbi beállításokkal:

![Fürt létrehozása](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Telepítse a Python-kódtár az Azure Databricks-fürtön

A telepítendő [Python-kódtár](/azure/kusto/api/python/kusto-python-client-library) az Azure Databricks-fürtön:

1. Nyissa meg az Azure Databricks-munkaterület és [könyvtár létrehozása](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Töltse fel a Python PyPI csomag vagy a tojás Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Töltse fel, telepítése és a kódtár csatlakoztatása a Databricks-fürt.
    - Adja meg a PyPi nevét: *azure-kusto-adatok*

## <a name="connect-to-adx-using-device-login"></a>Csatlakozás ADX eszközbejelentkezés használatával

[Importálja a Jegyzetfüzet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) használatával a [lekérdezés-ADX-eszközbejelentkezés](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notebook ADX a hitelesítő adatok használatával csatlakozni.

## <a name="connect-to-adx-using-azure-ad-app"></a>Csatlakozhat az Azure AD-alkalmazás használatával ADX

1. Hozzon létre az Azure AD-alkalmazás által [egy AAD-alkalmazás kialakítási](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Hozzáférést biztosít az Azure AD-alkalmazás az Azure Data Explorer Database, az alábbiak szerint:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | az adatbázis neve |
    | ```AAD App ID``` | az Azure AD-alkalmazás azonosítója |
    | ```AAD Tenant ID``` | az Azure AD-bérlő azonosítója |

### <a name="find-your-azure-ad-tenant-id"></a>Keresse meg az Azure AD-bérlő azonosítója

Alkalmazás hitelesítéséhez, az Azure Data Explorer használja az Azure AD-bérlő azonosítója. A bérlőazonosító megkereséséhez használja a következő URL-címet úgy, hogy a *YourDomain* kifejezés helyére a saját tartományát írja be.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Ha például a tartomány a *contoso.com*, az URL-cím a következő: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kattintson erre az URL-címre az eredmények megtekintéséhez; az első sor a következő. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

A bérlői azonosító `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store és biztonságossá tétele az Azure AD-alkalmazás azonosítója és kulcsa 

Store és az Azure AD-alkalmazás azonosítója és a kulcsot az Azure Databricks segítségével biztonságos [titkok](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) módon:
1. [A parancssori felület beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [A parancssori felület telepítése](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Hitelesítés beállítása](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurálja a [titkok](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) az alábbi Példaparancsok használatával:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>A Notebook importálása
[Importálja a Jegyzetfüzet](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) használatával a [lekérdezés-ADX – AAD-alkalmazás](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) notebook ADX csatlakozni. A helyőrző értékeket frissítse a fürt nevét, adatbázis neve és az Azure AD-bérlő azonosítója.