---
title: Az Azure Kubernetes Service (AKS)-fürt létrehozása
description: AKS-fürt létrehozása a parancssori felület vagy az Azure Portalon.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6bfe6f9b76693ded79aa9b9d21ddcac4e1a0733e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43110304"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS)-fürt létrehozása

Az Azure Kubernetes Service (AKS)-fürt is létrehozható az Azure CLI vagy az Azure Portalon.

## <a name="azure-cli"></a>Azure CLI

Használja a [az aks létrehozása] [ az-aks-create] paranccsal hozza létre az AKS-fürtöt.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Az alábbi lehetőségek érhetők el a `az aks create` parancsot. Tekintse meg a [Azure CLI-referenciáját] [ az-aks-create] az aks-ben minden egyes argumentum további tájékoztatást.

| Argumentum | Leírás | Szükséges |
|---|---|:---:|
| `--name` `-n` | Erőforrás neve a felügyelt fürt. | igen |
| `--resource-group` `-g` | Az Azure Kubernetes Service-erőforráscsoport nevére. | igen |
| `--admin-username` `-u` | Felhasználónév a Linux rendszerű virtuális gépek számára.  Alapértelmezett: azureuser. | nem |
| `--aad-client-app-id` | (ELŐZETES VERZIÓ) A "Natív" típusú egy Azure Active Directory ügyfélalkalmazás azonosítója. | nem |
| `--aad-server-app-id` | (ELŐZETES VERZIÓ) Az Azure Active Directory-kiszolgáló alkalmazás "Web app/API" típusú azonosítója. | nem |
| `--aad-server-app-secret` | (ELŐZETES VERZIÓ) Egy Azure Active Directory-kiszolgálóalkalmazás titkos kulcsot. | nem |
| `--aad-tenant-id` | (ELŐZETES VERZIÓ) Az Azure Active Directory-bérlő azonosítója. | nem |
| `--admin-username` `-u` | Felhasználói fiók létrehozása a virtuális gépek az SSH-hozzáférés csomóponton.  Alapértelmezett: azureuser. | nem |
| ` --client-secret` | A szolgáltatásnévhez társított titkos kulcs. | nem |
| `--dns-name-prefix` `-p` | A fürtök nyilvános IP-cím DNS-előtagot. | nem |
| `--dns-service-ip` | A Kubernetes DNS szolgáltatásnak kiosztott IP-cím. | nem |
| `--docker-bridge-address` | Az IP-cím és a Docker-híd hozzárendelt hálózati maszkot. | nem |
| `--enable-addons` `-a` | Engedélyezze a Kubernetes-bővítmények egy vesszővel tagolt lista. | nem |
| `--enable-rbac` `-r` | Kubernetes szerepköralapú hozzáférés-vezérlésének engedélyezése. | nem |
| `--generate-ssh-keys` | Hozzon létre SSH nyilvános és titkos kulcs fájljai, ha hiányzik. | nem |
| `--kubernetes-version` `-k` | A Kubernetes, például: 1.7.9"vagy"1.9.6"a fürt létrehozásához használt verzióját. | nem |
| `--location` `-l` | Az automatikusan létrehozott erőforráscsoport helye | nem |
| `--max-pods` `-m` | A podok üzembe helyezhető egy csomóponthoz maximális számát. | nem |
| `--network-plugin` | A Kubernetes hálózati beépülő modult. | nem |
| `--no-ssh-key` `-x` | Ne használjon, vagy hozzon létre egy helyi SSH-kulcsot. | nem |
| `--no-wait` | Ne várja meg, amíg a hosszú ideig futó művelet befejeződik. | nem |
| `--node-count` `-c` | A csomópontkészletek-csomópont alapértelmezett száma.  Alapértelmezett: 3. | nem |
| `--node-osdisk-size` | A GB-os virtuális gép csomópontkészletek osDisk mérete. | nem |
| `--node-vm-size` `-s` | A virtuális gép méretét.  Alapértelmezett: standard D1 v2. | nem |
| `--pod-cidr` | A CIDR jelölésrendszerben IP-tartomány, amelyből a pod IP-címek hozzárendelése kubenet használatakor. | nem |
| `--service-cidr` | A CIDR jelölésrendszerben IP-tartomány, amelyből service-fürt IP-címek hozzárendelése. | nem |
| `--service-principal` | Fürt-hitelesítéshez használt egyszerű szolgáltatást. | nem |
| `--ssh-key-value` | SSH kulcsfájl érték vagy a kulcs elérési útja.  Alapértelmezett: ~ /.ssh/id_rsa.pub. | nem |
| `--tags` | Hely elválasztott címkék a "kulcs [= érték]" formátumot. Használat: "% törölje a meglévő címkéit. | nem |
| `--vnet-subnet-id` | Egy alhálózatnak egy meglévő Vnetet, amelybe a fürt üzembe helyezéséhez azonosítója. | nem |
| `--workspace-resource-id` | Egy meglévő Log Analytics-munkaterület figyelési adatok tárolására szolgáló erőforrás-Azonosítóját. | nem |

## <a name="azure-portal"></a>Azure Portal

További információk az üzembe helyezése az Azure Portalon egy AKS-fürtöt, tekintse meg az Azure Kubernetes Service (AKS) [Azure portal rövid útmutatójának][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
