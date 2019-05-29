---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/14/2019
ms.openlocfilehash: 651236c25ed912ebd7399d351677a67e3826278c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306186"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Azure Red Hat OpenShift-fürt létrehozása

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan hozzon létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI-vel, skálázza fel, majd törölje az erőforrások törlése.

A sorozat első részében, megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Ehhez az oktatóanyaghoz az Azure CLI 2.0.65 verzióját.
>    
> Az Azure Red Hat OpenShift használata előtt, be kell szereznie legalább 4 Azure Red Hat OpenShift fenntartott alkalmazás csomópontok leírtak szerint [az Azure Red Hat OpenShift fejlesztési környezet beállítása](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Az oktatóanyag elkezdése előtt:

Győződjön meg arról, hogy [a fejlesztési környezet beállítása](howto-setup-environment.md), amely tartalmazza:
- A legújabb parancssori felület telepítése (2.0.65 verzió vagy újabb)
- Ha még nem rendelkezik egy bérlő létrehozására
- Ha még nem rendelkezik egy Azure-alkalmazás objektum létrehozását
- Biztonsági csoport létrehozása
- Egy Active Directory-felhasználót, jelentkezzen be a fürt létrehozásához.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI, nyissa meg a Bash parancs héjastól és futtatási `az login` bejelentkezni az Azure-bA.

```bash
az login
```

 Ha több előfizetést is hozzáférhet, futtassa `az account set -s {subscription ID}` cseréje `{subscription ID}` a használni kívánt előfizetést.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2. lépés: Azure Red Hat OpenShift-fürt létrehozása

A Bash parancssori ablakban állítsa be az alábbi változókat:

> [!IMPORTANT]
> Válassza ki a nevét, fürt, amely egyedi, és az összes kisbetűt vagy -fürt létrehozása sikertelen lesz.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Válassza ki azt a helyet, a fürt létrehozásához. Az azure-régiók listáját, amely támogatja az OpenShift az Azure-ban, lásd: [támogatott régiók](supported-resources.md#azure-regions). Például: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Állítsa be `APPID` a mentett érték az 5. lépésében [hozzon létre egy Azure AD-alkalmazás regisztrációjának](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

10. lépésében mentett "GROUPID" értékre [létrehozása az Azure AD biztonsági csoportok](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Állítsa be `SECRET` a mentett érték a 8. lépés [hozzon létre egy ügyfélkulcsot](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Állítsa be `TENANT` mentett, a bérlői azonosító értéke a 7. lépés [egy új bérlő létrehozása](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Az erőforráscsoport, a fürt létrehozásához. Futtassa a következő parancsot az ugyanazon a Bash rendszerhéj, amellyel a fenti változókat határozhat meg:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Nem kötelező: A fürt virtuális hálózat csatlakoztatása egy meglévő virtuális hálózatot

Ha nincs szüksége a virtuális hálózat (VNET), a fürt hoz létre egy meglévő virtuális hálózatok közötti társviszony-n keresztül kapcsolódni, kihagyhatja ezt a lépést.

Először kérje le a meglévő virtuális hálózat azonosítóját. Az azonosító lesz a következő formában: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Ha nem tudja, a hálózat nevét, vagy az erőforráscsoport, a meglévő virtuális hálózathoz tartozik, ugorjon a [virtuális hálózatok panelen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) , és kattintson a virtuális hálózaton. A virtuális hálózat lap jelenik meg, és felsorolja a hálózat és tartozik, az erőforráscsoport nevét.

Adjon meg egy VNET_ID változót, CLI-paranccsal az alábbi BASH-felületen:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Például:`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>A fürt létrehozása

Most már készen áll a fürt létrehozásához. A következő létrehozza a fürtöt a megadott Azure ad-bérlőhöz, adja meg az Azure AD alkalmazás-objektum és a titkos kulcsot használja, mint egy rendszerbiztonsági tagot, és a biztonsági csoport, amely tartalmazza a fürt rendszergazdai hozzáféréssel rendelkező tagok.

Ha Ön **nem** a fürt egy virtuális hálózati társviszony használja a következő parancsot:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Ha Ön **vannak** a fürt egy virtuális hálózati társviszony használja a következő parancsot, amely hozzáadja a `--vnet-peer` jelző:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Ha hiba történt, hogy a gazdagép neve nem érhető el, mert a fürt neve nem egyedi lehet. Próbálja ki az eredeti alkalmazásregisztráció törlése és a lépéseket, a másik fürtnevet a [Create egy új alkalmazásregisztráció] (howto-aad-app-configuration.md#create-a-new-app-registration), egy új felhasználók és biztonsági csoport létrehozása a lépés kihagyása megismétlése.

Néhány perc elteltével `az openshift create` fog befejeződni.

### <a name="get-the-sign-in-url-for-your-cluster"></a>A bejelentkezési URL-cím lekérése a fürt

Kérje le a következő parancs futtatásával jelentkezzen be a fürt URL-címe:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Keresse meg a `publicHostName` a kimenetben, például: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

A bejelentkezési URL-címet a fürt számára lesz `https://` követ a `publicHostName` értéket.  Például: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Ez az URI a következő lépésben fogja használni a részeként az alkalmazás regisztrációs átirányítási URI-t.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>3. lépés: Az alkalmazás regisztrációs átirányítási URI-JÁNAK frissítése

Most, hogy a bejelentkezési URL-címben a fürthöz, állítsa be az alkalmazás regisztrációs átirányítási felhasználói felületén:

1. Nyissa meg a [regisztrációk panelére](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Kattintson az alkalmazás regisztrációs objektumon.
3. Kattintson a **adjon hozzá egy átirányítási URI-JÁNAK**.
4. Ügyeljen arra, hogy **típus** van **webes** és állítsa be a **ÁTIRÁNYÍTÁSI URI-t** használatával a következő mintának: `https://<public host name>/oauth2callback/Azure%20AD`. Például:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kattintson a **Save** (Mentés) gombra

## <a name="step-4-sign-in-to-the-openshift-console"></a>4. lépés: Jelentkezzen be az OpenShift konzol

Most már készen áll az OpenShift-konzolon az új fürtre való bejelentkezéshez. A [OpenShift Webkonzol](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) jelenítheti meg, keresse meg és kezelheti az OpenShift projektek tartalmát is.

Szüksége lesz egy friss böngésző-példányt, amely még nem gyorsítótárazza az identitást, normál esetben használhatja az Azure Portalra való bejelentkezéshez.

1. Nyissa meg egy *inkognitó* (Chrome) ablakban vagy *InPrivate* (a Microsoft Edge) ablak.
2. Keresse meg a bejelentkezési URL-beszerzett fent, például: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Jelentkezzen be a felhasználónevet, 3. lépésében létrehozott [hozzon létre egy új Azure Active Directory-felhasználót](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

A **kért engedélyeket** párbeszédpanel fog megjelenni. Kattintson a **hozzájárul a szervezet nevében** majd **elfogadás**.

Most már jelentkezett be a fürt konzolba.

![Az OpenShift fürt konzoljának képernyőképe](./media/aro-console.png)

 Tudjon meg többet [az OpenShift konzollal](https://docs.openshift.com/aro/getting_started/developers_console.html) hozhat létre és a beépített rendszerképek a [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentációját.

## <a name="step-5-install-the-openshift-cli"></a>5. lépés: Telepítse az OpenShift CLI-t

A [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (vagy *c eszközök*) parancsokat biztosít az alkalmazások és a különféle komponenseinek használatát az OpenShift fürt folytatott interakcióra szolgáló alacsonyabb szintű segédprogramok kezeléséhez.

Az OpenShift-konzolon kattintson a jobb felső sarokban található kérdőjel Ön bejelentkezési neve, és válassza ki **parancssori eszközök**.  Kövesse a **legújabb kiadása** töltse le és telepítse a támogatott c CLI a Linux, MacOS vagy Windows mutató hivatkozást.

> [!NOTE]
> Ha nem látja, akkor a jobb felső sarokban található kérdőjel ikonra, válassza ki a *szolgáltatáskatalógus* vagy *alkalmazás konzolja* bal felső legördülő listából.
>
> Azt is megteheti, hogy is [töltse le a c CLI](https://www.okd.io/download.html) közvetlenül.

A **parancssori eszközök** oldal nyújt egy parancsot az űrlap `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Kattintson a *példányt vágólapra* gombra, hogy ezt a parancsot.  Egy terminálablakban [állítsa be az elérési út](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) helyileg telepített példányára c eszközöket tartalmazza. Ezután lépjen be a kimásolt c CLI-parancsot a fürthöz.

Ha nem sikerült a token értékét használja a fenti lépéseket, get token értékét: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
