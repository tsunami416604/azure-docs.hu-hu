---
title: Az Azure Kubernetes-szolgáltatás (AKS) támogatási szabályzatai
description: Ismerje meg az Azure Kubernetes-szolgáltatás (AKS) támogatási szabályzatait, a megosztott felelősséget és az előzetes verzióban (vagy alfa vagy béta verzióban lévő) funkciókat.
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593580"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Az Azure Kubernetes-szolgáltatás támogatási szabályzatai

Ez a cikk az Azure Kubernetes-szolgáltatás (AKS) technikai támogatási szabályzatait és korlátait ismerteti. A cikk részletezi a munkavégző csomópontok kezelését, a felügyelt vezérlősík-összetevőket, a külső gyártótól származó nyílt forráskódú összetevőket, valamint a biztonsági vagy javításkezelést.

## <a name="service-updates-and-releases"></a>Szolgáltatásfrissítések és -kiadások

* A kiadási információkról az [AKS kibocsátási megjegyzések című témakörben talál.](https://github.com/Azure/AKS/releases)
* Az előzetes verzióban elérhető funkciókról az [AKS előzetes verziófunkcióiés a kapcsolódó projektek](https://github.com/Azure/AKS/blob/master/previews.md)című témakörben talál további információt.

## <a name="managed-features-in-aks"></a>Felügyelt szolgáltatások az AKS-ben

Az alapinfrastruktúra szolgáltatásként (IaaS) felhőösszetevői, például a számítási vagy hálózati összetevők hozzáférést biztosítanak a felhasználóknak az alacsony szintű vezérlőkhöz és testreszabási lehetőségekhez. Ezzel szemben az AKS egy kulcsrakész Kubernetes-telepítést biztosít, amely biztosítja az ügyfelek számára a szükséges konfigurációk és képességek közös készletét. Az AKS-ügyfelek korlátozott testreszabási, telepítési és egyéb lehetőségekkel rendelkeznek. Ezeknek az ügyfeleknek nem kell aggódniuk vagy közvetlenül kezelnie a Kubernetes-fürtöket.

Az AKS-sel az ügyfél egy teljes körűen felügyelt *vezérlősíkot*kap. A vezérlősík tartalmazza az ügyfél működéséhez szükséges összes összetevőt és szolgáltatást, és kubernetes-fürtöket biztosít a végfelhasználóknak. Minden Kubernetes-összetevőt a Microsoft karbantart és üzemeltet.

A Microsoft a következő összetevőket kezeli és figyeli a vezérlőpanelen keresztül:

* Kubelet vagy Kubernetes API-kiszolgálók
* Etcd vagy kompatibilis kulcsérték-tároló, amely biztosítja a szolgáltatás minőségét (QoS), méretezhetőséget és futásidejű
* DNS-szolgáltatások (például kube-dns vagy CoreDNS)
* Kubernetes-proxy vagy hálózatkezelés

Az AKS nem teljesen felügyelt fürtmegoldás. Egyes összetevők, például a munkavégző csomópontok *megosztott felelősséggel*tartoznak, ahol a felhasználóknak segíteniük kell az AKS-fürt karbantartásában. Felhasználói bevitel szükséges például a munkavégző csomópont operációs rendszer (OS) biztonsági javításának alkalmazásához.

A szolgáltatások *kezelése* abban az értelemben történik, hogy a Microsoft és az AKS csapata telepíti, működteti és felelős a szolgáltatások rendelkezésre állásáért és működéséért. Az ügyfelek nem módosíthatják ezeket a felügyelt összetevőket. A Microsoft korlátozza a testreszabást, hogy egységes és méretezhető felhasználói élményt biztosítson. A teljesen testreszabható megoldást lásd: [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Az AKS-feldolgozócsomópontok az Azure Portalon rendszeres Azure IaaS-erőforrásokként jelennek meg. Ezek a virtuális gépek azonban egy egyéni Azure-erőforráscsoportba vannak telepítve (mc\\*előtaggal). Lehetőség van az AKS munkavégző csomópontok módosítására. A Secure Shell (SSH) segítségével például módosíthatja az AKS-munkavégző csomópontokat a normál virtuális gépek módosításának módjában (azonban nem módosíthatja az operációs rendszer alaplemezképét, és előfordulhat, hogy a módosítások nem maradnak meg frissítés vagy újraindítás esetén), és más Azure-erőforrásokat is csatolhat az AKS-hez. dolgozó csomópontok. De ha a *sávkezelés és a testreszabás* módosításai miatt módosítja az AKS-fürt nem támogatható. Kerülje a munkavégző csomópontok módosítását, kivéve, ha a Microsoft támogatási szolgálata módosításokat utasít.

A fentiekben meghatározott nem támogatott műveletek, például az összes ügynökcsomópont sávon kívüli feloldása miatt a fürt nem támogatott. Az AKS fenntartja a jogot, hogy archiválja a 30 naposra egyenlő és azazt követő hosszabb ideig a támogatási irányelvek alapján konfigurált vezérlőrepülőgépeket. Az AKS biztonsági másolatot készít a fürt etcd metaadatairól, és könnyen újrakioszthatja a fürtöt. Ezt az újraelosztást bármely PUT-művelet kezdeményezheti, amely a fürtöt visszahelyezi a támogatásba, például egy frissítést vagy az aktív ügynök csomópontokra való skálázást.

## <a name="shared-responsibility"></a>Megosztott feladatkörök

Fürt létrehozásakor az ügyfél határozza meg a Kubernetes munkavégző csomópontokat, amelyeket az AKS létrehoz. Az ügyfél-munkaterhelések ezeken a csomópontokon kerülnek végrehajtásra. Az ügyfelek birtokolják és megtekinthetik vagy módosíthatják a munkavégző csomópontokat.

Mivel az ügyfélfürt-csomópontok privát kódot hajtanak végre, és bizalmas adatokat tárolnak, a Microsoft támogatási szolgálata csak korlátozott módon férhet hozzá. A Microsoft támogatási szolgálata nem tud bejelentkezni, parancsokat végrehajtani, illetve nem tekintheti meg a csomópontok naplóit kifejezett ügyfélengedély vagy segítség nélkül.

Mivel a munkavégző csomópontok érzékenyek, a Microsoft nagy gondot szentel a háttérkezelés korlátozásának. Sok esetben a számítási feladatok továbbra is futni fog, még akkor is, ha a Kubernetes fő csomópontok, etcd, és más Microsoft által felügyelt összetevők sikertelen. A gondatlanul módosított munkavégző csomópontok adat- és munkaterhelés-veszteségeket okozhatnak, és a fürtet elérhetetlenné tehetik.

## <a name="aks-support-coverage"></a>Az AKS támogatási lefedettsége

A Microsoft technikai támogatást nyújt a következőkhez:

* A Kubernetes szolgáltatás által biztosított és támogatott összes Kubernetes-összetevővel való kapcsolat, például az API-kiszolgáló.
* A Kubernetes felügyeleti, üzemidő-, QoS- és üzemeltetése (például a Kubernetes főcsomópontjai, az API-kiszolgáló etcd és a kube-dns).
* Etcd. A támogatás magában foglalja az összes etcd-adat automatikus, átlátható biztonsági mentését 30 percenként a katasztrófatervezéshez és a fürtállapot helyreállításához. Ezek a biztonsági mentések közvetlenül nem érhetők el az ügyfelek vagy a felhasználók számára. Biztosítják az adatok megbízhatóságát és konzisztenciáját.
* A Kubernetes Azure-beli felhőszolgáltató-illesztőprogramjában található integrációs pontok. Ezek közé tartoznak a más Azure-szolgáltatásokba, például a terheléselosztókba, az állandó kötetekbe vagy a hálózatba (Kubernetes és az Azure CNI) való integrációk.
* Kérdések vagy problémák a vezérlősík-összetevők testreszabásával kapcsolatban, például a Kubernetes API-kiszolgáló, etcd és kube-dns.
* A hálózatépítéssel kapcsolatos problémák, például az Azure CNI, a kubenet vagy más hálózati hozzáféréssel és funkciókkal kapcsolatos problémák. A problémák közé tartozhat a DNS-feloldás, a csomagvesztés, az útválasztás és így tovább. A Microsoft különböző hálózati forgatókönyveket támogat:
  * Kubenet (alapszintű) és fejlett hálózatkezelés (Azure CNI) a fürtön és a kapcsolódó összetevőkön belül
  * Kapcsolódás más Azure-szolgáltatásokhoz és -alkalmazásokhoz
  * Be- és be- és terheléselosztási konfigurációk
  * Hálózati teljesítmény és késés

A Microsoft nem nyújt technikai támogatást a következőkhez:

* Kérdések a Kubernetes használatával kapcsolatban. A Microsoft támogatási szolgálata például nem ad tanácsot egyéni be- ésresvezérlők létrehozásához, alkalmazás-munkaterhelések használatához, illetve külső vagy nyílt forráskódú szoftvercsomagok vagy -eszközök alkalmazásához.
  > [!NOTE]
  > A Microsoft támogatási szolgálata tanácsot adhat az AKS-fürtfunkciókkal, testreszabással és hangolással kapcsolatban (például a Kubernetes-műveletekkel és -eljárásokkal kapcsolatban).
* Külső nyílt forráskódú projektek, amelyek nem részeként a Kubernetes vezérlősík vagy telepített AKS-fürtök. Ezek a projektek lehetnek Istio, Helm, követ, vagy mások.
  > [!NOTE]
  > A Microsoft a legjobb támogatást nyújt a külső nyílt forráskódú projektekhez, például a Helm hez és a Kuredhoz. Amennyiben a külső gyártó nyílt forráskódú eszköze integrálható a Kubernetes Azure felhőszolgáltatóval vagy más AKS-specifikus hibákkal, a Microsoft támogatja a Microsoft dokumentációjából származó példákat és alkalmazásokat.
* Harmadik féltől származó, zárt forráskódú szoftver. Ez a szoftver magában foglalhatja a biztonsági szkenneléseszközök és hálózati eszközök vagy szoftverek.
* Problémák a többfelhős vagy többszállítós kiépítési műveletekkel kapcsolatban. A Microsoft például nem támogatja az összevont többnyilvános felhőalapú szállítói megoldás futtatásával kapcsolatos problémákat.
* Az [AKS dokumentációjában](https://docs.microsoft.com/azure/aks/)felsoroltaktól eltérő hálózati testreszabások.
  > [!NOTE]
  > A Microsoft támogatja a hálózati biztonsági csoportokkal kapcsolatos problémákat és hibákat. A Microsoft támogatási szolgálata például válaszolhat az NSG frissítésének sikertelenségével, illetve a váratlan NSG- vagy terheléselosztó viselkedéssel kapcsolatos kérdésekre.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS-támogatási lefedettség a dolgozói csomópontokszámára

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>A Microsoft felelőssége az AKS munkavégző csomópontjaival

A Microsoft és az ügyfelek osztoznak a Kubernetes munkavégző csomópontokért, ahol:

* Az alap operációsrendszer-rendszerkép szükséges kiegészítéseket (például a figyelési és hálózati ügynökök).
* A munkavégző csomópontok automatikusan megkapják az operációs rendszer javításait.
* A munkavégző csomópontokon futó Kubernetes vezérlősík-összetevőkkel kapcsolatos problémák automatikusan kivannak állítva. Az összetevők a következők:
  * Kube-proxy
  * Hálózati alagutak, amelyek kommunikációs útvonalakat biztosítanak a Kubernetes fő összetevőihez
  * Kubelet
  * Docker vagy Moby démon

> [!NOTE]
> Egy munkavégző csomóponton, ha egy vezérlősík-összetevő nem működik, az AKS-csapat nak újra kell indítania az egyes összetevőket vagy a teljes munkavégző csomópontot. Ezek az újraindítási műveletek automatikusak, és automatikus szervizelést biztosítanak a gyakori problémák esetén. Ezek az újraindítások csak a _csomópont_ szintjén fordulnak elő, és nem a fürt, kivéve, ha vészhelyzeti karbantartás vagy kimaradás van.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Az AKS munkavégző csomópontjainak ügyfélfeladatai

A Microsoft nem indítja újra automatikusan a munkavégző csomópontokat az operációs rendszer szintű javítások alkalmazásához. Bár az operációs rendszer javításai a feldolgozócsomópontokhoz kerülnek, az *ügyfél* felelős a munkavégző csomópontok újraindításáért a módosítások alkalmazásához. A megosztott könyvtárak, démonok, például az SSHD (SSHD) és a rendszer vagy az operációs rendszer szintjén lévő egyéb összetevők automatikusan javításra kerülnek.

Az ügyfelek felelősek a Kubernetes-frissítések végrehajtásáért. Az Azure vezérlőpultján vagy az Azure CLI-n keresztül hajthatnak végre frissítéseket. Ez a Kubernetes biztonsági vagy funkciófejlesztéseit tartalmazó frissítésekre vonatkozik.

> [!NOTE]
> Mivel az AKS *felügyelt szolgáltatás,* a végső célok közé tartozik a javításokért, a frissítésekért és a naplógyűjtésért való felelősség eltávolítása a szolgáltatáskezelés teljesebb és gyakorlati eltávolítása érdekében. A szolgáltatás végpontok közötti felügyeleti kapacitásának növekedésével a jövőbeli kiadások kihagyhatnak bizonyos funkciókat (például a csomópontok újraindítását és az automatikus javítást).

### <a name="security-issues-and-patching"></a>Biztonsági problémák és javítás

Ha az AKS egy vagy több összetevőjében biztonsági hibát talál, az AKS-csapat az összes érintett fürtöt kijavítja a probléma enyhítése érdekében. Másik lehetőségként a csapat frissítési útmutatást ad a felhasználóknak.

Azon munkavégző csomópontok esetében, amelyeket egy biztonsági hiba érint, ha elérhető egy nulla állásidő-javítás, az AKS-csapat alkalmazza a javítást, és értesíti a felhasználókat a változásról.

Ha egy biztonsági javítás hoz szüksége a feldolgozói csomópont újraindításához, a Microsoft értesíti az ügyfeleket erről a követelményről. Az ügyfél felelős az újraindításért vagy a frissítésért a fürtjavítás lekérnie. Ha a felhasználók nem alkalmazzák a javításokat az AKS útmutatása szerint, a fürtjük továbbra is ki lesz téve a biztonsági problémának.

### <a name="node-maintenance-and-access"></a>Csomópont karbantartása és hozzáférése

A dolgozói csomópontok megosztott felelősséggel tartoznak, és az ügyfelek tulajdonában vannak. Emiatt az ügyfelek bejelentkezhetnek a dolgozói csomópontjaikba, és potenciálisan káros módosításokat hajthatnak végre, például kernelfrissítéseket, valamint csomagokat telepíthetnek vagy távolodhatnak el.

Ha az ügyfelek roncsolást hajtanak végre, vagy a vezérlősík-összetevők offline állapotba kerülése vagy működésképtelenné válása miatt válnak működésképtelenné, az AKS észleli ezt a hibát, és automatikusan visszaállítja a munkavégző csomópontot az előző működő állapotba.

Bár az ügyfelek bejelentkezhetnek és módosíthatják a munkavégző csomópontokat, ez nem ajánlott, mert a módosítások a fürtet nem támogathatók.

## <a name="network-ports-access-and-nsgs"></a>Hálózati portok, hozzáférések és NSG-k

Felügyelt szolgáltatásként az AKS speciális hálózati és kapcsolódási követelményekkel rendelkezik. Ezek a követelmények kevésbé rugalmasak, mint a normál IaaS-összetevőkre vonatkozó követelmények. Az AKS-ben az olyan műveletek, mint az NSG-szabályok testreszabása, egy adott port blokkolása (például a 443-as kimenő portot tiltó tűzfalszabályok használata), valamint az engedélyezési URL-címek elérhetetlenná tehetik a fürtöt.

> [!NOTE]
> Jelenleg az AKS nem teszi lehetővé a fürtből való kimenő forgalom teljes zárolását. A fürt által a kimenő forgalomhoz használható URL-címek és portok listájának szabályozásához [lásd: kimenő forgalom korlátozása.](limit-egress-traffic.md)

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nem támogatott alfa- és béta-Kubernetes-funkciók

Az AKS csak stabil funkciókat támogat a upstream Kubernetes projekten belül. Eltérő rendelkezés hiányában az AKS nem támogatja az alfa- és bétafunkciókat, amelyek a felsőbb szintben lévő Kubernetes-projektben érhetők el.

Két esetben előfordulhat, hogy az alfa- vagy bétafunkciók már az általános analfabetika előtt elérhetővé válikk:

* Az ügyfelek találkoztak az AKS termék-, támogatási vagy mérnöki csapataival, és felkérték őket, hogy próbálják ki ezeket az új funkciókat.
* Ezeket a [funkciókat egy szolgáltatásjelző engedélyezte.](https://github.com/Azure/AKS/blob/master/previews.md) Az ügyfeleknek kifejezetten engedélyezniük kell a funkciók használatát.

## <a name="preview-features-or-feature-flags"></a>Funkciók vagy szolgáltatásjelzők előnézete

A kiterjesztett tesztelést és felhasználói visszajelzést igénylő funkciók és funkciók esetében a Microsoft új előzetes verziójú funkciókat vagy funkciókat ad ki a szolgáltatásjelző mögött. Tekintsük ezeket a funkciókat előzetes vagy bétaverziós funkcióknak.

Az előzetes verziójú funkciók vagy a funkciójelző-funkciók nem éles környezetben való használatra valók. Az API-k és a viselkedés, a hibajavítások és egyéb módosítások folyamatos változásai instabil fürtöket és állásidőt eredményezhetnek.

A nyilvános előzetes verzióban található funkciók a "legjobb erőfeszítés" támogatás alá tartoznak, mivel ezek a funkciók előzetes verzióban vannak, és nem éles környezetben készülnek, és az AKS technikai támogató csapatai csak munkaidőben támogatják őket. További információkért lásd:

* [Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Az előzetes verziójú funkciók az *Azure-előfizetés* szintjén lépnek érvénybe. Ne telepítse az előzetes verziójú funkciókat éles előfizetésre. Éles előfizetés esetén az előzetes verziójú funkciók módosíthatják az API alapértelmezett viselkedését, és befolyásolhatják a rendszeres műveleteket.

## <a name="upstream-bugs-and-issues"></a>Upstream hibák és problémák

Tekintettel a fejlődés sebességére az upstream Kubernetes projektben, a hibák mindig felmerülnek. Néhány ilyen hiba nem javítható vagy dolgozható az AKS rendszeren belül. Ehelyett a hibajavítások nagyobb javításokat igényelnek a felsőstream projektekhez (például a Kubernetes, a csomópont vagy a feldolgozó operációs rendszerek és a kernelek). A Microsoft tulajdonában lévő összetevők (például az Azure felhőszolgáltató) esetében az AKS és az Azure munkatársai elkötelezettek a közösségben a problémák megoldása iránt.

Ha egy technikai támogatási problémát egy vagy több upstream hiba okoz gyökérokozta, az AKS támogatási és mérnöki csapatai:

* Azonosítsa és kapcsolja össze a felső kategóriás hibákat a támogató részletekkel, hogy segítsen megmagyarázni, miért befolyásolja ez a probléma a fürtöt vagy a munkaterhelést. Az ügyfelek a szükséges adattárakra mutató hivatkozásokat kapnak, hogy megnézhessék a problémákat, és megnézhessék, hogy egy új kiadás mikor biztosítja a javításokat.
* Lehetséges megoldások at vagy megoldásokat biztosít. Ha a probléma mérsékelhető, egy [ismert probléma](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) lesz iktatva az AKS-tárházban. Az ismert kérdés bejelentés magyarázza:
  * A probléma, beleértve a linkeket upstream hibákat.
  * A megoldás és a megoldás frissítésének vagy más adatmegőrzési részleteinek részletei.
  * A probléma felvételének durva idővonalai, a felsőbb értékesítési ütem alapján.
