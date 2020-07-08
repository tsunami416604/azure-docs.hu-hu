---
title: Az Azure Red Hat OpenShift fejlesztési környezet beállítása
description: Itt láthatók a Microsoft Azure Red Hat OpenShift használatának előfeltételei.
keywords: a Red Hat openshift beállítása
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477034"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Az Azure Red Hat OpenShift fejlesztői környezetének beállítása

Microsoft Azure Red Hat OpenShift-alkalmazások létrehozásához és futtatásához a következőket kell tennie:

* Telepítse az Azure CLI 2.0.65 (vagy újabb) verzióját (vagy használja a Azure Cloud Shell).
* Regisztráljon a `AROGA` szolgáltatáshoz és a társított erőforrás-szolgáltatóhoz.
* Hozzon létre egy Azure Active Directory (Azure AD) bérlőt.
* Hozzon létre egy Azure AD-alkalmazásobjektumet.
* Hozzon létre egy Azure AD-felhasználót.

Az alábbi utasítások végigvezetik az összes előfeltételen.

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure Red Hat OpenShift az Azure CLI 2.0.65 vagy újabb verzióját igényli. Ha már telepítette az Azure CLI-t, megtekintheti, hogy melyik verziót futtatja:

```azurecli
az --version
```

A kimenet első sora a CLI verzióját fogja tartalmazni, például: `azure-cli (2.0.65)` .

Az alábbi utasításokat követve [telepítheti az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t, ha új telepítésre vagy frissítésre van szüksége.

Másik lehetőségként használhatja a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). A Azure Cloud Shell használatakor ügyeljen arra, hogy a **bash** -környezetet válassza, ha azt tervezi, hogy követi az [Azure Red Hat OpenShift-fürt oktatóanyag-sorozatának létrehozását és felügyeletét](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Szolgáltatók és szolgáltatások regisztrálása

Az `Microsoft.ContainerService AROGA` `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` első Azure Red Hat OpenShift-fürt üzembe helyezése előtt manuálisan regisztrálni kell a szolgáltatást, a, és a szolgáltatót az előfizetésben.

A szolgáltatók és szolgáltatások manuális regisztrálásához kövesse az alábbi utasításokat egy bash-rendszerhéjból, ha telepítette a CLI-t, vagy a Azure Portal Azure Cloud Shell (bash) munkamenetből:

1. Ha több Azure-előfizetéssel rendelkezik, akkor a megfelelő előfizetés-azonosítót kell megadnia:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Regisztrálja a Microsoft. Tárolószolgáltatás AROGA szolgáltatást:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Regisztrálja a Microsoft. Storage szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Regisztrálja a Microsoft. számítási szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Regisztrálja a Microsoft. Solutions szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Regisztrálja a Microsoft. Network szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Regisztrálja a Microsoft. kulcstartó szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Frissítse a Microsoft. Tárolószolgáltatás erőforrás-szolgáltató regisztrációját:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active Directory (Azure AD-) bérlő létrehozása

Az Azure Red Hat OpenShift szolgáltatáshoz hozzá kell rendelni egy társított Azure Active Directory (Azure AD) bérlőt, amely képviseli a szervezetét és a Microsofttal való kapcsolatát. Az Azure AD-bérlő lehetővé teszi az alkalmazások regisztrálását, összeállítását és kezelését, valamint más Azure-szolgáltatások használatát.

Ha nem rendelkezik Azure AD-vel, amely az Azure Red Hat OpenShift-fürt bérlője, vagy ha egy bérlőt szeretne tesztelni, kövesse az Azure-beli [Red Hat OpenShift-fürthöz tartozó Azure ad-bérlő létrehozása](howto-create-tenant.md) az útmutató folytatása előtt című témakör utasításait.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD-felhasználó, biztonsági csoport és alkalmazásobjektum létrehozása

Az Azure Red Hat OpenShift engedélyekkel kell rendelkeznie a fürtön elvégzendő feladatok végrehajtásához, például a tároló konfigurálásához. Ezek az engedélyek egy [egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)szerint jelennek meg. Emellett új Active Directory felhasználót is létre kell hoznia az Azure Red Hat OpenShift-fürtön futó alkalmazások teszteléséhez.

Kövesse az [Azure ad alkalmazás-objektum és-felhasználó létrehozása](howto-aad-app-configuration.md) egyszerű szolgáltatásnév létrehozásához, az alkalmazáshoz tartozó ügyfél-titkos és hitelesítési visszahívási URL-címet, majd hozzon létre egy új Azure ad biztonsági csoportot és felhasználót a fürt eléréséhez.

## <a name="next-steps"></a>További lépések

Most már készen áll az Azure Red Hat OpenShift használatára!

Próbálja ki az oktatóanyagot:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
