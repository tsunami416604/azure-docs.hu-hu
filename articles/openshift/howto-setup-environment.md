---
title: Az Azure Red Hat OpenShift fejlesztési környezet beállítása |} A Microsoft Docs
description: Az alábbiakban a Microsoft Azure Red Hat OpenShift használatának előfeltételei.
services: openshift
keywords: Red hat openshift telepítés beállítása
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 21ccd7b2919714610dbd51c62701c4bb32d330d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808833"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Az Azure Red Hat OpenShift fejlesztői környezetének beállítása

Alkalmazások létrehozását és futtatását a Microsoft Azure Red Hat OpenShift, a következőket kell tennie:

* Az Azure fenntartott virtuálisgép-példányok vásárolhat.
* Verzió 2.0.65 telepítése (vagy magasabb) az Azure CLI-vel (vagy az Azure Cloud Shell használata).
* Regisztráljon a `AROGA` szolgáltatás és a társított erőforrás-szolgáltatók.
* Hozzon létre egy Azure Active Directory (Azure AD) bérlő.
* Hozzon létre egy Azure AD alkalmazás-objektum.
* Hozzon létre egy Azure AD-felhasználót.

Az alábbi utasításokat követve végigvezeti az összes ezeket az előfeltételeket.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Az Azure Red Hat OpenShift alkalmazás csomópontokon fenntartott példányok vásárlása

Az Azure Red Hat OpenShift használata előtt kell vásárolni egy legalább 4 Azure Red Hat OpenShift fenntartott alkalmazás csomópontból, amely után is elérheti a fürtök létrehozása.

Ha Ön Azure-ügyfélnek, [Azure Red Hat OpenShift fenntartott példányok megvásárlása](https://aka.ms/openshift/buy) az Azure Portalon keresztül. Vásárlás, után az előfizetés 24 órán belül aktív lesz.

Ha nem Azure-ügyfélnek, [kapcsolatfelvétel az értékesítési részleggel](https://aka.ms/openshift/contact-sales) és a folyamat elindításához az oldal alján az értékesítési űrlap kitöltésekor.

Tekintse meg a [Azure Red Hat OpenShift díjszabását ismertető lapon](https://aka.ms/openshift/pricing) további információt.

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Azure Red Hat OpenShift igényel 2.0.65 verzió vagy újabb verzióját az Azure parancssori felület. Ha már telepítette az Azure CLI, melyik verzió van futtatásával ellenőrizheti:

```bash
az --version
```

Az első sor a kimeneti például lesz a parancssori felület verziójának `azure-cli (2.0.65)`.

Az alábbiakban útmutatást [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ha szüksége van egy új telepítést vagy a verziófrissítésre.

Másik lehetőségként használhatja a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Az Azure Cloud Shell használata esetén ügyeljen arra, hogy válassza ki a **Bash** környezetet, ha azt tervezi, hogy kövesse a a [létrehozása és kezelése az Azure Red Hat OpenShift fürt](tutorial-create-cluster.md) oktatóanyag-sorozat.

## <a name="register-providers-and-features"></a>Regisztrálja a szolgáltatók és funkciók

A `Microsoft.ContainerService AROGA` funkció, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` és `Microsoft.Network` szolgáltatók az előfizetéshez, az első Azure Red Hat OpenShift-fürt üzembe helyezése előtt manuálisan kell regisztrálni.

A szolgáltatók és a szolgáltatások manuálisan kell regisztrálnia, használja az alábbi utasításokat követve egy Bash rendszerhéjból, ha telepítette a parancssori felület vagy az Azure Cloud Shell (Bash) munkamenet az Azure Portalon:

1. Ha több Azure-előfizetéssel rendelkezik, adja meg a megfelelő előfizetés-azonosító:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. A Microsoft.ContainerService AROGA funkció regisztrálása:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. A Microsoft.Storage szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. A Microsoft.Compute szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. A Microsoft.Solutions-szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. A Microsoft.Network szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. A Microsoft.KeyVault-szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. A Microsoft.ContainerService erőforrás-szolgáltató regisztrációjának frissítése:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Az Azure Active Directory (Azure AD) bérlő létrehozása

Az Azure Red Hat OpenShift szolgáltatáshoz szükséges egy társított Azure Active Directory (Azure AD) bérlő, amely a szervezet és a kapcsolat a Microsoftnak. Az Azure AD-bérlő regisztrálásához, hozhat létre, és alkalmazások kezelése, valamint más Azure-szolgáltatások lehetővé teszi.

Ha nem rendelkezik a bérlő adatokként Azure Red Hat OpenShift-fürthöz tartozó Azure AD, vagy létrehoz egy bérlőt tesztelési, kövesse a [Azure Red Hat OpenShift-fürthöz tartozó Azure AD-bérlő létrehozása](howto-create-tenant.md) előtt Ez az útmutató folytatása.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Hozzon létre egy Azure AD felhasználói, biztonsági csoportot és alkalmazást objektum

Az Azure Red Hat OpenShift a fürtben, például a tárolás konfigurálása a feladatok végrehajtásához engedélyekkel kell rendelkeznie. Ezek az engedélyek vannak megadva keresztül egy [szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Érdemes azt is, egy új Active Directory-felhasználó létrehozása az Azure Red Hat OpenShift fürtön futó alkalmazások tesztelése.

Kövesse a [hozzon létre egy Azure AD alkalmazás-objektum és a felhasználó](howto-aad-app-configuration.md) egy szolgáltatásnév létrehozásához, egy ügyfél titkos kulcs és a hitelesítési visszahívási URL-címet generálni az alkalmazáshoz, és hozzon létre egy új Azure AD biztonsági csoport és a felhasználói eléréséhez a a fürt.

## <a name="next-steps"></a>További lépések

Most már készen áll Azure Red Hat OpenShift használja!

Próbálja ki az oktatóanyag:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
