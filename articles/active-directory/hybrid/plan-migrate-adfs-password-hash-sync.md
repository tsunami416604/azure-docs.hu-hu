---
title: 'Azure AD Connect: A Jelszókivonat-szinkronizálás az Azure ad összevonási áttelepít |} A Microsoft Docs'
description: Ez a cikk áthelyezi a hibrid identitáskezelési környezetet a összevonási Jelszókivonat-szinkronizálás kapcsolatos információkat tartalmaz.
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
ms.openlocfilehash: 988c3364bf3bb3c76536c043fd6733599719fdc7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435923"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>A Jelszókivonat-szinkronizálás az Azure Active Directory összevonási áttelepítésére

Ez a cikk ismerteti, hogyan helyezheti át a szervezet tartományok Jelszókivonat-szinkronizálás az Active Directory összevonási szolgáltatások (AD FS).

Is [töltse le a cikk](https://aka.ms/ADFSTOPHSDPDownload).

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>A Jelszókivonat-szinkronizálás történő áttelepítésre vonatkozó Előfeltételek

A következő előfeltételek szükségesek áttelepítéséhez a Jelszókivonat-szinkronizálást az AD FS használatával.

### <a name="update-azure-ad-connect"></a>Frissítés Azure AD Connect

Jelszókivonat-szinkronizálás használatával való áttelepítéséhez szükséges lépések sikeres végrehajtásához rendelkeznie kell [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 vagy újabb verziója. Az Azure AD Connectben 1.1.819.0 bejelentkezési átalakítás módon végrehajtott módosítások jelentősen. Áttelepítés az AD FS a felhőalapú hitelesítés ebben a verzióban a teljes időt potenciálisan óra perc csökken.

> [!IMPORTANT]
> Előfordulhat, hogy olvassa el az elavult dokumentáció, eszközök és blogok, hogy felhasználók átalakítását kötelező konvertálásakor tartományok az összevont identitás felügyelt identitás. *Felhasználók átalakítása* már nem szükséges. A Microsoft dolgozik azon, hogy frissítse a dokumentáció és eszközök a változás tükrözése érdekében.

Az Azure AD Connect-frissítéséhez hajtsa végre a lépéseit [az Azure AD Connect: Frissítés a legújabb verzióra a](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Jelszó Jelszókivonat-szinkronizálás szükséges engedélyek

Az Azure AD Connect egyéni telepítés vagy a gyorsbeállítások használatával konfigurálhatja. Az egyéni telepítési lehetőség használatakor a [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) a jelszókivonatok szinkronizálása nem feltétlenül helyen.

Az Azure AD Connect az Active Directory tartományi szolgáltatások (AD DS) szolgáltatásfiók a jelszókivonatok szinkronizálása a következő engedélyekkel kell rendelkeznie:

* Címtárváltozások replikálása
* Replikálás könyvtár összes változik

Most már van ideje, hogy ezeket az engedélyeket az erdőben lévő összes tartományban helyen.

### <a name="plan-the-migration-method"></a>Az áttelepítési módszer megtervezése

A Jelszókivonat-szinkronizálás és a zökkenőmentes egyszeri bejelentkezés (SSO) összevont identitáskezelési át két módszer közül választhat. A használt módszer attól függ, hogyan eredetileg konfigurálták az AD FS-példányhoz.

* **Az Azure AD Connect**. Ha az eredetileg konfigurálták az AD FS az Azure AD Connect használatával, *kell* módosítása a Jelszókivonat-szinkronizálás az Azure AD Connect varázsló használatával.

   Az Azure AD Connect automatikusan futtatja a **Set-MsolDomainAuthentication** parancsmagot, ha a felhasználó bejelentkezési módszert. Az Azure AD Connect automatikusan unfederates ellenőrzött összevont tartományok az Azure AD-bérlőben.

   > [!NOTE]
   > Jelenleg Ha eredetileg használt AD FS konfigurálása az Azure AD Connect, nem kerülheti el a bérlőn belüli összes tartomány unfederating, amikor módosítja a felhasználói bejelentkezés a Jelszókivonat-szinkronizálás. ‎
* **A PowerShell-lel az Azure AD Connect**. Használhatja ezt a módszert csak akkor, ha nem AD FS eredeti konfigurálásához az Azure AD Connect használatával. Ezt a beállítást továbbra is módosítania kell a felhasználói bejelentkezési módszer az Azure AD Connect varázsló segítségével. Ezzel a beállítással alapvető különbség az, hogy a varázsló automatikusan nem futtatható a **Set-MsolDomainAuthentication** parancsmagot. Ezzel a lehetőséggel rendelkezik teljes hozzáférés felett, hogy mely tartományok lesznek konvertálva, és milyen sorrendben.

Szeretné megtudni, melyik módszert használja, hajtsa végre a következő szakaszok lépéseit.

#### <a name="verify-current-user-sign-in-settings"></a>Ellenőrizze a jelenlegi felhasználói bejelentkezési beállítások

Annak ellenőrzése, a jelenlegi felhasználói bejelentkezési beállításai:

1. Jelentkezzen be a [Azure AD portálon](https://aad.portal.azure.com/) egy globális rendszergazdai fiókkal.
2. Az a **felhasználói bejelentkezés** területen ellenőrizze a következő beállításokat:
   * **Összevonási** értékre van állítva **engedélyezve**.
   * **Zökkenőmentes egyszeri bejelentkezést** értékre van állítva **letiltott**.
   * **Az átmenő hitelesítés** értékre van állítva **letiltott**.

   ![Képernyőkép a beállítások az Azure AD Connect felhasználói bejelentkezési szakaszban](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Az Azure AD Connect konfiguráció ellenőrzése

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect. Válassza ki **konfigurálása**.
2. Az a **további feladatok** lapon jelölje be **aktuális konfiguráció megtekintése**, majd válassza ki **tovább**.<br />

   ![A nézet jelenlegi konfigurációs beállítás további feladatok lapon kiválasztott képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Az a **a megoldás áttekintése** lapon, vegye figyelembe a **Jelszókivonat-szinkronizálás** állapotát.<br /> 

   * Ha **Jelszókivonat-szinkronizálás** értékre van állítva **letiltott**, hajtsa végre a lépéseket ebben a cikkben az engedélyezéshez.
   * Ha **Jelszókivonat-szinkronizálás** értékre van állítva **engedélyezve**, a szakaszt kihagyhatja **1. lépés: Jelszókivonat-szinkronizálás engedélyezése** ebben a cikkben.
4. Az a **a megoldás áttekintése** területén görgessen **Active Directory összevonási szolgáltatások (AD FS)**.<br />

   * Ha ez a szakasz jelenik meg az AD FS konfigurációt, biztonságosan feltételezzük, hogy az AD FS eredetileg konfigurálták az Azure AD Connect használatával. Átalakíthatja a tartományok összevont identitás felügyelt identitás, az Azure AD Connect használatával **felhasználói bejelentkezés módosítása** lehetőséget. A folyamat részleteit a szakasz a **beállítás v: Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect használatával**.
   * Ha az AD FS nem szerepel az aktuális beállítások, meg kell manuálisan átalakítása a tartományok összevont identitás felügyelt identitás PowerShell használatával. Ez a folyamat kapcsolatos további információkért lásd: a szakasz **b lehetőség Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect és a PowerShell használatával**.

### <a name="document-current-federation-settings"></a>A dokumentum aktuális összevonási beállítások

Az aktuális összevonási beállítások megkereséséhez futtassa a **Get-MsolDomainFederationSettings** parancsmagot:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Példa:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Ellenőrizze az azokat a beállításokat, előfordulhat, hogy testre szabott az összevonási tervezési és telepítési dokumentációját. Különös tekintettel a testreszabásokat a **PreferredAuthenticationProtocol**, **SupportsMfa**, és **PromptLoginBehavior**.

További információval a következő cikkek szolgálnak:

* [Az AD FS parancssor = bejelentkezési paraméter támogatása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Ha **SupportsMfa** értékre van állítva **igaz**, egy a helyszíni többtényezős hitelesítési megoldás használatával a felhasználó hitelesítési folyamat második tényezős kihívást behelyezése. Ez a beállítás már nem működik az Azure AD hitelesítési forgatókönyvei. 
>
> Ehelyett a felhőalapú Azure multi-factor Authentication szolgáltatás használatával hajtsa végre ugyanezt a funkciót. A folytatás előtt gondosan mérlegelje a multi-factor authentication követelményeinek. Mielőtt megkezdené a tartományok, győződjön meg arról, hogy ismeri az Azure multi-factor Authentication, a licencelési szempontokat, és a felhasználói regisztrációs folyamat használata.

#### <a name="back-up-federation-settings"></a>Összevonási beállítások biztonsági mentése

Bár nem módosul a más függő entitások, az AD FS farmon, a jelen cikkben ismertetett folyamatok során, azt javasoljuk, hogy az AD FS farmon, visszaállíthatja az aktuális érvényes biztonsági másolata. Az ingyenes Microsoft használatával is létrehozhat a jelenlegi érvényes biztonsági másolat [AD FS gyors visszaállítási eszköz](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Készítsen biztonsági másolatot az AD FS-ben és a egy meglévő farmra visszaállítása, vagy hozzon létre egy új farmot is használhatja az eszközt.

Ha nem kíván használni az AD FS gyors visszaállítási eszköz minimális, exportálnia kell a Microsoft Office 365 Identity Platform függő entitás megbízhatósága és hozzáadott társított egyéni jogcímszabályokat. A függő entitás megbízhatósága és a kapcsolódó jogcímszabályok exportálása, az alábbi PowerShell-példa:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Telepítési szempontokról és az AD FS használatával

Ez a szakasz ismerteti a telepítési szempontokról és a részletek az AD FS használatával kapcsolatban.

### <a name="current-ad-fs-use"></a>Aktuális Active Directory összevonási szolgáltatások használata

Mielőtt megkezdené az összevont identitás felügyelt identitás, példakódot jelenleg a használatának módja az AD FS az Azure ad, az Office 365-höz és más alkalmazások (függő entitás megbízhatóságai). Pontosabban vegye figyelembe, hogy a következő táblázat ismerteti:

| Ha | majd |
|-|-|
| Azt tervezi, hogy tovább használhassa a az AD FS más alkalmazásokkal (az Azure AD-től eltérő és az Office 365). | Miután átalakítja a tartományok, az AD FS és az Azure AD fog használni. Fontolja meg a felhasználói élményt. Bizonyos példahelyzetekben a felhasználók hitelesítéséhez kétszer szükség lehet: egyszer az Azure AD-(ahol a felhasználó más alkalmazások, például az Office 365 egyszeri bejelentkezés hozzáférést kap), és újra olyan alkalmazások, mint egy függőentitás-megbízhatóságot az AD FS továbbra is vannak kötve. |
| Az AD FS-példány nagymértékben testre szabott és támaszkodik a onload.js fájlban meghatározott testreszabási beállítások (például ha módosította a bejelentkezési élmény, hogy a felhasználók használhatják csak egy **SamAccountName** felhasználónevének formátumát egy felhasználó helyett egyszerű felhasználónév (UPN), vagy a szervezet rendelkezik az erősen márkás a bejelentkezési élmény). A onload.js fájl nem duplikálható az Azure AD-ben. | A folytatás előtt ellenőriznie kell, hogy az Azure AD aktuális testreszabási igényeinek megfelel. További információt és útmutatást lásd a az AD FS márkajelzési és az AD FS testreszabás.|
| Az AD FS használatával blokkolja a hitelesítési ügyfelek korábbi verzióiban.| Fontolja meg, és cserélje le az AD FS vezérlők együttes használatával tiltsa le a hitelesítési ügyfelek korábbi verziói [feltételes hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) és [Exchange Online ügyfél-hozzáférési szabályok](http://aka.ms/EXOCAR). |
| Felhasználók a multi-factor authentication szolgáltatás egy a helyszíni multi-factor authentication kiszolgáló megoldás elleni végrehajtásához, amikor a felhasználók hitelesítéséhez az AD FS-hez van szüksége.| Egy felügyelt identitás a tartományban a multi-factor authentication kihívást keresztül a helyszíni többtényezős hitelesítési megoldás nem behelyezése a hitelesítési folyamatát. Használhatja azonban a multi-factor authentication az Azure multi-factor Authentication szolgáltatás, a tartomány átalakítása után.<br /><br /> Ha a felhasználók jelenleg nem használja az Azure multi-factor Authentication szolgáltatás, a egy onetime felhasználói regisztrációs lépésre szükség. Kell előkészítése és a tervezett regisztrációs kommunikálni a felhasználók számára. |
| Jelenleg használja a hozzáférés-vezérlési házirendeket (engedélyezési szabályok) az AD FS Office 365-höz való hozzáférés szabályozásához.| Fontolja meg, és cserélje le a szabályzatok Azure ad-ben egyenértékű [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) és [Exchange Online ügyfél-hozzáférési szabályok](http://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Közös AD FS Testreszabás

Ez a szakasz ismerteti a gyakori AD FS testreszabás.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork jogcím

Az AD FS-problémákat a **InsideCorporateNetwork** jogcímet, ha a felhasználó hitelesíti magát a vállalati hálózaton belül van-e. Ez a jogcím majd adható át Azure ad-ben. Az igényt a felhasználó hálózati helye alapján multi-factor authentication kihagyásához szolgál. Annak megállapítása, hogy ez a funkció jelenleg engedélyezve van az AD FS kapcsolatban lásd: [megbízható IP-címek összevont felhasználók esetében](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

A **InsideCorporateNetwork** jogcím nem érhető el, miután a tartományok alakítja a Jelszókivonat-szinkronizálás. Használhat [nevesített helyek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) cserélje le ezt a funkciót.

Nevesített helyek konfigurálása, frissítenie kell az összes olyan feltételes hozzáférési szabályzatok belefoglalása vagy kizárása a hálózati konfigurált **minden megbízható hely** vagy **MFA megbízható IP-címek** helyezendő értékek tükrözik az új nevesített helyek.

További információ a **hely** azzal a feltételes hozzáférés feltétellel, lásd: [Active Directory feltételes hozzáférés helyek](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakoztatott eszközök

Eszköz csatlakoztatása az Azure AD, ha feltételes hozzáférési szabályokat, hogy az eszközök megfelelnek-e a hozzáférés biztonsági és megfelelőségi szabványainak kikényszerítő hozhat létre. Ezenkívül felhasználók jelentkezhetnek be az eszközökre egy szervezeti munkahelyi vagy iskolai fiók helyett egy személyes fiók. Használatakor a hibrid Azure AD-hez csatlakoztatott eszközök a csatlakozhat az Active Directory-tartományhoz csatlakoztatott eszközök Azure ad-hez. Az összevont környezetben előfordulhat, hogy van beállítva a funkció használatához.

Annak érdekében, hogy a hibrid való csatlakozás után a tartományok alakulnak a Jelszókivonat-szinkronizálást, a Windows 10-ügyfelek, a tartományhoz csatlakoztatott eszközök számára továbbra is az Azure AD Connect szinkronizálása az Azure AD az Active Directory számítógépfiókok kell használnia. 

Windows 8 és Windows 7-fiókok hibrid csatlakozás zökkenőmentes egyszeri Bejelentkezést használ a számítógép regisztrálása az Azure ad-ben. Nem kell a szinkronizálás a Windows 8 és Windows 7 számítógépfiókokat, mint amikor a Windows 10 rendszerű eszközökhöz. Azonban telepítenie kell egy frissített workplacejoin.exe fájlt (.msi fájl) n keresztül a Windows 8 és Windows 7-ügyfelek így azok regisztrálhatják magukat közvetlen egyszeri bejelentkezés használatával. [Töltse le az .msi fájl](https://www.microsoft.com/download/details.aspx?id=53554).

További információkért lásd: [konfigurálása hibrid Azure AD-hez csatlakoztatott eszközök](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Védjegyezés

Ha a szervezet [testre szabott az AD FS bejelentkezési oldalainak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) szeretné megjeleníteni a szervezet több profiljával kapcsolatos információkat, fontolja meg, így hasonló [az Azure AD bejelentkezési oldal testreszabása](https://docs.microsoft.com/azure/active-directory/customize-branding).

A testreszabások hasonló ugyan elérhető, néhány Vizualizáció módosul a bejelentkezési lapok várható az átalakítás után. Érdemes lehet, hogy a kommunikáció várható változásairól információt nyújt a felhasználók számára.

> [!NOTE]
> Szervezet márkajelzési áll rendelkezésre, csak akkor, ha az Azure Active Directory prémium vagy alapszintű licenc megvásárlása, vagy ha Office 365-licenccel rendelkezik.

## <a name="plan-deployment-and-support"></a>Üzembe helyezés és a támogatási csomag

A telepítésre és a támogatás megtervezéséhez ebben a szakaszban ismertetett feladatok végrehajtásához.

### <a name="plan-the-maintenance-window"></a>A karbantartási időszak tervezése

Bár a konvertálási folyamat viszonylag rövid, Azure ad-ben továbbra is az AD FS-kiszolgálók egyes hitelesítési kérelmek küldése a tartomány átalakítása befejezése után akár négy óra. A négy órás időszak alatt, és függően különböző szolgáltatás oldalán gyorsítótárakhoz az Azure AD előfordulhat, hogy fogadja el ezeket a hitelesítések. Felhasználók kaphat hibaüzenetet. A felhasználó sikeresen továbbra is az AD FS a hitelesítésre, de már nem fogad az Azure AD, a felhasználó jogkivonatot állít ki, mert pedig mostantól eltöröltük, hogy összevonási megbízhatósági kapcsolatot.

Érintett csak elérő felhasználók a szolgáltatások webböngészőn keresztül e konvertálás utáni időszak előtt a szolgáltatás ügyféloldali gyorsítótár nincs bejelölve. Hagyományos (Exchange ActiveSync, az Outlook 2010 vagy 2013-hoz) hatással lesz, mert az Exchange online-ban tartja a hitelesítő adatait tartalmazó gyorsítótár számára egy megadott ideig nem várt. A gyorsítótár segítségével csendes hitelesítse magát újra a felhasználónak. A felhasználó nem rendelkezik az AD FS való visszatéréshez. Ezek az ügyfelek az eszközön tárolt hitelesítő adatok segítségével csendes hitelesítse magát újra maguk után a gyorsítótárazott ez nincs bejelölve. Felhasználók nem várható, hogy a konvertálási folyamat eredményeként a jelszó utasításokat kapnak. 

Modern hitelesítési ügyfelek (az Office 2016 és Office 2013, iOS és Android-alkalmazások) egy érvénytelen frissítési jogkivonat használatával e folyamatos hozzáférésre erőforrások AD FS visszaadása helyett új hozzáférési jogkivonatok beszerzésére. Ezek az ügyfelek kapcsolódnak a jelszó utasításokat a konvertálási folyamat eredő hagyja. Az ügyfelek továbbra is működnek, további konfiguráció nélkül.

> [!IMPORTANT]
> Ne állítsa le az AD FS környezetének, vagy távolítsa el az Office 365 függőentitás-megbízhatóságot, amíg nem ellenőrizte, hogy minden felhasználó sikeresen hitelesítik a felhőalapú hitelesítés használatával.

### <a name="plan-for-rollback"></a>Visszaállítás tervezése

Ha egy nem oldható fel gyorsan fő problémával találkozik, dönthet, hogy összevonási való visszatérés lehetőségét a megoldást. Mi a teendő, ha az üzemelő példány nem bevezetése helyesen megtervezéséhez fontos. Ha a tartomány vagy a felhasználók átalakítás sikertelen üzembe helyezés során, vagy meg kell visszaállítania az összevonási, ismernie kell a bármilyen kimaradásról csökkentése és a felhasználók számára. a hatás csökkentésére.

#### <a name="to-roll-back"></a>Vissza

Visszaállítási tervezése, olvassa el az adott központi telepítési adatait összevonási tervezési és üzembe helyezési dokumentációját. A folyamat ezeket a feladatokat kell tartalmaznia:

* A felügyelt tartomány átalakítása összevont tartományok használatával a **Convert-MSOLDomainToFederated** parancsmagot.
* Szükség esetén további konfigurálás jogcímszabályokat.

### <a name="plan-communications"></a>A kommunikáció tervezése

Üzembe helyezés és a támogatás megtervezése fontos része annak ellenőrzése, hogy a felhasználók kapjanak proaktív módon tájékozódhat a jövőbeni változtatásokról. Felhasználók előzetesen érdemes tudni, mi lehet tapasztalható, és mire szükség, ezek közül. 

A Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés telepítése után a felhasználói bejelentkezési élmény a Office 365-höz és más erőforrások, amelyek a hitelesítés az Azure AD-módosítások keresztül éri el a elemet. Felhasználók, akik a hálózaton kívül vannak csak az Azure AD bejelentkezési lapján talál. Ezek a felhasználók nem irányítja át a kívülre irányuló webalkalmazás-proxy kiszolgálók által bemutatott űrlapalapú lap.

A kommunikáció stratégia a következő elemeket tartalmazza:

* Közelgő és engedélyezett funkciók a felhasználók értesítése használatával:
   * E-mailekhez és más belső kommunikációs csatornákat.
   * Vizualizációk, például poszterek.
   * Vezetői, élő vagy egyéb kommunikáció.
* Határozza meg, akik fogja szabni a kommunikációhoz, és akik küld a kommunikációhoz, és mikor.

## <a name="implement-your-solution"></a>A megoldás megvalósítása

A megoldás tervezett. Most már valósíthat meg azt. Megvalósítás az alábbi összetevőkből áll:

* Jelszókivonat-szinkronizálás engedélyezése.
* Közvetlen egyszeri bejelentkezés előkészítése.
* A bejelentkezési mód módosítása a Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés engedélyezése.

### <a name="step-1-enable-password-hash-synchronization"></a>1. lépés: Jelszókivonat-szinkronizálás engedélyezése

A megoldás első lépése, hogy a Jelszókivonat-szinkronizálás engedélyezése az Azure AD Connect varázsló használatával. A Jelszókivonat-szinkronizálás az engedélyezhető a környezetekben, amelyek összevonási opcionális szolgáltatása. A hitelesítési folyamat nincs hatással van. Ebben az esetben az Azure AD Connect indul el anélkül, hogy befolyásolná a felhasználók, akik az összevonási használatával írja alá jelszókivonatok szinkronizálása.

Ebből kifolyólag javasoljuk, hogy befejezéséhez ebben a lépésben egy előkészítési tevékenység jól, a tartományi bejelentkezési mód módosítása előtt. Ezt követően, akkor győződjön meg arról, hogy a Jelszókivonat-szinkronizálás megfelelően működik-e bőséges időt.

A Jelszókivonat-szinkronizálás engedélyezése:

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect varázslót, és válassza **konfigurálása**.
2. Válassza ki **szinkronizálási beállítások testreszabása**, majd válassza ki **tovább**.
3. Az a **az Azure AD Connect** lap, adja meg a felhasználónevet és a egy globális rendszergazdai fiók jelszavát.
4. Az a **csatlakoztassa a címtárakat** lapon jelölje be **tovább**.
5. A a **tartomány és szervezeti egységek szűrése** lapon jelölje be **tovább**.
6. Az a **választható funkciók** lapon jelölje be **jelszó-szinkronizálás**, majd válassza ki **tovább**.
 
   ![A jelszó-szinkronizálási lehetőséggel, a választható funkciók lapon kiválasztott képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Válassza ki **tovább** a fennmaradó lapokon. Az utolsó oldalon válassza ki a **konfigurálása**.
8. Az Azure AD Connect elindul, és a következő szinkronizálás jelszókivonatok szinkronizálása.

A Jelszókivonat-szinkronizálás engedélyezése után a jelszót kivonatolja, az Azure AD Connect szinkronizálási hatókör rehashed, és az Azure ad Szolgáltatásba írt összes felhasználója esetében. A felhasználók számától függően ez a művelet percet vagy akár több órát vehet igénybe.

Tervezési célokra, meg kell becsülni, hogy körülbelül 20 000 felhasználóhoz (1 óra) feldolgozása.

Győződjön meg arról, hogy a Jelszókivonat-szinkronizálás megfelelően működik-e, végezze el a **hibaelhárítás** feladat az Azure AD Connect varázslóban:

1. Nyissa meg az Azure AD Connect-kiszolgálón egy új Windows PowerShell-munkamenetet a Futtatás rendszergazdaként lehetőséggel használatával.
2. Futtatás `Set-ExecutionPolicy RemoteSigned` vagy `Set-ExecutionPolicy Unrestricted`.
3. Az Azure AD Connect varázsló elindításához.
4. Nyissa meg a **további feladatok** lapon jelölje be **hibaelhárítás**, majd válassza ki **tovább**.
5. Az a **hibaelhárítás** lapon jelölje be **indítsa el a** , indítsa el a hibaelhárítási menü a PowerShellben.
6. A fő menüjéből válassza **Jelszókivonat-szinkronizálás hibaelhárítása**.
7. Válassza ki, majd **Jelszókivonat-szinkronizálás nem működik minden**.

Problémák hibaelhárításával [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>2. lépés: Közvetlen egyszeri bejelentkezés előkészítése

Közvetlen egyszeri bejelentkezés használata az eszközök hozzá kell adnia egy Azure AD URL-cím felhasználók intranetes zóna beállításait az Active Directoryban egy csoportházirend használatával.

Alapértelmezés szerint webböngészők számítható ki automatikusan a megfelelő zónához, internetes vagy intranetes URL-címről. Ha például **http:\/\/contoso /** képez le az intranet zóna és **http:\/\/intranet.contoso.com** képez le (mivel az internet zóna az URL-cím pont szerepel). Böngészők csak akkor, ha explicit módon fel az URL-CÍMÉT a böngésző intranetzónához az küldeni felhőbeli végpont, az Azure AD URL-CÍMÉT, például a Kerberos-jegyet.

A lépéseket követve [bevezetése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) az eszközöknek a szükséges módosításokat.

> [!IMPORTANT]
> A módosítás elvégzése nem módosítja az Azure AD a felhasználói bejelentkezés módját. Fontos azonban alkalmazni ezt a konfigurációt az összes eszközre, mielőtt folytatja a műveletet. Azokon az eszközökön, ez a konfiguráció még nem kapott bejelentkező felhasználóknak egyszerűen szükséges beírjanak egy felhasználónevet és jelszót, hogy jelentkezzen be Azure ad-ben.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>3. lépés: A bejelentkezési módszer módosításához a Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés engedélyezése

A bejelentkezési mód módosítása a Jelszókivonat-szinkronizálás és közvetlen egyszeri bejelentkezés engedélyezése két lehetősége van.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>"A" lehetőség Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect használatával

Ezt a módszert használja, ha kezdetben az AD FS környezetének konfigurált Azure AD Connect használatával. Ez a módszer nem használható, ha Ön *nem* eredetileg konfigurálása az AD FS-környezet az Azure AD Connect használatával.

Először módosítsa a bejelentkezési módszer:

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect varázslójában.
2. Válassza ki **felhasználói bejelentkezés módosítása**, majd válassza ki **tovább**. 

   ![A módosítás felhasználói bejelentkezési lehetőség a további tevékenységek lap képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Az a **az Azure AD Connect** lap, adja meg a felhasználónevet és a egy globális rendszergazdai fiók jelszavát.
4. Az a **felhasználói bejelentkezés** lapon válassza ki a **jelszó Jelszókivonat szinkronizálása gomb**. Ügyeljen arra, hogy válassza ki a **felhasználói fiókok konvertálásának** jelölőnégyzetet. A beállítás elavult. Válassza ki **egyszeri bejelentkezés engedélyezése**, majd válassza ki **tovább**.

   ![Képernyőfelvétel az engedélyezése egyetlen bejelentkezési oldaláról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Az Azure AD Connect verziója 1.1.880.0, kezdve a **zökkenőmentes egyszeri bejelentkezést** alapértelmezés szerint a jelölőnégyzet be van jelölve.

   > [!IMPORTANT]
   > Biztonságosan figyelmen kívül hagyhatja a figyelmeztetéseket, amelyek jelzik, hogy a felhasználók átalakítását és teljes Jelszókivonat-szinkronizálás az összevonási a felhőalapú hitelesítés átalakításához szükséges lépéseket. Vegye figyelembe, hogy ezeket a lépéseket nem szükséges többé. Ha ezeket a figyelmeztetéseket, győződjön meg arról, hogy, és hogy az Azure AD Connect legújabb verzióját futtatja, továbbra is megjelenik ez az útmutató a legújabb használ. További információkért lásd: a szakasz [frissítés az Azure AD Connect](#update-azure-ad-connect).

5. Az a **egyszeri bejelentkezés engedélyezése** lapon adja meg a tartomány rendszergazdai fiókjának hitelesítő adatait, és válassza ki **tovább**.

   ![Képernyőfelvétel az engedélyezése egyetlen bejelentkezési oldaláról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Közvetlen egyszeri bejelentkezés engedélyezéséhez szükséges tartományi rendszergazdai fiók hitelesítő adatait. A folyamat befejeződése után a következő műveleteket, és ezek emelt szintű engedélyek szükségesek. A tartományi rendszergazdai fiók hitelesítő adatait az Azure AD Connectben vagy az Azure ad-ben nem tárolja. A tartományi rendszergazdai fiók hitelesítő adatait csak a funkció bekapcsolásához szolgálnak. A hitelesítő adatok nem őrződnek meg a folyamat sikeres befejeződése után.
   >
   > 1. Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory-példányában.
   > 2. A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   > 3. Két Kerberos egyszerű szolgáltatásnevek (SPN) jönnek létre, amelyek a két URL-címet, amelyekre az Azure AD-be.

6. Az a **konfigurálásra kész** lapon, győződjön meg arról, hogy a **indítsa el a szinkronizálást, amint a konfigurálás befejeződik** jelölőnégyzet be van jelölve. Ezután válassza ki **konfigurálása**.

      ![A rendszer készen áll a konfigurálása lap képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Ezen a ponton az összevont tartományok felügyelt hitelesítési változik. A Jelszókivonat-szinkronizálás a hitelesítési új módszer.

7. Az Azure AD-portálon válassza **Azure Active Directory** > **az Azure AD Connect**.
8. Ellenőrizze a beállításokat:
   * **Összevonási** értékre van állítva **letiltott**.
   * **Zökkenőmentes egyszeri bejelentkezést** értékre van állítva **engedélyezve**.
   * **Jelszó-szinkronizálás** értékre van állítva **engedélyezve**.<br /> 

   ![A beállítások, a felhasználói bejelentkezési szakaszban képernyőkép ](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Ugrás a [tesztelési és a következő lépések](#testing-and-next-steps).

   > [!IMPORTANT]
   > Hagyja ki a szakaszt **b lehetőség Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect és a PowerShell használatával**. Az adott szakaszban ismertetett lépések nem vonatkoznak, ha a bejelentkezési módszer módosításához a Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés engedélyezéséhez A beállítást választja.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>"B" lehetőség Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect és a PowerShell használatával

Használja ezt a beállítást, ha az összevont tartományokban nem először adja meg az Azure AD Connect használatával. Ez a folyamat során engedélyezte a zökkenőmentes egyszeri Bejelentkezést és a tartományok az összevont felügyelt kapcsoló.

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect varázslójában.
2. Válassza ki **felhasználói bejelentkezés módosítása**, majd válassza ki **tovább**.
3. Az a **az Azure AD Connect** lapon, a felhasználónevet és jelszót adjon meg egy globális rendszergazdai fiókkal.
4. Az a **felhasználói bejelentkezés** lapon válassza ki a **Jelszókivonat-szinkronizálás** gombra. Válassza ki **egyszeri bejelentkezés engedélyezése**, majd válassza ki **tovább**.

   Mielőtt engedélyezné a Jelszókivonat-szinkronizálás: ![Képernyőkép a ne konfigurálja a beállítás a felhasználó bejelentkezési oldalán](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Miután engedélyezte a Jelszókivonat-szinkronizálás: ![A felhasználó bejelentkezési oldalán új lehetőségeket bemutató képernyőkép ](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Az Azure AD Connect verziója 1.1.880.0, kezdve a **zökkenőmentes egyszeri bejelentkezést** alapértelmezés szerint a jelölőnégyzet be van jelölve.

5. Az a **egyszeri bejelentkezés engedélyezése** lapon adja meg egy tartományi rendszergazdai fiók hitelesítő adatait, és válassza ki **tovább**.

   > [!NOTE]
   > Közvetlen egyszeri bejelentkezés engedélyezéséhez szükséges tartományi rendszergazdai fiók hitelesítő adatait. A folyamat befejeződése után a következő műveleteket, és ezek emelt szintű engedélyek szükségesek. A tartományi rendszergazdai fiók hitelesítő adatait az Azure AD Connectben vagy az Azure ad-ben nem tárolja. A tartományi rendszergazdai fiók hitelesítő adatait csak a funkció bekapcsolásához szolgálnak. A hitelesítő adatok nem őrződnek meg a folyamat sikeres befejeződése után.
   >
   > 1. Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory-példányában.
   > 2. A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   > 3. Két Kerberos egyszerű szolgáltatásnevek (SPN) jönnek létre, amelyek a két URL-címet, amelyekre az Azure AD-be.

6. Az a **konfigurálásra kész** lapon, győződjön meg arról, hogy a **indítsa el a szinkronizálást, amint a konfigurálás befejeződik** jelölőnégyzet be van jelölve. Ezután válassza ki **konfigurálása**.

   ![Képernyőkép a a Konfigurálás gombra a Ready to lapjának konfigurálása](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Amikor kiválasztja a **konfigurálása** gomb, közvetlen egyszeri bejelentkezés konfigurálva van az előző lépésben jelzett. Jelszó kivonatoló szinkronizálási konfiguráció nem módosul, mert korábban engedélyezve lett.

   > [!IMPORTANT]
   > Nem a módosítás ahhoz, ahogyan a felhasználók jelentkezzen be most.

7. Az Azure AD-portálon ellenőrizze ezeket a beállításokat:
   * **Összevonási** értékre van állítva **engedélyezve**.
   * **Zökkenőmentes egyszeri bejelentkezést** értékre van állítva **engedélyezve**.
   * **Jelszó-szinkronizálás** értékre van állítva **engedélyezve**.

   ![A beállítások, a felhasználói bejelentkezési szakaszban képernyőkép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Felügyelt összevont tartományokban konvertálása

Ezen a ponton összevonási továbbra is engedélyezett és a tartományok működési áll. A telepítés folytatásához, minden egyes tartományhoz kell konvertálható összevont felhasználók hitelesítésének Jelszókivonat-szinkronizálás kényszerítése felügyelt.

> [!IMPORTANT]
> Tartományok átalakítása egy időben nem kell. Választhatja az éles környezetbeli bérlőhöz a teszt tartomány vagy a saját tartománnyal, amelyben a felhasználók a legalacsonyabb száma indul.

Az átalakítás végrehajtása az Azure AD PowerShell-modul használatával:

1. A PowerShellben jelentkezzen be az Azure AD globális rendszergazdai fiók használatával.
2. Konvertálja az első tartományt, futtassa a következő parancsot:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Az Azure AD-portálon válassza **Azure Active Directory** > **az Azure AD Connect**.
4. Győződjön meg arról, hogy a tartomány lett konvertálva felügyelt a következő parancs futtatásával:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Tesztelés és a következő lépések

Hajtsa végre a következő feladatokat a Jelszókivonat-szinkronizálás és az átalakítási folyamat befejezéséhez.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Hitelesítés tesztelése a Jelszókivonat-szinkronizálás használatával 

Összevont identitás a bérlő használatakor felhasználók ide irányítottuk az Azure AD bejelentkezési oldala a az AD FS-környezethez. Most, hogy a bérlő a Jelszókivonat-szinkronizálás használata helyett az összevont hitelesítés van konfigurálva, a nem AD FS átirányítja a felhasználókat. Ehelyett felhasználók bejelentkezés közvetlenül az Azure AD bejelentkezési oldal.

A Jelszókivonat-szinkronizálás teszteléséhez:

1. Nyissa meg az Internet Explorer InPrivate módban, úgy, hogy közvetlen egyszeri bejelentkezés nem automatikus bejelentkezés.
2. Nyissa meg az Office 365 bejelentkezési oldala ([http://portal.office.com](http://portal.office.com/)).
3. Adja meg a felhasználó egyszerű Felhasználónevét, és válassza ki **tovább**. Győződjön meg arról, hogy adjon meg egy hibrid felhasználó, aki szinkronizálása a helyszíni Active Directory-példányból, és akik korábban használták az összevont hitelesítés Felhasználóneve. Megjelenik egy oldal, amelyen meg a felhasználónevet és jelszót:

   ![Képernyőkép a bejelentkezési oldal, ahol megadhatja a felhasználónevet](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![A bejelentkezési oldal, ahol megadhatja a jelszó bemutató képernyőkép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Adja meg a jelszót és kiválasztása után **jelentkezzen be a**, program átirányítja az Office 365 portálon.

   ![Az Office 365-portál képernyőképe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Teszt közvetlen egyszeri bejelentkezés

1. Jelentkezzen be egy tartományhoz csatlakoztatott gép, amely a vállalati hálózathoz csatlakozik.
2. Az Internet Explorer vagy Chrome böngészőt nyissa meg a következő URL-címek (csere "contoso" a saját tartománnyal) egyike:

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Rendszer röviden átirányítja a felhasználót az Azure AD bejelentkezési oldal, amely megjeleníti az üzenet ", a bejelentkezés közben." A felhasználónak nem kell megadnia egy felhasználónevet vagy jelszót.<br />

   ![Képernyőkép az Azure AD bejelentkezési oldalára, az üzenet](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. A felhasználó lesz átirányítva, és sikeresen bejelentkezett a hozzáférési panel:

   > [!NOTE]
   > Közvetlen egyszeri bejelentkezés az Office 365-szolgáltatásokhoz (például myapps.microsoft.com/contoso.com) tartományemlékeztető támogató működik. Jelenleg az Office 365-portálon (portal.office.com) nem támogatja a tartomány mutatók. Felhasználók adja meg az egyszerű Felhasználónévre van szükség. Miután egy egyszerű felhasználónév van megadva, a közvetlen egyszeri bejelentkezés a Kerberos-jegy, a felhasználó nevében kérdezi le. A bejelentkezett felhasználó anélkül, hogy jelszót írna be.

   > [!TIP]
   > Érdemes megfontolni a [hibrid Azure AD join a Windows 10-es](https://docs.microsoft.com/azure/active-directory/device-management-introduction) továbbfejlesztett egyszeri bejelentkezési élményt biztosít.

### <a name="remove-the-relying-party-trust"></a>Távolítsa el a függő entitás megbízhatóságához

Után ellenőrizze, hogy minden felhasználó és az ügyfelek vannak a sikeres hitelesítés az Azure AD-n keresztül, már biztonságosan távolítsa el az Office 365 függőentitás-megbízhatóságot.

Ha más célra nem használja az AD FS (azt jelenti, más függő entitások számára), biztonságos, Active Directory összevonási szolgáltatások leszerelése ezen a ponton.

### <a name="rollback"></a>Visszaállítás

Ha jelentős probléma felderítése, és nem oldható fel, gyorsan, választhatja összevonási a megoldás visszaállítása.

Tekintse át az adott központi telepítési adatait összevonási tervezési és telepítési dokumentációját. A folyamat megteszik a kellő ezeket a feladatokat:

* A felügyelt tartomány átalakítása összevont hitelesítés használatával a **Convert-MSOLDomainToFederated** parancsmagot.
* Szükség esetén további jogcímek szabályok konfigurálása.

### <a name="sync-userprincipalname-updates"></a>Szinkronizálási userPrincipalName frissítések

Hagyományosan frissíti a **UserPrincipalName** attribútuma, amely a helyszíni környezetből a szinkronizálási szolgáltatást használja, le vannak tiltva, kivéve, ha az alábbi két feltétel teljesül:

* A felhasználó (nem összevont) felügyelt identitás tartományban van.
* A felhasználó még nem lett hozzárendelve licenc.

Győződjön meg arról, vagy a funkció kezelésével kapcsolatos információkért lásd: [userPrincipalName frissítések szinkronizálása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Hibaelhárítás

A támogatási csapat ismerje meg minden olyan hitelesítési problémák merülnek fel a közben vagy után a változás a összevonási felügyelni hibaelhárítása. A támogatási csapat megismerkedhetnek a gyakori hibaelhárítási lépéseket és a megfelelő műveleteket, amelyek segítenek elkülöníteni, és a probléma megoldásához az alábbi hibaelhárítási dokumentáció segítségével.

[Azure Active Directory a Jelszókivonat-szinkronizálás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Vihetők át a zökkenőmentes egyszeri bejelentkezési Kerberos visszafejtési kulcs

Fontos gyakran vihetők át a Kerberos visszafejtési kulcs AZUREADSSOACC fiók (amely az Azure AD). A AZUREADSSOACC számítógépfiók jön létre a helyszíni Active Directory-erdőben. Kifejezetten ajánljuk, hogy hosszabbítsa a Kerberos-visszafejtési kulcs legalább 30 naponta igazodva a módon, hogy az Active Directory tartományi tagok küldje el a jelszó módosítására. Nincs csatolt AZUREADSSOACC számítógépfiók-objektumból, ezért manuálisan kell végrehajtania a váltás nincs társított eszköz.

Indítsa el a zökkenőmentes egyszeri bejelentkezési Kerberos visszafejtési kulcs a helyszíni kiszolgálón, amelyen fut az Azure AD Connect a kapcsolódó kulcsváltást.

További információkért lásd: [hogyan vihetők át a Kerberos visszafejtési kulcs AZUREADSSOACC számítógépfiók?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>További lépések

* Ismerje meg [az Azure AD Connect tervezési alapelvei](plan-connect-design-concepts.md).
* Válassza ki a [megfelelő hitelesítési](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Ismerje meg [támogatott topológiák](plan-connect-design-concepts.md).
