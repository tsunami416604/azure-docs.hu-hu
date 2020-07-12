---
title: Támogatási szabályzatok az Azure Kubernetes Service (ak) szolgáltatáshoz
description: Ismerje meg az Azure Kubernetes Service (ak) támogatási szabályzatait, a közös felelősséget és az előzetes verzióban (vagy az Alpha-ban vagy a Beta-ban) elérhető szolgáltatásokat.
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: ce014fc338ebfad18b91ba65b614ca0a380a3f7c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243851"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Támogatási szabályzatok az Azure Kubernetes Service-hez

Ez a cikk részletesen ismerteti az Azure Kubernetes Service (ak) technikai támogatási szabályzatait és korlátozásait. A cikk a feldolgozó csomópontok felügyeletét, a felügyelt vezérlési sík összetevőit, a harmadik féltől származó nyílt forráskódú összetevőket, valamint a biztonsági vagy javítási felügyeletet is ismerteti.

## <a name="service-updates-and-releases"></a>Szolgáltatások frissítései és kiadásai

* A kiadással kapcsolatos információkért lásd az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases).
* Az előzetes verzió szolgáltatásaival kapcsolatos információkért lásd: az [AK előzetes verziójának funkciói és kapcsolódó projektjei](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Felügyelt funkciók az AK-ban

Az alap infrastruktúra szolgáltatásként (IaaS) Felhőbeli összetevői, például a számítási vagy hálózatkezelési összetevők hozzáférést biztosítanak a felhasználóknak az alacsony szintű vezérlőkhöz és a testreszabási lehetőségekhez. Ezzel szemben az AK egy kulcsrakész Kubernetes-telepítést biztosít, amely az ügyfeleknek a szükséges konfigurációk és képességek közös készletét biztosítja. Az AK-ügyfelek korlátozott testreszabási, üzembe helyezési és egyéb beállításokkal rendelkeznek. Ezeknek az ügyfeleknek nem kell aggódniuk, vagy közvetlenül a Kubernetes-fürtöket kell kezelnie.

Az AK-val az ügyfél egy teljes körűen felügyelt *vezérlési gépet*kap. A vezérlő síkja tartalmazza az ügyfél által igényelt összes összetevőt és szolgáltatást, és Kubernetes-fürtöket biztosít a végfelhasználók számára. Az összes Kubernetes-összetevőt a Microsoft tartja karban és üzemelteti.

A Microsoft a következő összetevőket felügyeli és figyeli a vezérlés ablaktáblán:

* Kubelet vagy Kubernetes API-kiszolgálók
* Etcd vagy egy kompatibilis kulcs-érték tároló, amely a szolgáltatásminőség (QoS), a méretezhetőség és a futtatókörnyezet biztosítására szolgál.
* DNS-szolgáltatások (például Kube-DNS vagy CoreDNS)
* Kubernetes proxy vagy hálózatkezelés

Az AK nem teljesen felügyelt fürt megoldás. Egyes összetevők, például a feldolgozó csomópontok *megosztott felelősséggel*rendelkeznek, ahol a felhasználóknak segíteniük kell az AK-fürt fenntartásában. Felhasználói bevitel szükséges, például a munkavégző csomópont operációs rendszer (OS) biztonsági javításának alkalmazásához.

A szolgáltatások *kezelése* abban az értelemben történik, hogy a Microsoft és az AK-csoport üzembe helyezi, működteti és felelős a szolgáltatás rendelkezésre állásának és működésének biztosításáért. Az ügyfelek nem változtathatják meg ezeket a felügyelt összetevőket. A Microsoft korlátozza a testreszabást, hogy konzisztens és skálázható felhasználói élményt biztosítson. A teljes mértékben testreszabható megoldásért lásd: [AK-motor](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Megosztott feladatkörök

Fürt létrehozásakor az ügyfél meghatározza a Kubernetes munkavégző csomópontokat, amelyeket az AK hoz létre. Az ügyfelek számítási feladatait ezeken a csomópontokon hajtja végre. A saját ügyfelei, és megtekinthetik és módosíthatják a munkavégző csomópontokat.

Mivel az ügyfél-fürtcsomópontok saját kódokat hajtanak végre, és bizalmas adatokat tárolnak, Microsoft ügyfélszolgálata csak korlátozott módon férhetnek hozzájuk. Microsoft ügyfélszolgálata nem tud bejelentkezni a alkalmazásba, végrehajthatja a parancsokat a-ben, vagy megtekintheti a csomópontok naplóit kifejezett ügyfél engedélye vagy segítsége nélkül.

Mivel a feldolgozó csomópontok érzékenyek, a Microsoft nagy gondossággal gondoskodik a háttérben történő felügyelet korlátozásáról. Sok esetben a munkaterhelés továbbra is futni fog, még akkor is, ha a Kubernetes főcsomópontok, a etcd és a Microsoft által felügyelt egyéb összetevők meghiúsulnak. A gondatlanul módosított munkavégző csomópontok adatvesztést okozhatnak az adatok és a munkaterhelések számára, és a fürt nem támogatható.

## <a name="aks-support-coverage"></a>AK-támogatás lefedettsége

A Microsoft technikai támogatást nyújt a következőkhöz:

> [!NOTE]
> A Microsoft/AK által végrehajtott összes művelet felhasználói beleegyezett a beépített Kubernetes `aks-service` -szerepkör és a beépített szerepkör-kötés alá `aks-service-rolebinding` . Ez a szerepkör lehetővé teszi az AK számára a fürtökkel kapcsolatos problémák hibaelhárítását és diagnosztizálását, de nem módosíthatja az engedélyeket, és nem hozhat létre szerepköröket vagy szerepkör-kötéseket, illetve egyéb magas jogosultsági A szerepkör-hozzáférés csak az aktív támogatási jegyek esetében engedélyezett az igény szerinti (JIT) hozzáféréssel.

* A Kubernetes szolgáltatás által biztosított és támogatott összes Kubernetes-összetevő, például az API-kiszolgáló kapcsolata.
* A Kubernetes Control Plant-szolgáltatások (Kubernetes főcsomópontok, API-kiszolgáló, etcd és Kube-DNS) felügyelete, üzemidő, QoS és műveletei.
* Etcd. A támogatás 30 percenként, a etcd és a fürt állapotának visszaállítására vonatkozó, automatizált, transzparens biztonsági mentéseket tartalmaz. Ezek a biztonsági másolatok nem érhetők el közvetlenül az ügyfelek és a felhasználók számára. Biztosítják az adatmegbízhatóságot és a konzisztenciát. Etcd. igény szerinti visszaállítás vagy helyreállítás nem támogatott szolgáltatásként.
* Bármely integrációs pont az Azure Cloud Provider-illesztőprogramban a Kubernetes-hez. Ezek közé tartoznak más Azure-szolgáltatások, például a terheléselosztó, az állandó kötetek vagy a hálózatkezelés (Kubernetes és Azure CNI) integrációja.
* A vezérlési sík összetevőinek, például a Kubernetes API-kiszolgáló, a etcd és a Kube-DNS testreszabásával kapcsolatos kérdések és problémák.
* A hálózatkezeléssel kapcsolatos problémák, például az Azure CNI, a kubenet vagy más hálózati hozzáférési és működési problémák. A problémák magukban foglalhatják a DNS-feloldást, a csomagok elvesztését, az útválasztást és így tovább. A Microsoft különböző hálózati forgatókönyveket támogat:
  * Kubenet (alapszintű) és speciális hálózatkezelés (Azure CNI) a fürtön és a társított összetevőkön belül
  * Kapcsolódás más Azure-szolgáltatásokhoz és alkalmazásokhoz
  * Bejövő vezérlők és bejövő vagy terheléselosztó konfigurációk
  * Hálózati teljesítmény és késés

A Microsoft nem nyújt technikai támogatást a következőkhöz:

* Kérdések a Kubernetes használatáról. A Microsoft ügyfélszolgálata például nem ad tanácsot az egyéni bejövő vezérlők létrehozásához, az alkalmazások számítási feladatainak használatához, vagy harmadik féltől származó vagy nyílt forráskódú szoftvercsomagok vagy eszközök alkalmazásához.
  > [!NOTE]
  > Microsoft ügyfélszolgálata tud tanácsot adni az AK-fürtök működéséről, testreszabásáról és finomhangolásáról (például Kubernetes működési problémák és eljárások).
* Harmadik féltől származó nyílt forráskódú projektek, amelyek nem a Kubernetes-vezérlési sík részeként vannak megadva, vagy AK-fürtökkel vannak telepítve. Ezek a projektek lehetnek Istio, Helm, megbízottat vagy mások.
  > [!NOTE]
  > A Microsoft a harmadik féltől származó nyílt forráskódú projektek, például a Helm és a Kured számára nyújt legjobb támogatást. Ha a harmadik féltől származó nyílt forráskódú eszköz integrálva van a Kubernetes Azure Cloud providerrel vagy más AK-specifikus hibákkal, a Microsoft a Microsoft dokumentációjában szereplő példákat és alkalmazásokat támogatja.
* Harmadik féltől származó zárt forrású szoftver. Ez a szoftver biztonsági ellenőrzési eszközöket és hálózati eszközöket, illetve szoftvereket tartalmazhat.
* A többfelhős vagy a többgyártós kiépítéssel kapcsolatos problémák. A Microsoft például nem támogatja az összevont többnyilvános felhő-gyártói megoldás futtatásával kapcsolatos problémákat.
* Az [AK dokumentációjában](./index.yml)nem szereplő hálózati testreszabások.
  > [!NOTE]
  > A Microsoft támogatja a hálózati biztonsági csoportokkal (NSG) kapcsolatos problémákat és hibákat. A Microsoft ügyfélszolgálata például választ kaphat a NSG sikertelen frissítésével vagy váratlan NSG vagy terheléselosztó viselkedésével kapcsolatos kérdésekre.

## <a name="aks-support-coverage-for-worker-nodes"></a>AK-támogatás a munkavégző csomópontok számára

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>A Microsoft feladatai az AK munkavégző csomópontjaihoz

A Microsoft és az ügyfelek megosztják a Kubernetes munkavégző csomópontok felelősségét, ahol:

* Az alap operációs rendszer rendszerképének a szükséges kiegészítései vannak (például figyelési és hálózati ügynökök).
* A munkavégző csomópontok automatikusan kapják meg az operációs rendszer javításait.
* A Kubernetes vezérlő síkja a munkavégző csomópontokon futó összetevőivel kapcsolatos problémákat automatikusan szervizeli a rendszer. Az összetevők többek között a következők:
  * Kube-proxy
  * Hálózati alagutak, amelyek kommunikációs útvonalakat biztosítanak a Kubernetes fő összetevőinek
  * Kubelet
  * Docker vagy Moby Daemon

> [!NOTE]
> Ha egy feldolgozó csomóponton nem működik a vezérlési sík összetevője, előfordulhat, hogy az AK-csapatnak újra kell indítania az egyes összetevőket vagy a teljes munkavégző csomópontot. Ezek az újraindítási műveletek automatizáltak, és automatikus szervizelést biztosítanak a gyakori problémákhoz. Ezek az újraindítások csak a _csomópont_ szintjén, és nem a fürtön történnek, kivéve, ha vészhelyzeti karbantartásra vagy kimaradásra van szükség.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Az AK munkavégző csomópontjaihoz tartozó ügyfél-felelősségi körök

A Microsoft nem indít automatikusan újraindítási munkavégző csomópontokat az operációsrendszer-szintű javítások alkalmazásához. Bár az operációsrendszer-javítások a munkavégző csomópontokra érkeznek, az *ügyfél* feladata a munkavégző csomópontok újraindítása a módosítások alkalmazásához. A megosztott kódtárak, démonok, például a szilárdtest hibrid meghajtója (SSHD) és a rendszer vagy operációs rendszer szintjén található egyéb összetevők automatikusan kijavítottak.

Az ügyfelek a Kubernetes-frissítések végrehajtásáért felelősek. Az Azure vezérlőpulton vagy az Azure CLI-n keresztül végezhetnek frissítéseket. Ez azokra a frissítésekre vonatkozik, amelyek a Kubernetes biztonsági vagy funkcionalitásbeli tökéletesítését tartalmazzák.

#### <a name="user-customization-of-worker-nodes"></a>A munkavégző csomópontok felhasználói testreszabása
> [!NOTE]
> Az AK munkavégző csomópontok a Azure Portalban normál Azure IaaS-erőforrásokként jelennek meg. Ezeket a virtuális gépeket azonban egy egyéni Azure-erőforráscsoporthoz helyezik üzembe (az MC * előtaggal \\ ). Az AK-beli feldolgozó csomópontok kibővítésére az alapkonfigurációjuk alapján van lehetősége. A Secure Shell (SSH) segítségével például úgy módosíthatja az AK-feldolgozó csomópontokat, hogy a szokásos virtuális gépeket módosítsák. Az alap operációsrendszer-rendszerkép azonban nem módosítható. Előfordulhat, hogy az egyéni módosítások nem tartanak fenn frissítést, méretezést, frissítést vagy újraindítást. A *sávon kívüli és az AK API-k hatókörén kívüli* változások **azonban**nem támogatottak. Kerülje a munkavégző csomópontok módosítását, kivéve, ha a Microsoft ügyfélszolgálata a módosítások elvégzésére utasítja.

A fent meghatározott, nem támogatott műveletek kiadása, például az összes ügynök csomópontjának lefoglalása nem támogatott, a fürtöt nem támogatja. Az AK fenntartja a jogot arra, hogy archiválja azokat a vezérlési síkokat, amelyek a további 30 napnál nem hosszabb időszakra vonatkozó támogatási irányelvek alapján vannak konfigurálva. Az AK biztonsági mentéseket tart fenn a fürt etcd metaadataiban, és a fürt könnyen újra kiosztható. Ezt az újraosztást bármely PUT művelet kezdeményezheti, amely a fürtöt visszahelyezi a támogatásba, például frissítésre vagy méretezésre az Active Agent-csomópontokra.

Az AK kezeli a munkavégző csomópontok életciklusát és műveleteit az ügyfelek nevében – a munkavégző csomópontokhoz társított IaaS-erőforrások módosítása **nem támogatott**. A nem támogatott műveletek egyik példája, hogy a VMSS-portálon vagy a VMSS API-n keresztül manuálisan módosítja a VMSS konfigurációit a virtuális gépek méretezési csoportjának testreszabásához.
 
A munkaterhelés-specifikus konfigurációk vagy csomagok esetében az AK javasolja a [Kubernetes-daemonsets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)használatát.

A Kubernetes privilegizált daemonsets és az init-tárolók használata lehetővé teszi, hogy az ügyfelek a külső gyártótól származó szoftvereket hangolják/módosíthassák vagy telepítsenek a fürt feldolgozói Ilyen testreszabási példák például az egyéni biztonsági ellenőrzési szoftverek hozzáadása vagy a sysctl-beállítások frissítése.

Habár ez egy ajánlott elérési út, ha a fenti követelmények érvényesek, az AK-mérnöki és-támogatás nem tud segítséget nyújtani a hibás/nem funkcionális módosítások hibaelhárításában vagy diagnosztizálásában, illetve azoknál, amelyek a csomópontot az ügyfelek által telepített daemonset elemet miatt elérhetetlenné teszik.

> [!NOTE]
> Az AK *felügyelt szolgáltatásként* olyan célokkal rendelkezik, mint például a javítások, a frissítések és a naplók felelősségének megszüntetése, hogy a szolgáltatások kezelése még teljesebb és kikapcsolt legyen. Ahogy a szolgáltatás kapacitása a teljes körű felügyelet növekedésével nő, a jövőbeli kiadások kihagyhatnak bizonyos funkciókat (például a csomópontok újraindítását és az automatikus javítást).

### <a name="security-issues-and-patching"></a>Biztonsági problémák és javítás

Ha egy vagy több AK-összetevőben talál biztonsági hibát, az AK-csapat az összes érintett fürtöt kijavítja a probléma enyhítése érdekében. Azt is megteheti, hogy a csapat a felhasználók számára is frissíti az útmutatót.

A biztonsági hiányosságot okozó munkavégző csomópontok esetében, ha a nulla leállás miatti javítás elérhető, az AK csapata ezt a javítást fogja alkalmazni, és értesíti a felhasználókat a változásról.

Ha egy biztonsági javításhoz a munkavégző csomópont újraindítása szükséges, a Microsoft értesíti az ügyfeleket erről a követelményről. Az ügyfél feladata a fürt javításának vagy frissítésének lekérése. Ha a felhasználók az AK-útmutatás szerint nem alkalmazzák a javításokat, a fürt továbbra is sebezhető marad a biztonsági problémákkal kapcsolatban.

### <a name="node-maintenance-and-access"></a>Csomópont-karbantartás és-hozzáférés

A munkavégző csomópontok közös felelősséggel rendelkeznek, és az ügyfelek tulajdonában vannak. Emiatt az ügyfeleknek lehetősége van bejelentkezni a munkavégző csomópontokra, és potenciálisan ártalmas változásokat, például kernel-frissítéseket, illetve csomagok telepítését vagy eltávolítását végezni.

Ha az ügyfelek romboló módosításokat végeznek, vagy a vezérlési sík összetevői offline állapotba lépnek vagy nem működnek, az AK felismeri ezt a hibát, és automatikusan visszaállítja a munkavégző csomópontot az előző munkaállapotba.

Bár az ügyfelek bejelentkezhetnek és módosíthatják a munkavégző csomópontokat, ezt nem kell elhagyni, mert a módosítások nem támogatják a fürtöt.

## <a name="network-ports-access-and-nsgs"></a>Hálózati portok, hozzáférés és NSG

Felügyelt szolgáltatásként az AK speciális hálózatkezelési és kapcsolódási követelményekkel rendelkezik. Ezek a követelmények kevésbé rugalmasak, mint a normál IaaS-összetevőkre vonatkozó követelmények. Az AK-ban az olyan műveletek, mint például a NSG-szabályok testreszabása, egy adott port blokkolása (például a 443-as kimenő portot letiltó tűzfalszabályok használata), valamint az engedélyezési URL-címek lehetővé teszik a fürt nem támogatott.

> [!NOTE]
> Az AK jelenleg nem teszi lehetővé, hogy teljes mértékben zárolja a kimenő forgalmat a fürtből. A fürt által a kimenő forgalomhoz használható URL-címek és portok listájának szabályozásához lásd: a kimenő [forgalom korlátozása](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Az Alpha és a Beta Kubernetes funkciói nem támogatottak

Az AK csak a felsőbb rétegbeli Kubernetes projektben lévő stabil funkciókat támogatja. Ha másként nincs dokumentálva, az AK nem támogatja a felsőbb rétegbeli Kubernetes projektben elérhető Alpha és Beta funkciókat.

Előfordulhat, hogy az Alpha vagy a Beta funkcióit az általánosan elérhetővé tétel előtt két forgatókönyvben kell elvégezni:

* Az ügyfelek az AK-termékekkel, támogatással vagy mérnöki csapatokkal találkoztak, és kérték az új funkciók kipróbálását.
* Ezeket [a funkciókat a szolgáltatás jelzője engedélyezte](https://github.com/Azure/AKS/blob/master/previews.md). Az ügyfeleknek explicit módon be kell jelentkezniük a szolgáltatások használatára.

## <a name="preview-features-or-feature-flags"></a>Előzetes verziójú funkciók vagy szolgáltatások jelzői

A kiterjesztett tesztelést és felhasználói visszajelzéseket igénylő funkciók és funkciók esetében a Microsoft új előzetes verziójú funkciókat vagy szolgáltatásokat bocsát ki a szolgáltatás jelölője mögött. Ezeket a funkciókat előzetes vagy bétaverziós funkciókként érdemes figyelembe venni.

Az előzetes verziójú funkciók vagy a szolgáltatás-jelölő funkciók nem éles környezetben használhatók. Az API-k és a viselkedés, a hibajavítások és más változások folyamatos változásai instabil fürtöket és állásidőt okozhatnak.

A nyilvános előzetes verzióban elérhető funkciók a "legjobb erőfeszítés" támogatásban részesülnek, mivel ezek a funkciók előzetes verzióban érhetők el, és nem éles környezetben, és az AK technikai támogatási csapatai csak munkaidőben támogatottak. További információt a következő témakörben talál:

* [Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Az előzetes verziójú funkciók az Azure- *előfizetések* szintjén lépnek életbe. Ne telepítse az előzetes verziójú szolgáltatásokat éles előfizetésre. Éles előfizetésnél az előzetes verziójú funkciók megváltoztathatják az alapértelmezett API-viselkedést, és befolyásolhatják a rendszeres műveleteket.

## <a name="upstream-bugs-and-issues"></a>Felsőbb rétegbeli hibák és problémák

A felsőbb rétegbeli Kubernetes projekt fejlesztésének gyorsasága miatt a hibák mindig felmerülhetnek. Ezen hibák némelyike nem oldható fel és nem dolgozható fel az AK-rendszeren belül. A hibajavítások Ehelyett nagyobb javítások szükségesek a felsőbb rétegbeli projektekhez (például Kubernetes, node vagy Worker operációs rendszerekhez és kernelekhez). A Microsoft tulajdonában lévő összetevők (például az Azure Cloud Provider), az AK és az Azure személyzete elkötelezett a Közösségen belüli felsőbb szintű problémák megoldásában.

Ha egy vagy több felsőbb rétegbeli hiba okozza a technikai támogatási problémát, az AK-támogatás és a mérnöki csapatok a következőket teszik:

* Azonosítsa és kapcsolja össze a felsőbb rétegbeli hibákat az összes kiegészítő részlettel, hogy elmagyarázza, miért befolyásolja a probléma a fürtjét vagy a munkaterhelést. Az ügyfelek a szükséges adattárakra mutató hivatkozásokat kapnak, így megtekinthetik a problémákat, és megtekinthetik, hogy egy új kiadás mikor fog javításokat nyújtani.
* Adja meg a lehetséges megkerülő megoldásokat és a megoldást. Ha a problémát enyhíteni lehet, a rendszer egy [ismert problémát](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) fog benyújtani az AK-tárházban. Az ismert problémákkal kapcsolatos bejelentés a következőket ismerteti:
  * A probléma, beleértve a felsőbb rétegbeli hibákra mutató hivatkozásokat is.
  * Megkerülő megoldás, valamint a frissítés vagy a megoldás más megőrzésének részletei.
  * A probléma felvételének durva ütemezése a felsőbb rétegbeli kiadási lépésszám alapján.
