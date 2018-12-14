---
title: 'Azure AD Connect: A Jelszókivonat-szinkronizálás az Azure ad összevonási áttelepít |} A Microsoft Docs'
description: Áthelyezi a hibrid identitáskezelési környezetet a összevonási Jelszókivonat-szinkronizálás információk.
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
ms.openlocfilehash: 687ff4a7411113721b16636e1d11b30573e41642
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346427"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>A Jelszókivonat-szinkronizálás az Azure ad összevonási áttelepítésére
A következő dokumentum útmutatást nyújt a Jelszókivonat-szinkronizálás az AD FS áthelyezését.

>[!NOTE]
>Letölthető a jelen dokumentum elérhető legyen a [Itt](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>A migrálás előfeltételei 
A következő előfeltételek szükségesek a migráláshoz.
### <a name="update-azure-ad-connect"></a>Frissítés Azure AD Connect

Rendelkeznie kell legalább sikeres végrehajtásához az átmenő hitelesítés áttelepítési lépéseit [az Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594.) 1.1.819.0. Ez a verzió bejelentkezési átalakítás történik, és csökkenti a felhőalapú hitelesítés potenciálisan óra és perc összevonási áttelepíthetők teljes időtartama érintő jelentős változásokat tartalmaz.

> [!IMPORTANT]
> Elavult dokumentáció, eszközök és blogok azt jelzi, hogy felhasználók átalakítását lépésre szükség amikor az összevont tartományok átalakítása felügyelt. Vegye figyelembe, hogy konvertálása a felhasználók már nem szükséges, és a Microsoft dolgozik dokumentáció és eszközök ennek megfelelően frissíti.

Az Azure AD Connect frissítése a legújabb verzióra, ezzel [update utasítások](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Jelszó Jelszókivonat-szinkronizálás szükséges engedélyek

Az Azure AD Connect Gyorsbeállítások vagy egyéni telepítés használatával konfigurálható. Az egyéni telepítési lehetőség használatakor a [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) a Jelszókivonat-szinkronizálás nem teljesülnek.

Az Azure AD Connect az AD DS-fiókot kell tudni jelszókivonatok szinkronizálása a következő engedélyekkel szolgáltatás.

* Címtárváltozások replikálása

* Replikálás könyvtár összes változik

Itt az ideje ellenőrizni ezeket engedélyek vannak érvényben, az erdőben lévő összes tartományban.

### <a name="plan-migration-method"></a>Áttelepítési módszer megtervezése

A Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés az összevont hitelesítés át két módszer áll rendelkezésre. Használt módszer az AD FS eredeti konfigurációjától függ. 



- **"A" lehetőség Az Azure AD Connect**. Ha az AD FS eredetileg konfigurálták az Azure AD Connect használatával, majd a Jelszókivonat-szinkronizálás, a felhasználói bejelentkezési mód módosítása kell elvégezni az Azure AD Connect varázsló lépéseit.   
Az Azure AD Connect használata esetén, a Set-MsolDomainAuthentication parancsmagot futtatja, automatikusan a felhasználói bejelentkezési módszert, és ezért azt nem szabályozhatják, unfederating összes az Azure AD-bérlő ellenőrzött összevont tartománynak.

> [!NOTE]
> Jelenleg nem kerülheti el a bérlőn belüli összes tartomány unfederating, amikor módosítja a felhasználói bejelentkezés a Jelszókivonat-szinkronizálás során az AAD Connect eredetileg használt az AD FS konfigurálása az Ön számára.  



- **"B" lehetőség A PowerShell-lel az Azure AD Connect**. Ez a módszer használható, csak akkor, ha az AD FS eredetileg nem lett konfigurálva az Azure AD Connecttel. Továbbra is módosítani kell a felhasználói bejelentkezési módszer az Azure AD Connect varázsló segítségével, de az alapvető különbség, hogy nem fog automatikusan futni a Set-MsolDomainAuthentication parancsmag az Ön számára nincs ismertségét, az AD FS farm rendelkezik, és ezért keresztül teljes hozzáféréssel rendelkezik mely tartományok lesznek konvertálva, és milyen sorrendben.

Szeretné megtudni, melyik módszert használja, hajtsa végre a lépéseket a következő szakaszt.

#### <a name="verify-current-user-sign-in-settings"></a>Ellenőrizze a jelenlegi felhasználói bejelentkezési beállítások

Ellenőrizze a jelenlegi felhasználói bejelentkezési beállításai az Azure AD-portálra való bejelentkezés [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) egy globális rendszergazdai fiókkal.

A felhasználói bejelentkezés szakaszban ellenőrizze, hogy engedélyezve van-e az összevonási és, hogy a zökkenőmentes egyszeri bejelentkezést és átmenő hitelesítés le van tiltva. Emellett győződjön meg arról, hogy meg kell jelennie le van tiltva, kivéve, ha ez korábban be lett kapcsolva a jelszó-szinkronizálás állapotát.

![Kép: 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Az Azure AD Connect konfiguráció ellenőrzése

   1. Nyissa meg az Azure AD Connect-kiszolgáló, és indítsa el az Azure AD Connect, majd válassza ki a konfigurálása. 
   2. A további feladatokat képernyőn válassza ki az aktuális konfiguráció megtekintése, és válassza ki a Tovább gombra.</br>
   ![Kép 31.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. A megoldás áttekintése képernyőn jegyezze fel a jelszó-szinkronizálás állapotát.</br> 

   A Jelszókivonat-szinkronizálás jelenleg le van tiltva van beállítva, ha szüksége lesz az engedélyezéshez Ez az Útmutató lépéseit kövesse. Ha a Jelszókivonat-szinkronizálás jelenleg engedélyezve van beállítva, nyugodtan kihagyhatja a szakasz [1. lépés – a Jelszókivonat-szinkronizálás engedélyezése](#_Step_1_–) ebben az útmutatóban.
   4. A megoldás áttekintése képernyőn görgessen lefelé az Active Directory összevonási szolgáltatások (AD FS).</br>
 
   Ha látja, hogy az AD FS konfigurációs van ebben a szakaszban, majd biztonságosan feltételezheti eredetileg konfigurálták az AD FS az Azure AD Connect használatával, és ezért a felügyeltről átalakítása összevont felügyelt is driven keresztül az Azure AD Connect "módosítása felhasználói bejelentkezés -a "beállítást, a folyamat részleteit a szakasz a **egy - kapcsoló. lehetőség a Jelszókivonat-szinkronizálás az Azure AD Connect használatával a összevonási**.
   5. Active Directory összevonási szolgáltatások szerepel az aktuális beállítások nem fogja látni, akkor a rendszer manuálisan konvertálnia kell a tartományából összevont Powershellbe, ez a szakasz részletesen keresztül kezeli **beállítás B - összevonási való váltás Az Azure AD Connect és a PowerShell használatával a Jelszókivonat-szinkronizálás**.

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
Azokat a beállításokat, előfordulhat, hogy testre szabott az összevonási tervezési és üzembe helyezési dokumentációját, kifejezetten PreferredAuthenticationProtocol SupportsMfa és PromptLoginBehavior ellenőrzése.

További információ a mi ezek a beállítások megtalálhatók alább.

[Active Directory összevonási szolgáltatások kérni = bejelentkezési paraméter támogatása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Ha SupportsMfa jelenleg értéke "True" majd ez azt jelenti, egy helyszíni MFA-megoldást használ 2nd factor kihívást behelyezése a felhasználói hitelesítési folyamatot. Ez nem fog működni az Azure AD hitelesítési forgatókönyvei, és ehelyett kell kihasználhatja az Azure MFA (felhőalapú) szolgáltatás ugyanannak a függvénynek végrehajtásához. Gondosan az MFA-követelmények felmérése előtt, és győződjön meg arról, hogy megismerte, hogyan használható az Azure MFA, a licencelési szempontokat, és a végfelhasználó regisztrációt a tartományok átalakítása előtt. Az Azure MFA-hoz, amely részletes üzembe helyezési útmutatóban talál [ https://aka.ms/deploymentplans ](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Biztonsági mentési összevonási beállítások

Bár nem lesz kell módosítás más függő felek számára az AD FS farmon, a folyamat során, ellenőrizze, hogy az AD FS farmon, a visszaállítható az aktuális érvényes biztonsági ajánlott. Ehhez használja az ingyenes Microsoft [AD FS gyors visszaállítási eszköz](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Ez az eszköz segítségével biztonsági mentése és visszaállítása az AD FS-ben vagy egy meglévő farmot, vagy egy új farmot.

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
| Az AD FS a nagymértékben testre szabott és tartománybeli adott testreszabási beállításokat a onload.js fájlban, amely nem duplikálható az Azure AD-ben 
(például módosította a bejelentkezési élmény, hogy a felhasználók csak írja be a SamAccountName formátum helyett egy egyszerű felhasználónevének, vagy rendelkezik egy márkaüzenettel ellátott erősen a bejelentkezési felület)| Győződjön meg arról, hogy a jelenlegi testreszabási követelmények érheti el az Azure AD a folytatás előtt kell. Tekintse meg az AD FS márkajelzési és az AD FS testreszabás szakaszok további információt és útmutatást.|
| Az örökölt hitelesítési ügyfeleket az AD FS-n keresztül blokkolja.| Érdemes lecserélni az örökölt hitelesítési ügyfelek jelenleg megtalálható az AD FS kombinációjával blokkolása vezérlők [feltételes hozzáférés-vezérlés az örökölt hitelesítés](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) és [Online Exchange ügyfélelérési Szabályok](http://aka.ms/EXOCAR).|
| A felhasználók hitelesítéséhez az AD FS többtényezős hitelesítés végrehajtásához egy a helyszíni MFA-kiszolgálói megoldás elleni van szüksége.| Nem kell az MFA-hitelesítést keresztül a helyszíni MFA-megoldást behelyezése a hitelesítési folyamat a felügyelt tartományhoz tartozó azonban használhatja az Azure MFA szolgáltatás érdekében a későbbiekben egyszer a tartomány lesz konvertálva. Ha a felhasználók nem használja az Azure MFA még ma, majd ez magában foglalja egy egyszeri végfelhasználói regisztrációs lépést, amely kell előkészítése, illetve kommunikálhatnak a végfelhasználók számára. |
| Használhatja (engedélyezési szabályok) hozzáférés-vezérlési szabályzatokkal még ma az AD FS Office 365-höz való hozzáférés szabályozásához.| Fontolja meg, és cserélje le ezeket az Azure ad-ben egyenértékű [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) és [Exchange Online ügyfél-hozzáférési szabályok](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Közös AD FS testreszabás szempontjai

#### <a name="inside-corporate-network-claim"></a>Jogcím a vállalati hálózaton belül

A InsideCorporateNetwork jogcímet AD FS által a hitelesítő felhasználó esetén a vállalati hálózaton belül. Ez a jogcím ezután átkerül az Azure ad-ben, és megkerülhetik a többtényezős hitelesítés a felhasználó hálózati helye alapján. Lásd: [megbízható IP-címek összevont felhasználók](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) hogyan kell meghatároznia, hogy jelenleg engedélyezve van az AD FS-ben ez az információ.

A InsideCorporateNetwork jogcím nem lesz elérhető többé a tartományok alakítja a Jelszókivonat-szinkronizálás után. [Nevesített helyek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) cserélje le ezt a funkciót is használható.

Nevű helyek konfigurálása után minden feltételes hozzáférési szabályzatokat konfigurálni vagy kizárja a hálózati helyeket a "Minden megbízható hely" vagy "MFA megbízható IP-címek" frissíteni kell, hogy az újonnan létrehozott nevű helyek tükrözik.

Lásd: [Active Directory feltételes hozzáférés helyek](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) további információ a feltételes hozzáférés a hely állapotára.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

Eszköz csatlakoztatása az Azure AD lehetővé teszi, amelyeket a biztonsági és megfelelőségi hozzáférés vonatkozó megfelelő eszközök feltételes hozzáférési szabályokat hozzon létre, és lehetővé teszi a felhasználóknak jelentkezzen be egy szervezeti munkahelyi vagy iskolai fiók használata helyett egy személyes eszköz fiók. Hibrid Azure AD-csatlakoztatott eszközök lehetővé teszi, hogy az AD-tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-ben. Az összevont környezetben előfordulhat, hogy konfigurálva ez a szolgáltatás.

Annak érdekében, hogy csatlakozzon a hibrid továbbra is fennáll, új eszközök működő csatlakoztatni a tartományhoz, a tartományok lettek konvertálva a Jelszókivonat-szinkronizálás után, az Azure AD Connect kell konfigurálni az Active Directory számítógépfiókok szinkronizálása a Windows 10-ügyfelek számára Azure ad-ben. Windows 7 és Windows 8-fiókok csatlakozzon a hibrid használandó közvetlen egyszeri bejelentkezés a számítógép regisztrálása az Azure ad-ben, és nem rendelkezik a Windows 10 rendszerű eszközökön is szinkronizálhatja őket. Azonban akkor üzembe egy frissített workplacejoin.exe fájlt (.msi) n keresztül ezek az alsó szintű ügyfelek így azok regisztrálhatják magukat a közvetlen egyszeri bejelentkezés használatával. [Töltse le az .msi fájlt](https://www.microsoft.com/download/details.aspx?id=53554). 

További információkért tekintse meg [hogyan hibrid konfigurálása Azure Active Directoryhoz csatlakoztatott eszközök](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Védjegyezés

Szervezete is [testre szabott az AD FS bejelentkezési oldalainak](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) a szervezet több profiljával kapcsolatos információk megjelenítéséhez. Ha igen, vegye figyelembe, hogy hasonló [az Azure AD bejelentkezési oldal testreszabása](https://docs.microsoft.com/azure/active-directory/customize-branding).

Hasonló testreszabások érhető el, amelyek bizonyos vizuális módosítások számíthat. Érdemes felvenni kívánt változások a végfelhasználók számára a kommunikáció.

> [!NOTE]
> Vállalati arculat csak akkor, ha az Azure ad prémium vagy alapszintű licenc vásárolt, vagy rendelkezik Office 365-licenccel érhető el.

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

A Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés telepítése után a végfelhasználói bejelentkezési változnak, ha fér hozzá az Office 365-höz és más kapcsolódó erőforrások hitelesítése az Azure AD-n keresztül. A hálózaton kívüli felhasználók számára megjelenik a lap csak az Azure AD bejelentkezési átirányította a az űrlapos oldalra, a külső irányuló Webalkalmazásproxy-kiszolgálók által bemutatott szemben.

Több elemei a kommunikáció stratégiájának megtervezése. Ezek a következők:

* Közelgő és engedélyezett funkciók segítségével a felhasználók értesítése
  * E-mailekhez és más belső kommunikációs csatornákat
  * Vizualizációk, például poszterek
  * Vezetői élő vagy egyéb kommunikáció
* Ki fogja szabni, és akik küld a kommunikációhoz, és mikor.

## <a name="implementing-your-solution"></a>A megoldás megvalósítása

Most, hogy a megoldás megtervezése, készen áll implementálásáról. Végrehajtása a következő összetevőket tartalmazza:

1. Jelszókivonat szinkronizálásának engedélyezése

2. A zökkenőmentes egyszeri bejelentkezés előkészítése

3. Bejelentkezési mód módosítása a Jelszókivonat-szinkronizálás és a közvetlen egyszeri bejelentkezés engedélyezése

### <a name="step-1--enable-password-hash-synchronization"></a>1. lépés – a Jelszókivonat-szinkronizálás engedélyezése

A megoldás első lépése a Jelszókivonat-szinkronizálás engedélyezése az Azure AD Connect varázsló a. A Jelszókivonat-szinkronizálás ez nem kötelező, hogy nem érinti a hitelesítési folyamathoz összevonással környezetekben is engedélyezhető. Ebben az esetben az Azure AD Connect indul jelszókivonatok anélkül, hogy befolyásolná a felhasználói bejelentkezés összevonással.

Ebből kifolyólag javasoljuk, hogy a lépés végrehajtása egy előkészítési tevékenység, a tartományok bejelentkezési mód módosítása előtt. Ezután a Jelszókivonat-szinkronizálás ellenőrzése bőséges időt megfelelően működik.

A Jelszókivonat-szinkronizálás engedélyezése:

   1. Az Azure AD Connect kiszolgáló nyissa meg a varázsló, és válassza ki a konfigurálása.
   2. Válassza ki a szinkronizálási beállítások testreszabása, és kattintson a Tovább gombra.
   3. A csatlakozás az Azure ad Szolgáltatáshoz képernyőn adja meg a felhasználónevet és a egy globális rendszergazdai jelszót.
   4. A csatlakozás a könyvtárak képernyőn kattintson a Tovább gombra.
   5. A tartomány és szervezeti egység szűrési képernyőn kattintson a Tovább gombra.
   6. A választható funkciókat felsoroló képernyőjén válassza a jelszó-szinkronizálást, és kattintson a Tovább gombra.
   ![Kép: 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Jelölje be a következő fennmaradó képernyők és konfigurálás utolsó lapján.
   8. Az Azure AD Connect indul el a következő szinkronizálás jelszókivonatok szinkronizálása.

A Jelszókivonat-szinkronizálás engedélyezése után a jelszót kivonatolja, az Azure AD Connect szinkronizálási hatókör lesznek rehashed és az Azure ad Szolgáltatásba írt összes felhasználója esetében. A felhasználók számától függően ez a művelet is igénybe vehet percet vagy akár néhány órát.

Tervezési célokra, meg kell megbecsülni, hogy körülbelül 20 000 felhasználóhoz dolgozhatók fel 1 óra.

A Jelszókivonat-szinkronizálás ellenőrzése megfelelően működjön, a hibaelhárítási feladat használata az Azure AD Connect varázslójában.

   1. Nyisson meg egy új Windows PowerShell-munkamenetet a az Azure AD Connect-kiszolgáló a Futtatás rendszergazdaként lehetőséggel.
   2. Futtassa a Set-ExecutionPolicy RemoteSigned vagy a Set-ExecutionPolicy Unrestricted.
   3. Az Azure AD Connect varázsló elindításához.
   4. Nyissa meg a további feladatok lapot, válassza ki a hibaelhárítás, és kattintson a Tovább gombra.
   5. A hibaelhárítás lapon kattintson az indítás, indítsa el a hibaelhárítási menü a PowerShellben.
   6. A fő menüjében válassza a Jelszókivonat-szinkronizálás hibaelhárítása.
   7. Sub menüben válassza a Jelszókivonat-szinkronizálás nem működik minden jelszót.

Ha problémák megkereséséhez használja az információk [Ez a cikk](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) hibaelhárítása.

### <a name="step-2--prepare-for-seamless-sso"></a>2 – előkészítési lépés a közvetlen egyszeri bejelentkezés

Az eszközök közvetlen egyszeri bejelentkezés használatára szüksége egy Azure AD URL-cím hozzáadása a felhasználók intranetes zóna beállításait az Active Directory csoportházirend használatával.

Alapértelmezés szerint a böngésző automatikusan kiszámolja a megfelelő zónához, internetes vagy intranetes, egy adott URL-címről. Például "http://contoso/"az Intranet zóna képez, mivel a"http://intranet.contoso.com/" rendeli az Internet zóna (mivel az URL-cím pont szerepel). Böngészők nem küldenek Kerberos-jegyekhez felhőbeli végpont, például az Azure AD URL-cím, kivéve, ha explicit módon fel az URL-CÍMÉT a böngésző intranetzónához az.

Kövesse a [felhasználócsoportra lépéseket](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) az eszközöknek a szükséges módosításokat.

> [!IMPORTANT]
> A módosítás elvégzése fogja módosítani az Azure AD a felhasználói bejelentkezés módját. Fontos azonban, a konfiguráció alkalmazása az összes eszközre, mielőtt folytatná a 3. lépés. Azt is vegye figyelembe, hogy adja meg a felhasználónevet és jelszót, hogy jelentkezzen be az Azure AD egyszerűen kell azokon az eszközökön nem érkezett meg ezt a konfigurációt, bejelentkező felhasználókat.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>3. lépés – a bejelentkezési módszer váltson PHS-ben és a közvetlen egyszeri bejelentkezés engedélyezése

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>A beállítás A - nál az Azure AD Connect használatával a összevonási Váltás

Ezt a módszert használja, ha az AD FS eredetileg konfigurálták az Azure AD Connect használatával. Ez a módszer nem használható, ha az AD FS eredetileg nem lett konfigurálva az Azure AD Connect használatával. Első **felhasználói bejelentkezési mód módosítása**

   1. Az Azure AD Connect kiszolgáló, a varázsló megnyitásához.
   2. Felhasználó váltása bejelentkezési válassza ki, és kattintson a Tovább gombra.
   ![Kép 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. Az a **az Azure AD Connect** képernyőn adja meg a felhasználónevet és jelszót egy **globális rendszergazdai**.
   4. Az a **felhasználói bejelentkezés** képernyőn, és az AD FS összevonási a választógomb váltson át a Jelszókivonat-szinkronizálás és ellenőrizze, hogy jelölje be a felhasználói fiókok konvertálásának, ez egy elavult lépés, amely egy jövőbeli verziójában megszűnik az AAD Connect. Emellett engedélyezze az egyszeri bejelentkezés válassza ki, majd kattintson **tovább**.
   ![Kép: 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Az Azure AD Connect verziója 1.1.880.0 verziótól kezdődően a zökkenőmentes egyszeri bejelentkezési jelölőnégyzet alapértelmezés szerint engedélyezve van.
   
   > [!IMPORTANT]
   > Biztonságosan figyelmen kívül hagyhatja a figyelmeztetéseket, amely azt jelzi, hogy a felhasználók átalakítását és teljes Jelszókivonat-szinkronizálás az összevonási a felhőalapú hitelesítés átalakításához szükséges lépéseket. Vegye figyelembe, hogy ezeket a lépéseket nem szükséges többé, az Azure AD Connect későbbi verzióiban lesz lehetősége felhasználók alakításához. Ha továbbra is látja ezeket a figyelmeztetéseket, ellenőrizze, hogy futnak-e a legújabb Azure AD Connect és, amikor ez az útmutató a legújabb verzióját használja. További információkért lásd: a [frissítés az Azure AD Connect szakasz](#_Update_Azure_AD).
   
   5. Engedélyezze az egyszeri bejelentkezés képernyőn adja meg a tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a Tovább gombra.
   ![Kép 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Tartományi rendszergazda hitelesítő adataira szükség, a folyamat a következő ezeket az emelt szintű engedélyeket igénylő műveleteket hajt végre közvetlen egyszeri bejelentkezés engedélyezése. A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően
   >  * Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory (AD).
   >  * A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   >  * Emellett két Kerberos egyszerű szolgáltatásnevek (SPN) jön létre, amelyek a két URL-címet, amelyekre az Azure AD-be.
   >  * A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően
   
   6. A Ready to Configure képernyő, ellenőrizze, hogy "Start szinkronizálási folyamat befejezése után a konfigurációs" jelölőnégyzet be van jelölve. Ezután válassza ki a konfigurálása.
   ![Kép: 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > Ezen a ponton az összevont tartományok fog módosulni felügyelt hitelesítést, amely most már használja a Jelszókivonat-szinkronizálás, a hitelesítési módszert.
       
   7. Nyissa meg az Azure AD portálra, válassza ki az Azure Active Directoryban, és válassza az Azure AD Connect.
   8. Győződjön meg arról, hogy összevonási közben zökkenőmentes egyszeri bejelentkezés le van tiltva, és a jelszó-szinkronizálás engedélyezve van.  
  ![37 kép](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Lépjen a [tesztelési és a következő lépések](#_Next_Steps_and).
   
   > [!IMPORTANT]
   > Hagyja ki a szakasz a beállítás B - Váltás a összevonási a Jelszókivonat-szinkronizálás az Azure AD Connect és a PowerShell használatával, az adott szakaszban ismertetett lépések nem vonatkoznak.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>A beállítás B - kapcsolót a összevonási nál az Azure AD Connect és a PowerShell használatával

Használja ezt a beállítást, ha az összevonási kezdetben nem konfigurálták az Azure AD Connect használatával.

Ez a folyamat részeként a rendszer közvetlen egyszeri bejelentkezés engedélyezése, és váltson a felügyelt, az összevont tartományok.

   1. Az Azure AD Connect kiszolgáló, a varázsló megnyitásához.
   2. Felhasználó váltása bejelentkezési válassza ki, és kattintson a Tovább gombra. 
   3. A csatlakozás az Azure ad Szolgáltatáshoz képernyőn adja meg a felhasználónevet és a egy globális rendszergazdai jelszót.
   4. Jelölje be a felhasználói bejelentkezési képernyőn történő Jelszókivonat-szinkronizálás, nem adja meg a választógombot, a módosítás engedélyezése egyszeri bejelentkezést, majd válassza a Tovább.
   
   Mielőtt a változás: ![Kép 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Miután a módosítás:  
   ![Kép 22-es](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Az Azure AD Connect verziója 1.1.880.0 verziótól kezdődően a zökkenőmentes egyszeri bejelentkezési jelölőnégyzet alapértelmezés szerint engedélyezve van.
   
   5. Engedélyezze az egyszeri bejelentkezés képernyőn adja meg a tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a Tovább gombra.
   
   > [!NOTE]
   > Tartományi rendszergazda hitelesítő adataira szükség, a folyamat a következő ezeket az emelt szintű engedélyeket igénylő műveleteket hajt végre közvetlen egyszeri bejelentkezés engedélyezése. A tartományi rendszergazda hitelesítő adatai nem tárolja az Azure AD Connectben vagy az Azure ad-ben. Azok csak az a funkció engedélyezéséhez használt, és utána eldobja a sikeres telepítést követően.
   > * Egy számítógép-fiók (amely az Azure AD) AZUREADSSOACC nevű jön létre a helyszíni Active Directory (AD).
   > * A számítógépfiók Kerberos visszafejtési kulcs biztonságosan megosztott Azure AD-val.
   > * Emellett két Kerberos egyszerű szolgáltatásnevek (SPN) jön létre, amelyek a két URL-címet, amelyekre az Azure AD-be.
   
   6. A Ready to Configure képernyő, ellenőrizze, hogy "Start szinkronizálási folyamat befejezése után a konfigurációs" jelölőnégyzet be van jelölve. Ezután válassza ki a konfigurálása.
   ![Kép: 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Amikor kiválasztja, zökkenőmentes egyszeri Bejelentkezést az előzetes verziók lépés megfelelően lesz konfigurálva. Nem lehet módosítani a jelszó a Jelszókivonat-szinkronizálás konfigurálása, ahogy azt korábban már engedélyezve van.
   
   > [!IMPORTANT]
   > Nem lesz kell módosítás ahhoz, ahogyan a felhasználók ezen a ponton jelentkeznek be.  
   
   7. Az Azure AD portálra ellenőrizze, hogy összevonási továbbra is engedélyezhető, és most zökkenőmentes egyszeri bejelentkezés engedélyezve van.
   ![Kép: 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Felügyelt összevont tartományokban konvertálása

Ezen a ponton összevonási továbbra is engedélyezett és a tartományok működési áll. A telepítés folytatásához, minden egyes tartományhoz kell alakítható át az összevont felhasználók hitelesítésének Jelszókivonat-szinkronizálás kényszerítése felügyelt.

> [!IMPORTANT]
> Nem minden tartományban kell a konvertálható egy időben, a kezdéshez választhatja az éles környezetbeli bérlőhöz vagy a tartomány legalább egy teszt tartomány felhasználók száma.

Az átalakítás az Azure AD PowerShell modul használatával történik.

   1. Nyissa meg a Powershellt, a bejelentkezés az Azure AD globális rendszergazdai fiók használatával.  
   2. Konvertálja az első tartományt, futtassa a következő parancsot:  
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   3. Nyissa meg az Azure AD portálra, válassza ki az Azure Active Directoryban, és válassza az Azure AD Connect.
   4. Győződjön meg arról, hogy a tartomány lett konvertálva felügyelt a következő parancs futtatásával:
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Tesztelés és a következő lépések

### <a name="test-authentication-with-phs"></a>Hitelesítés tesztelése-nál

A bérlő összevonási rendszer használata esetén felhasználók voltak oldalra való átirányítást a az Azure AD bejelentkezési oldal az AD FS-környezethez. Most, hogy a bérlő összevonási helyett a Jelszókivonat-szinkronizálás használatára van konfigurálva, a felhasználók nem get átirányítja az AD FS-hez, és inkább alá fogja írni közvetlenül az Azure AD bejelentkezési oldal.

Nyissa meg az Internet Explorer elkerülése érdekében a közvetlen egyszeri bejelentkezés automatikus bejelentkezés, és nyissa meg az Office 365 bejelentkezési oldala InPrivate módban ([http://portal.office.com](http://portal.office.com/)). Írja be a felhasználó egyszerű Felhasználónevét, majd kattintson a Tovább gombra. Ellenőrizze, hogy írja be, amelyeket a rendszer a helyszíni Active Directoryból szinkronizált, és akik korábban már volt összevont hibrid felhasználó egyszerű Felhasználóneve. A felhasználók számára megjelenik a képernyőn írja be a felhasználónevével és jelszavával.

![Kép 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Kép: 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Miután megadta a jelszót, az Office 365 portálra első átirányítva.

![Kép 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Közvetlen egyszeri bejelentkezés tesztelése

Jelentkezzen be a vállalati hálózathoz csatlakozó tartományhoz csatlakozó gépről. Nyissa meg az Internet Explorert, és nyissa meg a következő URL-címek egyikére:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (cserélje le a tartomány Contoso).

A felhasználó röviden irányítja az Azure AD bejelentkezési oldalára, és a "Próbál a bejelentkezés" üzenet jelenik meg, és nem felkéri a felhasználónév vagy jelszó.

![Kép 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Ezután a felhasználó fog első irányítja át, és sikeresen bejelentkezett a hozzáférési panelen:

> [!NOTE]
> Zökkenőmentes egyszeri bejelentkezést az Office 365-szolgáltatásokhoz (például myapps.microsoft.com/contoso.com) tartományemlékeztető támogató működik. Az Office 365 portálon (portal.office.com) jelenleg nem támogatja a tartományemlékeztető, és ezért valószínű, hogy a felhasználóknak kell-e egyszerű típusra. Után egy egyszerű felhasználónév megadott, zökkenőmentes egyszeri bejelentkezést az lekérni a Kerberos-jegy, a felhasználó nevében, és jelentkezzen őket a jelszó beírása nélkül. 

> [!NOTE]
> Érdemes megfontolni a [a hibrid Azure AD Join a Windows 10-es](https://docs.microsoft.com/azure/active-directory/device-management-introduction) továbbfejlesztett egyszeri bejelentkezési élményt biztosít.

### <a name="removal-of-the-relying-party-trust"></a>A függő entitás megbízhatóságához eltávolítása

Miután ellenőrizte, hogy minden felhasználó és az ügyfelek vannak a sikeres hitelesítés az Azure AD-n keresztül, azt is figyelembe kell venni az Office 365, a függő entitás megbízhatóságának eltávolítása biztonságos.

Ha az AD FS (más függő entitás Megbízhatóságai vannak konfigurálva), más célra nem használja, már biztonságosan ADFS most leszerelése.

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

### <a name="troubleshooting"></a>Hibaelhárítás

A támogatási csapat ismerje meg minden olyan hitelesítési problémák merülnek fel a közben vagy után a változás a összevonási felügyelni hibaelhárítása. A támogatási csapat megismerkedhetnek a gyakori hibaelhárítási lépéseket és a megfelelő műveleteket, amelyek segítenek elkülöníteni, és a probléma megoldásához az alábbi hibaelhárítási dokumentáció segítségével.

[Az Azure Active Directory a Jelszókivonat-szinkronizálás hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Vihetők át a zökkenőmentes egyszeri Bejelentkezést a Kerberos-visszafejtés

Fontos, hogy milyen gyakran vihetők át a Kerberos visszafejtési kulcs (amely az Azure AD) AZUREADSSOACC számítógépfiók létrehozása a helyszíni AD-erdőben. Kifejezetten ajánljuk, hogy hosszabbítsa a Kerberos-visszafejtési kulcs legalább 30 naponta igazodva hogyan be az Active Directory-tartomány tagjai a jelszó módosítására. Mivel nem lett a nem kapcsolódó AZUREADSSOACC számítógépfiók-objektumból az vihetők át a csatlakoztatott eszköz kell manuálisan kell elvégezni.

Kövesse az alábbi lépéseket a helyszíni kiszolgálón a Kerberos-visszafejtési kulcs az alkalmatlanság kezdeményezni az Azure AD Connect futtatja.

[Hogyan visszaválthatok keresztül a Kerberos visszafejtési kulcs AZUREADSSOACC számítógépfiók](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>További lépések

- [Az Azure AD Connect tervezési alapelvei](plan-connect-design-concepts.md)
- [Válassza ki a megfelelő hitelesítés](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Támogatott topológiák](plan-connect-design-concepts.md)
