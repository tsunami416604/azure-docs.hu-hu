---
title: Kimenő forgalom az Azure Kubernetes Service (AKS) korlátozása
description: Ismerje meg, milyen portokat és a címek szükségesek kimenő forgalom szabályozása az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: de0ba13a527569e446a44c275b7323d4487f53b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780300"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>Előnézet - korlát irányuló kimenő adatforgalmat a fürtcsomópontok és a szükséges portokat és szolgáltatásokat az Azure Kubernetes Service (AKS) az adathozzáférés szabályozása

Alapértelmezés szerint az AKS-fürtök (kimenő) kimenő internet-hozzáférés üzemmódban. A hálózati hozzáférési szintet lehetővé teszi a csomópontok és a külső erőforrások hozzáféréséhez szükséges szolgáltatásokat. Kimenő forgalomért korlátozni szeretné, ha korlátozott számú portok és címek elérhető karbantartási feladatokat a fürt kifogástalan karbantartása kell lennie. A fürt majd csak az alap rendszer tárolórendszerképeket a Microsoft Container Registry (MCR) vagy az Azure Container Registry (ACR), nem külső nyilvános adattár használatára van konfigurálva.

Ez a cikk ismerteti, milyen hálózati portok és teljes tartománynevek (FQDN) is szükséges és választható, ha egy AKS-fürtöt a kimenő forgalomért korlátozza.  Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók a következők: az önkiszolgáló és vehetnek részt. Visszajelzés és hibák gyűjtsön közösségünkhöz előzetes verziók vannak megadva. Azonban nem támogatja őket az Azure műszaki támogatást. Hozzon létre egy fürtöt, vagy adja hozzá ezeket a funkciókat a meglévő fürtökre, ha a fürt nem támogatott, mindaddig, amíg a funkció már nem előzetes verzióban érhető el és hallgatóknak az általánosan elérhető (GA).
>
> Ha az előzetes verziójú szolgáltatásaihoz is problémák merülnek fel [nyisson egy problémát a AKS GitHub-adattárat a] [ aks-github] az előzetes verziójú funkció a bejelentett hiba címét nevére.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Korlátozhatja a kimenő forgalomért AKS-fürt létrehozása, először engedélyeznie kell az előfizetés szolgáltatásjelzőre. Ez a funkció a regisztráció bármilyen alap rendszer tárolórendszerképek MCR vagy ACR használni létrehozott AKS-fürtök konfigurálása. Regisztrálja a *AKSLockingDownEgressPreview* jelző funkciót, használja a [az a funkció regisztrálása] [ az-feature-register] parancsot az alábbi példában látható módon:

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot ellenőrzéséhez használatával a [az szolgáltatáslistát] [ az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register] [ az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>Kimenő adatforgalom áttekintése

Felügyeleti és üzemeltetési célra az AKS-fürt csomópontjain kíván elérni bizonyos portokat és a teljes tartománynevek (FQDN). Ezek a műveletek lehet kommunikálni az API-kiszolgálóhoz, vagy töltse le és telepítse a fő Kubernetes fürt összetevőket és csomópont biztonsági frissítések. Alapértelmezés szerint kimenő forgalom (kimenő) internetes forgalom nem korlátozódik az AKS-fürt csomópontjaihoz. A fürt előfordulhat, hogy alap rendszer szolgáltatásból lekérhet tárolólemezképeket külső tárházakban.

Az AKS-fürt biztonságának növelése érdekében érdemes korlátozni a kimenő forgalomért. A fürt kérni alap rendszer tárolórendszerképeket MCR vagy ACR van konfigurálva. Ily módon a kimenő forgalomért zárolását, ha meg kell adnia bizonyos portokat és a teljes tartománynevek, hogy az AKS-csomópontok megfelelően szükséges külső szolgáltatásokkal kommunikálni. Ezek engedélyezett portok és teljes tartománynevek nélkül az AKS-csomópontok nem az API-kiszolgálóval folytatott kommunikációra és az alapvető összetevők telepítése.

Használhat [Azure tűzfal] [ azure-firewall] vagy 3. fél tűzfalat a kimenő forgalom biztonságát, és adja meg ezeket a szükséges portok és -címeket.

Az aks-ben portokat és a címek két készletnyi vannak:

* A [– szükséges portok és cím AKS-fürtök](#required-ports-and-addresses-for-aks-clusters) részletek engedélyezett kimenő forgalom minimális követelményeinek.
* A [címek és portok az AKS-fürtök esetén nem kötelező ajánlott](#optional-recommended-addresses-and-ports-for-aks-clusters) nem szükséges az összes forgatókönyvek, de más szolgáltatásokkal való integrációt, mint például az Azure Monitor nem fog megfelelően működni. Tekintse át a lehetséges portokat és teljes tartománynevek listáját, és a szolgáltatások és az AKS-fürt használt összetevők engedélyezése.

> [!NOTE]
> Csak a szolgáltatás jelző regisztráció engedélyezése után létrehozott új AKS-fürtök a kimenő forgalom korlátozása működik. A meglévő fürtök esetén [hajtsa végre a fürt frissítési művelet] [ aks-upgrade] használatával a `az aks upgrade` parancsot, mielőtt a kimenő forgalomért korlátozza.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Szükséges portok és a címek az AKS-fürtök

A következő kimenő portokat / hálózati szabályok szükségesek az AKS-fürt:

* TCP-port *443-as porton*
* TCP-port *9000*

A következő teljes tartománynév / alkalmazás szabályok szükségesek:

| Teljes tartománynév                      | Port      | Használat      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | Ez a cím az az API kiszolgálói végpont. |
| aksrepos.azurecr.io       | HTTPS:443 | Ez a cím megadása kötelező hozzáférési lemezképek az Azure Container Registry (ACR). |
| *.blob.core.windows.net   | HTTPS:443 | Ez a cím a rendszerképeket az ACR tárolt háttérrendszer tároló. |
| mcr.microsoft.com         | HTTPS:443 | Ez a cím megadása kötelező hozzáférési lemezképek Microsoft Container Registry (MCR). |
| management.azure.com      | HTTPS:443 | Ez a cím megadása kötelező a Kubernetes GET és PUT műveleteket. |
| login.microsoftonline.com | HTTPS:443 | Ez a cím megadása kötelező az Azure Active Directory-hitelesítést. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Nem kötelező ajánlott címek és portok az AKS-fürtök

A következő kimenő portokat / hálózati szabályok nem szükséges a megfelelő működéséhez az AKS-fürtök, de ajánlott:

* UDP-port *123* az idő NTP-szinkronizálás
* UDP-port *53-as* DNS-hez

A következő teljes tartománynév / alkalmazás szabályokat a megfelelő működéshez az AKS-fürtök használata javasolt:

| Teljes tartománynév                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | Ez a cím lehetővé teszi, hogy a szükséges biztonsági javítások és frissítések letöltéséhez a Linux-fürtcsomópontokon. |
| packages.microsoft.com                  | HTTPS:443 | Ez a cím a Microsoft-csomagok tárházából, használja a gyorsítótárazott *apt-get paranccsal* műveletek. |
| dc.services.visualstudio.com            | HTTPS:443 | Javasolt a megfelelő metrikák és a figyelést az Azure Monitor használatával. |
| *.opinsights.azure.com                  | HTTPS:443 | Javasolt a megfelelő metrikák és a figyelést az Azure Monitor használatával. |
| *.monitoring.azure.com                  | HTTPS:443 | Javasolt a megfelelő metrikák és a figyelést az Azure Monitor használatával. |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Ezzel a címmel helyes működéséhez az Azure Policy (jelenleg előzetes verzióban érhető el az aks-ben). |
| apt.dockerproject.org                   | HTTPS:443 | Ezzel a címmel megfelelő illesztőprogram telepítése és a GPU-alapú csomópontokon művelethez. |
| nvidia.github.io                        | HTTPS:443 | Ezzel a címmel megfelelő illesztőprogram telepítése és a GPU-alapú csomópontokon művelethez. |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, milyen portokat és a címek, hogy úgy korlátozhatja a fürt kimenő forgalomért. Miként kommunikál a podok magukat, és milyen korlátozások is definiálhat a fürtön belül van. További információkért lásd: [között pods házirendekkel az aks-ben hálózati forgalmának biztonságossá tétele][network-policy].

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]

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
