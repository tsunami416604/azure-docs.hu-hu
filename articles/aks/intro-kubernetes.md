---
title: Az Azure Kubernetes Service bemutatása
description: Megismerheti az Azure Kubernetes Service funkcióit és előnyeit, amelyekkel tárolóalapú alkalmazásokat helyezhet üzembe és felügyelhet az Azure-ban.
services: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 4115ab2140cd3bf67135e494e16fb8121c040ff6
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246196"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Az Azure Kubernetes Service (AKS) használatával egyszerűen helyezhetők üzembe a felügyelt Kubernetes-fürtök az Azure-ban. Az AKS egyszerűsíti a Kubernetes kezelését és csökkenti annak működési munkaterhelését azáltal, hogy a felelősség nagy részét az Azure-ra helyezi át. Üzemeltetett Kubernetes-szolgáltatásként az Azure olyan fontos műveleteket bonyolít le, mint az állapotmonitorozás és a karbantartás. A Kubernetes fő csomópontokat az Azure felügyeli. Ön csak az ügynökcsomópontokat felügyeli és tartja karban. Felügyelt Kubernetes-szolgáltatásként az AKS ingyenes, és csak a fürtjei ügynökcsomópontjaiért kell fizetnie, a fő csomópontokért nem.

Létrehozhat AKS-fürtöket a Microsoft Azure Portalon az Azure CLI-vel, vagy sablonalapú üzembehelyezési lehetőségekkel, például Resource Manager-sablonokkal és Terraformmal. Egy AKS-fürt üzembe helyezésekor a rendszer elvégzi Ön helyett a Kubernetes fő és összes más csomópontjának üzembe helyezését és konfigurálását. Az olyan további funkciók, mint a speciális hálózatkezelés, az Azure Active Directory-integráció és a monitorozás szintén konfigurálhatók az üzembehelyezési folyamat során. A Windows Server-tárolók az AK-ban támogatottak.

A Kubernetes alapjaival kapcsolatos további információkért lásd: [Kubernetes alapfogalmak az AK][concepts-clusters-workloads]-hoz.

Az első lépésekhez végezze el az AKS rövid útmutatójában leírtakat a [Microsoft Azure Portalon][aks-portal] vagy az [Azure CLI használatával][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Hozzáférés, biztonság és monitoring

A jobb biztonság és felügyelet érdekében az AK lehetővé teszi az integrációt a Azure Active Directory és a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) használatával. A fürtje és az erőforrásai állapotát is monitorozhatja.

### <a name="identity-and-security-management"></a>Identitás és biztonsági felügyelet

A fürt erőforrásaihoz való hozzáférés korlátozásához az AK támogatja a [Kubernetes szerepköralapú hozzáférés-vezérlést (KUBERNETES RBAC)][kubernetes-rbac]. A Kubernetes RBAC lehetővé teszi a Kubernetes erőforrásainak és névterének, valamint az ezekhez tartozó engedélyek hozzáférésének szabályozását. Az AKS-fürtöket az Azure Active Directoryval való integrációhoz is konfigurálhatja. Az Azure AD-integrációval a Kubernetes-hozzáférést a meglévő identitás és csoporttagság alapján lehet konfigurálni. Meglévő Azure AD-felhasználói és -csoportjai kaphatnak hozzáférést az AKS-erőforrásokhoz, amelyeket egy integrált bejelentkezési felületen keresztül érhetnek el.

Az identitással kapcsolatos további információkért lásd: [hozzáférési és identitási beállítások az AK][concepts-identity]-hoz.

Az AKS-fürtök biztonságossá tételéhez tekintse meg [Az Azure Active Directory és az AKS integrációja][aks-aad] című cikket.

### <a name="integrated-logging-and-monitoring"></a>Integrált naplózás és monitorozás

Az AKS-fürtök és az üzembe helyezett alkalmazások teljesítményének megismeréséhez a tárolóállapothoz használt Azure Monitor memória- és processzorhasználati mérőszámokat gyűjt a tárolókból, csomópontokból és vezérlőkből. Rendelkezésre állnak a tárolók naplói is, így [a Kubernetes fő csomópontok naplóinak áttekintésére][aks-master-logs] is van lehetőség. A rendszer egy Log Analytics-munkaterületen tárolja monitorozási adatokat, amelyek a Microsoft Azure Portalon, az Azure CLI-n vagy a REST-végponton keresztül érhetők el.

További információ: [Az Azure Kubernetes Service tárolóállapot-monitorozása][container-health].

## <a name="clusters-and-nodes"></a>Fürtök és csomópontok

Az AKS-csomópontok Azure-beli virtuális gépeken futnak. Csatlakoztathat tárterületet csomópontokhoz és podokhoz, frissíthet fürtkomponenseket, és használhat GPU-kat. Az AK támogatja a több csomópontot futtató Kubernetes-fürtöket a vegyes operációs rendszerek és a Windows Server-tárolók támogatásához. A Linux-csomópontok testreszabott Ubuntu operációsrendszer-rendszerképet futtatnak, és a Windows Server-csomópontok testreszabott Windows Server 2019 operációsrendszer-rendszerképet futtatnak.

### <a name="cluster-node-and-pod-scaling"></a>Fürtcsomópont- és podméretezés

Az erőforrások változtatása iránti igények miatt a szolgáltatását futtató fürtcsomópontok és podok száma automatikusan skálázhatók felfelé vagy lefelé. Egyaránt használhatja a podok automatikus vízszintes skálázását és a fürt automatikus skálázását. Ezzel a megközelítéssel az AKS-fürtök a képesek automatikusan alkalmazkodni az igényekhez, és csak a szükséges erőforrásokat használják.

További információ: [Azure Kubernetes Service- (AKS-) fürtök méretezése][aks-scale].

### <a name="cluster-node-upgrades"></a>Fürtcsomópont-frissítés

Az Azure Kubernetes Service számos Kubernetes-verziót kínál. Amint elérhetővé válik egy új verzió az AKS-ben, az Azure Portal vagy az Azure CLI használatával azonnal frissítheti fürtjét. A frissítési folyamat során a csomópontok megfelelően el vannak szigetelve és ki vannak ürítve, hogy minimális hatással legyenek a futó alkalmazásokra.

A verziók életciklusáról további tudnivalókat [Az AKS-ben támogatott Kubernetes verziók][aks-supported versions] című cikkben talál. A frissítés lépéseit [Az Azure Kubernetes Service- (AKS-) fürtök frissítése][aks-upgrade] című cikk ismerteti.

### <a name="gpu-enabled-nodes"></a>GPU-kompatibilis csomópontok

Az AK támogatja a GPU-kompatibilis csomópont-készletek létrehozását. Az Azure jelenleg egy vagy több GPU-t támogató virtuális gépet biztosít. A GPU-kompatibilis virtuális gépek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek.

További információ: [GPU-k használata az AKS-en][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Bizalmas számítástechnikai csomópontok (nyilvános előzetes verzió)

Az AK támogatja az Intel SGX ENKLÁVÉHOZ-alapú bizalmas számítástechnikai csomópont-készletek (DCSv2 virtuális gépek) létrehozását. A bizalmas számítástechnikai csomópontok lehetővé teszik, hogy a tárolók hardveres megbízható végrehajtási környezetben (enklávékban) fussanak. A tárolók közötti elkülönítés, amely a kód integritásával együtt igazolható, segíthet a védelmi részletes biztonsági stratégiában. A bizalmas számítástechnikai csomópontok egyaránt támogatják a bizalmas tárolókat (a meglévő Docker-alkalmazásokat) és az enklávét támogató tárolókat.

További információ: [bizalmas számítástechnikai csomópontok az AK-on][conf-com-node].

### <a name="storage-volume-support"></a>A tárkötetek támogatását

Az alkalmazás számítási feladatának támogatásához csatlakoztathat tárköteteket az állandó adatok tárolásához. Statikus és dinamikus kötetek egyaránt használhatók. Annak függvényében, hogy hány csatlakoztatott podnak kell a tárterületen osztoznia, használhat Azure Disks-alapú tárterületet egyszeres podhozzáférés esetén, vagy Azure Files-alapú tárterületet több egyidejű podhozzáférés esetén.

További információ: [tárolási lehetőségek az AK-ban lévő alkalmazásokhoz][concepts-storage].

A dinamikus állandó kötetek használatának első lépései az [Azure-lemezek][azure-disk] vagy a [Azure Files][azure-files]használatával.

## <a name="virtual-networks-and-ingress"></a>Virtuális hálózatok és bejövő forgalom

Az AKS-fürtök egy már meglévő virtuális hálózaton is üzembe helyezhetők. Ebben a konfigurációban a rendszer a fürtben található összes podhoz hozzárendel egy IP-címet a virtuális hálózatban, így azok közvetlenül kommunikálhatnak a fürtben található más podokkal és a virtuális hálózat egyéb csomópontjaival. A hüvelyek a ExpressRoute és helyek közötti (S2S) VPN-kapcsolatokon keresztül is csatlakozhatnak más szolgáltatásokhoz egy társ virtuális hálózaton, illetve helyszíni hálózatokon.

További információ: az [AK-beli alkalmazások hálózati fogalmai][aks-networking].

A bejövő forgalom megismeréséhez tekintse meg a [HTTP-alkalmazásútválasztás][aks-http-routing] című cikket.

### <a name="ingress-with-http-application-routing"></a>Bejövő forgalom HTTP-alkalmazásútválasztással

A HTTP-alkalmazásútválasztási bővítménynek köszönhetően egyszerűen hozzáférhet az AKS-fürtjére telepített alkalmazásokhoz. Ha engedélyezve van, a HTTP-alkalmazásútválasztási megoldás egy bejövőforgalom-vezérlőt konfigurál az AKS-fürtjén. Az alkalmazások telepítése során a nyilvánosan hozzáférhető DNS-neveket a rendszer automatikusan konfigurálja. A HTTP-alkalmazásútválasztás meghatároz egy DNS-zónát, és integrálja az AKS-fürttel. Ezután a Kubernetes bejövő forgalmának erőforrásai a szokásos módon üzembe helyezhetők.

A bejövő forgalom megismeréséhez tekintse meg a [HTTP-alkalmazásútválasztás][aks-http-routing] című cikket.

## <a name="development-tooling-integration"></a>Fejlesztési eszközök integrációja

A Kubernetes a fejlesztési és felügyeleti eszközök, például a Helm és a Visual Studio Code Kubernetes-bővítménye gazdag ökoszisztémával rendelkezik. Ezek az eszközök problémamentesen működnek az AKS-szolgáltatással.

Emellett az Azure Dev Spaces gyors, iteratív Kubernetes-fejlesztési élményt biztosít a csapatok számára. Minimális konfigurálással futtathat tárolókat és végezhet rajtuk hibakeresést közvetlenül az AKS-ben. Ismerkedés az [Azure Dev Spaces][azure-dev-spaces] használatával.

A DevOps Starter egyszerű megoldást kínál a meglévő kódok és git-adattárak Azure-ba való bevezetésére. A DevOps Starter automatikusan létrehozza az Azure-erőforrásokat, például az AK-t, az Azure DevOps-szolgáltatások olyan kiadási folyamatát, amely tartalmazza a CI-hez készült Build folyamatot, beállít egy kiadási folyamatot CD-re, majd létrehoz egy Azure Application Insights erőforrást a figyeléshez.

További információ: [DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Docker-rendszerképek támogatása és privát tárolóregisztrációs adatbázis

Az AKS támogatja a Docker rendszerképformátumát. Integrálhatja az AKS-szolgáltatást az Azure Container Registryvel (ACR-rel) a Docker-rendszerképek privát tárolásához.

Privát rendszerkép-tároló létrehozásához lásd: [Azure Container Registry][acr-docs].

## <a name="kubernetes-certification"></a>Kubernetes-tanúsítvány

Az Azure Kubernetes Service (AKS) CNCF-tanúsítvánnyal rendelkezik, mivel megfelel a Kubernetes követelményeinek.

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Kubernetes Service (AKS) az SOC, az ISO, a PCI DSS és a HIPAA szabványnak is megfelel. További információ: [az Microsoft Azure megfelelőségének áttekintése][compliance-doc].

## <a name="next-steps"></a>További lépések

Az Azure CLI gyors útmutatójának segítségével többet tudhat meg az AKS üzembe helyezéséről és felügyeletéről.

> [!div class="nextstepaction"]
> [AKS – rövid útmutató][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/insights/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
