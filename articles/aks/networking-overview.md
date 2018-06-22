---
title: Hálózati konfiguráció Azure Kubernetes szolgáltatás (AKS)
description: További információk a alapvető és speciális hálózati konfiguráció Azure Kubernetes szolgáltatás (AKS).
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/15/2018
ms.author: marsma
ms.openlocfilehash: 207accc30e10c4e2bed5b713fc59e2f9ad86a876
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309847"
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>Hálózati konfiguráció Azure Kubernetes szolgáltatás (AKS)

Amikor Azure Kubernetes szolgáltatás (AKS) fürtöt hoz létre, két hálózati lehetőségek közül választhat: **alapvető** vagy **speciális**.

## <a name="basic-networking"></a>Alapvető hálózat

A **alapvető** AKS fürt létrehozása az alapértelmezett konfigurációjának hálózati lehetőség. A hálózati konfigurációt a fürt és a három munkaállomás-csoporttal Azure teljesen kezeli, és központi telepítések, amelyek nem igényelnek egyéni VNet konfigurációja megfelelő. Ellenőrzése alatt tartja a hálózati konfiguráció nem rendelkeznek, például alhálózatok vagy az IP-címtartományok, a fürthöz rendelt alapvető hálózat kiválasztásakor.

Alapszintű hálózatkezelési használatra konfigurált AKS fürtben lévő csomópontok a [kubenet] [ kubenet] Kubernetes beépülő modul.

## <a name="advanced-networking"></a>Speciális hálózatkezelés

**Speciális** hálózat az Azure Virtual Network (VNet), amely konfigurál, biztosítható, azokat a virtuális hálózat erőforrások automatikus kapcsolat helyezi a három munkaállomás-csoporttal, és a gazdag integrációs képességeket állítsa be a Vnetek ajánlat.
Speciális hálózati akkor használható, ha a fürtök üzembe helyezése AKS a [Azure-portálon][portal], az Azure parancssori felület, vagy a Resource Manager sablonnal.

Speciális hálózati használatra konfigurált AKS fürtben lévő csomópontok a [Azure tároló hálózati illesztő (CNI)] [ cni-networking] Kubernetes beépülő modul.

![A csatlakozás egymástól független egyetlen Azure VNet hidak két csomópont bemutató ábra][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>Speciális hálózati szolgáltatások

Speciális hálózatkezelés az alábbi előnyöket biztosítja:

* Az egy meglévő virtuális hálózatot a AKS fürt központi telepítése, vagy hozzon létre egy új virtuális hálózat és a fürt IP-alhálózatot.
* A fürt minden tok-hozzá van rendelve a virtuális IP-címet, és közvetlenül kommunikálhatnak a fürt más három munkaállomás-csoporttal és más csomópontok a Vneten belül.
* Egy pod peered Vneten található más szolgáltatásokkal, és a helyszíni hálózatokhoz protokollon keresztül is kapcsolódhatnak ExpressRoute és webhelyek (közötti S2S) VPN-kapcsolatokat. A helyszíni három munkaállomás-csoporttal is érhetők el.
* Egy Kubernetes szolgáltatás indításához külső vagy belső az Azure Load Balancer keresztül. Is szolgáltatása alapszintű hálózatkezelési.
* Három munkaállomás-csoporttal az alhálózat, amelyeken engedélyezve Szolgáltatásvégpontok biztonságosan csatlakozhat az Azure-szolgáltatások, például az Azure Storage és az SQL-adatbázis.
* Használja a felhasználó által definiált útvonalakat (UDR) forgalom virtuális készülékre hálózati három munkaállomás-csoporttal.
* Három munkaállomás-csoporttal férhetnek hozzá a nyilvános interneten megtalálható erőforrásokhoz. Is szolgáltatása alapszintű hálózatkezelési.

> [!IMPORTANT]
> Egy speciális hálózati tárolhatja, legfeljebb konfigurált AKS fürt minden csomópontja **30 három munkaállomás-csoporttal** konfigurálásakor az Azure portál használatával.  Módosíthatja a maximális érték csak a maxPods tulajdonság módosítása a fürtben egy erőforrás-kezelő sablon telepítése során. Minden egyes virtuális hálózat üzembe helyezve az Azure CNI beépülő modul használatára pedig csak **4096 kiválasztott IP-címek**.

## <a name="advanced-networking-prerequisites"></a>Speciális hálózati Előfeltételek

* A VNet a AKS fürt engedélyeznie kell a kimenő internetkapcsolat.
* Ne hozzon létre több AKS fürt ugyanazon az alhálózaton.
* A AKS speciális hálózati nem támogatja a Vnetek használó Azure saját DNS-zónák.
* AKS fürt nem használhatja `169.254.0.0/16`, `172.30.0.0/16`, vagy `172.31.0.0/16` a Kubernetes szolgáltatás a címtartományt.
* Rendelkeznie kell a szolgáltatás egyszerű a AKS fürt `Contributor` engedélyeket a meglévő VNet tartalmazó erőforráscsoportot.

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzés tervezése

Speciális hálózati konfigurált fürtök megkövetelik, hogy további tervezésre. A virtuális hálózat és az alhálózat méretét kell elhelyezni az is három munkaállomás-csoporttal szeretné futtatni a számát, valamint a fürt a csomópontok számát.

A Vneten belül a megadott alhálózat IP-címet a három munkaállomás-csoporttal és a fürtcsomópontok rendeli. Minden csomópont van konfigurálva egy elsődleges IP-cím, amelyen a csomópont és 30 további IP-címek Azure CNI által előre konfigurálva van a csomópont ütemezett három munkaállomás-csoporttal társított IP. Ha a fürt a horizontális, minden csomópont hasonlóan az alhálózatból származó IP-címekkel rendelkező van beállítva.

Egy AKS fürt IP-cím tervezése áll egy Vnetet, a csomópont és három munkaállomás-csoporttal, legalább egy alhálózatot és egy Kubernetes szolgáltatás-címtartományt.

| Címtartomány / az Azure erőforrás | Korlátozásai és méretezése |
| --------- | ------------- |
| Virtuális hálózat | Lehet, hogy Azure VNet akkora, mint /8, de előfordulhat, hogy csak 4096 konfigurált IP-címeket. |
| Alhálózat | A csomópont és három munkaállomás-csoporttal elég nagynak kell lennie. A minimális alhálózati méretének kiszámításához: (csomópontok száma) + (csomópontok száma * három munkaállomás-csoporttal csomópontonként). Egy 50 csomópontot tartalmazó fürtben: (50) + (50 * 30) = 1,550, az alhálózaton kell lennie a /21 vagy nagyobb. |
| Kubernetes szolgáltatás címtartománya | Ez a tartomány nem kell minden hálózati elem által használt vagy ez a virtuális hálózat csatlakozik. Szolgáltatás címe CIDR /12 kisebbnek kell lennie. |
| Kubernetes DNS szolgáltatás IP-címe | IP-címnek a Kubernetes szolgáltatás-címtartományt, amely a fürtszolgáltatás felderítése (kube-dns) által használható. |
| Docker híd cím | IP-címet (a CIDR jelölésrendszer) használja a Docker hidat IP-cím csomópontján. Alapértelmezett 172.17.0.1/16. |

Mivel minden egyes virtuális hálózat üzembe helyezve az Azure CNI beépülő modul használatára pedig csak a korábban említett **4096 kiválasztott IP-címek**. Speciális hálózati tárolhatja, legfeljebb konfigurálva a fürt minden csomópontja **30 három munkaállomás-csoporttal**.

## <a name="deployment-parameters"></a>Telepítési paraméterek

Mikor hozzon létre egy AKS fürtöt, a következő paraméterek nem konfigurálható a speciális hálózati:

**Virtuális hálózati**: A virtuális hálózat, amelybe át kívánja a Kubernetes fürt központi telepítése. Ha szeretne létrehozni egy új virtuális hálózat a fürt számára, jelölje be *hozzon létre új* és kövesse a *virtuális hálózat létrehozása* szakasz.

**Alhálózati**: az alhálózat, ahol a fürt telepíteni szeretné a Vneten belül. Ha a fürt virtuális egy új alhálózatot létrehozni, jelölje be *hozzon létre új* és kövesse a a *hozzon létre alhálózatot* szakasz.

**Kubernetes szolgáltatás címtartomány**: A *Kubernetes szolgáltatás címtartomány* van az IP-címtartomány, ahonnan címek rendelt Kubernetes szolgáltatások a fürtben (Kubernetes szolgáltatásokra vonatkozó további információkért lásd: [ Szolgáltatások] [ services] a Kubernetes dokumentációjának).

A Kubernetes szolgáltatás IP-címtartomány:

* Nem lehet a virtuális hálózat IP-címtartomány a fürt
* Nem lehet átfedésben a bármely más Vnetekről, amellyel a fürt virtuális hálózat állomásokhoz
* Nem lehet átfedésben a bármely helyi IP-címek

Előre nem látható viselkedéshez vezethet, átfedő IP-címtartományok használatakor. Például ha egy pod próbál meg hozzáférni a fürtön kívüli olyan IP-címet, és hogy IP is előfordulhat, hogy egy szolgáltatás IP-cím, megjelenhet kiszámíthatatlan működést és hibákat.

**Kubernetes DNS szolgáltatás IP-címe**: a DNS szolgáltatás a fürt IP-cím. Ez a cím belül kell lennie a *Kubernetes szolgáltatás címtartomány*.

**Docker híd cím**: IP-cím és a Docker híd hozzárendelése hálózati maszkot. Az IP-cím nem a virtuális hálózat IP-címtartomány a fürt belül kell lennie.

## <a name="configure-networking---cli"></a>Hálózatkezelés – CLI beállítása

Amikor az Azure parancssori felülettel AKS fürtöt hoz létre, konfigurálhatja a speciális hálózati is. A következő parancsok segítségével hozzon létre egy új AKS fürtöt továbbfejlesztett hálózati szolgáltatások engedélyezve van.

Az alhálózati erőforrás-azonosító először érhető el a létező alhálózatot, amelybe tartományhoz fog csatlakozni a AKS fürt:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Használja a [az aks létrehozása] [ az-aks-create] parancsot a `--network-plugin azure` argumentum a speciális hálózati hozzon létre egy fürtöt. Frissítés a `--vnet-subnet-id` az előző lépésben összegyűjtött az alhálózati azonosító értéket:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurálja a hálózat - portál

Az alábbi képernyőfelvételen az Azure portálról AKS fürt létrehozása során ezek a beállítások konfigurálása példáját mutatja be:

![Speciális hálózati konfiguráció az Azure-portálon][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok érvényesek a **speciális** olyan hálózati beállításokat.

* *Speciális hálózati beállítható az Azure parancssori felület segítségével?*

  Nem. Speciális hálózati érhető el jelenleg csak ha AKS telepítése fürtöket az Azure portálon vagy a Resource Manager sablonnal.

* *Telepíthető virtuális gépek a fürt alhálózat?*

  Nem. Az alhálózat, a Kubernetes fürt által használt virtuális gépek telepítése nem támogatott. Virtuális gépek ugyanazt a virtuális hálózatot, de egy másik alhálózat telepítésére.

* *Konfigurálhatja a-pod hálózati házirendeket?*

  Nem. Pod hálózati házirendek jelenleg nem támogatott.

* *Legfeljebb három munkaállomás-csoporttal telepíthető konfigurálható csomópont van?*

  Alapértelmezés szerint minden egyes csomópontokra legfeljebb 30 három munkaállomás-csoporttal. A maximális érték csak módosításával módosítható a `maxPods` tulajdonság a fürtben egy erőforrás-kezelő sablon telepítése során.

* *Hogyan konfigurálhatók a további tulajdonságokat a(z) alhálózatra, AKS fürt létrehozása során létrehozott? Például Szolgáltatásvégpontok.*

  A virtuális hálózat és-alhálózatok AKS fürt létrehozása során létrehozott tulajdonságok teljes listáját a normál virtuális hálózat konfigurálása lapon az Azure-portálon konfigurálható.

## <a name="next-steps"></a>További lépések

### <a name="networking-in-aks"></a>A hálózatkezelés AKS

További tudnivalók a következő cikkekben AKS Hálózatkezelés:

[Egy statikus IP-címet használ az Azure Kubernetes szolgáltatás (AKS) terheléselosztó](static-ip.md)

[HTTPS érkező Azure tároló szolgáltatás (AKS)](ingress.md)

[A belső terheléselosztók használata Azure tároló szolgáltatás (AKS)](internal-lb.md)

### <a name="acs-engine"></a>Az ACS-motor

[Az Azure tároló szolgáltatás vezérlőprogramja (ACS motor)] [ acs-engine] nyílt forráskódú projekt, amely is használhatja a fürtök Docker-kompatibilis Azure telepítése Azure Resource Manager-sablonok hoz létre. Az ACS-motor Kubernetes, a DC/OS, a Swarm-mód és a Swarm orchestrators üzembe helyezhetők.

Az ACS-motor létrehozott Kubernetes fürtök támogatja a [kubenet] [ kubenet] és [Azure CNI] [ cni-networking] beépülő modulok. Ilyen alapvető és speciális hálózati forgatókönyv ACS motor által támogatott.

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
