---
title: Az Azure Kubernetes hálózati házirendek |} A Microsoft Docs
description: Ismerje meg információkat nyújtanak a Kubernetesről hálózati szabályzatokkal is gondoskodhat a Kubernetes-fürthöz.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b4f8577724781e5df10846a5fc4e30c8320403f2
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219770"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Az Azure Kubernetes hálózati házirendek – áttekintés

Ugyanúgy, mint a hálózati biztonsági csoportok (NSG) mikroszegmentációt biztosít a virtuális gépek hálózati házirendek mikroszegmentációt podok adja meg. Az Azure hálózati házirend-végrehajtása támogatja a standard Kubernetes hálózati házirend-megadás. Címkék segítségével válasszon ki egy csoportot a podok és határozza meg, és ezek a podok engedélyezett bejövő és kimenő szabályok listájának meghatározását. További tudnivalók a Kubernetes hálózati házirendeket a [Kubernetes dokumentációját](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![Kubernetes hálózati házirendek – áttekintés](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Az Azure hálózati házirendek működnek a tárolók VNet-integráció biztosító Azure CNI együtt. Támogatott Linux-csomópontok csak a még ma. Az implementáció a meghatározott házirendek kikényszerítésére a forgalom szűrése alapján Linux IP-tábla szabályok konfigurálása.

## <a name="planning-security-for-your-kubernetes-cluster"></a>A Kubernetes-fürthöz tartozó biztonsági tervezése
A fürt biztonsági implementálásakor hálózati biztonsági csoportok (NSG-k) szűrésére észak – dél-forgalmat, vagyis a be- és a fürt alhálózatot elhagyó forgalom majd Kubernetes hálózati házirendek kelet – Nyugat-forgalom esetén is. a fürt podok közötti adatforgalmat.

## <a name="using-azure-kubernetes-network-policies"></a>Azure-beli Kubernetes hálózati házirendek segítségével
Az Azure hálózati házirendek podok mikroszegmentációt biztosít az alábbi módon használható.

### <a name="acs-engine"></a>ACS-motor
Az ACS-Engine egy olyan eszköz, az Azure-ban hoz létre egy Azure Resource Manager-sablon egy Kubernetes-fürt központi telepítésére vonatkozóan. A fürt konfigurációját a sablon létrehozásakor a eszköz átadott JSON-fájlban van megadva. A fürt támogatott beállítások teljes listáját és a hozzájuk tartozó leírások kapcsolatos további tudnivalókért lásd a Microsoft Azure Container Service-motor - fürt.

Ahhoz, hogy a szabályzatok a fürtökön üzembe helyezett acs-engine használatával, adja meg a fürt definíciós fájlt lehet "azure" a networkPolicy beállítás értékét.

#### <a name="example-configuration"></a>Konfigurálása – példa

Az alábbi JSON konfigurálása – példa egy új virtuális hálózatot és alhálózatot hoz létre, és az Azure CNI, a Kubernetes-fürt üzembe helyezése. Azt javasoljuk, hogy a JSON-fájl szerkesztése "Jegyzettömb" használja. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      “kubernetesConfig”: {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>A saját Kubernetes-fürt létrehozása az Azure-ban
A megvalósítás biztosít hálózati házirendek Podok a Kubernetes-fürtök központi telepítését, anélkül, hogy az eszközök, mint például az ACS-Engine használható. Ebben az esetben, először a beépülő modul CNI telepíti és engedélyezi azt az összes virtuális gép egy fürtben. Részletes útmutatásért lásd: [üzembe helyezése a beépülő modul egy Kubernetes-fürthöz úgy, hogy saját maga](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Miután a fürt üzembe lesz helyezve, futtassa a következő `kubectl` paranccsal töltse le és a alkalmazni az Azure-beli hálózati házirend *daemonset* a fürthöz.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A megoldás is nyílt forráskódú, és a kód érhető el a [tárházat az Azure Container hálózatkezelés](https://github.com/Azure/azure-container-networking/tree/master/npm).



## <a name="next-steps"></a>További lépések
- Ismerje meg [az Azure Kubernetes Service](../aks/intro-kubernetes.md).
-  Ismerje meg [tárolóalapú hálózatkezelés](container-networking-overview.md).
- [A beépülő modul telepítése](deploy-container-networking.md) Kubernetes-fürtök vagy a Docker-tárolókat.