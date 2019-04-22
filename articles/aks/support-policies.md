---
title: Az Azure Kubernetes Service (AKS) támogatja a szabályzatok
description: Ismerje meg az Azure Kubernetes Service (AKS) támogatási házirendek, közös felelősség, előzetes/Alpha/bétaverziójának funkciói.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502173"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Az Azure Kubernetes Service (AKS) támogatja a szabályzatok

Ez a cikk részletesen körül AKS technikai támogatási házirendek, korlátozások és részleteit, beleértve a felügyelt munkavégző csomópont-felügyelet sík összetevők, a külső nyílt forráskódú összetevőkkel és a biztonsági / javítások kezelése.

## <a name="service-updates--releases"></a>Szolgáltatási hírek és kiadások

* Kiadási információ: a [AKS kibocsátási megjegyzései][1]
* Nyilvános előzetes verzióban elérhető szolgáltatásokat, lásd: [AKS előzetes verziójú funkciók és a kapcsolódó projektek][2]

## <a name="what-is-managed"></a>Mi a "felügyelt"

IaaS felhőalapú alapkomponensek számítási és a hálózat, amely alacsony szintű vezérlők és a testreszabási lehetőségek a felhasználók számára, kihasználva teszi közzé, ellentétben az AKS szolgáltatás biztosít egy kulcsrakész üzemelő Kubernetes-példányt, amely a leggyakoribb konfigurációk jelöl, és a Kubernetes esetében szükséges képességek. Ezt a szolgáltatást használó ügyfelek testreszabások, üzembe helyezésére és egyéb lehetőségek korlátozott számú rendelkezik. Ez egyben azt is jelenti, hogy ügyfeleink nem kell aggódni, vagy a Kubernetes-fürt közvetlenül kezelheti.

Az aks-sel, az ügyfél lekéri egy teljes körűen felügyelt **vezérlősík** – Ha a vezérlősík tartalmaz minden összetevők és szolgáltatások működtetéséhez, és adja meg a Kubernetes-fürtök a végfelhasználók számára szükséges. Az összes Kubernetes összetevő kezeli és a Microsoft által üzemeltetett.

Az a felügyelt **vezérlősík** a következő összetevők felügyelt és a Microsoft által figyelt:

* Kubelet / Kubernetes API-t kiszolgáló (ko)
* Etcd vagy egy kompatibilis kulcs/érték tároló – beleértve a szolgáltatás, a méretezhetőség és a futtatókörnyezet minősége
* DNS services (for example, kube-dns / CoreDNS)
* Kubernetes-Proxy/hálózatkezelés

Az AKS nem 100 %-os felügyelt **fürt** megoldás. Bizonyos összetevők (például a munkavégző csomópontok) rendelkezik bizonyos **közös felelősségekről** esetek ahol műveleteket karbantartása az AKS-fürtöt, felhasználói beavatkozásra van szükség. Például worker-csomópont operációs rendszer biztonsági javítás alkalmazása.

 **Felügyelt**, azt jelenti, hogy a Microsoft és az AKS-csapat helyez üzembe, működik, és a rendelkezésre állási és ezek a szolgáltatások működését. **Ügyfeleink ezeket az összetevőket nem módosítható**. Testreszabás korlátozódik egységesek és méretezhetőek legyenek felhasználói élményt nyújt. Egy teljes mértékben testreszabható megoldással, lásd: [AKS-motor](https://github.com/Azure/aks-engine).

> [!NOTE]
> Fontos megérteni, hogy az Azure Kubernetes Service-beli feldolgozó csomópontok tartalomként jelennek meg az Azure Portal rendszeres Azure IaaS-erőforrások, bár ezek a virtuális gépek vannak üzembe helyezve egy egyéni Azure-erőforráscsoportot (minősítéssel, MC előtaggal\\*). A felhasználó megváltoztathatja őket, SSH be csakúgy, mint a normál virtuális gépek (azonban nem, módosíthatja az alap operációsrendszer-képet, és a változtatások esetleg nem megőrzéséhez a frissítés vagy újraindítás) és más Azure-erőforrás csatolása, vagy másként módosíthatjuk. **Azonban ennek a sávon kívüli felügyelet és a testreszabási azt jelenti, hogy maga az AKS-fürtöt észleltek válhat. Kerülje a munkavégző csomópont lapjával Support utasításig.**

## <a name="what-is-shared-responsibility"></a>Mi a megosztott felelősségre

Fürt létrehozáskor az AKS Kubernetes munkavégző csomópontok az ügyfél által meghatározott számos hoz létre. Ezek a csomópontok feljegyzett, ahol az ügyfél munkaterheléseinek tevékenységében. Ügyfelek a saját, és megtekintheti vagy módosíthatja a munkavégző csomópontok.

Mivel ezek a csomópontok állnak végrehajtás alatt a titkos kódot, és a bizalmas adatok tárolásához, a Microsoft támogatási szolgálata **korlátozott hozzáféréssel** az összes ügyfél fürtcsomópont. A Microsoft támogatási nem jelentkezzen be, hajtsa végre a parancsokat vagy megtekinthetők a naplófájlok számára ezek a csomópontok nélkül expressz ügyfél engedélyt, illetve segítséget hajtsa végre a hibakeresési parancsokat útmutatása szerint a támogatási csoporthoz.

Ezek a munkavégző csomópontok bizalmas jellege miatt a Microsoft bármilyen korlátozása ezek a csomópontok "a háttérben" felügyeleti vesz igénybe. Akkor is, ha a Kubernetes fő csomópontok etcd és más összetevők sikertelen (Microsoft által felügyelt) a számítási feladatok futtatásához a legtöbb esetben továbbra is. Munkavégző csomópontok nélkül ellátás módosítják, adatokat és/vagy terhelési adatvesztéssel azt, és jelennek meg a fürt észleltek.

## <a name="azure-kubernetes-service-support-coverage"></a>Az Azure Kubernetes Service támogatásában

**A Microsoft technikai támogatást nyújt a következőkhöz:**

* A Kubernetes-szolgáltatást (például az API-kiszolgáló) által biztosított és támogatott összetevők Kubernetes való kapcsolódás
* Felügyeleti, a rendelkezésre állását, a QoS és a kubernetes az operations szabályozhatja az adatsík-szolgáltatások (Kubernetes fő csomópontok, API-kiszolgálóhoz, etcd, például kube-dns.
* Etcd támogatás magában foglalja az adatok etcd automatikus, átlátható biztonsági mentések a vészhelyreállítás megtervezése és a fürt állapot visszaállítását 30 percenként. Ezeket a biztonsági mentések nem érhetők el közvetlenül a felhasználók és felhasználói számára, és biztosítva az adatok megbízhatóságának és a konzisztencia
* Integráció más Azure-ban például a Kubernetes Azure Felhőszolgáltató illesztőprogram bármely integrációs pontjainak szolgáltatásokhoz, például a terheléselosztók, állandó kötetek, hálózati (Kubernetes, Azure CNI)
* Kérdések és problémák kerülő testreszabási vezérlési sík összetevők, például a Kubernetes API-t, a etcd és a kube-dns kiszolgáló.
* Hálózati témakörökről, például Azure CNI, Kubenet vagy más hálózati kiállítja a hozzáférési és funkciók problémák (DNS feloldási, csomagvesztés, Útválasztás és így tovább).
  * Támogatott hálózati forgatókönyvei közé tartozik a Kubenet (alapszintű) és a speciális hálózatkezelés (Azure CNI) belül a fürt és a kapcsolódó összetevőket, a kapcsolat más Azure-szolgáltatások és alkalmazások. Ezenkívül bejövő tartományvezérlők és a bejövő vagy terheléselosztó-konfiguráció, a hálózati teljesítmény és a késleltetés által támogatott Microsoft.

**A Microsoft nem nyújt technikai támogatást a a következőket:**

* Tanácsadó / "Útmutató" használata Kubernetes kérdéseket, például hogyan hozhat létre egyéni bejövő vezérlők, alkalmazások számítási feladatok kérdése van, és a külső-féltől származó és nyílt Forráskódú csomagokat vagy eszközök esnek a hatókörön.
  * Az aks-ben tanácsadó jegyek fürt funkciókat, testreszabási, finomhangolására – például: Kubernetes kapcsolatos problémák/útmutatóval-tos hatókörén belül vannak.
* Nincs megadva a Kubernetes részeként külső nyílt forráskódú projektek adatsík szabályozhatja, vagy üzembe helyezve az AKS-fürtök, például Istio, a Helm, az Envoy és mások.
  * Külső nyílt forráskódú projektek, például a Helm és Kured, elérhető legjobb lehetőség támogatásával áll rendelkezésre további példákat és a Microsoft-dokumentációt, és ahol a külső nyílt forráskódú eszköz integrálható-e a Kubernetes Azure felhőszolgáltató alkalmazásoktól vagy más AKS-specifikus hibák.
* Külső forrás lezárt szoftver – ezek lehetnek a biztonsági eszközök, eszköz vagy szoftver hálózati ellenőrzése.
* Nem támogatottak a "többfelhős" vagy a több szállító build mintáit kapcsolatos problémák, például az olyan az összevont több nyilvános felhő gyártói megoldást futtató nem támogatott.
* Bizonyos hálózati testreszabásokat, részletes ismertetését lásd: a hivatalos egyikéből [AKS dokumentációját][3].
  > [!NOTE]
  > Problémák és hibák körül hálózati biztonsági csoportok használata támogatott. Például támogatási kérdésekre képes választ adni az NSG-k való frissítése sikertelen volt, vagy váratlan NSG-t vagy a terheléselosztó viselkedését körül.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Az Azure Kubernetes Service támogatásában (munkavégző csomópont)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Munkavégző csomópontok az Azure Kubernetes Service a Microsoft feladatkörei

Leírtaknak megfelelően a `shared responsibility` szakaszban Kubernetes munkavégző csomópontok soroltuk egy közös-feladata modell, ahol:

* Az alap operációs rendszer lemezképének biztosítja a szükséges kiegészítésekkel (például a figyelés, és a hálózat ügynökök)
* A feldolgozó csomópontok operációsrendszer-javítások automatikus telepítését
* Kubernetes-szel kapcsolatos problémák automatikus szervizelését adatsík-összetevők, például a feldolgozó csomópontokon futó szabályozhatja:
  * kube-proxy
  * a kubernetes kommunikációs útvonalak biztosító hálózati alagutak fő összetevői
  * kubelet
  * docker/moby démon

> [!NOTE]
> Ha egy vezérlő adatsík összetevő nem működő egy munkavégző csomóponton, indítsa újra a teljes feldolgozó csomópontot, a probléma megoldásához az AKS-csapat szükségessé. Ez a felhasználó nevében történik, és nem végrehajtva, kivéve, ha egy ügyfél eszkalációs jön létre (a vevők aktív számítási feladatok és az adatok elérése miatt). Minden alkalommal, amikor lehetséges AKS csoporthoz fog működni, hogy a szükséges újraindítás nem alkalmazása érintő.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Az Azure Kubernetes Service munkavégző csomópontok az ügyfél feladatkörei

**A Microsoft nem hajtja végre:**

- Automatikus újraindítás feldolgozó csomópontokat az operációs rendszer. szintű javítások érvénybe léptetéséhez **(jelenleg lásd alább)**

Operációsrendszer-javítások érkezzenek a feldolgozó csomópontok, azonban a **az ügyfél felelőssége** , a feldolgozó csomópontok, a módosítások érvénybe léptetéséhez indítsa újra. Az automatikus javítással tartalmazza a megosztott szalagtárakkal, például az SSHD démonok és más rendszer/OS szintű összetevők.

Kubernetes-frissítéseket **ügyfelei felelősek az a frissítés végrehajtása** az Azure Vezérlőpultját, vagy az Azure CLI-n keresztül. Ez vonatkozik a frissítések, amelyek biztonsági vagy Kubernetes funkció fejlesztései tartalmazzák.

> [!NOTE]
> AKS-jébe egy `managed service` a szolgáltatás teljes célokat tartalmazza a felelősséget javítások, frissítések, naplók összegyűjtése és egyéb, hogy egy további teljesen felügyelt és hands-off szolgáltatás eltávolítása. Ezeket az elemeket (a csomópont újraindítása, automatikus javítást) a későbbi kiadásokban eltávolítható, növelje a teljes körű felügyeleti képességeket.

### <a name="security-issues-and-patching"></a>Biztonsági problémák és javítása

Bizonyos esetekben (például CVEs) egy biztonsági hiba található egy vagy több, az AKS szolgáltatás összetevőit. Ilyen esetekben AKS fog javítani az összes érintett fürtök a probléma megoldásához, ha lehetséges, vagy frissítési útmutatást nyújtanak a felhasználók számára.

Munkavégző csomópontok negatív hatással az ilyen biztonsági résre Ha a probléma egy adott üzemszünet nélküli javítás érhető el, a az AKS-csapat a alkalmazni a javítást, és értesítse a felhasználókat a változásról.

Ha egy biztonsági javítást igényel munkavégző csomópont újraindul, a Microsoft ennek a követelménynek az ügyfél értesíti, és az ügyfél felelős hajtsa végre az újraindítás és a javítás lekérése a fürt frissítése. Felhasználók a javításokat az AKS útmutatásának csak akkor érvényesíthetők, ha a fürt továbbra is sebezhetők az a hiba/hibák meghatározásához.

### <a name="node-maintenance-and-access"></a>Csomópont karbantartási és hozzáférés

Mivel a feldolgozó csomópontok egy közös felelősség és ügyfelek tulajdonjogát, ügyfelek jelentkezzen be ezek a feldolgozók, és hajtsa végre a vélhetően kárt okozó módosításokat, mint például a kernel-frissítések, csomagok eltávolítása és az új csomagok telepítése.

Ha az ügyfelek destruktív művelet, vagy vezérlési sík összetevők kapcsolat nélküli munka kiváltó műveleteket hajtson végre, vagy egyéb lenni nem működik, a AKS szolgáltatás, ez a hiba észlelése és a munkavégző csomópont visszaállítani az előző működő automatikus szervizelés végrehajtása állapot.

Bár az ügyfelek jelentkezzen be és munkavégző csomópontok az alter, *ellenjavallt,* mert is a fürt észleltek.

## <a name="network-ports-access-and-network-security-groups"></a>Hálózati portok, a hozzáférés és a hálózati biztonsági csoportok

Felügyelt szolgáltatásként az AKS a saját hálózati és a csatlakozási követelményekkel rendelkezik. Ezek a követelmények kevésbé rugalmas normál IaaS-összetevők. Ellentétben más IaaS-összetevők bizonyos műveletek (például a hálózati biztonsági csoportszabályok, adott porton blokkolja, engedélyezési URL-címe és így tovább testreszabása) is jelennek meg a észleltek fürt (például tűzfalszabályok blokkolja a 443-as kimenő porton).

> [!NOTE]
> Kimenő forgalom (például explicit tartomány/port engedélyezési) a fürtről teljesen sémákra jelenleg nem támogatjuk az AKS most. URL-címek és portok listája figyelmeztetés nélkül változhat, és biztosítható, hogy által az Azure-támogatási jegy keresztül. A megadott Tranzakciólista csak azoknak az ügyfeleinknek, akik hajlandó elfogadni, amely *bármikor hatással lehet a fürt rendelkezésre állását.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Alpha/béta Kubernetes funkciók (nem támogatott)

Az AKS csak támogatja a felsőbb rétegbeli Kubernetes-projektben stabil funkciókat. Alpha/bétaverziójú szolgáltatások a felsőbb rétegbeli Kubernetes használata nem támogatott, kivéve, ha egyébként közölt és dokumentált.

Van két olyan eset, ahol alfa vagy a bétaverzió funkciói előfordulhat, hogy történni a végleges verzió előtt:

* Ügyfelek megfelelt az AKS-termék, támogatási vagy fejlesztői csapataival, és explicit módon kérik, hogy próbálja ki az új funkciókról.
* Ezek a funkciók már [engedélyezve van egy funkció jelölője keresztül] [ 2] (explicit opt-in)

## <a name="preview-features--feature-flags"></a>Előzetes verziójú funkciók / jelzők funkció

A funkciókat és képességeket kiterjesztett tesztelési, közösségi és a felhasználói visszajelzések van szükség a Microsoft új előzetes verziójú funkciók vagy szolgáltatások mögött egy funkció jelölője ad ki. Ezek a szolgáltatások előzetes kell tekinteni, illetve bétaverziós, és kiváló kitéve arra, hogy az alábbi új funkciókat biztosít a felhasználóknak.

Azonban ezek az előzetes verzió, illetve a funkciók vannak nem üzemi használat céljára – API-k, változtatásokra, hibajavításokat és egyéb változások lehet tenni, hogy a szolgáltatás jelző instabil fürtök és az állásidőt eredményezhet. Ezen funkciók támogatását korlátozódik, hogy hibát vagy problémát jelent. Ne engedélyezze ezeket a funkciókat az éles rendszereket vagy az előfizetések.

> [!NOTE]
> Az Azure előzetes verziójú funkciók engedélyezésével érvénybe lép **előfizetés** szintjét. Ne telepítse előzetes verziójú funkciók éles előfizetés szerint, módosíthatja az alapértelmezett API működését érintő a normál működést.

## <a name="upstream-bugs-and-issues"></a>Felsőbb rétegbeli hibák és problémák

Adja meg a fejlesztés sebessége a felsőbb rétegbeli Kubernetes-projektben, nincsenek tüntetnek hibákról, nem javítható az AKS rendszerből körül dolgozott, illetve inkább megkövetelése (például a Kubernetes, a csomópont/feldolgozói OSE-kre és Kernelekkel) felsőbb rétegbeli projektek nagyobb javítások. A-összetevők (például az Azure Felhőszolgáltató) miénk AKS vagy az Azure munkatársai elkötelezettek felső a probléma kijavítása a közösségi.

Olyan esetekben, ahol a technikai támogatási probléma, legfelső szintű miatt egy vagy több felsőbb rétegbeli hibák az AKS támogatja, és mérnöki hajt végre a következő elemek:

* Határozza meg, és a felsőbb rétegbeli hibái bármely miért ez befolyásolja a fürt és/vagy terhelési támogató rendszernaplójából hivatkozásra. Ügyfelek biztosítja a szükséges kódtárak/problémák tekintse meg a problémákat, és láthatja, amikor új Kubernetes/egyéb kiadás tartalmazza a fix(es) mutató hivatkozásokkal
* A lehetséges dokumentálása vagy megoldások: Bizonyos esetekben lehetséges a probléma – ebben az esetben a "[ismert-probléma](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" az AKS-tárházban, amely azt ismerteti, be kell jelenteni:
  * A probléma, és a felsőbb rétegbeli hibák mutató hivatkozás
  * A megoldás, és körül frissítési/egyéb megőrzését a megoldás részletei
  * Nyers ütemtervek bekerülhetnek a felsőbb rétegbeli tempót alapján

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
