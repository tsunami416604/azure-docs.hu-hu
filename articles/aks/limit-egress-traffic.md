---
title: Kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, milyen portokra és címekre van szükség a kimenő forgalom szabályozásához az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080185"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>A fürtcsomópontok kimenő forgalomának szabályozása az Azure Kubernetes-szolgáltatásban (AKS)

Alapértelmezés szerint az AKS-fürtök korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkeznek. Ez a hálózati hozzáférési szint lehetővé teszi, hogy a futtatott csomópontok és szolgáltatások szükség szerint hozzáférjenek a külső erőforrásokhoz. Ha korlátozni szeretné a kimenő forgalom, korlátozott számú portés cím elérhetőnek kell lennie a megfelelő fürtkarbantartási feladatok fenntartása érdekében. A fürt alapértelmezés szerint úgy van beállítva, hogy csak a Microsoft Container Registry (MCR) vagy az Azure Container Registry (ACR) alaprendszertároló-lemezképeit használja. Konfigurálja az előnyben részesített tűzfal- és biztonsági szabályokat úgy, hogy engedélyezze ezeket a szükséges portokat és címeket.

Ez a cikk részletezi, hogy milyen hálózati portok és teljesen minősített tartománynevek (FQDNs) szükségesek, és nem kötelező, ha korlátozza a kimenő forgalom egy AKS-fürtben.

> [!IMPORTANT]
> Ez a dokumentum csak az AKS alhálózatot elhagyó forgalom zárolását ismerteti. Az AKS-nek nincsenek be- és be- és visszakerítési követelményei.  A belső alhálózati forgalom blokkolása hálózati biztonsági csoportok (NSG-k) és tűzfalak használatával nem támogatott. A fürtön belüli forgalom szabályozásához és blokkolásához használja a Hálózati házirendek című [kiszolgálót.][network-policy]

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.66-os vagy újabb verziójára van szükség telepítve és konfigurálva. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="egress-traffic-overview"></a>Kimenő forgalom – áttekintés

Felügyeleti és üzemeltetési célokra az AKS-fürt csomópontjainak bizonyos portokhoz és teljesen minősített tartománynevekhez (FQDN) kell hozzáférniük. Ezek a műveletek lehetnek az API-kiszolgálóval való kommunikáció, vagy a Kubernetes fürt alapvető összetevőinek és csomópontjainak biztonsági frissítéseinek letöltése és telepítése. Alapértelmezés szerint a kimenő (kimenő) internetes forgalom nincs korlátozva az AKS-fürt csomópontjai számára. A fürt lekérheti az alaprendszer tárolólemezképeit a külső tárolókból.

Az AKS-fürt biztonságának növelése érdekében érdemes lehet korlátozni a kimenő forgalom. A fürt úgy van beállítva, hogy lekéri az alaprendszertároló-lemezképeket az MCR vagy az ACR rendszerből. Ha ily módon zárolja a kimenő forgalmat, definiáljon konkrét portokat és teljes tartománynnakat, hogy az AKS-csomópontok megfelelően kommunikálhassanak a szükséges külső szolgáltatásokkal. Ezek nélkül az engedélyezett portok és teljes tartománynnák nélkül az AKS-csomópontok nem tudnak kommunikálni az API-kiszolgálóval, és nem tudnak fő összetevőket telepíteni.

Az [Azure Firewall][azure-firewall] vagy egy külső tűzfalberendezés használatával biztosíthatja a kimenő forgalom, és meghatározza ezeket a szükséges portok és címek. Az AKS nem hozza létre automatikusan ezeket a szabályokat. A következő portok és címek a hálózati tűzfalmegfelelő szabályainak létrehozásakor tekinthetők meg.

> [!IMPORTANT]
> Ha az Azure Firewall használatával korlátozza a kimenő forgalom, és hozzon létre egy felhasználó által definiált útvonal (UDR) az összes kimenő forgalom kényszerítése, győződjön meg arról, hogy hozzon létre egy megfelelő DNST-szabályt a tűzfalon, hogy megfelelően engedélyezze a bejövő forgalom. Az Azure Firewall udr használatával megszakítja a be- és átvezetés beállítását az aszimmetrikus útválasztás miatt. (A probléma akkor fordul elő, ha az AKS-alhálózat rendelkezik egy alapértelmezett útvonal, amely a tűzfal privát IP-címét, de használ egy nyilvános terheléselosztó - be- vagy Kubernetes szolgáltatás típusa: LoadBalancer). Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, de a visszatérési útvonal a tűzfal privát IP-címén keresztül történik. Mivel a tűzfal állapotalapú, eldobja a visszatérő csomagot, mert a tűzfal nem tud egy meghatározott munkamenetről. Az Azure Firewall integrálása a be- és információterhelés-elosztóval az [Azure Firewall integrálása az Azure Standard Load Balancer szolgáltatással(Integrálása)](https://docs.microsoft.com/azure/firewall/integrate-lb)témakörben olvashat.
> A 9000-es, A 22-es TCP-port és az 1194-es UDP-port forgalmát a kimenő munkavégző csomópont IP-címe és az API-kiszolgáló IP-címe közötti hálózati szabály segítségével zárolhatja.

Az AKS-ben két port- és címcsoport van:

* Az [AKS-fürtök szükséges portjai és címe](#required-ports-and-addresses-for-aks-clusters) az engedélyezett kimenő forgalom minimális követelményeit részletezi.
* Az [AKS-fürtök opcionális ajánlott címei és portjai](#optional-recommended-addresses-and-ports-for-aks-clusters) nem minden esetben szükségesek, de más szolgáltatásokkal, például az Azure Monitornal való integráció nem fog megfelelően működni. Tekintse át a választható portok és teljes tartománynnak listáját, és engedélyezze az AKS-fürtben használt szolgáltatások és összetevők bármelyikét.

> [!NOTE]
> A kimenő forgalom korlátozása csak az új AKS-fürtökön működik. Meglévő fürtök esetén [hajtson végre egy fürtfrissítési műveletet][aks-upgrade] a `az aks upgrade` paranccsal, mielőtt korlátozná a kimenő forgalmat.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Az AKS-fürtökhöz szükséges portok és címek

Az AKS-fürtökhöz a következő kimenő portokra/hálózati szabályokra van szükség:

* *443-as* TCP-port
* TCP [IPAddrOfYourAPIServer]:443 szükséges, ha olyan alkalmazással rendelkezik, amelynek beszélnie kell az API-kiszolgálóval.  Ez a módosítás a fürt létrehozása után állítható be.
* TCP-port *9000*, TCP port *22* és UDP port *1194* a bújtatási front pod kommunikálni a bújtatás végén az API-kiszolgálón.
    * Pontosabb an- és részletesebb információkért tekintse meg a **.hcp.\< helyen\>.azmk8s.io* és **.tun.\< az\>* alábbi táblázatban .azmk8s.io címet.
* *123-as* UDP-port az NTP-idő szinkronizálásához (Linux-csomópontok).
* A *DNS-hez* szükséges 53-as UDP-port akkor is szükséges, ha a podok közvetlenül hozzáférnek az API-kiszolgálóhoz.

A következő Teljes tartomány- és alkalmazásszabályok szükségesek:

> [!IMPORTANT]
> *A ki- és kilépési zároláshoz már nincs szükség **.blob.core.windows.net és aksrepos.azurecr.io.**  Meglévő fürtök esetén [hajtson végre egy fürtfrissítési műveletet][aks-upgrade] a paranccsal a `az aks upgrade` szabályok eltávolításához.

> [!IMPORTANT]
> *.cdn.mscr.io az Azure nyilvános felhőrégióiban a *.data.mcr.microsoft.com váltotta fel. A módosítások életbe léptetéséhez frissítse a meglévő tűzfalszabályokat.

- Azure Globális

| FQDN                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *.hcp. \<hely\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| *.tun. \<hely\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| *.cdn.mscr.io       | HTTPS:443 | Ez a cím az Azure Content Delivery Network (CDN) által támogatott MCR-tárolóhoz szükséges. |
| mcr.microsoft.com          | HTTPS:443 | Ez a cím szükséges a Microsoft Container Registry (MCR) lemezképeinek eléréséhez. Ez a rendszerleíró adatbázis a fürt frissítés és a fürt mérete során a fürt működéséhez szükséges külső képeket/diagramokat (például moby stb.) tartalmazza. |
| *.data.mcr.microsoft.com             | HTTPS:443 | Ez a cím szükséges az Azure tartalomkézbesítési hálózat (CDN) által támogatott MCR-tárolóhoz. |
| management.azure.com       | HTTPS:443 | Ez a cím szükséges a Kubernetes GET/PUT műveletekhez. |
| login.microsoftonline.com  | HTTPS:443 | Ez a cím szükséges az Azure Active Directory-hitelesítéshez. |
| ntp.ubuntu.com             | UDP:123   | Ez a cím szükséges az NTP időszinkronizálásához Linux csomópontokon. |
| packages.microsoft.com     | HTTPS:443 | Ez a cím a Microsoft csomagok tárháza, amelyet a gyorsítótárazott *apt-get* műveletekhez használnak.  Példa csomagok közé tartozik a Moby, a PowerShell és az Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Ez a cím a szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárházhoz szükséges. |

- Azure China 21Vianet

| FQDN                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *.hcp. \<hely\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| *.tun. \<hely\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| *.azk8s.cn        | HTTPS:443 | Ez a cím szükséges a szükséges bináris fájlok és képek letöltéséhez|
| mcr.microsoft.com          | HTTPS:443 | Ez a cím szükséges a Microsoft Container Registry (MCR) lemezképeinek eléréséhez. Ez a rendszerleíró adatbázis a fürt frissítés és a fürt mérete során a fürt működéséhez szükséges külső képeket/diagramokat (például moby stb.) tartalmazza. |
| *.cdn.mscr.io       | HTTPS:443 | Ez a cím az Azure Content Delivery Network (CDN) által támogatott MCR-tárolóhoz szükséges. |
| *.data.mcr.microsoft.com             | HTTPS:443 | Ez a cím szükséges az Azure tartalomkézbesítési hálózat (CDN) által támogatott MCR-tárolóhoz. |
| management.chinacloudapi.cn       | HTTPS:443 | Ez a cím szükséges a Kubernetes GET/PUT műveletekhez. |
| login.chinacloudapi.cn  | HTTPS:443 | Ez a cím szükséges az Azure Active Directory-hitelesítéshez. |
| ntp.ubuntu.com             | UDP:123   | Ez a cím szükséges az NTP időszinkronizálásához Linux csomópontokon. |
| packages.microsoft.com     | HTTPS:443 | Ez a cím a Microsoft csomagok tárháza, amelyet a gyorsítótárazott *apt-get* műveletekhez használnak.  Példa csomagok közé tartozik a Moby, a PowerShell és az Azure CLI. |

- Azure Government

| FQDN                       | Port      | Használat      |
|----------------------------|-----------|----------|
| *.hcp. \<hely\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| *.tun. \<hely\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Ez a cím szükséges a csomópont <-> API-kiszolgáló kommunikációhoz. Cserélje * \<\> * le a helyet arra a régióra, ahol az AKS-fürt telepítve van. |
| mcr.microsoft.com          | HTTPS:443 | Ez a cím szükséges a Microsoft Container Registry (MCR) lemezképeinek eléréséhez. Ez a rendszerleíró adatbázis a fürt frissítés és a fürt mérete során a fürt működéséhez szükséges külső képeket/diagramokat (például moby stb.) tartalmazza. |
|*.cdn.mscr.io              | HTTPS:443 | Ez a cím az Azure Content Delivery Network (CDN) által támogatott MCR-tárolóhoz szükséges. |
| *.data.mcr.microsoft.com             | HTTPS:443 | Ez a cím szükséges az Azure tartalomkézbesítési hálózat (CDN) által támogatott MCR-tárolóhoz. |
| management.usgovcloudapi.net       | HTTPS:443 | Ez a cím szükséges a Kubernetes GET/PUT műveletekhez. |
| login.microsoftonline.us  | HTTPS:443 | Ez a cím szükséges az Azure Active Directory-hitelesítéshez. |
| ntp.ubuntu.com             | UDP:123   | Ez a cím szükséges az NTP időszinkronizálásához Linux csomópontokon. |
| packages.microsoft.com     | HTTPS:443 | Ez a cím a Microsoft csomagok tárháza, amelyet a gyorsítótárazott *apt-get* műveletekhez használnak.  Példa csomagok közé tartozik a Moby, a PowerShell és az Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Ez a cím a szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárházhoz szükséges. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Választható ajánlott címek és portok AKS-fürtökhöz

Az AKS-fürtök esetében a következő kimenő portok/hálózati szabályok nem kötelezőek:

Az AKS-fürtök megfelelő működéséhez a következő Teljes tartományhálózat/ alkalmazásszabályok használata ajánlott:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Ez a cím lehetővé teszi, hogy a Linux-fürtcsomópontok letöltsék a szükséges biztonsági javításokat és frissítéseket. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Szükséges címek és portok gpu-kompatibilis AKS-fürtökhöz

A GPU-val engedélyezett AKS-fürtök esetében a következő Teljes tartományhálózat/ alkalmazásszabályok szükségesek:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Ez a cím a GPU-alapú csomópontokon történő megfelelő illesztőprogram-telepítéshez és -üzemeltetéshez használatos. |
| us.download.nvidia.com | HTTPS:443 | Ez a cím a GPU-alapú csomópontokon történő megfelelő illesztőprogram-telepítéshez és -üzemeltetéshez használatos. |
| apt.dockerproject.org | HTTPS:443 | Ez a cím a GPU-alapú csomópontokon történő megfelelő illesztőprogram-telepítéshez és -üzemeltetéshez használatos. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Szükséges címek és portok az Azure Monitor használatával az engedélyezett tárolókhoz

A következő FQDN / alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van az Azure Monitor tárolókhoz:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Ez a helyes metrikák és a figyeléstelemetria az Azure Monitor használatával. |
| *.ods.opinsights.azure.com    | HTTPS:443    | Ezt az Azure Monitor használja a naplóelemzési adatok betöltéséhez. |
| *.oms.opinsights.azure.com | HTTPS:443 | Ezt a címet az omsagent használja, amely a log-elemzési szolgáltatás hitelesítésére szolgál. |
|*.microsoftonline.com | HTTPS:443 | Ez a metrikák hitelesítésére és küldésére szolgál az Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Ez metrikák adatainak az Azure Monitornak való küldésére szolgál. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Szükséges címek és portok engedélyezve az Azure Dev Spaces

A következő FQDN / alkalmazásszabályok szükségesek az Azure dev spaces-t engedélyező AKS-fürtökhöz:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Ez a cím linux alpine és más Azure Dev Spaces-képek lekérése |
| gcr.io | HTTP:443 | Ez a cím a kormány-/kormányos képek lehúzására szolgál |
| storage.googleapis.com | HTTP:443 | Ez a cím a kormány-/kormányos képek lehúzására szolgál |
| azds-\<guid\>. \<hely\>.azds.io | HTTPS:443 | Kommunikáció az Azure Dev Spaces háttérszolgáltatásokkal a vezérlőhöz. A pontos teljes tartománykapcsolati szint a "userprofile%\.azds\settings.json" "dataplaneFqdn" területen található. |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Az AKS-fürtök szükséges címei és portjai az Azure-szabályzat tal (nyilvános előzetes verzióban) engedélyezve vannak

> [!CAUTION]
> Az alábbi funkciók némelyike előzetes verzióban érhető el.  A cikkben szereplő javaslatok változhatnak, ahogy a funkció nyilvános előzetes verzióra és a későbbi megjelenési szakaszokra vált.

A következő FQDN / alkalmazásszabályok szükségesek az Azure-szabályzatot engedélyező AKS-fürtökhöz.

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ez a cím az Azure-szabályzat megfelelő működéséhez használatos. (jelenleg előzetes verzió az AKS-ben) |
| raw.githubusercontent.com | HTTPS:443 | Ez a cím a beépített szabályzatok lekérése a GitHubról az Azure-szabályzat megfelelő működésének biztosítása érdekében. (jelenleg előzetes verzió az AKS-ben) |
| *.gk. \<hely\>.azmk8s.io | HTTPS:443    | Az Azure-szabályzat bővítmény, amely a gatekeeper naplózási végpont fut a fő kiszolgálón, hogy a naplózási eredmények. |
| dc.services.visualstudio.com | HTTPS:443 | Az Azure-szabályzat bővítmény, amely telemetriai adatokat küld az alkalmazások elemzési végpont. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>A Windows Server alapú csomópontok által igényelt (nyilvános előzetes verzióban) engedélyezve van

> [!CAUTION]
> Az alábbi funkciók némelyike előzetes verzióban érhető el.  A cikkben szereplő javaslatok változhatnak, ahogy a funkció nyilvános előzetes verzióra és a későbbi megjelenési szakaszokra vált.

A Windows Server alapú AKS-fürtökhöz a következő teljes tartományn-/alkalmazásszabályok szükségesek:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Windowsrendszerekkel kapcsolatos bináris fájlok telepítése |
| mp.microsoft.com,<span></span>www .msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Windowsrendszerekkel kapcsolatos bináris fájlok telepítése |
| kms.core.windows.net | TCP:1688 | Windowsrendszerekkel kapcsolatos bináris fájlok telepítése |


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogy milyen portokat és címeket engedélyez, ha korlátozza a fürt kimenő forgalom. Azt is megadhatja, hogy maguk a podok hogyan kommunikálhatnak, és milyen korlátozások vannak a fürtön belül. További információ: [Biztonságos forgalom a podok között az AKS hálózati házirendjei használatával.][network-policy]

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
