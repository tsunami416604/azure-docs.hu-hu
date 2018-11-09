---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 09/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 196c3cb58eb60922f4898b6919c8393853c6126a
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094284"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) használatával egyszerűen helyezhetők üzembe a felügyelt Kubernetes-fürtök az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. A Kubernetes fő csomópontokat az Azure felügyeli. Ön csak az ügynökcsomópontokat felügyeli és tartja karban. Felügyelt Kubernetes-szolgáltatásként az AKS ingyenes, és csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem.

Létrehozhat AKS-fürtöket a Microsoft Azure Portalon az Azure CLI-vel, vagy sablonalapú üzembehelyezési lehetőségekkel, például Resource Manager-sablonokkal és Terraformmal. Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során.

Az első lépésekhez végezze el az AKS rövid útmutatójában leírtakat a [Microsoft Azure Portalon][aks-portal] vagy az [Azure CLI használatával][aks-cli].

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A továbbfejlesztett biztonság és felügyelet érdekében az AKS lehetővé teszi a Microsoft Azure Active Directoryval történő integrációt és a Kubernetes szerepköralapú hozzáférés-vezérlésének használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

Az AKS támogatja a [szerepköralapú hozzáférés-vezérlést (RBAC)][kubernetes-rbac], amellyel korlátozhatja a fürt erőforrásaihoz való hozzáférést. Az RBAC lehetővé teszi a Kubernetes-erőforrások és -névterek hozzáférésének és az ezen erőforrásokhoz tartozó engedélyek felügyeletét. Az AKS-fürtöket az Azure Active Directoryval való integrációhoz is konfigurálhatja. Az Azure AD-integrációval a Kubernetes-hozzáférést a meglévő identitás és csoporttagság alapján lehet konfigurálni. Meglévő Azure AD-felhasználói és -csoportjai kaphatnak hozzáférést az AKS-erőforrásokhoz, amelyeket egy integrált bejelentkezési felületen keresztül érhetnek el.

Az AKS-fürtök biztonságossá tételéhez tekintse meg [Az Azure Active Directory és az AKS integrációja][aks-aad] című cikket.

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

Az AKS-fürtök és az üzembe helyezett alkalmazások teljesítményének megismeréséhez a tárolóállapothoz használt Azure Monitor memória- és processzorhasználati mérőszámokat gyűjt a tárolókból, csomópontokból és vezérlőkből. Rendelkezésre állnak a tárolók naplói is, így [a Kubernetes fő csomópontok naplóinak áttekintésére][aks-master-logs] is van lehetőség. A rendszer egy Log Analytics-munkaterületen tárolja monitorozási adatokat, amelyek a Microsoft Azure Portalon, az Azure CLI-n vagy a REST-végponton keresztül érhetők el.

További információ: [Az Azure Kubernetes Service tárolóállapot-monitorozása][container-health].

## <a name="cluster-and-node"></a>Fürt és csomópont

Az AKS-csomópontok Azure-beli virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat.

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások változtatása iránti igények miatt a szolgáltatását futtató fürtcsomópontok és podok száma automatikusan skálázhatók felfelé vagy lefelé. Egyaránt használhatja a podok automatikus vízszintes skálázását és a fürt automatikus skálázását. Ezzel a megközelítéssel az AKS-fürtök a képesek automatikusan alkalmazkodni az igényekhez, és csak a szükséges erőforrásokat használják.

További információ: [Azure Kubernetes Service- (AKS-) fürtök méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az Azure Kubernetes Service számos Kubernetes-verziót kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.

A verziók életciklusáról további tudnivalókat [Az AKS-ben támogatott Kubernetes verziók][aks-supported versions] című cikkben talál. A frissítés lépéseit [Az Azure Kubernetes Service- (AKS-) fürtök frissítése][aks-upgrade] című cikk ismerteti.

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását. Az Azure jelenlegi kínálatában egy vagy több GPU-val kompatibilis virtuális gépek érhetők el. A GPU-kompatibilis virtuális gépeket a nagy számítási és grafikai igényű, valamint vizualizációs számítási feladatok elvégzésére tervezték.

További információ: [GPU-k használata az AKS-en][aks-gpu].

### <a name="storage-volume-support"></a>Tárolókötetek támogatása

Az alkalmazás számítási feladatának támogatásához csatlakoztathat tárköteteket az állandó adatok tárolásához. Statikus és dinamikus kötetek egyaránt használhatók. Annak függvényében, hogy hány csatlakoztatott podnak kell a tárterületen osztoznia, használhat Azure Disks-alapú tárterületet egyszeres podhozzáférés esetén, vagy Azure Files-alapú tárterületet több egyidejű podhozzáférés esetén.

Ismerkedés a dinamikus tartós kötetek használatával az [Azure Disks][azure-disk] vagy az [Azure Files][azure-files] segítségével.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a rendszer a fürtben található összes podhoz hozzárendel egy IP-címet a virtuális hálózatban, így azok közvetlenül kommunikálhatnak a fürtben található más podokkal és a virtuális hálózat egyéb csomópontjaival. A podok a virtuális társhálózaton belül más szolgáltatásokhoz is csatlakozhatnak, illetve az ExpressRoute és a helyek közötti (S2S) VPN-kapcsolat segítségével helyszíni hálózatokhoz is.

További információ: [Az AKS hálózatkezelésének áttekintése][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazásútválasztási bővítménynek köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén. Az alkalmazások telepítése során a nyilvánosan hozzáférhető DNS-neveket a rendszer automatikusan konfigurálja. A HTTP-alkalmazásútválasztás meghatároz egy DNS-zónát, és integrálja az AKS-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.

A bejövő forgalom megismeréséhez tekintse meg a [HTTP-alkalmazásútválasztás][aks-http-routing] című cikket.

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes fejlesztői és felügyeleti eszközök széles választékát kínálja. Ezek közé tartozik például a Helm, a Draft és a Visual Studio Code-hoz készült Kubernetes-bővítmény. Ezek az eszközök problémamentesen működnek az AKS-szolgáltatással.

Emellett az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet rajtuk hibakeresést közvetlenül az AKS-ben. Ismerkedés az [Azure Dev Spaces][azure-dev-spaces] használatával.

Az Azure DevOps Project egy egyszerű megoldást kínál a már létező kódoknak és Git-adattáraknak az Azure-ba való áthelyezéséhez. A DevOps Project olyan Azure-erőforrásokat hoz létre automatikusan, mint az AKS, amely egy, a CI-hez való buildelési folyamatot tartalmazó kiadási folyamat az Azure DevOps Servicesben, továbbá beállít egy kiadási folyamatot a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

További információ: [Azure DevOps Project][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-rendszerképek támogatása és privát tárolóregisztrációs adatbázis

Az AKS támogatja a Docker rendszerképformátumát. Integrálhatja az AKS-szolgáltatást az Azure Container Registryvel (ACR-rel) a Docker-rendszerképek privát tárolásához.

Privát rendszerképtárolók létrehozásával kapcsolatban lásd: [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Kubernetes Service (AKS) CNCF-tanúsítvánnyal rendelkezik, mivel megfelel a Kubernetes követelményeinek.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Kubernetes Service (AKS) az SOC, az ISO, a PCI DSS és a HIPAA szabványnak is megfelel.

## <a name="next-steps"></a>További lépések

Az Azure CLI gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

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
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
