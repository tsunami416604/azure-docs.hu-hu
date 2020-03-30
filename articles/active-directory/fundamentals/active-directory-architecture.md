---
title: Architektúra áttekintése - Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogy mi az Azure Active Directory-bérlő, és hogyan kezelheti az Azure-t az Azure Active Directory használatával.
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
ms.openlocfilehash: 854fb4649f8c1113f20abe5807dd0ce473ba6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368065"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Mi az Azure Active Directory architektúra?

Az Azure Active Directory (Azure AD) lehetővé teszi, hogy biztonságosan kezelje az Azure-szolgáltatások és -erőforrások elérését a felhasználók számára. Az Azure AD-ben megtalálható az identitáskezelési megoldások teljes palettája. Az Azure AD-funkciókkal kapcsolatos információért lásd: [Mi az az Azure Active Directory?](active-directory-whatis.md)

Az Azure AD-vel felhasználókat és csoportokat hozhat létre és kezelhet, valamint engedélyeket adhat meg a vállalati erőforrások hozzáférésének engedélyezéséhez és megtagadásához. Az identitáskezeléssel kapcsolatos információért tekintse meg az [Azure-identitáskezelés alapjait leíró szakaszt](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Azure AD-architektúra

Az Azure AD földrajzilag elosztott architektúrája széles körű figyelést, automatikus átirányítást, feladatátvételt és helyreállítási képességeket ötvöz, amelyek vállalati szintű rendelkezésre állást és teljesítményt biztosítanak az ügyfelek számára.

Ez a cikk a következő architektúraelemeket tárgyalja:

*   Szolgáltatásarchitektúra kialakítása
*   Méretezhetőség
*   Folyamatos rendelkezésre állás
*   Adatközpontok

### <a name="service-architecture-design"></a>Szolgáltatásarchitektúra kialakítása

A hozzáférhető és használható, adatgazdag rendszer létrehozásának leggyakoribb módja független építőelemek vagy méretarányos egységek. Az Azure AD adatréteg, méretezési egységek *nevezzük partíciókat.*

Az adatréteg több front-end szolgáltatással rendelkezik, amelyek olvasási és írási képességeket nyújtanak. Az alábbi ábra bemutatja, hogy az egycímtárpartíció összetevői hogyan kerülnek kézbesítésre a földrajzilag elosztott adatközpontokban.

  ![Egycímtárú partíciódiagram](./media/active-directory-architecture/active-directory-architecture.png)

Az Azure AD architektúra összetevői között egy elsődleges replika és másodlagos replikák szerepelnek.

#### <a name="primary-replica"></a>Elsődleges replika

Az *elsődleges replika* fogadja azon partíció összes *írását*, amelyhez tartozik. Minden írási művelet azonnal replikálódik egy másik adatközpontban lévő másodlagos replikára, mielőtt a rendszer sikert jelezne a hívónak. Ezáltal biztosítható az írások georedundáns tartóssága.

#### <a name="secondary-replicas"></a>Másodlagos replikák

Az összes *címtárolvasás* *másodlagos replikákból*van kijavítva, amelyek fizikailag különböző földrajzi területeken található adatközpontokban találhatók. Sok másodlagos replika van, mivel az adatok replikálása aszinkron módon történik. A címtárolvasások, például a hitelesítési kérelmek, az ügyfelekhez közel álló adatközpontokból vannak kiszolgálva. A másodlagos replikák felelősek az olvasás méretezhetőségéért.

### <a name="scalability"></a>Méretezhetőség

A méretezhetőség azon képesség, amellyel a szolgáltatások ki tudnak bővülni a növekvő teljesítményigények kielégítése érdekében. Az írás méretezhetősége az adatok particionálásával érhető el. Az olvasás méretezhetősége az adatok egy partícióról több, a világ számos részén elosztott másodlagos replikákra történő replikálásával érhető el.

A címtáralkalmazásokból érkező kérelmek et a fizikailag legközelebbi adatközpontba irányítja a rendszer. Az írások transzparens módon vannak átirányítva az elsődleges replikához az írás-olvasás konzisztenciájának biztosítása érdekében. A másodlagos replikák jelentősen kibővítik a partíciók méretét, mert a címtárak leginkább olvasásokat szolgáltatnak.

A címtáralkalmazások a legközelebbi adatközpontokhoz csatlakoznak. Ez a kapcsolat javítja a teljesítményt, ezért a horizontális felskálázás lehetséges. Mivel egy címtárpartíció sok másodlagos replikával rendelkezhet, a másodlagos replikák közelebb helyezhetők a címtár ügyfeleihez. Csak a belső címtárszolgáltatás írásigényes összetevői célozzák meg közvetlenül az aktív elsődleges replikát.

### <a name="continuous-availability"></a>Folyamatos rendelkezésre állás

A rendelkezésre állás (vagy üzemidő) határozza meg, hogy egy rendszer mennyire tud zavartalanul működni. Az Azure AD magas rendelkezésre állásának kulcsa az, hogy a szolgáltatások gyorsan átterelheti a forgalmat több földrajzilag elosztott adatközpontok között. Minden adatközpont független, amely lehetővé teszi a korrelált hibamódok. Ezzel a magas rendelkezésre állású kialakítással az Azure AD nem igényel állásidőt a karbantartási tevékenységekhez.

Az Azure AD partícióterve a vállalati AD-tervhez képest egyszerűsödik, egy egyfőes kialakítást használva, amely gondosan hangszerelt és determinisztikus elsődleges replika feladatátvételi folyamatot tartalmaz.

#### <a name="fault-tolerance"></a>Hibatűrés

A rendszereknek jobb a rendelkezésre állása, ha képesek tűrni a hardveres, hálózati és szoftverhibákat. A címtárban lévő összes partícióhoz tartozik egy magas rendelkezésre állású főreplika. Ez az elsődleges replika. Ezen a replikán csak a partíció írása történik. Ez a replika folyamatos megfigyelés alatt áll, és az írások azonnal egy másik replikára helyezhetők (amely az új elsődleges replikává válik), ha a rendszer hibát észlel. Feladatátvétel esetén általában 1-2 percig megszűnhet az írás rendelkezésre állása. Ez nincs hatással az olvasás rendelkezésre állására.

Az olvasási műveletek (amelyek száma nagyságrendekkel meghaladja az írásokét) csak a másodlagos replikákhoz érkeznek. Mivel a másodlagos replikák idempotensek, egy adott partíció bármely replikájának elvesztése könnyedén orvosolható az olvasások egy másik replikára történő irányításával, általában ugyanabban az adatközpontban.

#### <a name="data-durability"></a>Adatok tartóssága

Az írás imázsa tartósan elkötelezte magát legalább két adatközpontban, mielőtt azt nyugtázzák. Ez úgy történik, hogy először véglegesíti az írást az elsődleges, majd azonnal replikálja az írást legalább egy másik adatközpontba. Ez az írási művelet biztosítja, hogy az elsődleges takarékot üzemeltető adatközpont esetleges katasztrofális elvesztése ne eredményezse az adatvesztést.

Az Azure AD nulla [helyreállítási idő célkitűzést (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) tart fenn, hogy ne veszítse el a feladatátvételi adatokat. Az érintett műveletek közé tartoznak az alábbiak:

* Tokenkiállítás és könyvtárolvasás
* Lehetővé teszi csak körülbelül 5 perc RTO könyvtár írások

### <a name="datacenters"></a>Adatközpontok

Az Azure AD replikái a világ számos részén található adatközpontokban vannak tárolva. További információ: [Azure global infrastructure.](https://azure.microsoft.com/global-infrastructure/)

Az Azure AD az alábbi jellemzőkkel rendelkező adatközpontok között működik:

* A hitelesítés, a Graph és más AD-szolgáltatások az átjárószolgáltatás mögött vannak. Az átjáró kezeli ezen szolgáltatások terheléselosztását. Automatikusan átveszi a feladatát, ha a rendszer tranzakciós állapotminta segítségével észleli a sérült kiszolgálókat. Ezek alapján az állapotpróbák, az átjáró dinamikusan irányítja a forgalmat a kifogástalan állapotú adatközpontok.
* *Olvasások*esetén a címtár másodlagos replikákkal és a megfelelő előtér-szolgáltatásokkal rendelkezik egy több adatközpontban működő aktív-aktív konfigurációban. Egy teljes adatközpont meghibásodása esetén a forgalom automatikusan egy másik adatközpontba lesz irányítva.
 **Írások*esetén a címtár feladatátvételi rendszer az adatközpontok között tervezett (új elsődleges szinkronizálás a régi elsődleges) vagy a vészhelyzeti feladatátvételi eljárások. Az adatok tartóssága úgy érhető el, hogy legalább két adatközpontba replikálja a véglegesítést.

#### <a name="data-consistency"></a>Adatkonzisztencia

A címtármodell az egyik végleges konzisztencia. Az elosztott aszinkron replikálórendszeregyik tipikus problémája, hogy az "adott" kópiából visszaadott adatok nem feltétlenül naprakészek. 

Az Azure AD-ben a másodlagos replikát megcélzó alkalmazások  olvasási és írási konzisztenciája úgy valósul meg, hogy az írásokat a rendszer az elsődleges replikára irányítja, és ezzel egyidejűleg visszahúzza azokat a másodlagos replikára.

Az Azure AD Microsoft Graph API-ját használó alkalmazásírások az írási és olvasási konzisztencia címtárreplikájának fenntartásából származnak. A Microsoft Graph API-szolgáltatás logikai munkamenetet tart fenn, amely affinitást jelent az olvasáshoz használt másodlagos kópiához; affinitás egy "replika jogkivonat", hogy a szolgáltatás gyorsítótárak egy elosztott gyorsítótár a másodlagos replika adatközpontban. A jogkivonat ezután a logikai munkamenet következő műveleteihez lesz felhasználva. Ugyanannak a logikai munkamenetnek a további használatával a további kérelmeket ugyanahhoz az Azure AD-adatközponthoz kell irányítani. Nem lehet folytatni a logikai munkamenetet, ha a címtárügyfél-kérelmek több Azure AD-adatközpontba vannak irányítva; ha ez megtörténik, akkor az ügyfél több logikai munkamenettel rendelkezik, amelyek független olvasási és olvasási konzisztenciákkal rendelkeznek.

 >[!NOTE]
 >Az írásokat a rendszer azonnal replikálja arra a másodlagos replikára, amelyre a logikai munkamenet olvasásai ki lettek adva.

#### <a name="backup-protection"></a>Biztonsági másolatok védelme

A címtár a végleges törlés helyett helyreállítható törlést valósít meg a felhasználók és bérlők számára, amely lehetővé teszi az egyszerű helyreállítást, ha az ügyfél esetleg véletlenül törölne valamit. Ha a bérlői rendszergazda véletlenül törli a felhasználókat, könnyen visszavonhatja és visszaállíthatja a törölt felhasználókat.

Az Azure AD naponta biztonsági másolatot készít az összes adatról, ezért képes az adatok mérvadó visszaállítására bármilyen logikai törlés vagy sérülés esetében. Az adatréteg hibajavító kódokat alkalmaz, így ellenőrizheti a hibákat, és automatikusan kijavíthatja a lemezhibák bizonyos típusait.

#### <a name="metrics-and-monitors"></a>Mérőszámok és figyelők

Egy magas rendelkezésre állású szolgáltatás futtatásához világszínvonalú metrikára és monitorozási képességekre van szükség. Az Azure AD folyamatosan elemzi a szolgáltatások állapotával kapcsolatos legfontosabb mérőszámokat és az egyes szolgáltatások sikerességi feltételeit, illetve jelentést készít azokról. Az egyes Azure AD-szolgáltatásokon belül és az összes szolgáltatásban folyamatosan kifejlesztése és finomítása, valamint figyelés és riasztás az egyes forgatókönyvekben.

Ha bármely Azure AD-szolgáltatás nem a várt módon működik, azonnal intézkedik a funkcionalitás mielőbbi visszaállítása érdekében. A legfontosabb metrika Az Azure AD-sávok, hogy milyen gyorsan élő webhely problémák észlelhetők és mérséklődnek az ügyfelek számára. Komoly erőfeszítéseket teszünk a monitoring és a riasztások területén azért, hogy minimalizáljuk az észleléshez szükséges időt (cél: <5 perc). A működőképesség javításával pedig a hiba súlyosságának mérsékléséhez szükséges időt igyekszünk csökkenteni (cél: <30 perc).

#### <a name="secure-operations"></a>Biztonságos műveletek

Működési vezérlők, például többtényezős hitelesítés (MFA) használata bármely művelethez, valamint az összes művelet naplózása. Ezenkívül a just-in-time jogosultsági rendszer használatával biztosítják a szükséges ideiglenes hozzáférést minden olyan operatív feladathoz, amely folyamatosan elérhető. További információkért lásd: [A megbízható felhő](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>További lépések

[Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop)