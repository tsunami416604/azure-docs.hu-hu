---
title: Alapelvei – biztonság az Azure Kubernetes-szolgáltatás (AKS)
description: Ismerje meg az Azure Kubernetes Service (AKS), beleértve a mester és csomópont kommunikációs, a hálózati házirendek és a Kubernetes titkos kulcsok biztonsági.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 66fc5c92410118f4e0042738d2107b272d68f9bf
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240337"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Biztonsággal kapcsolatos fogalmait, alkalmazások és-fürtök az Azure Kubernetes Service (AKS)

Alkalmazás számítási feladatainak futtatása az Azure Kubernetes Service (AKS), az ügyféladatok védelme érdekében a fürt biztonsági egy fő szempont. Kubernetes például tartalmazza a biztonsági összetevők *hálózati házirendek* és *titkok*. Az Azure majd hozzáadja az összetevők, például a hálózati biztonsági csoportok és előkészített a fürtfrissítések. A biztonsági összetevők kombinálják, így az AKS-fürt, az operációs rendszer legújabb biztonsági frissítéseket és a Kubernetes-kiadások futtatja, és a biztonságos a pod-forgalom és a bizalmas hitelesítő adatokat a hozzáférést.

Ez a cikk bemutatja az alapfogalmakat, amelyek az aks-ben az alkalmazások biztonságossá:

- [Fő összetevők biztonsági](#master-security)
- [Csomópont-biztonság](#node-security)
- [Fürt frissítése](#cluster-upgrades)
- [Hálózati biztonság](#network-security)
- [Kubernetes titkos kulcsok](#kubernetes-secrets)

## <a name="master-security"></a>Fő biztonsági

Az aks-ben a fő Kubernetes-összetevők a Microsoft által felügyelt szolgáltatás részét képezik. Minden egyes AKS-fürtöt tartalmaz a saját single-bérlős, dedikált Kubernetes fő biztosít az API-kiszolgálóhoz, a Feladatütemező, stb. A fő felügyelt és a Microsoft által karbantartott.

Alapértelmezés szerint a Kubernetes API-kiszolgáló nyilvános IP-címet használ, és a teljes tartománynév (FQDN) nevet. Szabályozhatja, hogy a Kubernetes szerepköralapú hozzáférés-vezérlés és az Azure Active Directory API-kiszolgálóhoz való hozzáférést. További információkért lásd: [az aks-sel az Azure AD-integrációs][aks-aad].

## <a name="node-security"></a>Csomópont-biztonság

AKS-csomópontok olyan Azure virtuális gépek kezelése és fenntartása. A csomópontok egy optimalizált Moby tároló modullal Ubuntu Linux-disztribúció futtatható. AKS-fürt létrehozásakor vagy skálázható fel, a rendszer automatikusan telepíti a csomópontok a legújabb operációs rendszer biztonsági frissítéseit és konfigurációkat.

Az Azure platform automatikusan alkalmazza az éjszakai történik a csomópontok operációs rendszer biztonsági javítások. Ha egy operációs rendszer biztonsági frissítést igényel a gazdagépet újra kell indítani, hogy újraindítás nem automatikusan történik. Manuálisan indítsa újra a csomópontokat, vagy egy általánosan használt megközelítés használandó [Kured][kured], egy nyílt forráskódú újraindítás démon a Kubernetes esetében. Kured fut, mint egy [DaemonSet] [ aks-daemonsets] , és figyeli a-fájljának jelenlétét, arról, hogy a számítógép újraindítása nem szükséges minden egyes csomópont. Újraindítások felügyelt ugyanazzal a fürtön [Ez a három csomópontunk, és a kiürítési folyamat](#cordon-and-drain) mint a fürtfrissítések.

Csomópontok nem hozzárendelt nyilvános IP-címeket a rendszer üzembe helyezi egy privát virtuális hálózat alhálózatához. Hibaelhárítás és kezelése céljából az SSH alapértelmezés szerint engedélyezve van. Az SSH-hozzáférés csak akkor használható a belső IP-cím használatával.

A tárolást, a csomópontok az Azure Managed Disks használata. A legtöbb Virtuálisgép-csomópontok méretét ezek a prémium szintű lemez nagy teljesítményű SSD-k által támogatott. A felügyelt lemezeken tárolt adatok automatikus titkosítása az Azure platformon. Javíthatja a redundancia, ezeket a lemezeket is biztonságosan replikálódnak az Azure-adatközpont.

Kubernetes-környezetekből AKS vagy máshol, jelenleg nem teljesen biztonságos megerősítve a rosszindulatú több-bérlős alkalmazásokhoz. További biztonsági szolgáltatások, például a *Pod biztonsági házirendek* vagy további részletes szerepköralapú hozzáférés-vezérlést (RBAC) csomópontok megnehezítik a biztonsági rések kiaknázását. Igaz biztonsági megerősítve a rosszindulatú több-bérlős-alapú számítási feladatait, azonban a hipervizort, hogy bízzon meg biztonsági csak szintjét. A Kubernetes esetében a biztonsági tartományához lesz a teljes fürtöt, nem az egyes csomópontok. Az ilyen típusú megerősítve a rosszindulatú több-bérlős számítási feladatokhoz fizikailag elkülönített fürtök kell használnia. Számítási feladatok elkülönítésére módjairól további információkért lásd: [ajánlott eljárásai az aks-ben a fürt elkülönítési][cluster-isolation],

## <a name="cluster-upgrades"></a>Fürt frissítése

Biztonsági és megfelelőségi, vagy a legújabb funkciók használatához az Azure biztosít, amellyel a frissítés az AKS-fürt és az összetevők eszközök. A frissítési vezénylési mind a Kubernetes fő- és összetevőket tartalmazza. Megtekintheti egy [elérhető Kubernetes-verziók listája](supported-kubernetes-versions.md) az AKS-fürt számára. A frissítési folyamat elindításához meg kell adni az egyik rendelkezésre álló verzió. Az Azure, majd biztonságosan cordons és kiüríti az AKS-csomópontok és hajtja végre a frissítést.

### <a name="cordon-and-drain"></a>Fekvő terület és kiürítési

A frissítés során így az új podok nem ütemezett rajtuk a AKS-csomópontok külön-külön szigetelve vannak a fürtből. A csomópontok ezután ürítve, és frissíteni az alábbiak szerint:

- Meglévő podok szabályosan leállt, és a többi csomópont ütemezve.
- A csomópont újraindul, a frissítési folyamat befejeződött, és illesztések visszakerül az AKS-fürtöt.
- Podok ütemezett futtassa újra a rajtuk.
- A fürt következő csomópontjára van szigetelve, és használja ugyanazt a folyamatot, amíg minden csomópont frissítése sikeresen ürítve.

További információkért lásd: [AKS-fürt frissítése][aks-upgrade-cluster].

## <a name="network-security"></a>Hálózati biztonság

Kapcsolati és biztonsági a helyszíni hálózatokkal az AKS-fürtöt meglévő Azure virtuális hálózati alhálózatra helyezheti üzembe. Ezeket a virtuális hálózatokat az Azure Site-to-Site VPN vagy Express Route kapcsolaton keresztül érik el a helyszíni hálózat lehet. Kubernetes bejövő tartományvezérlők privát, belső IP-címmel lehet definiálni, így a szolgáltatások elérhetők csak a belső hálózati kapcsolaton keresztül.

### <a name="azure-network-security-groups"></a>Azure-beli hálózati biztonsági csoportok

A forgalmat a virtuális hálózatok szűrése, az Azure hálózati biztonsági csoport szabályai használ. Ezek a szabályok határozzák meg, a forrás és cél IP-címtartományok, portok és protokollok, hogy engedélyezi vagy megtagadja a hozzáférést az erőforrásokhoz. Alapértelmezett szabályok jönnek létre a Kubernetes API-kiszolgálóhoz a TLS-forgalom engedélyezéséhez. Terheléselosztók, portleképezéseit vagy bejövő útvonal létrehozhat olyan szolgáltatásokat, mint az AKS automatikusan módosítja a hálózati biztonsági csoport, a forgalom áramlásának biztosításához.

## <a name="kubernetes-secrets"></a>Kubernetes titkos kulcsok

A Kubernetes *titkos* bizalmas adatok behelyezése podok, például a hozzáférési hitelesítő adatokhoz vagy kulcsokhoz szolgál. Először hozzon létre egy titkos kulcsot, a Kubernetes API-val. A pod vagy a központi telepítés határozza meg, ha egy adott titkos kód lehet igényelni. A titkos kulcs tárolása pedig a titkos kulcsok csupán útmutatóul szolgálnak a csomópontokra, amelyeken az ütemezett podot működéséhez szükség van rá *tmpfs*nem írt lemezre. A legutóbbi pod egy csomóponton, amely megköveteli a titkos kulcs törlése esetén a titkos kulcsot a csomópont tmpfs lesz törölve. Titkos kulcsok egy adott névtéren belül vannak tárolva, és csak az azonos névtérben podok hozzáférhet.

Titkos kulcsok használata csökkenti a bizalmas adatokat a pod vagy a service YAML manifest definiált. Ehelyett lehetősége kérni a Kubernetes API-kiszolgálóhoz a YAML-jegyzékfájlban részeként tárolt titkos kulcsot. Ez a módszer csak az adott pod hozzáférést biztosít a titkos kulcsot.

## <a name="next-steps"></a>További lépések

Ismerkedés az AKS-fürtök védelme, lásd: [AKS-fürt frissítése][aks-upgrade-cluster].

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a fürt biztonsági és frissítései az aks-ben][operator-best-practices-cluster-security].

És további információkat az alapvető Kubernetes AKS fogalmait tekintse meg a következő cikkeket:

- [Kubernetes AKS-fürtök / és a számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes / AKS-identitásnak][aks-concepts-identity]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes és az AKS-tároló][aks-concepts-storage]
- [Kubernetes AKS méretezése /][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
