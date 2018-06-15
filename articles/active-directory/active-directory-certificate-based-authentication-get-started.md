---
title: Ismerkedés az Azure Active Directory-alapú hitelesítés
description: Tanúsítványalapú hitelesítés konfigurálása a környezetben
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: db2c19bdc303f6f7773772dd7873878ceb892cc3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33882851"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Ismerkedés az Azure Active Directory-alapú hitelesítés

Tanúsítvány alapú hitelesítést lehetővé teszi, hogy hitelesítse Azure Active Directory ügyféltanúsítvánnyal egy Windows-, Android vagy iOS eszközön az Exchange online fiókot való csatlakozáskor:

- A Microsoft mobilalkalmazás például a Microsoft Outlook és a Microsoft Word
- Exchange ActiveSync (EAS) ügyfeleket

Ez a funkció beállítása szükségtelenné teszi adjon meg egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazásokat a mobileszközön.

Ez a témakör:

- Konfigurálja és Tanúsítványalapú hitelesítés a felhasználók számára a bérlő használata az Office 365 vállalati, vállalati, oktatási és Amerikai Egyesült államokbeli kormányzati csomagokban biztosít. Ez a funkció érhető el az előzetes verzió az Office 365 Kína, a US Government védelmet és a US Government Szövetségi csomagokban.
- Feltételezi, hogy már rendelkezik egy [nyilvános kulcsokra épülő infrastruktúra (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) és [AD FS](connect/active-directory-aadconnectfed-whatis.md) konfigurálva.

## <a name="requirements"></a>Követelmények

Tanúsítványalapú hitelesítés konfigurálásához a következő utasításokat kell teljesülniük:

- Tanúsítvány alapú hitelesítést (CBA) csak külső környezeteket böngészőalkalmazásokban vagy natív modern hitelesítést (ADAL) használó ügyfelek esetén támogatott. Az egyetlen kivétel ez alól az Exchange Active Sync (EAS) az Exchange Online (EXO), amely összevont és a kezelt fiókok is használható.
- A legfelső szintű hitelesítésszolgáltatót és köztes hitelesítésszolgáltatót kell állítani az Azure Active Directoryban.
- Minden egyes hitelesítésszolgáltató visszavont tanúsítványok listájának (CRL), amely az Internet felé néző URL-cím használatával lehet hivatkozni kell rendelkeznie.
- Rendelkeznie kell legalább egy hitelesítésszolgáltatót az Azure Active Directoryban konfigurált. A kapcsolódó lépései a [konfigurálása a hitelesítésszolgáltatók](#step-2-configure-the-certificate-authorities) szakasz.
- Exchange ActiveSync-ügyfelek az ügyféltanúsítvány rendelkeznie kell a felhasználó irányítható e-mail címet az Exchange online az egyszerű felhasználónév vagy a tulajdonos alternatív neve mezőben RFC822 nevének értékét. Az Azure Active Directory RFC822 értéke van leképezve a proxykiszolgáló címét attribútum a címtárban.
- Az ügyféleszköz hozzá kell férniük legalább egy hitelesítésszolgáltató által kiállított tanúsítványt.
- Ügyféltanúsítványt az ügyfél-hitelesítés lett kiállítva az ügyfélnek.

## <a name="step-1-select-your-device-platform"></a>1. lépés: Válassza ki az eszközplatformhoz

Első lépésként, a fontos adatokhoz, eszközplatform kell tekintse meg a következőket:

- Az Office-mobilalkalmazások támogatása
- A megadott megvalósítási követelmények

A kapcsolódó információk a következő eszközplatformokat létezik:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>2. lépés: A hitelesítésszolgáltatók konfigurálása

A hitelesítésszolgáltatók konfigurálása az Azure Active Directoryban, minden egyes hitelesítésszolgáltatót, töltse fel a következő:

* A tanúsítvány nyilvános részét, a *.cer* formátumban
* Az internetes URL-címeket a visszavont tanúsítványok listájának (CRL) tároló

A sémát a hitelesítésszolgáltatótól a következőképpen néz ki:

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

A konfigurációt, használhatja a [Azure Active Directory PowerShell 2-es verzió](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Indítsa el a Windows PowerShell rendszergazdai jogosultságokkal.
2. Az Azure AD-modul verzió telepítése [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) vagy újabb verzióját.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Első lépésként konfiguráció a bérlő kapcsolatot kell. Amint a bérlő kapcsolat létezik, tekintse át, adja hozzá, törlése, és módosítsa a megbízható tanúsítványszolgáltatók, amely a könyvtárban van meghatározva.

### <a name="connect"></a>Kapcsolódás

A bérlő-kapcsolatot létrehozni, használja a [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) parancsmagot:

    Connect-AzureAD

### <a name="retrieve"></a>Beolvasása

A megbízható tanúsítványszolgáltatók a címtárban definiált lekéréséhez használja a [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmag.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Hozzáadás

Megbízható hitelesítésszolgáltató létrehozásához használja a [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmag és állítsa be a **Vlelérésihelye** attribútum a helyes értéket:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Eltávolítás

Megbízható hitelesítésszolgáltató eltávolításához használja a [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Módosítás

Megbízható hitelesítésszolgáltató módosításához használja a [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagot:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>3. lépés: A visszavont tanúsítványok konfigurálása

Egy ügyfél-tanúsítvány visszavonására, az Azure Active Directory beolvassa a visszavont tanúsítványok listáját (CRL) a hitelesítésszolgáltató adatai részeként feltöltött URL-címeket, és gyorsítótárba helyezi azt. Az utolsó közzététele időbélyeg (**dátumig** tulajdonság) a visszavonási lista annak biztosítására szolgál a CRL Elérési útja még érvényes. A visszavont tanúsítványok Listájának rendszeres időközönként hivatkozott vonni a lista egy részét képező tanúsítványok való hozzáférést.

Ha több azonnali visszavonás szükséges (például, ha a felhasználó elveszíti a eszköz), érvénytelenítve lenne a felhasználó az engedélyezési jogkivonatot. A hitelesítési tokenek érvénytelenítésére, állítsa be a **StsRefreshTokenValidFrom** mezőt az adott felhasználó Windows PowerShell használatával. Frissítenie kell a **StsRefreshTokenValidFrom** mezőt, minden olyan felhasználóhoz, szeretné visszavonni a hozzáférést.

Annak érdekében, hogy a visszavont tanúsítványok továbbra is fennáll, meg kell adni a **dátumig** a tanúsítvány-visszavonási lista által meghatározott érték utáni dátumot **StsRefreshTokenValidFrom** , és ellenőrizze, hogy a szóban forgó tanúsítvány megtalálható-e a tanúsítvány-visszavonási listát.

Az alábbi lépéseket vázoltuk frissítése és az engedélyezési jogkivonat érvénytelenítése úgy, hogy a **StsRefreshTokenValidFrom** mező.

**Konfigurálhatja a visszavont tanúsítványok:**

1. Csatlakozás a MSOL szolgáltatás rendszergazdai hitelesítő adatokkal:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. A felhasználó aktuális StsRefreshTokensValidFrom értéke beolvasása:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Új StsRefreshTokensValidFrom értéket konfigurálása a felhasználó az aktuális időbélyeg egyenlő:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A dátum, beállíthatja a jövőben kell lennie. Ha a dátum nem a jövőben a **StsRefreshTokensValidFrom** tulajdonsága nincs beállítva. Ha a dátumot a jövőben **StsRefreshTokensValidFrom** beállítása a jelenlegi időpontnál (nem a dátum szerint a Set-MsolUser paranccsal).

## <a name="step-4-test-your-configuration"></a>4. lépés: A konfiguráció tesztelése

### <a name="testing-your-certificate"></a>A tanúsítvány tesztelése

Való bejelentkezéshez használja a konfiguráció első teszteléséhez, mint [Outlook Web Access](https://outlook.office365.com) vagy [SharePoint Online](https://microsoft.sharepoint.com) használatával a **eszközökön böngészőben**.

Ha a bejelentkezés sikeres, akkor tudja, hogy:

- A felhasználói tanúsítvány van kiépítve a vizsgálati eszköz
- Az AD FS megfelelően van konfigurálva.

### <a name="testing-office-mobile-applications"></a>Office-mobilalkalmazások tesztelése

**Tanúsítvány alapú hitelesítést a mobil Office-alkalmazás tesztelése:**

1. A vizsgálati eszköz telepítse az Office-mobilalkalmazás (például a OneDrive).
3. Az alkalmazás elindításához.
4. Adja meg a felhasználónevét, majd válassza ki a használni kívánt felhasználói tanúsítványt.

Akkor érdemes lehet sikeres volt.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync ügyfél alkalmazások tesztelése

Exchange ActiveSync (EAS) keresztül tanúsítvány alapú hitelesítést eléréséhez az EAS-profilt, amely tartalmazza az ügyféltanúsítványt az alkalmazás elérhetőnek kell lennie.

Az EAS-profil a következő adatokat kell tartalmazniuk:

- A hitelesítéshez használandó felhasználói tanúsítvány

- Az EAS-végpont (például outlook.office365.com)

Az EAS-profil konfigurálhatók és elhelyezett keresztül a mobileszköz-kezelés (MDM) például az Intune-ban, vagy manuálisan helyezi el a tanúsítványt az EAS-profil az eszköz használata az eszközön.

### <a name="testing-eas-client-applications-on-android"></a>Az Android EAS-ügyfél alkalmazások tesztelése

**Tanúsítványalapú hitelesítés tesztelése:**

1. Adja meg az alkalmazást, amely eleget tesz a korábbi szakaszában az EAS-profil.
2. Indítsa el az alkalmazást, és győződjön meg arról, hogy mail szinkronizál.

## <a name="next-steps"></a>További lépések

[Tanúsítvány alapú való továbbításához végzett hitelesítés további információt az Android-eszközökön.](active-directory-certificate-based-authentication-android.md)

[Tanúsítvány alapú való továbbításához végzett hitelesítés további információt az iOS-eszközökön.](active-directory-certificate-based-authentication-ios.md)