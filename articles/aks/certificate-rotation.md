---
title: Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan forgathatja el a tanúsítványokat egy Azure Kubernetes-szolgáltatási (ak-) fürtben.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00d8546cb20d12c5f1a94bdcababa04a77c73133
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134410"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes Service (ak) tanúsítványokat használ a számos összetevőjével való hitelesítéshez. Időnként előfordulhat, hogy ezeket a tanúsítványokat biztonsági vagy házirendi okokból kell elforgatnia. Előfordulhat például, hogy az összes tanúsítvány 90 naponkénti elforgatására vonatkozó szabályzattal rendelkezik.

Ez a cikk bemutatja, hogyan forgathatja el a tanúsítványokat az AK-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.76 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].


### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

Ennek a funkciónak a használatához a CLI *-előnézet CLI-* bővítmény 0.4.21 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AK-tanúsítványok, hitelesítésszolgáltatók és szolgáltatásfiókok

Az AK a következő tanúsítványokat, hitelesítésszolgáltatókat és szolgáltatásfiókokat hozza létre és használja:

* Az AK API-kiszolgáló létrehoz egy, a fürt HITELESÍTÉSSZOLGÁLTATÓjának nevezett hitelesítésszolgáltatót (CA).
* Az API-kiszolgáló rendelkezik egy fürt HITELESÍTÉSSZOLGÁLTATÓval, amely az API-kiszolgáló és a kubelets közötti egyirányú kommunikációhoz tartozó tanúsítványokat aláírja.
* Minden kubelet létrehoz egy tanúsítvány-aláírási kérelmet (CSR) is, amelyet a fürt HITELESÍTÉSSZOLGÁLTATÓja aláír a kubelet és az API-kiszolgáló közötti kommunikációhoz.
* A etcd a etcd és az API-kiszolgáló közötti kommunikációhoz a fürt HITELESÍTÉSSZOLGÁLTATÓja aláírja a tanúsítványt.
* A etcd kulcs értéke tároló olyan HITELESÍTÉSSZOLGÁLTATÓT hoz létre, amely aláírja a tanúsítványokat, és engedélyezi az adatreplikációt a etcd-replikák között az AK-fürtben.
* Az API-gyűjtő a fürt HITELESÍTÉSSZOLGÁLTATÓját használja a más API-kkal folytatott kommunikációhoz, például a nyílt Service Broker az Azure-hoz. Az API-gyűjtő rendelkezhet saját HITELESÍTÉSSZOLGÁLTATÓval is a tanúsítványok kiállításához, de jelenleg a fürt HITELESÍTÉSSZOLGÁLTATÓját használja.
* Mindegyik csomópont egy szolgáltatásfiók-(SA-) tokent használ, amelyet a fürt HITELESÍTÉSSZOLGÁLTATÓja aláír.
* Az `kubectl` ügyfél rendelkezik tanúsítvánnyal az AK-fürttel való kommunikációhoz.

> [!NOTE]
> A március 2019 előtt létrehozott AK-fürtökhöz két év után járó tanúsítványok tartoznak. A március 2019 után vagy bármely olyan fürt, amelynek a tanúsítványait elforgatták, 30 év után lejárnak.

## <a name="rotate-your-cluster-certificates"></a>A fürt tanúsítványainak elforgatása

> [!WARNING]
> A tanúsítványok `az aks rotate-certs` használatával történő elforgatása akár 30 percnyi állásidőt is okozhat az AK-fürt számára.

Az [az AK Get-hitelesítő adatok][az-aks-get-credentials] használatával jelentkezzen be az AK-fürtbe. Ez a parancs a `kubectl` ügyféltanúsítványt is letölti és konfigurálja a helyi gépen.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

A `az aks rotate-certs` használatával elforgathatja az összes tanúsítványt, hitelesítésszolgáltatót és SAs-t a fürtön.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> `az aks rotate-certs` befejezéséhez akár 30 percet is igénybe vehet. Ha a parancs végrehajtása nem sikerül, a `az aks show` használatával ellenőrizze, hogy a fürt állapota a *tanúsítvány elforgatása*-e. Ha a fürt hibás állapotban van, futtassa újra a `az aks rotate-certs` a tanúsítványok újbóli elforgatásához.

`kubectl` parancs futtatásával ellenőrizze, hogy a régi tanúsítványok már nem érvényesek-e. Mivel nem frissítette `kubectl`által használt tanúsítványokat, hibaüzenet jelenik meg.  Például:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Frissítse az `kubectl` által használt tanúsítványt `az aks get-credentials`futtatásával.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ellenőrizze, hogy a tanúsítványok frissítve lettek-e egy `kubectl` parancs futtatásával, amely mostantól sikeres lesz. Például:

```console
kubectl get no
```

## <a name="next-steps"></a>További lépések

Ez a cikk azt mutatja be, hogyan lehet automatikusan elforgatni a fürt tanúsítványait, hitelesítésszolgáltatóit és SAs-adatait. A biztonsági [és az Azure Kubernetes szolgáltatásban (ak) elérhető ajánlott eljárások][aks-best-practices-security-upgrades] további információkat találnak az AK biztonsággal kapcsolatos ajánlott eljárásairól.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
