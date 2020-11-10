---
title: Beépített tároló beállításjegyzékének konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
description: Beépített tároló beállításjegyzékének konfigurálása az Azure Red Hat OpenShift 4 rendszerhez
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414541"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Beépített tároló beállításjegyzékének konfigurálása az Azure Red Hat OpenShift 4 rendszerhez

Ebben a cikkben a beépített tárolók rendszerképének beállításjegyzékét fogja konfigurálni egy Azure Red Hat OpenShift (ARO) 4 fürthöz. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az Azure AD beállítása
> * OpenID Connect beállítása
> * A beépített tároló rendszerkép-beállításjegyzékének elérése

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy fürtöt az [Azure Red Hat OpenShift 4 fürt létrehozása](/azure/openshift/tutorial-create-cluster)című témakör lépéseit követve. Győződjön meg arról, hogy a következő argumentummal hozza létre a fürtöt: `--pull-secret` `az aro create` .  Erre akkor van szükség, ha be szeretné állítani az Azure AD-t a bejelentkezéshez, a jelen cikkben leírtak szerint.
* Kapcsolódjon a fürthöz a [Kapcsolódás Azure Red Hat OpenShift 4-fürthöz](/azure/openshift/tutorial-connect-cluster)című témakör lépéseit követve.
   * Ügyeljen arra, hogy kövesse a "OpenShift CLI telepítése" című szakasz lépéseit, mert a `oc` parancsot a cikk későbbi részében fogjuk használni.
   * Jegyezze fel a fürt konzoljának URL-címét, amely a következőképpen néz ki: `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . A és a értékeit a `<random>` `<region>` cikk későbbi részében fogja használni.
   * Jegyezze fel a `kubeadmin` hitelesítő adatokat. Ezeket a cikk későbbi részében fogjuk használni.

## <a name="set-up-azure-active-directory"></a>Azure Active Directory beállítása

Azure Active Directory (Azure AD) az OpenID Connect (OIDC) megvalósítása. A OIDC lehetővé teszi, hogy az Azure AD használatával jelentkezzen be az ARO-fürtbe. Az Azure AD beállításához kövesse az alábbi lépéseket.

1. Hozzon létre egy Azure AD-bérlőt a [gyors útmutató: bérlő beállítása](/azure/active-directory/develop/quickstart-create-new-tenant)című témakörben ismertetett lépéseket követve. Előfordulhat, hogy az Azure-fiók már rendelkezik Bérlővel. Ha igen, kihagyhatja a létrehozását, ha megfelelő jogosultságokkal rendelkezik a bérlőben a lépések végrehajtásához. Jegyezze fel a **bérlői azonosítóját**. Ezt az értéket később fogjuk használni.
2. Hozzon létre legalább egy Azure AD-felhasználót a [felhasználók hozzáadása vagy törlése Azure Active Directory használatával](/azure/active-directory/fundamentals/add-users-azure-active-directory)című rész lépéseit követve. Ezeket a fiókokat vagy a sajátját használhatja az alkalmazás teszteléséhez. Jegyezze fel ezen fiókok e-mail-címeit és jelszavát a bejelentkezéshez.
3. Hozzon létre egy új alkalmazás-regisztrációt az Azure AD-bérlőben a gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](/azure/active-directory/develop/quickstart-register-app)című témakörben ismertetett lépéseket követve. 
   1. Jegyezze fel a jegyzetet a és a értékével kapcsolatos előfeltételekben `<random>` `<region>` . Ezekkel az értékekkel hozhat létre egy URI-t az alábbi képletnek megfelelően:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Amikor eléri az **átirányítási URI** mezőhöz tartozó lépést, az előző lépésben adja meg az URI-t.
   1. Válassza a **Regisztráció** lehetőséget.
   1. Az alkalmazás **Áttekintés** lapján jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét az itt látható módon.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Az Azure AD-alkalmazás Áttekintés lapja.":::

4. Hozzon létre egy új ügyfél titkot. 
   1. Az újonnan létrehozott alkalmazás regisztrálása területen válassza a **tanúsítványok & Secrets** elemet a bal oldali navigációs ablaktáblán.
   1. Válassza az **Új titkos ügyfélkód** lehetőséget.
   1. Adja meg **a leírást** , és válassza a **Hozzáadás** lehetőséget.
   1. Mentse félre a generált **ügyfél titkos** értékét. Ezt az értéket a cikk későbbi részében fogja használni.

### <a name="add-openid-connect-identity-provider"></a>OpenID Connect-identitás szolgáltatójának hozzáadása

Az ARO beépített tároló-beállításjegyzéket biztosít.  Ennek eléréséhez konfigurálja az IDENTITÁSSZOLGÁLTATÓ-t az Azure AD OIDC az alábbi lépéseket követve.

1. Jelentkezzen be a böngészőből a OpenShift webkonzolra `kubeadmin` .  Ez ugyanaz az eljárás, amelyet a következő oktatóanyagban ismertetett lépések végrehajtásakor kell végrehajtani [: csatlakozás Azure Red Hat OpenShift 4-fürthöz](/azure/openshift/tutorial-connect-cluster).
1. A bal oldali navigációs panelen válassza az **adminisztrációs**  >  **fürt beállításai** lehetőséget.
1. A lap közepén válassza a **globális konfiguráció** lehetőséget.
1. Keresse meg a **OAuth** a tábla **konfigurációs erőforrás** oszlopában, és jelölje ki.
1. Az **identitás-szolgáltatók** területen válassza a **Hozzáadás** lehetőséget, majd válassza az **OpenID Connect** elemet.
1. Adja meg a **nevet** **OpenID** -ként.  Lehetséges, hogy ez az érték már be van töltve.
1. Állítsa be az **ügyfél-azonosítót** és az **ügyfél titkos kulcsát** az előző lépésből származó értékként.
1. A **kiállítói URL-cím** értékének megkereséséhez kövesse az alábbi lépéseket.
   1. Az **\<tenant-id>** URL-címben a **Azure Active Directory beállítása** szakasz során mentettre cserélje le `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Nyissa meg az URL-címet abban a böngészőben, amelyet a Azure Portalsal való kommunikációhoz használt.
   1. Másolja a tulajdonság **kiállítójának** értékét a VISSZAadott JSON-törzsbe, és illessze be azt a szövegmező címkével jelölt **URL-címére**.  A **kibocsátó** értéke hasonlóan fog kinézni `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Lépjen az oldal aljára, és válassza a **Hozzáadás** lehetőséget.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID Connect a OpenShift-ben.":::
1. Jelentkezzen ki a OpenShift webkonzolról a böngészőablak jobb felső sarkában található **Kube: admin** gombra kattintva, és válassza **ki** a kijelentkezés lehetőséget.

### <a name="access-the-built-in-container-image-registry"></a>A beépített tároló rendszerkép-beállításjegyzékének elérése

Az alábbi utasítások lehetővé teszik a beépített beállításjegyzék elérését.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Az Azure AD-felhasználó megadása rendszergazdaként

1. Jelentkezzen be a böngésző OpenShift webkonzolján egy Azure AD-felhasználó hitelesítő adataival.

   1. A konzolba való bejelentkezéshez használjon InPrivate, inkognitóban vagy más, a böngészőablakhoz hasonló szolgáltatást.
   1. Az ablak megjelenése a OIDC engedélyezése után eltérő lesz.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect engedélyezve bejelentkezési ablak.":::
   1. **OpenID** kiválasztása

   > [!NOTE]
   > Jegyezze fel a bejelentkezéshez használt felhasználónevet és jelszót. Ez a Felhasználónév és jelszó rendszergazdaként fog működni a többi művelethez és más cikkekhez.
1. Jelentkezzen be az OpenShift CLI-vel a következő lépésekkel.  A vita esetében ez a folyamat ismert `oc login` .
   1. A webkonzol jobb felső részén bontsa ki a bejelentkezett felhasználó helyi menüjét, majd válassza a **bejelentkezési parancs másolása** lehetőséget.
   1. Ha szükséges, jelentkezzen be egy új Tab-ablakba.
   1. Válassza ki a **jogkivonat megjelenítése** elemet.
   1. Másolja az alábbi, a **tokent tartalmazó bejelentkezési** azonosítót a vágólapra, és futtassa azt egy rendszerhéjban az itt látható módon.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Futtassa a parancsot `oc whoami` a konzolon, és jegyezze fel a kimenetet **\<aad-user>** .  Ezt az értéket a cikk későbbi részében fogjuk használni.
1. Jelentkezzen ki a OpenShift webkonzolról. Válassza ki a böngészőablak jobb felső sarkában látható gombot, és válassza ki a **\<aad-user>** **kijelentkezés** lehetőséget.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Adja meg az Azure AD-felhasználó számára a beállításjegyzék-interakcióhoz szükséges szerepköröket

1. Jelentkezzen be a OpenShift webkonzolra a böngészőjében a `kubeadmin` hitelesítő adatok használatával.
1. Jelentkezzen be a OpenShift CLI-be a token `kubeadmin` használatával a fenti lépéseket követve `oc login` , de a webkonzolra való bejelentkezés után végezze el azokat `kubeadmin` .
1. Futtassa a következő parancsokat, hogy engedélyezze a hozzáférést a **HRE-felhasználó** beépített beállításjegyzékéhez.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   A kimenetnek az alábbihoz hasonlóan kell kinéznie.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   A kimenetnek az alábbihoz hasonlóan kell kinéznie.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   A kimenetnek az alábbihoz hasonlóan kell kinéznie.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   A kimenetnek az alábbihoz hasonlóan kell kinéznie.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>A tároló-beállításjegyzék URL-címének beszerzése

A `oc get route` tároló-beállításjegyzék URL-címének lekéréséhez használja a következő parancsot:.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Jegyezze fel **Container Registry URL-cím** konzoljának kimenetét. A rendszer ezt az útmutatót és az azt követőket is a teljes beállításjegyzék neveként fogja használni.

## <a name="next-steps"></a>További lépések

Használja a beépített tároló lemezképének beállításjegyzékét egy alkalmazás OpenShift-on való üzembe helyezésével.  A Java-alkalmazások esetében kövesse a útmutató útmutatását, [helyezzen üzembe egy Java-alkalmazást az Open Liberty/WebSphere Liberty használatával egy Azure Red Hat OpenShift 4 fürtön](howto-deploy-java-liberty-app.md).
