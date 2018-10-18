---
title: Alapelvei – az Azure Kubernetes-szolgáltatás (AKS-) hálózat
description: További információ az Azure Kubernetes Service (AKS), beleértve az alapszintű és speciális hálózatkezelés, bejövő tartományvezérlők, terheléselosztókat és statikus IP-címek hálózati.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381166"
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

A Kubernetes *szolgáltatások* logikailag csoportosítják a podok lehetővé teszik a közvetlen hozzáférést egy IP-címe vagy DNS-nevet és a egy adott porton. Forgalom használatával is terjeszthető a *terheléselosztó*. Az alkalmazás forgalmának összetettebb útválasztását is elérhető a *bejövő tartományvezérlők*. Biztonság és a hálózati forgalmat a podok minden lehetséges a Kubernetes *hálózati házirendek*.

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

- *Alapszintű* hálózatkezelés – a hálózati erőforrások létrehozása és az AKS-fürt üzembe lesz helyezve konfigurálva.
- *Speciális* hálózati használata – az AKS-fürt csatlakozik-e meglévő virtuális hálózati erőforrásokat és konfigurációkat.

### <a name="basic-networking"></a>Egyszerű hálózatkezelés

A *alapszintű* hálózatkezelés lehetőség az AKS-fürt létrehozása az alapértelmezett konfigurációja. Az Azure platform kezeli a hálózati konfigurációt a fürt és a podokat. Alapszintű hálózatkezelési célszerű olyan telepítésekhez, amelyek nem igényelnek egyéni virtuális hálózati konfiguráció. Az alapvető hálózati, például az alhálózatok nevének a hálózati konfiguráció vagy az AKS-fürtöt rendelt IP-címtartományok nem lehet definiálni.

Alapszintű hálózatkezelési használatra konfigurált egy AKS-fürt csomópontjain a [kubenet] [ kubenet] Kubernetes beépülő modult.

Alapszintű hálózatkezelési a következő szolgáltatásokat biztosítja:

- A Kubernetes szolgáltatás elérhetővé külső vagy belső az Azure Load Balanceren keresztül.
- Podok a nyilvános interneten lévő erőforrások eléréséhez.

### <a name="advanced-networking"></a>Speciális hálózatkezelés

*Speciális* hálózatkezelés helyezi a podokat megszünteti egy Ön által konfigurált Azure virtuális hálózatban. Ez a virtuális hálózat más Azure-erőforrások és a képességek széles skáláját integráció automatikus kapcsolatot biztosít. Speciális hálózatkezelés akkor megfelelő, a telepítéshez szükséges adott virtuális hálózati konfigurációk, például egy meglévő alhálózat és a kapcsolat. Speciális hálózatkezelés definiálhat ezen alhálózatok nevének és IP-címtartományokat.

A speciális hálózati használatra konfigurált egy AKS-fürt csomópontjain a [Azure Container hálózati adapter (CNI)] [ cni-networking] Kubernetes beépülő modult.

![A hidak egyes egyetlen Azure virtuális hálózathoz csatlakozó két csomópont bemutató ábra.][advanced-networking-diagram]

Speciális hálózatkezelés alapszintű hálózatkezelési keresztül a következő szolgáltatásokat biztosítja:

- Egy meglévő Azure virtuális hálózatot az AKS-fürt üzembe helyezése, vagy hozzon létre egy új virtuális hálózat és alhálózat a fürt számára.
- A fürt minden pod IP-címet a virtuális hálózatban van hozzárendelve. A podok közvetlenül kommunikálhatnak a fürt más podok és a virtuális hálózat más csomópontjaira.
- A pod csatlakozhat más szolgáltatások a virtuális társhálózatban, beleértve a helyszíni hálózathoz az ExpressRoute és site-to-site (S2S) VPN-kapcsolatok. Podok is a helyszíni érhető el.
- Azure-szolgáltatások, például az Azure Storage és SQL DB biztonságosan kapcsolódhat a podok egy alhálózathoz, hogy engedélyezve van.
- Létrehozhat felhasználó által megadott útvonalak (udr-t), a podok forgalom irányítása hálózati virtuális berendezésre.

További információkért lásd: [speciális hálózati az AKS-fürt konfigurálása][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Bejövő forgalom vezérlők

Amikor létrehoz egy terheléselosztó szolgáltatás típusú, egy alapul szolgáló Azure load balancer erőforrás jön létre. A terheléselosztó van konfigurálva a szolgáltatás egy adott porton a podok forgalom elosztása. A terheléselosztó csak akkor működik a rétegben 4 – a szolgáltatás nem észleli a tényleges alkalmazások, és nem hajthat végre semmilyen további útválasztási szempontok.

*Bejövő forgalom tartományvezérlők* 7. rétegben működik, és intelligensebb szabályok segítségével alkalmazás forgalom elosztása. Egy közös Bejövőforgalom-vezérlőjéhez használata irányíthatja a HTTP-forgalom a bejövő URL-cím alapján különböző alkalmazásokhoz.

![Az AKS-fürt bejövő adatforgalmat bemutató ábra.][aks-ingress]

Az aks-ben hozzon létre egy bejövő erőforrást vagy hasonló nginx-et, vagy használja az AKS HTTP útválasztási funkcióval. Ha engedélyezi a HTTP-alkalmazások útválasztása az AKS-fürt, az Azure platform hoz létre a Bejövőforgalom-vezérlőt és a egy *külső DNS-* vezérlő. Kubernetes bejövő új erőforrások jönnek létre, mert a szükséges DNS-beli A rekordokat a DNS-zóna fürtre jellemző jönnek létre. További információkért lásd: [üzembe helyezése a HTTP-alkalmazások útválasztása][aks-http-routing].

Bejövő forgalom egy másik gyakori funkcióját a TLS/SSL-lezárást. A HTTPS-en keresztül elérhető nagy méretű webes alkalmazások a TLS-lezárást lehet kezelni, a bejövő forgalom erőforrásnak, nem pedig maga az alkalmazás. Ahhoz, hogy a TLS-tanúsítvány automatikus létrehozása és konfigurálása, konfigurálhatja a bejövő forgalom erőforrás szolgáltatók, például hozzunk titkosítására használandó. Egy nginx-et Bejövőforgalom-vezérlőjéhez konfigurálása most titkosítása további információkért lásd: [bejövő forgalom és TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Egy hálózati biztonsági csoport szűrő forgalmat a virtuális gépek esetében például az AKS-csomópontok. Szolgáltatások, például a terheléselosztó létrehozása az Azure platform automatikusan beállítja az minden olyan hálózati biztonsági csoport szabályait, amelyek szükségesek. Az AKS-fürt podok forgalom szűrése hálózati biztonsági csoport szabályokat manuálisan ne konfigurálja. A Kubernetes szolgáltatásjegyzékek részeként minden szükséges portok és a továbbítás határozzák meg, és lehetővé teszik az Azure platform létrehozni vagy frissíteni a megfelelő szabályokat.

Az alapértelmezett szabályok a forgalmat az SSH például tartoznak hálózati biztonsági csoport. Fürt kezelése és hozzáférés hibaelhárítása vonatkoznak az alapértelmezett szabályokat. Az alapértelmezett szabályokat törlése az AKS-kezelési problémákhoz vezethet, és működésképtelenné válik a szolgáltatási szint célkitűzés (SLO).

## <a name="next-steps"></a>További lépések

AKS-hálózatkezelés – első lépések, olvassa el [létrehozása és a egy AKS-fürt speciális hálózatkezelés konfigurálása][aks-configure-advanced-networking].

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
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md