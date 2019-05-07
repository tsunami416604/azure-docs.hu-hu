---
title: Az Azure Red Hat OpenShift fejlesztési környezet beállítása |} A Microsoft Docs
description: Az alábbiakban a Microsoft Azure Red Hat OpenShift használatának előfeltételei.
services: openshift
keywords: Red hat openshift telepítés beállítása
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 7c5fbf135c02abf04e90865e20e902a95174598c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078115"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Az Azure Red Hat OpenShift fejlesztési környezet beállítása

Alkalmazások létrehozását és futtatását a Microsoft Azure Red Hat OpenShift, a következőket kell tennie:

* Az Azure fenntartott virtuálisgép-példányok vásárolhat.
* Verzió 2.0.64 telepítése (vagy magasabb) az Azure CLI-vel (vagy az Azure Cloud Shell használata).
* Regisztráljon a `openshiftmanagedcluster` szolgáltatás és a társított erőforrás-szolgáltatók.
* Hozzon létre egy Azure Active Directory (Azure AD) bérlő.
* Hozzon létre egy Azure AD alkalmazás-objektum.
* Hozzon létre egy Azure AD-felhasználót.

Az alábbi utasításokat követve végigvezeti az összes ezeket az előfeltételeket.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Az Azure virtuális gép fenntartott példányok vásárlása

Az Azure Red Hat OpenShift használata előtt kell vásárolni az Azure fenntartott virtuálisgép-példányok.

Ha Ön Azure-ügyfélnek, itt a hogyan [beszerzési Azure virtuális gép fenntartott példányok](https://aka.ms/openshift/buy). Foglalás csökkenti a teljes körűen felügyelt Azure-szolgáltatások előzetes és felhőköltéseiket. Tekintse meg [ *Mik az Azure-foglalások* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) tudhat meg többet a foglalást, és hogyan, pénzt takaríthat meg.

Ha nem Azure-ügyfélnek, [kapcsolatfelvétel az értékesítési részleggel](https://aka.ms/openshift/contact-sales) és a folyamat elindításához az oldal alján az értékesítési űrlap kitöltésekor.

## <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Azure Red Hat OpenShift igényel 2.0.64 verzió vagy újabb verzióját az Azure parancssori felület. Ha már telepítette az Azure CLI, melyik verzió van futtatásával ellenőrizheti:

```bash
az --version
```

Az első sor a kimeneti például lesz a parancssori felület verziójának `azure-cli (2.0.64)`.

Az alábbiakban útmutatást [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ha szüksége van egy új telepítést vagy a verziófrissítésre.

Másik lehetőségként használhatja a [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Az Azure Cloud Shell használata esetén ügyeljen arra, hogy válassza ki a **Bash** környezetet, ha azt tervezi, hogy kövesse a a [létrehozása és kezelése az Azure Red Hat OpenShift fürt](tutorial-create-cluster.md) oktatóanyag-sorozat.

## <a name="register-providers-and-features"></a>Regisztrálja a szolgáltatók és funkciók

A `Microsoft.ContainerService openshiftmanagedcluster` funkció, `Microsoft.Solutions`, és `Microsoft.Network` szolgáltatók az előfizetéshez, az első Azure Red Hat OpenShift-fürt üzembe helyezése előtt manuálisan kell regisztrálni.

A szolgáltatók és a szolgáltatások manuálisan kell regisztrálnia, kövesse az alábbi utasításokat egy Bash rendszerhéjból, ha telepítette a parancssori felület vagy az Azure Cloud Shell (Bash) munkamenet az Azure Portalon:.
1. Ha több Azure-előfizetéssel rendelkezik, adja meg a megfelelő előfizetés-azonosító:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. A Microsoft.ContainerService openshiftmanagedcluster funkció regisztrálása:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. A Microsoft.Solutions-szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. A Microsoft.Network szolgáltató regisztrálásához:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. A Microsoft.ContainerService erőforrás-szolgáltató regisztrációjának frissítése:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Az Azure Active Directory (Azure AD) bérlő létrehozása

Az Azure Red Hat OpenShift szolgáltatáshoz szükséges egy társított Azure Active Directory (Azure AD) bérlő, amely a szervezet és a kapcsolat a Microsoftnak. Az Azure AD-bérlő regisztrálásához, hozhat létre, és alkalmazások kezelése, valamint más Azure-szolgáltatások lehetővé teszi.

Ha nem rendelkezik a bérlő adatokként Azure Red Hat OpenShift-fürthöz tartozó Azure AD, vagy létrehoz egy bérlőt tesztelési, kövesse a [Azure Red Hat OpenShift-fürthöz tartozó Azure AD-bérlő létrehozása](howto-create-tenant.md) előtt Ez az útmutató folytatása.

## <a name="create-an-azure-ad-application-object-and-user"></a>Az Azure AD alkalmazás-objektum és a felhasználó létrehozása

Az Azure Red Hat OpenShift a fürtben, például a tárolás konfigurálása a feladatok végrehajtásához engedélyekkel kell rendelkeznie. Ezek az engedélyek vannak megadva keresztül egy [szolgáltatásnév](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) jönnek létre, ha a munkaterhelés Azure Red Hat OpenShift működtetni szeretné képviselő Azure AD alkalmazás regisztrálása és. Érdemes azt is, egy új Active Directory-felhasználó létrehozása az Azure Red Hat OpenShift fürtön futó alkalmazások tesztelése.

Kövesse a [hozzon létre egy Azure AD alkalmazás-objektum és a felhasználó](howto-aad-app-configuration.md) megtudhatja, hogyan hozhat létre egy egyszerű szolgáltatást, egy ügyfél titkos kulcs és a hitelesítési visszahívási URL-címet generálni az alkalmazáshoz, és hozzon létre egy új Active Directory-felhasználó teszteléséhez.

## <a name="next-steps"></a>További lépések

Most már készen áll Azure Red Hat OpenShift használja!

Próbálja ki az oktatóanyag:
> [!div class="nextstepaction"]
> [Az Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli