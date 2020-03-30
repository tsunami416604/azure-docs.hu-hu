---
title: Hibrid identitástervezés – adatvédelmi stratégia Azure | Microsoft dokumentumok
description: Ön határozza meg az adatvédelmi stratégia a hibrid identitásmegoldás, hogy megfeleljen az üzleti követelményeknek, hogy a megadott.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0186d862968259aae73071cfecd7d62443d0256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109369"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>Határozza meg az adatvédelmi stratégiát a hibrid identitáskezelési megoldáshoz
Ebben a feladatban a hibrid identitáskezelési megoldás adatvédelmi stratégiáját határozza meg, hogy megfeleljen a következőkben meghatározott üzleti követelményeknek:

* [Az adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [A tartalomkezeléshez kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [A hozzáférés-vezérlésre vonatkozó követelmények meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Az incidensmegoldáshoz kapcsolódó követelmények meghatározása](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Adatvédelmi beállítások megadása
A [címtárszinkronizálási követelmények meghatározása](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)című részben leírtak szerint a Microsoft Azure AD szinkronizálhat a helyszíni Active Directory tartományi szolgáltatásokkal (AD DS). Ez az integráció lehetővé teszi, hogy a szervezetek az Azure AD segítségével ellenőrizzék a felhasználók hitelesítő adatait, amikor vállalati erőforrásokelérésére próbálnak hozzáférni. Ezt mindkét esetben megteheti: a helyszíni és a felhőben lévő adatok. Az Azure AD-ben az adatokhoz való hozzáférés hez felhasználói hitelesítés szükséges egy biztonsági jogkivonat-szolgáltatáson (STS) keresztül.

A hitelesítés után a rendszer beolvassa az egyszerű felhasználónevet (UPN) a hitelesítési jogkivonatból. Ezután az engedélyezési rendszer határozza meg a felhasználó tartományának megfelelő replikált partíciót és tárolót. A felhasználó létezésére, engedélyezett állapotára és szerepkörére vonatkozó információk segítségével az engedélyezési rendszer meghatározhatja, hogy a célbérlőhöz való hozzáférés jogosult-e a felhasználó számára az adott munkamenetben. Bizonyos engedélyezett műveletek (konkrétan felhasználói és jelszó-visszaállítás létrehozása) létrehoznak egy naplózási nyomvonalat, amelyet a bérlői rendszergazda a megfelelőségi erőfeszítések vagy vizsgálatok kezeléséhez használ.

Előfordulhat, hogy az adatok áthelyezése a helyszíni adatközpontból az Azure Storage-ba internetkapcsolaton keresztül nem mindig valósítható meg az adatmennyiség, a sávszélesség rendelkezésre állása vagy egyéb szempontok miatt. Az [Azure Storage Import/Export szolgáltatás](../../storage/common/storage-import-export-service.md) hardveralapú lehetőséget biztosít nagy mennyiségű adat blob storage elhelyezéséhez/beolvasásához. Lehetővé teszi, hogy [a BitLocker-titkosítású merevlemez-meghajtókat](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) közvetlenül egy Azure-adatközpontba küldje el, ahol a felhőszolgáltatók feltöltik a tartalmat a tárfiókba, vagy letölthetik az Azure-adatokat a meghajtókra, hogy visszatérjenek Önhöz. Ehhez a folyamathoz csak titkosított lemezeket fogadunk el (a feladat beállítása során maga a szolgáltatás által létrehozott BitLocker-kulcs használatával). A BitLocker kulcs külön-külön érhető el az Azure számára, így a sávon kívüli kulcsmegosztást biztosít.

Mivel az átvitel közbeni adatok különböző forgatókönyvekben is történhetnek, fontos tudni, hogy a Microsoft Azure [virtuális hálózatot](https://azure.microsoft.com/documentation/services/virtual-network/) használ a bérlők egymástól való elkülönítésére, olyan intézkedéseket alkalmazva, mint a gazda- és vendégszintű tűzfalak, az IP-csomagszűrés, a portblokkolás és a HTTPS-végpontok. Azonban az Azure belső kommunikációjának nagy része, beleértve az infrastruktúra és az infrastruktúra közötti (helyszíni) infrastruktúrát is, szintén titkosítva van. Egy másik fontos forgatókönyv az Azure-adatközpontokon belüli kommunikáció; A Microsoft kezeli a hálózatokat annak biztosítása érdekében, hogy egyetlen virtuális gép sem tud megszemélyesíteni vagy lehallgatni egy másik IP-címét. A TLS/SSL az Azure Storage vagy az SQL-adatbázisok elérésekor, illetve a Felhőszolgáltatásokhoz való csatlakozáskor használatos. Ebben az esetben az ügyfél-rendszergazda felelős a TLS/SSL-tanúsítvány beszerzéséért és a bérlői infrastruktúrájukra való üzembe helyezéséért. A virtuális gépek között ugyanabban a központi telepítésben vagy a bérlők között a Microsoft Azure virtuális hálózaton keresztül történő adatforgalom titkosított kommunikációs protokollokon, például HTTPS-en, SSL/TLS-en vagy más módon védhető.

Attól függően, hogy hogyan válaszolt a kérdésekre az [adatvédelmi követelmények meghatározása](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)című részben, meg kell tudnia határozni, hogyan szeretné megvédeni az adatait, és hogy a hibrid identitáskezelési megoldás hogyan segíthet ebben a folyamatban. Az alábbi táblázat az Azure által támogatott, az egyes adatvédelmi forgatókönyvekhez elérhető lehetőségeket mutatja be.

| Adatvédelmi lehetőségek | Nyugalomban a felhőben | Nyugalmi helyen a helyszínen | Szállítás közben |
| --- | --- | --- | --- |
| BitLocker meghajtótitkosítás |X |X | |
| SQL Server adatbázisok titkosításához |X |X | |
| Virtuálisgép-vm titkosítás | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Olvassa el [a Megfelelőségi szolgáltatás a](https://azure.microsoft.com/support/trust-center/services/) Microsoft Azure [Adatvédelmi központ](https://azure.microsoft.com/support/trust-center/) ban, hogy többet tudjon meg a tanúsítványokról, amelyeknek az egyes Azure-szolgáltatások megfelelnek.
> Mivel az adatvédelmi lehetőségek többrétegű megközelítést alkalmaznak, e lehetőségek összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy az adatok egyes állapotához rendelkezésre álló összes lehetőséget kihasználja.
>
>

## <a name="define-content-management-options"></a>Tartalomkezelési beállítások megadása

Az Azure AD-nek a hibrid identitáskezelési infrastruktúra kezeléséhez való használatának egyik előnye, hogy a folyamat a végfelhasználó szempontjából teljesen átlátható. A felhasználó megpróbál hozzáférni egy megosztott erőforráshoz, az erőforrás hitelesítést igényel, a felhasználónak hitelesítési kérelmet kell küldenie az Azure AD-nek a jogkivonat beszerzése és az erőforrás elérése érdekében. Ez az egész folyamat történik a háttérben, anélkül, hogy a felhasználói beavatkozás. 

Az adatvédelmi szempontot kezelő szervezeteknek általában adatbesorolásra van szükségük a megoldásukhoz. Ha a jelenlegi helyszíni infrastruktúra már használja az adatbesorolást, az Azure AD a felhasználó identitásának fő tárháza. Az adatbesoroláshoz a helyszínen használt általános eszköz a Windows Server 2012 R2 [adatbesorolási eszközkészlete.](https://msdn.microsoft.com/library/Hh204743.aspx) Ez az eszköz segíthet a magánfelhőfájl-kiszolgálókon lévő adatok azonosításában, besorolásában és védelmében. A feladat végrehajtásához a Windows Server 2012 [automatikus fájlbesorolása](https://technet.microsoft.com/library/hh831672.aspx) is használható.

Ha a szervezet nem rendelkezik az adatok besorolása a helyén, de meg kell védenie a bizalmas fájlokat anélkül, hogy új kiszolgálók at helyszíni, használhatják a Microsoft [Azure Rights Management Service.](https://technet.microsoft.com/library/JJ585026.aspx)  Az Azure RMS titkosítási, identitás- és engedélyezési szabályzatokat használ a fájlok és az e-mailek védelméhez, és több eszközön – telefonokon, táblagépeken és számítógépeken – is működik. Mivel az Azure RMS egy felhőalapú szolgáltatás, nem kell explicit módon konfigurálni a bizalmi kapcsolatokat más szervezetekkel, mielőtt megosztana velük védett tartalmat. Ha már rendelkeznek Office 365- vagy Azure AD-címtárral, a szervezetek közötti együttműködés automatikusan támogatott. Szinkronizálhatja is csak a címtár attribútumok, amelyek az Azure RMS kell, hogy támogassa a közös identitás a helyszíni Active Directory-fiókok, az Azure Active Directory szinkronizálási szolgáltatások (AAD Sync) vagy az Azure AD Connect használatával.

A tartalomkezelés fontos része annak megértése, hogy ki melyik erőforráshoz fér hozzá, ezért a bővített naplózási képesség fontos az identitáskezelési megoldás számára. Az Azure AD 30 napon keresztül biztosít naplót, többek között a következőket:

* A szerepkör-tagság változásai (pl. a globális rendszergazdai szerepkörhöz hozzáadott felhasználó)
* Hitelesítő adatok frissítése (pl. jelszómódosítás)
* Tartománykezelés (pl. egyéni tartomány ellenőrzése, tartomány eltávolítása)
* Alkalmazások hozzáadása és eltávolítása
* Felhasználókezelés (pl. felhasználó hozzáadása, eltávolítása, frissítése)
* Licencek hozzáadása és eltávolítása

> [!NOTE]
> Olvassa el [a Microsoft Azure biztonsági és naplózási naplókezelés című,](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) további információit az Azure naplózási lehetőségeiről.
> Attól függően, hogy hogyan válaszolt a kérdésekre a [tartalomkezelési követelmények meghatározása](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)című részben, meg kell tudnia határozni, hogyan szeretné kezelni a tartalmat a hibrid identitáskezelési megoldásban. Bár a 6.
>
>

| Tartalomkezelési beállítások | Előnyök | Hátrányok |
| --- | --- | --- |
| Központosított helyszíni szolgáltatások (Active Directory tartalomvédelmi kiszolgáló) |Az adatok besorolásáért felelős kiszolgálói infrastruktúra teljes körű ellenőrzése <br> Beépített funkció a Windows Server rendszerben, nincs szükség további licencre vagy előfizetésre <br> Integrálható az Azure AD-vel hibrid forgatókönyvben <br> Támogatja a Microsoft Online-szolgáltatások, például az Exchange Online és a SharePoint Online, valamint az Office 365 tartalomvédelmi szolgáltatásait <br> Támogatja a helyszíni Microsoft server termékeket, például az Exchange Server, a SharePoint Server és a Windows Server és a File Classification Infrastructure (FCI) rendszert futó fájlkiszolgálókat. |Nagyobb karbantartás (lépést tartani a frissítésekkel, a konfigurációval és a potenciális frissítésekkel), mivel az it a kiszolgáló tulajdonosa <br> Kiszolgálói infrastruktúra megkövetelése a helyszínen<br> Nem használja ki az Azure képességeit natív módon |
| Központosított a felhőben (Azure RMS) |Könnyebben kezelhető a helyszíni megoldáshoz képest <br> Hibrid forgatókönyv esetén integrálható az AD DS-sel <br>  Teljes mértékben integrálva az Azure AD-vel <br> Nincs szükség a kiszolgáló helyszíni telepítéséhez a szolgáltatás <br> Támogatja a helyszíni Microsoft server termékeket, például az Exchange Server, a SharePoint, a Server és a Windows Server és fájlbesorolás, infrastruktúra (FCI) rendszert. <br> It, a BYOK képességgel teljes mértékben szabályozhatja a bérlő kulcsát. |A szervezetnek rendelkeznie kell egy felhőalapú előfizetéssel, amely támogatja az RMS-t <br> A szervezetnek rendelkeznie kell egy Azure AD könyvtárral az RMS felhasználói hitelesítésének támogatásához |
| Hibrid (az Azure RMS integrálva, helyszíni Active Directory tartalomvédelmi kiszolgáló) |Ez a forgatókönyv a központi lagúnák és a felhő előnyeit is összegyűjti. |A szervezetnek rendelkeznie kell egy felhőalapú előfizetéssel, amely támogatja az RMS-t <br> A szervezetnek rendelkeznie kell egy Azure AD könyvtárral az RMS felhasználói hitelesítésének támogatásához, <br> Kapcsolatot igényel az Azure felhőszolgáltatása és a helyszíni infrastruktúra között |

## <a name="define-access-control-options"></a>Hozzáférés-vezérlési beállítások megadása
Az Azure AD-ben elérhető hitelesítési, engedélyezési és hozzáférés-vezérlési képességek kihasználásával engedélyezheti a vállalat számára a központi identitástár használatát, miközben lehetővé teszi a felhasználók és a partnerek számára az egyszeri bejelentkezés (SSO) használatát az alábbi ábrán látható módon:

![központosított irányítás](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

Központosított irányítás és teljes integráció más könyvtárakkal

Az Azure Active Directory egyszeri bejelentkezést biztosít több ezer SaaS-alkalmazásés helyszíni webalkalmazás számára. Tekintse meg az [Azure Active Directory összevonási kompatibilitási lista: külső identitásszolgáltatók, amelyek segítségével valósítható meg egyszeri bejelentkezési](how-to-connect-fed-compatibility.md) cikk további részleteket az egyszeri bejelentkezés harmadik fél, amely a Microsoft által tesztelt. Ez a funkció lehetővé teszi a szervezet számára, hogy különböző B2B-forgatókönyveket valósítson meg, miközben az identitás- és hozzáférés-kezelést is kézben tartja. Azonban a B2B tervezési folyamat során fontos, hogy megértsék a hitelesítési módszert, amely et a partner, és ellenőrizze, ha ez a módszer támogatja az Azure-ban. Jelenleg az Azure AD a következő módszereket támogatja:

* Biztonsági helyességifeltétel-jelölőnyelv (SAML)
* Oauth
* Kerberos
* Tokenek
* Tanúsítványok

> [!NOTE]
> olvassa el [az Azure Active Directory hitelesítési protokolljait,](https://msdn.microsoft.com/library/azure/dn151124.aspx) hogy további részleteket tudjon meg az egyes protokollokról és az Azure-beli képességeiről.
>
>

Az Azure AD-támogatás használatával a mobilüzleti alkalmazások ugyanazt az egyszerű mobilszolgáltatások hitelesítési élményét is használhatják, hogy az alkalmazottak vállalati Active Directory-hitelesítő adataikkal bejelentkezhessenek a mobilalkalmazásaikba. Ezzel a funkcióval az Azure AD támogatja, mint egy identitásszolgáltató a mobilszolgáltatások mellett a többi identitásszolgáltatók már támogatott (amelyek magukban foglalják a Microsoft-fiókok, Facebook-azonosító, Google-azonosító és A Twitter-azonosító). Ha a helyszíni alkalmazások a felhasználó hitelesítő adatait használják a vállalat AD DS-ben, a felhőből érkező partnerek és felhasználók hozzáférésének átláthatónak kell lennie. A felhasználók feltételes hozzáférés-vezérlését (felhőalapú) webalkalmazásokra, webes API-ra, Microsoft felhőszolgáltatásokra, külső SaaS-alkalmazásokra és natív (mobil) ügyfélalkalmazásokra kezelheti, és a biztonság, a naplózás, a jelentéskészítés előnyeit egyben élvezheti Hely. Azonban ajánlott a megvalósítás ellenőrzése nem éles környezetben vagy korlátozott számú felhasználóval.

> [!TIP]
> fontos megemlíteni, hogy az Azure AD nem rendelkezik csoportházirenddel, mint az AD DS rendelkezik. Az eszközökházirendek érvényesítéséhez mobileszköz-felügyeleti megoldásra, például [a Microsoft Intune-ra](https://technet.microsoft.com/library/jj676587.aspx)van szükség.
>
>

Miután a felhasználó hitelesítése az Azure AD használatával, fontos, hogy értékelje ki a hozzáférési szintet, amely a felhasználó rendelkezik. A felhasználó által egy erőforráson keresztül birtokolt hozzáférési szint eltérő lehet. Bár az Azure AD adhat hozzá egy további biztonsági réteget bizonyos erőforrásokhoz való hozzáférés szabályozásával, ne feledje, hogy az erőforrás maga is rendelkezik a saját hozzáférés-vezérlési lista külön-külön, például a hozzáférés-vezérlés a fájlkiszolgálón található fájlok. Az alábbi ábra összefoglalja a hibrid forgatókönyvben használható hozzáférés-vezérlési szinteket:

![hozzáférés-vezérlés](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

Az x. ábrán látható minden egyes interakció egy hozzáférés-vezérlési forgatókönyvet jelent, amelyet az Azure AD lefedhet. Az alábbiakban az egyes forgatókönyvek leírása látható:

1. Feltételes hozzáférés a helyszíni üzemeltetett alkalmazásokhoz: A Regisztrált eszközöket hozzáférési házirendekkel használhatja az AD FS és a Windows Server 2012 R2 használatára konfigurált alkalmazásokhoz.

2. Hozzáférés-vezérlés az Azure Portal: Az Azure is lehetővé teszi a portálhoz való hozzáférés vezérlését a szerepköralapú hozzáférés-vezérlés (RBAC)). Ez a módszer lehetővé teszi a vállalat számára, hogy korlátozza az Azure Portalon egy adott személy által elvégzett műveletek számát. Az RBAC használatával a portálhoz való hozzáférés szabályozása, az informatikai rendszergazdák delegálhathozzáférést a következő hozzáférés-kezelési megközelítések:

   - Csoportalapú szerepkör-hozzárendelés: Hozzáférést rendelhet az Azure AD-csoportokhoz, amelyek szinkronizálhatók a helyi Active Directoryból. Ez lehetővé teszi, hogy kihasználja a szervezet által eszközként és csoportok kezelésére szolgáló folyamatokban végrehajtott meglévő befektetéseket. Használhatja az Azure AD Premium delegált csoportfelügyeleti funkcióját is.
   - Beépített szerepkörök használata az Azure-ban: Három szerepkört használhat – tulajdonos, közreműködő és olvasó, így biztosíthatja, hogy a felhasználók és csoportok csak a feladataik elvégzéséhez szükséges feladatok elvégzéséhez szükséges feladatok elvégzéséhez jogosultak legyenek.
   -  Részletes erőforrásokhoz való hozzáférés: Szerepkörökhozzárendelést rendelhet a felhasználókhoz és csoportokhoz egy adott előfizetéshez, erőforráscsoporthoz vagy egy adott Azure-erőforráshoz, például egy webhelyhez vagy adatbázishoz. Ily módon biztosíthatja, hogy a felhasználók hozzáférjenek az összes szükséges erőforráshoz, és ne férjenek hozzá azokhoz az erőforrásokhoz, amelyeket nem kell kezelniük.

   > [!NOTE]
   > Ha alkalmazásokat hoz létre, és testre szeretné szabni a hozzáférés-vezérlést, az Azure AD alkalmazásszerepkörök is használhatók az engedélyezéshez. Tekintse át ezt a [WebApp-RoleClaims-DotNet példát,](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) hogyan hozhat létre az alkalmazást a funkció használatához.


3. Feltételes hozzáférés az Office 365-alkalmazásokhoz a Microsoft Intune-nal: A rendszergazdák feltételes hozzáférésű eszközházirendeket hozhatnak létre a vállalati erőforrások védelmére, ugyanakkor lehetővé teszik a megfelelő eszközökön dolgozó informatikai dolgozók számára a szolgáltatások elérését. 
  
4. Feltételes hozzáférés Saas-alkalmazásokhoz: [Ez a funkció](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/) lehetővé teszi az alkalmazásonkénti többtényezős hitelesítési hozzáférési szabályok konfigurálását és a hozzáférés letiltását a nem megbízható hálózaton lévő felhasználók számára. A többtényezős hitelesítési szabályokat alkalmazhatja az alkalmazáshoz rendelt összes felhasználóra, vagy csak a megadott biztonsági csoportokon belüli felhasználókra. A felhasználók kizárhatók a többtényezős hitelesítési követelmény alól, ha az alkalmazást a szervezet hálózatán belüli IP-címről férnek hozzá.

Mivel a hozzáférés-vezérlési lehetőségek többrétegű megközelítést alkalmaznak, a beállítások összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy minden olyan forgatókönyvhöz elérhető lehetőséget kihasznál, amely az erőforrásokhoz való hozzáférés szabályozását igényli.

## <a name="define-incident-response-options"></a>Incidensválasz-beállítások megadása
Az Azure AD segíthet az informatikai identitás potenciális biztonsági kockázatok a környezetben a felhasználói tevékenység figyelése. Az it-szolgáltatások az Azure AD Access és a Usage jelentések segítségével betekintést nyerhetnek a szervezet címtárának integritásába és biztonságába. Ezekkel az információkkal az informatikai rendszergazda jobban meg tudja határozni, hogy hol lehet nekik lehetséges biztonsági kockázatok, hogy megfelelően tervezhessék meg ezeket a kockázatokat.  [Az Azure AD Premium-előfizetés](../fundamentals/active-directory-get-started-premium.md) biztonsági jelentésekkel rendelkezik, amelyek lehetővé teszik az informatikai szolgáltatások számára ezen információk beszerzését. [Az Azure AD-jelentések](../reports-monitoring/overview-reports.md) a következőképpen vannak kategorizálva:

* **Anomáliajelentések**: A rendellenesnek talált bejelentkezési eseményeket tartalmaznak. A cél az, hogy felvilágosítsa Önt az ilyen tevékenységekről, és lehetővé tegye annak megállapítását, hogy egy esemény gyanús-e.
* **Integrált alkalmazásjelentés:** Betekintést nyújt a felhőalapú alkalmazások szervezetben történő használatba lépésének beépülő adataiba. Az Azure Active Directory több ezer felhőalkalmazással kínál integrációt.
* **Hibajelentések**: Jelezze a külső alkalmazások fiókokra való kiépítési során előforduló hibákat.
* **Felhasználóspecifikus jelentések:** Egy adott felhasználó eszköz/bejelentkezési tevékenységadatainak megjelenítése.
* **Tevékenységnaplók:** Az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban naplózott összes ellenőrzött esemény, valamint a csoporttevékenység-módosítások, valamint a jelszó-visszaállítási és regisztrációs tevékenység rekordját tartalmazza.

> [!TIP]
> Egy másik jelentés, amely szintén segíthet az incidensválasz-csapat dolgozik egy eseta a [felhasználó kiszivárgott hitelesítő adatok](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) jelentést. Ez a jelentés a kiszivárgott hitelesítő adatok listája és a bérlő közötti egyezéseket jelenik meg.
>


Az Azure AD egyéb fontos beépített jelentései, amelyek az incidensválaszok kivizsgálása során használhatók, és a következők:

* **Jelszó-visszaállítási tevékenység:** adjon betekintést a rendszergazdának arról, hogy a szervezetben hogyan használják a jelszó-visszaállítást.
* **Jelszó-visszaállítási regisztrációs tevékenység:** betekintést nyújt abba, hogy mely felhasználók regisztrálták a jelszó-visszaállítási módszereiket, és milyen módszereket választottak ki.
* **Csoportos tevékenység:** a Hozzáférési panelen kezdeményezett csoport (pl. felhasználók) változásainak előzményeit tartalmazza.

Az Azure AD Premium alapvető jelentéskészítési képessége mellett, amelyet az incidensválaszok kivizsgálása során használhat, az informatikai részlegek a naplózási jelentés előnyeit is kihasználhatják az olyan információk megszerzéséhez, mint például:

* A szerepkörtagság változásai (például a globális rendszergazdai szerepkörhöz hozzáadott felhasználó)
* Hitelesítő adatok frissítése (például jelszómódosítás)
* Tartománykezelés (például egyéni tartomány ellenőrzése, tartomány eltávolítása)
* Alkalmazások hozzáadása és eltávolítása
* Felhasználókezelés (például felhasználó hozzáadása, eltávolítása, frissítése)
* Licencek hozzáadása és eltávolítása

Mivel az incidensválasz beállításai többrétegű megközelítést alkalmaznak, a beállítások összehasonlítása nem alkalmazható erre a feladatra. Győződjön meg arról, hogy kihasználja az összes rendelkezésre álló lehetőségeket minden forgatókönyv, amely megköveteli, hogy az Azure AD jelentéskészítési képesség részeként a vállalat incidens-válasz folyamat.

## <a name="next-steps"></a>További lépések
[Hibrid identitáskezelési feladatok meghatározása](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Lásd még:
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)
