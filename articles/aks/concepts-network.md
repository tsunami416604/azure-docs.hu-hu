---
title: Fogalmak – Hálózatkezelés az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg az Azure Kubernetes-szolgáltatás (AKS) hálózatba szervezését, beleértve a kubenet és az Azure CNI-hálózatépítést, a bejövő forgalom vezérlőket, a terheléselosztókat és a statikus IP-címeket.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253935"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes-szolgáltatás (AKS) alkalmazásainak hálózati fogalmai

Az alkalmazásfejlesztés tárolóalapú mikroszolgáltatások megközelítésében az alkalmazás-összetevőknek együtt kell működniük a feladataik feldolgozásához. A Kubernetes különböző erőforrásokat biztosít, amelyek lehetővé teszik ezt az alkalmazáskommunikációt. Az alkalmazásokhoz belsőleg vagy külsőleg is csatlakozhat és elérhetővé tehet. Magas rendelkezésre állású alkalmazások létrehozásához betöltheti az alkalmazások egyensúlyát. Összetettebb alkalmazások szükség lehet a kimenő forgalom konfigurálása az SSL/TLS-végződés vagy több összetevő útválasztása. Biztonsági okokból szükség lehet a hálózati forgalom podok és csomópontok közötti áramlását is.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek az AKS-ben lévő alkalmazások hoznak hálózati hálózatot:

- [Szolgáltatások](#services)
- [Az Azure virtuális hálózatai](#azure-virtual-networks)
- [Be- és be- és visszatámadások vezérlői](#ingress-controllers)
- [Hálózati házirendek](#network-policies)

## <a name="kubernetes-basics"></a>A Kubernetes alapjai

Az alkalmazásokhoz való hozzáférés, illetve az alkalmazás-összetevők egymással való kommunikációjának engedélyezéséhez a Kubernetes egy absztrakciós réteget biztosít a virtuális hálózathoz. Kubernetes csomópontok egy virtuális hálózathoz csatlakozik, és a podok bejövő és kimenő kapcsolatot biztosíthatnak. A *kube-proxy* összetevő minden csomóponton fut, hogy biztosítsa ezeket a hálózati szolgáltatásokat.

A Kubernetes szolgáltatásban a *szolgáltatások* logikailag csoportosítják a podokat, hogy lehetővé tegyék a közvetlen hozzáférést egy IP-címen vagy DNS-névn keresztül és egy adott porton. A forgalmat *terheléselosztó*val is eloszthatja. Az alkalmazásforgalom összetettebb útválasztása a *be- és afeledt vezérlőkkel*is elérhető. A kubernetesi *hálózati házirendekkel*a podok hálózati forgalmának biztonsága és szűrése lehetséges.

Az Azure platform is segít egyszerűsíteni a virtuális hálózatépítés az AKS-fürtök. Kubernetes terheléselosztó létrehozásakor létrejön és konfigurálja az alapul szolgáló Azure-terheléselosztó erőforrást. Amikor megnyitja a hálózati portok podok, a megfelelő Azure hálózati biztonsági csoport szabályok vannak konfigurálva. HTTP-alkalmazás-útválasztás esetén az Azure is konfigurálhatja *a külső DNS-t,* mivel az új bejövő útvonalak vannak konfigurálva.

## <a name="services"></a>Szolgáltatások

Az alkalmazás-munkaterhelések hálózati konfigurációjának egyszerűsítése érdekében a Kubernetes *a Szolgáltatások* segítségével logikailag csoportosítja a podok készletét, és hálózati kapcsolatot biztosít. A következő szolgáltatástípusok érhetők el:

- **Fürt IP-címe** – Belső IP-címet hoz létre az AKS-fürtön való használatra. Csak belső alkalmazásokhoz használható, amelyek a fürtön belül más számítási feladatokat is támogatnak.

    ![Fürt IP-forgalmát bemutató diagram AKS-fürtben][aks-clusterip]

- **NodePort** – Port-leképezést hoz létre az alapul szolgáló csomóponton, amely lehetővé teszi, hogy az alkalmazás közvetlenül elérhető legyen a csomópont IP-címével és portjával.

    ![NodePort-forgalmat bemutató diagram AKS-fürtben][aks-nodeport]

- **LoadBalancer** – létrehoz egy Azure-terheléselosztó erőforrást, konfigurál egy külső IP-címet, és csatlakoztatja a kért podokat a terheléselosztó háttérkészlethez. Annak érdekében, hogy az ügyfelek forgalma elérje az alkalmazást, terheléselosztási szabályok jönnek létre a kívánt portokon. 

    ![AKS-fürt terheléselosztó forgalmát bemutató diagram][aks-loadbalancer]

    A bejövő forgalom további vezérléséhez és útválasztásához ehelyett használhat bejövő [forgalom vezérlőt.](#ingress-controllers)

- **ExternalName** - Egy adott DNS-bejegyzést hoz létre az alkalmazások könnyebb elérése érdekében.

A terheléselosztók és szolgáltatások IP-címe dinamikusan hozzárendelhető, vagy megadhat egy meglévő statikus IP-címet. Belső és külső statikus IP-címek is hozzárendelhetők. Ez a meglévő statikus IP-cím gyakran DNS-bejegyzéshez van kötve.

*Belső* és *külső* terheléselosztók is létrehozhatók. A belső terheléselosztók hoznak hozzá egy privát IP-címet, így nem érhetők el az internetről.

## <a name="azure-virtual-networks"></a>Azure-beli virtuális hálózatok

Az AKS-ben üzembe helyezhető fürt a következő két hálózati modell egyikét használja:

- *Kubenet-hálózatkezelés* – A hálózati erőforrások általában jönnek létre, és konfigurálva az AKS-fürt telepítése.
- *Azure Container Networking Interface (CNI)* hálózati – Az AKS-fürt csatlakozik a meglévő virtuális hálózati erőforrások és konfigurációk.

### <a name="kubenet-basic-networking"></a>Kubenet (alap) hálózatépítés

A *kubenet* hálózati beállítás az AKS-fürt létrehozásának alapértelmezett konfigurációja. A *kubenet*használatával a csomópontok egy IP-címet kapnak az Azure virtuális hálózati alhálózatból. A podok IP-címe a csomópontok Azure-beli virtuális hálózati alhálózatától logikailag eltérő címtérből származik. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. A forgalom forrás IP-címe NAT'd a csomópont elsődleges IP-címéhez.

Csomópontok használja a [kubenet][kubenet] Kubernetes plugin. Lehetővé teheti, hogy az Azure platform hozza létre és konfigurálja a virtuális hálózatokat, vagy dönthet úgy, hogy az AKS-fürtöt egy meglévő virtuális hálózati alhálózatba telepíti. Ismét csak a csomópontok kap egy irányítható IP-címet, és a podok nat-t használnak az AKS-fürtön kívüli más erőforrásokkal való kommunikációhoz. Ez a megközelítés jelentősen csökkenti az IP-címek számát, amelyeket le kell foglalnia a hálózati térben a podok számára.

További információ: [Kubenet networking konfigurálása AKS-fürthöz.][aks-configure-kubenet-networking]

### <a name="azure-cni-advanced-networking"></a>Azure CNI (speciális) hálózatkezelés

Az Azure CNI használatával minden pod kap egy IP-címet az alhálózatból, és közvetlenül elérhetővé válik. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre meg kell tervezni őket. Minden csomópont rendelkezik egy konfigurációs paramétera podok maximális általa támogatott. A csomópontonkénti IP-címek megfelelő száma ezután előre le van foglalva az adott csomópont számára. Ez a megközelítés több tervezést igényel, mivel egyébként az IP-cím kimerüléséhez vagy a fürtök újraépítésének szükségességéhez vezethet egy nagyobb alhálózatban, ahogy az alkalmazás igényei nőnek.

A csomópontok az [Azure Container Networking Interface (CNI)][cni-networking] Kubernetes beépülő modult használják.

![Két csomópontot ábrázoló, egyetlen Azure-alapú virtuális hálózathoz összekötő hidakkal][advanced-networking-diagram]

További információ: [Azure CNI konfigurálása AKS-fürthöz.][aks-configure-advanced-networking]

### <a name="compare-network-models"></a>Hálózati modellek összehasonlítása

A kubenet és az Azure CNI egyaránt biztosít hálózati kapcsolatot az AKS-fürtök számára. Vannak azonban előnyei és hátrányai, hogy minden. Magas szinten a következő szempontok érvényesek:

* **kubenet között**
    * Az IP-címterület megőrzése.
    * A Kubernetes belső vagy külső terheléselosztót használja a fürtön kívüli podok eléréséhez.
    * A felhasználó által definiált útvonalakat (UdRs) manuálisan kell kezelnie és karbantartania.
    * Fürtenként legfeljebb 400 csomópont.
* **Azure CNI**
    * A podok teljes virtuális hálózati kapcsolatot kapnak, és közvetlenül elérhetők a privát IP-címükön keresztül a csatlakoztatott hálózatokról.
    * Több IP-címterületet igényel.

A következő viselkedésbeli különbségek vannak a kubenet és az Azure CNI között:

| Képesség                                                                                   | Kubenet között   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Fürt központi telepítése meglévő vagy új virtuális hálózatban                                            | Támogatott - Az UdRs manuálisan alkalmazva | Támogatott |
| Pod-pod kapcsolat                                                                         | Támogatott | Támogatott |
| Pod-VM-kapcsolat; Virtuális gép ugyanabban a virtuális hálózatban                                          | Működik, ha a pod kezdeményezi | Mindkét irányban működik |
| Pod-VM-kapcsolat; Virtuális gép társviszony-létesített virtuális hálózatban                                            | Működik, ha a pod kezdeményezi | Mindkét irányban működik |
| Helyszíni hozzáférés VPN- vagy expressz útvonal használatával                                                | Működik, ha a pod kezdeményezi | Mindkét irányban működik |
| Hozzáférés a szolgáltatásvégpontok által védett erőforrásokhoz                                             | Támogatott | Támogatott |
| Kubernetes-szolgáltatások megjelenítése terheléselosztó szolgáltatás, App Gateway vagy be- éselőszolgáltatás vezérlő használatával | Támogatott | Támogatott |
| Alapértelmezett Azure DNS- és privát zónák                                                          | Támogatott | Támogatott |

Ami a DNS-t illeti, a kubenet és az Azure CNI beépülő modullal a DNS-t a CoreDNS, az AKS-ben futó démonkészlet kínálja. A Kubernetes-en található CoreDNS szolgáltatásról a [DNS-szolgáltatás testreszabása](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)című témakörben talál további információt. A CoreDNS alapértelmezés szerint úgy van konfigurálva, hogy ismeretlen tartományokat továbbítson a csomópont DNS-kiszolgálóinak, más szóval az AKS-fürt üzembe helyezését tartalmazó Azure virtuális hálózat DNS-funkciójának. Ezért az Azure DNS- és privát zónák az AKS-ben futó podok esetében is működni fognak.

### <a name="support-scope-between-network-models"></a>Támogatási hatókör a hálózati modellek között

A használt hálózati modelltől függetlenül a kubenet és az Azure CNI az alábbi módokon telepíthető:

* Az Azure platform automatikusan létrehozhatja és konfigurálhatja a virtuális hálózati erőforrásokat, amikor létrehoz egy AKS-fürtöt.
* Az AKS-fürt létrehozásakor manuálisan hozhatja létre és konfigurálhatja a virtuális hálózati erőforrásokat, és csatolhatja ezeket az erőforrásokat.

Bár a képességek, például a szolgáltatásvégpontok vagy udrs támogatottak a kubenet és az Azure CNI, az [AKS támogatási szabályzatok][support-policies] határozzák meg, milyen módosításokat végezhet. Példa:

* Ha manuálisan hozza létre a virtuális hálózati erőforrásokat egy AKS-fürthöz, a saját UdRs vagy szolgáltatásvégpontok konfigurálása kor támogatott.
* Ha az Azure platform automatikusan létrehozza a virtuális hálózati erőforrásokat az AKS-fürthöz, nem támogatott manuálisan módosítani az AKS által kezelt erőforrásokat a saját UdRs vagy szolgáltatásvégpontok konfigurálásához.

## <a name="ingress-controllers"></a>Bejövőforgalom-vezérlők

LoadBalancer típusú szolgáltatás létrehozásakor létrejön egy mögöttes Azure terheléselosztó erőforrás. A terheléselosztó úgy van konfigurálva, hogy forgalmat a podok a szolgáltatás egy adott porton. A LoadBalancer csak a 4.

*A be- ésrémvezérlők* a 7- es rétegben működnek, és intelligensebb szabályokat használhatnak az alkalmazásforgalom elosztásához. A bejövő vezérlők gyakori használata a HTTP-forgalom különböző alkalmazásokhoz való átirányítása a bejövő URL-cím alapján.

![AKS-fürt ben a forgalom áramlását bemutató diagram][aks-ingress]

Az AKS-ben létrehozhat egy bejövő erőforrást az NGINX-hez hasonló valamitva, vagy használhatja az AKS HTTP alkalmazásútválasztási szolgáltatást. Ha engedélyezi a HTTP-alkalmazás-útválasztást egy AKS-fürthöz, az Azure platform létrehozza a bejövő kapcsolat vezérlőjét és egy *külső DNS-vezérlőt.* Aktaszhálózatban új bejövő erőforrások létrehozásakor a szükséges DNS A rekordok egy fürtspecifikus DNS-zónában jönnek létre. További információt a [HTTP-alkalmazás-útválasztás központi telepítése című][aks-http-routing]témakörben talál.

A be- és akta másik gyakori jellemzője az SSL/TLS-megszüntetés. A HTTPS-en keresztül elért nagy webalkalmazások, a TLS-végződtetés a be- és écses erőforrás, nem pedig magában az alkalmazáson belül. Az automatikus TLS-hitelesítés generálása és konfigurálása érdekében konfigurálhatja a be- és écserőforrást olyan szolgáltatók használatára, mint például a Let's Encrypt. Az NGINX ingress vezérlő let's Encrypt segítségével történő konfigurálásáról a Bejövő forgalom és a [TLS][aks-ingress-tls]című témakörben talál további információt.

A bejövő kapcsolat vezérlője is konfigurálhatja, hogy megőrizze az ügyfélforrás IP-címét az AKS-fürttárolóira irányuló kérelmeken. Ha egy ügyfél kérése a bejövő kapcsolatvezérlőn keresztül egy tárolóba kerül az AKS-fürtben, a kérelem eredeti forrás IP-címe nem lesz elérhető a céltároló számára. Ha engedélyezi *az ügyfélforrás IP-megőrzését,* az ügyfél forrás IP-címe az *X-Forwarded-For*csoportban található kérelemfejlécben érhető el. Ha ügyfélforrás IP-megőrzése a be- és énekbe, nem használhatja az SSL pass-through. Az ügyfélforrás IP-megőrzése és az SSL-áthaladás más szolgáltatásokkal, például a *LoadBalancer* típussal is használható.

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoport szűri a forgalmat a virtuális gépek, például az AKS-csomópontok. A szolgáltatások, például a LoadBalancer létrehozásakor az Azure platform automatikusan konfigurálja a szükséges hálózati biztonsági csoport szabályokat. Ne konfigurálja manuálisan a hálózati biztonsági csoport szabályait az AKS-fürtben lévő podok forgalmának szűrésére. Definiálja a szükséges portokat és továbbítást a Kubernetes-szolgáltatás jegyzékfájljainak részeként, és hagyja, hogy az Azure platform hozza létre vagy frissítse a megfelelő szabályokat. A következő szakaszban tárgyalt hálózati házirendek használatával is automatikusan alkalmazhatja a forgalomszűrő szabályokat a podokra.

## <a name="network-policies"></a>Hálózati házirendek

Alapértelmezés szerint az AKS-fürt összes podja korlátozás nélkül küldhet és fogadhat forgalmat. A nagyobb biztonság érdekében érdemes lehet meghatározni a forgalom áramlását szabályozó szabályokat. Háttéralkalmazások gyakran csak a szükséges előtér-szolgáltatások, vagy az adatbázis-összetevők csak az alkalmazásrétegek, amelyek csatlakoznak hozzájuk.

A hálózati házirend egy Kubernetes szolgáltatás érhető el az AKS, amely lehetővé teszi a podok közötti forgalom szabályozását. Dönthet úgy, hogy engedélyezi vagy letagadja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, névtér vagy forgalmi port. A hálózati biztonsági csoportok inkább az AKS-csomópontokhoz tartoznak, nem a podokhoz. A hálózati házirendek használata megfelelőbb, felhőalapú módja a forgalom áramlásának szabályozásának. A podok dinamikusan jönnek létre egy AKS-fürtben, a szükséges hálózati házirendek automatikusan alkalmazható.

További információ: [Biztonságos forgalom a podok között a hálózati szabályzatok használatával az Azure Kubernetes Szolgáltatás (AKS)][use-network-policies].

## <a name="next-steps"></a>További lépések

Az AKS-hálózat használatának megkezdéséhez hozzon létre és konfiguráljon egy AKS-fürtöt saját IP-címtartományaival a [kubenet][aks-configure-kubenet-networking] vagy az [Azure CNI][aks-configure-advanced-networking]használatával.

A kapcsolódó gyakorlati tanácsokról az [AKS hálózati kapcsolatával és biztonságával kapcsolatos gyakorlati tanácsok című témakörben található.][operator-best-practices-network]

A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS-fürtök és munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes / AKS hozzáférés és identitás][aks-concepts-identity]
- [Kubernetes / AKS biztonság][aks-concepts-security]
- [Kubernetes / AKS tároló][aks-concepts-storage]
- [Kubernetes / AKS skála][aks-concepts-scale]

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
[support-policies]: support-policies.md
