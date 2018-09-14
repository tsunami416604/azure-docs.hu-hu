---
title: Az Azure Kubernetes Service bemutatása
description: Az Azure Kubernetes Service használatával egyszerűen helyezhetőek üzembe és felügyelhetőek a tárolóalapú alkalmazások az Azure-ban.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 294ccd70e0339ed5ad68022f002e4864fc52b452
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286720"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) használatával egyszerűen helyezhetők üzembe a felügyelt Kubernetes-fürtök az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. Emellett a szolgáltatás ingyenes, és csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem.

Ez a dokumentum áttekintést nyújt az Azure Kubernetes Service (AKS) jellemzőiről.

## <a name="flexible-deployment-options"></a>Rugalmas telepítési lehetőségek

Az Azure Kubernetes Service portál-, parancssor- és sablonalapú üzembehelyezési lehetőségeket kínál (Resource Manager-sablonok és Terraform). Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során.

További információért tekintse meg az [AKS-portál rövid útmutatóját][aks-portal] vagy az [AKS parancssori felület rövid útmutatóját][aks-cli].

## <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

Az AKS-fürtök támogatják a [Szerepköralapú hozzáférés-vezérlést (RBAC)][kubernetes-rbac]. Az AKS-fürtök az Azure Active Directoryval való integrációhoz is konfigurálhatók. Ebben a konfigurációban a Kubernetes-hozzáférést az Azure Active Directory-identitás és a csoporttagság alapján lehet konfigurálni.

További információ: [Az Azure Active Directory integrálása az AKS szolgáltatással][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

A tárolóállapot lehetővé teszi a teljesítményfigyelést a tárolókból, csomópontokból és vezérlőkből gyűjtött memória- és processzorhasználati mérőszámok segítségével. A rendszer a tárolónaplókat is gyűjti. Ezen adatokat a rendszer a Log Analytics-munkaterületen tárolja, és az Azure Portalon, az Azure CLI-n vagy a REST-végponton keresztül érhetők el.

További információ: [Az Azure Kubernetes Service tárolóállapot-monitorozása][container-health].

## <a name="cluster-node-scaling"></a>Fürtcsomópont-méretezés

Az AKS-fürtök csomópontjai az erőforrások iránti igény növekedésének megfelelően felskálázhatók. Ha csökken az erőforrások iránti igény, a fürtök leskálázásával csomópontok távolíthatók el. Az AKS méretezési műveletei az Azure Portal vagy az Azure CLI használatával végezhetők el.

További információ: [Azure Kubernetes Service- (AKS-) fürtök méretezése][aks-scale].

## <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az Azure Kubernetes Service számos Kubernetes-verziót kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.

További információ: [Azure Kubernetes Service- (AKS-) fürtök frissítése][aks-upgrade].

## <a name="http-application-routing"></a>HTTP-alkalmazások útválasztása

A HTTP-alkalmazásútválasztási megoldásnak köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén. Az alkalmazások telepítése során a nyilvánosan hozzáférhető DNS-neveket a rendszer automatikusan konfigurálja.

További információ: [HTTP-alkalmazások útválasztása][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását. Az Azure jelenlegi kínálatában egy vagy több GPU-val kompatibilis virtuális gépek érhetők el. A GPU-kompatibilis virtuális gépeket a nagy számítási és grafikai igényű, valamint vizualizációs számítási feladatok elvégzésére tervezték.

További információ: [GPU-k használata az AKS-en][aks-gpu].

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes fejlesztői és felügyeleti eszközök széles választékát kínálja. Ezek közé tartozik például a Helm, a Draft és a Visual Studio Code-hoz készült Kubernetes-bővítmény. Ezen eszközök problémamentesen működnek az Azure Kubernetes Service szolgáltatással.

Emellett az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet azokon hibakeresést közvetlenül az Azure Kubernetes Service-ben (AKS).

További információ: [Azure Dev Spaces][azure-dev-spaces].

Az Azure DevOps Project egy egyszerű megoldást kínál a már létező kódok és Git-adattárak az Azure-ba való áthelyezéséhez. A DevOps Project olyan Azure-erőforrásokat hoz létre automatikusan, mint az AKS, amely egy, a CI-hez való build-definíciót tartalmazó kiadási folyamat a VSTS-ben, továbbá beállít egy kiadási definíciót a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

További információ: [Azure DevOps Project][azure-devops].

## <a name="virtual-network-integration"></a>Virtuális hálózat integrációja

Az AKS-fürtöket egy már létező virtuális hálózatban is üzembe lehet helyezni. Ebben a konfigurációban a rendszer a fürtben található összes podhoz hozzárendel egy IP-címet a virtuális hálózatban, így azok közvetlenül kommunikálhatnak a fürtben található más podokkal és a virtuális hálózat egyéb csomópontjaival is. A podok a virtuális társhálózaton belül más szolgáltatásokhoz, továbbá az ExpressRoute és a helyek közötti (S2S) VPN-kapcsolat segítségével helyszíni hálózatokhoz is csatlakozhatnak.

További információ: [Az AKS hálózatkezelésének áttekintése][aks-networking].

## <a name="private-container-registry"></a>Privát tárolóregisztrációs adatbázis

Integrálhat az Azure Container Registryvel (ACR) a Docker-rendszerképek privát tárolásához.

További információ: [Azure Container Registry (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Tárolókötetek támogatása

Az állandó adatok esetében az Azure Kubernetes Service (AKS) támogatja a tárolókötetek csatlakoztatását. Az AKS-fürtök Azure Files- és Azure Disks-támogatással jönnek létre.

További információ: [Azure Files][azure-files] és [Azure Disks][azure-disk].

## <a name="docker-image-support"></a>Docker-rendszerkép támogatása

Az Azure Kubernetes Service (AKS) támogatja a Docker rendszerképformátumát.

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Kubernetes Service (AKS) CNCF-tanúsítvánnyal rendelkezik, mivel megfelel a Kubernetes követelményeinek.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Kubernetes Service (AKS) az SOC, az ISO és a PCI DSS szabványnak is megfelel.

## <a name="next-steps"></a>További lépések

Az AKS gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

> [!div class="nextstepaction"]
> [AKS – rövid útmutató][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

