---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - hibrid identitás életciklus bevezetési stratégia meghatározása |} Microsoft Docs"
description: "Segíti a hibrid identitáskezelési felügyeleti feladatok érhető el az egyes életciklus fázisokban beállításoknak megfelelően adja meg."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 18b40486a66d8e092a8af299460145989a1ab99d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Hibrid identitás életciklus bevezetési stratégia meghatározása
Ebben a feladatban fogja definiálni, a hibrid identitáskezelési megoldás az üzleti követelményeinek megfelelően, amelyet a megadott identity management stratégiája [határozza meg a hibrid identitáskezelési felügyeleti feladatok](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

A hibrid identitás felügyeleti feladatok a végpont identitás életciklusának korábbi ebben a lépésben bemutatott megfelelően megadásához fog kell figyelembe venni az egyes életciklus lépésekhez rendelkezésre álló beállításokkal.

## <a name="access-management-and-provisioning"></a>Kezelési és üzembe helyezését
Jó fiók hozzáférés-kezelési megoldás a szervezet nyomon követheti, pontosan milyen információkat nyújtanak a szervezet rendelkező.

Hozzáférés-vezérlés egy központosított, egyetlen pont létesítési rendszer kritikus függvény. Mellett a bizalmas adatok védelmével, hozzáférés-vezérlést a meglévő fiókokat, amelyeket elérhetővé tenni jóvá nem hagyott engedélyeket, vagy már nincs szükség. Elavult fiókokat, az üzembe helyezési-hivatkozások együttesen információkat mérvadó információkkal azokról a felhasználókról, akiknek a partnerek saját szabályozására. Az adatbázisok és az emberi erőforrások könyvtárak mérvadó felhasználói azonosító adatok általában megőrződik.

Fiókok a kifinomult informatikai nagyvállalatok akár több százszor is a hitelesítésszolgáltatók meghatározó paraméterek közé tartoznak, és ezeket a részleteket az üzembe helyezési rendszer szabályozhatók. Új felhasználók azonosítható a mérvadó forrásból nyújtó adatokkal. A hozzáférési kérelem jóváhagyása funkciójával indít el a folyamatok, amelyek a létesítési őket az erőforrás jóváhagyása (vagy elutasítása).

| Életciklus-kezelési fázis | A helyszíni | Felhő | Hibrid |
| --- | --- | --- | --- |
| Fiókok kezelése és üzembe helyezését |Az Active Directory® tartományi szolgáltatások (AD DS) kiszolgálói szerepkör segítségével létrehozhat egy méretezhető, biztonságos és kezelhető infrastruktúrát a felhasználó- és erőforrás-kezelést, és támogatást nyújthat olyan címtárhasználatra képes alkalmazásokhoz, például a Microsoft® Exchange Server. <br><br> [Active Directory tartományi szolgáltatásokban, az Identity manager keresztül létesíthet](https://technet.microsoft.com/library/ff686261.aspx) <br>[Az Active Directory Tartományi felhasználók oszthat](https://technet.microsoft.com/library/ff686263.aspx) <br><br> A rendszergazdák hozzáférés-vezérlés segítségével felügyelheti a felhasználók hozzáférését a megosztott erőforrások biztonsági okokból. Az Active Directoryban, hozzáférés-vezérlés kezelése objektumszinten által beállítás különböző szintű hozzáféréssel és engedéllyel, objektumok, például a teljes hozzáférés, írási, olvasható vagy nem férhető hozzá. Hozzáférés-vezérlés az Active Directory határozza meg a különböző felhasználók hogyan használhatják az Active Directory-objektumokat. Alapértelmezés szerint az Active Directory-objektumokra vonatkozó engedélyek beállítás van megadva a legbiztonságosabb beállítás. |Akkor hozzon létre egy Microsoft felhőszolgáltatásra hozzáférő minden felhasználói fiókot. Felhasználói fiókok módosítsa vagy törölje azokat, ha azok már nincs szükség is. Alapértelmezés szerint a felhasználók rendszergazdai jogosultságokkal nem rendelkező, de is rendelhet őket. További információkért lásd: [felhasználók kezelése az Azure AD](active-directory-create-users.md). <br><br> Az Azure Active Directoryban a fő szolgáltatásainak egyik képes kezelni az erőforrásokhoz való hozzáférést. Ezeket az erőforrásokat a könyvtár objektumok keresztüli szerepkörök a könyvtár, vagy az, hogy a címtárban, például az SaaS-alkalmazásokhoz, Azure-szolgáltatásokat, és a SharePoint-webhelyek vagy a helyszíni erőforrások a külső erőforrások kezeléséhez szükséges jogokat gazdabuszadaptereken része lehet. <br><br> A központ az Azure Active Directory-hozzáférés a felügyeleti megoldás, a biztonsági csoport. Az erőforrás tulajdonosa (vagy a rendszergazda a címtár) rendelhet egy csoportot adjon meg egy bizonyos hozzáférési jogosultsággal a saját erőforrásokat. A csoportnak a tagjai a rendszer a hozzáférést, és az erőforrás tulajdonosa delegálhatja a jogot, hogy valaki másnak – például a részleg vezetője vagy a segélyszolgálat rendszergazdák csoport tagjai listájának kezelése<br> <br> A témakör az Azure AD kezelése csoportok csoportok-en keresztüli hozzáférés kezeléséhez nyújt részletesebb információt. |Az Active Directory identitások kiterjeszti a felhőben, szinkronizálással és az összevonási |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlő (RBAC) által használt szerepkörök és teszteléséhez szabályzatok tesztelése, és az üzleti folyamatokat és hozzáférés biztosítása a felhasználók számára vonatkozó szabályok. Kulcs rendszergazdák szabályzatok létrehozása és a felhasználók hozzárendelése szerepkörökhöz, és a jogosultságok beállítása definiáló erőforrásokra vonatkozó ezeket a szerepköröket. Szerepalapú bővíti a identitáskezelési megoldás szoftveres folyamatok és csökkentse a kiépítési folyamat felhasználói kézi beavatkozást.
Az Azure AD RBAC lehetővé teszi, hogy a vállalat korlátozza, műveletek, amelyeket egy adott elvégezhetnek, amennyiben az Azure felügyeleti portálra hozzáférése van. RBAC történő hozzáférés szabályozása érdekében a portál használatával IT-rendszergazdák hitelesítésszolgáltató delegált hozzáférés a következő hozzáférés-felügyeleti módszerek használatával:

* **Csoportalapú szerepkör-hozzárendelés**: is hozzáférést biztosít, amely képes-e szinkronizálva az Azure AD-csoportok a helyi Active Directoryból. Ez lehetővé teszi, hogy kihasználja a meglévő beruházások értékét, amely a szervezet által végrehajtott tooling és-folyamatok csoportok kezelése. Használhatja az Azure AD Premium a delegált csoport felügyeleti szolgáltatása.
* **Használja ki a beépített szerepkörök az Azure-ban**: használhatja három szerepkörök – tulajdonos, közreműködő, és ahhoz való olvasóra, biztosítja, hogy felhasználók és csoportok csak a feladatok elvégzésének a munkája elvégzéséhez szükséges engedéllyel.
* **A részletes erőforrásokhoz való hozzáférés**: szerepkörök hozzárendelése felhasználók és csoportok az adott előfizetéshez, erőforráscsoport vagy egy egyedi az Azure erőforrás, például egy webhely vagy az adatbázis. Így biztosítható, hogy a felhasználók rendelkeznek az összes, a szükséges erőforrások elérése és nem érhető el, hogy nem kell kezelése erőforrások.

## <a name="provisioning-and-other-customization-options"></a>Üzembe helyezési és egyéb testreszabási lehetőségek
A csoport segítségével üzleti terveket és követelmények mennyi úgy dönt, hogy testre szabhatja a megoldást. Például a nagyvállalatok szükség lehet egy szakaszos bevezetési terv a munkafolyamatok és fokozatosan történő különböző földrajzi széles körben használt alkalmazások üzembe helyezéséhez egy idősorán alapuló egyéni adaptereket. Egy másik testreszabási terv két vagy több alkalmazást úgy kell létrehozni a teljes szervezeten belül, a sikeres vizsgálat után előfordulhat, hogy adja meg. Felhasználó-alkalmazás beavatkozás testre szabható, és erőforrások kialakítási eljárások automatizált üzembe helyezést befogadásához megváltozhat.

Lehet, hogy egy szolgáltatás vagy összetevő eltávolítása is kiosztásának megszüntetése. Például egy fiók megszüntetés azt jelenti, hogy a fiók törölnek egy erőforrást.

A hibrid típusú erőforrások kiépítése egyesíti a kérelem és a szerepkör-alapú megoldások, mindkettő által támogatott az Azure AD. Az alkalmazottak vagy a felügyelt rendszerekről egy részéhez egy üzleti előfordulhat, hogy automatizálni szeretné a hozzáférés a szerepkör-alapú hozzárendelés. Üzleti is előfordulhat, hogy kezeli, más hozzáférési kérelmek vagy kivételek kérelemalapú modell használatával. Egyes vállalatok előfordulhat, hogy kézi hozzárendelés kezdődnie, és fejleszteni hibrid modell, a teljes szerepkör-alapú központi telepítés egy későbbi időpontban szándéka felé.

Más vállalatokkal, előfordulhat, hogy praktikus teljes szerepkör-alapú üzembe helyezés eléréséhez üzleti okokból található, és megcélozni kívánt célként egy hibrid megközelítés. Továbbra is más vállalatokkal, előfordulhat, hogy megfelelőnek csak kérelem-alapú üzembe helyezés, és nem kíván további erőfeszítésekre meghatározhatja és kezelheti a szerepkör-alapú, az automatikus létesítési házirendek fektetnek.

## <a name="license-management"></a>Licenckezelés
Csoportalapú Licenckezelés az Azure AD lehetővé teszi, hogy a rendszergazda felhasználók hozzárendelése egy biztonsági csoportot, és az Azure AD automatikusan rendel hozzá licencet a csoport összes tagját. Ha a felhasználók ezt követően hozzá, vagy eltávolítani a csoportból, licenc lesz automatikusan hozzárendelve vagy megfelelően eltávolítani.

Directoryból szinkronizált csoportok használhatók a helyszíni AD vagy az Azure AD kezelése. Párosítás ennek az az Azure AD premium önkiszolgáló csoportkezelési könnyen delegálhatja a megfelelő döntéshozók számára a licenc-hozzárendelést. Akkor biztos lehet abban, hogy a problémák, mint például a licenc ütközések és a hiányzó helyadatok automatikusan rendezett.

## <a name="self-regulating-user-administration"></a>Önálló szabályozó felhasználókezelés
A szervezet összes belső szervezet erőforrások biztosításához indításakor valósítja meg az önálló szabályozó felhasználói felügyeleti funkció. Szervezet határain túlnyúló előnyeit és üzembe helyezési felhasználók előnyeit is megvalósítható. Ebben a környezetben a felhasználó állapotának változása automatikusan megjelenik a hozzáférési jogosultsága legyen a szervezet határain és földrajzi. Üzembe helyezési költségek csökkentése, és egyszerűsíthető a hozzáférés és a jóváhagyási folyamatot. A megvalósítás valósít meg a végpont hozzáférés kezelése szerepköralapú hozzáférés-vezérlés implementálása a szervezet teljes lehetséges. Keresztül szabályozására, a felhasználók átadása automatizált eljárások a felügyeleti költségek csökkentése érdekében. A biztonság fokozása érdekében automatizálása a biztonsági házirendek betartását, és egyszerűsítésére és felhasználói életciklusának kezelését és az erőforrás-kiépítés nagy felhasználói csoportok számára.

> [!NOTE]
> További információkért lásd: Azure ad-val önkiszolgáló alkalmazáshozzáférés-kezeléshez beállítása
> 
> 

Az Azure AD (jogosultság alapján) licenc-alapú munkahelyi szolgáltatásainak a címtárszolgáltatás vagy Azure AD-bérlő előfizetés aktiválása. Amint az előfizetése aktív a szolgáltatási lehetőségeket címtárszolgáltatás vagy a rendszergazdák által kezelhető és licenccel rendelkező felhasználók által használt. További információkért lásd: hogyan működik a licencelés munka az Azure AD?
Integráció más 3. fél szolgáltatók

Az Azure Active Directory egyszeri bejelentkezést biztosít, és a bővített SaaS-alkalmazásokhoz és a helyszíni webalkalmazások több ezer alkalmazás hozzáférés-biztonságot. Támogatott SaaS-alkalmazásokhoz az Azure Active Directory alkalmazáskatalógusában részletes listájáért lásd: az Azure Active Directory összevonási kompatibilitási lista: harmadik fél Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása

## <a name="define-synchronization-management"></a>Adja meg a szinkronizálási felügyeleti
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Ez az integráció felhasználók és a szervezetek kihasználhatják az alábbiak:

* A szervezetek biztosíthatnak a felhasználóknak hibrid közös identitással a helyszíni vagy felhőalapú szolgáltatásokat kihasználva a Windows Server Active Directory, majd csatlakozzon az Azure Active Directory között.
* A rendszergazdák alkalmazás erőforrás, a eszköz és a felhasználói identitást, a hálózati hely és a többtényezős hitelesítés alapján feltételes hozzáférést nyújthatnak.
* Felhasználók kihasználhatják a közös identitás fiókokon keresztül Office 365, Intune, SaaS-alkalmazások és a harmadik féltől származó alkalmazások az Azure AD-ben.
* A fejlesztők is használó alkalmazások létrehozását a közös identitás modell alkalmazások integrálása a helyszíni Active Directory vagy az Azure felhőalapú alkalmazásokhoz

Az alábbi ábra is rendelkezik identitás szinkronizálási folyamat áttekintése látható.

![](./media/hybrid-id-design-considerations/identitysync.png)

Identitás szinkronizálási folyamat

Tekintse át az alábbi táblázat segítségével összehasonlíthatja a szinkronizálási beállítások:

| Szinkronizálás lehetőséget | Előnyei | Hátrányok |
| --- | --- | --- |
| (A DirSync vagy az AADConnect) szinkronizálásra épülő |Felhasználók és csoportok szinkronizálja a helyszíni és felhő <br>  **A házirend-szabályozás**: fiók házirendeken keresztül állíthatók be, Active Directory, amely lehetővé teszi a rendszergazda a jelszóházirendek, a munkaállomás, a korlátozások, a zárolás kibővített vezérlők kezeléséhez, és több, anélkül, hogy a felhőben további feladatok elvégzéséhez.  <br>  **Hozzáférés-vezérlés**: korlátozhatja a felhőszolgáltatást, hogy a szolgáltatások is elérhetőek a vállalati környezetben, az online kiszolgálók vagy mindkettőt. <br>  A támogatási hívások csökkenteni: Ha a felhasználók kevesebb jelszót jegyezzen rendelkeznek, valószínűleg kevesebb elfelejtené. <br>  Biztonsági: Felhasználói identitások és információk védve vannak, mivel az összes a kiszolgálók és az egyszeri bejelentkezés, a használt szolgáltatások értékűre, és a helyszínen felügyelt. <br>  Erős hitelesítés támogatása: a felhőalapú szolgáltatás erős hitelesítést (más néven kéttényezős hitelesítést) használható. Azonban ha erős hitelesítés használatához kell használnia egyszeri bejelentkezést. | |
| Összevonáson alapuló (Active Directory összevonási Szolgáltatásokban) |Biztonságijogkivonat-szolgáltatás (STS) által engedélyezett. Az STS szolgáltatással történő egyszeri bejelentkezéses hozzáférést biztosít a Microsoft felhőszolgáltatással való konfigurálásakor létrehozni összevont megbízhatósági kapcsolat a helyszíni STS és az összevont tartományt az Azure AD-bérlő megadott között. <br> Lehetővé teszi, hogy a végfelhasználók számára, hogy ugyanazokat a hitelesítő adatok használatával férhet hozzá több erőforrást <br>a végfelhasználók nem kell több hitelesítőadat-készletek kezelése. Még, a felhasználók kell megadnia a hitelesítő adatokat Ha a programban részt vevő erőforrások, mindegyiknél B2B és B2C-forgatókönyvek esetében támogatott. |Speciális személyzet igényel dedikált helyszíni telepítésének és az AD FS-kiszolgáló. Ha az AD FS használatához a STS, nincsenek erős hitelesítés használatára vonatkozó korlátozások. További információkért lásd: [speciális beállítások konfigurálása az AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> További információ: [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)

