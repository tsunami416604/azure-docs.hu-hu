---
title: Az Azure Red Hat OpenShift fejlesztői környezet beállítása
description: A Microsoft Azure Red Hat OpenShift szolgáltatással való együttműködés előfeltételei az alábbiakban találhatók.
keywords: piros kalap openshift beállítás beállítása
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477034"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Az Azure Red Hat OpenShift fejlesztői környezetének beállítása

A Microsoft Azure Red Hat OpenShift alkalmazások létrehozásához és futtatásához a következőkre van szükség:

* Telepítse az Azure CLI 2.0.65-ös (vagy újabb) verzióját (vagy használja az Azure Cloud Shellt).
* Regisztráljon `AROGA` a szolgáltatásra és a társított erőforrás-szolgáltatókra.
* Hozzon létre egy Azure Active Directory (Azure AD) bérlő.
* Hozzon létre egy Azure AD-alkalmazásobjektumot.
* Hozzon létre egy Azure AD-felhasználót.

A következő utasítások végigvezetik az összes ilyen előfeltételen.

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Az Azure Red Hat OpenShift az Azure CLI 2.0.65-ös vagy újabb verzióját igényli. Ha már telepítette az Azure CLI-t, a következő futtatásával ellenőrizheti, hogy melyik verzióval rendelkezik:

```azurecli
az --version
```

Az első sor a kimenet lesz a `azure-cli (2.0.65)`CLI változat, például .

Az [alábbiakban az Azure CLI telepítésére](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vonatkozó utasításokat találja, ha új telepítésre vagy frissítésre van szüksége.

Alternatív megoldásként használhatja az [Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/overview) Az Azure Cloud Shell használata esetén **Bash** mindenképpen válassza ki a Bash-környezetet, ha az [Azure Red Hat OpenShift fürt bemutatósorozatának létrehozása és kezelése](tutorial-create-cluster.md) mellett szeretné követni.

## <a name="register-providers-and-features"></a>Szolgáltatók és funkciók regisztrálása

A `Microsoft.ContainerService AROGA` szolgáltatás `Microsoft.Solutions` `Microsoft.Compute`, `Microsoft.Storage` `Microsoft.KeyVault` , `Microsoft.Network` , , és a szolgáltatók manuálisan kell regisztrálni az előfizetésüzembe helyezése előtt az első Azure Red Hat OpenShift fürt.

Ezeknek a szolgáltatóknak és funkcióknak a manuális regisztrálásához kövesse az alábbi utasításokat egy Bash rendszerhéjból, ha telepítette a CLI-t, vagy az Azure Cloud Shell (Bash) munkamenetből az Azure Portalon:

1. Ha több Azure-előfizetéssel rendelkezik, adja meg a megfelelő előfizetés-azonosítót:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Regisztrálja a Microsoft.ContainerService AROGA szolgáltatást:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Regisztrálja a Microsoft.Storage szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Regisztrálja a Microsoft.Compute szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Regisztrálja a Microsoft.Solutions szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Regisztrálja a Microsoft.Network szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Regisztrálja a Microsoft.KeyVault szolgáltatót:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Frissítse a Microsoft.ContainerService erőforrás-szolgáltató regisztrációját:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active Directory (Azure AD) bérlő létrehozása

Az Azure Red Hat OpenShift szolgáltatáshoz egy társított Azure Active Directory (Azure AD) bérlő reprezentatot igényel, amely a szervezetet és a Microsofttal való kapcsolatát képviseli. Az Azure AD-bérlő lehetővé teszi az alkalmazások regisztrálását, létrehozása és kezelése, valamint más Azure-szolgáltatások használatát.

Ha nem rendelkezik az Azure Red Hat OpenShift-fürt bérlőjeként használható Azure AD-vel, vagy szeretne létrehozni egy bérlőt tesztelésre, kövesse az [Azure Red Hat OpenShift-fürthöz való Létrehozás az Azure-beli AD-bérlő létrehozása](howto-create-tenant.md) című útmutató utasításait, mielőtt folytatná ezt az útmutatót.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD-felhasználó, biztonsági csoport és alkalmazásobjektum létrehozása

Az Azure Red Hat OpenShift engedélyeket igényel a fürtön végzett feladatok végrehajtásához, például a tárterület konfigurálásához. Ezeket az engedélyeket egy [egyszerű szolgáltatás képviseli.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Emellett létre kell hoznia egy új Active Directory-felhasználót az Azure Red Hat OpenShift-fürtön futó alkalmazások teszteléséhez.

Kövesse az [Azure AD-alkalmazásobjektum létrehozása és felhasználója](howto-aad-app-configuration.md) című, egyszerű szolgáltatás létrehozása című, az alkalmazáshoz való ügyféltitkos és hitelesítési visszahívási URL-címének létrehozásához, valamint egy új Azure AD biztonsági csoport és felhasználó létrehozásához a fürt eléréséhez című útmutató utasításait.

## <a name="next-steps"></a>További lépések

Most már készen áll az Azure Red Hat OpenShift használatára!

Próbálja ki a bemutató:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
