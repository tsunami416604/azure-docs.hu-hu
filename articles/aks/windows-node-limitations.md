---
title: A Windows Server-csomópontkészletek korlátai az Azure Kubernetes szolgáltatásban (AKS)
description: A Windows Server-csomópontkészletek és alkalmazás-munkaterhelések azure Kubernetes-szolgáltatásban (AKS) történő futtatásakor ismert korlátozások megismerése
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366410"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>A Windows Server-csomópontkészletekre és az alkalmazás-munkaterhelések jelenlegi korlátaira az Azure Kubernetes-szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) rendszerben létrehozhat egy csomópontkészletet, amely a Windows Servert a csomópontok vendég operációs rendszerként futtatja. Ezek a csomópontok futtathatnak natív Windows-tárolóalkalmazásokat, például a . Mivel jelentős különbségek vannak abban, hogy a Linux és a Windows operációs rendszer hogyan nyújt tárolótámogatást, néhány gyakori Kubernetes és podtal kapcsolatos szolgáltatás jelenleg nem érhető el a Windows-csomópontkészletekhez.

Ez a cikk ismerteti az AKS Windows Server-csomópontjainak néhány korlátozását és operációsrendszer-koncepcióját. A Windows Server csomópontkészletei jelenleg előzetes verzióban jelennek meg.

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgáló opt-in. Az előzetes verziók "adott verzióban" és "ahogy elérhetők", és nem tartoznak a szolgáltatási szintre vonatkozó szerződések és a korlátozott jótállás hatálya alá. Az AKS-előzeteseket részben fedezi az ügyfélszolgálat a legjobb erőfeszítés alapján. Mint ilyen, ezek a funkciók nem célja a termelés használatra. További információt az alábbi támogatási cikkekben talál:
>
> * [Az AKS támogatási irányelvei][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Mely Windows operációs rendszerek támogatottak?

Az AKS a Windows Server 2019-et használja gazdaoperációs rendszer-verzióként, és csak a folyamatelkülönítést támogatja. Más Windows Server-verziók használatával készült tárolólemezképek nem támogatottak. [Windows-tárolóverzió-kompatibilitás][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>A Kubernetes más Windows és Linux rendszeren?

A Window Server-csomópontkészlet támogatása tartalmaz néhány korlátozást, amelyek a Kubernetes-projekt felsőáramú Windows Server-ének részét képezik. Ezek a korlátozások nem az AKS-re vonatkoznak. A Kubernetes-ben található Windows Server upstream támogatásáról a [Kubernetes projekt Kubernetes-dokumentum Bevezetés a Windows-ba támogatásának][intro-windows] [Támogatott funkciók és korlátozások][upstream-limitations] című szakaszában talál további információt.

Kubernetes történelmileg Linux-központú. Az upstream [Kubernetes.io][kubernetes] webhelyszámos példa Linux-csomópontokon való használatra szolgál. Windows Server-tárolókat használó központi telepítések létrehozásakor az operációs rendszer szintjén a következő szempontok érvényesek:

- **Identitás** – A Linux egy egész felhasználói azonosító (UID) alapján azonosítja a felhasználót. A felhasználó rendelkezik egy alfanumerikus felhasználónévvel is a bejelentkezéshez, amelyet a Linux a felhasználó UID azonosítójára fordít le. Hasonlóképpen a Linux azonosítja a felhasználói csoportot egy egész csoportazonosító (GID) alapján, és lefordítja a csoport nevét a megfelelő GID-re.
    - A Windows Server nagyobb bináris biztonsági azonosítót (SID) használ, amely a Windows Security Access Manager (SAM) adatbázisában található. Ez az adatbázis nincs megosztva az állomás és a tárolók között, illetve a tárolók között.
- **Fájlengedélyek** – A Windows Server nem az engedélyek és az UID+GID bitmaszkja alapján használ hozzáférés-vezérlési listát.
- **Fájlelérési utak** - a Windows Server rendszerben a / használata a \ parancsot használja a /.
    - A köteteket csatlakoztató pod specifikációkban adja meg helyesen a Windows Server-tárolók elérési útját. Például ahelyett, hogy egy Linux-tárolóban *lévő /mnt/volume* csatlakoztatási pont lenne, adjon meg egy meghajtóbetűjelet és helyet, például *a /K/Volume kapcsolót,* amelyet *K:* meghajtóként szeretne csatlakoztatni.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Milyen típusú lemezek támogatottak a Windows rendszerben?

Az Azure Disks és az Azure Files a támogatott kötettípusok, amelyek NTFS-kötetekként érhetők el a Windows Server tárolóban.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Futtathatók csak windowsos fürtök az AKS-ben?

Az AKS-fürt fő csomópontjait (a vezérlősíkot) az AKS üzemelteti, a szolgáltatás nem lesz elérhető a fő összetevőket tároló csomópontok operációs rendszerének. Minden AKS-fürt egy alapértelmezett első csomópontkészlettel jön létre, amely Linux alapú. Ez a csomópontkészlet rendszerszolgáltatásokat tartalmaz, amelyek szükségesek a fürt működéséhez. Javasoljuk, hogy legalább két csomópontot futtasson az első csomópontkészletben a fürt megbízhatóságának és a fürtműveletek képességének biztosítása érdekében. Az első Linux-alapú csomópontkészlet csak akkor törölhető, ha maga az AKS-fürt törlődik.

## <a name="what-network-plug-ins-are-supported"></a>Milyen hálózati beépülő modulok támogatottak?

Az AKS-fürtök windowsos csomópontkészletekkel kell használniuk az Azure CNI (advanced) hálózati modell. Kubenet (alapszintű) hálózatnem támogatott. A hálózati modellek közötti különbségekről az [AKS-ben található alkalmazások hálózati fogalmai][azure-network-models]című témakörben talál további információt. - Az Azure CNI hálózati modell további tervezést és szempontokat igényel az IP-cím kezeléséhez. Az Azure CNI megtervezéséről és megvalósításáról az [Azure CNI-hálózat konfigurálása az AKS-ben című témakörben][configure-azure-cni]talál további információt.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Megváltoztathatom a maximumot? # a hüvely ek csomópontonként?

Igen. A rendelkezésre álló következményekről és lehetőségekről a Podok maximális száma című [témakörben található.][maximum-number-of-pods]

## <a name="how-do-patch-my-windows-nodes"></a>Hogyan lehet a Windows-csomópontokat javítani?

Az AKS-ben lévő Windows Server-csomópontokat *frissíteni* kell a javítások javításához és frissítéséhez. A Windows-frissítések nincsenek engedélyezve az AKS csomópontjain. Az AKS új csomópontkészlet-lemezképeket ad ki, amint a javítások elérhetővé teszik, az ügyfelek felelőssége, hogy frissítsék a csomópontkészleteket, hogy naprakészek maradjanak a javításokon és a gyorsjavításon. Ez a kubernetes-verzióra is igaz. Az AKS kiadási megjegyzések jelzik, ha új verziók érhetők el. A Windows Server csomópontkészlet frissítéséről az [AKS csomópontkészlet frissítése][nodepool-upgrade]című témakörben talál további információt.

> [!NOTE]
> A frissített Windows Server-lemezkép csak akkor lesz használva, ha a csomópontkészlet frissítése előtt fürtfrissítés (vezérlősík-frissítés) történt.
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Hogyan forgathatom el a Windows-csomópontkészlet szolgáltatásnév?

Az előzetes verzió során a Windows-csomópontkészletek nem támogatják az egyszerű szolgáltatásrotációt az előzetes verzió korlátozásaként. A szolgáltatásnév frissítéséhez hozzon létre egy új Windows-csomópontkészletet, és telepítse át a podokat a régebbi készletből az újba. Ha ez befejeződött, törölje a régebbi csomópontkészletet.

## <a name="how-many-node-pools-can-i-create"></a>Hány csomópontkészletet hozhatok létre?

Az AKS-fürt legfeljebb 10 csomópontkészlettel rendelkezhet. Ezek a csomópontkészletek legfeljebb 1000 csomópontot használhat. [A csomópontkészlet korlátozásai][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Mi lehet a Windows-csomópontkészleteim neve?

A nevet legfeljebb 6 (hat) karakterre kell tartani. Ez az AKS jelenlegi korlátozása.

## <a name="are-all-features-supported-with-windows-nodes"></a>Minden szolgáltatás támogatja a Windows-csomópontok?

A Windows-csomópontok jelenleg nem támogatják a hálózati házirendeket és a kubenetet. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Futtatható a be- ésnagyítóvezérlők windowsos csomópontokon?

Igen, a Windows Server-tárolókat támogató bejövő adatkezelő az AKS Windows-csomópontjain futtatható.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Használhatom az Azure Dev Spaces-t Windows-csomópontokkal?

Az Azure Dev Spaces jelenleg csak Linux-alapú csomópontkészletek érhető el.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Használhatnak a Windows Server-tárolóim a gMSA-t?

A csoportáltal kezelt szolgáltatásfiókok (gMSA) támogatása jelenleg nem érhető el az AKS-ben.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Használhatom az Azure Monitort a Windows-csomókkal és tárolókkal rendelkező tárolókhoz?

Igen, az Azure Monitor azonban nem gyűjti a naplókat (stdout) a Windows-tárolókból. Továbbra is csatolhatja az élő stream a stdout naplók egy Windows-tárolóból.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Mi a teendő, ha olyan funkcióra van szükségem, amely nem támogatott?

Keményen dolgozunk azon, hogy az Összes szükséges funkciót bevigyük a Windowsba az AKS-ben, de ha hiányosságokat tapasztal, a nyílt forráskódú, upstream [aks-engine][aks-engine] projekt egyszerű és teljes mértékben testreszabható módot biztosít a Kubernetes azure-beli futtatására, beleértve a Windows-támogatást is. Kérjük, győződjön meg róla, hogy nézd meg a menetrend funkciók érkező [AKS ütemtervet][aks-roadmap].

## <a name="next-steps"></a>További lépések

A Windows Server-tárolók AKS-ben való ismerkedéséhez [hozzon létre egy csomópontkészletet, amely a Windows Server alkalmazást futtatja Az AKS rendszerben.][windows-node-cli]

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
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
