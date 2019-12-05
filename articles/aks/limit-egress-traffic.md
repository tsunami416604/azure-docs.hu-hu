---
title: Kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogy mely portokra és címekre van szükség a kimenő forgalom vezérléséhez az Azure Kubernetes szolgáltatásban (ak)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/29/2019
ms.author: mlearned
ms.openlocfilehash: 208ffaa4c78e00031e41b6e2b8c01edb667b54a6
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481147"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>A fürtcsomópontok kimenő forgalmának szabályozása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkeznek. A hálózati hozzáférés ezen szintje lehetővé teszi, hogy a futtatott csomópontok és szolgáltatások igény szerint hozzáférhessenek a külső erőforrásokhoz. Ha korlátozni szeretné a kimenő forgalom forgalmát, a fürt kifogástalan karbantartási feladatainak megtartása érdekében korlátozott számú portot és címet kell elérhetővé tenni. A fürt alapértelmezés szerint úgy van konfigurálva, hogy csak a Microsoft Container Registry (MCR) vagy a Azure Container Registry (ACR) alapszintű rendszertároló-lemezképeit használja. Konfigurálja az előnyben részesített tűzfal-és biztonsági szabályokat, hogy engedélyezze ezeket a szükséges portokat és címeket.

Ez a cikk részletesen ismerteti, hogy mely hálózati portok és teljes tartománynevek (FQDN-EK) szükségesek és választhatók, ha egy AK-fürtön korlátozza a kimenő forgalmat.

> [!IMPORTANT]
> Ez a dokumentum csak azt ismerteti, hogyan lehet zárolni az AK-alhálózatot elhagyó forgalmat. Az AK nem rendelkezik bejövő követelményekkel.  A belső alhálózati forgalom blokkolása hálózati biztonsági csoportokkal (NSG) és tűzfalakkal nem támogatott. A fürtön belüli forgalom szabályozásához és letiltásához használja a [hálózati házirendeket][network-policy].

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.66 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="egress-traffic-overview"></a>Kimenő forgalom áttekintése

A felügyeleti és működési célokra az AK-fürtök csomópontjainak bizonyos portokhoz és teljes tartománynevek (FQDN) eléréséhez kell rendelkezniük. Ezek a műveletek az API-kiszolgálóval való kommunikációra, illetve az alapvető Kubernetes-fürt összetevőinek és a csomópontok biztonsági frissítéseinek letöltésére és telepítésére is használhatók. Alapértelmezés szerint a kimenő (kimenő) internetes forgalom nem korlátozódik egy AK-fürt csomópontjaira. A fürt lehívhatja a rendszertároló lemezképeit a külső adattárakból.

Az AK-fürt biztonságának növeléséhez előfordulhat, hogy korlátozni szeretné a kimenő forgalmat. A fürt úgy van konfigurálva, hogy lekérje az alapszintű rendszertároló lemezképeit a MCR vagy az ACR-ből. Ha ily módon zárolja a kimenő forgalom forgalmát, definiáljon bizonyos portokat és FQDN-ket, hogy az AK-csomópontok megfelelően kommunikáljanak a szükséges külső szolgáltatásokkal. Ezen engedélyezve portok és teljes tartománynevek nélkül az AK-csomópontok nem tudnak kommunikálni az API-kiszolgálóval, vagy nem telepíthetik az alapvető összetevőket.

A kimenő forgalom védelméhez [Azure Firewall][azure-firewall] vagy külső gyártótól származó tűzfal-berendezést használhat, és meghatározhatja a szükséges portokat és címeket. Az AK nem hozza létre automatikusan ezeket a szabályokat. A következő portok és címek hivatkoznak a megfelelő szabályok a hálózati tűzfalban való létrehozásakor.

> [!IMPORTANT]
> Ha Azure Firewall használatával korlátozza a kimenő forgalom forgalmát, és egy felhasználó által megadott útvonalat (UDR) hoz létre az összes kimenő forgalom kikényszerítéséhez, akkor győződjön meg arról, hogy megfelelő DNAT-szabályt hoz létre a tűzfalban, hogy megfelelően engedélyezze a bejövő forgalmat. A Azure Firewall használata UDR megszakítja a bejövő beállításokat az aszimmetrikus útválasztás miatt. (A probléma akkor fordul elő, ha az AK-alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére mutat, de a következő típusú nyilvános terheléselosztó-bejövő vagy Kubernetes-szolgáltatást használja: terheléselosztó). Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, a visszatérési útvonal azonban a tűzfal magánhálózati IP-címén halad át. Mivel a tűzfal állapot-nyilvántartó, eldobja a visszaadott csomagot, mert a tűzfal nem ismeri a létesített munkamenetet. Ha szeretné megtudni, hogyan integrálhatja a Azure Firewallt a bemenő vagy a Service Load balancerrel, tekintse meg a [Azure Firewall integrálása az Azure standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb)-nal című
> A 9000-es TCP-port és a 22-es TCP-port forgalmát a kimenő munkavégző csomópont IP-címei és az API-kiszolgáló IP-címe közötti hálózati szabály használatával zárolhatja.

Az AK-ban két portot és címet kell kijelölni:

* Az [AK-fürtökhöz szükséges portok és címek](#required-ports-and-addresses-for-aks-clusters) részletesen részletezik a megengedett kimenő forgalomra vonatkozó minimális követelményeket.
* Az [AK-fürtök választható ajánlott címei és portjai](#optional-recommended-addresses-and-ports-for-aks-clusters) nem szükségesek az összes forgatókönyvhöz, de más szolgáltatásokkal, például Azure monitor való integráció nem fog megfelelően működni. Tekintse át a választható portok és a teljes tartománynevek listáját, és engedélyezze az AK-fürtben használt szolgáltatások és összetevők engedélyezését.

> [!NOTE]
> A kimenő forgalom korlátozása csak az új AK-fürtökön működik. Meglévő fürtök esetén [hajtson végre egy fürt-frissítési műveletet][aks-upgrade] a `az aks upgrade` parancs használatával, mielőtt korlátozza a kimenő forgalmat.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Szükséges portok és címek az AK-fürtökhöz

A következő kimenő portok/hálózati szabályok szükségesek egy AK-fürthöz:

* *443* -es TCP-port
* TCP [IPAddrOfYourAPIServer]: a 443-as verzióra akkor van szükség, ha olyan alkalmazással rendelkezik, amelynek az API-kiszolgálóval kell kommunikálnia.  Ezt a módosítást a fürt létrehozása után lehet beállítani.
* A *9000* -es TCP-port és a *22-es* TCP-port az alagút elülső Pod-portjához az API-kiszolgáló bújtatási végpontjának használatával folytatott kommunikációhoz.
    * További részletekért tekintse meg a * *. HCP.\<helyet\>. azmk8s.IO* és * *. TUN.\<hely\>. azmk8s.IO* címeket a következő táblázatban.
* A DNS esetében a *53* -es UDP-portra akkor is szükség van, ha közvetlenül az API-kiszolgálóhoz fér hozzá.

A következő teljes tartománynév/alkalmazás szabályok szükségesek:
- Azure globális

| TELJES TARTOMÁNYNÉV                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *. HCP.\<hely\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| *. TUN.\<hely\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| aksrepos.azurecr.io        | HTTPS:443 | Ez a címe Azure Container Registry (ACR) rendszerképeinek eléréséhez szükséges. Ez a beállításjegyzék külső gyártótól származó lemezképeket/diagramokat (például metrikai kiszolgálót, alapszintű DNS-t stb.) tartalmaz a fürt működéséhez a frissítés és a fürt skálázása során.|
| *.blob.core.windows.net    | HTTPS:443 | Ez a címe az ACR-ben tárolt rendszerképek háttér-tárolója. |
| mcr.microsoft.com          | HTTPS:443 | Ez a címe szükséges a rendszerképek eléréséhez a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza a fürt működéséhez szükséges, az első féltől származó lemezképeket és diagramokat (például a Moby stb.). |
| *.cdn.mscr.io              | HTTPS:443 | Ez a címe az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| management.azure.com       | HTTPS:443 | Ez a címe a Kubernetes GET/PUT műveletekhez szükséges. |
| login.microsoftonline.com  | HTTPS:443 | Ez a címe Azure Active Directory hitelesítéshez szükséges. |
| ntp.ubuntu.com             | UDP:123   | Ez a címe a Linux-csomópontok NTP-időszinkronizálásához szükséges. |
| packages.microsoft.com     | HTTPS:443 | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
| acs-mirror.azureedge.net   | HTTPS:443 | Ez a címe a szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárház. |
- Azure China

| TELJES TARTOMÁNYNÉV                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *. HCP.\<hely\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| *. TUN.\<hely\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| *. azk8s.cn        | HTTPS:443 | Ez a címe szükséges a szükséges bináris fájlok és lemezképek letöltéséhez|
| mcr.microsoft.com          | HTTPS:443 | Ez a címe szükséges a rendszerképek eléréséhez a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza a fürt működéséhez szükséges, az első féltől származó lemezképeket és diagramokat (például a Moby stb.). |
| *.cdn.mscr.io              | HTTPS:443 | Ez a címe az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| management.chinacloudapi.cn       | HTTPS:443 | Ez a címe a Kubernetes GET/PUT műveletekhez szükséges. |
| login.chinacloudapi.cn  | HTTPS:443 | Ez a címe Azure Active Directory hitelesítéshez szükséges. |
| ntp.ubuntu.com             | UDP:123   | Ez a címe a Linux-csomópontok NTP-időszinkronizálásához szükséges. |
| packages.microsoft.com     | HTTPS:443 | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
- Azure Government

| TELJES TARTOMÁNYNÉV                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *. HCP.\<hely\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| *. TUN.\<hely\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Ez a címe az API-kiszolgáló végpontja. Cserélje le *\<helyet\>* arra a régióra, ahol az AK-fürtöt üzembe helyezi. |
| aksrepos.azurecr.io        | HTTPS:443 | Ez a címe Azure Container Registry (ACR) rendszerképeinek eléréséhez szükséges. Ez a beállításjegyzék külső gyártótól származó lemezképeket/diagramokat (például metrikai kiszolgálót, alapszintű DNS-t stb.) tartalmaz a fürt működéséhez a frissítés és a fürt skálázása során.|
| *.blob.core.windows.net    | HTTPS:443 | Ez a címe az ACR-ben tárolt rendszerképek háttér-tárolója. |
| mcr.microsoft.com          | HTTPS:443 | Ez a címe szükséges a rendszerképek eléréséhez a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza a fürt működéséhez szükséges, az első féltől származó lemezképeket és diagramokat (például a Moby stb.). |
| *.cdn.mscr.io              | HTTPS:443 | Ez a címe az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| management.usgovcloudapi.net       | HTTPS:443 | Ez a címe a Kubernetes GET/PUT műveletekhez szükséges. |
| login.microsoftonline.us  | HTTPS:443 | Ez a címe Azure Active Directory hitelesítéshez szükséges. |
| ntp.ubuntu.com             | UDP:123   | Ez a címe a Linux-csomópontok NTP-időszinkronizálásához szükséges. |
| packages.microsoft.com     | HTTPS:443 | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
| acs-mirror.azureedge.net   | HTTPS:443 | Ez a címe a szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárház. |
## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Választható ajánlott címek és portok az AK-fürtökhöz

A következő kimenő portok/hálózati szabályok nem kötelezőek egy AK-fürthöz:

A következő teljes tartománynév/alkalmazás-szabályok javasoltak az AK-fürtök megfelelő működéséhez:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Ez a címe lehetővé teszi, hogy a Linux-fürtcsomópontok letöltsék a szükséges biztonsági javításokat és frissítéseket. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Szükséges címek és portok a GPU-t támogató AK-fürtökhöz

A GPU-t használó AK-fürtök esetében a következő teljes tartománynév/alkalmazás szabályok szükségesek:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |
| us.download.nvidia.com | HTTPS:443 | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |
| apt.dockerproject.org | HTTPS:443 | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Szükséges címek és portok Azure Monitor for containers engedélyezve

A következő teljes tartománynevek és alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van az Azure Monitor a tárolók számára:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Ez a megfelelő mérőszámok és figyelési telemetria Azure Monitor használatával. |
| *.ods.opinsights.azure.com    | HTTPS:443 | Ezt az Azure Monitor használja a log Analytics-adatfeldolgozáshoz. |
| *.oms.opinsights.azure.com | HTTPS:443 | Ezt a címeket a omsagent használja, amely a log Analytics szolgáltatás hitelesítésére szolgál. |
|*.microsoftonline.com | HTTPS:443 | Ezt a rendszer a metrikák Azure Monitor való hitelesítésére és küldésére használja. |
|*.monitoring.azure.com | HTTPS:443 | Ez a metrikai adatok Azure Monitorba küldésére szolgál. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Szükséges címek és portok engedélyezve az Azure dev Spaces szolgáltatással

A következő teljes tartománynév/alkalmazás szabályok szükségesek azon AK-fürtökhöz, amelyeken engedélyezve van az Azure dev Spaces:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Ez a címe a linuxos alpesi és egyéb Azure dev Spaces-rendszerképek lekérésére szolgál |
| gcr.io | HTTP: 443 | Ez a címe a Helm/Tiller-képek lekérésére szolgál |
| storage.googleapis.com | HTTP: 443 | Ez a címe a Helm/Tiller-képek lekérésére szolgál |
| azds –<guid>.<location>. azds.io | HTTPS:443 | Kommunikáció az Azure dev Spaces háttér-szolgáltatásaival a vezérlőhöz. A pontos FQDN a (z) "dataplaneFqdn" elemben található a következőben:% felhasználói név%\.azds\settings.JSON |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Szükséges címek és portok az AK-fürtökhöz Azure Policy (nyilvános előzetes verzióban) engedélyezve

> [!CAUTION]
> Az alábbi funkciók némelyike előzetes verzióban érhető el.  A cikkben szereplő javaslatok változhatnak, mivel a szolgáltatás a nyilvános előzetes verzióra és a későbbi kiadási szakaszokra helyezi át a szolgáltatást.

A következő teljes tartománynevek/alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van a Azure Policy.

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ez a címe Azure Policy helyes működéséhez használatos. (jelenleg előzetes verzióban érhető el az AK-ban) |
| raw.githubusercontent.com | HTTPS:443 | Ez a címe a beépített szabályzatok GitHubról történő lekérésére szolgál a Azure Policy megfelelő működésének biztosítása érdekében. (jelenleg előzetes verzióban érhető el az AK-ban) |
| *. GK.<location>. azmk8s.io | HTTPS:443 | Az Azure Policy bővítmény a főkiszolgálón futó forgalomirányító végpontot tárgyalja a naplózási eredmények beszerzéséhez. |
| dc.services.visualstudio.com | HTTPS:443 | Az Azure Policy bővítmény telemetria adatokat küld az Applications-elemzések végpontjának. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>A Windows Server-alapú csomópontok (nyilvános előzetes verzió) által igényelt engedélyezve

> [!CAUTION]
> Az alábbi funkciók némelyike előzetes verzióban érhető el.  A cikkben szereplő javaslatok változhatnak, mivel a szolgáltatás a nyilvános előzetes verzióra és a későbbi kiadási szakaszokra helyezi át a szolgáltatást.

A Windows Server-alapú AK-fürtökhöz a következő FQDN-/alkalmazás-szabályok szükségesek:

| TELJES TARTOMÁNYNÉV                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | A Windows rendszerhez kapcsolódó bináris fájlok telepítése |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | A Windows rendszerhez kapcsolódó bináris fájlok telepítése |
| kms.core.windows.net | TCP: 1688 | A Windows rendszerhez kapcsolódó bináris fájlok telepítése |


## <a name="next-steps"></a>Következő lépések

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
