---
title: OpenShift futó Azure Red Hat-OpenShift 4 – a Azure Portal és a OpenShift webkonzol használatával konfigurálja Azure Active Directory hitelesítést
description: Ismerje meg, hogyan konfigurálható Azure Active Directory hitelesítés a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtökhöz a Azure Portal és a OpenShift webkonzol használatával
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: 6b6248aac35c22b9ffd2cd95df41e84986356259
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205312"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Active Directory hitelesítés konfigurálása Azure Red Hat OpenShift 4 fürthöz (portál)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.75 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Előkészületek

Hozza létre a fürt **OAuth visszahívási URL-címét** , és jegyezze fel. Ügyeljen arra, hogy a fürt nevével cserélje le az **ARO-RG** nevét és az **ARO-fürtöt** .

> [!NOTE]
> A `AAD` OAuth visszahívási URL-címében szereplő szakasznak meg kell egyeznie a OAuth-identitás szolgáltatójának nevével.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Azure Active Directory alkalmazás létrehozása hitelesítéshez

Jelentkezzen be a Azure Portalba, és lépjen a [Alkalmazásregisztrációk](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)panelre, majd kattintson az **új regisztráció** elemre új alkalmazás létrehozásához.

Adja meg az alkalmazás nevét, például: **ARO-azuread-Auth**, és töltse ki az **átirányítási URI** -t a korábban lekért OAuth visszahívási URL-címének használatával.

![Új alkalmazásregisztráció](media/aro4-ad-registerapp.png)

Navigáljon a **tanúsítványok & a titkok** elemre, és kattintson az **új ügyfél-titok** lehetőségre, és adja meg a részleteket. Jegyezze fel a kulcs értékét, ahogy azt egy későbbi szakaszban fogja használni. Nem fogja tudni lekérni.

![Titkos kulcs létrehozása](media/aro4-ad-clientsecret.png)

Navigáljon az **Áttekintés** elemre, és jegyezze fel az **alkalmazás (ügyfél) azonosítóját** és **KÖNYVTÁRát (bérlői azonosítóját**). Szüksége lesz rájuk egy későbbi fázisban.

![Alkalmazás-(ügyfél-) és könyvtár-(bérlői) azonosítók beolvasása](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Nem kötelező jogcímek konfigurálása

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban [választható jogcímeket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak.

A következő választható jogcímeket használhatja:

* Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
* Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
* Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A OpenShift a `email` jogcím használatára konfigurálja, és visszatérhet `upn` a következőre: az előnyben részesített Felhasználónév beállításához adja hozzá `upn` a Azure Active Directory által visszaadott azonosító jogkivonat részét.

Navigáljon a **jogkivonat-konfiguráció (előzetes verzió)** elemre, és kattintson az **opcionális jogcím hozzáadása**lehetőségre. Válassza az **azonosító** lehetőséget, majd jelölje be az **e-mail** és **UPN** jogcímek.

![Titkos kulcs létrehozása](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Felhasználók és csoportok társítása a fürthöz (nem kötelező)

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők. Az Azure AD lehetővé teszi, hogy a bérlői rendszergazdák és fejlesztők egy alkalmazást a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára korlátozzák.

A [felhasználók és csoportok alkalmazáshoz való hozzárendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration)kövesse az Azure Active Directory dokumentációjának utasításait.

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID-hitelesítés konfigurálása

A `kubeadmin` hitelesítő adatok beolvasása. Futtassa a következő parancsot a `kubeadmin` felhasználó jelszavának megkereséséhez.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Az alábbi példa kimenetében látható, hogy a jelszó `kubeadminPassword`a következő lesz:.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

A fürt konzoljának URL-címét a következő parancs futtatásával érheti el, amely a következőképpen fog kinézni:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Indítsa el a konzol URL-címét egy böngészőben, és `kubeadmin` jelentkezzen be a hitelesítő adatok használatával.

Lépjen az **Adminisztráció**elemre, kattintson a **fürt beállításai**elemre, majd válassza a **globális konfiguráció** fület. görgessen a **OAuth**elemre.

Görgessen le a **Hozzáadás** lehetőség kiválasztásához az **Identity Providers** területen, majd válassza az **OpenID Connect**elemet.
![Válassza az OpenID Connect lehetőséget az identitás-szolgáltatók legördülő listából](media/aro4-oauth-idpdrop.png)

Adja meg a nevet **HRE**, az **ügyfél-azonosítóként** az **alkalmazás-azonosítóként** és az **ügyfél titkos kulcsaként**. A **kiállító URL-címe** a következőképpen van formázva: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Cserélje le a helyőrzőt a korábban lekért bérlői AZONOSÍTÓra.

![Adja meg a OAuth részleteit](media/aro4-oauth-idp-1.png)

Görgessen le a **jogcímek** szakaszhoz, és frissítse az **előnyben részesített felhasználónevet** az **UPN** -jogcím értékének használatára.

![Adja meg a jogcímek részleteit](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Bejelentkezés ellenőrzése Azure Active Directory

Ha most kijelentkezik a OpenShift webkonzolon, és megpróbál újra bejelentkezni, megjelenik egy új lehetőség a **HRE**szolgáltatásba való bejelentkezéshez. Előfordulhat, hogy néhány percet várnia kell.

![Bejelentkezési képernyő Azure Active Directory lehetőséggel](media/aro4-login-2.png)
