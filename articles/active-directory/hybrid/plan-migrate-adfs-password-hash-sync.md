---
title: 'Azure AD Connect: Áttelepítés összevonásról PHS-re az Azure AD-hez | Microsoft dokumentumok'
description: Ez a cikk a hibrid identitáskörnyezet összevonásról jelszókivonat-szinkronizálásra való áthelyezésével kapcsolatos információkat tartalmaz.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b621c9cbc35d0e9956f6648d870102affd84c24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028392"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Áttelepítés összevonásról jelszókivonat-szinkronizálásra az Azure Active Directoryszámára

Ez a cikk azt ismerteti, hogy a szervezeti tartományok hogyan helyezhető át az Active Directory összevonási szolgáltatásokból (AD FS) a jelszókivonat-szinkronizálásba.

> [!NOTE]
> A hitelesítési módszer módosításához tervezésre, tesztelésre és potenciálisan állásidőre van szükség. [A szakaszos bevezetés](how-to-connect-staged-rollout.md) alternatív módot kínál a jelszókivonat-szinkronizálás tesztelésére és fokozatos áttelepítésére az összevonásról a felhőalapú hitelesítésre.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>A jelszókivonat-szinkronizálásra való áttérés előfeltételei

A következő előfeltételek szükségesek az AD FS-ről a jelszókivonat-szinkronizálás használatára való áttelepítéshez.

### <a name="update-azure-ad-connect"></a>Az Azure AD Connect frissítése

Legalább a jelszókivonat-szinkronizálásra való áttelepítés hez szükséges lépések sikeres végrehajtásához az Azure AD 1.1.819.0-s csatlakozással kell [rendelkeznie.](https://www.microsoft.com/download/details.aspx?id=47594) Ez a verzió jelentős változásokat tartalmaz a bejelentkezési átalakítás elvégzésének módjában, és potenciálisan órákról percekre csökkenti az összevonásról a felhőalapú hitelesítésre való áttérés teljes idejét.


> [!IMPORTANT]
> Előfordulhat, hogy elavult dokumentációkban, eszközökben és blogokban olvassa el, hogy a felhasználók konverziója szükséges, ha tartományokat konvertál összevont identitásból felügyelt identitássá. *A felhasználók konvertálása* már nem szükséges. A Microsoft azon dolgozik, hogy frissítse a dokumentációt és az eszközöket, hogy azok tükrözzék ezt a változást.

Az Azure AD Connect frissítéséhez hajtsa végre az [Azure AD Connect: Frissítés a legújabb verzióra](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)című lépéseit.

### <a name="password-hash-synchronization-required-permissions"></a>A jelszókivonat-szinkronizálás szükséges engedélyei

Az Azure AD Connect konfigurálható expressz beállításokkal vagy egyéni telepítéssel. Ha az egyéni telepítési beállítást használta, előfordulhat, hogy a jelszókivonat-szinkronizáláshoz [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) nincsenek érvényben.

Az Azure AD Connect Active Directory tartományi szolgáltatások (AD DS) szolgáltatásfiókhoz a következő engedélyek szükségesek a jelszókivétek szinkronizálásához:

* Címtármódosítások replikálása
* Könyvtármódosítás-módosítások replikálása

Most van itt az ideje annak ellenőrzésére, hogy ezek az engedélyek az erdő összes tartományára vonatkozóan érvényben vannak-e.

### <a name="plan-the-migration-method"></a>Az áttelepítési módszer megtervezése

Az összevont identitáskezelésről a jelszókivonat-szinkronizálásra és a zökkenőmentes egyszeri bejelentkezésre (SSO) való áttelepítés két módszer közül választhat. A használt módszer attól függ, hogy az AD FS-példány eredetileg hogyan lett konfigurálva.

* **Azure AD Connect**. Ha eredetileg konfigurált AD FS az Azure AD Connect használatával, az Azure AD Connect varázsló használatával módosítania *kell* a jelszókivonat-szinkronizálást.

   Az Azure AD Connect automatikusan futtatja a **Set-MsolDomainAuthentication** parancsmag, amikor módosítja a felhasználói bejelentkezési módszert. Az Azure AD Connect automatikusan kikapcsolja az Azure AD-bérlő összes ellenőrzött összevont tartományát.

   > [!NOTE]
   > Jelenleg, ha eredetileg az Azure AD Connect konfigurálása AD FS konfigurálása, nem kerülheti el a bérlő összes tartományának kikapcsolása, amikor módosítja a felhasználói bejelentkezés jelszókivonat-szinkronizálás. ‎
* **Az Azure AD Connect a PowerShelllel.** Ezt a módszert csak akkor használhatja, ha eredetileg nem konfigurálta az AD FS-t az Azure AD Connect használatával. Ehhez a beállításhoz továbbra is módosítania kell a felhasználói bejelentkezési módszert az Azure AD Connect varázslón keresztül. Az alapvető különbség ezzel a beállítással az, hogy a varázsló nem futtatja automatikusan a **Set-MsolDomainAuthentication** parancsmagot. Ezzel a beállítással teljes mértékben szabályozhatja, hogy mely tartományokat konvertálja a program, és milyen sorrendben.

A következő szakaszokban ismertesse, hogy melyik módszert kell használnia.

#### <a name="verify-current-user-sign-in-settings"></a>A jelenlegi felhasználói bejelentkezési beállítások ellenőrzése

Az aktuális felhasználói bejelentkezési beállítások ellenőrzése:

1. Jelentkezzen be az [Azure AD-portálon](https://aad.portal.azure.com/) egy globális rendszergazdai fiók használatával.
2. A **Felhasználó bejelentkezésszakaszában** ellenőrizze a következő beállításokat:
   * **Az összevonás** **beállítása Engedélyezve.**
   * **A zökkenőmentes egyszeri bejelentkezés** **letiltva**értékre van állítva.
   * **Az átmenő hitelesítés** **beállítása Letiltva.**

   ![Képernyőkép az Azure AD Connect felhasználói bejelentkezési szakaszának beállításairól](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Az Azure AD Connect konfigurációjának ellenőrzése

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect et. Válassza a **Konfigurálás** lehetőséget.
2. A **További feladatok** lapon válassza az **Aktuális konfiguráció megtekintése**lehetőséget, majd a **Tovább**gombot.<br />

   ![Képernyőkép a További feladatok lapon kijelölt Aktuális konfiguráció megtekintése beállításról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. A **Megoldás áttekintése** lapon jegyezze fel a **Jelszó-kivonat szinkronizálási** állapotát.<br /> 

   * Ha **a jelszókivonat-szinkronizálás** **letiltva**beállítással rendelkezik, hajtsa végre a cikkben ismertetett lépéseket az engedélyezéséhez.
   * Ha **a jelszókivonat-szinkronizálás** **engedélyezve**van, akkor kihagyja az **1.**
4. A **Megoldás áttekintése** lapon görgessen az **Active Directory összevonási szolgáltatások (AD FS) elemre.**<br />

   * Ha az AD FS-konfiguráció jelenik meg ebben a szakaszban, nyugodtan feltételezheti, hogy az AD FS eredetileg az Azure AD Connect használatával konfigurált. Az Azure AD Connect **Change felhasználói bejelentkezési** beállítással konvertálhatja a tartományokat az összevont identitásból felügyelt identitássá. A folyamat részletesen az **A lehetőség: Váltás az összevonás ról a jelszó kivonat szinkronizálása az Azure AD Connect használatával.**
   * Ha az AD FS nem szerepel a jelenlegi beállításokban, manuálisan kell konvertálnia a tartományokat összevont identitásból felügyelt identitássá a PowerShell használatával. A folyamatról további információt a **B lehetőség: Váltás összevonásról jelszókivonat-szinkronizálásra**az Azure AD Connect és a PowerShell használatával című szakaszban talál.

### <a name="document-current-federation-settings"></a>Az aktuális összevonási beállítások dokumentálása

Az aktuális összevonási beállítások megkereséséhez futtassa a **Get-MsolDomainFederationSettings** parancsmalapot:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Példa:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Ellenőrizze, hogy milyen beállításokat szabtak testre az összevonási tervezési és telepítési dokumentációhoz. A **PreferAuthenticationProtocol**, **SupportsMfa**és **PromptLoginBehavior**alkalmazásban keressen testreszabásokat.

További információval a következő cikkek szolgálnak:

* [AD FS prompt=bejelentkezési paraméter támogatása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Ha **a SupportsMfa** **értéke Igaz,** helyszíni többtényezős hitelesítési megoldást használ egy második tényezős kihívás befecskendezéséhez a felhasználói hitelesítési folyamatba. Ez a beállítás már nem működik az Azure AD hitelesítési forgatókönyvek átalakítása után ezt a tartományt összevont felügyelt hitelesítés. Az összevonás letiltása után megszakítja a kapcsolatot a helyszíni összevonásával, és ez magában foglalja a helyszíni MFA-adaptereket is. 
>
> Ehelyett használja az Azure többtényezős hitelesítés felhőalapú szolgáltatás ugyanazt a funkciót. A folytatás előtt gondosan értékelje ki a többtényezős hitelesítési követelményeket. A tartományok konvertálása előtt győződjön meg arról, hogy tisztában van az Azure többtényezős hitelesítés használatával, a licencelési következményekkel és a felhasználói regisztrációs folyamattal.

#### <a name="back-up-federation-settings"></a>Összevonási beállítások biztonsági és biztonsági őrei

Bár a jelen cikkben ismertetett folyamatok során az AD FS-farm többi függő entitását nem módosítja, azt javasoljuk, hogy az AD FS-farm aktuális érvényes biztonsági másolatát állítsa vissza, amelyről visszaállíthatja. Az ingyenes Microsoft [AD FS Gyors-visszaállítási eszközzel](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)létrehozhat egy érvényes biztonsági másolatot. Az eszközzel biztonsági másolatot készíthet az AD FS fájlrendszerről, visszaállíthatja a meglévő farmot, vagy létrehozhat egy új farmot.

Ha úgy dönt, hogy nem használja az AD FS gyors-visszaállítási eszközt, legalább exportálja a Microsoft Office 365 identitásplatformfüggő fél megbízhatóságát és a hozzáadott egyéni jogcímszabályokat. Exportálhatja a függő entitás megbízhatóságát és a kapcsolódó jogcímszabályokat a következő PowerShell-példa használatával:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Üzembe helyezési szempontok és az AD FS használata

Ez a szakasz az AD FS használatával kapcsolatos központi telepítési szempontokat és részleteket ismerteti.

### <a name="current-ad-fs-use"></a>Az AD FS jelenlegi használata

Mielőtt az összevont identitásból felügyelt identitássá alakítana, alaposan vizsgálja meg, hogyan használja jelenleg az AD FS-t az Azure AD, az Office 365 és más alkalmazásokhoz (függő entitás megbízhatósága). Az alábbi táblázatban ismertetett forgatókönyveket különösen figyelembe kell venni:

| Ha a(z) | Akkor |
|-|-|
| Azt tervezi, hogy továbbra is használja az AD FS-t más alkalmazásokkal (az Azure AD és az Office 365-ön kívül). | A tartományok konvertálása után az AD FS és az Azure AD használatát is használni fogja. Vegye figyelembe a felhasználói élményt. Bizonyos esetekben előfordulhat, hogy a felhasználóknak kétszer kell hitelesíteniük magukat: egyszer az Azure AD-hez (ahol a felhasználó egyszeri bejelentkezést kap más alkalmazásokhoz, például az Office 365-höz), és újra minden olyan alkalmazás esetében, amely továbbra is függőként kötődik az AD FS-hez, mint függő entitás megbízhatósága. |
| Az AD FS-példány erősen testre szabott, és az onload.js fájl ban megadott testreszabási beállításokra támaszkodik (például ha úgy módosította a bejelentkezési élményt, hogy a felhasználók csak **egy SamAccountName** formátumot használjanak a felhasználónevükhöz a felhasználónév (UPN) helyett, vagy a szervezet erősen megbélyegezte a bejelentkezési élményt). Az onload.js fájl nem kettőzhető az Azure AD-ben. | Mielőtt folytatna, ellenőriznie kell, hogy az Azure AD megfelel-e az aktuális testreszabási követelményeknek. További információt és útmutatást az AD FS márkajelzésről és az AD FS testreszabásáról szóló szakaszokban talál.|
| Az AD FS segítségével blokkolja a hitelesítési ügyfelek korábbi verzióit.| Fontolja meg a hitelesítési ügyfelek korábbi verzióit tiltó AD FS-vezérlők cseréjét [feltételes hozzáférés-vezérlők](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) és [Exchange Online ügyfélkapcsolati szabályok](https://aka.ms/EXOCAR)kombinációjával. |
| A felhasználóknak többtényezős hitelesítést kell végrehajtaniuk egy helyszíni többtényezős hitelesítési kiszolgálón, amikor a felhasználók hitelesítik magukat az AD FS szolgáltatásban.| Felügyelt identitástartományban nem adhat be többtényezős hitelesítési kihívást a helyszíni többtényezős hitelesítési megoldáson keresztül a hitelesítési folyamatba. Azonban használhatja az Azure többtényezős hitelesítési szolgáltatás többtényezős hitelesítés a tartomány konvertálása után.<br /><br /> Ha a felhasználók jelenleg nem használják az Azure többtényezős hitelesítést, egy egyszeri felhasználói regisztrációs lépés szükséges. Elő kell készítenie a tervezett regisztrációt, és közölnie kell a felhasználókkal. |
| Az AD FS programban jelenleg hozzáférés-vezérlési házirendeket (AuthZ-szabályokat) használ az Office 365-höz való hozzáférés szabályozásához.| Fontolja meg a házirendek megfelelő Azure AD [feltételes hozzáférési szabályzatokkal](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) és [Exchange Online ügyfélhozzáférési szabályokkal](https://aka.ms/EXOCAR)való lecserélését.|

### <a name="common-ad-fs-customizations"></a>Gyakori AD FS-testreszabások

Ez a szakasz az AD FS gyakori testreszabásait ismerteti.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork jogcím

Az AD FS kiadja az **InsideCorporateNetwork** jogcímet, ha a hitelesítő felhasználó a vállalati hálózaton belül van. Ez a jogcím ezután továbbadható az Azure AD-nek. A jogcím a felhasználó hálózati helye alapján kerüli meg a többtényezős hitelesítést. Arról, hogy miként állapíthatja meg, hogy ez a funkció jelenleg engedélyezve van-e az AD FS szolgáltatásban, olvassa el [a Megbízható IP-k összevont felhasználók számára](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

Az **InsideCorporateNetwork** jogcím nem érhető el, miután a tartományok jelszókivonat-szinkronizálássá konvertálódnak. Az [Azure AD-ben elnevezett helyek](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) használatával lecserélheti ezt a funkciót.

Miután konfigurálta az elnevezett helyeket, frissítenie kell az összes feltételes hozzáférési házirendet, amelyek úgy vannak konfigurálva, hogy tartalmazzák vagy kizárják a **hálózatot Minden megbízható hely** vagy **az MFA megbízható IP-értékeit,** hogy azok tükrözzék az új elnevezett helyeket.

A Feltételes hozzáférés **helyfeltételéről** az [Active Directory feltételes hozzáférési helyei című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)talál további információt.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-hez csatlakozó eszközök

Amikor csatlakozik egy eszközhöz az Azure AD-hez, feltételes hozzáférési szabályokat hozhat létre, amelyek kényszerítik, hogy az eszközök megfelelnek a biztonsági és megfelelőségi hozzáférési szabványoknak. Emellett a felhasználók személyes fiók helyett szervezeti munkahelyi vagy iskolai fiókkal is bejelentkezhetnek egy eszközre. Ha hibrid Azure AD-hez csatlakozott eszközöket használ, csatlakozhat az Active Directoryhoz tartományhoz csatlakozott eszközökhöz az Azure AD-hez. Előfordulhat, hogy az összevont környezet be van állítva a funkció használatára.

Annak érdekében, hogy a hibrid csatlakozás továbbra is működjön minden olyan eszközön, amely a tartományok jelszókivonat-szinkronizálássá konvertálása után csatlakozik a tartományhoz, windows 10-es ügyfelek esetén az Azure AD Connect eszközbeállításait kell használnia az Active Directory számítógép szinkronizálásához. fiókokat az Azure AD-be. 

Windows 8-as és Windows 7-es számítógépfiókok esetén a hibrid csatlakozás zökkenőmentes egyszeri bejelentkezést használ a számítógép Regisztrálásához az Azure AD-ben. Nem kell szinkronizálnia a Windows 8 és a Windows 7 számítógépfiókokat, mint a Windows 10-es eszközökesetében. Azonban telepítenie kell egy frissített workplacejoin.exe fájlt (egy .msi fájlon keresztül) a Windows 8 és a Windows 7 ügyfelekszámára, hogy zökkenőmentes egyszeri bejelentkezéssel regisztrálhassák magukat. [Töltse le az .msi fájlt](https://www.microsoft.com/download/details.aspx?id=53554).

További információ: [Hibrid Azure AD-hez csatlakozó eszközök konfigurálása.](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Védjegyezés

Ha a szervezet [testre szabta az AD FS bejelentkezési lapjait,](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) hogy a szervezet számára relevánsabb információkat jelenítsen meg, fontolja meg [az Azure AD bejelentkezési lapjához hasonló testreszabásokat.](https://docs.microsoft.com/azure/active-directory/customize-branding)

Bár hasonló testreszabások érhetők el, a bejelentkezési oldalakon a konverzió után néhány vizuális változásra kell számítani. Előfordulhat, hogy a felhasználóknak tájékoztatást szeretne adni a kommunikáció ban várható változásokról.

> [!NOTE]
> A szervezet márkajelzése csak akkor érhető el, ha megvásárolja az Azure Active Directory prémium vagy alapszintű licencét, vagy ha Office 365-licenccel rendelkezik.

## <a name="plan-deployment-and-support"></a>Telepítés és támogatás megtervezése

Végezze el az ebben a szakaszban ismertetett feladatokat, amelyek segítséget nyújtanak az üzembe helyezés és támogatás megtervezéséhez.

### <a name="plan-the-maintenance-window"></a>A karbantartási időszak megtervezése

Bár a tartomány konverziós folyamat viszonylag gyors, az Azure AD továbbra is küldhet bizonyos hitelesítési kérelmeket az AD FS-kiszolgálók legfeljebb négy órával a tartomány átalakítása befejezése után. Ebben a négy órás időszakban, és attól függően, hogy a különböző szolgáltatásoldali gyorsítótárak, az Azure AD előfordulhat, hogy nem fogadja el ezeket a hitelesítéseket. A felhasználók hibaüzenetet kaphatnak. A felhasználó továbbra is sikeresen hitelesíthető az AD FS szolgáltatással szemben, de az Azure AD már nem fogadja el a felhasználó által kibocsátott jogkivonatot, mert az összevonási megbízhatóság ida már eltávolításra kerül.

Ez csak azaz az érintettek érintettek, akik a konverzió utáni időszakban webböngészőn keresztül férnek hozzá a szolgáltatásokhoz. Az örökölt ügyfeleket (Exchange ActiveSync, Outlook 2010/2013) várhatóan ez nem érinti, mert az Exchange Online egy meghatározott ideig gyorsítótárban tartja a hitelesítő adatokat. A gyorsítótár segítségével csendben újra hitelesíti a felhasználót. A felhasználónak nem kell visszatérnie az AD FS rendszerhez. Az ezen ügyfelek eszközén tárolt hitelesítő adatok segítségével a gyorsítótárazott adatok törlődése után csendben újra hitelesítheti magát. A felhasználóktól nem várható, hogy a tartomány konverziós folyamata miatt jelszókérések jelenjenek meg. 

A modern hitelesítési ügyfelek (Office 2016 és Office 2013, iOS és Android-alkalmazások) érvényes frissítési jogkivonatot használnak az erőforrásokhoz való folyamatos hozzáféréshez szükséges új hozzáférési jogkivonatok beszerzéséhez, ahelyett, hogy visszatérnének az AD FS-hez. Ezek az ügyfelek immunisak a tartománykonverziós folyamatból eredő jelszókérésekre. Az ügyfelek további konfiguráció nélkül is működni fognak.

> [!IMPORTANT]
> Ne állítsa le az AD FS-környezetet, és ne távolítsa el az Office 365 függő entitás megbízhatóságát, amíg nem ellenőrizte, hogy minden felhasználó sikeresen hitelesítheti magát felhőalapú hitelesítéssel.

### <a name="plan-for-rollback"></a>Visszaállítás megtervezése

Ha olyan fontos problémával találkozik, amelyet nem tud gyorsan megoldani, dönthet úgy, hogy visszavonja a megoldást az összevonásra. Fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem megfelelően van betervezve. Ha a tartomány vagy a felhasználók átalakítása sikertelen a központi telepítés során, vagy ha vissza kell vonnia az összevonáshoz, meg kell értenie, hogyan csökkentheti a kimaradások és a felhasználókra gyakorolt hatás csökkentését.

#### <a name="to-roll-back"></a>Visszaállítás visszaállítása

A visszaállítás megtervezéséhez ellenőrizze az összevonás tervezési és üzembe helyezési dokumentációját a konkrét központi telepítési részletekért. A folyamatnak a következő feladatokat kell tartalmaznia:

* Felügyelt tartományok átalakítása összevont tartományokká a **Convert-MSOLDomainToFederated** parancsmag használatával.
* Szükség esetén további jogcímszabályok konfigurálása.

### <a name="plan-communications"></a>A kommunikáció tervezése

A telepítés és támogatás tervezésének fontos része annak biztosítása, hogy a felhasználók proaktív módon értesüljenek a közelgő változásokról. A felhasználóknak előre tudniuk kell, hogy mit tapasztalhatnak, és mire van szükség tőlük. 

A jelszókivonat-szinkronizálás és a zökkenőmentes egyszeri bejelentkezés telepítése után az Office 365 eléréséhez és az Azure AD-módosításokon keresztül hitelesített egyéb erőforrások hoz való hozzáférés felhasználói bejelentkezési élmény. A hálózaton kívüli felhasználók csak az Azure AD bejelentkezési lapot látják. Ezek a felhasználók nem lesznek átirányítva a külső elérésű webalkalmazás-proxykiszolgálók által bemutatott űrlapalapú lapra.

A következő elemek felvétele a kommunikációs stratégiába:

* Értesítse a felhasználókat a közelgő és a kiadott funkciókról a következő használatával:
   * E-mail és egyéb belső kommunikációs csatornák.
   * Vizualizációk, például poszterek.
   * ügyvezető, élő vagy egyéb kommunikáció.
* Határozza meg, hogy ki és ki és mikor küldi el a kommunikációt.

## <a name="implement-your-solution"></a>Valósítsa meg a megoldást

Kitervelted a megoldásodat. Most már végre is valósíthatja. A megvalósítás a következő összetevőket foglalja magában:

* Jelszókivonat-szinkronizálás engedélyezése.
* Felkészülés a zökkenőmentes egyszeri bejelentkezésre.
* A bejelentkezési módszer jelszókivonat-szinkronizálásra való módosítása és a zökkenőmentes egyszeri bejelentkezés engedélyezése.

### <a name="step-1-enable-password-hash-synchronization"></a>1. lépés: Jelszókivonat-szinkronizálás engedélyezése

A megoldás megvalósításának első lépése a jelszókivonat-szinkronizálás engedélyezése az Azure AD Connect varázsló használatával. A jelszókivonat-szinkronizálás egy választható szolgáltatás, amelyet az összevonást használó környezetekben engedélyezhet. Nincs hatással a hitelesítési folyamatra. Ebben az esetben az Azure AD Connect elindítja a jelszókihések szinkronizálását anélkül, hogy befolyásolná az összevonással bejelentkező felhasználókat.

Ezért azt javasoljuk, hogy ezt a lépést előkészítő feladatként hajtsa végre jóval a tartomány bejelentkezési metódusának módosítása előtt. Ezt követően elegendő ideje lesz ellenőrizni, hogy a jelszókivonat-szinkronizálás megfelelően működik-e.

A jelszókivonat-szinkronizálás engedélyezése:

1. Az Azure AD Connect kiszolgálón nyissa meg az Azure AD Connect varázslót, és válassza a **Konfigurálás**lehetőséget.
2. Válassza **a Szinkronizálási beállítások testreszabása**lehetőséget, majd a **Tovább**gombot.
3. A Csatlakozás az **Azure AD-hez** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **Könyvtárak csatlakoztatása** lapon válassza a **Tovább gombot.**
5. A **Tartomány- és szervezetiegység-szűrés** lapon válassza a **Tovább gombot.**
6. A **Választható szolgáltatások** lapon válassza a **Jelszó-szinkronizálás**lehetőséget, majd a **Tovább**gombot.
 
   ![Képernyőkép a Választható szolgáltatások lapon kiválasztott Jelszó-szinkronizálási beállításról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. A fennmaradó oldalakon válassza a **Tovább** gombot. Az utolsó lapon válassza a **Konfigurálás**lehetőséget.
8. Az Azure AD Connect megkezdi a jelszókivétszinkronizálást a következő szinkronizáláskor.

Miután a jelszókivonat-szinkronizálás engedélyezve van, az Azure AD Connect szinkronizálási hatókörében lévő összes felhasználó jelszókivonatait a rendszer újraéssze és az Azure AD-be írja. A felhasználók számától függően ez a művelet percekig vagy több óráig is eltarthat.

Tervezési célokra meg kell becsülni, hogy körülbelül 20 000 felhasználó feldolgozása 1 óra alatt.

Annak ellenőrzéséhez, hogy a jelszókivonat-szinkronizálás megfelelően működik-e, végezze el a **hibaelhárítási** feladatot az Azure AD Connect varázslóban:

1. Nyisson meg egy új Windows PowerShell-munkamenetet az Azure AD Connect-kiszolgálón a Futtatás rendszergazdaként beállítással.
2. Futtassa `Set-ExecutionPolicy RemoteSigned` a vagy `Set-ExecutionPolicy Unrestricted`a ot.
3. Indítsa el az Azure AD Connect varázslót.
4. Nyissa meg a **További feladatok** lapot, válassza **a Hibaelhárítás**lehetőséget, majd a **Tovább**gombot.
5. A **Hibaelhárítás** lapon válassza az **Indítás** gombot a PowerShell hibaelhárítási menüjének elindításához.
6. A főmenüben válassza a **Jelszókivonat-szinkronizálás hibaelhárítása lehetőséget.**
7. Az almenüben válassza a **Jelszókivonat-szinkronizálás parancsát.**

A problémák elhárításához olvassa el a [Jelszó-kivonat szinkronizálásának elhárítása az Azure AD Connect szinkronizálásával című témakört.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

### <a name="step-2-prepare-for-seamless-sso"></a>2. lépés: Készüljön fel a zökkenőmentes egyszeri bejelentkezésre

Ahhoz, hogy az eszközök zökkenőmentes egyszeri bejelentkezést használhassanak, hozzá kell adnia egy Azure AD-URL-címet a felhasználók intranetes zónabeállításaihoz az Active Directory csoportházirendjének használatával.

Alapértelmezés szerint a webböngészők automatikusan kiszámítják a megfelelő zónát , akár az internetet, akár az intranetet egy URL-ből. Például **http:\/\/contoso/** leképezi az intranetes zóna és **\/\/http: intranet.contoso.com** leképezi az internetzónát (mert az URL-cím tartalmaz egy pont). A böngészők kerberos-jegyeket küldenek egy felhővégpontra, például az Azure AD URL-címére, csak akkor, ha explicit módon hozzáadja az URL-címet a böngésző intranetes zónájához.

Végezze el a szükséges módosítások [bevezetéséhez](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) szükséges lépéseket.

> [!IMPORTANT]
> A módosítás nem módosítja a felhasználók Azure AD-be való bejelentkezésének módját. Fontos azonban, hogy ezt a konfigurációt a folytatás előtt alkalmazza az összes eszközére. Azok a felhasználók, akik nem kapták meg ezt a konfigurációt, egyszerűen meg kell adniuk egy felhasználónevet és egy jelszót az Azure AD-be való bejelentkezéshez.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>3. lépés: Módosítsa a bejelentkezési módszert jelszókivonat-szinkronizálásra, és engedélyezze a zökkenőmentes egyszeri bejelentkezést

Két lehetősége van a bejelentkezési módszer jelszókivonat-szinkronizálásra történő módosítására és a zökkenőmentes egyszeri bejelentkezés engedélyezésére.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>A. lehetőség: Váltás összevonásról jelszókivonat-szinkronizálásra az Azure AD Connect használatával

Akkor használja ezt a módszert, ha először konfigurálta az AD FS-környezetet az Azure AD Connect használatával. Ez a módszer nem használható, ha eredetileg *nem* konfigurálta az AD FS-környezetet az Azure AD Connect használatával.

Először módosítsa a bejelentkezési módszert:

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect varázslót.
2. Válassza **a Felhasználó bejelentkezésének módosítása**lehetőséget, majd a **Tovább**gombot. 

   ![Képernyőkép a Felhasználói bejelentkezés módosítása beállításról a További feladatok lapon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. A Csatlakozás az **Azure AD-hez** lapon adja meg egy globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **Felhasználó bejelentkezése** lapon válassza a **Jelszókivonat-szinkronizálás gombot**. Győződjön meg arról, hogy bekell jelölnie a **Felhasználói fiókok konvertálásának nem történt jelölőnégyzetet.** A beállítás elavult. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget, majd a **Tovább**gombot.

   ![Képernyőkép az Egyszeri bejelentkezés engedélyezése lapról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Az Azure AD Connect 1.1.880.0-s verziójával kezdve a **Zökkenőmentes egyszeri bejelentkezés** jelölőnégyzet alapértelmezés szerint be van jelölve.

   > [!IMPORTANT]
   > Nyugodtan figyelmen kívül hagyhatja azon figyelmeztetéseket, amelyek azt jelzik, hogy a felhasználói konverzió és a teljes jelszókivonat-szinkronizálás szükséges lépések az összevonásról a felhőalapú hitelesítésre való átalakításhoz. Vegye figyelembe, hogy ezek a lépések már nem szükségesek. Ha továbbra is megjelennek ezek a figyelmeztetések, győződjön meg arról, hogy az Azure AD Connect legújabb verzióját futtatja, és hogy az útmutató legújabb verzióját használja. További információt az [Azure AD Connect frissítése](#update-azure-ad-connect)című szakaszban talál.

5. Az **Egyszeri bejelentkezés engedélyezése** lapon adja meg a Tartományfelügyelő fiók hitelesítő adatait, majd kattintson a **Tovább**gombra.

   ![Képernyőkép az Egyszeri bejelentkezés engedélyezése lapról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > A tartományrendszergazdai fiók hitelesítő adatai szükségesek a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez. A folyamat a következő műveleteket hajtja végre, amelyek megkövetelik ezeket az emelt szintű engedélyeket. A tartományi rendszergazdai fiók hitelesítő adatait nem tárolja az Azure AD Connect vagy az Azure AD. A tartományi rendszergazdai fiók hitelesítő adatai csak a szolgáltatás bekapcsolására szolgálnak. A hitelesítő adatok elvetésre kerülnek, ha a folyamat sikeresen befejeződik.
   >
   > 1. Az AZUREADSSOACC nevű (az Azure AD-t jelölő) számítógépfiók a helyszíni Active Directory-példányban jön létre.
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos szolgáltatásegyszerű név (SPN) jön létre, hogy képviselje az Azure AD-bejelentkezés során használt két URL-címeket.

6. A **Konfigurálásra kész** lapon győződjön meg arról, hogy **a szinkronizálási folyamat indítása a konfiguráció befejezésekor** jelölőnégyzet be van jelölve. Ezután válassza a **Konfigurálás**lehetőséget.

      ![Képernyőkép a Konfigurálásra kész lapról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Ezen a ponton az összes összevont tartományok fog változni a felügyelt hitelesítés. A jelszókivonat-szinkronizálás az új hitelesítési módszer.

7. Az Azure AD portálon válassza az **Azure Active Directory** > **Azure AD Connect lehetőséget.**
8. Ellenőrizze a következő beállításokat:
   * **Az összevonás** **letiltva**értékre van állítva.
   * **A zökkenőmentes egyszeri bejelentkezés** **beállítása Engedélyezve**.
   * **A Jelszó-szinkronizálás** beállítása **Engedélyezve**.<br /> 

   ![Képernyőkép, amely a Felhasználó bejelentkezési szakaszában lévő beállításokat jeleníti meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Ugrás a tesztelésre és a [következő lépésekre.](#testing-and-next-steps)

   > [!IMPORTANT]
   > A **B lehetőség: Váltás összevonásról jelszókivonat-szinkronizálásra az Azure AD Connect és a PowerShell használatával.** Az ebben a szakaszban leírt lépések nem érvényesek, ha az A lehetőséget választotta a bejelentkezési módszer jelszókivonat-szinkronizálásra való módosításához, és a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>B. lehetőség: Váltás összevonásról jelszókivonat-szinkronizálásra az Azure AD Connect és a PowerShell használatával

Akkor használja ezt a beállítást, ha kezdetben nem konfigurálta az összevont tartományokat az Azure AD Connect használatával. A folyamat során zökkenőmentes egyszeri bejelentkezést engedélyez, és a tartományokat összevontról felügyeltre váltja.

1. Az Azure AD Connect-kiszolgálón nyissa meg az Azure AD Connect varázslót.
2. Válassza **a Felhasználó bejelentkezésének módosítása**lehetőséget, majd a **Tovább**gombot.
3. A Csatlakozás az **Azure AD-hez** lapon adja meg a globális rendszergazdai fiók felhasználónevét és jelszavát.
4. A **Felhasználó bejelentkezési** lapján válassza a **Jelszókivonat-szinkronizálás** gombot. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget, majd a **Tovább**gombot.

   A jelszókivonat-szinkronizálás engedélyezése előtt: ![Képernyőkép, amelyen a Ne konfigurálás lehetőség látható a Felhasználó bejelentkezési lapján](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   A jelszókivonat-szinkronizálás engedélyezése után: ![Képernyőkép, amely új beállításokat jelenít meg a Felhasználó bejelentkezési lapján](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Az Azure AD Connect 1.1.880.0-s verziójával kezdve a **Zökkenőmentes egyszeri bejelentkezés** jelölőnégyzet alapértelmezés szerint be van jelölve.

5. Az **Egyszeri bejelentkezés engedélyezése** lapon adja meg egy tartományi rendszergazdai fiók hitelesítő adatait, majd kattintson a **Tovább**gombra.

   > [!NOTE]
   > A tartományrendszergazdai fiók hitelesítő adatai szükségesek a zökkenőmentes egyszeri bejelentkezés engedélyezéséhez. A folyamat a következő műveleteket hajtja végre, amelyek megkövetelik ezeket az emelt szintű engedélyeket. A tartományi rendszergazdai fiók hitelesítő adatait nem tárolja az Azure AD Connect vagy az Azure AD. A tartományi rendszergazdai fiók hitelesítő adatai csak a szolgáltatás bekapcsolására szolgálnak. A hitelesítő adatok elvetésre kerülnek, ha a folyamat sikeresen befejeződik.
   >
   > 1. Az AZUREADSSOACC nevű (az Azure AD-t jelölő) számítógépfiók a helyszíni Active Directory-példányban jön létre.
   > 2. A számítógépfiók Kerberos-visszafejtési kulcsa biztonságosan meg van osztva az Azure AD-vel.
   > 3. Két Kerberos szolgáltatásegyszerű név (SPN) jön létre, hogy képviselje az Azure AD-bejelentkezés során használt két URL-címeket.

6. A **Konfigurálásra kész** lapon győződjön meg arról, hogy **a szinkronizálási folyamat indítása a konfiguráció befejezésekor** jelölőnégyzet be van jelölve. Ezután válassza a **Konfigurálás**lehetőséget.

   ![Képernyőkép, amelyen a Konfigurálás gomb látható a Konfigurálásra való kész lapon](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Ha a Konfigurálás gombot **választja,** a zökkenőmentes egyszeri bejelentkezés az előző lépésben jelzettek szerint van konfigurálva. A jelszókivonat-szinkronizálás ikonfigurációja nem módosul, mert korábban engedélyezve volt.

   > [!IMPORTANT]
   > Jelenleg nem történik módosítás a felhasználók bejelentkezési módjában.

7. Az Azure AD portálon ellenőrizze a következő beállításokat:
   * **Az összevonás** **beállítása Engedélyezve.**
   * **A zökkenőmentes egyszeri bejelentkezés** **beállítása Engedélyezve**.
   * **A Jelszó-szinkronizálás** beállítása **Engedélyezve**.

   ![Képernyőkép, amely a Felhasználó bejelentkezési szakaszában lévő beállításokat jeleníti meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Tartományok konvertálása összevontról felügyeltre

Ezen a ponton az összevonás továbbra is engedélyezve van, és működőképes a tartományok. A központi telepítés folytatásához minden tartományt összevontról jelszókivonat-szinkronizálással kell kényszeríteni, hogy a felhasználó hitelesítése jelszókivonat-szinkronizálással történjen.

> [!IMPORTANT]
> Nem kell egyszerre konvertálnia az összes tartományt. Dönthet úgy, hogy az éles bérlő teszttartományával kezdi, vagy a legalacsonyabb számú felhasználóval rendelkező tartománnyal kezdi.

Végezze el az átalakítást az Azure AD PowerShell-modul használatával:

1. A PowerShellben jelentkezzen be az Azure AD-be egy globális rendszergazdai fiók használatával.
2. Az első tartomány konvertálásához futtassa a következő parancsot:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Az Azure AD portálon válassza az **Azure Active Directory** > **Azure AD Connect lehetőséget.**
4. Ellenőrizze, hogy a tartományt a következő parancs futtatásával alakították-e át felügyeltsé:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Tesztelés és a következő lépések

Végezze el a következő feladatokat a jelszókivonat-szinkronizálás ellenőrzéséhez és az átalakítási folyamat befejezéséhez.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Hitelesítés tesztelése jelszókivonat-szinkronizálással 

Amikor a bérlő összevont identitást használt, a felhasználók átlett irányítva az Azure AD bejelentkezési lapról az AD FS-környezetbe. Most, hogy a bérlő úgy van beállítva, hogy az összevont hitelesítés helyett jelszókivonat-szinkronizálást használjon, a felhasználók nem lesznek átirányítva az AD FS-be. Ehelyett a felhasználók jelentkezzen be közvetlenül az Azure AD bejelentkezési oldalon.

Jelszókivonat-szinkronizálás tesztelése:

1. Nyissa meg az Internet Explorert InPrivate módban, hogy a zökkenőmentes egyszeri bejelentkezés ne jelentkezzen be automatikusan.
2. Nyissa meg az Office 365[https://portal.office.com](https://portal.office.com/)bejelentkezési lapját ( ).
3. Írjon be egy felhasználói felhasználófelhasználó-azonosítót, és válassza a **Tovább gombot.** Győződjön meg arról, hogy egy olyan hibrid felhasználó upn-ját adta meg, aki a helyszíni Active Directory-példányból lett szinkronizálva, és aki korábban összevont hitelesítést használt. Megjelenik egy oldal, amelyen megadja a felhasználónevet és a jelszót:

   ![Képernyőkép, amelyen az a bejelentkezési lap látható, amelyen felhasználónevet ad meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Képernyőkép, amelyen az a bejelentkezési lap látható, amelyen jelszót ad meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Miután megadta a jelszót, és a **Bejelentkezés**lehetőséget választja, a rendszer átirányítja az Office 365-portálra.

   ![Képernyőkép az Office 365 portálról](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Teszt zökkenőmentes egyszeri bejelentkezés

1. Jelentkezzen be a vállalati hálózathoz csatlakoztatott tartományhoz csatlakoztatott gépbe.
2. Az Internet Explorer ben vagy a Chrome-ban lépjen az alábbi URL-címek egyikére (cserélje le a "contoso" szót a tartományára):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   A felhasználó röviden átirányítja az Azure AD bejelentkezési lap, amely a "Próbál bejelentkezni." A rendszer nem kér felhasználónevet vagy jelszót a felhasználótól.<br />

   ![Képernyőkép, amely az Azure AD bejelentkezési lapját és üzenetét jeleníti meg](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. A felhasználó átlett irányítva, és sikeresen be van jelentkezve a hozzáférési panelre:

   > [!NOTE]
   > A zökkenőmentes egyszeri bejelentkezés olyan Office 365-szolgáltatásokon működik, amelyek támogatják a tartománytippeket (például myapps.microsoft.com/contoso.com). Jelenleg az Office 365 portál (portal.office.com) nem támogatja a tartománytippeket. A felhasználóknak meg kell adniuk egy upn.Users are required to enter a UPN. Az upn megadása után a zökkenőmentes egyszeri bejelentkezés lekéri a Kerberos-jegyet a felhasználó nevében. A felhasználó jelszó megadása nélkül jelentkezett be.

   > [!TIP]
   > Fontolja meg az Azure AD hibrid csatlakozás üzembe helyezését [a Windows 10-en](https://docs.microsoft.com/azure/active-directory/device-management-introduction) a jobb sso-élmény érdekében.

### <a name="remove-the-relying-party-trust"></a>A függő entitás megbízhatóságának eltávolítása

Miután ellenőrzi, hogy az összes felhasználó és ügyfél sikeresen hitelesítette-e az Azure AD-n keresztül, biztonságosan eltávolíthatja az Office 365 függő entitás megbízhatóságát.

Ha az AD FS-t nem használja más célokra (azaz más függő entitás megbízhatósági kapcsolatokhoz), akkor ezen a ponton biztonságosan leszerelheti az AD FS-t.

### <a name="rollback"></a>Visszagurítás

Ha egy fontos problémát észlel, és nem tudja gyorsan megoldani, dönthet úgy, hogy visszavonja a megoldást az összevonásra.

Tekintse meg az összevonás tervezési és üzembe helyezési dokumentációját a konkrét központi telepítési részletekért. A folyamatnak a következő feladatokat kell magában foglalnia:

* A menedzselt tartományokat a **Convert-MSOLDomainToFederated** parancsmag használatával szövetséges hitelesítéssé alakíthatja.
* Szükség esetén konfiguráljon további jogcímszabályokat.

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName frissítések szinkronizálása

A helyszíni környezetből a szinkronizálási szolgáltatást használó **UserPrincipalName** attribútum korábbi frissítései blokkolva vannak, kivéve, ha mindkét feltétel teljesül:

* A felhasználó egy felügyelt (nem összevont) identitástartományban van.
* A felhasználóhoz nincs licenc rendelve.

A szolgáltatás ellenőrzéséről és bekapcsolásáról a [UserPrincipalName frissítések szinkronizálása című](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)témakörben olvashat.

### <a name="troubleshooting"></a>Hibaelhárítás

A támogatási csapatnak meg kell értenie, hogyan háríthatók el a hitelesítési problémák, amelyek az összevonásról a felügyeltre történő módosítás során vagy után merülnek fel. Az alábbi hibaelhárítási dokumentáció segítségével a támogatási csapat megismerheti a gyakori hibaelhárítási lépéseket és a probléma elkülönítését és megoldását segítő megfelelő műveleteket.

[Az Azure Active Directory jelszókivonat-szinkronizálásának hibaelhárítása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés – problémamegoldása](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>A zökkenőmentes SSO Kerberos visszafejtési kulcs átgörgetése

Fontos, hogy gyakran forgassa át az AZUREADSSOACC számítógépfiók Kerberos-visszafejtési kulcsát (amely az Azure AD-t jelöli). Az AZUREADSSOACC számítógépfiók a helyszíni Active Directory-erdőben jön létre. Javasoljuk, hogy legalább 30 naponta gördülje át a Kerberos-visszafejtési kulcsot, hogy igazodjon az Active Directory tartományi tagjai jelszómódosítások küldésének módjához. Nincs társított eszköz az AZUREADSSOACC számítógépfiók-objektumhoz csatlakoztatva, ezért manuálisan kell végrehajtania a váltást.

Indítsa el a zökkenőmentes Egyszeri bejelentkezés kerberos-visszafejtési kulcs átütemezését az Azure AD Connect szolgáltatást futtató helyszíni kiszolgálón.

További információ: [Hogyan vehetem át az AZUREADSSOACC számítógépfiók Kerberos-visszafejtési kulcsát?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>További lépések

* Ismerje meg az [Azure AD Connect tervezési koncepcióit.](plan-connect-design-concepts.md)
* Válassza ki a [megfelelő hitelesítést](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* További információ a [támogatott topológiákról.](plan-connect-design-concepts.md)
