---
title: Tanúsítványalapú hitelesítés – Azure Active Directory
description: A tanúsítványalapú hitelesítés konfigurálása a környezetben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b57c4f474b0b9def08005f32f48225d36ea8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848833"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>A tanúsítványalapú hitelesítés első lépései az Azure Active Directoryban

A tanúsítványalapú hitelesítés lehetővé teszi, hogy az Azure Active Directory ügyféltanúsítvánnyal hitelesítse magát Windows, Android vagy iOS rendszerű eszközön, amikor az Exchange online fiókját a következőkhöz csatlakoztatja:

- Microsoft mobilalkalmazások, például a Microsoft Outlook és a Microsoft Word
- Exchange ActiveSync (EAS) ügyfelek

A szolgáltatás konfigurálásával nincs szükség felhasználónév és jelszó kombináció megadására bizonyos levelezési és Microsoft Office-alkalmazásokban a mobileszközön.

Ez a téma:

- Az Office 365 Enterprise, Business, Education és us Government csomagok bérlői számára a tanúsítványalapú hitelesítés konfigurálásának és használatához szükséges lépéseket tartalmazza. Ez a funkció előzetes verzióban érhető el az Office 365 China, az Egyesült Államok kormányának védelmi és az Egyesült Államok kormányzati szövetségi terveiben.
- Feltételezi, hogy már rendelkezik [nyilvános kulcsinfrastruktúrával (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) és [konfigurált AD FS-sel.](../hybrid/how-to-connect-fed-whatis.md)

## <a name="requirements"></a>Követelmények

A tanúsítványalapú hitelesítés konfigurálásához a következő utasításoknak igaznak kell lenniük:

- A tanúsítványalapú hitelesítés (CBA) csak a böngészőalkalmazások, a modern hitelesítést (ADAL) vagy az MSAL-kódtárakat használó natív ügyfelek összevont környezetei esetén támogatott. Az egyetlen kivétel az Exchange Active Sync (EAS) for Exchange Online (EXO), amely összevont és kezelt fiókokhoz használható.
- A legfelső szintű hitelesítésszolgáltatót és a köztes hitelesítésszolgáltatókat konfigurálni kell az Azure Active Directoryban.
- Minden hitelesítésszolgáltatónak rendelkeznie kell egy visszavont tanúsítványok listájával, amely egy internetes URL-címen keresztül hivatkozható.
- Legalább egy hitelesítésszolgáltatókonfigurálva kell lennie az Azure Active Directoryban. A kapcsolódó lépéseket [a Hitelesítésszolgáltatók konfigurálása](#step-2-configure-the-certificate-authorities) című szakaszban találja.
- Exchange ActiveSync-ügyfelek esetén az ügyféltanúsítványnak rendelkeznie kell a felhasználó irányítható e-mail címével az Exchange online-ban a Tulajdonos alternatív neve mező Egyszerű név vagy RFC822 Név értékében. Az Azure Active Directory leképezi az RFC822 értéket a címtárban lévő Proxy Address attribútumhoz.
- Az ügyféleszköznek legalább egy olyan hitelesítésszolgáltatóhoz kell hozzáférnie, amely ügyféltanúsítványokat állít ki.
- Az ügyfélhitelesítéshez ügyféltanúsítványt kell kiállítani az ügyfélszámára.

>[!IMPORTANT]
>Az Azure Active Directory sikeresen letöltendő és gyorsítótárazott crl-jének maximális mérete 20 MB, és a visszavont tanúsítványok listájának letöltéséhez szükséges idő nem haladhatja meg a 10 másodpercet.  Ha az Azure Active Directory nem tudja letölteni a visszavont tanúsítványok at, a megfelelő hitelesítésszolgáltató által kiadott tanúsítványokat használó tanúsítványalapú hitelesítési hitelesítésszolgáltató sikertelen lesz. A visszavont tanúsítványok listájának fájljainak méretkorlátain belüli állapotának biztosításához ajánlott eljárásokat kell biztosítani, ha a tanúsítványok élettartamát ésszerű határokon belül tartja, és megtisztítja a lejárt tanúsítványokat. 

## <a name="step-1-select-your-device-platform"></a>1. lépés: Válassza ki az eszköz platformját

Első lépésként a fontos eszközplatformesetében át kell tekintenie a következőket:

- Az Office mobilalkalmazások támogatása
- A konkrét végrehajtási követelmények

A kapcsolódó információk a következő eszközplatformokra vonatkoznak:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>2. lépés: A hitelesítésszolgáltatók konfigurálása

A hitelesítésszolgáltatók azure-active directoryban való konfigurálásához töltse fel az egyes hitelesítésszolgáltatókat:

* A tanúsítvány nyilvános része *.cer* formátumban
* Azok az internetre néző URL-címek, ahol a visszavont tanúsítványok listája (CRL)

A hitelesítésszolgáltató sémája a következőképpen néz ki:

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

A konfigurációhoz használhatja az [Azure Active Directory PowerShell 2-es verzióját:](/powershell/azure/install-adv2?view=azureadps-2.0)

1. Indítsa el a Windows PowerShellt rendszergazdai jogosultságokkal.
2. Telepítse az Azure AD modul [2.0.0.33-as](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) vagy újabb verzióját.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Első konfigurációs lépésként létre kell hoznia egy kapcsolatot a bérlővel. Amint létrejön a bérlővel való kapcsolat, áttekintheti, hozzáadhatja, törölheti és módosíthatja a címtárban definiált megbízható hitelesítésszolgáltatókat.

### <a name="connect"></a>Kapcsolódás

A bérlővel való kapcsolat létrehozásához használja a [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) parancsmast:

    Connect-AzureAD

### <a name="retrieve"></a>Elhoz

A címtárban definiált megbízható hitelesítésszolgáltatók lekéréséhez használja a [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmagját.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Hozzáadás

Megbízható hitelesítésszolgáltató létrehozásához használja a [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmast, és állítsa a **crlDistributionPoint** attribútumot a megfelelő értékre:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Eltávolítás

Megbízható hitelesítésszolgáltató eltávolításához használja az [Eltávolítás-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmast:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Módosítás

Megbízható hitelesítésszolgáltató módosításához használja a [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) parancsmast:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>3. lépés: Visszavonás konfigurálása

Az ügyféltanúsítvány visszavonásához az Azure Active Directory lekéri a visszavont tanúsítványok listáját (CRL) a hitelesítésszolgáltató adatai részeként feltöltött URL-címekről, és gyorsítótárazza azt. A visszavont tanúsítványok listájának utolsó közzétételi időbélyege **(Hatálybalépés dátumtulajdonság)** a visszavont tanúsítványok listájának érvényességének biztosítására szolgál. A visszavont tanúsítványok listáját rendszeresen hivatkoznak a lista részét beálló tanúsítványokhoz való hozzáférés visszavonására.

Ha több azonnali visszavonásra van szükség (például ha egy felhasználó elveszíti az eszközt), a felhasználó engedélyezési jogkivonata érvényteleníthető. Az engedélyezési jogkivonat érvénytelenítéséhez állítsa be az adott felhasználó **StsRefreshTokenValidFrom** mezőjét a Windows PowerShell használatával. Minden olyan felhasználó esetében frissítenie kell az **StsRefreshTokenValidFrom** mezőt, amelynek hozzáférését vissza kívánja vonni.

Annak érdekében, hogy a visszavonás továbbra is fennálljon, a visszavont tanúsítványok listájának **érvényességi dátumát** az **StsRefreshTokenValidFrom** által beállított érték utáni dátumra kell állítania, és biztosítania kell, hogy a kérdéses tanúsítvány a visszavont tanúsítványok listájában legyen.

A következő lépések az **stsRefreshTokenValidFrom** mező beállításával ismertetik az engedélyezési jogkivonat frissítésének és érvénytelenítésének folyamatát.

**Visszavonás konfigurálása:**

1. Rendszergazdai hitelesítő adatokkal való kapcsolatfelvétel az MSOL szolgáltatáshoz:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. A felhasználó aktuális StsRefreshTokensValidFrom értékének beolvasása:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Új StsRefreshTokensValidFrom érték konfigurálása a felhasználó számára az aktuális időbélyeggel megegyező:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A beállított dátumnak a jövőben kell lennie. Ha a dátum nem a jövőben, a **StsRefreshTokensValidFrom** tulajdonság nincs beállítva. Ha a dátum a jövőben van, az **StsRefreshTokensValidFrom** az aktuális időre van beállítva (nem a Set-MsolUser parancs által jelzett dátumra).

## <a name="step-4-test-your-configuration"></a>4. lépés: A konfiguráció tesztelése

### <a name="testing-your-certificate"></a>A tanúsítvány tesztelése

Első konfigurációs tesztként próbáljon meg bejelentkezni az [Outlook Web Access](https://outlook.office365.com) be vagy a [SharePoint](https://microsoft.sharepoint.com) **Online-ba az eszközböngészővel.**

Ha a bejelentkezés sikeres, akkor tudja, hogy:

- A felhasználói tanúsítvány ki lett építve a teszteszközre
- Az AD FS megfelelően van konfigurálva

### <a name="testing-office-mobile-applications"></a>Az Office mobilalkalmazásának tesztelése

**Tanúsítványalapú hitelesítés tesztelése a mobil Office-alkalmazásban:**

1. A teszteszközre telepítsen egy Office mobilalkalmazást (például a OneDrive-ot).
3. Indítsa el az alkalmazást.
4. Adja meg a felhasználónevét, majd válassza ki a használni kívánt felhasználói tanúsítványt.

A bejelentkezés sikeresen megtörtént.

### <a name="testing-exchange-activesync-client-applications"></a>Exchange ActiveSync-ügyfélalkalmazások tesztelése

Az Exchange ActiveSync (EAS) tanúsítványalapú hitelesítéssel történő eléréséhez az alkalmazás számára elérhetőnek kell lennie az ügyféltanúsítványt tartalmazó EAS-profilnak.

Az EAS-profilnak a következő információkat kell tartalmaznia:

- A hitelesítéshez használandó felhasználói tanúsítvány

- Az EAS-végpont (például outlook.office365.com)

Az EAS-profil konfigurálható és elhelyezhető az eszközön a mobileszköz-felügyelet (MDM) használatával, például az Intune-nal, vagy manuálisan elhelyezheti a tanúsítványt az EAS-profilban az eszközön.

### <a name="testing-eas-client-applications-on-android"></a>Az EAS-ügyfélalkalmazások tesztelése Androidon

**Tanúsítványhitelesítés tesztelése:**

1. Konfiguráljon egy EAS-profilt az alkalmazásban, amely megfelel az előző szakasz követelményeinek.
2. Nyissa meg az alkalmazást, és ellenőrizze, hogy a levelek szinkronizálása történik-e.

## <a name="next-steps"></a>További lépések

[További információ az Android-eszközök tanúsítványalapú hitelesítéséről.](active-directory-certificate-based-authentication-android.md)

[További információ az iOS-eszközökön a tanúsítványalapú hitelesítésről.](active-directory-certificate-based-authentication-ios.md)
