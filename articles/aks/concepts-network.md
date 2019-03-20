---
title: Alapelvei – az Azure Kubernetes-szolgáltatás (AKS-) hálózat
description: További információ az Azure Kubernetes Service (AKS), beleértve a kubenet és Azure CNI hálózatkezelés, bejövő tartományvezérlők, terheléselosztókat és statikus IP-címek hálózati.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: cbdbf7dcd6269991d23c61d316dcee68e6678171
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58175666"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Alkalmazások az Azure Kubernetes Service (AKS) hálózati fogalmai

Egy tároló-alapú mikroszolgáltatás-alapú megközelítést alkalmazásfejlesztés, az alkalmazás-összetevők együtt kell működniük a feladataik feldolgozásához. Kubernetes, amelyek lehetővé teszik az alkalmazás kommunikációs különféle erőforrásokhoz biztosít. Csatlakozhat, és tegye elérhetővé az alkalmazások, kívül és belül. Magas rendelkezésre állású alkalmazások létrehozásához, betöltheti az alkalmazások terheléselosztása. Összetettebb alkalmazások bejövő forgalom konfigurációt igényelhetnek a TLS/SSL-lezárást vagy továbbítani tudja a több összetevőt. Biztonsági okokból szükség lehet a hálózati forgalom korlátozásához, vagy a podok és a csomópontok között.

Ez a cikk az alapfogalmakat, az aks-ben az alkalmazások hálózati biztosító mutatja be:

- [Szolgáltatások](#services)
- [Azure virtuális hálózatok](#azure-virtual-networks)
- [Bejövő forgalom vezérlők](#ingress-controllers)
- [Hálózati házirendek](#network-policies)

## <a name="kubernetes-basics"></a>A Kubernetes alapjai

Engedélyezi a hozzáférést az alkalmazások, illetve alkalmazás-összetevők kommunikálnak egymással, a Kubernetes virtuális hálózati absztrakciós réteget biztosít. Kubernetes-csomópontokon csatlakoztatva van egy virtuális hálózathoz, és megadhat podok bejövő és kimenő kapcsolat. A *kube-proxy* összetevő ezekre a hálózati funkciókat biztosít minden egyes csomóponton futnak.

A Kubernetes *szolgáltatások* logikailag csoportosítják a podok lehetővé teszik a közvetlen hozzáférést egy IP-címe vagy DNS-nevet és a egy adott porton. Forgalom használatával is terjeszthető a *terheléselosztó*. Az alkalmazás forgalmának összetettebb útválasztását is elérhető a *bejövő tartományvezérlők*. Biztonság és a hálózati forgalmat a podok minden lehetséges a Kubernetes *hálózati házirendek* (előzetes verzióban érhető el az aks-ben).

Az Azure platform egyszerűbbé válik a virtuális hálózatkezelés az AKS-fürtök esetén is segít. Amikor létrehoz egy Kubernetes-terheléselosztó, az alapul szolgáló Azure load balancer erőforrás létrehozta és konfigurálta. Podok hálózati portok megnyitását, a megfelelő Azure-beli hálózati biztonsági csoport szabályai vannak konfigurálva. A HTTP-alkalmazások útválasztása, Azure is konfigurálhatja *külső DNS* új pontjaként konfiguráltak.

## <a name="services"></a>Szolgáltatások

Alkalmazások és szolgáltatások számára a hálózati konfiguráció leegyszerűsítése Kubernetes használ *szolgáltatások* logikailag csoportosíthatja a podok készletét, és adja meg a hálózati kapcsolatot. A következő szolgáltatástípusok érhetők el:

- **Fürt IP-címe** – belső IP-cím használata az AKS-fürtöt hoz létre. Jó, csak belső alkalmazások, amelyek támogatják a fürtön belüli más számítási feladatok számára.

    ![AKS-fürt a fürt IP-forgalom áramlását bemutató ábra.][aks-clusterip]

- **NodePort** -port hozzárendelést hoz létre az alapul szolgáló csomóponton, amely lehetővé teszi az alkalmazás számára érhető el közvetlenül a csomópont IP-cím és port használatával.

    ![Az AKS-fürt NodePort adatforgalmat bemutató ábra.][aks-nodeport]

- **Terheléselosztó** – létrehoz egy Azure load balancer-erőforrás, konfigurálja a külső IP-cím és a terheléselosztó háttérkészletének a kért podok csatlakozik. Ügyfelek forgalom engedélyezésére elérhetőséget az alkalmazást, a terheléselosztási szabályok jönnek létre a kívánt portokat. 

    ![Ábra a Load Balancer adatforgalmat az AKS-fürt][aks-loadbalancer]

    További vezérlőelemek és a bejövő forgalom útválasztását, ehelyett használhat egy [Bejövőforgalom-vezérlőjéhez](#ingress-controllers).

- **ExternalName** -egyszerűbb alkalmazás-hozzáférést egy adott DNS-bejegyzést hoz létre.

Az IP-cím, a terheléselosztók és a szolgáltatások dinamikusan hozzárendelhető, vagy megadhat egy meglévő statikus IP-cím használatára. Belső és a külső statikus IP-címek rendelhetők. A meglévő statikus IP-cím gyakran olyan DNS-bejegyzés van kötve.

Mindkét *belső* és *külső* terheléselosztók hozhatók létre. Belső terheléselosztók csak rendelt magánhálózati IP-cím, így nem érhető el az internetről.

## <a name="azure-virtual-networks"></a>Azure-alapú virtuális hálózatok

Az aks-ben helyezhető üzembe egy fürtöt, amely a következő két hálózati következő modell valamelyikét használja:

- *Kubenet* hálózat - erőforrások általában létrehozza és beállítja, az AKS-fürt üzembe lesz helyezve a hálózaton.
- *Az Azure Container hálózati adapter (CNI)* hálózati használata – az AKS-fürt csatlakozik-e meglévő virtuális hálózati erőforrásokat és konfigurációkat.

### <a name="kubenet-basic-networking"></a>Hálózatkezelés Kubenet (alapszintű)

A *kubenet* hálózatkezelés lehetőség az AKS-fürt létrehozása az alapértelmezett konfigurációja. A *kubenet*, csomópontok IP-cím lekérése az Azure virtuális hálózat alhálózatához. Podok logikailag különböző címtér az IP-címet kapnak a csomópontok az Azure virtuális hálózat alhálózatához. Hálózati címfordítás (NAT) majd van konfigurálva, így a podok elérheti az erőforrásokat az Azure-beli virtuális hálózaton. A forgalmat a forrás IP-címe NAT lenne a csomópont elsődleges IP-cím.

Csomópontok használata a [kubenet] [ kubenet] Kubernetes beépülő modult. Hagyhatja, hogy hozzon létre és konfigurálja a virtuális hálózatok az Ön számára, vagy dönt, hogy egy meglévő virtuális hálózat alhálózatának az AKS-fürt üzembe helyezése az Azure platformon. Újra csak a csomópontok egy irányítható IP-címet kap, és a podok NAT használatával kommunikáljon az AKS-fürtön kívül más erőforrásokkal. Ez a megközelítés nagymértékben csökkenti a podok használatára a hálózati tárhelyre fenn kell IP-címek számát.

További információkért lásd: [konfigurálni az AKS-fürt hálózati kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (advanced) networking

Az Azure CNI minden pod IP-címet kap az alhálózatról, és közvetlenül is elérhetők. Ezen IP-címek a hálózati hely belül egyedinek kell lennie, és előre kell készülni. Minden egyes csomópont rendelkezik egy konfigurációs paraméter, amely támogatja a podok maximális számát. IP-címek száma csomópontonként megfelelőjét majd számára vannak fenntartva meghozni a csomóponton. Ez a megközelítés további tervezést igényel, és IP-cím Erőforrásfogyás vagy kell építeni a teljesítményszinten növekedésével nagyobb alhálózat a fürtök gyakran vezet.

Csomópontok használata a [Azure tároló-hálózati adapter (CNI)] [ cni-networking] Kubernetes beépülő modult.

![A hidak egyes egyetlen Azure virtuális hálózathoz csatlakozó két csomópont bemutató ábra.][advanced-networking-diagram]

Az Azure CNI kubenet hálózatkezelés során a következő szolgáltatásokat biztosítja:

- A fürt minden pod IP-címet a virtuális hálózatban van hozzárendelve. A podok közvetlenül kommunikálhatnak a fürt más podok és a virtuális hálózat más csomópontjaira.
- Azure-szolgáltatások, például az Azure Storage és SQL DB biztonságosan kapcsolódhat a podok egy alhálózathoz, hogy engedélyezve van.
- Létrehozhat felhasználó által megadott útvonalak (udr-t), a podok forgalom irányítása hálózati virtuális berendezésre.

További információkért lásd: [az AKS-fürt konfigurálása Azure CNI][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Bejövő forgalom vezérlők

Amikor létrehoz egy terheléselosztó szolgáltatás típusú, egy alapul szolgáló Azure load balancer erőforrás jön létre. A terheléselosztó van konfigurálva a szolgáltatás egy adott porton a podok forgalom elosztása. A terheléselosztó csak akkor működik a rétegben 4 – a szolgáltatás nem észleli a tényleges alkalmazások, és nem hajthat végre semmilyen további útválasztási szempontok.

*Bejövő forgalom tartományvezérlők* 7. rétegben működik, és intelligensebb szabályok segítségével alkalmazás forgalom elosztása. Egy közös Bejövőforgalom-vezérlőjéhez használata irányíthatja a HTTP-forgalom a bejövő URL-cím alapján különböző alkalmazásokhoz.

![Az AKS-fürt bejövő adatforgalmat bemutató ábra.][aks-ingress]

Az aks-ben hozzon létre egy bejövő erőforrást vagy hasonló nginx-et, vagy használja az AKS HTTP útválasztási funkcióval. Ha engedélyezi a HTTP-alkalmazások útválasztása az AKS-fürt, az Azure platform hoz létre a Bejövőforgalom-vezérlőt és a egy *külső DNS-* vezérlő. Kubernetes bejövő új erőforrások jönnek létre, mert a szükséges DNS-beli A rekordokat a DNS-zóna fürtre jellemző jönnek létre. További információkért lásd: [üzembe helyezése a HTTP-alkalmazások útválasztása][aks-http-routing].

Bejövő forgalom egy másik gyakori funkcióját a TLS/SSL-lezárást. A HTTPS-en keresztül elérhető nagy méretű webes alkalmazások a TLS-lezárást lehet kezelni, a bejövő forgalom erőforrásnak, nem pedig maga az alkalmazás. Ahhoz, hogy a TLS-tanúsítvány automatikus létrehozása és konfigurálása, konfigurálhatja a bejövő forgalom erőforrás szolgáltatók, például hozzunk titkosítására használandó. Egy nginx-et Bejövőforgalom-vezérlőjéhez konfigurálása most titkosítása további információkért lásd: [bejövő forgalom és TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoport szűri a forgalmat a virtuális gépek esetében például az AKS-csomópontok. Szolgáltatások, például a terheléselosztó létrehozása az Azure platform automatikusan beállítja az minden olyan hálózati biztonsági csoport szabályait, amelyek szükségesek. Az AKS-fürt podok forgalom szűrése hálózati biztonsági csoport szabályokat manuálisan ne konfigurálja. A Kubernetes szolgáltatásjegyzékek részeként minden szükséges portok és a továbbítás határozzák meg, és lehetővé teszik az Azure platform létrehozni vagy frissíteni a megfelelő szabályokat. Is használhatja hálózati házirendeket a következő szakaszban leírt módon podok forgalmat szűrő szabályokkal alkalmazhatja.

## <a name="network-policies"></a>Hálózati házirendek

Alapértelmezés szerint egy AKS-fürt összes podok küldhet és korlátozások nélkül forgalom fogadására. A nagyobb biztonság érdekében érdemes olyan szabályok, amelyek vezérlik a forgalmat. Háttéralkalmazásokhoz gyakran csak szükséges előtér-szolgáltatások érhetők el, vagy az adatbázis-összetevői csak elérhetők az alkalmazásrétegek, amely csatlakozni hozzájuk.

A hálózati házirend Kubernetes funkciója jelenleg előzetes verzióban érhető el az aks-ben, amely lehetővé teszi a podok közötti adatforgalom szabályozásához. Ha szeretné, beállítások, például a hozzárendelt címkék, névtérre vagy forgalmat port alapján adatforgalom engedélyezéséhez vagy letiltásához. Hálózati biztonsági csoportok további nem podok AKS-csomópontok számára. A hálózati házirendek használata egy megfelelőbbek, natív módon a forgalom szabályozásához. Podok dinamikusan az AKS-fürt létrehozásakor, a szükséges hálózati házirendeket a rendszer automatikusan elvégez.

További információkért lásd: [podok hálózati házirendek segítségével az Azure Kubernetes Service (AKS) közötti adatforgalom biztonságossá][use-network-policies].

## <a name="next-steps"></a>További lépések

AKS-hálózatkezelés – első lépések, létrehozása és konfigurálása egy AKS-fürtöt a saját IP-címtartományok használatával [kubenet] [ aks-configure-kubenet-networking] vagy [Azure CNI] [ aks-configure-advanced-networking].

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a hálózati kapcsolatot és az aks-ben biztonsági][operator-best-practices-network].

És további információkat az alapvető Kubernetes AKS fogalmait tekintse meg a következő cikkeket:

- [Kubernetes AKS-fürtök / és a számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes / AKS hozzáférési és azonosító][aks-concepts-identity]
- [Kubernetes / AKS biztonsági][aks-concepts-security]
- [Kubernetes és az AKS-tároló][aks-concepts-storage]
- [Kubernetes AKS méretezése /][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
