---
title: SWIFT CSP – CSCF v2020 terv minta vezérlők
description: A SWIFT CSP-CSCF v2020 tervezetének vezérlése. Mindegyik vezérlő egy vagy több olyan Azure-szabályzatra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 09/24/2019
ms.topic: sample
ms.openlocfilehash: 1826e4820f06fc3a858ce6cd5f8906baaa9808cc
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546514"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>A SWIFT CSP-CSCF v2020 Blueprint-minta leképezésének vezérlése

A következő cikk azt ismerteti, hogyan történik az Azure-tervezetek SWIFT CSP-CSCF v2020 tervrajza a SWIFT CSP-CSCF v2020 vezérlőkhöz. További információ a vezérlőkről: [Swift CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

A következő leképezések a **Swift CSP-CSCF v2020** vezérlőkhöz tartoznak. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a **\[előnézet\]: audit Swift CSP-CSCF v2020 Controls and a speciális virtuálisgép-bővítmények üzembe helyezése a naplózási követelmények** beépített házirend-kezdeményezésének támogatásához.

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1,2 és 5,1-fiók kezelése

Ez a terv segít áttekinteni azokat a fiókokat, amelyek esetleg nem felelnek meg a szervezete fiókjának felügyeleti követelményeinek. Ez a terv olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek az előfizetésre és az elavult fiókokra vonatkozó olvasási, írási és tulajdonosi engedélyekkel auditálják a külső fiókokat. A szabályzatok által auditált fiókok áttekintésével megteheti a megfelelő lépéseket a fiókok kezelésével kapcsolatos követelmények teljesítése érdekében.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2,6, 5,1, 6,4 és 6.5 A fiókok kezelése | Szerepköralapú sémák

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat is HOZZÁRENDEL az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését. A terv emellett egy Azure Policy definíciót rendel hozzá az egyéni RBAC-szabályok használatának naplózásához. Az egyéni RBAC-szabályok megvalósításának megismerése segíthet a szükséges és a megfelelő implementáció ellenőrzésében, mivel az egyéni RBAC-szabályok hibásak.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Egyéni RBAC-szabályok használatának naplózása
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9 a fiókok kezelése | Fiókok figyelése/atipikus használat

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A virtuális gépek eléréséhez szükséges JIT-kérelmeket a rendszer naplózza a tevékenység naplójában, amely lehetővé teszi az atipikus használat figyelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="13-51-and-64-separation-of-duties"></a>1,3, 5,1 és 6,4 vámtételek elkülönítése

Csak egy Azure-előfizetéshez tartozó tulajdonos nem engedélyez felügyeleti redundanciát. Ezzel szemben az Azure-előfizetések tulajdonosai is növelhetik a biztonsági réseket egy sérült tulajdonosi fiókon keresztül. Ez a tervezet segítséget nyújt a megfelelő számú Azure-előfizetési tulajdonos fenntartásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek az Azure-előfizetések tulajdonosainak számát naplózzák. A terv emellett Azure Policy definíciókat is hozzárendel, amelyek segítenek a rendszergazdák csoport tagságának vezérlésében a Windows rendszerű virtuális gépeken. Az előfizetés tulajdonosa és a virtuális gép rendszergazdai engedélyeinek kezelése segíthet a feladatok megfelelő elkülönítésének megvalósításában.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1,3, 5,1 és 6,4 legalacsonyabb jogosultság | Felhasználói jogosultságok áttekintése

Az Azure [szerepköralapú hozzáférés-vezérlést](../../../../role-based-access-control/overview.md) (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a naplózási fiókokhoz, amelyeket érdemes áttekinteni. A fiókokra vonatkozó mutatók áttekintésével meggyőződhet arról, hogy a minimális jogosultsági szintű vezérlők implementálva vannak.

- Az előfizetéshez legfeljebb 3 tulajdonost kell kijelölni
- A Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Telepítse a Windows rendszerű virtuális gépek naplózásának előfeltételeit, amelyekben a rendszergazdák csoport nem tartalmazza az összes megadott tagot.
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

## <a name="22-and-27-security-attributes"></a>2,2 és 2,7 biztonsági attribútumok

A Azure SQL Database speciális adatbiztonságának adatfelderítési és besorolási funkciója lehetővé teszi az adatbázisok bizalmas adatainak feltárását, osztályozását, címkézését és védelmét. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül. A speciális adatok biztonsága segítséget nyújt a szervezete megfelelő biztonsági attribútumaival kapcsolatos információk biztosításához. Ez a terv [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá a speciális adatbiztonság az SQL Serveren való használatának figyeléséhez és érvényesítéséhez. 

- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2,2, 2,7, 4,1 és 6,1 távoli elérés | Automatikus figyelés/vezérlés

Ez a terv segít a távelérés figyelésében és vezérlésében azáltal, hogy [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével figyeli, hogy a Azure app Service alkalmazás távoli hibakeresése ki van-e kapcsolva, valamint olyan házirend-definíciókat, amelyek a távoli hozzáférést lehetővé tevő linuxos virtuális gépek jelszavak nélküli fiókok kapcsolatai. Ez a terv egy Azure Policy-definíciót is hozzárendel, amely segít megfigyelni a nem korlátozott hozzáférést a Storage-fiókokhoz. Ezen mutatók monitorozásával biztosítható, hogy a távelérési módszerek megfeleljenek a biztonsági szabályzatnak.

- \[előzetes verzió\]: a Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik a távoli kapcsolatokat jelszavak nélküli fiókoktól
- \[előzetes verzió\]: telepítse az előfeltételeket a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózására
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás
- Távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1,3 és 6,4 a naplózási rekordok tartalma | Tervezett auditálási rekordok központosított kezelése

A Azure Monitor által gyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi konfigurációt és a felügyeletet. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2,2, 2,7 és 6,4 Válasz a naplózási feldolgozási hibákra

Ez a terv [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek figyelik a naplózási és eseménynaplózási konfigurációkat. Ezeknek a konfigurációknak a figyelése a rendszerhiba vagy a helytelen konfiguráció jelzését, valamint a javítási műveletek elvégzését is lehetővé teszi.

- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Diagnosztikai beállítás naplózása
- Naplózás üzembe helyezése az SQL Serveren

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1,3 és 6,4 audit felülvizsgálata, elemzése és jelentéskészítés | Központi áttekintés és elemzés

A Azure Monitor által összegyűjtött adatok naplózása egy Log Analytics munkaterületen történik, amely lehetővé teszi a központi jelentéskészítést és elemzést. Ez a terv segít biztosítani az események naplózását olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek az log Analytics Agent Azure-beli virtuális gépeken való üzembe helyezését naplózzák és érvényesítik.

- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre

## <a name="13-22-27-64-and-65a-audit-generation"></a>1,3, 2,2, 2,7, 6,4 és 6.5 A naplózási generáció

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével. Ezek a szabályzat-definíciók a Log Analytics ügynök Azure-beli virtuális gépeken való üzembe helyezését és az egyéb Azure-erőforrástípusok naplózási beállításainak konfigurálását is kikényszerítik. Ezek a szabályzat-definíciók a diagnosztikai naplók konfigurációját is naplózzák, hogy betekintést nyújtsanak az Azure-erőforrásokon belül végrehajtott műveletekre. Emellett a naplózás és a speciális adatbiztonság is konfigurálva van az SQL-kiszolgálókon.

- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre
- Diagnosztikai beállítás naplózása
- Az SQL Server szintű naplózási beállítások naplózása
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése

## <a name="11-least-functionality--prevent-program-execution"></a>1,1 legkisebb funkcionalitás | Program végrehajtásának megakadályozása

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem kényszerítési módban futtatható, amely tiltja a nem jóváhagyott alkalmazások futtatását. Ez a terv egy Azure Policy-definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1,1 legkisebb funkcionalitás | Engedélyezett szoftver/engedélyezési lista

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem lehetővé teszi a jóváhagyott alkalmazások listáját a virtuális gépekhez. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="11-user-installed-software"></a>1,1 felhasználó által telepített szoftver

A Azure Security Center adaptív alkalmazás-vezérlése intelligens, automatizált, teljes körű alkalmazás-engedélyezési megoldás, amely képes blokkolni vagy megakadályozni bizonyos szoftverek futtatását a virtuális gépeken. Az alkalmazás-vezérlőelem segítségével érvényesítheti és figyelheti a szoftverkorlátozó házirendek megfelelőségét. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít a virtuális gépek figyelésében, amelyekben az alkalmazás-engedélyezési lista ajánlott, de még nincs konfigurálva.

- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4,2 azonosítás és hitelesítés (szervezeti felhasználók) | Hálózati hozzáférés a Kiemelt jogosultságú fiókokhoz

Ez a terv segít a privilegizált hozzáférés korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével olyan fiókokat naplózni, amelyek tulajdonosi és/vagy írási engedélyekkel nem rendelkeznek a többtényezős hitelesítés engedélyezésével. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-t engedélyezni kell az előfizetés írási engedéllyel rendelkező fiókjain

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4,2 azonosítás és hitelesítés (szervezeti felhasználók) | Nem Kiemelt fiókok hálózati hozzáférése

Ez a terv segítséget nyújt a hozzáférés korlátozásához és vezérléséhez, ha egy [Azure Policy](../../../policy/overview.md) definíció hozzárendelésével naplózza a fiókokat olyan olvasási engedélyekkel, amelyeken nincs engedélyezve a többtényezős hitelesítés. A többtényezős hitelesítés révén a fiókok biztonságban maradhatnak, még akkor is, ha az egyik hitelesítő adat biztonsága sérül. A többtényezős hitelesítés engedélyezése nélküli fiókok figyelésével azonosíthatja azokat a fiókokat, amelyek nagyobb valószínűséggel veszélyeztethetik.

- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon

## <a name="23-and-41-authenticator-management"></a>2,3 és 4,1 hitelesítő kezelése

Ez a terv azokat a [Azure Policy](../../../policy/overview.md) definíciókat rendeli hozzá, amelyek a Linux rendszerű virtuális gépeket a jelszavak nélküli fiókok távoli kapcsolatainak engedélyezésére és/vagy helytelen engedélyekkel rendelkeznek a passwd fájlban. Ez a terv a Windows rendszerű virtuális gépekhez tartozó jelszó-titkosítási típus konfigurációját naplózó szabályzat-definíciókat is hozzárendeli. Ezen mutatók monitorozásával biztosíthatja, hogy a rendszerhitelesítő megfeleljenek a szervezete azonosítási és hitelesítési házirendjének.

- \[előzetes verzió\]: olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[előzetes verzió\]: követelmények telepítése olyan Linux rendszerű virtuális gépek naplózására, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva
- \[előzetes verzió\]: olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyekhez jelszó nélküli fiók tartozik
- \[előzetes verzió\]: követelmények telepítése a jelszavak nélküli fiókokkal rendelkező linuxos virtuális gépek naplózására
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem tárolják a jelszavakat visszafejthető titkosítással
- \[előzetes verzió\]: követelmények telepítése a jelszavakat nem tároló Windows-alapú virtuális gépek naplózásához a visszafejthető titkosítással

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>2,3 és 4,1 hitelesítő kezelése | Jelszó-alapú hitelesítés

Ez a terv segítséget nyújt az erős jelszavak betartatásához olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem igénylő Windows rendszerű virtuális gépeket naplózzák. A virtuális gépeknek a jelszó erőssége megsértésével kapcsolatos ismerete segít megtenni a szükséges javítási műveleteket, hogy az összes virtuálisgép-felhasználói fiók jelszava megfeleljen a szervezete jelszavas házirendjének.

- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát.
- \[előzetes verzió\]: olyan Windowsos virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- \[előzetes verzió\]: olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[előzetes verzió\]: olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre.
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem tárolják a jelszavakat visszafejthető titkosítással
- \[előzetes verzió\]: telepítse az előfeltételeket a Windows rendszerű virtuális gépek naplózására, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát.
- \[előzetes verzió\]: telepítse az előfeltételeket a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózására 70 nap
- \[előzetes verzió\]: telepítse az előfeltételeket olyan Windows rendszerű virtuális gépek naplózására, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[előzetes verzió\]: a jelszó bonyolultsága beállítással nem rendelkező Windows rendszerű virtuális gépek naplózásához szükséges előfeltételek központi telepítése
- \[előzetes verzió\]: telepítse az előfeltételeket a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre.
- \[előzetes verzió\]: a jelszavakat nem tároló Windows rendszerű virtuális gépek naplózásának előfeltételei a visszafejthető titkosítás használatával

## <a name="22-and-27-vulnerability-scanning"></a>2,2 és 2,7 sebezhetőségi vizsgálat

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével, amelyek figyelik az operációs rendszer biztonsági réseit, az SQL biztonsági réseket és a virtuális gépek Azure Security Center- A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. A terv emellett olyan szabályzat-definíciókat is hozzárendel, amelyek az SQL-kiszolgálókon a speciális adatbiztonságot auditálják és érvényesítik. A speciális adatbiztonság biztonsági rések felmérése és a komplex veszélyforrások elleni védelem lehetővé teszi, hogy jobban megértse a telepített erőforrások sebezhetőségeit.

- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell 
- A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell

## <a name="13-denial-of-service-protection"></a>1,3 a szolgáltatás védelmének megtagadása

Az Azure Distributed szolgáltatásmegtagadás (DDoS) standard csomagja további funkciókat és kockázatcsökkentő képességeket biztosít az alapszintű szolgáltatási szinten. Ezek a további funkciók Azure Monitor integrációt és a támadás utáni kockázatcsökkentő jelentések áttekintését is lehetővé teszi. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely azt naplózza, hogy a DDoS standard szint engedélyezve van-e. A szolgáltatási rétegek közötti képességbeli különbség megismerése segíthet kiválasztani a legjobb megoldást az Azure-környezethez tartozó szolgáltatások megtagadásának kezeléséhez.

- DDoS Protection a standardot engedélyezni kell

## <a name="11-and-61-boundary-protection"></a>1,1 és 6,1 határ védelme

Ez a terv segít a Rendszerhatár kezelésében és szabályozásában azáltal, hogy hozzárendel egy [Azure Policy](../../../policy/overview.md) definíciót, amely figyeli a hálózati biztonsági csoportra vonatkozó ajánlásokat a Azure Security Centerban. Azure Security Center elemzi az internetre irányuló virtuális gépek forgalmi mintáit, és hálózati biztonsági csoportokra vonatkozó ajánlásokat biztosít a potenciális támadási felület csökkentése érdekében.
Emellett a terv a nem védett végpontokat, alkalmazásokat és tárolási fiókokat figyelő szabályzat-definíciókat is hozzárendeli. A tűzfal által nem védett végpontok és alkalmazások, valamint a korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz.

- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz

## <a name="29a-boundary-protection--access-points"></a>2.9 a határ védelme | Hozzáférési pontok

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gép hozzáférése segít korlátozni az Azure-ban lévő erőforrások külső kapcsolatainak számát. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9 a határ védelme | Külső távközlési szolgáltatások

Az igény szerinti (JIT) virtuálisgép-hozzáférés lezárja az Azure-beli virtuális gépek felé irányuló bejövő adatforgalmat, így csökkentve a támadásokkal szembeni kitettséget, és így könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges. A JIT virtuális gépekhez való hozzáférés a hozzáférési kérelmek és a jóváhagyási folyamatok megkönnyítésével segíti a forgalomra vonatkozó kivételek kezelését. Ez a terv egy [Azure Policy](../../../policy/overview.md) -definíciót rendel hozzá, amely segít az igény szerinti hozzáférést támogató virtuális gépek figyelésében, de még nincsenek konfigurálva.

- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2,1, 2,4, 2.4 a, 2.5 A, és 2,6 adatátvitel titkossága és integritása | Titkosítási vagy alternatív fizikai védelem

Ez a terv segít a továbbított adatok bizalmas és integritásának védelmében [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, amelyek segítségével figyelheti a kommunikációs protokollok által megvalósított titkosítási mechanizmusokat. A kommunikáció megfelelő titkosítása segíthet a szervezet követelményeinek teljesítésében, illetve az információk jogosulatlan közzétételtől és módosítástól való védelmében.

- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához szükséges előfeltételek központi telepítése
- Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve
- A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell
- Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2,2, 2,3, 2,5, 4,1 és 2,7 a REST-adatok védelme | Titkosítási védelem

Ebből a tervből megtudhatja, hogyan kényszerítheti ki a szabályzatot a titkosítási vezérlők használatára, hogy olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendeljen hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát. Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzat-definíciók titkosítást igényelnek a Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; és naplózza a hiányzó titkosítást az SQL-adatbázisokon, a virtuális gépek lemezein és az Automation-fiók változóinak.

- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás

## <a name="13-22-and-27-flaw-remediation"></a>1,3, 2,2 és 2,7 hibás szervizelés

Ez a terv a hiányzó rendszerfrissítéseket, az operációs rendszer biztonsági réseit, az SQL-biztonsági réseket és a virtuális gépek Azure Security-beli biztonsági réseit figyelő [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével segíti az információs rendszerek hibáinak kezelését. Center. A Azure Security Center jelentéskészítési funkciókat biztosít, amelyekkel valós idejű betekintést nyerhet az üzembe helyezett Azure-erőforrások biztonsági állapotára. Ez a terv egy szabályzat-definíciót is hozzárendel, amely biztosítja a virtuálisgép-méretezési csoportok operációs rendszerének javítását.

- Az operációsrendszer-rendszerkép automatikus javításának megkövetelése Virtual Machine Scale Sets
- A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell
- A rendszerfrissítéseket telepíteni kell a virtuális gépekre
- A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell
- A virtuális gépek biztonsági konfigurációjában lévő biztonsági réseket szervizelni kell
- Az SQL-adatbázisok biztonsági réseit szervizelni kell

## <a name="61-malicious-code-protection"></a>6,1 rosszindulatú programkód védelme

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) definíciókat rendel hozzá, amelyek figyelik a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását, és kikényszerítik a Microsoftot antimalware megoldás a Windows rendszerű virtuális gépeken.

- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése
- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="61-malicious-code-protection--central-management"></a>6,1 rosszindulatú programkód védelme | Központi felügyelet

Ez a terv segít az Endpoint Protection kezelésében, beleértve a kártékony programkódok védelmét is, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek a Azure Security Center virtuális gépei hiányzó Endpoint Protection szolgáltatását figyelik. A Azure Security Center központosított felügyeleti és jelentéskészítési képességeket biztosít, amelyek lehetővé teszik, hogy valós idejű betekintést kapjon az üzembe helyezett Azure-erőforrások biztonsági állapotával.

- Az Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra kell telepíteni
- Hiányzó Endpoint Protection figyelése Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1,1, 1,3, 2,2, 2,7, 2,8 és 6,4 információs rendszer figyelése

Ez a terv a naplózás és az adatbiztonság Azure-erőforrásokon keresztüli naplózásával és érvényesítésével segíti a rendszerek figyelését. A szabályzatok a Log Analytics ügynök üzembe helyezését, valamint az SQL-adatbázisok, a Storage-fiókok és a hálózati erőforrások fokozott biztonsági beállításait naplózzák és érvényesítik. Ezek a képességek segítenek a rendellenes viselkedés és a támadási mutatók észlelésében, így elvégezheti a megfelelő lépéseket.

- \[előzetes verzió\]: naplózás Log Analytics ügynök üzembe helyezése – nem listázva a virtuálisgép-rendszerkép (OS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)
- \[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon
- Az SQL Server speciális biztonsági beállításaiban szerepelnie kell egy e-mail-címnek a biztonsági riasztások fogadásához
- A Azure Stream Analytics lévő diagnosztikai naplókat engedélyezni kell
- Speciális adatbiztonság üzembe helyezése SQL-kiszolgálókon
- Naplózás üzembe helyezése SQL-kiszolgálókon
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2,2 és 2,8 információs rendszer figyelése | Traffic/Covert kiszűrése elemzése

Az Azure Storage komplex veszélyforrások elleni védelme szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A védelmi riasztások közé tartoznak a rendellenes hozzáférési minták, a rendellenes kibontások/feltöltések és a gyanús tárolási tevékenységek. Ezek a mutatók segítséget nyújtanak a további kiszűrése észlelésében.

- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon

> [!NOTE]
> Az adott Azure Policy-definíciók rendelkezésre állása Azure Government és más nemzeti felhőkben is változhat.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a SWIFT CSP-CSCF v2020 tervezetének vezérlési leképezését, az alábbi cikkekben megismerheti a tervet, és bemutatja, hogyan helyezheti üzembe a mintát:

> [!div class="nextstepaction"]
> [Swift CSP-CSCF v2020 Blueprint – áttekintés](./index.md)
> [Swift CSP – CSCF v2020 Blueprint – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
