---
title: Hálózati konfiguráció az Azure Kubernetes Service (AKS)
description: További információ az Azure Kubernetes Service (AKS) alapszintű és speciális hálózati konfiguráció.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: da78d388c8e9fc9684942342f48902c2a248e3b1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072299"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Hálózati konfiguráció az Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS)-fürt létrehozásakor két hálózatkezelési lehetőségek közül választhat: **alapszintű** vagy **speciális**.

## <a name="basic-networking"></a>Egyszerű hálózatkezelés

A **alapszintű** hálózatkezelés lehetőség az AKS-fürt létrehozása az alapértelmezett konfigurációja. A hálózati konfigurációt a fürt és a podok teljes egészében az Azure által felügyelt, és egyéni VNet-konfiguráció nem igénylő központi telepítésekhez megfelelő. Szabályozhatja a hálózati konfiguráció nem rendelkezik, például alapszintű hálózatkezelési kiválasztásakor a fürthöz rendelt alhálózatok vagy az IP-címtartományt.

Alapszintű hálózatkezelési használatra konfigurált egy AKS-fürt csomópontjain a [kubenet] [ kubenet] Kubernetes beépülő modult.

## <a name="advanced-networking"></a>Speciális hálózatkezelés

**Speciális** hálózatkezelés helyezi a podokat megszünteti egy Azure Virtual Network (VNet), amely konfigurál, VNet-erőforrások automatikus kapcsolatot biztosító őket, és a a gazdag integrációs képességeket állítsa be a virtuális hálózatok ajánlat.
Ha üzembe AKS-fürt a érhető el a speciális hálózati a [az Azure portal][portal], Azure CLI-vel, vagy a Resource Manager-sablonnal.

A speciális hálózati használatra konfigurált egy AKS-fürt csomópontjain a [Azure Container hálózati adapter (CNI)] [ cni-networking] Kubernetes beépülő modult.

![A hidak egyes egyetlen Azure virtuális hálózathoz csatlakozó két csomópont bemutató ábra.][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Speciális hálózati szolgáltatások

Speciális hálózatkezelés az alábbi előnyöket nyújtja:

* Egy meglévő Vnetet az AKS-fürt üzembe helyezése, vagy hozzon létre egy új virtuális hálózatot és alhálózatot a fürt számára.
* A fürt minden pod IP-címet a virtuális hálózaton van hozzárendelve, és közvetlenül kommunikálhatnak a fürt más podok és a VNet más csomópontjaira.
* A pod társított virtuális hálózaton található más szolgáltatásokkal, valamint a helyszíni hálózatok protokollon keresztül is kapcsolódhatnak az ExpressRoute és site-to-site (S2S) VPN-kapcsolatok. Podok is a helyszíni érhető el.
* A Kubernetes szolgáltatás elérhetővé külső vagy belső az Azure Load Balanceren keresztül. Emellett szolgáltatása alapszintű hálózatkezelési.
* Podok alhálózatán, amelyeken engedélyezve a Szolgáltatásvégpontok Azure-szolgáltatások, például az Azure Storage és SQL DB biztonságosan csatlakozhat.
* Használja a felhasználó által megadott útvonalak (UDR) forgalom irányítására a podok egy hálózati virtuális berendezésre.
* Podok a nyilvános interneten lévő erőforrások eléréséhez. Emellett szolgáltatása alapszintű hálózatkezelési.

> [!IMPORTANT]
> Speciális hálózatkezelés üzemeltethet maximális konfigurált egy AKS-fürt egyes csomópontjaihoz **30 podok** konfigurálásakor az Azure portal használatával.  Módosíthatja a maximális érték csak a maxPods tulajdonság módosításával, a fürt Resource Manager-sablonnal üzembe helyezésekor. Minden egyes virtuális hálózat használata az Azure CNI beépülő modullal korlátozódik számára kiosztott **4096 konfigurált IP-címek**.

## <a name="advanced-networking-prerequisites"></a>Speciális hálózatkezelési Előfeltételek

* Az AKS-fürtöt a VNet engedélyeznie kell a kimenő internetkapcsolat.
* Ne hozzon létre több mint egy AKS-fürt ugyanazon az alhálózaton.
* Speciális hálózatkezelés az aks-ben nem támogatja a virtuális hálózatok az Azure saját DNS-zónák használata.
* AKS-fürt nem használhatja `169.254.0.0/16`, `172.30.0.0/16`, vagy `172.31.0.0/16` a Kubernetes szolgáltatás címtartományt.
* Az AKS-fürtöt használt egyszerű szolgáltatásnak rendelkeznie kell `Contributor` engedélyekkel a meglévő virtuális hálózatot tartalmazó erőforráscsoportot.

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzés tervezése

A speciális hálózati szolgáltatással konfigurált fürtöknél szükség további tervezésre. A virtuális hálózat és az alhálózat méretét is futtatni tervezett podjainak számát, valamint a fürt csomópontszámának kell elhelyezni.

IP-címek a podok és a fürtcsomópontok vannak rendelve az adott alhálózatról a virtuális hálózaton belül. Minden egyes csomópont egy elsődleges IP-címmel van konfigurálva, ez az IP-címét a csomópont- és 30 további IP-címek Azure CNI által előre konfiguráltan vannak rendelve a csomóponthoz ütemezett podok. Horizontális felskálázás a fürthöz, minden egyes csomópont hasonló módon van konfigurálva az alhálózat IP-címeket.

AKS-fürt IP-cím tervezése áll egy virtuális hálózathoz, a csomópontok és a podok, legalább egy alhálózatot és egy Kubernetes-szolgáltatás címtartományt.

| Címtartomány / Azure erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Azure virtuális hálózat, /8 méretűek is lehetnek, de előfordulhat, hogy csak 4096 konfigurált IP-címek. |
| Alhálózat | Elég nagy a csomópontok és a Podok kell lennie. A minimális alhálózat méretének kiszámításához: (a csomópontok száma) + (a csomópontok száma * Podok száma csomópontonként). Egy 50 csomópontot tartalmazó fürtben: (legfeljebb 50) + (50 * 30) = 1,550, az alhálózaton kell lennie a /21 vagy nagyobb. |
| Kubernetes szolgáltatás címtartomány | Ezt a tartományt nem kell az összes hálózati elem által használt vagy a virtuális hálózat csatlakozik. Szolgáltatás címének CIDR /12 kisebbnek kell lennie. |
| Kubernetes DNS szolgáltatás IP-címe | IP-címnek a Kubernetes-címtartományt, amely a fürtszolgáltatás felderítése (kube-dns) által használt szolgáltatás. |
| Docker híd címe | IP-címet (a CIDR-jelölés) használja a Docker-hidat, IP-cím csomópontokon. Alapértelmezés szerint 172.17.0.1/16. |

Ahogy korábban említett egyes virtuális hálózatok használata az Azure CNI beépülő modullal korlátozódik számára kiosztott **4096 konfigurált IP-címek**. Speciális hálózatkezelés üzemeltethet maximális konfigurált egy fürtben lévő mindegyik csomópont **30 podok**.

## <a name="deployment-parameters"></a>Üzembe helyezési paraméterek

Amikor az AKS-fürt létrehozása, a következő paraméterek a speciális hálózati konfigurálható:

**Virtuális hálózat**: A virtuális hálózatot, amelybe át szeretné a Kubernetes-fürt üzembe helyezéséhez. Ha szeretne létrehozni egy új virtuális hálózat a fürt számára, jelölje be *új létrehozása* kövesse a lépéseket a *virtuális hálózat létrehozása* szakaszban.

**Alhálózat**: az alhálózaton, ahol szeretné a fürt üzembe helyezése a Vneten belül. Ha azt szeretné, hozzon létre egy új alhálózatot a virtuális hálózat a fürt számára, válassza *új létrehozása* kövesse a lépéseket a *alhálózat létrehozásához* szakaszban.

**Kubernetes szolgáltatás címtartomány**: A *Kubernetes szolgáltatás címtartomány* van, amelyről címeket rendel a fürtben Kubernetes-szolgáltatás az IP-címtartomány (További információ a Kubernetes-szolgáltatás: [ Szolgáltatások] [ services] a Kubernetes dokumentációjában).

A Kubernetes szolgáltatás IP-címtartomány:

* Nem lehet a virtuális hálózat IP-címtartomány a fürt
* Nem lehet átfedésben, amellyel a fürt virtuális hálózaton is társul bármely más virtuális hálózatokhoz
* Nem lehet átfedésben bármilyen helyszíni IP-címek

Előre nem látható viselkedéshez vezethet, átfedő IP-címtartományok használatakor. Például ha egy pod próbál meg hozzáférni a fürtön kívüli IP-címet, és hogy IP is történik a szolgáltatás IP-cím lehet, előfordulhat, hogy látni kiszámíthatatlan működést és hibákat.

**Kubernetes DNS szolgáltatás IP-cím**: a DNS szolgáltatás a fürt IP-cím. Ez a cím belül kell lennie a *Kubernetes szolgáltatás címtartomány*.

**Docker-híd cím**: IP-cím és a Docker-híd hozzárendelése hálózati maszkot. Az IP-címet nem lehet a virtuális hálózat IP-címtartomány a fürt belül.

## <a name="configure-networking---cli"></a>Hálózatkezelés – parancssori felület beállítása

Amikor egy AKS-fürtöt az Azure CLI-vel hoz létre, fejlett hálózatkezelési is konfigurálhatja. A következő parancsokat használja egy új AKS-fürt létrehozása engedélyezve van a speciális hálózati szolgáltatásokkal.

Először kérje le a meglévő alhálózat, amelybe tartományhoz fog csatlakozni az AKS-fürtöt az alhálózat erőforrás-azonosító:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Használja a [az aks létrehozása] [ az-aks-create] parancsot a `--network-plugin azure` fürt létrehozása a speciális hálózati argumentum. Frissítés a `--vnet-subnet-id` az alhálózati azonosító értékét az előző lépésben gyűjtött:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurálja a hálózati használata – portál

Az Azure Portalról az alábbi képernyőképen látható egy példa az AKS-fürt létrehozása során ezek a beállítások:

![Speciális hálózati konfiguráció az Azure Portalon][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

A alkalmazni az alábbi kérdések és válaszok a **speciális** hálózati konfiguráció.

* *Telepíthetek virtuális gépeket saját fürt alhálózat?*

  Nem. Virtuális gépek üzembe helyezéséhez a Kubernetes-fürt által használt alhálózaton nem támogatott. Virtuális gépek ugyanazon a virtuális hálózaton, de egy másik alhálózat is üzembe helyezhetők.

* *Konfigurálhatja a-pod hálózati házirendeket?*

  Nem. Pod hálózati házirendek olyan jelenleg nem támogatott.

* *Az üzembe helyezhető, amely konfigurálható a csomópontra a podok maximális számát?*

  Alapértelmezés szerint minden csomópont legfeljebb 30 podok is üzemeltethet. A maximális érték csak módosításával módosíthatja a `maxPods` tulajdonság egy fürtöt egy Resource Manager-sablon üzembe helyezésekor.

* *Hogyan konfigurálhatom az alhálózatot, amelyet az AKS-fürt létrehozása során létrehozott további tulajdonságok? Ha például a Szolgáltatásvégpontok.*

  A virtuális hálózatot és alhálózatot hoz létre az AKS-fürt létrehozása során, tulajdonságok teljes listáját a normál virtuális hálózatok közötti konfigurációs lapja az Azure Portalon konfigurálhatók.

## <a name="next-steps"></a>További lépések

### <a name="networking-in-aks"></a>Hálózatkezelés az aks-ben

További információ a hálózatkezelés az aks-ben a következő cikkeket:

[Az Azure Kubernetes Service (AKS) terheléselosztót statikus IP-cím használata](static-ip.md)

[Az Azure Container Service (AKS) bejövő HTTPS-forgalom](ingress.md)

[Belső terheléselosztó az Azure Container Service (AKS) használatához](internal-lb.md)

### <a name="acs-engine"></a>ACS Engine

[Az Azure Container Service-motor (ACS Engine)] [ acs-engine] egy nyílt forráskódú projekt, amely létrehozza az Azure Resource Manager-sablonok üzembe helyezése a Docker-kompatibilis fürtök az Azure-on is használhat. Kubernetes, DC/OS, Swarm módú és Swarm vezénylők telepíthetők az ACS-motor.

ACS Engine használatával létrehozott Kubernetes-fürtök támogatják mind a [kubenet] [ kubenet] és [Azure CNI] [ cni-networking] beépülő modulokat. ACS Engine, alapvető és speciális hálózati forgatókönyveket támogatja.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: aks-ssh.md
