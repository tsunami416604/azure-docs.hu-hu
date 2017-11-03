---
title: "Az Azure Active Directory hibrid identitáskezelési elrendezésével kapcsolatos szempontok - adatok védelme stratégia kidolgozása |} Microsoft Docs"
description: "A hibrid identitáskezelési megoldás az üzleti követelményeinek megfelelően, hogy a megadott adatok védelmi stratégiája fogja definiálni."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 6c9b7423fa56886104bc6060d25904277b75f30c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás az adatok védelme stratégia meghatározása
Ebben a feladatban fogja definiálni, a data protection stratégiája a hibrid identitáskezelési megoldás az üzleti követelményeinek megfelelően, amelyet a megadott:

* [Adatvédelmi követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [A Tartalomkezelés követelmények meghatározása](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [Hozzáférés-vezérlési követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [Incidensválasz-követelmények meghatározása](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>Adatok védelmi beállítások meghatározása
Lett leírtak szerint [címtár-szinkronizálás követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md), a Microsoft Azure AD képes szinkronizálni az Active Directory tartományi szolgáltatások (AD DS) a helyszínen található. Ez az integráció lehetővé teszi a szervezetek kihasználhatják az Azure AD-felhasználó hitelesítő adatainak ellenőrzése a vállalati erőforrások eléréséhez közben. Ezt megteheti a mindkét forgatókönyvet: adatok rest a helyszíni és a felhőben.  Az Azure AD-adatokhoz való hozzáférés csak a felhasználó hitelesítése biztonságijogkivonat-szolgáltatás (STS) keresztül.

Ha hitelesített, az egyszerű felhasználónév (UPN) történő olvasásakor a hitelesítési jogkivonat és a replikált partíció, és határozza meg, a felhasználó tartománya megfelelő tárolót. A felhasználó fenntartása, engedélyezett állapotát, és a szerepkör az engedélyezési rendszert használják annak meghatározásához, hogy a kért cél bérlő van engedélyezve a felhasználó ebben a munkamenetben. Bizonyos engedélyezett műveletek (pontosabban, hozzon létre felhasználói jelszó-visszaállítás) hozzon létre egy megfelelőségi erőfeszítéseket vagy vizsgálatok kezelésére használható a bérlői rendszergazda által naplóban.

A helyszíni adatközpontját a Azure-tárolóba internetkapcsolaton keresztül áthelyezése adatait előfordulhat, hogy nem mindig valósítható adatmennyiség, a sávszélesség rendelkezésre állása vagy egyéb szempontok miatt. A [Azure Storage Import/Export szolgáltatás](../storage/common/storage-import-export-service.md) egy hardveralapú lehetőséget biztosít számára adatokat a blob Storage tárolóban nagy mennyiségű elhelyezéséhez/beolvasása. Ez lehetővé teszi, hogy küldjön [BitLocker-titkosítású](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) közvetlenül egy Azure-adatközpontban, ahol az a felhő üzemeltetői fel kell töltenie a tartalom a tárfiókhoz vagy Azure adatait letölthetik a lemezmeghajtók való visszatéréshez, a merevlemez-meghajtókat. Ez a folyamat (kulccsal a BitLocker által a szolgáltatás a feladat beállítása során létrehozott) csak a titkosított lemezek elfogadás. A BitLocker-kulcsot megadott Azure külön-külön, lehetővé téve a kulcs megosztása sávon kívül.

Mivel adatokat átvitel közben különböző helyzetekben végrehajthatók, olyan is fontos tudni, hogy használ-e a Microsoft Azure [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) bérlők forgalom egymástól, például a gazdagép és vendég szintű mértékek alkalmazó elkülönítése a tűzfalakra, IP-csomagok szűrését, blokkolja a port és HTTPS-végpontnak. Azonban a legtöbb Azure belső kommunikációs, beleértve az infrastruktúra-infrastruktúra és az infrastruktúra-ügyfelek (helyszíni) is titkosítást kapnak. Egy másik fontos helyzet lehet az Azure adatközpontjaiban; kommunikációja Ahhoz, hogy biztosítsa, hogy egyetlen virtuális gép megszemélyesíteni, vagy kihallgassa egy másik IP-címét a hálózatok a Microsoft felügyeli. A TLS/SSL Azure Storage vagy SQL-adatbázis elérésekor, vagy ha Felhőszolgáltatásokhoz csatlakozó használatos. Ebben az esetben az ügyfél rendszergazdai felelős a TLS/SSL-tanúsítvány beszerzése és a bérlői infrastruktúra telepítését. Adatok forgalom azonos környezetben a virtuális gépek között, vagy egy központi telepítésnél, Microsoft Azure virtuális hálózaton keresztül a bérlők közötti áthelyezése védelme például HTTPS, az SSL/TLS vagy mások titkosított kommunikáció protokollokon keresztül.

Attól függően, hogy hogyan válaszolt kérdéseit [adatvédelmi követelményeinek meghatározása](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md), kell tudni határoznia azt, hogyan szeretné védeni az adatokat, és hogyan a hibrid identitáskezelési megoldás segítséget nyújt a. A táblázatban az Azure által támogatott, az egyes adatok védelmi forgatókönyvek esetén rendelkezésre álló lehetőségek.

| Adatok védelmi beállítások | A felhő aktívan | A többi helyszíni | Az átvitel során |
| --- | --- | --- | --- |
| A BitLocker titkosítást |X |X | |
| SQL Server adatbázisok titkosítása |X |X | |
| VM-VM-titkosítás | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> Olvasási [szolgáltatás megfelelőségi](https://azure.microsoft.com/support/trust-center/services/) : [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) további információkat a tanúsítványok, amely megfelel az egyes Azure szolgáltatás.
> A beállítások a data protection egy többrétegű módszert használja, mivel ezek a lehetőségek összehasonlítása nem vonatkoznak a feladathoz. Győződjön meg arról, hogy minden egyes állapot esetében az adatok a rendelkezésre álló lehetőségek hasznosítja ki.
>
>

## <a name="define-content-management-options"></a>A Tartalomkezelés beállítások megadása
Egy hibrid identitás-infrastruktúra kezelése az Azure AD segítségével előnye, hogy a folyamat teljesen átlátható a végfelhasználó szempontjából. A felhasználó megpróbálja elérni egy megosztott erőforráson, az erőforrás-hitelesítés szükséges, a felhasználónak kell hitelesítési kérelmet küld az Azure AD ahhoz, hogy a jogkivonat beszerzése és az erőforrás elérésére. A teljes folyamat a háttérben, felhasználói beavatkozás nélkül történik. Akkor is engedélyt szeretne megadni egy [csoport](active-directory-manage-groups.md#getting-started-with-access-management) a felhasználók annak érdekében, hogy azok bizonyos általános műveletek elvégzéséhez.

Kapcsolatos adatvédelmi probléma, amely általában azok a szervezetek, a megoldás az adatok besorolásával szükséges. A jelenlegi helyszíni infrastruktúrával már használja az adatok besorolásával, akkor lehetséges, hogy kihasználja az Azure ad-val, a fő tárház felhasználói identitás. Egy közös eszközt, hogy a rendszer használt a helyszíni az adatok besorolását nevezik [adatbesorolási eszközkészlet](https://msdn.microsoft.com/library/Hh204743.aspx) a Windows Server 2012 R2 rendszerben. Ez az eszköz azonosítására, besorolásában és védelmében fájlkiszolgálókon a magánfelhőben található adatok segítséget. Akkor is kihasználhatják a [automatikus Fájlbesorolás](https://technet.microsoft.com/library/hh831672.aspx) a Windows Server 2012 ehhez.

Ha a szervezet nem rendelkezik adatbesorolási, pedig a következőnek kell védelemmel ellátni egy bizalmas fájlt új kiszolgálók helyszíni hozzáadása nélkül, akkor a Microsoft [Azure Rights Management szolgáltatás](https://technet.microsoft.com/library/JJ585026.aspx).  Az Azure RMS titkosítási, identitáskezelési és engedélyezési házirendeket használ a fájlok és e-mailek biztonságossá tételéhez, és több eszközön is használható – telefonokon, táblagépeken és számítógépeken. Mivel az Azure RMS egy felhőszolgáltatás, nincs nincs szükség a Megbízhatóságok kifejezett konfigurálására más szervezetekkel előtt védett tartalmakat megoszthatja velük. Ha már rendelkeznek Office 365- vagy Azure AD-címtárhoz, a szervezetek közötti együttműködés automatikusan támogatott. Csak az Azure RMS-sel szemben közös identitás támogatása a helyszíni Active Directory-fiókok Azure Active Directory szinkronizálási szolgáltatások (AAD Sync) vagy az Azure AD Connect használatával könyvtárattribútumokat is szinkronizálhatja.

Fontos része a Tartalomkezelés megérteni, hogy ki fér hozzá erőforrás-, ezért egy részletes naplózási képesség számára fontos a identitáskezelési megoldás a. Az Azure AD biztosít napló több mint 30 napja, beleértve:

* Szerepkör tagság változása (pl.: globális rendszergazdai szerepkörhöz hozzáadott felhasználó)
* Hitelesítőadat-frissítések (pl.: jelszó-változtatásának)
* Tartományok (pl.: egy egyéni tartományt, a tartomány eltávolításakor ellenőrzése)
* Hozzáadásával vagy eltávolításával alkalmazások
* Felhasználók kezelése (pl.: hozzáadását, eltávolítását, a felhasználó frissítése)
* Hozzáadásával vagy eltávolításával licencek

> [!NOTE]
> Olvasási [Microsoft Azure biztonsági és naplózási napló felügyeleti](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) további információkat az Azure-ban naplózási képességeket.
> Attól függően, hogy hogyan válaszolt kérdéseit [tartalomkezelési követelmények meghatározása](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md), kell tudni határoznia azt, hogyan szeretné kezelni a hibrid identitáskezelési megoldás a tartalmat. Míg a 6 elérhetővé tett összes beállítás integrálható az Azure ad-vel, fontos határozza meg, amely az üzleti igényeinek jobban megfelelő.
>
>

| Tartalomkezelési lehetőségeket | Előnyei | Hátrányok |
| --- | --- | --- |
| Helyszínen központosított (Active Directory tartalomvédelmi szolgáltatások) |A kiszolgáló-infrastruktúrájának felelős az adatok osztályozásához teljes hozzáféréssel <br> A Windows Server, nincs szükség további licencek vagy előfizetések beépített kapacitásprofilokban <br> Integrálható az Azure AD-t egy hibrid forgatókönyvek <br> Információk rights management (IRM) képességeket támogatja a Microsoft Online szolgáltatásaiban, például az Exchange Online és SharePoint online-hoz, valamint Office 365 <br> Támogatja a helyszíni Microsoft kiszolgálói termékeket, például Exchange Server, SharePoint Server és Windows Server és a fájlbesorolási infrastruktúra (FCI) futtató fájlkiszolgálókat. |Magasabb, karbantartási (tartsa be a frissítéseket, a konfiguráció és a potenciális frissítéseinek), mert informatikai a kiszolgáló tulajdonosa <br> Helyszíni kiszolgálói infrastruktúrát igényel<br> Doesn'tleverage Azure-képességek natív módon |
| A felhőben (Azure RMS) központosított |Könnyebben kezelhető a helyszíni megoldáshoz képest <br> Integrálható az Active Directory tartományi szolgáltatások a hibrid forgatókönyvek <br>  Teljesen integrálva van az Azure AD <br> Egy kiszolgáló helyszíni ahhoz, hogy a szolgáltatás telepítése nem igényel. <br> Támogatja a helyszíni Microsoft kiszolgálói termékeket, például Exchange Server, a SharePoint, a kiszolgáló és a Windows Server és a Fájlbesorolás, infrastruktúra (FCI) futtató fájlkiszolgálókat <br> Informatikai, teljes felügyeletet gyakorolhat a bérlői kulcs BYOK alkalmas lehet. |A szervezetnek rendelkeznie kell egy, az RMS-t támogató felhőalapú előfizetéssel <br> A szervezetnek rendelkeznie kell egy Azure AD-címtár támogatja az RMS felhasználóhitelesítését |
| Hibrid (Azure RMS integrálva van, a helyszíni Active Directory tartalomvédelmi szolgáltatások) |Ebben a forgatókönyvben a következő előnyöket, központosított a helyszínen és a felhőben összegzi. |A szervezetnek rendelkeznie kell egy, az RMS-t támogató felhőalapú előfizetéssel <br> A szervezetének felhasználói hitelesítés támogatásához az RMS-hez készült Azure AD-címtárral kell rendelkeznie. <br> Azure-felhőszolgáltatás közötti kapcsolatot igényel, és a helyszíni infrastruktúra |

## <a name="define-access-control-options"></a>Adja meg a hozzáférés-vezérlési lehetőségek
Ami a hitelesítési, engedélyezési és hozzáférés szabályozása lesz ahhoz, hogy a vállalat számára segítségével egy központi identitás-tárházat miközben lehetővé teszi a felhasználók és a partnerek egyszeri bejelentkezés (SSO) az ábrán látható Azure AD-ben elérhető képességek alább:

![](./media/hybrid-id-design-considerations/centralized-management.png)

Központosított kezelését, és teljesen más címtárak integrációját

Egyszeri bejelentkezés az SaaS-alkalmazásokhoz ezer biztosít az Azure Active Directory és a helyszíni webalkalmazások. Kérjük, olvassa el a [Azure Active Directory összevonási kompatibilitási lista: harmadik fél Identitásszolgáltatók, amelyek segítségével az egyszeri bejelentkezés megvalósítása](https://msdn.microsoft.com/library/azure/jj679342.aspx) kapcsolatban bővebben az SSO külső Microsoft által ellenőrzött cikk. Ez a funkció lehetővé teszi, hogy a szervezet az identitások és hozzáférések felügyeletéhez irányítását megtartásával különféle forgatókönyvekhez, amik B2B végrehajtásához. Azonban a B2B során folyamat tervezésekor fontos tisztában lenni azzal a hitelesítési módszer, amelynek a partner által használható, és ellenőrizze, hogy ez a metódus Azure által támogatott. Jelenleg az Azure AD által támogatott módszerek a következők:

* Security Assertion Markup Language (SAML)
* OAuth
* Kerberos
* Tokenek
* Tanúsítványok

> [!NOTE]
> olvassa el [Azure Active Directory hitelesítési protokolljai](https://msdn.microsoft.com/library/azure/dn151124.aspx) tudni, hogy minden protokoll és az Azure-ban platformképességei kapcsolatos további részleteket.
>
>

A az Azure AD-támogatás, mobil üzleti alkalmazások használatával könnyen Mobile Services hitelesítés felhasználói élmény hogy az alkalmazottak az alkalmazásokat a vállalati Active Directory hitelesítő adataikkal bejelentkezni. Ez a szolgáltatás az Azure AD identitás-szolgáltatóként a Mobile Servicesben mellett és az egyéb identitás-szolgáltatóktól már támogatott (többek között Microsoft Accounts, a Facebook-azonosító, a Google-azonosító és a Twitter azonosítója) támogatott. Ha a helyszíni alkalmazások a vállalat Active Directory tartományi Szolgáltatásokban található a felhasználó hitelesítő adatait használja, a hozzáférést a partnerek és a felhőből érkező felhasználók átlátszó kell lennie. Felhasználó feltételes hozzáférés-vezérlés (felhőalapú) webalkalmazás, webes API-t, a Microsoft cloud services csomag, 3. fél SaaS-alkalmazásokhoz és natív (mobil) ügyfélalkalmazások kezelése, és rendelkezik a biztonsági, előnyeit naplózását, egyetlen helyen reporting. Azonban ajánlott ennek ellenőrzése nem éles környezetben, vagy a felhasználók csak korlátozott mennyiségű.

> [!TIP]
> Fontos, hogy az Azure AD nincs csoportházirend, ha az AD DS felhívja. Ahhoz, hogy az eszközök házirend szüksége lesz a mobileszköz-kezelési megoldás például [a Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx).
>
>

Miután a felhasználó hitelesítése az Azure AD, fontos értékelje ki, hogy a felhasználónak kell, hogy hozzáférési szintet. A hozzáférési szintet, amelyet a felhasználónak kell erőforrás eltérőek lehetnek, amíg az Azure AD egyes erőforrásokhoz való hozzáférés vezérlése adhat hozzá egy további biztonsági réteget, kell még figyelembe venni, hogy az erőforrás maga is beállíthatja, hogy a saját hozzáférés-vezérlési lista külön-külön , például a hozzáférés-vezérlés a fájlkiszolgálón lévő fájlokhoz. Az alábbi ábra a hibrid forgatókönyvek lehet hozzáférés-vezérlés szintek foglalja össze:

![](./media/hybrid-id-design-considerations/accesscontrol.png)

A diagramban. ábra x. bemutatta minden interakció egy esetén elegendő az Azure AD hozzáférési vezérlési forgatókönyv jelöli. Az alábbiakban egy leírást az egyes forgatókönyvek közül választhat:

1. Feltételes hozzáférés az alkalmazásokhoz a helyszínen szolgáltatott: használhat regisztrált eszközöket hozzáférési házirendekkel alkalmazásokat, amelyek a Windows Server 2012 R2 AD FS használatára van konfigurálva. A helyszíni feltételes hozzáférés beállításáról további információért lásd: [Helyszíni feltételes hozzáférés beállítása az Azure Active Directory eszközregisztrációjával](active-directory-conditional-access.md).

2. Hozzáférés-vezérlés az Azure portálon: Azure is lehetővé teszi a portálhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlést (RBAC) használatával). Ez a módszer lehetővé teszi, hogy a vállalat az Azure portálon teheti meg egy adott műveletek mennyiségének korlátozásához. Az RBAC történő hozzáférés szabályozása érdekében a portál használatával IT-rendszergazdák a következő hozzáférés-felügyeleti módszerek használatával is adhat hozzáférést:

* Csoportalapú szerepkör-hozzárendelést: is hozzáférést biztosít, amely képes-e szinkronizálva az Azure AD-csoportok a helyi Active Directoryból. Ez lehetővé teszi, hogy kihasználja a meglévő beruházások értékét, amely a szervezet által végrehajtott tooling és-folyamatok csoportok kezelése. Használhatja az Azure AD Premium a delegált csoport felügyeleti szolgáltatása.
* Használja ki a beépített szerepkörök az Azure-ban: használhatja három szerepkörök – tulajdonos, közreműködő, és ahhoz való olvasóra, biztosítja, hogy felhasználók és csoportok csak a feladatok elvégzésének a munkája elvégzéséhez szükséges engedéllyel.
* A részletes erőforrásokhoz való hozzáférés: szerepkörök hozzárendelése felhasználók és csoportok az adott előfizetéshez, erőforráscsoport vagy egy egyedi az Azure erőforrás, például egy webhely vagy az adatbázis. Így biztosítható, hogy a felhasználók rendelkeznek az összes, a szükséges erőforrások elérése és nem érhető el, hogy nem kell kezelése erőforrások.

> [!NOTE]
> Ha fejleszt alkalmazásokat, és testre szeretne szabni a hozzáférés-vezérlés a számukra, akkor is az Azure AD alkalmazás-szerepkörök használatára a hitelesítéshez. Tekintse át a [WebApp-RoleClaims-DotNet példa](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) hogyan Ez a funkció használatához az alkalmazás elkészítésére.
>
>

3. Feltételes hozzáférés az Office 365-alkalmazások Microsoft Intune-nal: IT-rendszergazdák hozhat létre feltételes hozzáférési szabályzatok, lehetővé téve az információkkal dolgozó szakemberek a szolgáltatásokat a feltételeknek megfelelő eszközökön a vállalati erőforrások biztonságossá tételére. További információ: [Feltételes hozzáférés eszközházirendjei Office 365-szolgáltatásokhoz](active-directory-conditional-access-device-policies.md).

4. Feltételes hozzáférés a Szolgáltatottszoftver-alkalmazásoknál: [Ez a szolgáltatás](http://blogs.technet.com/b/ad/archive/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work.aspx) lehetővé teszi a multi-factor authentication alkalmazás hozzáférési szabályok és a nem megbízható hálózaton a felhasználók hozzáférésének blokkolása konfigurálását. A multi-factor Authentication hitelesítés szabályokat alkalmazhat az alkalmazáshoz, vagy csak a felhasználók számára megadott biztonsági csoportokban rendelt összes felhasználó. Felhasználók kizárhatók a multi-factor authentication követelményeinek, ha az alkalmazás egy IP-címről, amelyek a szervezeten belüli hálózati érik el.

A hozzáférés-vezérlési beállításokat egy többrétegű módszert használja, mivel ezek a lehetőségek összehasonlítása nem vonatkoznak a feladathoz. Győződjön meg arról, hogy minden beállítás érhető el az egyes forgatókönyvek esetében, amelyhez engedélyeznie kell az erőforrásokhoz való hozzáférés szabályozása hasznosítja ki.

## <a name="define-incident-response-options"></a>Incidensválasz-beállítások megadása
Az Azure AD segít a potenciális biztonsági kockázatot is rejt magában a környezet figyeli a felhasználó tevékenység, az informatikai részleg kihasználhatja az Azure AD hozzáférési és használati jelentések funkció címinfrastruktúra megjelenítési lehetőségeinek az integritásra és a szervezete címtárát, biztonsági azonosító informatikai. Ezt az információt egy informatikai rendszergazda is jobban meghatározhatja, ahol lehetséges biztonsági kockázatokat a vizsgálandó, hogy azok megfelelően megtervezheti kockázatok csökkentésének lehetőségeit.  [Az Azure AD Premium előfizetéssel](active-directory-get-started-premium.md) tartozik egy engedélyezheti a biztonsági jelentések informatikai beszerezni ezeket az adatokat. [Az Azure AD-jelentések](active-directory-view-access-usage-reports.md) szerint vannak kategóriába alább látható módon:

* **Az anomáliadetektálási jelentések**: bejelentkezési eseményeket, amelyek észleltünk a rendellenes kell tartalmaznia. Célunk, ellenőrizze, hogy tisztában legyen ilyen tevékenység, és lehetővé teszik a tudni győződjön meg arról, hogy az esemény gyanúsnak meghatározása.
* **Integrált alkalmazás jelentés**: hogyan használja a szervezet a felhőalapú alkalmazások betekintést nyújt. Az Azure Active Directory integrálható a felhőalapú alkalmazások ezer.
* **Hibajelentések**: a külső alkalmazásokba fiókok létesítésekor előforduló hibákat jelzik.
* **Felhasználó-specifikus jelentések**: a tevékenységek adatai egy adott felhasználó eszköz/sign megjelenítéséhez.
* **Tevékenységi naplóit**: belül utolsó 24 órában, legutóbbi 7 nap vagy 30 napnál, valamint csoport tevékenység módosításainak és jelszó alaphelyzetbe állítása és nyilvántartási tevékenység feljegyzés az összes naplózott eseményeket tartalmazza.

> [!TIP]
> Egy másik olyan jelentést, amely segíthet az Incidensmegoldási csapat az esetek is van a [kiszivárgott hitelesítő adatokkal rendelkező felhasználó](http://blogs.technet.com/b/ad/archive/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials.aspx) jelentés.  Ez a jelentés egyezéseket felfedi a kiszivárgott hitelesítő adatok lista és a bérlői között.
>
>

Más fontos beépített jelentések az Azure AD-az incidensekre adott reakciók vizsgálat során használható, és a következő:

* **Jelszó-visszaállítási tevékenység**: Adjon meg a rendszergazdai jelszó alaphelyzetbe állítása a szervezet használja milyen aktívan betekintést.
* **Jelszó-visszaállítási regisztrációs tevékenység**: biztosít, amelybe felhasználók regisztrált a jelszó alaphelyzetbe állítása, módszerei insights és módszerek van kiválasztva.
* **Tevékenység csoport**: változtatások előzményeit biztosítja a csoporthoz (pl.: hozzáadott vagy eltávolított felhasználók), amely a hozzáférési panelen kezdeményezték.

Mellett a fő jelentéskészítési szolgáltatásával Azure AD Premium, amely is javítható az Incidensmegoldási vizsgálat során, az informatikai részleg során is kihasználhatja a naplózási jelentés tájékoztatást kaphat, mint:

* Szerepkör tagság változása (pl.: globális rendszergazdai szerepkörhöz hozzáadott felhasználó)
* Hitelesítőadat-frissítések (pl.: jelszó-változtatásának)
* Tartományok (pl.: egy egyéni tartományt, a tartomány eltávolításakor ellenőrzése)
* Hozzáadásával vagy eltávolításával alkalmazások
* Felhasználók kezelése (pl.: hozzáadását, eltávolítását, a felhasználó frissítése)
* Hozzáadásával vagy eltávolításával licencek

A lehetőségeinek incidensválaszra egy többrétegű módszert használja, mivel ezek a lehetőségek összehasonlítása nem vonatkoznak a feladathoz. Győződjön meg arról, hogy minden beállítás érhető el az egyes forgatókönyvek esetében, amelyek kell használnia az Azure AD jelentéskészítési képességet a vállalat incidensválasz folyamat részeként hasznosítja ki.

## <a name="next-steps"></a>Következő lépések
[Hibrid identitás felügyeleti feladatok meghatározása](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](active-directory-hybrid-identity-design-considerations-overview.md)
