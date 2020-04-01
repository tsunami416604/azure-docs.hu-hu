---
title: A bizalmi kapcsolatok működésének megoldása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: További információ az erdőszintű bizalmi kapcsolat azure AD tartományi szolgáltatásokkal való működéséről
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 903881a1d15c1f043e381f50e5b69d661cd08192
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476439"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>A megbízhatósági kapcsolatok működése az erőforráserdőkben az Azure Active Directory tartományi szolgáltatásokban

Az Active Directory tartományi szolgáltatások (AD DS) több tartomány vagy erdő közötti biztonságot nyújt a tartomány- és erdőszintű bizalmi kapcsolatokon keresztül. Mielőtt a hitelesítés a bizalmi kapcsolatok között megtörténne, a Windows rendszernek először ellenőriznie kell, hogy a felhasználó, számítógép vagy szolgáltatás által kért tartomány nak van-e megbízhatósági kapcsolata a kérelmező fiók tartományával.

A megbízhatósági kapcsolat ellenőrzéséhez a Windows biztonsági rendszer bizalmi útvonalat számít ki a kérést fogadó kiszolgáló tartományvezérlője és a kérelmező fiók tartományában lévő tartományvezérlő között.

Az AD DS és a Windows elosztott biztonsági modellje által biztosított hozzáférés-vezérlési mechanizmusok környezetet biztosítanak a tartományi és erdőszintű bizalmi kapcsolatok működéséhez. Ahhoz, hogy ezek a bizalmi kapcsolatok megfelelően működjenek, minden erőforrásnak vagy számítógépnek rendelkeznie kell egy közvetlen megbízhatósági útvonalat egy tartományvezérlőhöz abban a tartományban, amelyben található.

A megbízhatósági elérési utat a Hálózati bejelentkezés szolgáltatás valósítja meg a megbízható tartományi hatósággal létesített hitelesített távoli eljáráshívási (RPC) kapcsolattal. A biztonságos csatorna a tartományok közötti megbízhatósági kapcsolatokon keresztül más AD DS-tartományokra is kiterjed. Ez a biztonságos csatorna a felhasználók és csoportok biztonsági információinak beszerzésére és ellenőrzésére szolgál, beleértve a biztonsági azonosítókat (SID-ket).

## <a name="trust-relationship-flows"></a>Bizalmi kapcsolati folyamatok

A bizalmi kapcsolatok feletti biztonságos kommunikáció folyamata határozza meg a bizalmi kapcsolat rugalmasságát. A bizalmi kapcsolat létrehozásának vagy konfigurálásának módját határozza meg, hogy a kommunikáció milyen mértékben terjedjen ki az erdőkön belül vagy erdők között.

A bizalmi kapcsolatok feletti kommunikáció áramlását a bizalmi kapcsolat iránya határozza meg. A bizalmi kapcsolatok lehetnek egyirányúak vagy kétirányúak, és lehetnek tranzitívak vagy nem tranzitívak.

Az alábbi ábra azt mutatja, hogy *Tree 2* az *1.* Ennek eredményeképpen *az 1.* *Tree 2* *Tree 1* *Tree 2*

![Két erdő közötti bizalmi kapcsolatok diagramja](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Egyirányú és kétirányú bizalmi kapcsolatok

Az erőforrásokhoz való hozzáférést lehetővé tevő bizalmi kapcsolatok lehetnek egyirányúak vagy kétirányúak.

Az egyirányú megbízhatósági kapcsolat egy egyirányú hitelesítési útvonal, amelyet két tartomány között hoztak létre. Az A és *a B* *tartomány* közötti egyirányú bizalmi kapcsolatban az A *tartomány* felhasználói hozzáférhetnek a B *tartomány*erőforrásaihoz. A B *tartomány* felhasználói azonban nem férhetnek hozzá az *A tartomány*erőforrásaihoz.

Egyes egyirányú bizalmi kapcsolatok lehetnek nem tranzitívak vagy tranzitívak a létrehozott bizalmi kapcsolat típusától függően.

Kétirányú bizalmi kapcsolatban *az A tartomány* megbízik a B *tartományban,* a *B tartomány* pedig az *A tartományban*. Ez a konfiguráció azt jelenti, hogy a hitelesítési kérelmek mindkét irányban továbbíthatók a két tartomány között. Egyes kétirányú kapcsolatok a létrehozott bizalmi kapcsolat típusától függően nem tranzitívak vagy tranzitívak lehetnek.

Az AD DS-erdőben lévő összes tartománykapcsolat kétirányú, tranzitív megbízhatósági kapcsolat. Új gyermektartomány létrehozásakor a rendszer automatikusan kétirányú, tranzitív bizalmi kapcsolatot hoz létre az új gyermektartomány és a szülőtartomány között.

### <a name="transitive-and-non-transitive-trusts"></a>Tranzitív és nem tranzitív bizalmi kapcsolatok

A tranzitivitás határozza meg, hogy a bizalmi kapcsolat kiterjeszthető-e azon a két tartományon kívül, amellyel létrehozták.

* A tranzitív bizalmi kapcsolat a más tartományokkal fennálló bizalmi kapcsolatok kiterjesztésére használható.
* A nem tranzitív bizalmi kapcsolat a más tartománnyal fennálló bizalmi kapcsolatok megtagadására használható.

Minden alkalommal, amikor új tartományt hoz létre egy erdőben, a rendszer automatikusan kétirányú, tranzitív bizalmi kapcsolatot hoz létre az új tartomány és a szülőtartomány között. Ha gyermektartományokat ad hozzá az új tartományhoz, a megbízhatósági útvonal felfelé halad a tartományhierarchián keresztül, kiterjesztve az új tartomány és a szülőtartomány között létrehozott kezdeti megbízhatósági útvonalat. A tranzitív bizalmi kapcsolatok felfelé haladnak a tartományfán keresztül, ahogy létrejön, tranzitív bizalmi kapcsolatokat hozva létre a tartományfa összes tartománya között.

A hitelesítési kérelmek követik ezeket a megbízhatósági útvonalakat, így az erdő bármely tartományából származó fiókokat az erdő bármely más tartománya hitelesítheti. Egyetlen bejelentkezési folyamattal a megfelelő engedélyekkel rendelkező fiókok hozzáférhetnek az erdő bármely tartományának erőforrásaihoz.

## <a name="forest-trusts"></a>Erdőszintű megbízhatóságok

Az erdőszintű bizalmi kapcsolatok segítenek a szegmentált AD DS-infrastruktúrák kezelésében, valamint az erőforrásokhoz és más objektumokhoz való hozzáférés támogatásában több erdőben. Az erdőbizalmi kapcsolatok hasznosak a szolgáltatók, az egyesülésen vagy felvásárláson átesett vállalatok, az együttműködésen alapuló üzleti extranetek és az adminisztratív autonómia megoldását kereső vállalatok számára.

Az erdőszintű bizalmi kapcsolatok használatával két különböző erdőt kapcsolhat össze egyirányú vagy kétirányú tranzitív bizalmi kapcsolat kialakításához. Az erdőszintű megbízhatósági kapcsolat lehetővé teszi a rendszergazdák számára, hogy két AD DS-erdőt egyetlen megbízhatósági kapcsolattal kapcsoljanak össze, hogy zökkenőmentes hitelesítési és engedélyezési élményt nyújtsanak az erdőkben.

Erdőszintű bizalmi kapcsolat csak az egyik erdő erdő gyökértartománya és egy másik erdő gyökértartománya között hozható létre. Az erdőszintű bizalmi kapcsolatok csak két erdő között hozhatók létre, és implicit módon nem terjeszthetők ki egy harmadik erdőre. Ez a viselkedés azt jelenti, hogy ha *Forest 2*erdőszintű bizalmi kapcsolat jön létre *Forest 3*az *1.* *Forest 2* *Forest 1* *Forest 3*

Az alábbi ábra két különálló erdőszintű megbízhatósági kapcsolatot mutat be három AD DS-erdő között egy szervezetben.

![Erdőszintű bizalmi kapcsolatok diagramja egyetlen szervezeten belül](./media/concepts-forest-trust/forest-trusts.png)

Ez a példa konfigurációja a következő hozzáférést biztosítja:

* A *2-es erdő* felhasználói az *1.* *Forest 3*
* A *3-as erdő* felhasználói a *2.*
* Az *1.* *Forest 2*

Ez a konfiguráció nem teszi lehetővé az *1-es erdő* felhasználói számára a *3-as erdő* erőforrásainak elérését, vagy fordítva. Ahhoz, hogy az *1-es* és a *3-as erdő* felhasználói megoszthassák az erőforrásokat, kétirányú tranzitív megbízhatósági kapcsolatot kell létrehozni a két erdő között.

Ha két erdő között egyirányú erdőszintű bizalmi kapcsolat jön létre, a megbízható erdő tagjai használhatják a megbízó erdőben található erőforrásokat. A bizalmi kapcsolat azonban csak egy irányban működik.

Ha például egyirányú erdőszintű bizalmi kapcsolat jön létre az *Forest 2* *1.*

* Az *1-es erdő* tagjai hozzáférhetnek a *2-es erdőben*található erőforrásokhoz.
* A *2-es erdő* tagjai nem férhetnek hozzá az *1-es erdőben* található erőforrásokhoz ugyanazzal a megbízhatósági kapcsolatsal.

> [!IMPORTANT]
> Az Azure AD tartományi szolgáltatások erőforráserdő csak támogatja az egyirányú erdőszintű bizalmi helyszíni Active Directory.

### <a name="forest-trust-requirements"></a>Erdőszintű bizalmi kapcsolatokra vonatkozó követelmények

Az erdőszintű bizalmi kapcsolat létrehozása előtt ellenőriznie kell, hogy a megfelelő DNS-infrastruktúrával rendelkezik-e. Az erdőszintű bizalmi kapcsolatok csak akkor hozhatók létre, ha az alábbi DNS-konfigurációk egyike érhető el:

* Az egyetlen gyökér DNS-kiszolgáló mindkét erdő DNS-névterének gyökér DNS-kiszolgálója – a gyökérzóna minden DNS-névtérre delegálást tartalmaz, és az összes DNS-kiszolgáló gyökértippje tartalmazza a gyökér DNS-kiszolgálót.
* Ahol nincs megosztott gyökér DNS-kiszolgáló, és az egyes erdődns-névtér gyökér DNS-kiszolgálói minden DNS-névtérhez dns feltételes továbbítókat használnak a másik névtérben lévő nevek lekérdezéseinek irányításához.

    > [!IMPORTANT]
    > Az Azure AD tartományi szolgáltatások erőforráserdőkell használniezt a DNS-konfigurációt. Az erőforráserdő DNS-névterétől eltérő DNS-névtér üzemeltetése nem az Azure AD tartományi szolgáltatások szolgáltatása. Feltételes továbbítók a megfelelő konfigurációt.

* Ahol nincs megosztott gyökér DNS-kiszolgáló, és az egyes erdődns-névtér gyökér DNS-kiszolgálói dns-zónákat használnak, amelyek minden DNS-névtérben úgy vannak konfigurálva, hogy a másik névtérben lévő nevek lekérdezéseit továbbítsa.

Erdőszintű bizalmi kapcsolat létrehozásához a Tartománygazdák csoport (az erdő gyökértartományában) vagy a Vállalati rendszergazdák csoport tagjának kell lennie az Active Directoryban. Minden bizalmi kapcsolathoz olyan jelszó van rendelve, amelyet mindkét erdő rendszergazdáinak ismerniük kell. A vállalati rendszergazdák mindkét erdőben lévő tagjai egyszerre hozhatnak létre bizalmi kapcsolatokat mindkét erdőben, és ebben a forgatókönyvben a rendszer automatikusan létrehoz és ír egy titkosításilag véletlenszerű jelszót mindkét erdőhöz.

Az Azure AD tartományi szolgáltatások kimenő erdőszintű bizalmi kapcsolata az Azure Portalon jön létre. A bizalmi kapcsolatot nem hozza létre manuálisan magával a felügyelt tartománnyal. A bejövő erdőszintű bizalmi kapcsolatot a helyszíni Active Directoryban korábban megjegyzett jogosultságokkal rendelkező felhasználónak kell konfigurálnia.

## <a name="trust-processes-and-interactions"></a>Bizalmi folyamatok és interakciók

Számos tartományok közötti és erdők közötti tranzakció függ a tartomány- vagy erdőszintű bizalmi kapcsolatoktól a különböző feladatok elvégzéséhez. Ez a szakasz azokat a folyamatokat és interakciókat ismerteti, amelyek az erőforrások megbízhatósági kapcsolatok között való elérésekor és a hitelesítési átirányítások kiértékelése során fordulnak elő.

### <a name="overview-of-authentication-referral-processing"></a>A hitelesítési átirányítási feldolgozás áttekintése

Ha egy hitelesítési kérelmet tartományra hivatkoznak, a tartomány tartományvezérlőjének meg kell határoznia, hogy létezik-e megbízhatósági kapcsolat azzal a tartománnyal, amelyről a kérelem érkezett. A megbízhatósági kapcsolat irányát és azt, hogy a bizalmi kapcsolat tranzitív vagy nem tranzitív-e, szintén meg kell határozni, mielőtt hitelesíti a felhasználót a tartomány erőforrásainak eléréséhez. A megbízható tartományok közötti hitelesítési folyamat a használt hitelesítési protokolltól függően változik. A Kerberos V5 és NTLM protokollok eltérőmódon dolgozzák fel a tartományhitelesítésre vonatkozó átirányításokat

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 átirányítási feldolgozás

A Kerberos V5 hitelesítési protokoll az ügyfél hitelesítési és engedélyezési információihoz a tartományvezérlők hálózati bejelentkezési szolgáltatásától függ. A Kerberos protokoll egy online kulcselosztó központhoz (KDC) és a munkamenetjegyek Active Directory-fióktárolójához csatlakozik.

A Kerberos protokoll megbízhatósági kapcsolatokat is használ a több birodalombeli jegymegadási szolgáltatásokhoz (TGS) és a jogosultsági attribútumtanúsítványok (PAC-k) érvényesítéséhez egy biztonságos csatornán keresztül. A Kerberos protokoll csak nem Windows márkájú Kerberos-rendszerekkel, például MIT Kerberos-birodalommal hajt végre többbirodalom-hitelesítést, és nem kell együttműködnie a Hálózati bejelentkezés szolgáltatással.

Ha az ügyfél a Kerberos V5-öt használja hitelesítésre, a fióktartományában lévő tartományvezérlőtől jegyet kér a céltartomány kiszolgálójára. A Kerberos KDC megbízható közvetítőként működik az ügyfél és a kiszolgáló között, és olyan munkamenetkulcsot biztosít, amely lehetővé teszi a két fél számára egymás hitelesítését. Ha a céltartomány eltér az aktuális tartománytól, a kulcsszolgáltató logikai eljárást követ annak megállapítására, hogy a hitelesítési kérelem hivatkozható-e:

1. Az aktuális tartományt közvetlenül a kért kiszolgáló tartománya megbízhatónak kezeli?
    * Ha igen, küldjön az ügyfélnek egy átirányítást a kért tartományra.
    * Ha nem, folytassa a következő lépéssel.

2. Van tranzitív bizalmi kapcsolat az aktuális tartomány és a megbízhatósági útvonal következő tartománya között?
    * Ha igen, küldjön az ügyfélnek egy átirányítást a megbízhatósági útvonal következő tartományára.
    * Ha nem, küldjön az ügyfélnek egy bejelentkezési megtagadott üzenetet.

### <a name="ntlm-referral-processing"></a>NTLM átirányítási feldolgozás

Az NTLM hitelesítési protokoll az ügyfél hitelesítési és engedélyezési információihoz a tartományvezérlők hálózati bejelentkezési szolgáltatásától függ. Ez a protokoll hitelesíti azokat az ügyfeleket, amelyek nem használnak Kerberos-hitelesítést. Az NTLM bizalmi kapcsolatokat használ a hitelesítési kérelmek tartományok közötti fogadásához.

Ha az ügyfél NTLM-et használ a hitelesítéshez, a kezdeti hitelesítési kérelem közvetlenül az ügyféltől a céltartomány erőforrás-kiszolgálójáig kerül. Ez a kiszolgáló olyan kihívást hoz létre, amelyre az ügyfél válaszol. A kiszolgáló ezután elküldi a felhasználó válaszát a számítógépfiók-tartománytartományvezérlőnek. Ez a tartományvezérlő összeveti a felhasználói fiókot a biztonsági fiókok adatbázisával.

Ha a fiók nem létezik az adatbázisban, a tartományvezérlő a következő logikával határozza meg, hogy végrehajtsa-e az átadó hitelesítést, továbbítsa-e a kérelmet, vagy megtagadja-e a kérelmet:

1. Az aktuális tartomány közvetlen megbízhatósági kapcsolatban áll a felhasználó tartományával?
    * Ha igen, a tartományvezérlő elküldi az ügyfél hitelesítő adatait a felhasználó tartományának tartományvezérlőjének áthaladási hitelesítésre.
    * Ha nem, folytassa a következő lépéssel.

2. Az aktuális tartomány tranzitív megbízhatósági kapcsolatban áll a felhasználó tartományával?
    * Ha igen, adja át a hitelesítési kérelmet a megbízhatósági útvonal következő tartományának. Ez a tartományvezérlő megismétli a folyamatot, ha a felhasználó hitelesítő adatait a saját biztonsági fiókok adatbázisához ellenőrzi.
    * Ha nem, küldjön az ügyfélnek egy bejelentkezési megtagadott üzenetet.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>A hitelesítési kérelmek Kerberos-alapú feldolgozása erdőszintű bizalmi kapcsolatokon keresztül

Ha két erdőt köt össze erdőszintű bizalmi kapcsolat, a Kerberos V5 vagy NTLM protokollok használatával végrehajtott hitelesítési kérelmek et az erdők között lehet átirányítani, hogy mindkét erdő erőforrásaihoz hozzáférést biztosítsanak.

Az erdőszintű bizalmi kapcsolatok első létrehozásakor minden erdő összegyűjti a partnererdő összes megbízható névterét, és az adatokat [egy megbízható tartományobjektumban](#trusted-domain-object)tárolja. A megbízható névterek közé tartoznak a tartományfanevek, az egyszerű felhasználónév (UPN) utótagok, az egyszerű szolgáltatásnév (SPN) utótagok és a másik erdőben használt biztonsági azonosító (SID) névterek. A TDO-objektumok replikálódnak a globális katalógusba.

Mielőtt a hitelesítési protokollok követhetik az erdő megbízhatósági útvonalát, az erőforrásszámítógép egyszerű szolgáltatásnevét (SPN) fel kell oldani a másik erdő egy helyére. Az spn a következők egyike lehet:

* Egy állomás DNS-neve.
* Egy tartomány DNS-neve.
* A szolgáltatás csatlakozási pontobjektumának megkülönböztető neve.

Amikor egy erdő munkaállomása megpróbál hozzáférni egy másik erdő erőforrásszámítógépének adataihoz, a Kerberos hitelesítési folyamat kapcsolatba lép a tartományvezérlővel, hogy az erőforrásszámítógép spn-jéhez jusson. Miután a tartományvezérlő lekérdezi a globális katalógust, és megállapítja, hogy az spn nem ugyanabban az erdőben van, mint a tartományvezérlő, a tartományvezérlő visszaküldi a szülőtartományát a munkaállomásra. Ezen a ponton a munkaállomás lekérdezi a szolgáltatásjegy szülőtartományát, és továbbra is követi az átirányítási láncot, amíg el nem éri azt a tartományt, ahol az erőforrás található.

Az alábbi ábra és lépések részletesen ismertetik a Kerberos hitelesítési folyamatot, amelyet akkor alkalmaznak, amikor a Windows rendszert futtató számítógépek egy másik erdőben található számítógépről próbálnak hozzáférni az erőforrásokhoz.

![A Kerberos-folyamat diagramja erdőszintű bizalmi kapcsolaton keresztül](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *A User1* a europe.tailspintoys.com tartományból származó hitelesítő adatokkal jelentkezik be a *Workstation1-be.* *europe.tailspintoys.com* A felhasználó ezután megpróbál hozzáférni egy megosztott erőforráshoz a *usa.wingtiptoys.com* erdőben található *FileServer1-en.*

2. *A Workstation1* kapcsolatba lép a Kerberos KDC-vel a tartományában lévő *tartományvezérlőn, a ChildDC1*- és szolgáltatási jegyet kér a *FileServer1* spn-hez.

3. *A ChildDC1* nem találja az spn-t a tartományi adatbázisában, és lekérdezi a globális katalógust, hogy megtudja, a *tailspintoys.com* erdőben található tartományok tartalmazzák-e ezt az spn-t. Mivel a globális katalógus a saját erdőjére korlátozódik, az spn nem található.

    A globális katalógus ezután ellenőrzi az adatbázisban az erdővel létrehozott erdőszintű bizalmi kapcsolatokra vonatkozó információkat. Ha megtalálható, összehasonlítja az erdő megbízható tartományobjektumban (TDO) felsorolt névutótagokat a cél spn utótagjával, hogy egyezést találjon. Ha egyezést talál, a globális katalógus útválasztási emlékeztetőt ad vissza a *ChildDC1-nek.*

    Az útválasztási tanácsok segítenek a hitelesítési kérelmeknek a célerdő felé való irányításában. A tippek csak akkor használatosak, ha az összes hagyományos hitelesítési csatorna, például a helyi tartományvezérlő, majd a globális katalógus nem találja az spn-t.

4. *A ChildDC1* visszaküldi a szülőtartományát a *Workstation1-nek.*

5. *A Workstation1* kapcsolatba lép a *ForestRootDC1* (szülőtartománya) tartományvezérlőjével, hogy a *wingtiptoys.com* erdő erdőgyökér-tartományában tartományvezérlőre *(ForestRootDC2)* hivatkozik.

6. *Workstation1* kapcsolatba *ForestRootDC2* a *wingtiptoys.com* erdőben egy szolgáltatási jegyet a kért szolgáltatás.

7. *A ForestRootDC2* felveszi a kapcsolatot a globális katalógussal, hogy megtalálja az spn-t, a globális katalógus pedig megtalálja az spn egyezését, és visszaküldi a *ForestRootDC2 rendszernek.*

8. *A ForestRootDC2* ezután *usa.wingtiptoys.com* visszaküldi az usa.wingtiptoys.com a *Workstation1-nek.*

9. *A Workstation1* kapcsolatba lép a KDC-vel a *ChildDC2-n,* és egyezteti a jegyet a *User1* számára, hogy hozzáférjen a *FileServer1*rendszerhez.

10. Miután *a Workstation1* rendelkezik egy szolgáltatásjegyszel, elküldi a szolgáltatásjegyet a *FileServer1-nek,* amely beolvassa *a User1*biztonsági hitelesítő adatait, és ennek megfelelően létrehoz egy hozzáférési jogkivonatot.

## <a name="trusted-domain-object"></a>Megbízható tartományobjektum

A szervezeten belüli minden tartomány- vagy erdőszintű bizalmi kapcsolatot egy megbízható tartományobjektum (TDO) képvisel, amely a *tartományrendszertárolóban* van tárolva.

### <a name="tdo-contents"></a>TDO-tartalom

A TDO-ban található információk attól függően változnak, hogy a TDO-t tartomány- vagy erdőszintű bizalmi kapcsolat hozta-e létre.

Tartománymegbízhatósági kapcsolat létrehozásakor a TDO-ban megjelennek az olyan attribútumok, mint a DNS-tartománynév, a tartomány sid-je, a megbízhatóság típusa, a megbízhatóság tranzitivitása és a kölcsönös tartománynév. Az erdőszintű bizalmi tdo-k további attribútumokat tárolnak a partnererdő összes megbízható névterének azonosításához. Ezek közé az attribútumok közé tartoznak a tartományfanevek, az egyszerű felhasználónév (UPN) utótagok, az egyszerű szolgáltatásnév (SPN) utótagok és a biztonsági azonosító (SID) névterei.

Mivel a bizalmi kapcsolatok tdo-kként tárolódnak az Active Directoryban, az erdő összes tartománya ismeri az erdőben működő megbízhatósági kapcsolatokat. Hasonlóképpen, ha két vagy több erdő tanyán keresztül csatlakozik egymáshoz, az egyes erdők erdőgyökér-tartományai ismerik azokat a megbízhatósági kapcsolatokat, amelyek a megbízható erdők összes tartományában érvényben vannak.

### <a name="tdo-password-changes"></a>A TDO jelszó módosításai

A megbízhatósági kapcsolat mindkét tartománya megosztja a jelszót, amely az Active Directory TDO objektumában található. A fiókkarbantartási folyamat részeként a megbízó tartományvezérlő 30 naponta módosítja a TDO-ban tárolt jelszót. Mivel az összes kétirányú bizalmi kapcsolat valójában két egyirányú bizalmi kapcsolat, amelyek ellentétes irányba haladnak, a folyamat kétszer fordul elő a kétirányú bizalmi kapcsolatok esetében.

A bizalmi kapcsolatnak van egy megbízható és megbízható oldala. A megbízható oldalon bármely írható tartományvezérlő használható a folyamathoz. A megbízó oldalon az elsődleges tartományvezérlő emulátora végrehajtja a jelszómódosítást.

A jelszó módosításához a tartományvezérlők a következő eljárást hajtják végre:

1. A megbízó tartomány elsődleges tartományvezérlő-emulátora új jelszót hoz létre. A megbízható tartomány tartományvezérlője soha nem kezdeményezi a jelszómódosítást. Mindig a megbízó tartomány PDC emulátora kezdeményezi.

2. A megbízhatósági tartomány elsődleges tartományvezérlő-emulátora a TDO objektum *OldPassword* mezőjét az aktuális NewPassword mezőre *állítja.*

3. A megbízó tartomány elsődleges tartományvezérlő-emulátora a TDO objektum *NewPassword* mezőjét az új jelszóra állítja be. Az előző jelszó másolatának megtartása lehetővé teszi a régi jelszó visszaállítását, ha a megbízható tartomány tartományvezérlője nem kapja meg a módosítást, vagy ha a módosítás nem replikálódik az új megbízhatósági jelszót használó kérelem előtt.

4. A megbízó tartomány elsődleges tartományvezérlő-emulátora távoli hívást kezdeményez a megbízható tartomány tartományvezérlőjére, és arra kéri, hogy állítsa be a megbízhatósági fiók jelszavát az új jelszóra.

5. A megbízható tartomány tartományvezérlője a megbízhatósági jelszót az új jelszóra módosítja.

6. A megbízhatósági kapcsolat mindkét oldalán a rendszer replikálja a frissítéseket a tartomány többi tartományvezérlőjére. A megbízó tartományban a módosítás a megbízható tartományobjektum sürgős replikációját indítja el.

A jelszó most mindkét tartományvezérlőn módosul. A normál replikáció elosztja a TDO-objektumokat a tartomány többi tartományvezérlőjének. A megbízó tartomány tartományvezérlője azonban módosíthatja a jelszót anélkül, hogy a megbízható tartományban lévő tartományvezérlőt sikeresen frissítené. Ez a forgatókönyv azért fordulhat elő, mert a jelszómódosítás feldolgozásához szükséges biztonságos csatorna nem jött létre. Az is lehetséges, hogy a megbízható tartomány tartományvezérlője a folyamat egy bizonyos pontján nem érhető el, és nem kapja meg a frissített jelszót.

Az olyan helyzetek kezeléséhez, amikor a jelszómódosítás nem történt sikeresen kommunikálva, a megbízó tartomány tartományvezérlője soha nem módosítja az új jelszót, hacsak nem hitelesítette sikeresen (biztonságos csatornát állított be) az új jelszó használatával. Ez a viselkedés az oka annak, hogy mind a régi, mind az új jelszavak a megbízó tartomány TDO objektumában vannak.

A jelszómódosítása nem véglegesítve, amíg a jelszó használatával történő hitelesítés sikeres nem lesz. A régi, tárolt jelszó használható a biztonságos csatornán keresztül, amíg a megbízható tartomány tartományvezérlője meg nem kapja az új jelszót, így lehetővé teszi a folyamatos szolgáltatást.

Ha az új jelszóval történő hitelesítés sikertelen, mert a jelszó érvénytelen, a megbízó tartományvezérlő a régi jelszóval próbál hitelesítést használni. Ha sikeresen hitelesíti magát a régi jelszóval, 15 percen belül folytatja a jelszómódosítási folyamatot.

A megbízhatósági jelszó frissítéseinek 30 napon belül replikálódniuk kell a bizalmi kapcsolat mindkét oldalának tartományvezérlőire. Ha a megbízhatósági jelszót 30 nap elteltével módosítják, és a tartományvezérlő csak az N-2 jelszót használja, akkor nem használhatja a megbízható oldalról a bizalmi kapcsolatot, és nem tud biztonságos csatornát létrehozni a megbízható oldalon.

## <a name="network-ports-used-by-trusts"></a>Bizalmi kapcsolatok által használt hálózati portok

Mivel a bizalmi kapcsolatokat különböző hálózati határokon keresztül kell telepíteni, előfordulhat, hogy egy vagy több tűzfalra kell átnyúlniuk. Ebben az esetben a tűzfalon keresztüli megbízható forgalmat alagutat áshat, vagy megnyithat a tűzfal adott portjait, hogy a forgalom áthaladjon.

> [!IMPORTANT]
> Az Active Directory tartományi szolgáltatások nem támogatják az Active Directory RPC-forgalom adott portokra való korlátozását.

Olvassa el a Microsoft támogatási cikkének **Windows Server 2008 és újabb verziók című** szakaszát: A tűzfal [konfigurálása az Active Directory-tartományokhoz és a bizalmi kapcsolatokhoz](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) az erdőszintű megbízhatósági kapcsolatokhoz szükséges portokról.

## <a name="supporting-services-and-tools"></a>Támogató szolgáltatások és eszközök

A megbízhatósági kapcsolatok és a hitelesítés támogatása érdekében néhány további szolgáltatást és felügyeleti eszközt használ.

### <a name="net-logon"></a>Hálózati bejelentkezés

A Hálózati bejelentkezés szolgáltatás biztonságos csatornát tart fenn egy Windows-alapú számítógépről egy tartományvezérlőre. A következő megbízhatósági folyamatokban is használatos:

* Megbízhatóság beállítása és kezelése – A Net Logon segít a megbízhatósági jelszavak fenntartásában, a megbízhatósági információk összegyűjtésében és a megbízhatósági kapcsolatok ellenőrzésében az LSA-folyamattal és a TDO-val való interakcióval.

    Erdőszintű bizalmi kapcsolatok esetén a megbízhatósági adatok közé tartozik az Erdei megbízhatósági információ (*FTInfo*) rekord, amely tartalmazza a megbízható erdő által kezelendő névterek készletét, amely egy olyan mezővel van eltüntetve, amely jelzi, hogy a megbízó erdő minden jogcímet megbízhatónak tart-e.

* Hitelesítés – A felhasználói hitelesítő adatokat egy védett csatornán keresztül egy tartományvezérlőnek adja át, és visszaadja a tartomány biztonsági azonosítóit és a felhasználó felhasználói jogait.

* Tartományvezérlő helye – Segít a tartományban vagy tartományokban lévő tartományvezérlők megkeresésében vagy megkeresésében.

* Áthaladási ellenőrzés – A más tartományok felhasználóinak hitelesítő adatait a Net Logon dolgozza fel. Ha egy megbízható tartománynak ellenőriznie kell egy felhasználó identitását, a felhasználó hitelesítő adatait a hálózati bejelentkezésen keresztül továbbítja a megbízható tartománynak ellenőrzésre.

* Jogosultsági attribútumtanúsítvány (PAC) ellenőrzése – Ha egy Kerberos protokollt a hitelesítéshez használó kiszolgálónak ellenőriznie kell a PAC-ot egy szolgáltatásjegyben, a PAC-ot a biztonságos csatornán keresztül elküldi a tartományvezérlőnek ellenőrzésre.

### <a name="local-security-authority"></a>Helyi biztonsági szervezet

A helyi biztonsági hatóság (LSA) egy védett alrendszer, amely a rendszer helyi biztonságának minden aspektusáról információt tart fenn. Az LSA együttesen helyi biztonsági házirendként ismert, különböző szolgáltatásokat nyújt a nevek és azonosítók közötti fordításhoz.

Az LSA biztonsági alrendszer kernel és felhasználói módban egyaránt nyújt szolgáltatásokat az objektumokhoz való hozzáférés érvényesítéséhez, a felhasználói jogosultságok ellenőrzéséhez és a naplózási üzenetek létrehozásához. Az LSA felelős a megbízható vagy nem megbízható tartományokban lévő szolgáltatások által bemutatott munkamenetjegyek érvényességének ellenőrzéséért.

### <a name="management-tools"></a>Felügyeleti eszközök

A rendszergazdák az *Active Directory – tartományok és megbízhatósági kapcsolatok*, a *Netdom* és *az Nltest* címtárban lévő, létrehozási, eltávolítási vagy módosítási kapcsolatok at használhatják.

* *Az Active Directory – tartományok és megbízhatósági kapcsolatok* a Microsoft Management Console (MMC) a tartományi megbízhatósági kapcsolatok, a tartomány- és erdőműködési szintek, valamint a felhasználó egyszerű névutótagjainak felügyeletére szolgál.
* A *Netdom* és *az Nltest* parancssori eszközök segítségével megbízható kapcsolatokat kereshet, jeleníthet meg, hozhat létre és kezelhet. Ezek az eszközök közvetlenül kommunikálnak a tartományvezérlő LSA-jogosultságával.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az erőforrás-erdőkről, olvassa el a [Hogyan működnek az erdőszintű bizalmi kapcsolatok az Azure AD DS-ben?][concepts-trust]

Az Azure AD DS felügyelt tartomány erőforráserdővel való létrehozásának első [lépései az Azure AD DS felügyelt tartományának létrehozása és konfigurálása][tutorial-create-advanced]című témakörben található. Ezután [létrehozhat egy kimenő erdőszintű bizalmi kapcsolatot egy helyszíni tartományban (előzetes verzió)][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
