---
title: A helyszíni feltételes hozzáférés az Azure Active Directoryban |} Microsoft Docs
description: Egy részletes útmutató a helyszíni alkalmazások feltételes hozzáférést tesz lehetővé a Windows Server 2012 R2 Active Directory összevonási szolgáltatások (AD FS) használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 6ae9df8b-31fe-4d72-9181-cf50cfebbf05
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.custom: seohack1
ms.openlocfilehash: 0ce4497a8bebf9078363509c1f962728ab4189f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="setting-up-on-premises-conditional-access-by-using-azure-active-directory-device-registration"></a>A helyszíni feltételes hozzáférés beállítása az Azure Active Directory eszközregisztrációs használatával
Ha munkahelyi-csatlakozás a felhasználók saját eszközét, az Azure Active Directory (Azure AD) eszközregisztrációs szolgáltatását, az eszközeik jelölhető, amelyről ismert, hogy a szervezet. Az alábbiakban részletesen ismerteti az Active Directory összevonási szolgáltatások (AD FS) a Windows Server 2012 R2 használatával a helyszíni alkalmazások feltételes hozzáférést tesz lehetővé.

> [!NOTE]
> Az Office 365 licenc- vagy Azure AD Premium licenccel kell adni, ha az Azure Active Directory eszköz regisztrációs szolgáltatás feltételes hozzáférési házirendek a regisztrált eszközök használatával. Ezek közé tartozik a házirendekben, amelyek az AD FS-ben a helyszíni erőforrások érvényesíti.
> 
> Feltételes hozzáférés a helyszíni erőforrások forgatókönyvekkel kapcsolatos további információkért lásd: [csatlakozás a munkahelyhez bármilyen eszközről egyszeri Bejelentkezéssel és zavartalan második tényezős hitelesítéssel a különböző vállalati alkalmazások](https://technet.microsoft.com/library/dn280945.aspx).

Ezek a képességek érhetők el, az ügyfél számára, akik az Azure Active Directory Premium-licenc vásárlása.

## <a name="supported-devices"></a>Támogatott eszközök
* Windows 7-tartományhoz csatlakozó eszközök
* Windows 8.1 személyes és a tartományhoz csatlakozó eszközök
* iOS 6 és újabb verziók esetében a Safari böngésző
* Android 4.0-s vagy újabb verzió, Samsung GS3 vagy későbbi telefonok, Samsung Galaxy megjegyzés 2 vagy újabb rendszerű táblagépek

## <a name="scenario-prerequisites"></a>A forgatókönyv előfeltételei
* Az Office 365-előfizetéssel vagy a prémium szintű Azure Active Directory
* Az Azure Active Directory-bérlő
* Windows Server Active Directory (Windows Server 2008 vagy újabb)
* A Windows Server 2012 R2 frissített séma
* Az Azure Active Directory Premium licenc
* Windows Server 2012 R2 összevonási szolgáltatások, az egyszeri bejelentkezés az Azure AD
* Windows Server 2012 R2 webalkalmazás-Proxy 
* A Microsoft Azure Active Directory Connect (Azure AD Connect) [(Azure AD Connect letöltése)](http://www.microsoft.com/en-us/download/details.aspx?id=47594)
* Ellenőrzött tartomány

## <a name="known-issues-in-this-release"></a>Ebben a kiadásban ismert problémák
* Eszközalapú feltételes hozzáférési házirendek az Active Directory objektum eszközvisszaíró az Azure Active Directory szükséges. Az eszközobjektumok az Active Directory visszaírása akár három órába is telhet.
* iOS 7-es eszközökön mindig megkérdezi a felhasználót a felhasználói tanúsítvány kiválasztása az ügyféltanúsítvány-alapú hitelesítés során.
* IOS 8, iOS 8.3 nem működnek, mielőtt az egyes verziói.

## <a name="scenario-assumptions"></a>A forgatókönyv Előfeltételek
Ez a forgatókönyv feltételezi, hogy rendelkezik-e az Azure AD-bérlő és a helyszíni Active Directory hibrid környezetben. Ezek a bérlők kell csatlakoztatni az Azure AD Connect egy ellenőrzött tartomány és az AD FS az egyszeri bejelentkezés. A következő ellenőrzőlista segítségével konfigurálja a környezetet a követelményeknek megfelelően.

## <a name="checklist-prerequisites-for-conditional-access-scenario"></a>Ellenőrzőlista: A feltételes hozzáférési forgatókönyv előfeltételei
Az Azure AD-bérlő kapcsolódnak a helyszíni Active Directory-példányban.

## <a name="configure-azure-active-directory-device-registration-service"></a>Azure Active Directory eszközregisztrációs szolgáltatás konfigurálása
Ez az útmutató segítségével telepítheti és konfigurálhatja az Azure Active Directory eszközregisztrációs szolgáltatás a szervezet számára.

Ez az útmutató azt feltételezi, hogy a Windows Server Active Directory konfigurált, és a Microsoft Azure Active Directory előfizetett. Tekintse meg a korábban ismertetett előfeltételeknek.

Az Azure Active Directory eszközregisztrációs szolgáltatás és az Azure Active Directory-bérlő központi telepítéséhez hajtsa végre az alábbi ellenőrzőlista sorrendben. Ha egy hivatkozás egy elméleti témához kerül, térjen vissza ehhez az ellenőrzőlistához ezt követően, úgy, hogy a hátralévő műveletekkel folytatja. Egyes feladatok közé tartozik a forgatókönyv ellenőrzési lépés, amelyek segítségével győződjön meg arról, hogy a lépés sikeresen befejeződött.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>1. lépés: Engedélyezze Azure Active Directory eszközregisztráció
Kövesse az ellenőrzőlista engedélyezése és konfigurálása az Azure Active Directory eszközregisztrációs szolgáltatást.

| Tevékenység | Leírások | 
| --- | --- |
| Eszközök regisztrációjához az eszközök a munkahelyi csatlakoztatás az Azure Active Directory-bérlőben. Alapértelmezés szerint a Azure multi-factor Authentication szolgáltatás nincs engedélyezve. Azt javasoljuk azonban, hogy többtényezős hitelesítés használata, amikor regisztrál egy eszközt. Ahhoz, hogy a többtényezős hitelesítés az Active Directory eszközregisztrációs szolgáltatás, ügyeljen arra, hogy az AD FS többtényezős hitelesítési szolgáltató. |[Azure Active Directory eszközregisztrációs engedélyezése](active-directory-device-registration-get-started.md)| 
|Eszközök az Azure Active Directory eszközregisztrációs szolgáltatás felderítéséhez a jól ismert DNS-rekordok megkeresésével. A vállalat DNS-ÉT, hogy az eszköz képes felderíteni az Azure Active Directory eszközregisztrációs szolgáltatás konfigurálása |[Azure Active Directory-eszköz regisztrációs felderítés konfigurálása](active-directory-device-registration-get-started.md)| 


## <a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>2. lépés: Telepítse és konfigurálja a Windows Server 2012 R2 Active Directory összevonási szolgáltatások, és állítsa be az Azure AD összevonási kapcsolat

| Tevékenység | Leírások |
| --- | --- |
| Active Directory tartományi szolgáltatások telepítése a Windows Server 2012 R2 séma kiterjesztésű fájl. Nem szükséges frissítenie minden tartományvezérlő Windows Server 2012 R2 rendszerre. A séma frissítése nem egyetlen követelménye. |[Az Active Directory tartományi szolgáltatások séma frissítése](#upgrade-your-active-directory-domain-services-schema) |
| Eszközök az Azure Active Directory eszközregisztrációs szolgáltatás felderítéséhez a jól ismert DNS-rekordok megkeresésével. A vállalat DNS-ÉT, hogy az eszköz képes felderíteni az Azure Active Directory eszközregisztrációs szolgáltatás konfigurálása |[Az Active Directory támogatási eszközök előkészítése](#prepare-your-active-directory-to-support-devices) |

## <a name="part-3-enable-device-writeback-in-azure-ad"></a>3. lépés: Engedélyezze az Azure AD eszközvisszaíró
| Tevékenység | Leírások |
| --- | --- |
| Fejezze be a második rész a "Eszközvisszaírás engedélyezése az Azure AD Connectben." Amikor végzett, térjen vissza az útmutató. |[Eszközvisszaírás engedélyezése az Azure AD Connectben](./connect/active-directory-aadconnect-feature-device-writeback.md) |

## <a name="optional-part-4-enable-multi-factor-authentication"></a>[Választható] 4. lépés: Az többtényezős hitelesítés engedélyezése
Határozottan javasoljuk, hogy a multi-factor Authentication több lehetőség közül választhat. Ha azt szeretné, hogy többtényezős hitelesítést, lásd: [válassza ki a multi-factor Authentication biztonsági megoldást,](authentication/concept-mfa-whichversion.md). Ez magában foglalja az egyes megoldások, és hivatkozások segítségével konfigurálja a megoldást az Ön által választott leírását.

## <a name="part-5-verification"></a>5. rész: ellenőrzése
A telepítés befejeződött, és bizonyos esetekben tekinthetők meg. Az alábbi hivatkozások segítségével a szolgáltatás kísérletezhet, és ismerkedjen meg a szolgáltatásai.

| Tevékenység | Leírások |
| --- | --- |
| Egyes eszközöket csatlakoztat a munkahelyhez Azure Active Directory eszközregisztrációs szolgáltatás használatával. IOS, Windows és Android-eszköz csatlakozhat. |[Eszközök regisztrálása a munkahelyi Azure Active Directory eszközregisztrációs szolgáltatással](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Megtekintheti és engedélyezése vagy letiltása a regisztrált eszközöket a felügyeleti portál használatával. Ebben a feladatban néhány regisztrált eszközöket a felügyeleti portál használatával megtekintése. |[Az Azure Active Directory eszközregisztrációs szolgáltatás áttekintése](active-directory-device-registration-get-started.md) |
| Győződjön meg arról, hogy eszközobjektumok kerüljenek vissza az Azure Active Directory Windows Server Active Directory. |[Ellenőrizze, hogy a regisztrált eszközök vissza kerülnek az Active Directory](#verify-registered-devices-are-written-back-to-active-directory) |
| Most, hogy a felhasználók regisztrálhatják az eszközeiket, létrehozhat az alkalmazás hozzáférési szabályzatok az AD FS, amelyek csak regisztrált eszközök engedélyezésére. Ez a feladat létrehozza egy alkalmazás-hozzáférési szabályt és egy egyéni hozzáférés-megtagadási üzenetet. |[Alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenet létrehozása](#create-an-application-access-policy-and-custom-access-denied-message) |

## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Az Azure Active Directory integrálása a helyszíni Active Directoryban

**Lásd:**

- [A helyszíni címtárak integrálása az Azure Active Directory](./connect/active-directory-aadconnect.md) – általános információk áttekintéséhez.

- [Az Azure AD Connect egyéni telepítési](./connect/active-directory-aadconnect-get-started-custom.md) – a telepítési utasításokat.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Az Active Directory tartományi szolgáltatások séma frissítése
> [!NOTE]
> Miután frissítette az Active Directory-séma, a folyamat nem állítható vissza korábbi állapotba. Azt javasoljuk, hogy először frissíti a tesztkörnyezetben.
> 

1. Jelentkezzen be a tartományvezérlőre egy olyan fiókkal, amely a vállalati rendszergazdák és a séma-rendszergazdai jogosultságokkal rendelkezik.
2. Másolás a **[adathordozó] \support\adprep** directory és az Active Directory-tartományvezérlők egyikére alkönyvtárak (ahol **[adathordozó]** elérési útját a Windows Server 2012 R2 telepítési adathordozó).
4. A parancssorból, navigáljon a **adprep** könyvtárhoz, és futtassa **adprep.exe/forestprep**. Kövesse a képernyőn megjelenő utasításokat követve végezze el a séma frissítést.

## <a name="prepare-your-active-directory-to-support-devices"></a>Eszközök támogatásához az Active Directory előkészítése
> [!NOTE]
> Ez az egy egyszeri művelet készíti elő az Active Directory-erdőben lévő eszközök támogatására kell futtatnia. A művelet elvégzéséhez be kell jelentkeznie be vállalati rendszergazdai jogosultságokkal, és az Active Directory-erdőben kell rendelkeznie a Windows Server 2012 R2 séma.
> 


### <a name="prepare-your-active-directory-forest"></a>Az Active Directory-erdő előkészítése
1. Az összevonási kiszolgálón nyisson meg egy Windows PowerShell parancsablakot, és írja be **Initialize-ADDeviceRegistration**. 
2. Ha a rendszer kéri **ServiceAccountName**, adja meg a az AD FS szolgáltatása szolgáltatásfiókként kiválasztott fiók nevét. Ha csoportosan felügyelt szolgáltatásfiókok, adja meg a fiókot a **domain\accountname$** formátumban. Egy olyan tartományi fiók, a következő formátumot kell követnie **domain\accountname**.

### <a name="enable-device-authentication-in-ad-fs"></a>Az AD FS eszközhitelesítés engedélyezése
1. Az összevonási kiszolgálón nyissa meg az AD FS felügyeleti konzolon, és navigáljon a **AD FS** > **hitelesítési házirendek**.
2. Az a **műveletek** ablaktáblán válassza előbb **általános elsődleges hitelesítés szerkesztése**.
3. Ellenőrizze **eszközhitelesítés engedélyezése**, majd válassza ki **OK**.
4. Alapértelmezés szerint az AD FS rendszeres időközönként eltávolítja nem használt eszközöket az Active Directoryból. Tiltsa le ezt a feladatot, amikor Azure Active Directory eszközregisztrációs szolgáltatás használja, hogy az eszközöket kezelheti az Azure-ban.

### <a name="disable-unused-device-cleanup"></a>Tiltsa le a nem használt eszköz karbantartása
Az összevonási kiszolgálón nyisson meg egy Windows PowerShell parancsablakot, és írja be **Set-AdfsDeviceRegistration - MaximumInactiveDays 0**.

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Az Azure AD Connect Felkészülés az eszközök visszaírásához.
Fejezze be az 1. lépés: az Azure AD Connect előkészítése.

## <a name="join-devices-to-your-workplace-by-using-azure-active-directory-device-registration-service"></a>Eszközök regisztrálása a munkahelyi Azure Active Directory eszközregisztrációs szolgáltatás használatával

### <a name="join-an-ios-device-by-using-azure-active-directory-device-registration"></a>Csatlakozás az iOS-eszközök Azure Active Directory eszközregisztrációs segítségével
Azure Active Directory eszközregisztráció az iOS-eszközök a sugárzási profil regisztrációs folyamatot használ. Ez a folyamat akkor kezdődik, amikor a felhasználó kapcsolódik a profil regisztrációs URL-CÍMÉT a Safari böngésző. Az URL-cím formátuma a következő:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Ebben az esetben `yourdomainname` az Azure Active Directoryval konfigurált tartománynév. Például ha a tartománynév contoso.com, az URL-cím a következőképpen történik:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Való kommunikációhoz az URL-cím, a felhasználók számos különböző módja van. Például egy ajánlott módszer közzéteszi az URL-cím az AD FS egyéni hozzáférés-megtagadási üzenet található. Ezt az információt a következő szakasz tárgyalja [hozzon létre egy alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenet](#create-an-application-access-policy-and-custom-access-denied-message).

### <a name="join-a-windows-81-device-by-using-azure-active-directory-device-registration"></a>Azure Active Directory eszközregisztrációs segítségével össze a Windows 8.1 eszköz
1. Válassza ki a Windows 8.1-eszközön **gépház** > **hálózati** > **munkahelyi**.
2. Adja meg a felhasználónevét UPN formátumú; például **dan@contoso.com**.
3. Válassza ki **csatlakozás**.
4. Amikor a rendszer kéri, jelentkezzen be a fiókjával. Az eszköz már csatlakoztatva van.

### <a name="join-a-windows-7-device-by-using-azure-active-directory-device-registration"></a>Csatlakozás egy Windows 7 eszköz segítségével az Azure Active Directory eszközregisztrációs
Windows 7-tartományhoz csatlakozó eszközök regisztrálásához kell telepítenie a [eszközregisztrációs szoftvercsomagot](https://www.microsoft.com/download/details.aspx?id=53554).

A csomag használatával kapcsolatos útmutatásért lásd: [Windows Installer-csomag nem - Windows 10-es számítógépeken](device-management-hybrid-azuread-joined-devices-setup.md#windows-installer-packages-for-non-windows-10-computers).

## <a name="verify-that-registered-devices-are-written-back-to-active-directory"></a>Győződjön meg arról, hogy a regisztrált eszközök vissza kerüljenek-e az Active Directory
Megtekintheti, és győződjön meg arról, hogy az eszköz objektumok rendelkeznek írt vissza az Active Directory LDP.exe eszközzel vagy az ADSI-szerkesztő. Mindkettő az Active Directory rendszergazdai eszközök érhető el.

Alapértelmezés szerint az Azure Active Directory biztonsági írt eszközobjektumok az AD FS farm megegyező tartományban kerülnek.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Alkalmazás-hozzáférési házirend és egyéni hozzáférés-megtagadási üzenet létrehozása
A következő példa: egy Függőentitás-megbízhatóság az AD FS-alkalmazás létrehozása és konfigurálása egy kiadási engedélyezési szabályt, amely lehetővé teszi, hogy csak a regisztrált készülékek. Most már csak a regisztrált eszközök hozzáférése engedélyezve legyen az alkalmazás. 

Hogy megkönnyítse a felhasználók számára az alkalmazás eléréséhez, konfigurálnia utasításokat tartalmaz ahhoz, hogyan lehet az eszköz egyéni, a hozzáférés-megtagadási üzenetet. A felhasználók most már rendelkezik a zökkenőmentes úgy, hogy regisztrálják az eszközeiket, így az alkalmazás eléréséhez.

A következő lépések bemutatják a forgatókönyv végrehajtásához.

> [!NOTE]
> Jelen szakaszban feltételezzük, hogy már konfigurálta a függő entitás megbízhatóságának az AD FS-ben az alkalmazáshoz.
> 

1. Az AD FS MMC eszköz megnyitásához, majd válassza ki **AD FS** > **megbízhatósági kapcsolatok** > **függő entitás Megbízhatóságai**.
2. Keresse meg az alkalmazás, amely az új hozzáférési szabály vonatkozik. Kattintson a jobb gombbal az alkalmazást, majd válassza ki **Jogcímszabályok szerkesztése**.
3. Válassza ki a **Kiállításengedélyezési szabályok** lapra, majd válassza ki **szabály hozzáadása**.
4. Az a **jogcímszabály** sablon legördülő listában válassza **engedélyezése vagy tiltása felhasználók egy bejövő jogcím alapján**. Ezután kattintson a **Tovább** gombra.
5. Az a **Jogcímszabály nevének** mezőbe írja be **hozzáférés engedélyezése a regisztrált eszközökről**.
6. Az a **bejövő jogcím típusa** legördülő listában válassza **regisztrált felhasználó van**.
7. Az a **bejövő jogcím értéke** mezőbe írja be **igaz**.
8. Válassza ki a **hozzáférés engedélyezése a bejövő jogcímmel rendelkező felhasználók** választógombot.
9. Válassza ki **Befejezés**, majd válassza ki **alkalmaz**.
10. Távolítsa el az összes szabály, amely megengedőbb, mint a létrehozott szabály. Például, távolítsa el az alapértelmezett szabály **összes felhasználó hozzáférésének engedélyezése**.

Az alkalmazás konfigurálva van az engedélyezi a hozzáférést, csak akkor, ha a felhasználó regisztrálva, és a munkahelyhez csatlakoztatott eszközről érkezik. Speciális hozzáférési szabályzatok, lásd: [kockázatkezelés további többtényezős hitelesítéssel érzékeny alkalmazások](https://technet.microsoft.com/library/dn280949.aspx).

Ezután konfigurálja a egyéni hibaüzenet az alkalmazáshoz. A hibaüzenet a következő tájékoztatja a felhasználókat arról, hogy azok csatlakoztatnia kell az eszközt a munkahelyhez mielőtt hozzáférhetne az alkalmazáshoz. Egy egyéni alkalmazás-hozzáférés-megtagadási üzenetet is létrehozhat egyéni HTML- és PowerShell használatával.

Az összevonási kiszolgálón nyisson meg egy PowerShell-parancsablakot, és írja be a következő parancsot. Cserélje le a parancs részeit elemek, amelyek adott rendszerhez:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Ez az alkalmazás elérése előtt regisztrálnia kell az eszközt.

**Ha iOS-eszközt használ, kattintson erre a hivatkozásra eszköz**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Az iOS-eszközzel csatlakoztat a munkahelyhez.

Ha a Windows 8.1-eszközt használ, az eszköz csatlakozhat kiválasztásával **gépház**> **hálózati** > **munkahelyi**.

A fenti parancsokban **függő fél megbízhatósági neve** az AD FS-ben az alkalmazás megbízható függő entitás megbízhatóságának objektum neve.
És **tartomány.com címről** a tartománynév, amely már konfigurálta az Azure Active Directoryban (például contoso.com).
Ügyeljen arra, hogy eltávolítása sortörést (ha van ilyen), amelyeket átad a HTML-tartalmakat a **Set-AdfsRelyingPartyWebContent** parancsmag.

Most felhasználók általi elérésekor az alkalmazás egy eszközről, amely nincs regisztrálva az Azure Active Directory eszközregisztrációs szolgáltatással, láthatják hiba.

