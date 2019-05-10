---
title: Oktatóanyag – Azure Red Hat OpenShift-fürt létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI használatával
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/08/2019
ms.openlocfilehash: baada8a5238725456ca4a2ec7e8257c229066115
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466182"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Oktatóanyag: Az Azure Red Hat OpenShift-fürt létrehozása

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan hozzon létre egy Microsoft Azure Red Hat OpenShift-fürtöt az Azure CLI-vel, skálázza fel, majd törölje az erőforrások törlése.

A sorozat első részében, megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Az Azure Red Hat OpenShift-fürt létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Az Azure Red Hat OpenShift-fürt létrehozása
> * [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)
> * [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

Győződjön meg arról, hogy [a fejlesztési környezet beállítása](howto-setup-environment.md), amely tartalmazza:
- A legújabb parancssori felület telepítése (2.0.64 verzió vagy újabb)
- Bérlő létrehozása
- Egy Azure-alkalmazás objektumának létrehozása
- Egy a fürtben futó alkalmazásokhoz való bejelentkezéshez használt Active Directory-felhasználó létrehozása.

## <a name="step-1-sign-in-to-azure"></a>1. lépés: Bejelentkezés az Azure-ba

Ha helyileg futtatja az Azure CLI, nyissa meg a Bash parancs héjastól és futtatási `az login` bejelentkezni az Azure-bA.

```bash
az login
```

 Ha több előfizetést is hozzáférhet, futtassa `az account set -s {subscription ID}` cseréje `{subscription ID}` a használni kívánt előfizetést.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>2. lépés: Az Azure Red Hat OpenShift-fürt létrehozása

A Bash parancssori ablakban állítsa be az alábbi változókat:

> [!IMPORTANT]
> A fürt neve csak kisbetűket, vagy a fürt létrehozása sikertelen lesz.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Használja ugyanazt a nevet a fürt eljárás 6. lépésében megadottal [hozzon létre új alkalmazásregisztráció](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Válassza ki azt a helyet, a fürt létrehozásához. Az azure-régiók listáját, amely támogatja az OpenShift az Azure-ban, lásd: [támogatott régiók](supported-resources.md#azure-regions). Például: `LOCATION=eastus`.

Állítsa be `FQDN` a fürt teljesen minősített nevére. Ez a név áll a fürt nevét, helyét, és `.cloudapp.azure.com` végén lesz hozzáfűzve. Ez az azonos a bejelentkezési URL-6. lépésében létrehozott [hozzon létre új alkalmazásregisztráció](howto-aad-app-configuration.md#create-a-new-app-registration). Példa:  

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Állítsa be `APPID` a mentett érték a 9. lépés [hozzon létre egy új alkalmazásregisztráció](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Állítsa be `SECRET` a mentett érték a 6. lépés [hozzon létre egy ügyfélkulcsot](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Állítsa be `TENANT` mentett, a bérlői azonosító értéke a 7. lépés [egy új bérlő létrehozása](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Az erőforráscsoport, a fürt létrehozásához. Futtassa a következő parancs a Bash felületet, amellyel a fenti változókat határozhat meg:

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

Most már készen áll a fürt létrehozásához.

 Ha a virtuális hálózathoz a fürt nem csatlakozik egy meglévő virtuális hálózatot, hagyja ki a záró `--vnet-peer-id $VNET_ID` paraméter a következő példában.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Néhány perc múlva `az openshift create` fogja sikeresen befejeződtek, valamint a fürt adatait tartalmazó JSON-választ adja vissza.

> [!NOTE]
> Ha hiba történt, hogy a gazdagép neve nem érhető el, mert a fürt neve nem egyedi lehet. Próbálja ki az eredeti alkalmazásregisztráció törlése és a lépései [hozzon létre egy új alkalmazásregisztráció] (howto-aad-app-configuration.md#create-a-new-app-registration) (új felhasználó létrehozása óta már létrehozott egyet az utolsó lépés kihagyása) megismétlése egy különböző fürt neve.

## <a name="step-3-sign-in-to-the-openshift-console"></a>3. lépés: Jelentkezzen be az OpenShift konzol

Most már készen áll az OpenShift-konzolon az új fürtre való bejelentkezéshez. A [OpenShift Webkonzol](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) jelenítheti meg, keresse meg és kezelheti az OpenShift projektek tartalmát is.

Mint léptetjük a [új Azure AD-felhasználó](howto-aad-app-configuration.md#create-a-new-active-directory-user) tesztelési létrehozott. Ehhez szüksége lesz egy friss böngésző-példányt, amely még nem gyorsítótárazza az identitást, normál esetben használhatja az Azure Portalra való bejelentkezéshez.

1. Nyissa meg egy *inkognitó* (Chrome) ablakban vagy *InPrivate* (a Microsoft Edge) ablak.
2. Keresse meg a bejelentkezési URL-6. lépésében létrehozott [hozzon létre egy új alkalmazásregisztráció](howto-aad-app-configuration.md#create-a-new-app-registration). Például: https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> Az OpenShift konzol egy önaláírt tanúsítványt használ.
> Amikor a rendszer kéri a böngészőben, a figyelmeztetés mellőzése, és fogadja el a "nem megbízható" tanúsítványt.

Jelentkezzen be a felhasználónév és jelszó, amelyet [hozzon létre egy új Active Directory-felhasználót](howto-aad-app-configuration.md#create-a-new-active-directory-user) amikor a **kért engedélyeket** párbeszédpanel megjelenésekor válassza **hozzájárul a szervezet nevében**  , majd **fogadja el**.

Most már jelentkezett be a fürt konzolba.

![Az OpenShift fürt konzoljának képernyőképe](./media/aro-console.png)

 Tudjon meg többet [az OpenShift konzollal](https://docs.openshift.com/aro/getting_started/developers_console.html) hozhat létre és a beépített rendszerképek a [Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html) dokumentációját.

## <a name="step-4-install-the-openshift-cli"></a>4. lépés: Telepítse az OpenShift CLI-t

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
> * Az Azure Red Hat OpenShift-fürt létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt skálázása](tutorial-scale-cluster.md)