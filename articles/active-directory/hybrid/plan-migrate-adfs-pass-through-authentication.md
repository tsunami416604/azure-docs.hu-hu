---
title: 'Azure AD Connect: Átmenő hitelesítés az Azure ad összevonási áttelepít |} A Microsoft Docs'
description: Információk a áthelyezi a hibrid identitáskezelési környezetet a összevonási átmenő hitelesítés.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5361c8940c8c7dba5338a3f5a0ed18910f7e45a0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410329"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Átmenő hitelesítés az Azure ad összevonási áttelepítésére
A következő dokumentum útmutatást nyújt az átmenő hitelesítés AD FS áthelyezését.

>[!NOTE]
>Letölthető a jelen dokumentum elérhető legyen a [Itt](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Átmenő hitelesítés előfeltételeinek
A következő előfeltételek szükségesek a migráláshoz.
### <a name="update-azure-ad-connect"></a>Frissítés Azure AD Connect

Rendelkeznie kell legalább sikeres végrehajtásához az átmenő hitelesítés áttelepítési lépéseit [az Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ez a verzió bejelentkezési átalakítás történik, és csökkenti a felhőalapú hitelesítés potenciálisan óra és perc összevonási áttelepíthetők teljes időtartama érintő jelentős változásokat tartalmaz.

> [!IMPORTANT]
> Elavult dokumentáció, eszközök és blogok azt jelzi, hogy felhasználók átalakítását lépésre szükség amikor az összevont tartományok átalakítása felügyelt. **Felhasználók átalakítása** van többé nem szükséges, és a Microsoft dolgozik dokumentáció és eszközök ennek megfelelően frissíti.

Az Azure AD Connect frissítése a legújabb verzióra, ezzel [update utasítások](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Hitelesítési ügynökök számának és elhelyezésének megtervezése

Az átmenő hitelesítés az Azure AD Connect kiszolgáló és a helyi Windows-kiszolgálók passzív ügynökök telepítésével valósítható meg. Telepítse az ügynököket, az Active Directory-tartományvezérlők a késés csökkentése érdekében a lehető legközelebb.

A legtöbb ügyfél két vagy három hitelesítés ügynökök vonatkoznak a magas rendelkezésre állás és a kapacitás, és a egy bérlő lehet legfeljebb 12 ügynökök regisztrálva. Az első ügynök mindig telepítve van az AAD Connect magát a kiszolgálót is. Tekintse meg a [információ a hálózati forgalom becsléseket és teljesítmény-útmutató](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) ügynök korlátozások és az ügynök telepítési lehetőségek.

### <a name="plan-migration-method"></a>Áttelepítési módszer megtervezése

ESP és közvetlen egyszeri bejelentkezés az összevont hitelesítés át két módszer áll rendelkezésre. Használt módszer az AD FS eredeti konfigurációjától függ.

- **Az Azure AD Connect**. Ha az AD FS eredetileg konfigurálták az Azure AD Connect, akkor a módosítás az átmenő hitelesítés használatával *kell* hajtható végre, az Azure AD Connect varázsló lépéseit.

Az Azure AD Connect használata esetén, a Set-MsolDomainAuthentication parancsmagot futtatja, automatikusan a felhasználói bejelentkezési módszert, és ezért azt nem szabályozhatják, unfederating összes az Azure AD-bérlő ellenőrzött összevont tartománynak.  
‎  
> [!NOTE]
> Jelenleg nem kerülheti el nem federating minden tartomány a bérlőben, amikor a felhasználó módosítja jelentkezzen be az átmenő hitelesítés az AAD Connect eredetileg használt AD FS konfigurálása, ha.  
‎
- **A PowerShell-lel az Azure AD Connect**. Ez a módszer használható, csak akkor, ha az AD FS eredetileg nem lett konfigurálva az Azure AD Connecttel. Továbbra is módosítani kell a felhasználói bejelentkezési módszer az Azure AD Connect varázsló segítségével, de az alapvető különbség, hogy nem fog automatikusan futni a Set-MsolDomainAuthentication parancsmag az Ön számára nincs ismertségét, az AD FS farm rendelkezik, és ezért keresztül teljes hozzáféréssel rendelkezik mely tartományok lesznek konvertálva, és milyen sorrendben.

Szeretné megtudni, melyik módszert használja, hajtsa végre a lépéseket a következő szakaszt.

#### <a name="verify-current-user-sign-in-settings"></a>Ellenőrizze a jelenlegi felhasználói bejelentkezési beállítások

Ellenőrizze a jelenlegi felhasználói bejelentkezési beállításai az Azure AD-portálra való bejelentkezés [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) egy globális rendszergazdai fiókkal. 

Az a **felhasználói bejelentkezés** területén ellenőrizze, hogy **összevonási** van **engedélyezve** , és hogy **zökkenőmentes egyszeri bejelentkezést** és  **Az átmenő hitelesítés** vannak **letiltott**. 

![Kép: 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Győződjön meg arról, hogyan lett konfigurálva az összevonási

   1. Az Azure AD Connect-kiszolgáló, és indítsa el az Azure AD Connect, majd jelölje ki az **konfigurálása**.
   2. Az a **további feladatok** képernyőn válassza ki **aktuális konfiguráció megtekintése** majd **tovább**.</br>
   ![Kép 31.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. Az a **a megoldás áttekintése** lapon görgessen le a **Active Directory összevonási szolgáltatások (AD FS)**.</br>
   Ha látja, hogy az AD FS konfigurációs van ebben a szakaszban, majd biztonságosan feltételezheti eredetileg konfigurálták az AD FS az Azure AD Connect használatával, és ezért a felügyeltről átalakítása összevont felügyelt driven is az Azure AD Connecten keresztül  **Felhasználói bejelentkezés módosítása** beállítást, a folyamat részleteit a szakasz a **1. lehetőség: Az átmenő hitelesítés konfigurálása az Azure AD Connect használatával**.  
‎
   4. Active Directory összevonási szolgáltatások szerepel az aktuális beállítások nem fogja látni, akkor lesz manuálisan konvertálnia kell a tartományából, amelyek részleteit a szakasz a Powershellen keresztül felügyelt összevont **2. lehetőség – Váltás a összevonási ESP az Azure AD Connect és a PowerShell használatával**.

### <a name="document-current-federation-settings"></a>A dokumentum aktuális összevonási beállítások

A Get-MsolDomainFederationSettings parancsmag futtatásával az aktuális összevonási beállítás is található.

A parancs a következő:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Példa:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


Azokat a beállításokat, előfordulhat, hogy testre szabott az összevonási tervezési és üzembe helyezési dokumentációját, kifejezetten ellenőrzése **PreferredAuthenticationProtocol**, **SupportsMfa**, és  **PromptLoginBehavior**.

További információ a mi ezek a beállítások megtalálhatók alább.

[Active Directory összevonási szolgáltatások kérni = bejelentkezési paraméter támogatása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Ha SupportsMfa jelenleg értéke "True" majd ez azt jelenti, egy helyszíni MFA-megoldást használ 2nd factor kihívást behelyezése a felhasználói hitelesítési folyamatot. Ez nem fog működni az Azure AD hitelesítési forgatókönyvei, és ehelyett kell kihasználhatja az Azure MFA (felhőalapú) szolgáltatás ugyanannak a függvénynek végrehajtásához. Gondosan az MFA-követelmények felmérése előtt, és győződjön meg arról, hogy megismerte, hogyan használható az Azure MFA, a licencelési szempontokat, és a végfelhasználó regisztrációt a tartományok átalakítása előtt.

#### <a name="backup-federation-settings"></a>Biztonsági mentési összevonási beállítások

Bár nem lesz kell módosítás más függő felek számára az AD FS farmon, a folyamat során, ellenőrizze, hogy az AD FS farmon, a visszaállítható az aktuális érvényes biztonsági ajánlott. Ehhez használja az ingyenes Microsoft [AD FS gyors visszaállítási eszköz](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Ezzel az eszközzel használható biztonsági mentése és visszaállítása az AD FS-ben vagy egy meglévő farmot, vagy egy új farmot.

Ha nem kíván használni az AD FS gyors visszaállítási eszköz, majd minimális kell exportálnia az "a Microsoft Office 365 Identity Platform" függő entitás megbízhatóságához, és előfordulhat, hogy hozzáadta társított egyéni jogcímszabályokat. Ehhez a következő PowerShell-példa-n keresztül

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Telepítési megfontolások és az AD FS használatát

### <a name="validate-your-current-ad-fs-usage"></a>Ellenőrizze a jelenlegi AD FS használata

Mielőtt konvertálja az összevont felügyelt, akkor tekintse meg szorosan hogyan használja az AD FS még ma az Azure AD és Office 365-höz és más alkalmazások (függő entitás megbízhatóságai). Pontosabban fontolja meg a következő táblázatban:

| Ha| majd |
|-|-|
| Ezeket az alkalmazásokat az AD FS megőrzése kívánja.| Az AD FS és az Azure AD fog használni, és a végfelhasználói élmény ezért figyelembe kell vennie. Felhasználók előfordulhat, hogy kétszer bizonyos esetekben, miután az Azure AD-(fog ered egyszeri Bejelentkezéssel és újabb verziók esetében más alkalmazásokhoz, az Office 365-höz hasonló) hitelesítenie kell és újra bármely alkalmazásokhoz, mint egy függőentitás-megbízhatóságot az AD FS továbbra is kötve. |
| Az AD FS a nagymértékben testre szabott és tartománybeli nem duplikálható az Azure AD-ben onload.js fájlban meghatározott testreszabási beállítások (például módosította a bejelentkezési élmény úgy, hogy a felhasználók csak adja meg a SamAccountName formátum a felhasználóneve adatkötetekkel egy egyszerű felhasználónevet, vagy rendelkezik egy márkaüzenettel ellátott erősen a bejelentkezési élmény)| Győződjön meg arról, hogy a jelenlegi testreszabási követelmények érheti el az Azure AD a folytatás előtt kell. Tekintse meg az AD FS márkajelzési és az AD FS testreszabás szakaszok további információt és útmutatást.|
| Az örökölt hitelesítési ügyfeleket az AD FS-n keresztül blokkolja.| Érdemes lecserélni az örökölt hitelesítési ügyfelek jelenleg megtalálható az AD FS kombinációjával blokkolása vezérlők [feltételes hozzáférés-vezérlés az örökölt hitelesítés](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) és [Online Exchange ügyfélelérési Szabályok](http://aka.ms/EXOCAR). |
| A felhasználók hitelesítéséhez az AD FS többtényezős hitelesítés végrehajtásához egy a helyszíni MFA-kiszolgálói megoldás elleni van szüksége.| Nem kell az MFA-hitelesítést keresztül a helyszíni MFA-megoldást behelyezése a hitelesítési folyamat a felügyelt tartományhoz tartozó azonban használhatja az Azure MFA szolgáltatás érdekében a későbbiekben egyszer a tartomány lesz konvertálva. Ha a felhasználók nem használja az Azure MFA még ma, majd ez magában foglalja egy onetime végfelhasználói regisztrációs lépést, amely kell előkészítése, illetve kommunikálhatnak a végfelhasználók számára. |
| Használhatja (engedélyezési szabályok) hozzáférés-vezérlési szabályzatokkal még ma az AD FS Office 365-höz való hozzáférés szabályozásához.| Fontolja meg, és cserélje le ezeket az Azure ad-ben egyenértékű [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) és [Exchange Online ügyfél-hozzáférési szabályok](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Közös AD FS testreszabás szempontjai

#### <a name="inside-corporate-network-claim"></a>Jogcím a vállalati hálózaton belül

A InsideCorporateNetwork jogcímet AD FS által a hitelesítő felhasználó esetén a vállalati hálózaton belül. Ez a jogcím ezután átkerül az Azure ad-ben, és megkerülhetik a többtényezős hitelesítés a felhasználó hálózati helye alapján. Lásd: [megbízható IP-címek összevont felhasználók](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) hogyan kell meghatároznia, hogy jelenleg engedélyezve van az AD FS-ben ez az információ.

A InsideCorporateNetwork jogcím nem lesz elérhető többé a tartományok konvertálja az átmenő hitelesítés után. [Nevesített helyek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) cserélje le ezt a funkciót is használható.

Nevű helyek konfigurálása után minden feltételes hozzáférési szabályzatokat konfigurálni vagy kizárja a hálózati helyeket a "Minden megbízható hely" vagy "MFA megbízható IP-címek" frissíteni kell, hogy az újonnan létrehozott nevű helyek tükrözik.

Lásd: [Active Directory feltételes hozzáférés helyek](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) további információ a feltételes hozzáférés a hely állapotára.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

Eszköz csatlakoztatása az Azure AD lehetővé teszi, amelyeket a biztonsági és megfelelőségi hozzáférés vonatkozó megfelelő eszközök feltételes hozzáférési szabályokat hozzon létre, és lehetővé teszi, hogy a felhasználók számára, hogy jelentkezzen be egy szervezeti munkahelyi vagy iskolai fiók használata helyett egy személyes eszköz fiók. Hibrid Azure AD-csatlakoztatott eszközök lehetővé teszi, hogy az AD-tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-ben. Az összevont környezetben előfordulhat, hogy konfigurálva ez a szolgáltatás.

Annak érdekében, hogy csatlakozzon a hibrid továbbra is fennáll, új eszközök működő csatlakoztatni a tartományhoz, a tartományok konvertált átmenő hitelesítés után, az Azure AD Connect kell konfigurálni az Active Directory számítógépfiókok szinkronizálása a Windows 10-ügyfelek számára Azure ad-ben. Windows 7 és Windows 8-fiókok csatlakozzon a hibrid használandó közvetlen egyszeri bejelentkezés a számítógép regisztrálása az Azure ad-ben, és nem rendelkezik a Windows 10 rendszerű eszközökön is szinkronizálhatja őket. Azonban akkor üzembe egy frissített workplacejoin.exe fájlt (.msi) n keresztül ezek az alsó szintű ügyfelek így azok regisztrálhatják magukat a közvetlen egyszeri bejelentkezés használatával. [Töltse le az .msi fájlt](https://www.microsoft.com/download/details.aspx?id=53554).

Ez a követelmény a további információkért lásd: [hogyan hibrid konfigurálása Azure Active Directoryhoz csatlakoztatott eszközök](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Védjegyezés

Szervezete is [testre szabott az AD FS bejelentkezési oldalainak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) a szervezet több profiljával kapcsolatos információk megjelenítéséhez. Ha igen, vegye figyelembe, hogy hasonló [az Azure AD bejelentkezési oldal testreszabása](https://docs.microsoft.com/azure/active-directory/customize-branding).

Hasonló testreszabások érhető el, amelyek bizonyos vizuális módosítások számíthat. Érdemes felvenni kívánt változások a végfelhasználók számára a kommunikáció.

> [!NOTE]
> Vállalati arculat csak akkor, ha az Azure ad prémium vagy alapszintű licenc vásárolt, vagy rendelkezik Office 365-licenccel érhető el.

## <a name="planning-for-smart-lockout"></a>Intelligens zárolás tervezése

Az Azure AD az intelligens zárolási jelszót találgatásos támadások ellen védi, és megakadályozza, hogy a helyszíni Active Directory-fiókot, ha az átmenő hitelesítés van használatban, és fiókzárolási csoport házirend van beállítva, az Active Directory kizárásuk. 

További információ a [intelligens zárolás funkciót és a konfiguráció szerkesztése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="planning-deployment-and-support"></a>Üzembe helyezés és a támogatás megtervezése

### <a name="plan-the-maintenance-window"></a>A karbantartási időszak tervezése

Amíg a tartomány átalakítási folyamat viszonylag rövid, Azure ad-ben továbbra is küldhet egyes hitelesítési kérelmek, az AD FS-kiszolgálók akár négy óra tartomány átalakítása befejezése után egy ideig. A négy órás időszak alatt, és függően különböző szolgáltatás oldalán gyorsítótárak, ezek hitelesítések előfordulhat, hogy nem fogadja el az Azure AD és a felhasználók egy hibaüzenetet fog kapni, mint lesz sikeres hitelesítése az Active Directory összevonási szolgáltatások továbbra is, de nem fogadja el az Azure AD egy a felhasználó jogkivonatot állít ki, ahogy pedig mostantól eltöröltük, hogy összevonási megbízhatósági kapcsolatot.

> [!NOTE]
> A művelet csak hatás elérő felhasználók a szolgáltatások egy böngészőből a post átalakítás időszakban mindaddig, amíg a szolgáltatás ügyféloldali gyorsítótár nincs bejelölve. Hagyományos (Exchange ActiveSync, az Outlook 2010 vagy 2013-hoz) nem kell negatív, mint az Exchange online-ban tartja a hitelesítő adatait tartalmazó gyorsítótár egy ideig, amellyel újra hitelesíteni kell a felhasználói beavatkozás nélkül anélkül, hogy térjen vissza az AD FS. Ezek az ügyfelek az eszközön tárolt hitelesítő adatok segítségével újra hitelesíteni magukat csendes gyorsítótárazott ez nincs bejelölve, és ezért felhasználók nem kaphatnak a jelszó utasításokat a konvertálási folyamat eredményeként. Ezzel szemben, Modern hitelesítési ügyfelek (az Office 2013 vagy 2016, IOS és Android-alkalmazások) esetében ezek egy érvényes frissítési jogkivonat használata vissza az AD FS megnyitása helyett erőforrások folyamatos elérését az új hozzáférési tokenek beszerzése érdekében, és ezért a jelszó utasításokat, mint a védett egy tartomány átalakítása eredményének feldolgozása és a jövőben működéséhez szükséges további konfigurálás nélkül.
> [!IMPORTANT]
> Ne állítsa le az AD FS környezetének, vagy távolítsa el az Office 365, a függő entitás megbízhatóságának, amíg nem ellenőrizte, hogy minden felhasználó sikeresen hitelesít felhőalapú hitelesítés használatával.

### <a name="plan-for-rollback"></a>Visszaállítás tervezése

Ha egy súlyos problémát talál, és nem oldható fel, dönthet, hogy állítsa vissza a megoldás összevonási biztonsági. Mi a teendő, ha az üzemelő példány nem fogadja a tervek szerint megtervezéséhez fontos. Ha a tartományt vagy a felhasználók átalakítását nem sikerül a telepítés során, vagy a rollback összevonási kell, majd ismernie kell bármilyen kimaradásról csökkentése és a felhasználók a gyakorolt hatást.

#### <a name="rolling-back"></a>Visszaállítása

Olvassa el az összevonási tervezési és üzembe helyezési dokumentációját az adott példány adatait. A folyamat megteszik a kellő:

* A felügyelt tartományokat használja a Convert-MSOLDomainToFederated összevont konvertálása 

* Szükség esetén további konfigurálás jogcímszabályokat.

### <a name="plan-change-communications"></a>Módosítsa a kommunikáció tervezése

Fontos része annak a tervezési, telepítési és a támogatási rendszer meggyőződött arról, hogy a végfelhasználók számára proaktív módon tisztában a módosításokat és mi is lehet, vagy tegye. 

Az átmenő hitelesítés és a közvetlen egyszeri bejelentkezés telepítése után a végfelhasználói bejelentkezési változnak, ha fér hozzá az Office 365-höz és más kapcsolódó erőforrások hitelesítése az Azure AD-n keresztül. A hálózaton kívüli felhasználók számára megjelenik az Azure AD bejelentkezési oldal csak átirányította a az űrlapos oldalra, a külső irányuló Webalkalmazásproxy-kiszolgálók által bemutatott szemben.

Több elemei a kommunikáció stratégiájának megtervezése. Ezek a következők:

* Közelgő és engedélyezett funkciók segítségével a felhasználók értesítése
  * E-mailekhez és más belső kommunikációs csatornákat
  * Vizualizációk, például poszterek
  * Vezetői élő vagy egyéb kommunikáció
* Ki fogja szabni, és akik küld a kommunikációhoz, és mikor.

## <a name="implementing-your-solution"></a>A megoldás megvalósítása

Most, hogy a megoldás megtervezése, készen áll implementálásáról. Végrehajtása a következő összetevőket tartalmazza:

   1. A zökkenőmentes egyszeri bejelentkezés előkészítése
   2. Bejelentkezési módszer az átmenő hitelesítés módosítása és közvetlen egyszeri bejelentkezés engedélyezése

## <a name="step-1--prepare-for-seamless-sso"></a>1. lépés – előkészítés a közvetlen egyszeri bejelentkezés

Az eszközök közvetlen egyszeri bejelentkezés használatára szüksége egy Azure AD URL-cím hozzáadása a felhasználók intranetes zóna beállításait az Active Directory csoportházirend használatával.

Alapértelmezés szerint a böngésző automatikusan kiszámolja a megfelelő zónához, internetes vagy intranetes, egy adott URL-címről. Például "http://contoso/"az Intranet zóna képez, mivel a"http://intranet.contoso.com/" rendeli az Internet zóna (mivel az URL-cím pont szerepel). Böngészők nem küldenek Kerberos-jegyekhez felhőbeli végpont, például az Azure AD URL-cím, kivéve, ha explicit módon fel az URL-CÍMÉT a böngésző intranetzónához az.

Kövesse a [felhasználócsoportra lépéseket](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) az eszközöknek a szükséges módosításokat.

> [!IMPORTANT]
> A módosítás elvégzése fogja módosítani az Azure AD a felhasználói bejelentkezés módját. Fontos azonban, a konfiguráció alkalmazása az összes eszközre, mielőtt folytatná a is a 3. lépés vegye figyelembe, hogy az eszközökön, amelyek nem érkezett meg ezt a konfigurációt bejelentkező felhasználókat egyszerűen kell adnia a felhasználónevét és jelszavát, hogy jelentkezzen be az Azure AD.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>2. lépés – a bejelentkezési módszer módosításához az ESP és a közvetlen egyszeri bejelentkezés engedélyezése

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>"A" lehetőség ESP konfigurálása az Azure AD Connect használatával

Ezt a módszert használja, ha az AD FS eredetileg konfigurálták az Azure AD Connect használatával. Ez a módszer nem használható, ha az AD FS eredetileg nem lett konfigurálva az Azure AD Connect használatával.

> [!IMPORTANT]
> Ne feledje, hogy minden tartományban az alábbi lépéseket követve lesznek konvertálva, az összevont felügyelt. Tekintse át a szakasz áttelepítési módszer megtervezése további információt.[](#_Plan_Migration_Method)

Először rendelkezik bejelentkezési módszerének módosítása:

   1. Az Azure AD Connect kiszolgáló, a varázsló megnyitásához.
   2. Válassza ki **módosítása felhasználói bejelentkezési** majd **tovább**. 
   3. Az a **az Azure AD Connect** képernyőn adja meg a felhasználónevet és a egy globális rendszergazdai jelszót.
   4. A **felhasználói bejelentkezés** képernyőn, majd a választógombot, a **és az AD FS összevonási** való **átmenő hitelesítés**válassza **egyszeri bejelentkezés engedélyezése**  majd **tovább**.
   5. Engedélyezze az egyszeri bejelentkezés képernyőn adja meg a tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a Tovább gombra.  

   > [!NOTE]
   > Tartományi rendszergazda hitelesítő adataira szükség, a folyamat a következő ezeket az emelt szintű engedélyeket igénylő műveleteket hajt végre közvetlen egyszeri bejelentkezés engedélyezése. A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően
   >
   > * Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory (AD).
   > * A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   > * Emellett két Kerberos egyszerű szolgáltatásnevek (SPN) jön létre, amelyek a két URL-címet, amelyekre az Azure AD-be.
   > * A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően

   6. Az a **készen áll a konfigurálás** képernyőn, ellenőrizze, hogy **Start szinkronizálási folyamat befejezése után a konfigurációs** jelölőnégyzet be van jelölve. Válassza ki **konfigurálása**.</br>
   ![kép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Nyissa meg a **Azure AD portálon**válassza **Azure Active Directory**, majd válassza ki **az Azure AD Connect**.
   8. Ellenőrizze, hogy, amely **összevonási le van tiltva** közben **zökkenőmentes egyszeri bejelentkezési** és **Pass-alapos hitelesítési** vannak **engedélyezve**.</br>
   ![kép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Ezután további hitelesítési módszereket kell telepítenie. Nyissa meg a **az Azure portal**, keresse meg a **Azure Active Directory, az Azure AD Connect** kattintson **átmenő hitelesítés**.

Az a **átmenő hitelesítés** lap, kattintson a Letöltés gombra. Az a **letöltése** ügynök képernyő, kattintson a **feltételek elfogadása és letöltés**.

A további hitelesítési ügynökök letöltést. Telepítse a másodlagos hitelesítési ügynök egy tartományhoz csatlakoztatott kiszolgálón. 

> [!NOTE]
> Az első ügynök mindig telepítve van az Azure AD Connect-kiszolgáló magát a konfigurációs módosítások, a felhasználói bejelentkezés szakaszban az Azure AD Connect eszköz részeként. Minden olyan további hitelesítési ügynökök külön kiszolgálóra kell telepíteni. Azt javasoljuk, hogy a 2-3 további hitelesítési ügynökök között érhető el. 

Futtassa a hitelesítési ügynök telepítése. A telepítés során meg kell adnia hitelesítő adatait, egy **globális rendszergazdai** fiókot.

![Kép 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Kép 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

A hitelesítési ügynök telepítése után léphet vissza az átmenő hitelesítési ügynök health-oldal a további ügynökök állapotának ellenőrzéséhez.


Ugrás a tesztelés és a következő lépéseket.

> [!IMPORTANT]
> Hagyja ki a szakaszt b lehetőség Váltson a összevonási ESP használatával az Azure AD Connect és a PowerShell, a lépéseket, hogy a szakasz csak akkor érvényesíthetők.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>A beállítás B - kapcsolót a összevonási ESP az Azure AD Connect és a PowerShell használatával

Használja ezt a beállítást, ha az összevonási kezdetben nem konfigurálták az Azure AD Connect használatával. Először engedélyeznie kell az átmenő hitelesítés:

   1. Az Azure AD Connect kiszolgáló, a varázsló megnyitásához.
   2. Válassza ki **módosítása felhasználói bejelentkezési** majd **tovább**.
   3. Az a **az Azure AD Connect** képernyőn adja meg a felhasználónevet és a egy globális rendszergazdai jelszót.
   4. A a **felhasználói bejelentkezés** képernyőn, majd a választógombot, a **ne konfiguráljon** a **átmenő hitelesítés**, jelölje be **egyszeri bejelentkezésengedélyezése** majd **tovább**.
   5. A **engedélyezése az egyszeri bejelentkezés** képernyőn, adja meg a tartomány rendszergazdai fiókjának hitelesítő adatait, majd válassza ki **tovább**.

   > [!NOTE]
   > Tartományi rendszergazda hitelesítő adataira szükség, a folyamat a következő ezeket az emelt szintű engedélyeket igénylő műveleteket hajt végre közvetlen egyszeri bejelentkezés engedélyezése. A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően.
   >
   > * Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory (AD).
   > * A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   > * Emellett két Kerberos egyszerű szolgáltatásnevek (SPN) jön létre, amelyek a két URL-címet, amelyekre az Azure AD-be.

   6. Az a **készen áll a konfigurálás** képernyőn, ellenőrizze, hogy **Start szinkronizálási folyamat befejezése után a konfigurációs** jelölőnégyzet be van jelölve. Válassza ki **konfigurálása**.</br>
   ‎![kép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   A következő lépések történnek beállítás kiválasztásakor:
   * Az első átmenő hitelesítési ügynök telepítve van
   * A csatlakoztatott szolgáltatás engedélyezve van
   * Közvetlen egyszeri bejelentkezés engedélyezve van.  
   7. Ellenőrizze, hogy **összevonási** továbbra is **engedélyezve** és **zökkenőmentes egyszeri bejelentkezést** és **Pass-alapos hitelesítési** mostantól engedélyezve van.
   ![2. kép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Válassza ki **átmenő hitelesítés** , és győződjön meg arról, hogy a állapota **aktív**.</br>
   
   Ha a hitelesítési ügynök nem aktív, hajtsa végre a [ezeket a hibaelhárítási lépéseket](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) a beszélgetés folyamat a következő lépés végrehajtása előtt. Így fennáll a veszélye hitelesítési ami kimaradást okoz a tartományok ellenőrzése előtt át, az ESP ügynökök sikeresen telepítette és, hogy azok állapota "Aktív" az Azure Portalon.  
   9. Ezután telepítheti a további hitelesítési ügynököket. Nyissa meg a **az Azure portal**, keresse meg a **Azure Active Directory**, **az Azure AD Connect** kattintson **átmenő hitelesítés**.
   10. Az a **átmenő hitelesítés** lapon a **letöltése** gombra. Az a **-ügynök letöltése** lapon kattintson a **feltételek elfogadása és letöltés**.
   
   A további hitelesítési ügynökök letöltést. Telepítse a másodlagos hitelesítési ügynök egy tartományhoz csatlakoztatott kiszolgálón.

  > [!NOTE]
  > Az első ügynök mindig telepítve van az Azure AD Connect-kiszolgáló magát a konfigurációs módosítások, a felhasználói bejelentkezés szakaszban az Azure AD Connect eszköz részeként. Minden olyan további hitelesítési ügynökök külön kiszolgálóra kell telepíteni. Azt javasoljuk, hogy a 2-3 további hitelesítési ügynökök között érhető el.
  
   11. Futtassa a hitelesítési ügynök telepítése. A telepítés során meg kell adnia hitelesítő adatait, egy **globális rendszergazdai** fiókot.</br>
   ![Kép 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Kép 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. A hitelesítési ügynök telepítése után léphet vissza az átmenő hitelesítési ügynök health-oldal a további ügynökök állapotának ellenőrzéséhez.

Ezen a ponton összevonási továbbra is engedélyezett és a tartományok működési áll. A telepítés folytatásához, minden egyes tartományhoz kell alakítható át az összevont felügyelt, az átmenő hitelesítés elindítja a tartományhoz tartozó hitelesítési kérelmek.

Nem minden tartományban kell a konvertálható egy időben, a kezdéshez választhatja az éles környezetbeli bérlőhöz vagy a tartomány legalább egy teszt tartomány felhasználók száma.

Az átalakítás az Azure AD PowerShell modul használatával történik.

   1. Nyissa meg **PowerShell** , és jelentkezzen be az Azure AD-t egy **globális rendszergazdai** fiókot.
   2. Konvertálja az első tartományt, futtassa a következő parancsot:
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Nyissa meg a **Azure AD portálon**válassza **Azure Active Directory**, majd válassza ki **az Azure AD Connect**.  
   4. Miután az összevont tartományok szükséges formába átalakítani, ellenőrizze, hogy, amely **összevonási le van tiltva** közben **zökkenőmentes egyszeri bejelentkezést** és **átmenő hitelesítés** vannak **Engedélyezve**.</br>
   ![kép](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Tesztelés és a következő lépések

### <a name="test-pass-through-authentication"></a>Átmenő hitelesítés tesztelése 

A bérlő összevonási rendszer használata esetén felhasználók voltak oldalra való átirányítást a az Azure AD bejelentkezési oldal az AD FS-környezethez. Most, hogy a bérlő összevonási helyett az átmenő hitelesítés használatára van konfigurálva, a felhasználók nem get átirányítja az AD FS-hez, és ehelyett alá fogja írni közvetlenül az Azure AD bejelentkezési oldal.

Nyissa meg az Internet Explorer elkerülése érdekében a közvetlen egyszeri bejelentkezés automatikus bejelentkezés, és nyissa meg az Office 365 bejelentkezési oldala InPrivate módban ([http://portal.office.com](http://portal.office.com/)). Írja be a **UPN** a felhasználó, és kattintson a **tovább**. Ellenőrizze, hogy írja be, amelyeket a rendszer a helyszíni Active Directoryból szinkronizált, és akik korábban már volt összevont hibrid felhasználó egyszerű Felhasználóneve. A felhasználók számára megjelenik a képernyőn írja be a felhasználónevével és jelszavával.

![Kép 18-ra](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Kép 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

Miután megadta a jelszót, az Office 365 portálra első átirányítva.

![Kép: 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Közvetlen egyszeri bejelentkezés tesztelése

   1. Jelentkezzen be egy tartományhoz csatlakoztatott gép, amely a vállalati hálózathoz csatlakozik. 
   2. Nyissa meg **az Internet Explorer** vagy **Chrome** , és folytassa a következő URL-címek egyikére:   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (cserélje le a tartomány Contoso).

      A felhasználó röviden irányítja az Azure AD bejelentkezési oldalra, és a "Próbál a bejelentkezés" üzenet jelenik meg, és nem felkéri a felhasználónév vagy jelszó.</br>
   ![Kép 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Ezután a felhasználó fog első irányítja át, és sikeresen bejelentkezett a hozzáférési panelen:

> [!NOTE]
> Zökkenőmentes egyszeri bejelentkezést az Office 365-szolgáltatásokhoz (például myapps.microsoft.com/contoso.com) tartományemlékeztető támogató működik. Az Office 365 portálon (portal.office.com) jelenleg nem támogatja a tartományemlékeztető, és ezért valószínű, hogy a felhasználóknak kell-e egyszerű típusra. Után egy egyszerű felhasználónév megadott, zökkenőmentes egyszeri bejelentkezést az lekérni a Kerberos-jegy, a felhasználó nevében, és jelentkezzen őket a jelszó beírása nélkül.
> [!TIP]
> Érdemes megfontolni a [a hibrid Azure AD Join a Windows 10-es](https://docs.microsoft.com/azure/active-directory/device-management-introduction) továbbfejlesztett egyszeri bejelentkezési élményt biztosít.

### <a name="removal-of-the-relying-party-trust"></a>A függő entitás megbízhatóságához eltávolítása

Miután ellenőrizte, hogy minden felhasználó és az ügyfelek vannak a sikeres hitelesítés az Azure AD-n keresztül, azt is figyelembe kell venni az Office 365, a függő entitás megbízhatóságának eltávolítása biztonságos.

Ha az AD FS (más függő entitás Megbízhatóságai vannak konfigurálva), más célra nem használja, már biztonságosan leszerelése mostantól az AD FS.

### <a name="rollback"></a>Visszaállítás

Ha egy súlyos problémát talál, és nem oldható fel, dönthet, hogy állítsa vissza a megoldás összevonási biztonsági.

Olvassa el az összevonási tervezési és üzembe helyezési dokumentációját az adott példány adatait. A folyamat megteszik a kellő:

* A felügyelt tartományokat használja a Convert-MSOLDomainToFederated összevont konvertálása
* Szükség esetén további konfigurálás jogcímszabályokat.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>A userPrincipalName frissítések szinkronizálásának engedélyezése

Hagyományosan a UserPrincipalName attribútum a helyszínről a szinkronizálási szolgáltatás használata frissítések le van tiltva, kivéve, ha az alábbi két feltétel teljesül:

* A felhasználó felügyelt (nem összevont).
* A felhasználó nem lett hozzárendelve licenc.

Győződjön meg arról, vagy a funkció engedélyezéséhez utasításokért tekintse meg a következő cikket:

[UserPrincipalName frissítések szinkronizálása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Vihetők át a zökkenőmentes egyszeri bejelentkezési Kerberos visszafejtési kulcs

Fontos, hogy milyen gyakran vihetők át a Kerberos visszafejtési kulcs (amely az Azure AD) AZUREADSSOACC számítógépfiók létrehozása a helyszíni AD-erdőben. Kifejezetten ajánljuk, hogy hosszabbítsa a Kerberos-visszafejtési kulcs legalább 30 naponta igazodva hogyan be az Active Directory-tartomány tagjai a jelszó módosítására. Társított eszköz sem AZUREADSSOACC számítógépfiók-objektumból csatlakozik, a helyettesítő kell manuálisan kell elvégezni.

Kövesse az alábbi lépéseket a helyszíni kiszolgálón vihetők át a Kerberos-visszafejtési kulcs kezdeményezni az Azure AD Connect futtatja.

[Hogyan visszaválthatok keresztül a Kerberos visszafejtési kulcs AZUREADSSOACC számítógépfiók](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Megoldás rendelkezésre állásának a hitelesítési ügynökök futtató kiszolgálókat kell figyelni. Kiszolgálóteljesítmény-számlálók általános, valamint a hitelesítési ügynökök használható hitelesítési statisztikák és hibák teljesítményobjektummal teszik elérhetővé.

Hitelesítési ügynökök operations jelentkezzen Windows eseménynaplók "Alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin" alatt.

Hibaelhárítási naplók is engedélyezhető, ha szükséges.

További információk: [monitorozási és naplózási](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>További lépések

- [Az Azure AD Connect tervezési alapelvei](plan-connect-design-concepts.md)
- [Válassza ki a megfelelő hitelesítés](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Támogatott topológiák](plan-connect-design-concepts.md)
