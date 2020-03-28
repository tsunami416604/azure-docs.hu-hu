---
title: Oktatóanyag – Hozzon létre egy Azure Red Hat OpenShift-fürtöt
description: Ismerje meg, hogyan hozhat létre Microsoft Azure Red Hat OpenShift fürtöt az Azure CLI használatával
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 11/04/2019
ms.openlocfilehash: 58fc695707995aafe4d804ffab8beee7c52b4320
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79455298"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Hozzon létre egy Azure Red Hat OpenShift-fürtöt

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan hozhat létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával, méretezheti, majd törölheti az erőforrások törléséhez.

A sorozat első részében megtudhatja, hogyan:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Ez az oktatóanyag az Azure CLI 2.0.65-ös verzióját igényli.

Az oktatóanyag elkezdése előtt:

Győződjön meg arról, hogy [beállította a fejlesztői környezetet,](howto-setup-environment.md)amely a következőket tartalmazza:
- A legújabb CLI telepítése (2.0.65-ös vagy újabb verzió)
- Bérlő létrehozása, ha még nem rendelkezik ilyen
- Azure-alkalmazásobjektum létrehozása, ha még nem rendelkezik ilyen
- Biztonsági csoport létrehozása
- Active Directory-felhasználó létrehozása a fürtbe való bejelentkezéshez.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha az Azure CLI-t helyileg futtatja, nyisson meg egy Bash parancshéjat, és futtassa a `az login` bejelentkezést az Azure-ba.

```azurecli
az login
```

 Ha több előfizetéshez is hozzáfér, futtassa `az account set -s {subscription ID}` a cserélni `{subscription ID}` a használni kívánt előfizetéssel.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2. lépés: Azure Red Hat OpenShift-fürt létrehozása

A Bash parancsablakban állítsa be a következő változókat:

> [!IMPORTANT]
> Válasszon egyedi nevet a fürtnek, és az összes kis- vagy fürtlétrehozása sikertelen lesz.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Válassza ki a fürt létrehozásához helyet. Az Azure-beli OpenShift szolgáltatást támogató Azure-régiók listáját a Támogatott régiók című [témakörben tetszetős.](supported-resources.md#azure-regions) Például: `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Állítsa `APPID` be az [Azure AD-alkalmazás regisztrációjának 5.](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)

```bash
APPID=<app ID value>
```

Állítsa a "GROUPID" értéket az Azure [AD biztonsági csoport létrehozása](howto-aad-app-configuration.md#create-an-azure-ad-security-group)10.

```bash
GROUPID=<group ID value>
```

Állítsa `SECRET` be az [Ügyféltitok létrehozása](howto-aad-app-configuration.md#create-a-client-secret)8.

```bash
SECRET=<secret value>
```

Az `TENANT` új bérlő létrehozása című [7.](howto-create-tenant.md#create-a-new-azure-ad-tenant)

```bash
TENANT=<tenant ID>
```

Hozza létre a fürt erőforráscsoportját. Futtassa a következő parancsot ugyanabból a Bash rendszerhéjból, amelyet a fenti változók definiálására használt:

```azurecli
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Nem kötelező: A fürt virtuális hálózatának csatlakoztatása meglévő virtuális hálózathoz

Ha nem kell csatlakoztatnia a létrehozott fürt virtuális hálózatát egy meglévő virtuális hálózathoz társviszony-létesítés útján, hagyja ki ezt a lépést.

Ha az alapértelmezett előfizetésen kívüli hálózatra létesít, akkor az adott előfizetésben regisztrálnia kell a Microsoft.ContainerService szolgáltatót is. Ehhez futtassa az alábbi parancsot az adott előfizetésben. Ellenkező esetben, ha a virtuális hálózat, amelynek társviszonya van ugyanabban az előfizetésben található, kihagyhatja a regisztrálási lépést.

`az provider register -n Microsoft.ContainerService --wait`

Először a meglévő virtuális hálózat azonosítóját. Az azonosító a következő `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`formában lesz: .

Ha nem tudja a hálózat nevét vagy azt az erőforráscsoportot, amelyhez a meglévő virtuális hálózat tartozik, lépjen a [Virtuális hálózatok panelre,](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) és kattintson a virtuális hálózatra. Megjelenik a Virtuális hálózat lap, amely felsorolja a hálózat nevét és azt az erőforráscsoportot, amelyhez tartozik.

Definiáljon egy VNET_ID változót a következő CLI paranccsal egy BASH-rendszerhéjban:

```azurecli
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Például:`VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="optional-connect-the-cluster-to-azure-monitoring"></a>Nem kötelező: A fürt csatlakoztatása az Azure Monitoring szolgáltatáshoz

Először a **meglévő** log-analytics munkaterület azonosítóját. Az azonosító a következő űrlapon lesz látható:

`/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.OperationalInsights/workspaces/{workspace-id}`.

Ha nem ismeri a naplóelemzési munkaterület nevét vagy azt az erőforráscsoportot, amelyhez a meglévő naplóelemzési munkaterület tartozik, lépjen a [Log-Analytics-munkaterületre,](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) és kattintson a log-analytics-munkaterületre. Megjelenik a naplóelemzésmunkaterület lapja, amely felsorolja a munkaterület nevét és azt az erőforráscsoportot, amelyhez tartozik.

_Naplóelemzési munkaterület létrehozásához [lásd: Naplóelemzési munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace-cli.md)_

Definiáljon egy WORKSPACE_ID változót a következő CLI paranccsal egy BASH-rendszerhéjban:

```azurecli
WORKSPACE_ID=$(az monitor log-analytics workspace show -g {RESOURCE_GROUP} -n {NAME} --query id -o tsv)
```

### <a name="create-the-cluster"></a>A fürt létrehozása

Most már készen áll a fürt létrehozására. A következő hozza létre a fürtöt a megadott Azure AD-bérlőben, adja meg az Azure AD alkalmazásobjektumot, és titkos kulcsot, amelyet rendszerbiztonsági tagként kell használni, valamint a biztonsági csoportot, amely a fürthöz rendszergazdai hozzáféréssel rendelkező tagokat tartalmazza.

> [!IMPORTANT]
> Győződjön meg arról, hogy megfelelően adta hozzá a megfelelő engedélyeket az Azure AD alkalmazáshoz, ahogy [az itt részletezve](howto-aad-app-configuration.md#add-api-permissions) van, mielőtt létrehozna egy fürtöt

Ha **nem** egy virtuális hálózatra létesíti a fürtöt, vagy **nem** szeretné az Azure Monitoring szolgáltatást, használja a következő parancsot:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

**Ha** a fürtöt virtuális hálózatra létesíti, használja a `--vnet-peer` következő parancsot, amely hozzáadja a jelzőt:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

Ha **azt szeretné,** hogy az Azure Monitoring `--workspace-id` a fürttel, használja a következő parancsot, amely hozzáadja a jelzőt:

```azurecli
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --workspace-id $WORKSPACE_ID
```

> [!NOTE]
> Ha hibaüzenetet kap, hogy az állomásnév nem érhető el, annak az lehet az oka, hogy a fürtneve nem egyedi. Próbálja meg az eredeti alkalmazásregisztrációt, és ismételje meg a lépéseket egy másik fürtnévvel az [Új alkalmazásregisztráció létrehozása című](howto-aad-app-configuration.md#create-an-azure-ad-app-registration)részben, kihagyva az új felhasználói és biztonsági csoport létrehozásának lépését.




Néhány perc múlva, `az openshift create` befejeződik.

### <a name="get-the-sign-in-url-for-your-cluster"></a>A fürt bejelentkezési URL-címének beszereznie

A következő parancs futtatásával jelentkezz be a fürtbe való bejelentkezéshez az URL-cím:

```azurecli
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Keresse meg `publicHostName` a kimenet, például:`"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`

A fürt bejelentkezési URL-címét `https://` az `publicHostName` érték követi.  Például: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Ezt az URI-t a következő lépésben fogja használni az alkalmazásregisztrációs átirányítási URI részeként.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>3. lépés: Az alkalmazásregisztrációs átirányítási URI frissítése

Most, hogy rendelkezik a fürt bejelentkezési URL-címére, állítsa be az alkalmazásregisztrációs átirányítási felhasználói felületet:

1. Nyissa meg az [Alkalmazásregisztrációk panelt.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)
2. Kattintson az alkalmazásregisztrációs objektumra.
3. Kattintson az **Átirányítási URI hozzáadása gombra.**
4. Győződjön meg arról, hogy a **TYPE** **web,** és állítsa be az **ÁTIRÁNYÍTÁSURI-t** a következő minta használatával: `https://<public host name>/oauth2callback/Azure%20AD`. Például:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Kattintson a **Mentés gombra**

## <a name="step-4-sign-in-to-the-openshift-console"></a>4. lépés: Bejelentkezés az OpenShift konzolba

Most már készen áll arra, hogy bejelentkezzen az új fürt OpenShift konzoljára. Az [OpenShift webkonzol](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) lehetővé teszi az OpenShift-projektek tartalmának megjelenítését, tallózását és kezelését.

Szüksége lesz egy friss böngészőpéldány, amely nem gyorsítótárazott az identitás, amely általában az Azure Portalon való bejelentkezéshez használt.

1. *Inkognitóablak* (Chrome) vagy *InPrivate-ablak* (Microsoft Edge) megnyitása.
2. Keresse meg a fent megszerzett bejelentkezési URL-t, például:`https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Jelentkezzen be az Új Azure Active [Directory-felhasználó létrehozása](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user)3.

Megjelenik **egy kért engedély** párbeszédpanel. Kattintson **a Hozzájárulás a szervezet nevében,** majd az Elfogadás **gombra.**

Most már be van jelentkezve a fürtkonzolba.

![Képernyőkép az OpenShift fürtkonzolról](./media/aro-console.png)

 A [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentációjában további információ arról, hogy miként hozhat létre és hozhat létre képeket az [OpenShift konzollal.](https://docs.openshift.com/aro/getting_started/developers_console.html)

## <a name="step-5-install-the-openshift-cli"></a>5. lépés: Az OpenShift CLI telepítése

Az [OpenShift CLI](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (vagy *OC Tools)* parancsokat biztosít az alkalmazások kezeléséhez, és alacsonyabb szintű segédprogramokat az OpenShift fürt különböző összetevőivel való kommunikációhoz.

Az OpenShift konzolon kattintson a bejelentkezési név vel a jobb felső sarokban lévő kérdőjelre, és válassza a **Parancssori eszközök parancsot.**  Kövesse a **Legújabb kiadás** linket a támogatott oc CLI letöltéséhez és telepítéséhez Linuxra, MacOS-re vagy Windowsra.

> [!NOTE]
> Ha nem látható a kérdőjel ikon a jobb felső sarokban, válassza a *Szolgáltatáskatalógus* vagy az *Alkalmazáskonzol* elemet a bal felső legördülő menüből.
>
> Felváltva, akkor [töltse le a oc CLI](https://www.okd.io/download.html) közvetlenül.

A **Parancssori eszközök** lap az `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`űrlap parancsát tartalmazza.  A parancs másolásához kattintson a *Vágólap másolása* gombra.  Egy terminálablakban [állítsa be az elérési utat](https://docs.okd.io/latest/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) úgy, hogy tartalmazza az oc eszközök helyi telepítését. Ezután jelentkezzen be a fürtbe a másolt oc CLI paranccsal.

Ha a fenti lépésekkel nem tudta beszerezni a token `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`értékét, a token értékét a következőtől kapja meg: .

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Azure Red Hat OpenShift-fürt létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
