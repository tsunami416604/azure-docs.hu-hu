---
title: Ismerkedés az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés
description: Ügyféltanúsítvány-alapú hitelesítés konfigurálása a környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e6d66993658e7f0531a43a9b9e0f93183f91d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165144"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Az Azure Active Directory Tanúsítványalapú hitelesítés első lépései

Ügyféltanúsítvány-alapú hitelesítés lehetővé teszi hitelesíthető az Azure Active Directory egy Windows, Android vagy IOS rendszerű eszközön ügyféltanúsítvánnyal az Exchange online-fiókhoz való csatlakozáskor:

- A Microsoft mobil alkalmazások, például a Microsoft Outlook és a Microsoft Word
- Exchange ActiveSync (EAS) típusú ügyfelek

Ez a funkció konfigurálása szükségtelenné meg kell adnia egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazások a mobileszközén.

Ez a témakör:

- A lépések végrehajtásával konfigurálhatja, és a tanúsítvány alapú hitelesítést a felhasználók számára a bérlők kihasználhassák az Office 365 nagyvállalati verzió, vállalati, oktatási és US Government-csomagokban biztosít. Ez a funkció érhető el előzetes verzióban érhető el az Office 365-Kína, US Government Defense és US Government Szövetségi csomagoknak.
- Feltételezi, hogy már rendelkezik egy [nyilvános kulcsokra épülő infrastruktúrájú (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) és [az AD FS](../hybrid/how-to-connect-fed-whatis.md) konfigurálva.

## <a name="requirements"></a>Követelmények

Tanúsítványalapú hitelesítés konfigurálásához a következő utasításokat kell teljesülniük:

- Tanúsítványalapú hitelesítés (CBA) csak az összevont környezetekben böngészőalkalmazásokban vagy modern authentication (ADAL) használó natív ügyfelek esetében támogatott. Az egyetlen kivétel az Exchange Active Sync (EAS) az Exchange Online (EXO), amely összevont és felügyelt fiók használható a.
- A legfelső szintű hitelesítésszolgáltatót és köztes hitelesítésszolgáltatót kell konfigurálni az Azure Active Directoryban.
- Minden egyes hitelesítésszolgáltató visszavont tanúsítványok listájának (CRL), amely egy internetkapcsolattal rendelkező URL-CÍMEN keresztül lehet rá hivatkozni kell rendelkeznie.
- Az Azure Active Directoryban beállított legalább egy hitelesítésszolgáltatót kell rendelkeznie. A kapcsolódó lépései a [konfigurálása a hitelesítésszolgáltatók](#step-2-configure-the-certificate-authorities) szakaszban.
- Exchange ActiveSync-ügyfelek az ügyféltanúsítvány rendelkeznie kell a felhasználó irányítható e-mail címét az Exchange online az egyszerű felhasználónév vagy a tulajdonos alternatív neve mezőben RFC822 nevének értékét. Az Azure Active Directory az RFC822 értéket képez le a Proxy cím attribútuma a címtárban.
- Az ügyféleszköz legalább egy hitelesítésszolgáltató által kiállított ügyféltanúsítványok hozzáféréssel kell rendelkeznie.
- Ügyféltanúsítványt az ügyfél-hitelesítés részére lett kiállítva az ügyfélnek.

## <a name="step-1-select-your-device-platform"></a>1. lépés: Válassza ki az eszközplatformot

Az Ön számára, eszközplatform, első lépésként tekintse át a következőket kell:

- Az Office-mobilalkalmazások támogatása
- A megvalósítási követelmények

A kapcsolódó információkat a következő eszközplatformokat létezik:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>2. lépés: A hitelesítésszolgáltatók konfigurálása

A hitelesítésszolgáltatók konfigurálása az Azure Active Directoryban, minden egyes hitelesítésszolgáltató, töltse fel a következőket:

* A tanúsítvány nyilvános részét, a *.cer* formátum
* Az internetre irányuló URL-címeket, hol találhatók a visszavont tanúsítványok listájának (CRL)

A séma a hitelesítésszolgáltató a következőképpen néz ki:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

A konfiguráció, használhatja a [Azure Active Directory PowerShell 2-es verzió](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Indítsa el a Windows Powershellt rendszergazdai jogosultságokkal.
2. Az Azure AD-modul verzió telepítése [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) vagy újabb verziója.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Az első konfigurációs lépés kell kapcsolatot létesíteni az a bérlőhöz. Amint az a bérlőhöz kapcsolat létezik, tekintse át, hozzáadása, törlése, és módosítsa a könyvtárat a definiált megbízható hitelesítésszolgáltatók.

### <a name="connect"></a>Kapcsolódás

A bérlő kapcsolatot hoz létre, használja a [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) parancsmagot:

    Connect-AzureAD

### <a name="retrieve"></a>Lekérés

A megbízható tanúsítványszolgáltatók a könyvtárat a definiált lekéréséhez használja a [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Hozzáadás

Egy megbízható hitelesítésszolgáltatótól létrehozásához használja a [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot, és állítsa a **Vlelérésihelye** attribútum a helyes értékre:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Eltávolítás

Egy megbízható hitelesítésszolgáltatótól eltávolításához használja a [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Módosítás

Egy megbízható hitelesítésszolgáltatótól módosításához használja a [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>3. lépés: Visszavont tanúsítványok konfigurálása

Ügyféltanúsítvány visszavonása, hogy az Azure Active Directory hitelesítésszolgáltató adatai részeként feltöltése URL-címeket olvas be a visszavont tanúsítványok listáját (CRL), és gyorsítótárba helyezi azt. Az utolsó időbélyeg közzétételi (**hatályba lépés dátuma** tulajdonság) a visszavont tanúsítványok Listáját a segítségével győződjön meg arról a CRL-t továbbra is érvényes. A visszavont tanúsítványok Listájának rendszeres időközönként vonni a hozzáférést a tanúsítványok, a lista egy részét képező hivatkozik.

Ha több azonnali visszavonás szükség (például, ha egy felhasználó elveszíti az eszközt), a felhasználó az engedélyezési jogkivonatot érvényteleníthető. Érvényteleníteni az engedélyezési jogkivonatra, állítsa be a **StsRefreshTokenValidFrom** mezőjét az adott felhasználó Windows PowerShell használatával. Frissítenie kell a **StsRefreshTokenValidFrom** mezőt, minden olyan felhasználóhoz, szeretné visszavonni a hozzáférést.

Annak érdekében, hogy a visszavont tanúsítványok továbbra is fennáll, be kell állítani a **hatályba lépés dátuma** által meghatározott érték utáni dátumot a CRL **StsRefreshTokenValidFrom** és ellenőrizze, hogy a szóban forgó tanúsítvány szerepel-e a visszavont tanúsítványok Listáját.

Az alábbi lépéseket vázoltuk frissítése és az engedélyezési jogkivonatot érvénytelenítése beállításával a **StsRefreshTokenValidFrom** mező.

**Visszavont tanúsítványok konfigurálása:**

1. Csatlakozás az MSOL szolgáltatásba a rendszergazdai hitelesítő adataival:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Kérje le a felhasználó aktuális StsRefreshTokensValidFrom értéke:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. A felhasználó egyenlő az aktuális Timestamp konfigurálása egy új StsRefreshTokensValidFrom értéket:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A beállított dátumot kell lennie a jövőben. Ha a dátum nem a jövőben a **StsRefreshTokensValidFrom** tulajdonság nincs beállítva. Ha a dátum a jövőben **StsRefreshTokensValidFrom** az aktuális idő (nem a Set-MsolUser parancs által jelzett dátuma) értékre van állítva.

## <a name="step-4-test-your-configuration"></a>4. lépés: A konfiguráció tesztelése

### <a name="testing-your-certificate"></a>A tanúsítvány tesztelés

Első konfiguráció teszteléséhez, mint kell próbál bejelentkezni [az Outlook Web Access](https://outlook.office365.com) vagy [SharePoint online-hoz](https://microsoft.sharepoint.com) használatával a **eszközön futó böngészővel**.

Ha a bejelentkezés sikeres volt, majd tudja, hogy:

- A felhasználói tanúsítvány lett kiépítve a vizsgálati eszköz
- Az AD FS megfelelően van konfigurálva.

### <a name="testing-office-mobile-applications"></a>Office-mobilalkalmazások tesztelése

**Tanúsítvány alapú hitelesítést a mobil Office-alkalmazás teszteléséhez:**

1. A vizsgálati eszköz telepítse az Office mobile alkalmazás (például a OneDrive).
3. Indítsa el az alkalmazást.
4. Adja meg a felhasználónevet, és válassza ki a használni kívánt felhasználói tanúsítványt.

Akkor érdemes lehet sikeresen bejelentkezett.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync-ügyfélalkalmazások tesztelése

Tanúsítványalapú hitelesítés használatával az Exchange ActiveSync (EAS) eléréséhez az EAS-profilt, amely tartalmazza az ügyféltanúsítvány az alkalmazás elérhetőnek kell lennie.

Az EAS-profilnévként tartalmaznia kell a következő információkat:

- A felhasználói tanúsítvány hitelesítéséhez

- Az EAS-végpont (például outlook.office365.com)

EAS-profilnévként konfigurálhatók és az eszközön keresztül a kihasználtság mobileszköz-kezelési (MDM) például az Intune-ban, vagy úgy, hogy manuálisan a tanúsítványt az eszköz EAS-profilban elhelyezett.

### <a name="testing-eas-client-applications-on-android"></a>Tesztelés Android rendszeren az EAS-ügyfélalkalmazások

**Tanúsítványalapú hitelesítés teszteléséhez:**

1. Az EAS-profil konfigurálása az alkalmazásban, amely eleget tesz a korábbi szakaszában.
2. Nyissa meg az alkalmazást, és győződjön meg arról, hogy mail szinkronizál.

## <a name="next-steps"></a>További lépések

[Tanúsítványalapú hitelesítés további információt az Android-eszközökön.](active-directory-certificate-based-authentication-android.md)

[Tanúsítványalapú hitelesítés további információ az iOS-eszközökön.](active-directory-certificate-based-authentication-ios.md)
