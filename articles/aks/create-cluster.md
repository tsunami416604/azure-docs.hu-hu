---
title: Egy Azure Kubernetes szolgáltatás (AKS) fürt létrehozása
description: Hozzon létre egy AKS fürtöt a parancssori felületen vagy az Azure-portálon.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 684bf44c6c3c67ce1d5c798f3406270d76a8e2fa
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029232"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Egy Azure Kubernetes szolgáltatás (AKS) fürt létrehozása

Az Azure Kubernetes szolgáltatás (AKS) fürtöt vagy az Azure parancssori felület, vagy az Azure-portálon hozhatja létre.

## <a name="azure-cli"></a>Azure CLI

Használja a [az aks létrehozása] [ az-aks-create] parancsot a AKS fürt létrehozásához.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Az alábbi beállítások érhetők el a a `az aks create` parancsot. Tekintse meg a [Azure CLI hivatkozás] [ az-aks-create] AKS minden egyes argumentum további tájékoztatást a.

| Argumentum | Leírás | Szükséges |
|---|---|:---:|
| `--name` `-n` | A felügyelt fürt erőforrás nevét. | igen |
| `--resource-group` `-g` | Az Azure Kubernetes erőforráscsoport neve. | igen |
| `--admin-username` `-u` | Felhasználónév a Linux virtuális gépek számára.  Alapértelmezett: azureuser. | nem |
| `--aad-client-app-id` | (ELŐZETES VERZIÓ) Egy Azure Active Directory ügyfélalkalmazás "Native" típusú azonosítója. | nem |
| `--aad-server-app-id` | (ELŐZETES VERZIÓ) Egy Azure Active Directory-kiszolgáló alkalmazás "Web app/API" típusú azonosítója. | nem |
| `--aad-server-app-secret` | (ELŐZETES VERZIÓ) A titkos kulcsot az Azure Active Directory kiszolgálói alkalmazás. | nem |
| `--aad-tenant-id` | (ELŐZETES VERZIÓ) Az Azure Active Directory-bérlő azonosítója. | nem |
| `--admin-username` `-u` | Felhasználói fiók létrehozása az SSH-elérést a virtuális gépek csomóponton.  Alapértelmezett: azureuser. | nem |
| ` --client-secret` | A szolgáltatásnévhez társított titkos kulcs. | nem |
| `--dns-name-prefix` `-p` | DNS-előtagot a fürtök nyilvános IP-cím. | nem |
| `--dns-service-ip` | IP-címnek a Kubernetes DNS-szolgáltatáshoz. | nem |
| `--docker-bridge-address` | Az IP-cím és a Docker híd hozzárendelt hálózati maszkot. | nem |
| `--enable-addons` `-a` | Engedélyezze a Kubernetes bővítményei egy vesszővel elválasztott listában. | nem |
| `--enable-rbac` `-r` | Kubernetes szerepköralapú hozzáférés-vezérlés engedélyezése. | nem |
| `--generate-ssh-keys` | Hiányzik az SSH nyilvános és titkos kulcs fájlok hoz létre. | nem |
| `--kubernetes-version` `-k` | A fürt, például "1.7.9" vagy "1.9.6" létrehozásához használandó Kubernetes verzióját. | nem |
| `--locaton` `-l` | Az automatikusan létrehozott erőforráscsoport helye | nem |
| `--max-pods` `-m` | Három munkaállomás-csoporttal központilag telepíthető egy csomópont maximális száma. | nem |
| `--network-plugin` | A Kubernetes hálózati beépülő modul használatához. | nem |
| `--no-ssh-key` `-x` | Ne használjon, vagy hozzon létre egy helyi SSH-kulcsot. | nem |
| `--no-wait` | Ne várja meg, a hosszú futású művelet befejezéséhez. | nem |
| `--node-count` `-c` | A csomópont-készletek a csomópontok alapértelmezett számát.  Alapértelmezett: 3. | nem |
| `--node-osdisk-size` | A csomópont-készlet virtuális gép GB osDisk mérete. | nem |
| `--node-vm-size` `-s` | A virtuális gép mérete.  Alapértelmezett: standard D1 v2 típusú. | nem |
| `--pod-cidr` | A CIDR jelölésrendszer IP-tartomány, amelyből pod IP-címek hozzárendelése kubenet használata esetén. | nem |
| `--service-cidr` | A CIDR jelölésrendszer IP-tartomány, amelyből szolgáltatás fürt IP-címet hozzárendelni. | nem |
| `--service-principal` | Fürt-hitelesítéshez használt egyszerű. | nem |
| `--ssh-key-value` | SSH kulcsfájl érték vagy kulcs elérési útvonala.  Alapértelmezett: ~ /.ssh/id_rsa.pub. | nem |
| `--tags` | Lemezterület elválasztott címkéket a "key [= érték]" formátumban. Használjon "törölje a meglévő címkék. | nem |
| `--vnet-subnet-id` | Az azonosítója, amelybe a fürt központi telepítése egy meglévő virtuális hálózatot az alhálózaton. | nem |
| `--workspace-resource-id` | Egy meglévő Naplóelemzési munkaterület figyelési adatok tárolására használandó erőforrás-azonosító. | nem |

## <a name="azure-portal"></a>Azure Portal

További információk az Azure portálon AKS fürt telepítése, lásd: az Azure Kubernetes szolgáltatás (AKS) [az Azure portál gyors üzembe helyezés][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
