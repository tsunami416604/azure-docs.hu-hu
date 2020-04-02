---
title: Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan forgathatja el a tanúsítványokat egy Azure Kubernetes-szolgáltatás (AKS) fürtben.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00dcef4ae0f04fc7f550859238ae8c7e1ad19384
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549073"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Tanúsítványok elforgatása az Azure Kubernetes szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) tanúsítványokat használ a hitelesítéshez számos összetevőjével. Biztonsági vagy házirend-okokból időnként szükség lehet a tanúsítványok elforgatására. Előfordulhat például, hogy 90 naponta elforgatja az összes tanúsítványt.

Ez a cikk bemutatja, hogyan forgathatja el a tanúsítványokat az AKS-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy az Azure CLI 2.0.77-es vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-tanúsítványok, hitelesítésszolgáltatók és szolgáltatásfiókok

Az AKS a következő tanúsítványokat, hitelesítésszolgáltatókat és szolgáltatásfiókokat hoz létre és használja:

* Az AKS API-kiszolgáló létrehoz egy hitelesítésszolgáltatót (CA), a fürthitelesítésszolgáltatót.
* Az API-kiszolgáló rendelkezik egy fürthitelesítési webhel, amely aláírja az API-kiszolgáló és a kubelets közötti egyirányú kommunikáció tanúsítványait.
* Minden kubelet is létrehoz egy tanúsítvány-aláíró kérelem (CSR), amely a fürt hitelesítésszolgáltató által aláírt, a kubelet az API-kiszolgáló közötti kommunikációhoz.
* Az etcd kulcs értéktárolója rendelkezik egy, a fürthitelesítésszolgáltató által aláírt tanúsítvánnyal az API-kiszolgálóval való kommunikációhoz.
* Az etcd kulcsérték-tároló létrehoz egy hitelesítésszolgáltatót, amely tanúsítványokat ír alá az AKS-fürt etcd replikai közötti adatreplikáció hitelesítéséhez és engedélyezéséhez.
* Az API-összesítő a fürthitelesítésszolgáltató segítségével állít ki tanúsítványokat a más API-kkal való kommunikációhoz. Az API-összesítő rendelkezhet saját hitelesítésszolgáltatóval is a tanúsítványok kiállításához, de jelenleg a fürthitelesítésszolgáltatót használja.
* Minden csomópont egy szolgáltatásfiók (SA) jogkivonatot használ, amelyet a fürthitelesítésszolgáltató ír alá.
* Az `kubectl` ügyfél rendelkezik egy tanúsítvánnyal az AKS-fürttel való kommunikációhoz.

> [!NOTE]
> A 2019 márciusa előtt létrehozott AKS-fürtök rendelkeznek két év elteltével lejáró tanúsítványokkal. A 2019 márciusa után létrehozott vagy a tanúsítványokkal elforgatott fürtök fürthitelesítési tanúsítványokkal rendelkeznek, amelyek 30 év után lejárnak. Az összes többi tanúsítvány két év után lejár. A fürt létrehozásának ellenőrzéséhez `kubectl get nodes` tekintse meg a csomópontkészletek *korát.*
> 
> Ezenkívül ellenőrizheti a fürt tanúsítványának lejárati dátumát is. A következő parancs például megjeleníti a *myAKSCluster* fürt tanúsítványának részleteit.
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>A fürttanúsítványok elforgatása

> [!WARNING]
> A tanúsítványok elforgatása `az aks rotate-certs` akár 30 perc leállást is okozhat az AKS-fürtszámára.

Az [aks get-credentials][az-aks-get-credentials] használatával jelentkezzen be az AKS-fürtbe. Ez a parancs letölti `kubectl` és konfigurálja az ügyféltanúsítványt a helyi számítógépen.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

A `az aks rotate-certs` fürt összes tanúsítványának, hitelesítésszolgáltatójának és hitelesítésszolgáltatójának elforgatására használható.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> A befejezés akár 30 `az aks rotate-certs` percet is igénybe vehet. Ha a parancs befejezése `az aks show` előtt sikertelen, ellenőrizze, hogy a fürt állapota *Tanúsítvány elforgatása*. Ha a fürt meghibásodott állapotban `az aks rotate-certs` van, futtassa újra a tanúsítványok újraforgatásához.

Ellenőrizze, hogy a régi tanúsítványok már `kubectl` nem érvényesek-e egy parancs futtatásával. Mivel nem frissítette a `kubectl`tanúsítványokat, hibaüzenet jelenik meg.  Példa:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Frissítse a futtató `kubectl` `az aks get-credentials`által használt tanúsítványt.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Ellenőrizze, hogy a tanúsítványok `kubectl` at egy parancs futtatásával frissítették-e, ami most sikeres lesz. Példa:

```console
kubectl get no
```

> [!NOTE]
> Ha olyan szolgáltatások, amelyek futnak az AKS-en, például az [Azure Dev Spaces,][dev-spaces]előfordulhat, hogy frissítenie kell [a szolgáltatásokhoz kapcsolódó tanúsítványokat][dev-spaces-rotate] is.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan kell automatikusan forgatni a fürt tanúsítványait, hitelesítésszolgáltatóit és kiszolgálóit. Az AKS biztonsági gyakorlati tanácsairól az [Azure Kubernetes-szolgáltatás (AKS) szolgáltatásban található fürtbiztonsággal és -frissítésektel kapcsolatos gyakorlati tanácsok című témakört az Azure Kubernetes-szolgáltatásban (AKS)][aks-best-practices-security-upgrades] talál.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
