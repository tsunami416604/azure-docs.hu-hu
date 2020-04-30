---
title: Oktatóanyag – kapcsolódás Azure Red Hat OpenShift 4 rendszerű fürthöz
description: Útmutató Microsoft Azure Red Hat OpenShift-fürthöz való kapcsolódáshoz
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d7efe781f1ba2beb1fa7dd4fdaaad280fc789de2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204385"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Oktatóanyag: Kapcsolódás Azure Red Hat OpenShift 4 rendszerű fürthöz

Ebben az oktatóanyagban, amely három részből áll, a OpenShift 4-es verzióját futtató Azure Red Hat OpenShift-fürthöz csatlakozik a OpenShift webkonzolon keresztül a kubeadmin-felhasználóként. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> * Hitelesítő adatok beszerzése `kubeadmin` a fürthöz
> * A OpenShift parancssori felületének telepítése
> * Kapcsolódás Azure Red Hat OpenShift-fürthöz az OpenShift CLI használatával

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban létrehoztak egy Azure Red Hat OpenShift-fürtöt. Ha még nem tette meg ezeket a lépéseket, és követni szeretné a lépéseket, kezdje az [1. oktatóanyag használatával – hozzon létre egy Azure Red Hat Openshift 4 fürtöt.](tutorial-create-cluster.md)

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.75 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A `kubeadmin` felhasználó segítségével bejelentkezhet a fürtbe.  Futtassa a következő parancsot a `kubeadmin` felhasználó jelszavának megkereséséhez.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
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
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Indítsa el a konzol URL-címét egy böngészőben, és `kubeadmin` jelentkezzen be a hitelesítő adatok használatával.

![Azure Red Hat OpenShift bejelentkezési képernyő](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>A OpenShift parancssori felületének telepítése

Ha bejelentkezett a OpenShift webkonzolba, kattintson a következőre **?** a jobb felső sarokban, majd a **parancssori eszközök menüpontban**. Töltse le a számítógépének megfelelő kiadást.

![Azure Red Hat OpenShift bejelentkezési képernyő](media/aro4-download-cli.png)

A parancssori felület legújabb kiadását is letöltheti a gépre <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

Ha a Azure Cloud Shell parancsokat futtatja, töltse le a legújabb OpenShift 4 CLI Linux rendszerhez.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Kapcsolat a OpenShift CLI használatával

Kérje le az API-kiszolgáló címeit.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Jelentkezzen be a OpenShift-fürt API-kiszolgálójára a következő parancs használatával. Cserélje le ** \<a kubeadmin Password>** az imént beolvasott jelszóra.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:
> [!div class="checklist"]
> * Hitelesítő adatok beszerzése `kubeadmin` a fürthöz
> * A OpenShift parancssori felületének telepítése
> * Kapcsolódás Azure Red Hat OpenShift-fürthöz az OpenShift CLI használatával

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift-fürt törlése](tutorial-delete-cluster.md)