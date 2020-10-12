---
title: OpenShift futó Azure Red Hat-OpenShift 4 – Azure Active Directory hitelesítés konfigurálása a parancssor használatával
description: Ismerje meg, hogyan konfigurálható Azure Active Directory hitelesítés a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürtökhöz a parancssor használatával
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: fd6ea0749cce154ae20479bc54ef9b7374a69d0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469422"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Active Directory hitelesítés konfigurálása Azure Red Hat OpenShift 4 fürthöz (CLI)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.6.0 vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest).

A Azure Active Directory alkalmazás konfigurálásához használni kívánt fürtözött URL-címek beolvasása.

Hozza létre a fürt OAuth visszahívási URL-címét, és tárolja egy változó **oauthCallbackURL**. Ügyeljen arra, hogy a fürt nevével cserélje le az **ARO-RG** nevét és az **ARO-fürtöt** .

> [!NOTE]
> A `AAD` OAuth visszahívási URL-címében szereplő szakasznak meg kell egyeznie a OAuth-identitás szolgáltatójának nevével.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Azure Active Directory alkalmazás létrehozása hitelesítéshez

Hozzon létre egy Azure Active Directory alkalmazást, és kérje le a létrehozott alkalmazás azonosítóját. Cserélje le a **\<ClientSecret>** t biztonságos jelszóval.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Ehhez ehhez hasonlót kell kapnia. Jegyezze fel, mert ez a **AppID** , amelyre szüksége lesz a későbbi lépésekben.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Kérje le az alkalmazást birtokló előfizetés bérlői AZONOSÍTÓját.

```azure
az account show --query tenantId -o tsv
```

Ehhez ehhez hasonlót kell kapnia. Jegyezze fel, mert ez a **TenantId** , amelyre szüksége lesz a későbbi lépésekben.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Jegyzékfájl létrehozása az azonosító jogkivonatban szerepeltetni kívánt választható jogcímek meghatározásához

Az alkalmazások fejlesztői használhatják az Azure AD-alkalmazásokban [választható jogcímeket](../active-directory/develop/active-directory-optional-claims.md) annak meghatározására, hogy mely jogcímeket szeretnék elküldeni az alkalmazásnak.

A következő választható jogcímeket használhatja:

- Válassza ki az alkalmazáshoz tartozó jogkivonatokban szerepeltetni kívánt további jogcímeket.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

A OpenShift a jogcím használatára konfigurálja, `email` és visszatérhet a következőre `upn` : az előnyben részesített Felhasználónév beállításához adja hozzá a `upn` Azure Active Directory által visszaadott azonosító jogkivonat részét.

Hozzon létre egy **manifest.js** fájlt a Azure Active Directory alkalmazás konfigurálásához.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>A Azure Active Directory alkalmazás optionalClaims frissítése jegyzékkel

Cserélje le **\<AppID>** a elemet a korábban kapott azonosítóra.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>A Azure Active Directory alkalmazás-hatókör engedélyeinek frissítése

Ahhoz, hogy el tudja olvasni a felhasználói adatokat Azure Active Directoryból, meg kell határoznia a megfelelő hatóköröket.

Cserélje le **\<AppID>** a elemet a korábban kapott azonosítóra.

Adjon hozzá engedélyt a **Azure Active Directory Graph. user. Read** hatókörhöz a bejelentkezés engedélyezéséhez és a felhasználói profil olvasásához.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> Ha ezt a Azure Active Directory globális rendszergazdaként hitelesíti, figyelmen kívül hagyhatja az üzenetet a jóváhagyás megadásához, mert a saját fiókjába való bejelentkezés után a rendszer erre kéri.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Felhasználók és csoportok társítása a fürthöz (nem kötelező)

Az Azure Active Directory (Azure AD) bérlőben regisztrált alkalmazások alapértelmezés szerint a sikeres hitelesítést végző bérlő összes felhasználója számára elérhetők. Az Azure AD lehetővé teszi, hogy a bérlői rendszergazdák és fejlesztők egy alkalmazást a bérlőben lévő felhasználók vagy biztonsági csoportok meghatározott csoportjára korlátozzák.

A [felhasználók és csoportok alkalmazáshoz való hozzárendeléséhez](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)kövesse az Azure Active Directory dokumentációjának utasításait.

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID-hitelesítés konfigurálása

A `kubeadmin` hitelesítő adatok beolvasása. Futtassa a következő parancsot a felhasználó jelszavának megkereséséhez `kubeadmin` .

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

Az alábbi példa kimenetében látható, hogy a jelszó a következő lesz: `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Jelentkezzen be a OpenShift-fürt API-kiszolgálójára a következő parancs használatával. A `$apiServer` változó [korábban]()lett beállítva. Cserélje le **\<kubeadmin password>** a t a beolvasott jelszóra.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Hozzon létre egy titkos OpenShift a Azure Active Directory alkalmazás titkos kódjának tárolásához, és cserélje le **\<ClientSecret>** a korábban beolvasott titkos kulcsra.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```

Hozzon létre egy **oidc. YAML** fájlt a OpenShift OpenID-hitelesítés konfigurálásához Azure Active Directoryon. Cserélje **\<AppID>** le **\<TenantId>** a és a értéket a korábban lekért értékekre.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Alkalmazza a konfigurációt a fürtre.

```azurecli-interactive
oc apply -f oidc.yaml
```

Az alábbihoz hasonló választ fog kapni.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Bejelentkezés ellenőrzése Azure Active Directory

Ha most kijelentkezik a OpenShift webkonzolon, és megpróbál újra bejelentkezni, megjelenik egy új lehetőség a **HRE**való bejelentkezéshez. Előfordulhat, hogy néhány percet várnia kell.

![Bejelentkezési képernyő Azure Active Directory lehetőséggel](media/aro4-login-2.png)