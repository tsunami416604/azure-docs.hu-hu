---
title: Az architektúra áttekintése – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory-bérlő fogalmát, és kezelése az Azure-ban az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d0511f008a3d5bc39a0fb2d9406d33b72dbede6
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532941"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Mi az az Azure Active Directory architektúrájának?

Az Azure Active Directory (Azure AD) lehetővé teszi, hogy biztonságosan kezelje az Azure-szolgáltatások és -erőforrások elérését a felhasználók számára. Az Azure AD-ben megtalálható az identitáskezelési megoldások teljes palettája. Az Azure AD-funkciókkal kapcsolatos információért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md)

Az Azure AD-vel felhasználókat és csoportokat hozhat létre és kezelhet, valamint engedélyeket adhat meg a vállalati erőforrások hozzáférésének engedélyezéséhez és megtagadásához. Az identitáskezeléssel kapcsolatos információért tekintse meg az [Azure-identitáskezelés alapjait leíró szakaszt](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Azure AD-architektúra

Az Azure AD földrajzilag elosztott architektúrája a széles körű megfigyelését, automatikus átirányítás, feladatátvételi és helyreállítási funkciókat, amelyeket vállalati szintű rendelkezésre állást és teljesítményt nyújthat az ügyfeleknek egyesíti.

Ez a cikk a következő architektúraelemeket tárgyalja:

*   Szolgáltatásarchitektúra kialakítása
*   Méretezhetőség
*   Folyamatos rendelkezésre állás
*   Adatközpontok

### <a name="service-architecture-design"></a>Szolgáltatásarchitektúra kialakítása

Az elérhető és használható, adatgazdag rendszerek létrehozásának leggyakoribb módja a független építőelemek vagy skálázási egységek használata. Az Azure AD adatszintjéhez a skálázási egységeket *partícióknak*nevezzük.

Az adatréteg több front-end szolgáltatással rendelkezik, amelyek olvasási és írási képességeket nyújtanak. Az alábbi ábra azt mutatja be, hogyan történik egy egykönyvtáros partíció összetevőinek terjesztése a földrajzilag elosztott adatközpontokban.

  ![Egykönyvtáras partíciós diagram](./media/active-directory-architecture/active-directory-architecture.png)

Az Azure AD architektúra összetevői között egy elsődleges replika és másodlagos replikák szerepelnek.

#### <a name="primary-replica"></a>Elsődleges replika

Az *elsődleges replika* fogadja azon partíció összes *írását*, amelyhez tartozik. Minden írási művelet azonnal replikálódik egy másik adatközpontban lévő másodlagos replikára, mielőtt a rendszer sikert jelezne a hívónak. Ezáltal biztosítható az írások georedundáns tartóssága.

#### <a name="secondary-replicas"></a>Másodlagos replikák

Az összes címtárbeli *olvasás* a *másodlagos replikák*szolgáltatásból származik, amelyek olyan adatközpontokban találhatók, amelyek fizikailag a különböző földrajzi régiókban találhatók. Sok másodlagos replika van, mivel az adatok replikálása aszinkron módon történik. A címtárbeli olvasások, például a hitelesítési kérelmek olyan adatközpontokban vannak kiszolgálva, amelyek az ügyfelekhez vannak közelebb. A másodlagos replikák felelősek az olvasás méretezhetőségéért.

### <a name="scalability"></a>Méretezhetőség

A méretezhetőség azon képesség, amellyel a szolgáltatások ki tudnak bővülni a növekvő teljesítményigények kielégítése érdekében. Az írás méretezhetősége az adatok particionálásával érhető el. Az olvasás méretezhetősége az adatok egy partícióról több, a világ számos részén elosztott másodlagos replikákra történő replikálásával érhető el.

Az adatközpontot, amelyben a fizikailag legközelebbi érkező kérések legyenek átirányítva. Az írások transzparens módon vannak átirányítva az elsődleges replikához az írás-olvasás konzisztenciájának biztosítása érdekében. A másodlagos replikák jelentősen kibővítik a partíciók méretét, mert a címtárak leginkább olvasásokat szolgáltatnak.

A címtáralkalmazások a legközelebbi adatközpontokhoz csatlakoznak. Ez a kapcsolat javítja a teljesítményt, és ezért horizontális felskálázása lehetséges. Mivel egy címtárpartíció sok másodlagos replikával rendelkezhet, a másodlagos replikák közelebb helyezhetők a címtár ügyfeleihez. Csak a belső címtárszolgáltatás írásigényes összetevői célozzák meg közvetlenül az aktív elsődleges replikát.

### <a name="continuous-availability"></a>Folyamatos rendelkezésre állás

A rendelkezésre állás (vagy üzemidő) határozza meg, hogy egy rendszer mennyire tud zavartalanul működni. Az Azure AD magas rendelkezésre állásának kulcsa az, hogy a szolgáltatások gyorsan áthelyezhetik a forgalmat több földrajzilag elosztott adatközpont között. Az egyes adatközpontok függetlenek, ami lehetővé teszi a dekorrelált meghibásodási módokat. Ezen a magas rendelkezésre állású kialakításon keresztül az Azure AD nem igényel állásidőt a karbantartási tevékenységekhez.

Az Azure AD partíciójának kialakítása a vállalati AD kialakításához, amely tartalmazza az elsődleges replika körültekintően összehangolt és determinisztikus feladatátvételi folyamatot egyszeri terv használatával képest egyszerű.

#### <a name="fault-tolerance"></a>Hibatűrés

A rendszereknek jobb a rendelkezésre állása, ha képesek tűrni a hardveres, hálózati és szoftverhibákat. A címtárban lévő összes partícióhoz tartozik egy magas rendelkezésre állású főreplika. Ez az elsődleges replika. Ezen a replikán csak a partíció írása történik. Ez a replika folyamatos megfigyelés alatt áll, és az írások azonnal egy másik replikára helyezhetők (amely az új elsődleges replikává válik), ha a rendszer hibát észlel. Feladatátvétel esetén általában 1-2 percig megszűnhet az írás rendelkezésre állása. Ez nincs hatással az olvasás rendelkezésre állására.

Az olvasási műveletek (amelyek száma nagyságrendekkel meghaladja az írásokét) csak a másodlagos replikákhoz érkeznek. Mivel a másodlagos replikák idempotensek, egy adott partíció bármely replikájának elvesztése könnyedén orvosolható az olvasások egy másik replikára történő irányításával, általában ugyanabban az adatközpontban.

#### <a name="data-durability"></a>Adattartósság

Az írás a tartósan előtt legalább két adatközponthoz van véglegesítve. Ez először véglegesíti az írást az elsődlegesen, majd azonnal replikálja az írást legalább egy másik adatközpontba. Ez az írási művelet biztosítja, hogy az elsődlegesként szolgáló adatközpont potenciális katasztrofális elvesztése ne okozza az adatvesztést.

Az Azure AD egy nulla [helyreállítási időre vonatkozó célkitűzést (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) tart fenn, hogy ne veszítse el a feladatátvételsel kapcsolatos adatvesztést. Az érintett műveletek közé tartoznak az alábbiak:

* Kiállítási és a címtárolvasások
* Így csak körülbelül 5 percet RTO címtárba

### <a name="datacenters"></a>Adatközpontok

Az Azure AD replikái a világ számos részén található adatközpontokban vannak tárolva. További információ: [Azure globális infrastruktúra](https://azure.microsoft.com/global-infrastructure/).

Az Azure AD az alábbi jellemzőkkel rendelkezik az adatközpontok között:

* Hitelesítés, a Graph és az egyéb AD-szolgáltatások az átjárószolgáltatás mögött található. Az átjáró kezeli ezen szolgáltatások terheléselosztását. Ez feladatátvételt hajt végre automatikus Ha bármelyik nem megfelelő állapotú kiszolgálókat észlel tranzakciós állapottesztek. Ezen állapot-tesztek alapján az átjáró dinamikusan irányítja át a forgalmat az egészséges adatközpontokhoz.
* *Olvasás*esetén a címtár másodlagos replikákkal és megfelelő előtér-szolgáltatásokkal rendelkezik, amelyek több adatközpontban működő aktív-aktív konfigurációban működnek. Egy teljes adatközpont meghibásodása esetén a rendszer automatikusan átirányítja a forgalmat egy másik adatközpontba.
 \* Az *írások*esetében a címtár az elsődleges (fő) replikát az adatközpontok között tervezett (az új elsődleges szinkronizálása a régi elsődleges) vagy a vészhelyzeti feladatátvételi eljárások segítségével végzi. Az adattartósságot úgy érheti el, hogy legalább két adatközpontba replikálja a végrehajtást.

#### <a name="data-consistency"></a>Adatkonzisztencia

A könyvtár modell az egyik végleges kényszerítené a felhasználókat. Az elosztott aszinkron replikációs rendszerek egyik általános problémája egy "adott" replikáról lekért adatok nem feltétlenül naprakészek. 

Az Azure AD-ben a másodlagos replikát megcélzó alkalmazások  olvasási és írási konzisztenciája úgy valósul meg, hogy az írásokat a rendszer az elsődleges replikára irányítja, és ezzel egyidejűleg visszahúzza azokat a másodlagos replikára.

Az Azure AD Graph API-t használó alkalmazások írásai számára nem kötelező az affinitás fenntartása egy címtár replikája felé az olvasási-írási konzisztencia érdekében. Az Azure AD Graph szolgáltatás egy logikai munkamenetet tart fenn, amely affinitással rendelkezik az olvasáshoz használt másodlagos replikához. Az affinitás "replika-tokenben" van rögzítve, amelyet a Graph szolgáltatás a másodlagos replika adatközpontjában lévő elosztott gyorsítótár használatával gyorsítótáraz. A jogkivonat ezután a logikai munkamenet következő műveleteihez lesz felhasználva. Ha továbbra is ugyanazt a logikai munkamenetet szeretné használni, akkor a későbbi kérelmeket ugyanahhoz az Azure AD-adatközponthoz kell irányítani. Ha a címtár-ügyfél kérései több Azure AD-adatközpontba vannak irányítva, nem lehet folytatni a logikai munkamenetet. Ha ez történik, az ügyfél több olyan logikai munkamenettel rendelkezik, amelyeknek független írási és olvasási konzisztencia van.

 >[!NOTE]
 >Az írásokat a rendszer azonnal replikálja arra a másodlagos replikára, amelyre a logikai munkamenet olvasásai ki lettek adva.

#### <a name="backup-protection"></a>Biztonsági mentés védelme

A címtár a végleges törlés helyett helyreállítható törlést valósít meg a felhasználók és bérlők számára, amely lehetővé teszi az egyszerű helyreállítást, ha az ügyfél esetleg véletlenül törölne valamit. A bérlői rendszergazda véletlenül töröl egy felhasználót, ha azok egyszerűen visszavonni, és helyreállíthatja a törölt felhasználót.

Az Azure AD naponta biztonsági másolatot készít az összes adatról, ezért képes az adatok mérvadó visszaállítására bármilyen logikai törlés vagy sérülés esetében. Az adatréteg adatrétegünk hibajavító kódokat alkalmaz, így azt is ellenőrizze a hibákat, és automatikusan kijavítani bizonyos típusú lemezhibákat.

#### <a name="metrics-and-monitors"></a>Metrikák és figyelők

Egy magas rendelkezésre állású szolgáltatás futtatásához világszínvonalú metrikára és monitorozási képességekre van szükség. Az Azure AD folyamatosan elemzi a szolgáltatások állapotával kapcsolatos legfontosabb mérőszámokat és az egyes szolgáltatások sikerességi feltételeit, illetve jelentést készít azokról. Nincs folyamatos fejlesztési és a hangolási mérőszámok és monitorozási és riasztási minden esetben minden egyes Azure AD szolgáltatásban, és az összes szolgáltatásban.

Ha bármelyik Azure AD szolgáltatás nem várt módon működik, azonnal műveletet funkciójának helyreállításához ismerhető meg leggyorsabban. A legfontosabb mérőszám az Azure AD nyomon követi a milyen gyorsan élő webhely problémákat észlelt, és az ügyfelek problémák elhárításáról. Komoly erőfeszítéseket teszünk a monitoring és a riasztások területén azért, hogy minimalizáljuk az észleléshez szükséges időt (cél: <5 perc). A működőképesség javításával pedig a hiba súlyosságának mérsékléséhez szükséges időt igyekszünk csökkenteni (cél: <30 perc).

#### <a name="secure-operations"></a>Biztonságos műveletek

Műveleti vezérlőket, például a többtényezős hitelesítés (MFA) használatával minden olyan művelet, valamint minden művelet naplózását. Emellett minden olyan operatív tevékenység igény szerinti folyamatos számára szükséges ideiglenes hozzáférést just-in-time jogosultságszint-emelési rendszert használ. További információkért lásd: [A megbízható felhő](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Következő lépések

[Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop)