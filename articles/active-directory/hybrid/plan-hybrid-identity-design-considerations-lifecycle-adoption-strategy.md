---
title: Hibrid identitás tervezési - életciklus bevezetési stratégiája Azure |} A Microsoft Docs
description: Segít a hibrid identitáskezelési feladatokat minden életciklus fázisban rendelkezésre álló lehetőségek alapján határozza meg.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0842daa9ca8e0f64d0ca7d2610b6116c2bd5b8
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202825"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Hibrid identitás-életciklus bevezetési stratégiája határozza meg
Ebben a feladatban meg fogja definiálni, a hibrid identitáskezelési megoldás az üzleti szükségletek kielégítése céljából, amelyet a megadott identity management stratégiája [határozza meg a hibrid identitáskezelési feladatokat](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Annak meghatározásához, a hibrid identitáskezelési feladatokat a korábban ebben a lépésben bemutatott teljes körű identitás-életciklus megfelelően, akkor fontolja meg az egyes életciklus fázisokban elérhető lehetőségeket.

## <a name="access-management-and-provisioning"></a>Hozzáférés-kezelés és üzembe helyezés
Egy jó fiók hozzáférés-kezelési megoldása a szervezet segítségével nyomon pontosan ki férhet hozzá információkról a szervezeten belül.

Hozzáférés-vezérlés egy központosított, pont kiépítési rendszer kritikus fontosságú függvénye. Amellett, hogy bizalmas adatokat védő hozzáférés-vezérlés meglévő fiókokat, amelyeket elérhetővé jóvá nem hagyott engedélyeket vagy a rendszer már nem szükséges. Elavult fiókok, a kiépítési rendszer hivatkozások együtt fiók adatait a felhasználókat, akik a saját a fiókok mérvadó információkat szabályozására. Mérvadó felhasználói azonosító adatok, általában kezelik az adatbázisok és a könyvtárakat az emberi erőforrások.

Kifinomult informatikai nagyvállalatok számára fiókjainak tartalmazza a hitelesítésszolgáltatók meghatározó paraméterek több száz, és ezeket az adatokat a kiépítési rendszer által szabályozható. Új felhasználók az adatokkal, Ön által megadott a mérvadó forrás azonosíthatók. A hozzáférési kérés jóváhagyási funkció indítja el a folyamatot, amelyet jóváhagyása (vagy elutasítása) erőforrás-kiépítés őket.

| Életciklus-kezelési fázis | A helyszínen | Felhő | Hibrid |
| --- | --- | --- | --- |
| Fiókok kezelése és üzembe helyezését |Az Active Directory® tartományi szolgáltatások (AD DS) kiszolgálói szerepkör segítségével hozzon létre egy méretezhető, biztonságos és kezelhető infrastruktúrát, a felhasználó- és erőforrás-kezelést, és támogatást nyújt a címtárhasználatra képes alkalmazásokhoz, például a Microsoft® Exchange Server. <br><br> [Active Directory tartományi szolgáltatásokban, az Identity manager keresztül helyezheti üzembe a](https://technet.microsoft.com/library/ff686261.aspx) <br>[ Helyezheti üzembe a felhasználók az AD DS-ben](https://technet.microsoft.com/library/ff686263.aspx) <br><br> A rendszergazdák hozzáférés-vezérlés használatával felhasználó biztonsági okokból megosztott erőforrásokhoz való hozzáférés kezelésére. Az Active Directoryban, hozzáférés-vezérlés felügyelt, az objektumok szintjén által beállítás különböző szintű hozzáféréssel és engedélyekkel, objektumok, például a teljes hozzáférés, írás, Olvasás vagy nem férhető hozzá. Hozzáférés-vezérlés az Active Directory határozza meg a különböző felhasználók használhatják az Active Directory-objektumok. Alapértelmezés szerint az Active Directory-objektumokra vonatkozó engedélyek vannak beállítva a legbiztonságosabb beállítás. |Akkor hozzon létre egy fiókot a Microsoft felhőszolgáltatása hozzáférő felhasználók esetében. Felhasználói fiókok módosíthatja vagy törölheti őket, amikor szükség van rájuk már nem is. Alapértelmezés szerint felhasználók nem rendelkeznek rendszergazdai engedélyekkel, de igény szerint rendelheti azokat. <br><br> Azure Active Directoryban a legfontosabb funkcióit egyik képes kezelni az erőforrásokhoz való hozzáférést. Ezek az erőforrások képezhetik a címtár részét, például az objektumok kezelésére szolgáló engedélyek esetében, amelyeket a címtár szerepkörei biztosítanak, vagy lehetnek a címtáron kívüli erőforrások, például SaaS-alkalmazások, Azure-szolgáltatások, SharePoint-webhelyek vagy helyszíni erőforrások. <br><br> A központ az Azure Active Directory hozzáférési a felügyeleti megoldás, a biztonsági csoport. Az erőforrás tulajdonosa (vagy a címtár rendszergazdája) hozzárendelhet egy csoportot a tulajdonában lévő erőforrásokhoz adott hozzáférési jogosultság biztosításához. A csoport tagjait a rendszer a hozzáférést, és az erőforrás tulajdonosa delegálhatja a jogot arra, hogy valaki másnak – például egy részleg manager vagy a segélyszolgálat rendszergazdák csoport tagjai listájának kezelése<br> <br> A Managing csoportok Azure ad-ben a szakaszban további információk a csoportokon keresztül hozzáférés-kezelés. |Active Directory-identitásokkal kiterjesztheti a felhőbe, szinkronizálással és az összevonási keresztül |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlőelemet használ szerepkörök (RBAC) és kiépítési szabályzatok kiértékelése, tesztelése és az üzleti folyamatokat és a felhasználók hozzáférésének engedélyezésére vonatkozó szabályok. Kulcs rendszergazdák kiépítési szabályzatok létrehozása és a felhasználók szerepkörökhöz rendelése és erőforrásokra vonatkozó ezek a szerepkörök definiáló jogosultságok beállítása. RBAC kibővíti az identitáskezelési megoldás szoftveres folyamatok használhatja, és csökkentheti a felhasználói manuális beavatkozás a kiépítési folyamat során.
Az Azure AD RBAC lehetővé teszi, hogy a cég, amely egy egyéni teheti meg, ha rendelkezik hozzáféréssel az Azure portal-műveletek számának korlátozása. RBAC használatával férhet hozzá a portálon, a hitelesítésszolgáltatói rendszergazdák hozzáférés delegálása a következő hozzáférés-felügyeleti módszerek használatával:

* **Csoport alapú szerepkör-hozzárendelés**: Hozzáférés a helyi Active Directoryból hozzárendelheti az Azure AD-csoportokat, ingyeneseket szinkronizálja. Ez lehetővé teszi, hogy a meglévő befektetések által a szervezet tett eszközök és csoportok kezelése a folyamatok. Prémium szintű Azure AD a delegált csoport felügyeleti funkciót is használhatja.
* **Használja ki a beépített szerepkörök az Azure-ban**: Három szerepkört is használhatja – tulajdonos, közreműködő és olvasó, győződjön meg arról, hogy felhasználók és csoportok engedélye csak a feladataik elvégzéséhez szükséges feladatokat végezheti el.
* **Erőforrásokhoz való hozzáférés részletes**: Szerepköröket rendelhet felhasználókat és csoportokat egy adott előfizetés, erőforráscsoport vagy például egy webhely vagy az adatbázis egy önálló Azure-erőforrás. Így biztosítható, hogy a felhasználók rendelkeznek minden az erőforrásokhoz való hozzáférés, és nem kell kezelniük, erőforrásokhoz sem fér hozzá.

## <a name="provisioning-and-other-customization-options"></a>Kiépítés és egyéb testreszabási lehetőségek
A csapat segítségével üzleti terveket és követelmények mennyi úgy dönt, hogy az identitáskezelési megoldás testreszabása. Például a nagyvállalati szükség lehet a többfázisú bevezetési terv a munkafolyamatok és a egy növekményes üzembe helyezés területtől széles körben használt alkalmazások idősorán alapuló egyéni adapterek. Előfordulhat, hogy egy másik testreszabási csomagot adja meg két vagy több alkalmazás teljes szervezeten belül, a sikeres tesztelés után ki kell építeni. Alkalmazás felhasználói beavatkozás testre szabható, és eljárásai az erőforrásoknak az Automatikus kiépítés befogadásához megváltozhat.

Akkor is megszüntetése összetevő vagy szolgáltatás eltávolítása. Például egy fiók a megszüntetés azt jelenti, hogy a fiók törölnek egy erőforrást.

A hibrid modell, az erőforrásoknak a kérés- és szerepkör-alapú megközelítést, amelyek támogatottak az Azure AD által egyesíti. Az alkalmazottak vagy a felügyelt rendszerekről részéhez egy üzleti érdemes automatizálni-hozzárendelés szerepköralapú hozzáférés. Egy üzleti is kezelni minden más hozzáférési kérések vagy kivételek kérelem-alapú modellel. Egyes vállalatok előfordulhat, hogy manuális hozzárendelés kezdődnie, és fejlesztheti tovább a hibrid modell, egy teljes körűen szerepkör-alapú üzemelő példány egy későbbi időpontban szándéka felé.

Más cégeket azzal, előfordulhat, hogy praktikus üzleti okokból teljes szerepkör-alapú üzembe helyezés eléréséhez keresse meg, és a hibrid megközelítés a kívánt célját, célként. Továbbra is más cégeket azzal, előfordulhat, hogy egyetlen kérelem-alapú üzembe helyezés elégedett, és nem szeretne további definiálása és kezelése a szerepkör-alapú, az automatikus kiépítési házirendek erőfeszítésekre befektetni.

## <a name="license-management"></a>Licenckezelés
Csoport alapú licenc kezelése az Azure AD lehetővé teszi, hogy a rendszergazda felhasználók hozzárendelése egy biztonsági csoportot, és az Azure AD automatikusan licenceket rendel hozzá a csoport összes tagját. Ha a felhasználó ezt követően hozzá, vagy eltávolítása a csoportból, a licenc lesz kell automatikusan kiosztani vagy megvonni megfelelő módon.

Csoportok szinkronizálása a helyszíni AD- vagy kezelése az Azure ad-ben. Párosítás Ez az Azure AD premium önkiszolgáló csoportkezelés egyszerűen delegálhatja a licenc-hozzárendelés a megfelelő döntéshozók számára. Biztos lehet abban is, hogy a problémák, mint például a licenc ütközések és a helyadatok hiányoznak automatikusan rendezi a rendszer.

## <a name="self-regulating-user-administration"></a>Önálló szabályozó felhasználók felügyelete
Amikor a szervezet belső minden szervezet között-erőforrások kiépítése elindul, a helyi szabályozó felhasználói felügyeleti funkció valósíthatja meg. Határait átlépve is vegye figyelembe az előnyei és a felhasználók eszközkiépítési előnyeit. Ebben a környezetben egy felhasználó állapotának változása automatikusan megjelenik a hozzáférési jogosultságok között a szervezet határain és földrajzi területről. Kiépítési költségek csökkentése, és a hozzáférési és engedélyezési folyamat leegyszerűsíthető. A megvalósítás a szerepköralapú hozzáférés-vezérlés a teljes körű hozzáférés-kezelés megvalósítása a szervezet teljes potenciálját valósít meg. Automatizált eljárások szabályozzák a felhasználók átadásának felügyeleti költségek csökkentéséhez. Biztonsági házirendek betartását, automatizálásával javíthatja a biztonsági és egyszerűsíthetők és felhasználóiéletciklus-kezelést és az erőforrás-kiépítés nagy számítógépcsoportokon központosítása.

> [!NOTE]
> További információkért lásd: beállítása az Azure AD önkiszolgáló alkalmazáshozzáférés-kezeléshez
> 
> 

Az Azure AD (jogosultság alapján) licenc-alapú szolgáltatások munkahelyi egy előfizetéshez a címtárszolgáltatás és Azure AD-bérlő aktiválása. Ha az előfizetés aktív a szolgáltatásfunkciók directory/szolgáltatás-rendszergazdák által felügyelt, és a licenccel rendelkező felhasználók által használt. 

## <a name="integration-with-other-3rd-party-providers"></a>Integráció más 3. külső szolgáltatók

Az Azure Active Directory egyszeri bejelentkezést biztosít, és a bővített alkalmazás hozzáférés-biztonságot, több ezer SaaS-alkalmazásokhoz és a helyszíni webalkalmazásokat. További információkért lásd: [alkalmazások integrálása az Azure Active Directoryval](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Adja meg a szinkronizálás kezelése
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Ez az integráció felhasználók és a szervezetek kihasználhatják a következőket:

* Szervezetek számára a helyszíni vagy felhőalapú szolgáltatásokat kihasználva a Windows Server Active Directory, Azure Active Directory majd csatlakozzon a hibrid identitással rendelkező felhasználók biztosíthat.
* A rendszergazdák feltételes hozzáférést biztosító alkalmazás-erőforrás, eszköz és a felhasználói identitás, hálózati hely és a multi-factor authentication alapján is biztosítanak.
* Felhasználók használhatják a saját közös identitást fiókokon keresztül az Office 365, az Intune-ban, a SaaS-alkalmazásokhoz az Azure AD-ben és a harmadik féltől származó alkalmazások.
* A fejlesztők fejleszthetnek, az a közös identitás modell alkalmazások alkalmazások integrálása az Active Directoryban a helyszínen vagy az Azure felhőalapú alkalmazások számára

Az alábbi ábra egy identitás szinkronizálási folyamat áttekintése példát tartalmaz.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identitás-szinkronizációs folyamata

Tekintse át a következő táblázat segítségével összehasonlíthatja a szinkronizálási beállítások:

| Szinkronizálási lehetőséget | Előnyök | Hátrányai |
| --- | --- | --- |
| Szinkronizálási-alapú (a DirSync vagy az aad Connect) |Felhasználók és csoportok szinkronizálása a helyszíni és felhőbeli <br>  **A házirend-szabályozás**: Active Directory, amely lehetővé teszi a rendszergazda tudja felügyelni a jelszóházirendek, munkaállomás, korlátozások, zárolás kibővített vezérlők és több, nem kell további feladatokat a felhőben szabályzatainak beállítása.  <br>  **Hozzáférés-vezérlés**: Korlátozhatja a hozzáférést a felhőszolgáltatáshoz, hogy a szolgáltatások érhetők el a vállalati környezetben keresztül online kiszolgálók, vagy mindkettőt. <br>  Támogatási hívások szűkíteni: Ha a felhasználók kevesebb jelszót kell fejben rendelkeznek, valószínűleg kevesebb felejtse el őket. <br>  Biztonság: Felhasználói identitások és információk védettek, mert a kezelt kiszolgálók és egyszeri bejelentkezést, a használt szolgáltatások mindegyikét, és a helyszínen felügyelt. <br>  Erős hitelesítés támogatása: A felhőalapú szolgáltatás erős hitelesítést (más néven a kéttényezős hitelesítés) is használhatja. Azonban ha erős hitelesítés használata esetén kell használnia az egyszeri bejelentkezés. | |
| Összevonás-alapú (AD FS) n keresztül |Biztonságijogkivonat-szolgáltatás (STS) által engedélyezett. Az STS szolgáltatással történő egyszeri bejelentkezéses hozzáférést biztosítanak a Microsoft felhőszolgáltatással való konfigurálásakor, hozhat létre egy összevont megbízhatósági kapcsolat a helyszíni STS és az Azure AD-bérlőben már megadott összevont tartomány között. <br> Lehetővé teszi, hogy a végfelhasználók számára, hogy ugyanazokat a hitelesítő adatok használatával férhet hozzá több erőforrást <br>a végfelhasználók nem kell több hitelesítőadat-készletek kezelése. Még, a felhasználóknak meg kell adnia a hitelesítő adataikat a programban részt vevő erőforrásokhoz, minden egyes támogatott B2B és B2C-forgatókönyveket. |Speciális személyzetet igényel üzembe helyezése és karbantartása dedikált helyszíni AD FS-kiszolgálók. Erős hitelesítés használatára vonatkozó korlátozások vonatkoznak, ha azt tervezi, hogy az STS az AD FS használata. További információkért lásd: [speciális beállításainak konfigurálása az AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> További információ: [a helyszíni identitások integrálása az Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

