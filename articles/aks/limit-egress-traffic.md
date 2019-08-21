---
title: Kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogy mely portokra és címekre van szükség a kimenő forgalom vezérléséhez az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: 369729f10de4a55cd14bb866795ea1aa15b3d9da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639784"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Előzetes verzió – a fürtcsomópontok kimenő forgalmának korlátozása és a szükséges portokhoz és szolgáltatásokhoz való hozzáférés szabályozása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkeznek. A hálózati hozzáférés ezen szintje lehetővé teszi, hogy a futtatott csomópontok és szolgáltatások igény szerint hozzáférhessenek a külső erőforrásokhoz. Ha korlátozni szeretné a kimenő forgalom forgalmát, a fürt kifogástalan karbantartási feladatainak megtartása érdekében korlátozott számú portot és címet kell elérhetővé tenni. A fürt ezután úgy van konfigurálva, hogy csak a Microsoft Container Registry (MCR) vagy a Azure Container Registry (ACR) alapszintű rendszertároló-lemezképeit használja, nem pedig a külső nyilvános adattárakat. A szükséges portok és címek engedélyezéséhez konfigurálnia kell az előnyben részesített tűzfalat és biztonsági szabályokat.

Ez a cikk részletesen ismerteti, hogy mely hálózati portok és teljes tartománynevek (FQDN-EK) szükségesek és választhatók, ha egy AK-fürtön korlátozza a kimenő forgalmat.  Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.66 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Ha olyan AK-fürtöt szeretne létrehozni, amely képes korlátozni a kimenő forgalom forgalmát, először engedélyezzen egy szolgáltatás-jelölőt az előfizetésében. Ez a szolgáltatás regisztrál minden olyan AK-fürtöt, amelyet Ön hoz létre a MCR vagy ACR alapszintű rendszertárolói lemezképek használatához. A *AKSLockingDownEgressPreview* szolgáltatás jelzőjét a következő példában látható módon regisztrálja az az [Feature Register][az-feature-register] parancs használatával:

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Kimenő forgalom áttekintése

A felügyeleti és működési célokra az AK-fürtök csomópontjainak bizonyos portokhoz és teljes tartománynevek (FQDN) eléréséhez kell rendelkezniük. Ezek a műveletek az API-kiszolgálóval való kommunikációra, illetve az alapvető Kubernetes-fürt összetevőinek és a csomópontok biztonsági frissítéseinek letöltésére és telepítésére is használhatók. Alapértelmezés szerint a kimenő (kimenő) internetes forgalom nem korlátozódik egy AK-fürt csomópontjaira. A fürt lehívhatja a rendszertároló lemezképeit a külső adattárakból.

Az AK-fürt biztonságának növeléséhez előfordulhat, hogy korlátozni szeretné a kimenő forgalmat. A fürt úgy van konfigurálva, hogy lekérje az alapszintű rendszertároló lemezképeit a MCR vagy az ACR-ből. Ha ily módon zárolja a kimenő forgalom forgalmát, meg kell határoznia bizonyos portokat és FQDN-ket, hogy az AK-csomópontok megfelelően kommunikáljanak a szükséges külső szolgáltatásokkal. Ezen engedélyezve portok és teljes tartománynevek nélkül az AK-csomópontok nem tudnak kommunikálni az API-kiszolgálóval, vagy nem telepíthetik az alapvető összetevőket.

A kimenő forgalom védelméhez [Azure Firewall][azure-firewall] vagy külső gyártótól származó tűzfal-berendezést használhat, és meghatározhatja a szükséges portokat és címeket. Az AK nem hozza létre automatikusan ezeket a szabályokat. A következő portok és címek hivatkoznak a megfelelő szabályok a hálózati tűzfalban való létrehozásakor.

> [!IMPORTANT]
> Ha Azure Firewall használatával korlátozza a kimenő forgalom forgalmát, és egy felhasználó által megadott útvonalat (UDR) hoz létre az összes kimenő forgalom kikényszerítéséhez, akkor győződjön meg arról, hogy megfelelő DNAT-szabályt hoz létre a tűzfalban, hogy megfelelően engedélyezze a bejövő forgalmat. A Azure Firewall használata UDR megszakítja a bejövő beállításokat az aszimmetrikus útválasztás miatt. (A probléma oka, hogy az AK-alhálózatnak van egy alapértelmezett útvonala, amely a tűzfal magánhálózati IP-címére mutat, de nyilvános terheléselosztó-bejövő vagy Kubernetes-szolgáltatást használ: Terheléselosztó). Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, a visszatérési útvonal azonban a tűzfal magánhálózati IP-címén halad át. Mivel a tűzfal állapot-nyilvántartó, eldobja a visszaadott csomagot, mert a tűzfal nem ismeri a létesített munkamenetet. Ha szeretné megtudni, hogyan integrálhatja a Azure Firewallt a bemenő vagy a Service Load balancerrel, tekintse meg a [Azure Firewall integrálása az Azure standard Load Balancer](https://docs.microsoft.com/en-us/azure/firewall/integrate-lb)-nal című
>

Az AK-ban két portot és címet kell kijelölni:

* Az [AK-fürtökhöz szükséges portok és címek](#required-ports-and-addresses-for-aks-clusters) részletesen részletezik a megengedett kimenő forgalomra vonatkozó minimális követelményeket.
* Az [AK-fürtök választható ajánlott címei és portjai](#optional-recommended-addresses-and-ports-for-aks-clusters) nem szükségesek az összes forgatókönyvhöz, de más szolgáltatásokkal, például Azure monitor való integráció nem fog megfelelően működni. Tekintse át a választható portok és a teljes tartománynevek listáját, és engedélyezze az AK-fürtben használt szolgáltatások és összetevők engedélyezését.

> [!NOTE]
> A kimenő forgalom korlátozása csak a funkció-jelölő regisztrációjának engedélyezése után létrehozott új AK-fürtökön működik. Meglévő fürtök esetén [hajtson végre egy fürt-frissítési műveletet][aks-upgrade] a paranccsal a `az aks upgrade` kimenő forgalom korlátozása előtt.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Szükséges portok és címek az AK-fürtökhöz

A következő kimenő portok/hálózati szabályok szükségesek egy AK-fürthöz:

* *443* -es TCP-port
* A *9000* -es TCP-port és a *22-es* TCP-port az alagút elülső Pod-portjához az API-kiszolgáló bújtatási végpontjának használatával folytatott kommunikációhoz.
    * További részletekért tekintse meg a * *. HCP.\< Location\>. azmk8s.IO* és * *. TUN.\< a\>location. azmk8s.IO* címei a következő táblázatban láthatók.

A következő teljes tartománynév/alkalmazás szabályok szükségesek:

| TELJES TARTOMÁNYNÉV                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *. HCP. \<Location\>. azmk8s.IO | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje  *\<le\> a helyet* azzal a régióval, ahol az AK-fürtöt üzembe helyezi. |
| *.tun.\<location\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje  *\<le\> a helyet* azzal a régióval, ahol az AK-fürtöt üzembe helyezi. |
| aksrepos.azurecr.io        | HTTPS:443 | Ez a címe Azure Container Registry (ACR) rendszerképeinek eléréséhez szükséges. |
| *.blob.core.windows.net    | HTTPS:443 | Ez a címe az ACR-ben tárolt rendszerképek háttér-tárolója. |
| mcr.microsoft.com          | HTTPS:443 | Ez a címe szükséges a rendszerképek eléréséhez a Microsoft Container Registryban (MCR). |
| *.cdn.mscr.io              | HTTPS:443 | Ez a címe az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| management.azure.com       | HTTPS:443 | Ez a címe a Kubernetes GET/PUT műveletekhez szükséges. |
| login.microsoftonline.com  | HTTPS:443 | Ez a címe Azure Active Directory hitelesítéshez szükséges. |
| api.snapcraft.io           | HTTPS:443, HTTP:80 | Ez a címnek a Linux-csomópontokon a Snap-csomagok telepítéséhez szükséges. |
| ntp.ubuntu.com             | UDP:123   | Ez a címe a Linux-csomópontok NTP-időszinkronizálásához szükséges. |
| *. docker.io                | HTTPS:443 | Ez a címe szükséges ahhoz, hogy lekérje a szükséges tárolók lemezképeit az alagút elejére. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Választható ajánlott címek és portok az AK-fürtökhöz

* *53* -es UDP-port a DNS-hez

A következő teljes tartománynév/alkalmazás-szabályok javasoltak az AK-fürtök megfelelő működéséhez:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Ez a címe lehetővé teszi, hogy a Linux-fürtcsomópontok letöltsék a szükséges biztonsági javításokat és frissítéseket. |
| packages.microsoft.com                  | HTTPS:443 | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez. |
| dc.services.visualstudio.com            | HTTPS:443 | Ajánlott a megfelelő metrikák és figyelési Azure Monitor használatával. |
| *.opinsights.azure.com                  | HTTPS:443 | Ajánlott a megfelelő metrikák és figyelési Azure Monitor használatával. |
| *.monitoring.azure.com                  | HTTPS:443 | Ajánlott a megfelelő metrikák és figyelési Azure Monitor használatával. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ez a címe a Azure Policy helyes működéséhez használatos (jelenleg előzetes verzióban az AK-ban). |
| apt.dockerproject.org                   | HTTPS:443 | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |
| nvidia.github.io                        | HTTPS:443 | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogy milyen portokat és címeket kell engedélyezni a fürt kimenő forgalmának korlátozására. Azt is meghatározhatja, hogy a hüvelyek hogyan kommunikálhatnak és milyen korlátozásokkal rendelkeznek a fürtön belül. További információ: [biztonságos forgalom a hüvelyek között a hálózati házirendek használatával az AK-ban][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
