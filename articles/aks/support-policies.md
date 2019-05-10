---
title: Támogatja a szabályzatok az Azure Kubernetes Service (AKS)
description: Útmutató az Azure Kubernetes Service (AKS) támogatási házirendek, közös felelősség és funkciók előzetes (vagy alpha vagy beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506682"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Házirendek támogatása az Azure Kubernetes Service-ben

Ez a cikk részletesen műszaki támogatási irányelveik és korlátozások az Azure Kubernetes Service (AKS). A cikk a munkavégző csomópont-felügyelet, a felügyelt vezérlési sík összetevők, a külső nyílt forráskódú összetevőkkel és a biztonsági vagy patch management is részletesen.

## <a name="service-updates-and-releases"></a>Szolgáltatási hírek és kiadások

* Kiadási információkért lásd: [AKS kibocsátási megjegyzések](https://github.com/Azure/AKS/releases).
* Előzetes verzióban elérhető funkciókról további információért lásd: [AKS előzetes verziójú funkciók és a kapcsolódó projektek](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Felügyelt szolgáltatások, az aks-ben

Alapvető infrastruktúra-szolgáltatás (IaaS) cloud egy összetevők, például a számítási és hálózati összetevőket, hozzáférést a felhasználóknak alacsony szintű vezérlők és a testreszabási lehetőségek. Ezzel szemben az AKS biztosít egy kulcsrakész üzemelő Kubernetes-példányt, amelyekkel a felhasználók a konfigurációkat és funkciókat kell közös csoportját. AKS-ügyfelek csak korlátozottan testreszabási, üzembe helyezésére és egyéb lehetőségek. Ezeket az ügyfeleket nem kell foglalkoznia, vagy közvetlenül a Kubernetes-fürtök kezelése.

Az aks-sel, az ügyfél lekéri egy teljes körűen felügyelt *vezérlősík*. A vezérlősík összes összetevőjének és az ügyfél az működtetni, és adja meg a Kubernetes-fürtök a végfelhasználók számára szükséges szolgáltatásokat tartalmazza. Az összes Kubernetes összetevő kezeli és a Microsoft által üzemeltetett.

A Microsoft felügyeli, és figyeli a Vezérlőpulton keresztül a következő összetevők:

* Kubelet- vagy Kubernetes API-kiszolgáló
* Etcd vagy egy kompatibilis kulcs-érték tároló, a szolgáltatásminőség (QoS), a méretezhetőség és a futtatókörnyezet megadása
* DNS-szolgáltatások (például kube-dns vagy CoreDNS)
* Kubernetes-proxy és a hálózat

Az AKS nem egy teljesen felügyelt fürt megoldás. Egyes összetevők, például a munkavégző csomópontok, rendelkezik *megosztott felelősségre*, ahol felhasználókat kell segít fenntartani az AKS-fürtöt. Felhasználói bevitel szükséges, ha például egy feldolgozó csomópont operációs rendszer (OS) biztonsági javítást.

A szolgáltatások *felügyelt* abban az értelemben, hogy a Microsoft és az AKS-csapat helyez üzembe, működik, és a szolgáltatás rendelkezésre állása és funkciókat. Ügyfeleink ezeket felügyelt összetevőket nem módosítható. A Microsoft testreszabási egységesek és méretezhetőek legyenek a felhasználói élmény biztosítása érdekében korlátozza. Egy teljes mértékben testreszabható megoldással, lásd: [AKS motor](https://github.com/Azure/aks-engine).

> [!NOTE]
> AKS-munkavégző csomópontok az Azure Portalon rendszeres Azure IaaS-erőforrások tartalomként jelennek meg. De ezek a virtuális gépek vannak üzembe helyezve egy egyéni Azure-erőforráscsoportot (minősítéssel, MC előtaggal\\*). Akkor lehet módosítani az AKS-feldolgozó csomópontok. Például használja a Secure Shell (SSH) AKS munkavégző csomópontok megváltoztatni megváltoztatja normál virtuális gépek (azonban nem, módosíthatja az alap operációsrendszer-képet, és a változtatások esetleg nem megőrzéséhez a frissítés vagy újraindítás) és egyéb Azure-erőforrások csatolhat az aks-ben munkavégző csomópontok. Ha módosít, de *sávon kívüli felügyelet és a testreszabás,* észleltek válhat az AKS-fürtöt. Ne változtassa feldolgozó csomópontokat, hacsak Support arra utasítja a hajthat végre.

## <a name="shared-responsibility"></a>Közös felelősség

Ha a fürt létrejött, az ügyfél a Kubernetes AKS létrehozó feldolgozó csomópontok határozza meg. Ügyfél munkaterheléseinek végrehajtása ezeken a csomópontokon. Ügyfelek a saját, és megtekintheti vagy módosíthatja a feldolgozó csomópontok.

Ügyfél fürtcsomópontok titkos kódot, és a bizalmas adatok tárolása, mert Support hozzájuk férhetnek csak korlátozott módon. A Microsoft Support, hajtsa végre a parancsokat és megtekinthetők a naplófájlok számára ezek a csomópontok expressz ügyfél engedélyt vagy támogatás nélkül nem tud bejelentkezni.

Mivel a munkavégző csomópontok-és nagybetűket, a Microsoft bármilyen korlátozása a háttérben futó felügyeleti vesz igénybe. Sok esetben a számítási feladatok futtatásához, akkor is, ha a Kubernetes fő csomópontok etcd és más Microsoft által felügyelt összetevői sikertelen továbbra is. Carelessly módosított munkavégző csomópontok okozhatnak a veszteségeket és számítási feladatok, és találja meg a fürt észleltek.

## <a name="aks-support-coverage"></a>AKS-támogatás terjedelme

A Microsoft technikai támogatást nyújt a következőkhöz:

* Kapcsolódás a Kubernetes összetevők a Kubernetes-szolgáltatást biztosít, amely támogatja, például az API-kiszolgálóhoz.
* Felügyeleti, hasznos üzemidőt, a QoS és kubernetes operations szabályozhatja az adatsík szolgáltatások (fő Kubernetes-csomópontokon, API-t kiszolgáló, etcd és kube-dns, a példában).
* Etcd. Támogatás magában foglalja az adatok etcd automatikus, átlátható biztonsági mentések a vészhelyreállítás megtervezése és a fürt állapot visszaállítását 30 percenként. Ezeket a biztonsági másolatokat közvetlenül elérhető az ügyfelek vagy a felhasználók nem. Biztosítják az adatok megbízhatóságának és konzisztenciát.
* Minden integrációs pontokat a Kubernetes Azure-felhő szolgáltató illesztőprogramját. Ezek közé tartozik például terheléselosztók, állandó kötetek vagy (a Kubernetes és az Azure CNI) hálózat más Azure-szolgáltatásokkal való integráció.
* Kérdések és problémák kapcsolatos testreszabási vezérlési sík összetevők, például a Kubernetes API-t, a etcd és a kube-dns kiszolgáló.
* Hálózati, például az Azure CNI, kubenet, vagy más hálózati hozzáférés és funkcionális problémák kapcsolatos probléma. Problémák lehetnek a DNS feloldási, csomagvesztés, Útválasztás és így tovább. A Microsoft a különböző hálózati forgatókönyveket támogatja:
  * Kubenet (alapszintű) és a speciális hálózatkezelés (Azure CNI) a fürtön belül, és a kapcsolódó összetevők
  * Más Azure-szolgáltatásokhoz és alkalmazásokhoz való kapcsolódás
  * Bejövő forgalom vezérlőket és a bejövő és a load balancer-konfigurációk
  * Hálózati teljesítmény és a késés

A Microsoft nem nyújt technikai támogatást, a a következőket:

* Kubernetes használatával kapcsolatos kérdésekre. Ha például Microsoft Support nem tanácsainak hozzon létre egyéni bejövő tartományvezérlőket, alkalmazások és szolgáltatások használatával, vagy külső és nyílt forráskódú szoftvercsomagokat vagy eszközök.
  > [!NOTE]
  > A Microsoft Support is tanácsokkal AKS fürt funkcióit, testreszabási és a finomhangolással kapcsolatban (a példában, Kubernetes-működési problémákra és eljárások).
* A Kubernetes részeként nem biztosított külső nyílt forráskódú projektek adatsík szabályozhatja, vagy az AKS-fürt üzembe helyezéséhez. Ezek a projektek lehetnek Istio, a Helm, az Envoy vagy mások.
  > [!NOTE]
  > A Microsoft legjobb támogatás külső nyílt forráskódú projekt, például a Helm és Kured is biztosít. A külső nyílt forráskódú eszköz a Kubernetes Azure felhőszolgáltató vagy más AKS-specifikus hibák integrálható, ahol a Microsoft támogatja, példákat és az alkalmazások a Microsoft-dokumentációt.
* Külső lezárt forráskódú szoftver. Ez a szoftver biztonsági vizsgálati eszközök és a hálózati eszközök vagy a szoftver tartalmazhat.
* Multicloud vagy multivendor build mintáit kapcsolatos problémákat. Például a Microsoft nem támogatja egy összevont multipublic felhőalapú gyártói megoldást futtató kapcsolatos problémákat.
* Hálózati testreszabások kivételével, amelyek nem szerepelnek a [AKS dokumentációját](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > A Microsoft támogatja a problémákat és a hálózati biztonsági csoportok (NSG) kapcsolatos hibákat. Ha például Support kérdésekre képes választ adni egy NSG-t frissítése sikertelen, vagy egy nem várt NSG-t vagy a Betöltés a terheléselosztó viselkedését.

## <a name="aks-support-coverage-for-worker-nodes"></a>Az AKS támogatás terjedelme munkavégző csomópontok

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>A Microsoft feladatkörei AKS munkavégző csomópontok

A Microsoft és az ügyfelek felelőssége Kubernetes munkavégző csomópontok megosztása ahol:

* Az alap operációsrendszer-lemezkép hozzáadása (például a figyelés, és a hálózat ügynökök) szükséges.
* A feldolgozó csomópontok automatikusan megkapja az operációsrendszer-javítások.
* A Kubernetes-szel kapcsolatos problémák automatikusan megtörténjen a feldolgozó csomópontokon futó összetevők adatsík szabályozza. Összetevők a következők:
  * Kube-proxy
  * a kubernetes kommunikációs útvonalak biztosító hálózati alagutak fő összetevői
  * kubelet
  * Docker vagy Moby démon

> [!NOTE]
> A munkavégző csomóponton Ha egy vezérlő adatsík összetevő nem működik, az AKS-csapat előfordulhat, hogy kell a teljes munkavégző csomópont újraindítása. A korlátozott hozzáférés az ügyfél aktív számítási feladatok és az adatokat, mert az AKS-csapat munkavégző csomópont újraindul, csak akkor, ha az ügyfél a probléma kivizsgálásához. Amikor csak lehetséges, az AKS-csapat működik, nehogy egy újra kell indítani az alkalmazást.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Az AKS munkavégző csomópontok az ügyfél feladatkörei

A Microsoft nem bejelölve operációsrendszer-szintű-javítások alkalmazása a feldolgozó csomópontokat. Bár a feldolgozó csomópontok operációsrendszer-javítások érkeznek a *ügyfél* felelős az újraindítás a feldolgozó csomópontok, a módosítások életbe léptetéséhez. Megosztott szalagtárakkal, például a hibrid tartós állapotú meghajtót (SSHD) démonok és más összetevőket, a rendszer vagy az operációs rendszer szintjén automatikusan javítani.

Kubernetes-frissítések végrehajtása ügyfelek felelőssége. Frissítés az Azure Vezérlőpultján vagy az Azure CLI, hajthat végre. Ez vonatkozik a frissítések, amelyek biztonsági vagy Kubernetes funkció fejlesztései tartalmazzák.

> [!NOTE]
> Mivel az AKS- *felügyelt szolgáltatás*, a záró célokat tartalmazza a javítások, frissítések, felelősséget eltávolítása és a naplót, hogy a service management befejeződött, és hands-off. Növeli a kapacitást a szolgáltatás teljes körű felügyeleti, mivel a jövőbeli kiadások előfordulhat, hogy hagyja el bizonyos funkciók (például csomópont újraindítása, és automatikus javítás).

### <a name="security-issues-and-patching"></a>Biztonsági problémák és javítása

Ha egy biztonsági hiba található egy vagy több összetevőt az AKS, az AKS-csapat a probléma megoldásához az összes érintett fürtök fog javítások. Azt is megteheti a csapat biztosít a felhasználók frissítéséhez.

A feldolgozó csomópontokat, hogy egy biztonsági hiba merül fel az, ha az üzemszünet nélküli javítás érhető el az AKS-csapat a alkalmazni a javítást, és értesítse a felhasználókat a változásról.

Amikor egy szervizcsomag szükséges a munkavégző csomópont újraindítása, a Microsoft értesíti ügyfeleit, ennek a követelménynek. Az ügyfél újraindítása vagy frissítése az beolvasása a fürt javítás felelős. Ha a felhasználók nem alkalmazza a javításokat az AKS útmutatás alapján, a fürt továbbra is sebezhetők az a biztonsági probléma.

### <a name="node-maintenance-and-access"></a>Csomópont karbantartási és hozzáférés

Munkavégző csomópontok ügyfelek tulajdonosa, és a egy megosztott kell eljárnia. Emiatt az ügyfélnél jelentkezzen be a munkavégző csomópontok és vélhetően kárt okozó módosításokat például kernelfrissítés és telepítése vagy eltávolítása a csomagokat.

Ha ügyfelek romboló módosításokat, vagy okozhat vezérlési sík összetevők kapcsolat nélkül vagy működésképtelenné válik, az AKS ezt a hibát észleli, és automatikus visszaállítása a munkavégző csomópont a korábbi működési állapotát.

Bár az ügyfelek jelentkezzen be, és módosítsa a feldolgozó csomópontokat, ez nem javasolt, mert is módosításokat egy fürt észleltek.

## <a name="network-ports-access-and-nsgs"></a>Hálózati portok, hozzáférési és NSG-k

Felügyelt szolgáltatásként az AKS a saját hálózati és a csatlakozási követelményekkel rendelkezik. Ezek a követelmények kevésbé rugalmas normál IaaS-összetevők követelményei. Az aks-ben műveletek, például egy adott portot (például, amely letiltja a 443-as porton keresztüli kimenő tűzfalszabályok) blokkolja az NSG-szabályok testreszabása, és engedélyezési URL-címeket is elérhetővé a fürt észleltek.

> [!NOTE]
> Jelenleg az AKS nem teszi lehetővé a fürtből (például explicit tartomány vagy port engedélyezési) származó kimenő forgalom teljes mértékben zárolását. Az URL-címek és portok listája az figyelmeztetés nélkül változhatnak. Az Azure támogatási jegy létrehozásával kérheti le a frissített listát tartalmazza. A lista csak azoknak az ügyfeleinknek, akik hajlandó elfogadni, hogy a fürt rendelkezésre állási hatással lehet a *bármikor.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Nem támogatott alfa és béta Kubernetes-szolgáltatások

Az AKS támogatja a felsőbb rétegbeli Kubernetes-projektben csak a stabil funkciókat. Kivéve, ha egyébként dokumentálva, az AKS nem támogatja a alfa és béta elérhető funkciókat a felsőbb rétegbeli Kubernetes-projektben.

A két esetben alfa vagy a bétaverzió funkciói esetleg történni általánosan elérhető:

* Ügyfelek az AKS-termék, támogatási vagy fejlesztői csapataival megfelelt, és próbálja ki az új funkciókról kérte.
* Ezek a funkciók már [jelzőt a szolgáltatás által engedélyezett](https://github.com/Azure/AKS/blob/master/previews.md). Ügyfelek explicit módon kell engedélyezve a a funkciók használatához.

## <a name="preview-features-or-feature-flags"></a>Előzetes verziójú funkciók vagy a szolgáltatás jelzők

Szolgáltatásainak és a kiterjesztett tesztelés igénylő funkciók és felhasználói visszajelzéseket a Microsoft közzétette új előzetes verziójú funkciók vagy szolgáltatások mögött funkció azt a jelzőt. Vegye figyelembe ezeket a funkciókat, prerelease vagy a bétaverzió funkciói.

Előzetes verziójú funkciók vagy a szolgáltatás-jelző funkciók nem éles céljára. API-k és a viselkedés, a hibajavítások és a további módosításokat folyamatban lévő változásai nem stabil fürtök és az állásidőt eredményezhet.

Nyilvános előzetes verzióban érhető el a szolgáltatások olyan "ajánlott beavatkozást" támogatás keretében tartoznak, ezek a szolgáltatások előzetes verzióként érhetők el és nem éles céljára, és csak munkaidőben az AKS technikai támogatási csapat által támogatott. További információért tekintse meg:

* [Az Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Előzetes verziójú funkciók az Azure érvénybe *előfizetés* szintjét. Ne telepítse az előzetes verziójú funkciók egy éles előfizetésre. Egy adott éles előfizetés előzetes verziójú funkciók API alapértelmezés módosíthatja, és befolyásolja a normál működést.

## <a name="upstream-bugs-and-issues"></a>Felsőbb rétegbeli hibák és problémák

Adja meg a fejlesztés sebessége a felsőbb rétegbeli Kubernetes-projektben, hibák tüntetnek merülhetnek fel. Ezek a hibák némelyike nem tudjon fókuszálni vagy az AKS rendszerből körül működött. Hibajavításokat tartalmaz, nagyobb javítások felsőbb rétegbeli projektekben (például a Kubernetes, a csomópont- vagy feldolgozói operációs rendszerek és a kernelt) van szükség. A Microsoft tulajdonában van (például az Azure-felhő szolgáltató) összetevők AKS és Azure személyzet elkötelezettek közösségi felső problémák kijavítása.

A technikai támogatási probléma a gyökér-oka egy vagy több felsőbb rétegbeli hibák, az AKS támogatási és mérnöki csapat fogja végrehajtani:

* Határozza meg, és a felsőbb rétegbeli hibái támogató részletes magyarázatát, hogy miért érdemes ezt a hibát észleli, a fürt vagy a számítási feladatok való hivatkozásra. Ügyfelei a szükséges tárházak mutató hivatkozásokat fogadjon, így tekintse meg a problémákat, és tekintse meg, mikor adjon meg egy új kiadás javításokat.
* Adja meg a lehetséges megoldások vagy megoldásokkal kapcsolatban. Ha a probléma enyhíthető, egy [ismert hiba](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) az AKS-tárházban fogja benyújtani. Az ismert-probléma-bejelentési ismerteti:
  * A probléma, beleértve a felsőbb rétegbeli hibák mutató hivatkozásokat.
  * A megoldás és a egy frissítés, vagy egy másik részleteit a megoldás megőrzését.
  * Durva vonatkozóan a probléma a felvételt a felsőbb rétegbeli tempót alapján.
