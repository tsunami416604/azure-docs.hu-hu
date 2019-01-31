---
title: Az Azure Active Directory a helyszíni feltételes hozzáférés beállítása |} A Microsoft Docs
description: Egy lépésenkénti útmutató, amellyel a helyi alkalmazásokhoz való feltételes hozzáférés engedélyezése a Windows Server 2012 R2 Active Directory összevonási szolgáltatások (AD FS) használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.subservice: devices
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 00921fd82463a75e81e1279d436a576461e02f78
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301645"
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>A helyszíni feltételes hozzáférés beállítása az Azure Active Directory eszközregisztrációjával

Ha munkahelyi-csatlakozás a felhasználók saját eszközét, az Azure Active Directory (Azure AD) eszközregisztrációs szolgáltatás, eszközeiket is lehet megjelölve ismert, hogy a szervezet. Következő egy lépésenkénti útmutató a helyszíni alkalmazások feltételes hozzáférése engedélyezve van a Windows Server 2012 R2 Active Directory összevonási szolgáltatások (AD FS) használatával.

> [!NOTE]
> Egy Office 365-licenc vagy a prémium szintű Azure AD-licenc szükség, az Azure Active Directory eszköz regisztrációs szolgáltatás feltételes hozzáférési szabályzatok regisztrált eszközök használata esetén. Ezek közé tartozik a házirendekben, amelyek a helyszíni erőforrások AD FS-sel tartat be.
> 
> Feltételes hozzáférés a helyszíni erőforrások forgatókönyvekkel kapcsolatos további információkért lásd: [csatlakozás munkahelyhez bármilyen eszközről egyszeri Bejelentkezéssel és zavartalan második tényezős hitelesítés különböző vállalati alkalmazások](https://technet.microsoft.com/library/dn280945.aspx).

Ezek a képességek érhetők el, akiknek az Azure Active Directory Premium-licenc vásárlása.

## <a name="supported-devices"></a>Támogatott eszközök

* Windows 7-tartományhoz csatlakozott eszközökkel
* Windows 8.1 személyes és a tartományhoz csatlakoztatott eszközök
* iOS 6 és újabb verziók esetében a Safari böngésző
* Android 4.0-s vagy újabb, Samsung GS3 vagy újabb telefonokon, Samsung Galaxy megjegyzés 2 vagy újabb rendszerű táblagépek

## <a name="scenario-prerequisites"></a>A forgatókönyv előfeltételei

* Az Office 365-előfizetés vagy a prémium szintű Azure Active Directory
* Egy Azure Active Directory-bérlő
* A Windows Server Active Directory (Windows Server 2008 vagy újabb)
* A Windows Server 2012 R2 frissített séma
* Az Azure Active Directory Premium licenc
* A Windows Server 2012 R2 összevonási szolgáltatások, az Azure AD egyszeri bejelentkezés konfigurálva
* Windows Server 2012 R2 Web Application Proxy 
* A Microsoft Azure Active Directory Connect (Azure AD Connect) [(Azure AD Connect letöltése)](https://www.microsoft.com/download/details.aspx?id=47594)
* Ellenőrzött tartomány

## <a name="known-issues-in-this-release"></a>Ebben a kiadásban az ismert problémák

* Eszközalapú feltételes hozzáférési szabályzatok eszközvisszaírás objektumot az Active Directory szükséges az Azure Active Directoryból. Eszköz visszaírása az Active Directory-objektumok esetében akár három órát is igénybe vehet.
* iOS 7-es eszközökön mindig kéri a felhasználót válasszon ki egy ügyféltanúsítvány-alapú hitelesítés során.
* IOS 8-at, mielőtt iOS 8.3 nem működnek az egyes verziói.

## <a name="scenario-assumptions"></a>A forgatókönyv feltételezések

Ez a forgatókönyv azt feltételezi, hogy az Azure AD-bérlő és a egy helyszíni Active Directory hibrid környezetben. Ezek a bérlők kell csatlakoztatni egy ellenőrzött tartomány, az Azure AD Connect és az AD FS-sel egyszeri bejelentkezéshez. A következő ellenőrzőlista segítségével konfigurálja a környezetet a követelményeinek megfelelően.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Ellenőrzőlista: Feltételes hozzáférési forgatókönyv előfeltételei

Az Azure AD-bérlő összekapcsolása a helyszíni Active Directory-példányból.

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory eszközregisztrációs szolgáltatás konfigurálása

Ez az útmutató segítségével üzembe helyezése és konfigurálása az Azure Active Directory eszközregisztrációs szolgáltatás a szervezet számára.

Ez az útmutató feltételezi, hogy konfigurálta a Windows Server Active Directory és a Microsoft Azure Active Directory feliratkozott. Tekintse meg a korábban ismertetett előfeltételeknek.

Az Azure Active Directory eszközregisztrációs szolgáltatás az Azure Active Directory-bérlőben üzembe helyezéséhez az alábbi ellenőrzőlista sorrendben feladatok végrehajtása. Ha egy hivatkozás egy elméleti témához, térjen vissza ehhez a listához ezt követően, így folytathatja a hátralévő műveletekkel. Bizonyos feladatokat, amelyek segítségével győződjön meg arról, hogy a lépés sikeresen befejeződött-e forgatókönyv ellenőrzési lépés tartalmazza.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>1. rész: Az Azure Active Directory eszközregisztráció engedélyezése

Kövesse az ellenőrzőlista engedélyezése és konfigurálása az Azure Active Directory eszközregisztrációs szolgáltatás.

| Tevékenység | Leírások | 
| --- | --- |
| Eszközregisztráció az Azure Active Directory-bérlőben, az eszközök a munkahelyi csatlakozás engedélyezése. Alapértelmezés szerint az Azure multi-factor Authentication hitelesítés nincs engedélyezve a szolgáltatáshoz. Azt javasoljuk azonban, amikor regisztrál egy eszközt multi-factor Authentication szolgáltatás használata. Az Active Directory eszközregisztrációs szolgáltatás engedélyezése a multi-factor Authentication, előtt győződjön meg arról, hogy az AD FS konfigurálása a multi-factor Authentication-szolgáltatót. |[Az Azure Active Directory eszközregisztráció engedélyezése](active-directory-device-registration-get-started.md)| 
|Eszközök az Azure Active Directory eszközregisztrációs szolgáltatás felderítéséhez a jól ismert DNS-rekordok megkeresésével. A vállalati DNS konfigurálása, úgy, hogy az eszközök az Azure Active Directory eszközregisztrációs szolgáltatás képes felderíteni. |[Azure Active Directory eszközregisztráció felderítésének konfigurálása](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2. rész: Központi telepítése és konfigurálása a Windows Server 2012 R2 Active Directory összevonási szolgáltatások és az Azure AD összevonási kapcsolat

| Tevékenység | Leírások |
| --- | --- |
| Üzembe helyezés az Active Directory tartományi szolgáltatások a Windows Server 2012 R2 sémakiterjesztések. Nem kell minden tartományvezérlő Windows Server 2012 R2 frissítése. A séma frissítése egyetlen követelménye. |[Az Active Directory tartományi szolgáltatások sémájának frissítése](#upgrade-your-active-directory-domain-services-schema) |
| Eszközök az Azure Active Directory eszközregisztrációs szolgáltatás felderítéséhez a jól ismert DNS-rekordok megkeresésével. A vállalati DNS konfigurálása, úgy, hogy az eszközök az Azure Active Directory eszközregisztrációs szolgáltatás képes felderíteni. |[Az Active Directory támogatási eszközök előkészítése](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3. rész: Eszközvisszaírás engedélyezése az Azure ad-ben

| Tevékenység | Leírások |
| --- | --- |
| Végezze el a második részében "Eszközvisszaírás engedélyezése az Azure AD Connectben." Miután végzett, térjen vissza az útmutatóban. |[Eszközvisszaírás engedélyezése az Azure AD Connectben](hybrid/how-to-connect-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcionális] 4. lépés: Enable Multi-Factor Authentication

Erősen ajánlott úgy beállítani, hogy a multi-factor Authentication a különböző lehetőségek közül. Ha azt szeretné, a többtényezős hitelesítés megkövetelése, [válassza ki a multi-factor Authentication szolgáltatás biztonsági megoldást,](authentication/concept-mfa-whichversion.md). Az egyes megoldások és a hivatkozások segítségével konfigurálja a megoldást a választott leírását tartalmazza.

## <a name="part-5-verification"></a>5. lépés: Ellenőrzés

Az üzembe helyezés befejeződött, és néhány esetben kipróbálhatja. Az alábbi hivatkozások segítségével kísérletezhet a szolgáltatás és a hozzá tartozó szolgáltatások megismerése.

| Tevékenység | Leírások |
| --- | --- |
| Egyes eszközök a munkahelyi csatlakozás az Azure Active Directory eszközregisztrációs szolgáltatás használatával. Csatlakozhat az iOS, Windows és Android-eszközökön. |[Eszközök csatlakoztatása a munkahelyen, az Azure Active Directory eszközregisztrációs szolgáltatással](#join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service) |
| Megtekintheti és engedélyezése vagy letiltása a regisztrált eszközök a felügyeleti portál használatával. Ebben a feladatban az egyes regisztrált eszközöket a felügyeleti portál használatával megtekintése. |[Az Azure Active Directory eszközregisztrációs szolgáltatás áttekintése](active-directory-device-registration-get-started.md) |
| Győződjön meg arról, hogy eszköz objektumokat a rendszer visszaírja az Azure Active Directoryból a Windows Server Active Directory. |[Ellenőrizze a regisztrált eszközöket a rendszer visszaírja az Active Directoryhoz](#verify-that-registered-devices-are-written-back-to-active-directory) |
| Most, hogy a felhasználók regisztrálhatják eszközeiket, alkalmazást hozhat létre hozzáférési szabályzatok az AD FS, amelyek csak regisztrált eszközök engedélyezésére. Ebben a feladatban egy alkalmazás-hozzáférési szabály és a egy egyéni hozzáférés-megtagadási üzenetet hoz létre. |[Alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenet létrehozása](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Azure Active Directory integrálása a helyszíni Active Directoryval

**Lásd:**

- [A helyszíni címtárak integrálása az Azure Active Directory](hybrid/whatis-hybrid-identity.md) – elméleti információk áttekintéséhez.

- [Az Azure AD Connect egyéni telepítési](hybrid/how-to-connect-install-custom.md) – a telepítési utasításokat.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Az Active Directory tartományi szolgáltatások sémájának frissítése

> [!NOTE]
> Miután frissítette az Active Directory-sémát, a folyamat nem vonható vissza. Azt javasoljuk, hogy először a verziófrissítés tesztkörnyezetben.
> 

1. Jelentkezzen be a tartományvezérlőre egy olyan fiókkal, amely a vállalati rendszergazdák és a séma-rendszergazdai jogosultságokkal rendelkezik.
1. Másolás a **[adathordozó] \support\adprep** directory és a egy, az Active Directory-tartományvezérlők alkönyvtárak (ahol **[adathordozó]** elérési útja, a Windows Server 2012 R2 telepítési adathordozó).
1. A parancssort. Ehhez nyissa meg a **adprep** könyvtárhoz, majd futtassa **adprep.exe/forestprep**. Kövesse a képernyőn megjelenő utasításokat a sémafrissítés végrehajtásához.

## <a name="prepare-your-active-directory-to-support-devices"></a>Eszközök támogatásához az Active Directory előkészítése

> [!NOTE]
> Ez a műveletet egyszer kell készíti elő az eszközök támogatásához az Active Directory-erdőben kell futtatnia. Ez az eljárás befejezéséhez be kell jelentkeznie vállalati rendszergazdai jogosultságokkal, és az Active Directory-erdőben kell rendelkeznie a Windows Server 2012 R2 séma.
> 


### <a name="prepare-your-active-directory-forest"></a>Az Active Directory-erdő előkészítése

1. Az összevonási kiszolgálón, nyisson meg egy Windows PowerShell-parancsablakot, és írja be **Initialize-ADDeviceRegistration**. 
1. Amikor a rendszer **ServiceAccountName**, adja meg az AD FS szolgáltatásfiókként megadott szolgáltatásfiók nevét. Ha csoportosan felügyelt szolgáltatásfiókok, adja meg a fiókot a **domain\accountname$** formátumban. Egy olyan tartományi fiók, használja a következő formátumot **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Engedélyezze az eszközhitelesítést az AD FS-ben

1. Az összevonási kiszolgálón nyissa meg az AD FS felügyeleti konzolon, és lépjen **az AD FS** > **hitelesítési házirendek**.
1. Az a **műveletek** ablaktáblán válassza előbb **általános elsődleges hitelesítés szerkesztése**.
1. Ellenőrizze **hitelesítés engedélyezése az eszköz**, majd válassza ki **OK**.
1. Alapértelmezés szerint az AD FS rendszeres időközönként eltávolítja a fel nem használt eszközök az Active Directoryból. Tiltsa le ezt a feladatot, amikor az Azure Active Directory eszközregisztrációs szolgáltatás használja, hogy az eszközök kezelhetők az Azure-ban.

### <a name="disable-unused-device-cleanup"></a>Tiltsa le a nem használt eszköz karbantartása

Az összevonási kiszolgálón, nyisson meg egy Windows PowerShell-parancsablakot, és írja be **Set-AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Az Azure AD Connect eszközvisszaírás előkészítése

Végezze el az 1. rész: Készítse elő az Azure AD Connect.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Eszközök csatlakoztatása a munkahelyi Azure Active Directory eszközregisztrációs szolgáltatás használatával

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>IOS-eszközök csatlakoznak az Azure Active Directory eszközregisztrációjával

Az Azure Active Directory eszközregisztráció az iOS-eszközök a sugárzási profil regisztrációs folyamatot használ. Ez a folyamat akkor kezdődik, amikor a felhasználó csatlakozik a profil regisztrációs URL-CÍMÉT a Safari böngésző. Az URL-cím formátuma a következő:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/<yourdomainname>`

Ebben az esetben `yourdomainname` az Azure Active Directoryval konfigurált tartománynév. Ha például a tartománynév a contoso.com, az URL-cím értéke:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com`

Nincsenek számos különböző módon való kommunikációhoz az URL-címet a felhasználókhoz. Javasoljuk, hogy egy metódus például az AD FS egyéni alkalmazás hozzáférés-megtagadási üzenet van közzététele az URL-címet. Ezt az információt a következő szakasz foglalkozik [hozzon létre egy alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenetet](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Windows 8.1-eszköz csatlakoztatása az Azure Active Directory eszközregisztrációjával

1. Válassza ki a Windows 8.1-eszköz **gépház** > **hálózati** > **munkahelyi**.
1. Egyszerű felhasználónév formátumú; adja meg a felhasználónevet Ha például **dan@contoso.com**.
1. Válassza ki **csatlakozzon**.
1. Amikor a rendszer kéri, jelentkezzen be hitelesítő adataival. Az eszköz most már csatlakozott.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Windows 7 eszköz csatlakoztatása az Azure Active Directory eszközregisztrációjával

Windows 7-tartományhoz csatlakoztatott eszközök regisztrálását, telepíteni kell a [eszközregisztrációs szoftvercsomagot](https://www.microsoft.com/download/details.aspx?id=53554).

A csomag használatával kapcsolatos utasításokért lásd: [Windows Installer-csomagokat a Windows 10-számítógépeket](devices/hybrid-azuread-join-control.md#control-windows-down-level-devices).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Győződjön meg arról, hogy regisztrált eszközöket a rendszer visszaírja az Active Directoryhoz

Megtekintheti, és győződjön meg arról, hogy az eszköz objektumok alkalmazáskonfigurációjának vissza az Active Directory LDP.exe vagy ADSI-szerkesztő használatával. Mindkét érhetők el az Active Directory rendszergazdai eszközök.

Alapértelmezés szerint, hogy a rendszer visszaírja az Azure Active Directoryból eszközobjektumok az AD FS farm azonos tartományban kerülnek.

`CN=RegisteredDevices,defaultNamingContext`

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenet létrehozása

Vegye figyelembe az alábbi forgatókönyvet: Hozzon létre egy alkalmazást Függőentitás-megbízhatóság szerint az AD FS-ben, és a egy kiadási engedélyezési szabályt, amely lehetővé teszi, hogy csak a regisztrált készülékek konfigurálása. Most már csak a regisztrált eszközök engedélyezett az alkalmazás eléréséhez. 

Megkönnyítik a felhasználók az alkalmazás eléréséhez, hozzáférés-Megtagadás egy egyéni üzenet csatlakoztassák az eszközüket vonatkozó utasításokat tartalmazó konfigurálnia. A felhasználók már zavartalanul regisztrálják az eszközeiket, így az alkalmazás eléréséhez.

A következő lépések bemutatják, hogyan valósíthat meg ebben a forgatókönyvben.

> [!NOTE]
> Ez a szakasz azt feltételezi, hogy már konfigurált egy függő entitás megbízhatóságának az alkalmazás az AD FS-ben.
> 

1. Nyissa meg az AD FS MMC eszközt, és válassza ki **az AD FS** > **megbízhatósági kapcsolatok** > **függő entitás Megbízhatóságai**.
1. Keresse meg az alkalmazást, amelyhez az új hozzáférési szabály vonatkozik. Kattintson a jobb gombbal az alkalmazást, és válassza **Jogcímszabályok szerkesztése**.
1. Válassza ki a **Kiállításengedélyezési szabályok** lapra, és válassza ki **szabály hozzáadása**.
1. Az a **jogcímszabály** sablon legördülő listában válassza **engedélyezése vagy elutasítása felhasználók alapján egy bejövő jogcím**. Ezután kattintson a **Tovább** gombra.
1. Az a **Jogcímszabály neve** mezőbe írja be a **engedélyezze a hozzáférést a regisztrált eszközökről**.
1. Az a **bejövő jogcím típusa** legördülő listában válassza **van regisztrálva felhasználó**.
1. Az a **bejövő jogcím értéke** mezőbe írja be a **igaz**.
1. Válassza ki a **ezzel a bejövő jogcímmel rendelkező felhasználók elérésének** választógombot.
1. Válassza ki **Befejezés**, majd válassza ki **alkalmaz**.
1. Távolítsa el minden olyan szabályok, amelyek megengedőbb, mint a létrehozott szabály. Például, távolítsa el az alapértelmezett szabály **hozzáférés engedélyezés minden felhasználónak**.

Az alkalmazás konfigurálva van az hozzáférés csak akkor engedélyezi a felhasználó regisztrált, és a a munkahelyhez csatlakoztatott eszközről érkezik. Speciális hozzáférési szabályzatok, lásd: [további többtényezős hitelesítéssel érzékeny alkalmazások Kockázatfelügyelete](https://technet.microsoft.com/library/dn280949.aspx).

Ezután konfigurálja a egyéni hibaüzenet az alkalmazáshoz. A hibaüzenet lehetővé teszi, hogy a felhasználókat, hogy csatlakoznia kell az eszközt a munkahelyhez, mielőtt hozzáférhetne az alkalmazás. Egyéni HTML és a PowerShell használatával létrehozhat egy egyéni alkalmazás-hozzáférés-megtagadási üzenetet.

Az összevonási kiszolgálón nyisson meg egy PowerShell parancsablakot, és írja be a következő parancsot. Cserélje le a parancs részeit elemek, amelyek az adott rendszerhez:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage`

Ez az alkalmazás eléréséhez regisztrálnia kell az eszközt.

**Ha egy iOS-eszközt használ, válassza ezt a hivatkozást az eszköz csatlakoztatása az**:

`https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com`

Az iOS-eszköz csatlakoztatása munkahelyhez.

Ha a Windows 8.1-eszközt használ, az eszköz csatlakozhat kiválasztásával **gépház**> **hálózati** > **munkahelyi**.

Az előző parancsokban **függő entitás megbízhatósági neve** neve, az alkalmazás függő entitás Megbízhatóságaként objektumot az AD FS-ben.
És **sajattartomany.com** az Azure Active Directoryval (például contoso.com) konfigurált tartománynév.
Ügyeljen arra, hogy távolítsa el minden sortörést (ha van ilyen) a HTML-tartalmakat, amelyeket átad a **Set-AdfsRelyingPartyWebContent** parancsmagot.

Most már felhasználók érhetik el az alkalmazást, amely nincs regisztrálva az Azure Active Directory eszközregisztrációs szolgáltatás az eszközről, ha azok hibaüzenetet.
