---
title: Fogalmak – Biztonság az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg az Azure Kubernetes-szolgáltatás (AKS) biztonságát, beleértve a fő és a csomópont kommunikációját, a hálózati szabályzatokat és a Kubernetes-titkokat.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595943"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) alkalmazás- és fürtkészletei biztonsági fogalmai

Az ügyféladatok védelme érdekében az Azure Kubernetes-szolgáltatás (AKS) alkalmazás-számítási feladatainak futtatásakor a fürt biztonsága kulcsfontosságú szempont. A Kubernetes olyan biztonsági összetevőket tartalmaz, mint a *hálózati házirendek* és *a Titkos kulcsok*. Az Azure ezután hozzáadja az összetevőket, például a hálózati biztonsági csoportok és a hangszerelt fürtfrissítéseket. Ezek a biztonsági összetevők kombinálva tartják az AKS-fürt a legújabb operációs rendszer biztonsági frissítéseit és a Kubernetes-kiadásokat, valamint a biztonságos pod forgalmat és a bizalmas hitelesítő adatokhoz való hozzáférést.

Ez a cikk bemutatja azokat az alapfogalmakat, amelyek biztosítják az alkalmazásokat az AKS-ben:

- [Főösszetevők biztonsága](#master-security)
- [Csomópont biztonsága](#node-security)
- [Fürtfrissítések](#cluster-upgrades)
- [Hálózati biztonság](#network-security)
- [A Kubernetes titkos kódjai](#kubernetes-secrets)

## <a name="master-security"></a>Főbiztonság

Az AKS-ben a Kubernetes fő összetevői a Microsoft által nyújtott felügyelt szolgáltatás részét képezik. Minden AKS-fürt rendelkezik saját egybérlős, dedikált Kubernetes-főkiszolgálóval az API-kiszolgáló, az ütemező stb. Ezt a főkiszolgálót a Microsoft kezeli és karbantartja.

Alapértelmezés szerint a Kubernetes API-kiszolgáló nyilvános IP-címet és egy teljesen minősített tartománynevet (FQDN) használ. Az API-kiszolgálóhoz való hozzáférést a Kubernetes szerepköralapú hozzáférés-vezérlésés az Azure Active Directory használatával szabályozhatja. További információ: [Azure AD integráció az AKS-sel.][aks-aad]

## <a name="node-security"></a>Csomópont biztonsága

Az AKS-csomópontok olyan Azure virtuális gépek, amelyeket ön kezel és karbantart. A Linux-csomópontok optimalizált Ubuntu disztribúciót futtatnak a Moby-tároló futásidejével. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS-ben) optimalizált Windows Server 2019-kiadást futtatnak, és a Moby-tároló futásidejét is használják. Amikor egy AKS-fürt jön létre, vagy felskálázott, a csomópontok automatikusan telepíti a legújabb operációs rendszer biztonsági frissítéseit és konfigurációit.

Az Azure platform automatikusan alkalmazza az operációs rendszer biztonsági javításait a Linux-csomópontokra éjszakánként. Ha egy Linux operációs rendszer biztonsági frissítése gazdagép újraindítását igényli, az újraindítás nem történik meg automatikusan. Manuálisan újraindíthatja a Linux-csomópontokat, vagy gyakori megközelítés a [Kured][kured], a Kubernetes nyílt forráskódú újraindításdémonjának használata. Kured [daemonSet][aks-daemonsets] néven fut, és figyeli az egyes csomópontok egy fájl jelenlétét, jelezve, hogy újraindításra van szükség. Az újraindítások kezelése a fürtön keresztül a fürt frissítésével azonos [kordon- és csatornafolyamat](#cordon-and-drain) használatával lesz kezelve.

Windows Server-csomópontok esetén (jelenleg előzetes verzióban az AKS-ben) a Windows Update nem futtatja automatikusan és nem alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklusa és a saját ellenőrzési folyamat körüli rendszeres ütemezés szerint frissítenie kell az AKS-fürt Windows Server csomópontkészlete(i) rendszert. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és javításokat futtatják, majd eltávolítja a régebbi csomópontokat. Erről a folyamatról további információt az [AKS csomópontkészletének frissítése című témakörben talál.][nodepool-upgrade]

A csomópontok egy privát virtuális hálózati alhálózatba vannak telepítve, nyilvános IP-címek hozzárendelése nélkül. Hibaelhárítási és felügyeleti célokra az SSH alapértelmezés szerint engedélyezve van. Ez az SSH-hozzáférés csak a belső IP-cím használatával érhető el.

A tárolás biztosításához a csomópontok azure felügyelt lemezek et használnak. A legtöbb virtuálisgép-csomópont mérete, ezek prémium szintű lemezek nagy teljesítményű SSD-k által támogatott. A felügyelt lemezeken tárolt adatok automatikusan titkosítva vannak az Azure platformon belül. A redundancia javítása érdekében ezek a lemezek is biztonságosan replikálódik az Azure adatközpontban.

Kubernetes környezetek, az AKS vagy máshol, jelenleg nem teljesen biztonságos az ellenséges több-bérlős használat. További biztonsági funkciók, például *pod biztonsági házirendek* vagy részletesebb szerepkör-alapú hozzáférés-vezérlés (RBAC) a csomópontok megnehezítik a biztonsági réseket. Azonban a valódi biztonság ellenséges több-bérlős számítási feladatok futtatásakor, a hipervizor az egyetlen biztonsági szint, amelyben meg kell bíznia. A Kubernetes biztonsági tartománya a teljes fürtlesz, nem egy adott csomópont. Az ilyen típusú ellenséges több-bérlős számítási feladatok, fizikailag elkülönített fürtök használata kell használnia. A munkaterhelések elkülönítésének módjairól az [AKS-ben a fürtelkülönítésgyakorlati tanácsok című témakörben talál][cluster-isolation]további információt.

## <a name="cluster-upgrades"></a>Fürtfrissítések

A biztonság és a megfelelőség érdekében, vagy a legújabb funkciók használatához az Azure eszközöket biztosít az AKS-fürt és -összetevők frissítésének koordinálására. Ez a frissítés vezénylési tartalmazza a Kubernetes fő- és ügynök-összetevőket is. Megtekintheti az AKS-fürthöz [rendelkezésre álló Kubernetes-verziók listáját.](supported-kubernetes-versions.md) A frissítési folyamat elindításához meg kell adnia az alábbi verziók egyikét. Az Azure ezután biztonságosan kordonok és kiüríti az egyes AKS-csomópontok, és végrehajtja a frissítést.

### <a name="cordon-and-drain"></a>Kordon és lefolyó

A frissítési folyamat során az AKS-csomópontok külön-külön kordonnal vannak elzárva a fürtből, így az új podok nincsenek ütemezve. A csomópontokezután lecsapolt és korszerűsített az alábbiak szerint:

- Egy új csomópont van telepítve a csomópontkészletben. Ez a csomópont a legújabb operációsrendszer-lemezképet és javításokat futtatja.
- A meglévő csomópontok egyike frissítésre van azonosítva. A csomópontpodjai szabályosan levannak állítva, és a csomópontkészlet többi csomópontján vannak ütemezve.
- Ez a meglévő csomópont törlődik az AKS-fürtből.
- A fürt következő csomópontja kordonnal van elkerítve és kiürítve ugyanazzal a folyamattal, amíg az összes csomópontot a frissítési folyamat részeként sikeresen le nem cseréli.

További információt az [AKS-fürt frissítése című témakörben talál.][aks-upgrade-cluster]

## <a name="network-security"></a>Hálózati biztonság

A helyszíni hálózatokkal való kapcsolat és biztonság érdekében telepítheti Az AKS-fürtöt meglévő Azure virtuális hálózati alhálózatokba. Ezek a virtuális hálózatok előfordulhat, hogy egy Azure-hely-hely VPN vagy express route kapcsolat vissza a helyszíni hálózathoz. Kubernetes ingress vezérlők lehet definiálni a magán-, belső IP-címeket, így a szolgáltatások csak ezen a belső hálózati kapcsolaton keresztül érhető el.

### <a name="azure-network-security-groups"></a>Azure-beli hálózati biztonsági csoportok

A virtuális hálózatok forgalmának szűréséhez az Azure hálózati biztonsági csoportszabályokat használ. Ezek a szabályok határozzák meg az erőforrásokhoz való hozzáférést engedélyezett vagy megtagadott forrás- és célIP-tartományokat, portokat és protokollokat. Az alapértelmezett szabályok a Kubernetes API-kiszolgálóra irányuló TLS-forgalom engedélyezéséhez jönnek létre. Amikor terheléselosztókkal, portleképezésekkel vagy bejövő útvonalakkal hoz létre szolgáltatásokat, az AKS automatikusan módosítja a hálózati biztonsági csoportot, hogy a forgalom megfelelően folyjon.

## <a name="kubernetes-secrets"></a>A Kubernetes titkos kódjai

A Kubernetes *titkos kulcsot* bizalmas adatok podok, például a hozzáférési hitelesítő adatok vagy kulcsok. Először hozzon létre egy titkos kulcsot a Kubernetes API használatával. A pod vagy a központi telepítés definiálásakor egy adott titkos kulcsot lehet kérni. A titkos kulcsok csak olyan csomópontok számára vannak megadva, amelyekhez szükség van egy ütemezett podra, és a titkos kulcsot *tmpfs*tárolja, nem lemezre írva. Amikor az utolsó pod egy csomóponton, amely megköveteli a titkos kulcsot törlődik, a titkos kulcs törlődik a csomópont tmpfs törlődik. A titkos kulcsok egy adott névtérben tárolódnak, és csak az ugyanazon a névtéren belül lévő podok férhetnek hozzá.

A titkos kulcsok használata csökkenti a podban vagy a szolgáltatás YAML-jegyzékfájlban definiált bizalmas információkat. Ehelyett a YAML-jegyzékfájl részeként a Kubernetes API-kiszolgálón tárolt titkos kulcsot kéri. Ez a megközelítés csak az adott pod hozzáférést biztosít a titkos. Kérjük, vegye figyelembe: a nyers titkos jegyzékfájlok tartalmazzák a titkos adatokat base64 formátumban (további részletekért lásd a [hivatalos dokumentációt).][secret-risks] Ezért ezt a fájlt bizalmas információként kell kezelni, és soha nem kell elkötelezettnek lennie a forrásellenőrzés iránt.

## <a name="next-steps"></a>További lépések

Az AKS-fürtök védelmének első lépései az [AKS-fürt frissítése][aks-upgrade-cluster].

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a fürtbiztonságés az AKS-frissítések][operator-best-practices-cluster-security] és [az AKS pod-biztonsággal kapcsolatos gyakorlati tanácsok című témakörben.][developer-best-practices-pod-security]

A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS-fürtök és munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identitás][aks-concepts-identity]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes / AKS tároló][aks-concepts-storage]
- [Kubernetes / AKS skála][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
