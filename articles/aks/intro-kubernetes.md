---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5515951a9bde596935f4c603ffd9e088f74dee45
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615162"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) használatával egyszerűen helyezhetők üzembe a felügyelt Kubernetes-fürtök az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. A Kubernetes fő csomópontokat az Azure felügyeli. Ön csak az ügynökcsomópontokat felügyeli és tartja karban. Felügyelt Kubernetes-szolgáltatásként az AKS ingyenes, és csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem.

Létrehozhat AKS-fürtöket a Microsoft Azure Portalon az Azure CLI-vel, vagy sablonalapú üzembehelyezési lehetőségekkel, például Resource Manager-sablonokkal és Terraformmal. Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során. A Windows Server-tárolók támogatása jelenleg az aks-ben előzetes verzióban érhető el.

További információ a Kubernetes alapjai: [Kubernetes alapvető fogalmait az aks-ben][concepts-clusters-workloads].

Első lépésként hajtsa végre az AKS gyors [az Azure Portalon][aks-portal] or [with the Azure CLI][aks-cli].

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A továbbfejlesztett biztonság és felügyelet érdekében az AKS lehetővé teszi a Microsoft Azure Active Directoryval történő integrációt és a Kubernetes szerepköralapú hozzáférés-vezérlésének használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

Fürt erőforrásokhoz való hozzáférés korlátozásához AKS támogatja [Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC)][kubernetes-rbac]. Az RBAC lehetővé teszi a Kubernetes-erőforrások és -névterek hozzáférésének és az ezen erőforrásokhoz tartozó engedélyek felügyeletét. Az AKS-fürtöket az Azure Active Directoryval való integrációhoz is konfigurálhatja. Az Azure AD-integrációval a Kubernetes-hozzáférést a meglévő identitás és csoporttagság alapján lehet konfigurálni. Meglévő Azure AD-felhasználói és -csoportjai kaphatnak hozzáférést az AKS-erőforrásokhoz, amelyeket egy integrált bejelentkezési felületen keresztül érhetnek el.

Az identitás további információkért lásd: [hozzáférési és azonosító beállításai az aks-ben][concepts-identity].

Az AKS-fürtök biztonságos, lásd: [integrálása az Azure Active Directory az aks-sel][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

Az AKS-fürtök és az üzembe helyezett alkalmazások teljesítményének megismeréséhez a tárolóállapothoz használt Azure Monitor memória- és processzorhasználati mérőszámokat gyűjt a tárolókból, csomópontokból és vezérlőkből. Tárolónaplók érhetők el, és emellett [tekintse át a fő Kubernetes-naplók][aks-master-logs]. A rendszer egy Log Analytics-munkaterületen tárolja monitorozási adatokat, amelyek a Microsoft Azure Portalon, az Azure CLI-n vagy a REST-végponton keresztül érhetők el.

További információkért lásd: [figyelő Azure Kubernetes Service-ben tároló állapotának][container-health].

## <a name="clusters-and-nodes"></a>Fürtök és a csomópontok

Az AKS-csomópontok Azure-beli virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat. Az AKS támogatja a Kubernetes-fürtök, amely több csomópontkészleteit a vegyes operációs rendszerek és a Windows Server-tárolók (jelenleg előzetes verzióban érhető el). Linux-csomópontok egy Ubuntu operációs rendszer testre szabott lemezképet futtassa, és a Windows Server-csomópontok egy testre szabott Windows Server 2019 operációsrendszer-lemezkép futtatásához.

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások változtatása iránti igények miatt a szolgáltatását futtató fürtcsomópontok és podok száma automatikusan skálázhatók felfelé vagy lefelé. Egyaránt használhatja a podok automatikus vízszintes skálázását és a fürt automatikus skálázását. Ezzel a megközelítéssel az AKS-fürtök a képesek automatikusan alkalmazkodni az igényekhez, és csak a szükséges erőforrásokat használják.

További információkért lásd: [Azure Kubernetes Service (AKS)-fürt méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az Azure Kubernetes Service számos Kubernetes-verziót kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.

Életciklus-verziókkal kapcsolatos további tudnivalókért lásd: [az aks-ben a Kubernetes támogatott verziók][aks-supported versions]. For steps on how to upgrade, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását. Az Azure jelenlegi kínálatában egy vagy több GPU-val kompatibilis virtuális gépek érhetők el. A GPU-kompatibilis virtuális gépeket a nagy számítási és grafikai igényű, valamint vizualizációs számítási feladatok elvégzésére tervezték.

További információkért lásd: [segítségével gpu-kat használ az aks-en][aks-gpu].

### <a name="storage-volume-support"></a>Tárolókötetek támogatása

Az alkalmazás számítási feladatának támogatásához csatlakoztathat tárköteteket az állandó adatok tárolásához. Statikus és dinamikus kötetek egyaránt használhatók. Annak függvényében, hogy hány csatlakoztatott podnak kell a tárterületen osztoznia, használhat Azure Disks-alapú tárterületet egyszeres podhozzáférés esetén, vagy Azure Files-alapú tárterületet több egyidejű podhozzáférés esetén.

További információkért lásd: [tárolási lehetőségek az aks-ben alkalmazásokhoz][concepts-storage].

Ismerkedés a dinamikus állandó mennyiségű [Azure Disks][azure-disk] or [Azure Files][azure-files].

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a rendszer a fürtben található összes podhoz hozzárendel egy IP-címet a virtuális hálózatban, így azok közvetlenül kommunikálhatnak a fürtben található más podokkal és a virtuális hálózat egyéb csomópontjaival. A podok a virtuális társhálózaton belül más szolgáltatásokhoz is csatlakozhatnak, illetve az ExpressRoute és a helyek közötti (S2S) VPN-kapcsolat segítségével helyszíni hálózatokhoz is.

További információkért lásd: a [fogalmak az aks-ben az alkalmazások hálózati][aks-networking].

Első lépések a bejövő forgalmat, lásd: [HTTP-alkalmazások útválasztása][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazásútválasztási bővítménynek köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén. Az alkalmazások telepítése során a nyilvánosan hozzáférhető DNS-neveket a rendszer automatikusan konfigurálja. A HTTP-alkalmazásútválasztás meghatároz egy DNS-zónát, és integrálja az AKS-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.

Első lépések a bejövő forgalmat, lásd: [HTTP-alkalmazások útválasztása][aks-http-routing].

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes fejlesztői és felügyeleti eszközök széles választékát kínálja. Ezek közé tartozik például a Helm, a Draft és a Visual Studio Code-hoz készült Kubernetes-bővítmény. Ezek az eszközök problémamentesen működnek az AKS-szolgáltatással.

Emellett az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet rajtuk hibakeresést közvetlenül az AKS-ben. Első lépésként lásd [Azure fejlesztési tárolóhelyek][azure-dev-spaces].

Az Azure DevOps Project egy egyszerű megoldást kínál a már létező kódoknak és Git-adattáraknak az Azure-ba való áthelyezéséhez. A DevOps Project olyan Azure-erőforrásokat hoz létre automatikusan, mint az AKS, amely egy, a CI-hez való buildelési folyamatot tartalmazó kiadási folyamat az Azure DevOps Servicesben, továbbá beállít egy kiadási folyamatot a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

További információkért lásd: [Azure DevOps-projekt][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-rendszerképek támogatása és privát tárolóregisztrációs adatbázis

Az AKS támogatja a Docker rendszerképformátumát. Integrálhatja az AKS-szolgáltatást az Azure Container Registryvel (ACR-rel) a Docker-rendszerképek privát tárolásához.

Privát rendszerkép tároló létrehozásához lásd: [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Kubernetes Service (AKS) CNCF-tanúsítvánnyal rendelkezik, mivel megfelel a Kubernetes követelményeinek.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Kubernetes Service (AKS) az SOC, az ISO, a PCI DSS és a HIPAA szabványnak is megfelel. További információkért lásd: [áttekintése a Microsoft Azure-megfelelőségi][compliance-doc].

## <a name="next-steps"></a>További lépések

Az Azure CLI gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

> [!div class="nextstepaction"]
> [AKS gyors][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
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
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
