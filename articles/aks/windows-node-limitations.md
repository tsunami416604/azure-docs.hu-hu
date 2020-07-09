---
title: A Windows Server Node Pool korlátozásai
titleSuffix: Azure Kubernetes Service
description: Ismerje meg az ismert korlátozásokat a Windows Server Node-készletek és az alkalmazás-munkaterhelések Azure Kubernetes szolgáltatásban (ak) való futtatásakor
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: c420eb850313900d3726b93dd97f911a428d3560
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85339874"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>A Windows Server Node-készletek és az alkalmazások számítási feladatainak jelenlegi korlátai az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) olyan csomópont-készletet hozhat létre, amely a Windows Server rendszert futtatja a csomópontokon lévő vendég operációs rendszerként. Ezek a csomópontok natív Windows-tárolókat futtathatnak, például a .NET-keretrendszerre épülő alkalmazásokat. Mivel a Linux és a Windows operációs rendszerekben jelentős különbségek vannak a tárolók támogatásában, néhány gyakori Kubernetes és Pod-hez kapcsolódó szolgáltatás jelenleg nem érhető el a Windows-csomópontok készletei számára.

Ez a cikk a Windows Server-csomópontokra vonatkozó korlátozásokat és operációsrendszer-fogalmakat ismerteti az AK-ban.

## <a name="which-windows-operating-systems-are-supported"></a>Mely Windows operációs rendszerek támogatottak?

Az AK a Windows Server 2019-es verzióját használja gazda operációs rendszerként, és csak a folyamatok elkülönítését támogatja. A más Windows Server-verziók használatával létrehozott tároló-lemezképek nem támogatottak. [Windows-tároló verziójának kompatibilitása][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>A Kubernetes különbözik a Windows és a Linux rendszereken?

A Windows Server-csomópontok készletének támogatása olyan korlátozásokat tartalmaz, amelyek a felsőbb rétegbeli Windows-kiszolgáló részét képezik a Kubernetes projektben. Ezek a korlátozások nem kifejezetten az AK-ra vonatkoznak. További információ a Windows Server rendszerhez készült, a Kubernetes-ben elérhető felső szintű támogatásról: a Kubernetes-projektből származó [Windows-támogatásra való bevezetés][intro-windows] [támogatott funkcionalitás és korlátozások][upstream-limitations] című szakasza.

A Kubernetes történelmileg Linux-alapú. A felsőbb rétegbeli [Kubernetes.IO][kubernetes] webhely számos példája Linux-csomópontokon használható. Ha Windows Server-tárolókat használó központi telepítéseket hoz létre, a következő szempontokat kell figyelembe venni az operációs rendszer szintjén:

- **Identitás** – a Linux a felhasználót egy egész számú felhasználói azonosító (UID) alapján azonosítja. A felhasználó a bejelentkezéshez alfanumerikus felhasználónevet is tartalmaz, amelyet a Linux a felhasználó UID azonosítójának fordít. A Linux hasonló módon azonosítja a felhasználói csoportokat egy egész számú csoportazonosító (GID) alapján, és a csoport nevét a hozzá tartozó GID-re fordítja.
    - A Windows Server egy nagyobb bináris biztonsági azonosítót (SID) használ, amelyet a Windows Security Access Manager (SAM) adatbázisa tárol. Ez az adatbázis nincs megosztva a gazdagép és a tárolók között, vagy a tárolók között.
- **Fájlengedélyek –** a Windows Server SID-alapú hozzáférés-vezérlési listát használ a bitmaszk és az UID + GID helyett
- **Fájlelérési utak** – a Windows Server-konvenció a \ helyett a következőt használja:/.
    - A kötetek csatlakoztatására szolgáló Pod specifikációban a Windows Server-tárolók megfelelő elérési útját kell megadnia. Például egy Linux-tárolóban a */mnt/Volume* csatlakoztatási pontja helyett olyan meghajtóbetűjelet és helyet kell megadnia, mint amilyen például a */K/Volume* a *K:* meghajtó csatlakoztatására.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Milyen típusú lemezeket támogat a Windows?

Az Azure-lemezek és a Azure Files támogatott mennyiségi típusok, amelyek a Windows Server-tárolóban NTFS-kötetként érhetők el.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Futtathatok csak Windows rendszerű fürtöket az AK-ban?

Egy AK-fürt fő csomópontjai (a vezérlő síkja) a szolgáltatásban találhatók, a fő összetevőket üzemeltető csomópontok operációs rendszere nem lesz elérhető. Az összes AK-fürt a Linux-alapú alapértelmezett első csomópont-készlettel jön létre. Ez a csomópont-készlet rendszerszolgáltatásokat tartalmaz, amelyek szükségesek a fürt működéséhez. Azt javasoljuk, hogy legalább két csomópontot futtasson az első csomópont-készletben a fürt megbízhatóságának és a fürt működésének a biztosításához. Az első Linux-alapú csomópont-készlet nem törölhető, ha maga az AK-fürt nem törlődik.

## <a name="what-network-plug-ins-are-supported"></a>Milyen hálózati beépülő modulok támogatottak?

A Windows Node-készletekkel rendelkező AK-fürtöknek az Azure CNI (Advanced) hálózatkezelési modellt kell használniuk. A Kubenet (alapszintű) hálózatkezelés nem támogatott. További információ a hálózati modellekkel kapcsolatos különbségekről: az [AK-beli alkalmazások hálózati fogalmai][azure-network-models]. – Az Azure CNI Network Model további tervezést és szempontokat igényel az IP-címek kezeléséhez. Az Azure-CNI tervezésével és megvalósításával kapcsolatos további információkért lásd: [Az Azure CNI hálózatkezelésének konfigurálása az AK-ban][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Módosítható a max. a hüvelyek száma egy csomóponton?

Igen. Az elérhető hatásokkal és lehetőségekkel kapcsolatban lásd: [hüvelyek maximális száma][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Hogyan végezhető el a Windows-csomópontok javítása?

Az AK-ban lévő Windows Server-csomópontokat *frissíteni* kell a legújabb javítások és frissítések beszerzéséhez. A Windows-frissítések nincsenek engedélyezve az AK-beli csomópontokon. Az AK az új csomópont-készlet rendszerképeit azonnal kibocsátja, amint a javítások elérhetők, így az ügyfelek feladata a csomópont-készletek frissítése, hogy a jelenlegi javításokon és gyorsjavításokon maradjon. Ez a használatban lévő Kubernetes-verzióra is igaz. Az AK kibocsátási megjegyzései azt jelzik, hogy mikor érhetők el új verziók. A Windows Server-csomópontok készletének frissítésével kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

> [!NOTE]
> A frissített Windows Server-rendszerkép csak akkor lesz használatban, ha a fürt frissítése (a vezérlési sík frissítése) a csomópont-készlet frissítése előtt lett elvégezve.
>

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Miért jelenik meg hibaüzenet, amikor új Windows Agent-készletet próbálok létrehozni?

Ha a fürtöt február 2020 előtt hozta létre, és még soha nem hajtotta meg a fürt frissítési műveleteit, a fürt továbbra is egy régi Windows-rendszerképet használ. Előfordulhat, hogy a következőhöz hasonló hibaüzenetet észlelt:

"A központi telepítési sablonban hivatkozott rendszerképek alábbi listája nem található: közzétevő: MicrosoftWindowsServer, ajánlat: WindowsServer, SKU: 2019-Datacenter-Core-smalldisk-2004, verzió: Latest. https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimageAz elérhető rendszerképek keresésével kapcsolatos utasításokért tekintse meg a következő témakört:.

A probléma megoldásához:

1. Frissítse a [fürt vezérlőjét][upgrade-cluster-cp]. Ezzel frissíti a rendszerkép-ajánlatot és a közzétevőt.
1. Hozzon létre új Windows Agent-készleteket.
1. Windows-hüvelyek áthelyezése meglévő Windows Agent-készletekből új Windows Agent-készletekbe.
1. Régi Windows-ügynök készletek törlése.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hogyan elforgatja a szolgáltatásnevet a Windows-csomópontos készlethez?

A Windows-csomópontok nem támogatják a szolgáltatás egyszerű elforgatását. Az egyszerű szolgáltatás frissítéséhez hozzon létre egy új Windows-csomópontot, és telepítse át a hüvelyeket a régi készletből az újat. Ha ez befejeződött, törölje a régi csomópont-készletet.

## <a name="how-many-node-pools-can-i-create"></a>Hány Node-készletet hozhatok létre?

Az AK-fürt legfeljebb 10 csomópont-készletet tartalmazhat. Ezekhez a csomópont-készletekhez legfeljebb 1000 csomópont tartozhat. [Csomópont-készletre vonatkozó korlátozások][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Mire használhatom a Windows-csomópontok készleteit?

A nevet legfeljebb 6 (hat) karakterrel kell megtartania. Ez az AK jelenlegi korlátozása.

## <a name="are-all-features-supported-with-windows-nodes"></a>Támogatottak-e a Windows-csomópontok összes funkciója?

A hálózati házirendek és a kubenet a Windows-csomópontok esetében jelenleg nem támogatottak.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Futtathatok bejövő vezérlőket Windows-csomópontokon?

Igen, a Windows Server-tárolókat támogató bejövő adatkezelők a Windows-csomópontokon is futhatnak az AK-ban.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Használhatom az Azure dev Spaces szolgáltatást Windows-csomópontokkal?

Az Azure dev Spaces jelenleg csak a Linux-alapú csomópont-készletek esetében érhető el.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Használhatom a Windows Server-tárolókat a gMSA?

A csoportosan felügyelt szolgáltatásfiókok (gMSA) támogatása jelenleg nem érhető el az AK-ban.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Használhatom a Windows-csomópontokkal és-tárolókkal rendelkező tárolók Azure Monitor?

Igen, azonban Azure Monitor nyilvános előzetes verzióban érhető el a naplók (StdOut, stderr) és a Windows-tárolók metrikáinak összegyűjtéséhez. Az stdout-naplók élő streamjét egy Windows-tárolóból is csatlakoztathatja.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Mi a teendő, ha olyan szolgáltatásra van szükségem, amely nem támogatott?

Keményen dolgozunk, hogy az összes olyan funkciót felhasználjuk, amire szüksége van az AK-ban, de ha hiányosságok lépnek fel, a nyílt forráskódú és a felsőbb rétegbeli [AK-motor][aks-engine] projekt egyszerű és teljes mértékben testreszabható módot kínál a Kubernetes Azure-ban való futtatására, beleértve a Windows-támogatást is. Kérjük, tekintse meg a következő, az AK- [útitervtel][aks-roadmap]kapcsolatos szolgáltatások ütemtervét.

## <a name="next-steps"></a>További lépések

A Windows Server-tárolók az AK-ban való megkezdéséhez [hozzon létre egy olyan csomópont-készletet, amely a Windows Server rendszerű][windows-node-cli]

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
