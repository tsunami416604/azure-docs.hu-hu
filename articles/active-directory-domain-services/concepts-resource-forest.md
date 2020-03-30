---
title: Erőforráserdő-fogalmak az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogy mi az az erőforráserdő az Azure Active Directory tartományi szolgáltatásokban, és hogy ezek milyen előnyökkel járnak a szervezet számára a korlátozott felhasználói hitelesítési beállításokkal vagy biztonsági problémákkal rendelkező hibrid környezetben.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: a583e32cbc3d58d5dfc5616335b2f38ad20fac14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233609"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Erőforráserdő-fogalmak és -szolgáltatások az Azure Active Directory tartományi szolgáltatásokhoz

Az Azure Active Directory tartományi szolgáltatások (AD DS) bejelentkezési élményt nyújt az örökölt, helyszíni és üzletági alkalmazások hoz. A helyszíni és a felhőbeli felhasználók felhasználói, csoportjai és jelszókiszavai szinkronizálódnak az Azure AD DS felügyelt tartományával. Ezek a szinkronizált jelszókivétek biztosítják a felhasználók számára a helyszíni Active Directory, az Office 365 és az Azure Active Directory számára használható hitelesítő adatok egyetlen készletét.

Bár biztonságos és további biztonsági előnyöket biztosít, egyes szervezetek nem szinkronizálhatják ezeket a felhasználói jelszavakat az Azure AD vagy az Azure AD DS. Előfordulhat, hogy a szervezet felhasználói nem ismerik a jelszavukat, mert csak intelligens kártyás hitelesítést használnak. Ezek a korlátozások megakadályozzák, hogy egyes szervezetek az Azure AD DS segítségével emelje fel és helyezze át a helyszíni klasszikus alkalmazásokat az Azure-ba.

Ezeknek az igényeknek és korlátozásoknak a kielégítésére létrehozhat egy Azure AD DS felügyelt tartományt, amely egy erőforráserdőt használ. Ez az általános könyv ismerteti, hogy mik azok az erdők, és hogyan bíznak más erőforrásokban a biztonságos hitelesítési módszer biztosításához. Az Azure AD DS erőforrás-erdők jelenleg előzetes verzióban.

> [!IMPORTANT]
> Az Azure AD DS erőforrás-erdők jelenleg nem támogatják az Azure HDInsight vagy az Azure Files. Az alapértelmezett Azure AD DS felhasználói erdők mindkét további szolgáltatást támogatja.

## <a name="what-are-forests"></a>Mik azok az erdők?

Az *erdő* egy logikai konstrukció, amelyet az Active Directory tartományi szolgáltatások (AD DS) egy vagy több *tartomány csoportosítására*használnak. A tartományok ezután tárolóobjektumokat tárolnak a felhasználók vagy csoportok számára, és hitelesítési szolgáltatásokat nyújtanak.

Az Azure AD DS-ben az erdő csak egy tartományt tartalmaz. A helyszíni AD DS-erdők gyakran sok tartományt tartalmaznak. A nagy szervezetek, különösen az egyesülések és felvásárlások után, előfordulhat, hogy a végén több helyszíni erdők, amelyek mindegyike több tartományt tartalmaz.

Alapértelmezés szerint egy Azure AD DS felügyelt tartomány jön *létre,* mint egy felhasználói erdő. Ez az erdőtípus szinkronizálja az Azure AD összes objektumát, beleértve a helyszíni Activeád-ds környezetben létrehozott felhasználói fiókokat is. A felhasználói fiókok közvetlenül hitelesíthetik magukat az Azure AD DS felügyelt tartományával szemben, például bejelentkezhetnek egy tartományhoz csatlakozó virtuális gépbe. A felhasználói erdő akkor működik, ha a jelszókivonatok szinkronizálhatók, és a felhasználók nem használnak kizárólagos bejelentkezési módszereket, például intelligens kártya hitelesítést.

Egy Azure AD *DS-erőforrás-erdőben* a felhasználók hitelesítik egy egyirányú *erdőszintű bizalmi kapcsolat* a helyszíni AD DS-ből. Ezzel a módszerrel a felhasználói objektumok és a jelszó kimondott kibék nem szinkronizálva az Azure AD DS. A felhasználói objektumok és hitelesítő adatok csak a helyszíni AD DS-ben találhatók. Ez a megközelítés lehetővé teszi, hogy a vállalatok erőforrásokat és alkalmazásplatformokat üzemeltetjenek az Azure-ban, amelyek a klasszikus hitelesítéstől, például az LDAPS-től, a Kerberos-tól vagy az NTLM-től függenek, de a hitelesítési problémák vagy problémák törlődnek. Az Azure AD DS erőforrás-erdők jelenleg előzetes verzióban.

Az erőforráserdők azt is lehetővé teszik, hogy az alkalmazásokat összetevőként emelje fel és elmozdítsa. Számos régebbi helyszíni alkalmazás többrétegű, gyakran webkiszolgálót vagy előtér-kiszolgálót és számos adatbázissal kapcsolatos összetevőt használ. Ezek a szintek megnehezítik a teljes alkalmazás egy lépésben történő felemelése és a felhőre való áthelyezését. Az erőforráserdőkkel fokozatosan emelheti az alkalmazást a felhőbe, ami megkönnyíti az alkalmazás áthelyezését az Azure-ba.

## <a name="what-are-trusts"></a>Mik azok a bizalmi kapcsolatok?

Az egynél több tartománnyal rendelkező szervezeteknek gyakran szükségük van a felhasználókra egy másik tartomány megosztott erőforrásainak eléréséhez. A megosztott erőforrásokhoz való hozzáféréshez az egyik tartomány felhasználóinak hitelesíteniük kell magukat egy másik tartományban. A hitelesítési és engedélyezési lehetőségek biztosításához a különböző tartományokban lévő ügyfelek és kiszolgálók között *megbízhatósági kapcsolatnak* kell lennie a két tartomány között.

Tartományi megbízhatósági kapcsolatok esetén az egyes tartományok hitelesítési mechanizmusai megbíznak a másik tartományból érkező hitelesítésekben. A bizalmi kapcsolatok segítségével szabályos hozzáférést biztosítanak az erőforrás-tartomány (a *meghatalmazó* tartomány) megosztott erőforrásaihoz, ha ellenőrzik, hogy a bejövő hitelesítési kérelmek megbízható hatóságtól (a *megbízható* tartománytól) származnak-e. A bizalmi kapcsolatok hidakként működnek, amelyek csak a tartományok közötti érvényesített hitelesítési kérelmeket engedélyezik.

Az, hogy a bizalmi kapcsolat hogyan adja át a hitelesítési kérelmeket, a konfigurálásmódjától függ. A bizalmi kapcsolatok az alábbi módokon konfigurálhatók:

* **Egyirányú** – hozzáférést biztosít a megbízható tartományból a megbízó tartomány erőforrásaihoz.
* **Kétirányú** – hozzáférést biztosít az egyes tartományokból a másik tartomány erőforrásaihoz.

A bizalmi kapcsolatok úgy is konfigurálva vannak, hogy további bizalmi kapcsolatokat kezeljenek az alábbi módok egyikén:

* **Nem tranzitív** – A bizalmi kapcsolat csak a két megbízhatósági partner tartomány között létezik.
* **Tranzitív** – A megbízhatóság automatikusan kiterjed bármely más olyan tartományra, amelyben bármelyik partner megbízik.

Bizonyos esetekben a megbízhatósági kapcsolatok automatikusan létrejönnek a tartományok létrehozásakor. Máskor ki kell választania egy megbízhatósági típust, és kifejezetten létre kell hoznia a megfelelő kapcsolatokat. A használt bizalmi kapcsolatok konkrét típusai és a bizalmi kapcsolatok szerkezete attól függ, hogy az Active Directory címtárszolgáltatás hogyan van rendszerezve, és hogy a Windows különböző verziói léteznek-e a hálózaton.

## <a name="trusts-between-two-forests"></a>Bizalmi kapcsolatok két erdő között

Egyetlen erdőn belüli tartományi bizalmi kapcsolatokat egy másik erdőre is kiterjesztheti, ha manuálisan hoz létre egyirányú vagy kétirányú erdőszintű bizalmi kapcsolatot. Az erdőszintű bizalmi kapcsolat olyan tranzitív bizalmi kapcsolat, amely csak egy erdő gyökértartománya és egy második erdőgyökér-tartomány között létezik.

* Az egyirányú erdőszintű bizalmi kapcsolat lehetővé teszi, hogy az egyik erdő összes felhasználója megbízzon a másik erdő összes tartományában.
* A kétirányú erdőszintű bizalmi kapcsolat tranzitív bizalmi kapcsolatot képez mindkét erdő összes tartománya között.

Az erdőbizalmi trösztök tranzititása a két erdészeti partnerre korlátozódik. Az erdőszintű bizalmi kapcsolat nem terjed ki a partnerek által megbízhatónak vetett további erdőkre.

![Az Azure AD DS és a helyszíni AD DS közötti erdőszintű bizalmi kapcsolat diagramja](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

A szervezet Active Directory-struktúrájától függően különböző tartományi és erdőszintű megbízhatósági konfigurációkat hozhat létre. Az Azure AD DS csak egyirányú erdőszintű megbízhatósági kapcsolatot támogat. Ebben a konfigurációban az Azure AD DS erőforrásai megbízhatnak a helyszíni erdő összes tartományában.

## <a name="supporting-technology-for-trusts"></a>A bizalmi kapcsolatok technológiájának támogatása

A bizalmi kapcsolatok különböző szolgáltatásokat és szolgáltatásokat használnak, például a DNS-t a tartományvezérlők partneri erdőkben való megkereséséhez. A megbízhatósági kapcsolatok az NTLM és a Kerberos hitelesítési protokolloktól, valamint a Windows alapú engedélyezési és hozzáférés-vezérlési mechanizmusoktól is függenek, hogy biztonságos kommunikációs infrastruktúrát biztosítsanak az Active Directory-tartományokban és erdőkben. Az alábbi szolgáltatások és szolgáltatások segítik a sikeres bizalmi kapcsolatokat.

### <a name="dns"></a>DNS

Az AD DS-nek DNS-re van szüksége a tartományvezérlő (DC) helyéhez és elnevezéséhez. Az AD DS sikeres működéséhez a DNS következő támogatása áll rendelkezésre:

* Olyan névfeloldási szolgáltatás, amely lehetővé teszi, hogy a hálózati állomások és szolgáltatások megkeressék a tartományvezérlőket.
* Olyan elnevezési struktúra, amely lehetővé teszi, hogy a vállalat tükrözze szervezeti felépítését a címtárszolgáltatási tartományainak nevében.

A DNS-tartomány névtere általában az AD DS tartománynévteret tükröz. Ha az Active Directory tartományi szolgáltatások központi telepítése előtt van egy meglévő DNS-névtér, a DNS-névtér általában particionált az Active Directory számára, és létrejön egy DNS-altartomány és delegálás az Active Directory erdőgyökérszámára. Ezután minden Active Directory gyermektartományhoz további DNS-tartományneveket ad hozzá.

A DNS az Active Directory tartományvezérlők helyének támogatására is használható. A DNS-zónák olyan DNS-erőforrásrekordokat népesítenek fel, amelyek lehetővé teszik a hálózati állomások és szolgáltatások számára az Active Directory tartományvezérlők megkeresését.

### <a name="applications-and-net-logon"></a>Alkalmazások és hálózati bejelentkezés

Mind az alkalmazások, mind a Hálózati bejelentkezés szolgáltatás a Windows elosztott biztonsági csatornamodelljének összetevői. A Windows Server és az Active Directory integrálására integrált alkalmazások hitelesítési protokollokat használnak a Hálózati bejelentkezés szolgáltatással való kommunikációhoz, hogy biztonságos elérési út jön létre, amelyen keresztül hitelesítés fordulhat elő.

### <a name="authentication-protocols"></a>Hitelesítési protokollok

Az Active Directory tartományvezérlők az alábbi protokollok egyikével hitelesítik a felhasználókat és az alkalmazásokat:

* **Kerberos 5-ös verziójú hitelesítési protokoll**
    * A Kerberos 5-ös verziójú protokoll a Windows rendszert futtató és külső operációs rendszereket támogató helyszíni számítógépek által használt alapértelmezett hitelesítési protokoll. Ez a protokoll az 1510-es számú RFC-dokumentumban van megadva, és teljes mértékben integrálva van az Active Directory, a kiszolgálói üzenetblokk (SMB), a HTTP és a távoli eljáráshívás (RPC), valamint az ezeket a protokollokat használó ügyfél- és kiszolgálóalkalmazásokba.
    * A Kerberos protokoll használata esetén a kiszolgálónak nem kell kapcsolatba lépnie a tartományvezérlővel. Ehelyett az ügyfél kap egy jegyet a kiszolgálóhoz, ha a kiszolgálófiók tartományában lévő tartományvezérlőről kér egyet. A kiszolgáló ezután más hatóság megkérdezése nélkül érvényesíti a jegyet.
    * Ha egy tranzakcióban részt vevő számítógép nem támogatja a Kerberos 5-ös verzióját, a rendszer az NTLM protokollt használja.

* **NTLM hitelesítési protokoll**
    * Az NTLM protokoll egy klasszikus hálózati hitelesítési protokoll, amelyet a régebbi operációs rendszerek használnak. Kompatibilitási okokból az Active Directory-tartományok a korábbi Windows-alapú ügyfelekhez és kiszolgálókhoz, valamint külső operációs rendszerekhez tervezett alkalmazásokból származó hálózati hitelesítési kérelmek feldolgozására használják.
    * Ha az NTLM protokollt egy ügyfél és egy kiszolgáló között használja, a kiszolgálónak kapcsolatba kell lépnie egy tartományvezérlő tartományhitelesítési szolgáltatásával az ügyfél hitelesítő adatainak ellenőrzése érdekében. A kiszolgáló úgy hitelesíti az ügyfelet, hogy továbbítja az ügyfél hitelesítő adatait egy tartományvezérlőnek az ügyfélfiók-tartományban.
    * Ha két Active Directory-tartományt vagy -erdőt bizalmi kapcsolat köt össze, az ezekkel a protokollokkal végrehajtott hitelesítési kérelmek et úgy lehet átirányítani, hogy mindkét erdő erőforrásaihoz hozzáférést biztosítsanak.

## <a name="authorization-and-access-control"></a>Engedélyezés és hozzáférés-vezérlés

Az engedélyezési és megbízhatósági technológiák együttműködnek, hogy biztonságos kommunikációs infrastruktúrát biztosítsanak az Active Directory-tartományokban vagy erdőkben. Az engedélyezés határozza meg, hogy a felhasználó milyen szintű hozzáféréssel rendelkezik a tartomány erőforrásaihoz. A bizalmi kapcsolatok megkönnyítik a felhasználók tartományok közötti engedélyezését azáltal, hogy utat biztosítanak a felhasználók hitelesítéséhez más tartományokban, így az ezekben a tartományokban megosztott erőforrásokra vonatkozó kéréseik engedélyezhetők.

Ha egy megbízható tartományban egy hitelesítési kérelmet a megbízható tartomány érvényesít, az átkerül a célerőforráshoz. A célerőforrás ezután meghatározza, hogy engedélyezi-e a felhasználó, a szolgáltatás vagy a számítógép által a megbízható tartományban a hozzáférés-vezérlési konfiguráció alapján tett konkrét kérést.

A bizalmi kapcsolatok biztosítják ezt a mechanizmust a megbízó tartománynak átadott hitelesítési kérelmek érvényesítéséhez. Az erőforrás-számítógépen található hozzáférés-vezérlési mechanizmusok határozzák meg a megbízható tartományban a kérelmező számára biztosított végső hozzáférési szintet.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a bizalmi kapcsolatokról, olvassa el a [Hogyan működnek az erdőszintű bizalmi kapcsolatok az Azure AD DS-ben?][concepts-trust]

Az Azure AD DS felügyelt tartomány erőforráserdővel való létrehozásának első [lépései az Azure AD DS felügyelt tartományának létrehozása és konfigurálása][tutorial-create-advanced]című témakörben található. Ezután [létrehozhat egy kimenő erdőszintű bizalmi kapcsolatot egy helyszíni tartományban (előzetes verzió)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
