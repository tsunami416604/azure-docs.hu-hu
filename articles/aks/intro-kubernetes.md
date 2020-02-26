---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: f78b65169899107bcd4626bbed65bd4fddd8af43
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595450"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) használatával egyszerűen helyezhetők üzembe a felügyelt Kubernetes-fürtök az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. A Kubernetes fő csomópontokat az Azure felügyeli. Ön csak az ügynökcsomópontokat felügyeli és tartja karban. Felügyelt Kubernetes-szolgáltatásként az AKS ingyenes, és csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem.

Létrehozhat AKS-fürtöket a Microsoft Azure Portalon az Azure CLI-vel, vagy sablonalapú üzembehelyezési lehetőségekkel, például Resource Manager-sablonokkal és Terraformmal. Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során. A Windows Server-tárolók támogatása jelenleg előzetes verzióban érhető el az AK-ban.

A Kubernetes alapjaival kapcsolatos további információkért lásd: [Kubernetes alapfogalmak az AK][concepts-clusters-workloads]-hoz.

Első lépésként végezze el az AK gyors üzembe helyezését [a Azure Portal][aks-portal] vagy [Az Azure CLI-vel][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A továbbfejlesztett biztonság és felügyelet érdekében az AKS lehetővé teszi a Microsoft Azure Active Directoryval történő integrációt és a Kubernetes szerepköralapú hozzáférés-vezérlésének használatát. A fürtje és az erőforrásai állapotát is monitorozhatja.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

A fürt erőforrásaihoz való hozzáférés korlátozásához az AK támogatja a [Kubernetes szerepköralapú hozzáférés-vezérlést (RBAC)][kubernetes-rbac]. Az RBAC lehetővé teszi a Kubernetes-erőforrások és -névterek hozzáférésének és az ezen erőforrásokhoz tartozó engedélyek felügyeletét. Az AKS-fürtöket az Azure Active Directoryval való integrációhoz is konfigurálhatja. Az Azure AD-integrációval a Kubernetes-hozzáférést a meglévő identitás és csoporttagság alapján lehet konfigurálni. Meglévő Azure AD-felhasználói és -csoportjai kaphatnak hozzáférést az AKS-erőforrásokhoz, amelyeket egy integrált bejelentkezési felületen keresztül érhetnek el.

Az identitással kapcsolatos további információkért lásd: [hozzáférési és identitási beállítások az AK][concepts-identity]-hoz.

Az AK-fürtök biztonságossá tételéhez lásd: [Azure Active Directory integrálása az AK-val][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

Az AKS-fürtök és az üzembe helyezett alkalmazások teljesítményének megismeréséhez a tárolóállapothoz használt Azure Monitor memória- és processzorhasználati mérőszámokat gyűjt a tárolókból, csomópontokból és vezérlőkből. A tároló naplói elérhetők, és [áttekintheti a Kubernetes fő naplóit][aks-master-logs]is. A rendszer egy Log Analytics-munkaterületen tárolja monitorozási adatokat, amelyek a Microsoft Azure Portalon, az Azure CLI-n vagy a REST-végponton keresztül érhetők el.

További információ: az [Azure Kubernetes Service Container Health figyelése][container-health].

## <a name="clusters-and-nodes"></a>Fürtök és csomópontok

Az AKS-csomópontok Azure-beli virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat. Az AK támogatja a több csomópontot futtató Kubernetes-fürtöket a vegyes operációs rendszerek és a Windows Server-tárolók (jelenleg előzetes verzióban) támogatásához. A Linux-csomópontok testreszabott Ubuntu operációsrendszer-rendszerképet futtatnak, és a Windows Server-csomópontok testreszabott Windows Server 2019 operációsrendszer-rendszerképet futtatnak.

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások változtatása iránti igények miatt a szolgáltatását futtató fürtcsomópontok és podok száma automatikusan skálázhatók felfelé vagy lefelé. Egyaránt használhatja a podok automatikus vízszintes skálázását és a fürt automatikus skálázását. Ezzel a megközelítéssel az AKS-fürtök a képesek automatikusan alkalmazkodni az igényekhez, és csak a szükséges erőforrásokat használják.

További információ: [Azure Kubernetes Service (ak) fürt méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az Azure Kubernetes Service számos Kubernetes-verziót kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.

További információ az életciklus-verziókról: [támogatott Kubernetes-verziók az AK-ban][aks-supported versions]. A frissítésével kapcsolatos további információkért lásd: [Azure Kubernetes Service (ak) fürt frissítése][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását. Az Azure jelenlegi kínálatában egy vagy több GPU-val kompatibilis virtuális gépek érhetők el. A GPU-kompatibilis virtuális gépeket a nagy számítási és grafikai igényű, valamint vizualizációs számítási feladatok elvégzésére tervezték.

További információ: a [GPU használata az AK-on][aks-gpu].

### <a name="storage-volume-support"></a>Tárolókötetek támogatása

Az alkalmazás számítási feladatának támogatásához csatlakoztathat tárköteteket az állandó adatok tárolásához. Statikus és dinamikus kötetek egyaránt használhatók. Annak függvényében, hogy hány csatlakoztatott podnak kell a tárterületen osztoznia, használhat Azure Disks-alapú tárterületet egyszeres podhozzáférés esetén, vagy Azure Files-alapú tárterületet több egyidejű podhozzáférés esetén.

További információ: [tárolási lehetőségek az AK-ban lévő alkalmazásokhoz][concepts-storage].

A dinamikus állandó kötetek használatának első lépései az [Azure-lemezek][azure-disk] vagy a [Azure Files][azure-files]használatával.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a rendszer a fürtben található összes podhoz hozzárendel egy IP-címet a virtuális hálózatban, így azok közvetlenül kommunikálhatnak a fürtben található más podokkal és a virtuális hálózat egyéb csomópontjaival. A podok a virtuális társhálózaton belül más szolgáltatásokhoz is csatlakozhatnak, illetve az ExpressRoute és a helyek közötti (S2S) VPN-kapcsolat segítségével helyszíni hálózatokhoz is.

További információ: az [AK-beli alkalmazások hálózati fogalmai][aks-networking].

A bejövő forgalom forgalmának megkezdéséhez lásd: [http-alkalmazás útválasztása][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazásútválasztási bővítménynek köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén. Az alkalmazások telepítése során a nyilvánosan hozzáférhető DNS-neveket a rendszer automatikusan konfigurálja. A HTTP-alkalmazásútválasztás meghatároz egy DNS-zónát, és integrálja az AKS-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.

A bejövő forgalom forgalmának megkezdéséhez lásd: [http-alkalmazás útválasztása][aks-http-routing].

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes fejlesztői és felügyeleti eszközök széles választékát kínálja. Ezek közé tartozik például a Helm, a Draft és a Visual Studio Code-hoz készült Kubernetes-bővítmény. Ezek az eszközök problémamentesen működnek az AKS-szolgáltatással.

Emellett az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet rajtuk hibakeresést közvetlenül az AKS-ben. Első lépésként tekintse meg az [Azure dev Spaces][azure-dev-spaces]című témakört.

Az Azure DevOps Project egy egyszerű megoldást kínál a már létező kódoknak és Git-adattáraknak az Azure-ba való áthelyezéséhez. A DevOps Project olyan Azure-erőforrásokat hoz létre automatikusan, mint az AKS, amely egy, a CI-hez való buildelési folyamatot tartalmazó kiadási folyamat az Azure DevOps Servicesben, továbbá beállít egy kiadási folyamatot a CD-hez, majd létrehoz egy Azure Application Insights-erőforrást a monitorozáshoz.

További információ: [Azure DevOps Project][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-rendszerképek támogatása és privát tárolóregisztrációs adatbázis

Az AKS támogatja a Docker rendszerképformátumát. Integrálhatja az AKS-szolgáltatást az Azure Container Registryvel (ACR-rel) a Docker-rendszerképek privát tárolásához.

Privát rendszerkép-tároló létrehozásához lásd: [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Kubernetes Service (AKS) CNCF-tanúsítvánnyal rendelkezik, mivel megfelel a Kubernetes követelményeinek.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Kubernetes Service (AKS) az SOC, az ISO, a PCI DSS és a HIPAA szabványnak is megfelel. További információ: [az Microsoft Azure megfelelőségének áttekintése][compliance-doc].

## <a name="next-steps"></a>Következő lépések

Az Azure CLI gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

> [!div class="nextstepaction"]
> [AK-gyors útmutató][aks-cli]

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
