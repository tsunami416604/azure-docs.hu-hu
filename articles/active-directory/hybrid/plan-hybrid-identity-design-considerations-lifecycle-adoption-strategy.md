---
title: Hibrid identitástervezés – életciklus-bevezetési stratégia Azure | Microsoft dokumentumok
description: Segít meghatározni a hibrid identitáskezelési feladatokat az egyes életciklus-fázisok elérhető lehetőségeinek megfelelően.
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
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109314"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Hibrid identitáséletciklus-bevezetési stratégia meghatározása
Ebben a feladatban a hibrid identitáskezelési megoldás identitáskezelési stratégiáját határozza meg, hogy megfeleljen a hibrid identitáskezelési feladatok meghatározása című csoportban megadott üzleti [követelményeknek.](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

A hibrid identitáskezelési feladatok meghatározásához a lépés korábbi szakaszában bemutatott teljes identitáséletciklus szerint, figyelembe kell vennie az egyes életciklus-fázisok elérhető lehetőségeket.

## <a name="access-management-and-provisioning"></a>Hozzáférés-kezelés és -ellátás
Egy jó fiókhozzáférés-kezelési megoldással a szervezet pontosan nyomon követheti, hogy ki milyen információkhoz férhet hozzá a szervezeten belül.

A hozzáférés-vezérlés egy központi, egypontos kiépítési rendszer kritikus függvénye. A bizalmas adatok védelme mellett a hozzáférés-vezérlés olyan meglévő fiókokat is elérhetővé teszi, amelyek nem jóváhagyott engedélyezést tartalmaznak, vagy már nincs rájuk szükség. Az elavult fiókok vezérléséhez a létesítési rendszer összekapcsolja a fiókadatokat a fiókokat birtokló felhasználók mérvadó adataival. A mérvadó felhasználói identitásadatokat általában az emberi erőforrások adatbázisaiban és könyvtáraiban tartják fenn.

A kifinomult informatikai vállalatok fiókjai több száz olyan paramétert tartalmaznak, amelyek meghatározzák a hatóságokat, és ezeket az adatokat a létesítési rendszer szabályozhatja. Az új felhasználók a mérvadó forrásból megadott adatokkal azonosíthatók. A hozzáférési kérelem-jóváhagyási képesség elindítja azokat a folyamatokat, amelyek jóváhagyják (vagy elutasítják) az erőforrás-kiépítést.

| Életciklus-kezelési fázis | A helyszínen | Felhő | Hibrid |
| --- | --- | --- | --- |
| Számlakezelés és -kiépítés |Az Active Directory® tartományi szolgáltatások (AD DS) kiszolgálói szerepkör segítségével méretezhető, biztonságos és kezelhető infrastruktúrát hozhat létre a felhasználó- és erőforrás-kezeléshez, és támogatást nyújthat olyan címtárhasználatra képes alkalmazásokhoz, mint a Microsoft® Exchange Server. <br><br> [Az AD DS-ben csoportokat helyezhet üzembe egy Identitáskezelőn keresztül](https://technet.microsoft.com/library/ff686261.aspx) <br>[Felhasználókat az AD DS-ben is kiépíthet.](https://technet.microsoft.com/library/ff686263.aspx) <br><br> A rendszergazdák a hozzáférés-vezérlés segítségével kezelhetik a megosztott erőforrásokhoz való felhasználói hozzáférést biztonsági okokból. Az Active Directoryban a hozzáférés-vezérlés objektumszinten történik, ha különböző hozzáférési szinteket vagy engedélyeket állít be az objektumokhoz, például a Teljes hozzáféréshez, az Íráshoz, az Olvasáshoz vagy a Nincs hozzáféréshez. Az Active Directory hozzáférés-vezérlése határozza meg, hogy a különböző felhasználók hogyan használhatják az Active Directory-objektumokat. Alapértelmezés szerint az Active Directory ban lévő objektumokra vonatkozó engedélyek a legbiztonságosabb értékre vannak állítva. |Létre kell hoznia egy fiókot minden olyan felhasználó számára, aki hozzáfér egy Microsoft felhőszolgáltatáshoz. Módosíthatja a felhasználói fiókokat is, vagy törölheti őket, ha már nincs rájuk szükség. Alapértelmezés szerint a felhasználók nem rendelkeznek rendszergazdai engedélyekkel, de tetszés szerint hozzárendelheti őket. <br><br> Az Azure Active Directoryn belül az egyik fő funkció az erőforrásokhoz való hozzáférés kezelése. Ezek az erőforrások képezhetik a címtár részét, például az objektumok kezelésére szolgáló engedélyek esetében, amelyeket a címtár szerepkörei biztosítanak, vagy lehetnek a címtáron kívüli erőforrások, például SaaS-alkalmazások, Azure-szolgáltatások, SharePoint-webhelyek vagy helyszíni erőforrások. <br><br> Az Azure Active Directory hozzáférés-kezelési megoldásának középpontjában a biztonsági csoport áll. Az erőforrás tulajdonosa (vagy a címtár rendszergazdája) hozzárendelhet egy csoportot a tulajdonában lévő erőforrásokhoz adott hozzáférési jogosultság biztosításához. A csoport tagjai hozzáférést kapnak, és az erőforrás tulajdonosa átruházhatja a jogot, hogy a csoport tagjainak listáját másnak kell kezelnie – például egy részlegvezetőnek vagy egy ügyfélszolgálati rendszergazdának.<br> <br> Az Azure AD csoportok kezelése szakasz további információt nyújt a csoportokon keresztüli hozzáférés kezeléséről. |Az Active Directory-identitások kiterjesztése a felhőbe szinkronizálással és összevonással |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök és kiépítési szabályzatok segítségével értékeli, teszteli és kényszeríti az üzleti folyamatok és szabályok a felhasználók számára való hozzáférés biztosítása. A kulcsrendszergazdák létesítési házirendeket hoznak létre, és a felhasználókat szerepkörökhöz rendelik, és jogosultságokat határoznak meg az erőforrásokhoz ezekhez a szerepkörökhöz. Az RBAC kiterjeszti az identitáskezelési megoldást a szoftveralapú folyamatok használatára, és csökkenti a felhasználói manuális interakciót a kiépítési folyamatban.
Az Azure AD RBAC lehetővé teszi a vállalat számára, hogy korlátozza a műveletek számát, amelyeket egy adott személy elláthatja, ha hozzáférnek az Azure Portalhoz. Az RBAC használatával a portálhoz való hozzáférés szabályozásához az informatikai rendszergazdák a következő hozzáférés-kezelési módszerekkel delegálják a hozzáférést:

* **Csoportalapú szerepkör-hozzárendelés:** Hozzáférést rendelhet az Azure AD-csoportokhoz, amelyek szinkronizálhatók a helyi Active Directoryból. Ez lehetővé teszi, hogy kihasználja a szervezet által eszközként és folyamatokban a csoportok kezeléséhez eszközölt és folyamatokban végrehajtott meglévő befektetéseket. Használhatja az Azure AD Premium delegált csoportfelügyeleti funkcióját is.
* **Használja ki a beépített szerepköröket az Azure-ban:** Három szerepkört használhat – tulajdonos, közreműködő és olvasó, így biztosíthatja, hogy a felhasználók és csoportok csak a feladataik elvégzéséhez szükséges feladatok elvégzéséhez szükséges feladatokelvégzéséhez jogosultak legyenek.
* **Részletes erőforrásokhoz való hozzáférés:** Szerepköröket rendelhet a felhasználókhoz és csoportokhoz egy adott előfizetéshez, erőforráscsoporthoz vagy egy adott Azure-erőforráshoz, például egy webhelyhez vagy adatbázishoz. Ily módon biztosíthatja, hogy a felhasználók hozzáférjenek az összes szükséges erőforráshoz, és ne férjenek hozzá azokhoz az erőforrásokhoz, amelyeket nem kell kezelniük.

## <a name="provisioning-and-other-customization-options"></a>Kiépítési és egyéb testreszabási lehetőségek
A csapat használhatja az üzleti tervek és követelmények annak eldöntésére, hogy mennyi az identitáskezelési megoldás testreszabásához. Egy nagyvállalat például szükség lehet egy szakaszos bevezetési terv munkafolyamatokhoz és egyéni adapterekhez, amely egy idősoron alapul a földrajzi lagúnák között széles körben használt alkalmazások növekményes kiépítéséhez. Egy másik testreszabási terv lehetővé teheti, hogy két vagy több alkalmazás kiépítése a teljes szervezeten belül, a sikeres tesztelés után. A felhasználó és az alkalmazás közötti interakció testreszabható, és az erőforrások kiépítésére vonatkozó eljárások módosíthatók az automatikus kiépítés hez.

Megszüntetheti a szolgáltatást vagy összetevőt. Például egy fiók kivonása azt jelenti, hogy a fiók törlődik egy erőforrásból.

Az erőforrások kiépítési hibrid modellje egyesíti a kéréseket és a szerepköralapú megközelítéseket, amelyeket az Azure AD is támogat. Az alkalmazottak vagy felügyelt rendszerek egy részhalmaza esetén előfordulhat, hogy egy vállalkozás szeretné automatizálni a hozzáférést szerepköralapú hozzárendeléssel. Egy vállalkozás is kezelni az összes többi hozzáférési kérelmek vagy kivételek egy kérelem-alapú modell. Egyes vállalkozások manuális hozzárendeléssel kezdődhetnek, és egy hibrid modell felé fejlődhetnek, azzal a szándékkal, hogy egy jövőbeli időpontban teljes mértékben szerepköralapú telepítést tegyenek.

Más vállalatok üzleti okokból nem találják praktikusnak a teljes szerepköralapú kiépítés elérését, és a hibrid megközelítést célozzák meg kívánt célként. Előfordulhat azonban, hogy más vállalatok elégedettek lehetnek csak a kérésalapú kiépítéssel, és nem kívánnak további erőfeszítéseket tenni a szerepköralapú, automatizált kiépítési házirendek meghatározására és kezelésére.

## <a name="license-management"></a>Licenckezelés
Az Azure AD csoportalapú licenckezelése lehetővé teszi a rendszergazdák számára, hogy felhasználókat rendeljenek egy biztonsági csoporthoz, és az Azure AD automatikusan hozzárendeli a licenceket a csoport összes tagjához. Ha egy felhasználót később hozzáadnak a csoporthoz, vagy eltávolítanak a csoportból, a rendszer automatikusan hozzárendel i vagy eltávolítja a licencet.

You can use groups you synchronize from on-premises AD or manage in Azure AD. Ha ezt párosítja az Azure AD prémium szintű önkiszolgáló csoportkezelővel, könnyedén delegálhatja a licenchozzárendelést a megfelelő döntéshozóknak. Biztos lehet benne, hogy az olyan problémák, mint a licencütközések és a hiányzó helyadatok automatikusan rendeződnek.

## <a name="self-regulating-user-administration"></a>Önszabályozó felhasználói adminisztráció
Amikor a szervezet elkezdi az erőforrások kiépítését az összes belső szervezet között, megvalósítja az önszabályozó felhasználói felügyeleti képességet. A felhasználók szervezeti határokon átnyúló kiépítésének előnyeit és előnyeit is megvalósíthatja. Ebben a környezetben a felhasználó állapotának változása automatikusan megjelenik a szervezeti határokon és földrajzi területeken lévő hozzáférési jogokban. Csökkentheti a kiépítési költségeket, és egyszerűsítheti a hozzáférési és jóváhagyási folyamatokat. A megvalósítás teljes mértékben kiaknázhatja a szerepköralapú hozzáférés-vezérlés megvalósításában rejlő lehetőségeket a teljes körű hozzáférés-kezeléshez a szervezetben. A felhasználói kiépítés szabályozására szolgáló automatizált eljárásokkal csökkentheti az adminisztratív költségeket. A biztonsági házirendek kényszerítésének automatizálásával, valamint a felhasználók életciklus-kezelésének és erőforrás-kifejlesztésének egyszerűsítésével és központosításával növelheti a biztonságot a nagy felhasználói populációk számára.

> [!NOTE]
> További információ: Az Azure AD beállítása az önkiszolgáló alkalmazások hozzáférés-kezeléséhez
> 
> 

Licencalapú (jogosultság-alapú) Azure AD-szolgáltatások működnek az Azure AD-címtárban/-bérlőben egy előfizetés aktiválásával. Miután az előfizetés aktív, a szolgáltatás képességeit a címtár-/szolgáltatás-rendszergazdák és a licencelt felhasználók használhatják. 

## <a name="integration-with-other-3rd-party-providers"></a>Integráció más külső szolgáltatókkal

Az Azure Active Directory egyszeri bejelentkezést és továbbfejlesztett alkalmazás-hozzáférési biztonságot biztosít több ezer SaaS-alkalmazás és helyszíni webalkalmazás számára. További információ: [Alkalmazások integrálása az Azure Active Directoryval](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Szinkronizáláskezelés definiálása
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Ezzel az integrációval a felhasználók és szervezetek a következőket használhatják:

* A szervezetek a windows Server Active Directoryt használó, majd az Azure Active Directoryhoz való csatlakozást használó, a helyszíni vagy felhőalapú szolgáltatások közös hibrid identitását biztosíthatják a felhasználók számára.
* A rendszergazdák feltételes hozzáférést biztosíthatnak az alkalmazáserőforrás, az eszköz- és felhasználói identitás, a hálózati hely és a többtényezős hitelesítés alapján.
* A felhasználók az Azure AD-ben az Office 365, az Intune, a SaaS-alkalmazások és a külső alkalmazások fiókain keresztül használhatják ki közös identitásukat.
* A fejlesztők olyan alkalmazásokat hozhatnak létre, amelyek kihasználják a közös identitásmodellt, és integrálják az alkalmazásokat a helyszíni Active Directoryba vagy az Azure-ba felhőalapú alkalmazásokhoz

Az alábbi ábra egy példa az identitás-szinkronizálási folyamat magas szintű nézetét.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identitás-szinkronizálási folyamat

A szinkronizálási beállítások összehasonlításához tekintse át az alábbi táblázatot:

| Szinkronizáláskezelés beállítás | Előnyök | Hátrányok |
| --- | --- | --- |
| Szinkronizálásalapú (DirSync vagy AADConnect segítségével) |Helyszíni és felhőalapú szinkronizált felhasználók és csoportok <br>  **Házirend-vezérlés**: A fiókházirendek az Active Directoryn keresztül állíthatók be, így a rendszergazda további feladatok végrehajtása nélkül kezelheti a jelszóházirendeket, a munkaállomást, a korlátozásokat, a zárolási vezérlőket és egyebeket.  <br>  **Hozzáférés-vezérlés**: Korlátozhatja a hozzáférést a felhőszolgáltatáshoz, így a szolgáltatások a vállalati környezeten, az online kiszolgálókon vagy mindkettőn keresztül érhetők el. <br>  Csökkentett támogatási hívások: Ha a felhasználók kevesebb jelszót kell megjegyezniük, kevésbé valószínű, hogy elfelejtik őket. <br>  Biztonság: A felhasználói identitások és az információk védettek, mert az egyszeri bejelentkezéshez használt összes kiszolgáló és szolgáltatás a helyszínen van elsajátítva és vezérelhető. <br>  Erős hitelesítés támogatása: Erős hitelesítést (más néven kétfaktoros hitelesítést) használhat a felhőszolgáltatással. Ha azonban erős hitelesítést használ, egyszeri bejelentkezést kell használnia. | |
| Összevonásalapú (AD FS-en keresztül) |A biztonsági jogkivonat-szolgáltatás (STS) engedélyezte. Ha úgy konfigurál egy STS-t, hogy egyszeri bejelentkezést biztosítson egy Microsoft felhőszolgáltatással, összevont megbízhatósági kapcsolatot hoz létre a helyszíni STS és az Azure AD-bérlőben megadott összevont tartomány között. <br> Lehetővé teszi a végfelhasználók számára, hogy ugyanazt a hitelesítő adatokat használják több erőforráshoz való hozzáféréshez <br>a végfelhasználóknak nem kell több hitelesítő adatot fenntartaniuk. A felhasználóknak azonban meg kell adniuk a hitelesítő adataikat a részt vevő erőforrások mindegyikéhez., B2B és B2C támogatott forgatókönyvek. |Speciális személyzetre van szükség a dedikált helyszíni AD FS-kiszolgálók telepítéséhez és karbantartásához. Az erős hitelesítés használatára korlátozások vonatkoznak, ha az AD FS-t az STS-hez kívánja használni. További információt az [AD FS 2.0 speciális beállításainak konfigurálása](https://go.microsoft.com/fwlink/?linkid=235649)című témakörben talál. |

> [!NOTE]
> További információ: [A helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)című témakörben talál.
> 
> 

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)

