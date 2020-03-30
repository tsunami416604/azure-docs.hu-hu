---
title: Azure Kubernetes hálózati házirendek | Microsoft dokumentumok
description: További információ a Kubernetes hálózati házirendjeiről a Kubernetes-fürt védelméhez.
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
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159695"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Az Azure Kubernetes hálózati szabályzatainak áttekintése

A hálózati házirendek mikroszegmentálást biztosítanak a podok számára, csakúgy, mint a hálózati biztonsági csoportok (NSG-k) mikroszegmentálást biztosítanak a virtuális gépekszámára. Az Azure network policy implementációja támogatja a szabványos Kubernetes hálózati házirend specifikációját. Címkék használatával válassza ki a podok egy csoportját, és határozza meg a be- és kimenő forgalom szabályait, amelyek meghatározzák, hogy milyen típusú forgalmat engedélyezett, és ezekből a podok. A Kubernetes hálózati házirendjeiről a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/services-networking/network-policies/)olvashat bővebben.

![Kubernetes hálózati házirendek – áttekintés](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Az Azure hálózati szabályzatok együtt működik az Azure CNI, amely virtuális hálózat integrációt biztosít a tárolók. Ez csak a Linux csomópontok ma támogatott. A megvalósítások konfigurálják a Linux IP-táblázat szabályait a megadott szabályzatok alapján a forgalomszűrés kényszerítéséhez.

## <a name="planning-security-for-your-kubernetes-cluster"></a>A Kubernetes-fürt biztonságának megtervezése
A fürt biztonságának megvalósításakor a hálózati biztonsági csoportok (NSG-k) segítségével szűrje az észak-déli forgalmat, azaz a fürt alhálózatába belépő és onnan kilépő forgalmat, és használja a Kubernetes hálózati házirendeket a kelet-nyugati forgalomhoz, azaz a podok közötti forgalomhoz a fürthöz.

## <a name="using-azure-kubernetes-network-policies"></a>Az Azure Kubernetes hálózati házirendjeinek használata
Az Azure Network szabályzatok a podok mikroszegmentálásának biztosításához használható alábbi módokon.

### <a name="acs-engine"></a>ACS-motor
Az ACS-Engine egy olyan eszköz, amely egy Azure Resource Manager-sablont hoz létre egy Kubernetes-fürt Azure-beli üzembe helyezéséhez. A fürtkonfiguráció egy JSON-fájlban van meghatározva, amelyet a sablon létrehozásakor a rendszer továbbít az eszköznek. A támogatott fürtbeállítások teljes listájával és a beállítások leírásával kapcsolatos további részletekért tekintse meg a Microsoft Azure Container Service Engine – Fürtdefiníció című részt.

Ha engedélyezni szeretné az acs-engine használatával telepített fürtök házirendjeit, adja meg a networkPolicy beállítás értékét a fürtdefiníciós fájlban, hogy "azure" legyen.

#### <a name="example-configuration"></a>Konfigurációs példa

Az alábbi JSON-példakonfiguráció új virtuális hálózatot és alhálózatot hoz létre, és egy Kubernetes-fürtöt telepít benne az Azure CNI-vel. Javasoljuk, hogy a JSON-fájl szerkesztéséhez használja a "Jegyzettömb" kifejezést. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
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
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Saját Kubernetes-fürt létrehozása az Azure-ban
A megvalósítás segítségével a Kubernetes-fürtök podok hálózati szabályzatok, amelyek saját maga üzembe helyezése, anélkül, hogy az eszközök, például az ACS-engine. Ebben az esetben először telepítse a CNI beépülő modult, és engedélyezze azt a fürt minden virtuális gépén. Részletes információ: [A beépülő modul üzembe helyezése saját kezűleg üzembe helyezett Kubernetes-fürthöz](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

A fürt üzembe helyezése után `kubectl` futtassa a következő parancsot az Azure hálózati *házirend-démonkészlet* letöltéséhez és a fürtre való alkalmazásához.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A megoldás nyílt forráskódú is, és a kód elérhető az [Azure Container Networking tárházban.](https://github.com/Azure/azure-container-networking/tree/master/npm)



## <a name="next-steps"></a>További lépések
- További információ az [Azure Kubernetes szolgáltatásról.](../aks/intro-kubernetes.md)
-  További információ a [tárolóhálózatokról.](container-networking-overview.md)
- [Telepítse a beépülő modult](deploy-container-networking.md) a Kubernetes-fürtökhöz vagy a Docker-tárolókhoz.
