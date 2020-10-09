---
title: Fogalmak – hálózati diagram az Azure Red Hat-hoz a OpenShift 4
description: Hálózatkezelési diagram és az Azure Red Hat OpenShift hálózatkezelésének áttekintése
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: fb81405e85d6e2653e0cf6c007c363493992161a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87419971"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Hálózatkezelés az Azure Red Hat-on a OpenShift 4

Ez az útmutató áttekintést nyújt a OpenShift 4 fürtökön található Azure Red Hat-beli hálózatkezelésről, valamint egy diagramról és a fontos végpontok listájáról.

Az alapvető OpenShift hálózati fogalmakkal kapcsolatos további részletekért tekintse meg az [Azure Red Hat OpenShift 4 hálózatkezelési dokumentációját](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Hálózati fogalmak az Azure Red Hat OpenShift

![Azure Red Hat OpenShift 4 hálózati diagram](./media/concepts-networking/aro4-networking-diagram.png)

Ha a OpenShift 4 rendszeren telepíti az Azure Red Hat-ot, a teljes fürt egy virtuális hálózaton belül található. Ezen a virtuális hálózaton belül a főcsomópontok és a munkavégzők csomópontjai a saját alhálózatában élnek. Mindegyik alhálózat nyilvános és belső terheléselosztó használatával működik.

## <a name="explanation-of-endpoints"></a>A végpontok magyarázata

Az alábbi lista az Azure Red Hat OpenShift-fürtök fontos végpontait ismerteti.

* **ARO-pls**
    * Ez egy Azure Private link-végpont, amelyet a Microsoft és a Red Hat site megbízhatósági mérnökök használnak a fürt kezeléséhez.
* **ARO – belső-LB**
    * Ez a végpont kiegyensúlyozza az API-kiszolgáló felé irányuló forgalmat. A terheléselosztó esetében a fő csomópontok a háttér-készletben találhatók.
* **ARO – nyilvános – LB**
    * Ha az API nyilvános, a végpont átirányítja és kiegyensúlyozza az API-kiszolgáló felé irányuló forgalmat. Ez a végpont egy nyilvános kimenő IP-címet rendel hozzá, így a Masters hozzáférhet Azure Resource Managerhoz, és visszajelentheti a fürt állapotát.
* **ARO – belső**
    * Ez a végpont kiegyensúlyozza a belső szolgáltatási forgalmat. A terheléselosztó esetében a feldolgozó csomópontok a háttér-készletben találhatók.
    * A terheléselosztó alapértelmezés szerint nem jön létre. Ez a terheléselosztó akkor jön létre, ha a terheléselosztó típusú szolgáltatást a megfelelő megjegyzésekkel hozza létre. Például: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Hálózati házirendek (bejövő forgalom)**
    * A OpenShift SDN részeként támogatott
    * Alapértelmezés szerint engedélyezve van, az ügyfelek által végzett kényszerítés
    * A v1 NetworkPolicy megfelelő, azonban a "kimenő és IPBlock" típusok még nem támogatottak
    * **ARO**
    * Ez a végpont kiegyensúlyozza az API-kiszolgáló felé irányuló forgalmat. A terheléselosztó esetében a fő csomópontok a háttér-készletben találhatók.
  * **ARO – belső-LB**
    * Ez a végpont bármilyen nyilvános forgalomhoz használatos. A létrehozás és az alkalmazás és az útvonal esetében ez a bejövő forgalom elérési útja.
    * Ez a terheléselosztó a kifelé irányuló internetkapcsolatot is magában foglalja a munkavégző csomópontokon futó összes Pod Azure Load Balancer kimenő szabályokon keresztül.
        * Jelenleg a kimenő szabályok nem konfigurálhatók. 1 024 TCP-portot foglalnak le az egyes csomópontokhoz.
        * A új választható disableoutboundsnat nincs beállítva az LB-szabályokban, így a hüvelyek az ebben az ALB-ben konfigurált nyilvános IP-címekként is beszerezhetők.
        * A két előző pont következményeként az ideiglenes SNAT-portok hozzáadásának egyetlen módja, ha nyilvános terheléselosztó-szolgáltatásokat ad hozzá az ARO-hoz.
* **Hálózati házirendek (kimenő forgalom)**
    * A kimenő forgalomra vonatkozó szabályzatokat a OpenShift-ben elérhető kimenő tűzfal funkció segítségével lehet támogatni.
    * Csak egy névtér/projekt esetében.
    * A kimenő forgalomra vonatkozó szabályzatok nem támogatottak az "alapértelmezett" névtérben.
    * A kimenő forgalomra vonatkozó szabályok kiértékelése sorrendben történik (a legelsőtől az utolsóig).
    * **ARO-kimenő – pip**
        * Ez a végpont a munkavégző csomópontok nyilvános IP-címeként (PIP) szolgál.
        * Ez a végpont lehetővé teszi, hogy a szolgáltatások hozzáadhatnak egy Azure Red Hat OpenShift-fürtből származó adott IP-címet egy engedélyezési listához.
* **ARO-Node-NSG**
    * Ha egy szolgáltatást tesz elérhetővé, az API létrehoz egy szabályt ebben a hálózati biztonsági csoportban, így a forgalom átáramlik és eléri a csomópontokat.
    * Alapértelmezés szerint ez a hálózati biztonsági csoport engedélyezi az összes kimenő forgalmat. A kimenő forgalom jelenleg csak az Azure Red Hat OpenShift-vezérlési síkra korlátozódik.
* **ARO-controlplane-NSG**
    * Ez a végpont csak a főcsomópontok 6443-as portján keresztül engedélyezi a forgalmat.
* **Azure Container Registry**
    * Ez egy tároló-beállításjegyzék, amelyet a Microsoft belsőleg biztosít és használ.
        * Ez a beállításjegyzék a gazdagép-platform lemezképeit és a fürt összetevőit tartalmazza. Például a tárolók figyelése vagy naplózása.
        * Nem az Azure Red Hat OpenShift ügyfelei számára készült.  
        * Csak olvasható.
        * A beállításjegyzék kapcsolatai a szolgáltatási végponton keresztül történnek (az Azure-szolgáltatások közötti belső kapcsolat).
        * Ez a belső beállításjegyzék alapértelmezés szerint nem érhető el a fürtön kívül.
* **Privát kapcsolat**
    * Engedélyezi a hálózati kapcsolatot a felügyeleti síkon a fürtszolgáltatás fürtbe.
    * A Microsoft és a Red Hat site megbízhatósági mérnökök segítenek a fürt felügyeletében.

## <a name="networking-basics-in-openshift"></a>Hálózatkezelés alapjai a OpenShift-ben

A OpenShift szoftver által definiált hálózatkezelés (SDN) az vSwitch (OVS), a Container Network Interface (CNI) specifikáción alapuló OpenFlow implementáció használatával konfigurálja az átfedési hálózatot. Az SDN különböző beépülő modulokat támogat, a hálózati házirend pedig az Azure Red Hat-on a OpenShift 4 platformon használt beépülő modul. Az SDN felügyeli az összes hálózati kommunikációt, így a virtuális hálózatokon nincs szükség további útvonalakra a pod-alapú kommunikáció eléréséhez.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Azure Red Hat OpenShift hálózatkezelési sajátosságai

Az alábbi szolgáltatások az Azure Red Hat OpenShift vonatkoznak:
* A saját virtuális hálózat használata támogatott.
* A pod és a Service Network CIDRs konfigurálható.
* A csomópontok és a főkiszolgálók eltérő alhálózatokban találhatók.
* A csomópontoknak és a főkiszolgálók virtuális hálózati alhálózatának minimális/27-nek kell lennie.
* A pod CIDR minimális/18 méretűnek kell lennie (a pod hálózat nem irányítható IP-címek, és csak a OpenShift SDN-ben használatos).
* Az egyes csomópontok kiosztása/23 alhálózat (512 IP-cím) a hüvelye számára. Ez az érték nem módosítható.
* Nem csatlakoztathat Pod-t több hálózathoz.
* Nem konfigurálhatja a kimenő forgalomra vonatkozó statikus IP-címet. (Ez egy OpenShift funkció. További információ: a [kimenő IP](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html)-címek konfigurálása.

## <a name="network-settings"></a>Hálózati beállítások

A következő hálózati beállítások érhetők el az Azure Red Hat OpenShift 4-ben:

* **API láthatósága** – az az [ARO Create parancs](tutorial-create-cluster.md#create-the-cluster)futtatásakor az API láthatóságát állíthatja be.
    * "Nyilvános" – az API-kiszolgáló külső hálózatok számára érhető el.
    * "Private" – az API-kiszolgáló egy magánhálózati IP-címet rendelt a főhálózatból, csak csatlakoztatott hálózatokon (a fürt más alhálózatai között) érhető el. A rendszer létrehoz egy privát DNS-zónát az ügyfél nevében.
* **Behatolás láthatósága** – az az [ARO Create parancs](tutorial-create-cluster.md#create-the-cluster)futtatásakor az API láthatóságát állíthatja be.
    * A "nyilvános" útvonalak alapértelmezett értéke a nyilvános Azure standard Load Balancer (ez módosítható).
    * A "privát" útvonalak alapértelmezett értéke a belső terheléselosztó (ez módosítható).

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
A hálózati biztonsági csoportok a csomópont erőforráscsoport-csoportjában lesznek létrehozva, amely zárolva van. A hálózati biztonsági csoportok közvetlenül az alhálózatokhoz vannak rendelve, nem a csomópont hálózati adapterén. A hálózati biztonsági csoportok nem változtathatók meg, ami azt jelenti, hogy nincs engedélye a módosítására. 

Nyilvánosan látható API-kiszolgálóval azonban nem hozhat létre hálózati biztonsági csoportokat, és hozzárendelheti azokat a hálózati adapterekhez.

## <a name="domain-forwarding"></a>Tartomány továbbítása
Az Azure Red Hat OpenShift az CoreDNS-t használja. A tartomány továbbítása konfigurálható (további részletekért tekintse meg a [DNS-továbbítás használatával](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) kapcsolatos dokumentációt).

Jelenleg nem hozhatja a saját DNS-t a virtuális hálózatokra.


A kimenő forgalomról és arról, hogy az Azure Red Hat OpenShift milyen támogatást nyújt a kilépéshez, tekintse meg a [támogatási szabályzatok](support-policies-v4.md) dokumentációját.
