---
title: Jelentkezzen be az e-mail-címmel, alternatív bejelentkezési AZONOSÍTÓként Azure Active Directory
description: Megtudhatja, hogyan konfigurálhatja és engedélyezheti a felhasználók számára, hogy bejelentkezzenek Azure Active Directory az e-mail-címükkel, alternatív bejelentkezési AZONOSÍTÓként (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837387"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Bejelentkezés az Azure-ba e-mailben másodlagos bejelentkezési AZONOSÍTÓként (előzetes verzió)

Számos szervezet szeretné engedélyezni, hogy a felhasználók a helyszíni címtár-környezettel megegyező hitelesítő adatokkal jelentkezzenek be az Azure-ba. A hibrid hitelesítésnek nevezett megközelítéssel a felhasználóknak csak egy hitelesítő adatot kell megemlékezniük.

Néhány szervezet nem helyezte át a hibrid hitelesítést a következő okok miatt:

* Alapértelmezés szerint a Azure Active Directory (Azure AD) egyszerű felhasználónév (UPN) a helyszíni címtárral megegyező egyszerű felhasználónévre van beállítva.
* Az Azure AD UPN módosítása helytelen egyezést hoz létre az olyan helyszíni és az Azure-környezetek között, amelyek bizonyos alkalmazásokkal és szolgáltatásokkal kapcsolatos problémákat okozhatnak.
* Az üzleti vagy megfelelőségi okok miatt a szervezet nem szeretné a helyszíni UPN-t használni az Azure-ba való bejelentkezéshez.

Ha segítségre van szüksége a hibrid hitelesítésre való áttéréshez, beállíthatja az Azure AD-t úgy, hogy a felhasználók a hitelesített tartományba tartozó e-mailben jelentkezzenek be az Azure-ba, alternatív bejelentkezési AZONOSÍTÓként. Ha például a *contoso* a *Fabrikam*-re lett átnevezve, és nem kívánja tovább használni az örökölt `balas@contoso.com` UPN-t, a rendszer mostantól másodlagos Bejelentkezési azonosítóként is használhatja az e-mailt. Egy alkalmazáshoz vagy szolgáltatáshoz való hozzáféréshez a felhasználók a hozzárendelt e-mail-címével, például a paranccsal jelentkezhetnek be az Azure-ba `balas@fabrikam.com` .

|     |
| --- |
| Jelentkezzen be az Azure AD-be a másodlagos bejelentkezési AZONOSÍTÓként a Azure Active Directory nyilvános előzetes verziójára. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Az Azure AD bejelentkezési módszereinek áttekintése

Az egyszerű felhasználónevek (UPN-EK) a helyszíni címtárban és az Azure AD-ben lévő felhasználói fiókok egyedi azonosítói. A címtárban lévő összes felhasználói fiókot egy egyszerű felhasználónév jelképezi, például: `balas@contoso.com` . Ha a helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetet az Azure AD-vel szinkronizálja, alapértelmezés szerint az Azure AD UPN-t úgy kell beállítani, hogy az megfeleljen a helyszíni UPN-nek.

Számos szervezet esetében érdemes beállítani a helyszíni UPN-t és az Azure AD UPN-t az egyeztetéshez. Amikor a felhasználók bejelentkeznek az Azure-alkalmazásokba és-szolgáltatásokba, az Azure AD UPN-t használják. Egyes szervezetek azonban nem használhatják a bejelentkezéshez szükséges UPN-ket az üzleti szabályzatok vagy a felhasználói élménygel kapcsolatos problémák miatt.

Azok a szervezetek, amelyek nem használhatják az egyező UPN-ket az Azure AD-ben, néhány lehetőség közül választhatnak:

* Az egyik módszer az, ha az Azure AD UPN-t az üzleti igények alapján (például:) szeretné beállítani `balas@fabrikam.com` .
    * Azonban nem minden alkalmazás és szolgáltatás kompatibilis a helyszíni UPN-re és az Azure AD UPN-re vonatkozó eltérő érték használatával.
* A jobb megoldás az, hogy az Azure AD és a helyszíni UPN-azonosítók ugyanarra az értékre legyenek beállítva, és konfigurálja az Azure AD-t, hogy a felhasználók alternatív Bejelentkezési azonosítóként jelentkezzenek be az Azure-ba.

Ha az e-mail-cím másodlagos bejelentkezési AZONOSÍTÓként szolgál, a felhasználók az egyszerű felhasználónév megadásával továbbra is bejelentkezhetnek az Azure-ba, de az e-mailben is bejelentkezhetnek. Ennek támogatásához meg kell adnia egy e-mail-címet a felhasználó *ProxyAddresses* attribútumában a helyszíni címtárban. Ez a *ProxyAddress* attribútum egy vagy több e-mail-címet támogat.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Bejelentkezési e-mail-címek szinkronizálása az Azure AD-vel

A hagyományos Active Directory tartományi szolgáltatások (AD DS) vagy Active Directory összevonási szolgáltatások (AD FS) (AD FS) hitelesítés közvetlenül a hálózaton történik, és a AD DS-infrastruktúra kezeli. A hibrid hitelesítéssel a felhasználók Ehelyett közvetlenül az Azure AD-be is jelentkezhetnek be.

Ennek a hibrid hitelesítési módszernek a támogatásához szinkronizálja helyszíni AD DS környezetét az Azure AD-vel [Azure ad Connect][azure-ad-connect] és konfigurálja jelszó-kivonatoló szinkronizálás (PHS) vagy átmenő hitelesítés (PTA ESP) használatával.

A felhasználó mindkét konfigurációs beállításban beküldi felhasználónevét és jelszavát az Azure AD-nek, amely ellenőrzi a hitelesítő adatokat, és kibocsát egy jegyet. Amikor a felhasználók bejelentkeznek az Azure AD-be, azzal megszűnik a szervezet számára a AD FS infrastruktúra üzemeltetése és kezelése.

![Az Azure AD hibrid identitás diagramja jelszó-kivonatolási szinkronizálással](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Az Azure AD hibrid identitás diagramja átmenő hitelesítéssel](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

A Azure AD Connect által automatikusan szinkronizált felhasználói attribútumok egyike a *ProxyAddresses*. Ha a felhasználók e-mail-címmel rendelkeznek a helyszíni AD DS környezetben a *ProxyAddresses* attribútum részeként, az automatikusan szinkronizálva lesz az Azure ad-vel. Ezt az e-mail-címet később közvetlenül az Azure AD bejelentkezési folyamatában használhatja alternatív bejelentkezési AZONOSÍTÓként.

> [!IMPORTANT]
> A rendszer csak a bérlő ellenőrzött tartományában lévő e-maileket szinkronizálja az Azure AD-vel. Minden egyes Azure AD-bérlő egy vagy több ellenőrzött tartománnyal rendelkezik, amelyekhez Ön bizonyította a tulajdonjogát, és a bérlőhöz egyedi módon kötve van.
>
> További információ: [Egyéni tartománynév hozzáadása és ellenőrzése az Azure ad-ben][verify-domain].

További információ: [válassza ki a megfelelő hitelesítési módszert az Azure ad Hybrid Identity megoldáshoz][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Felhasználói bejelentkezés engedélyezése e-mail-címmel

Ha a *ProxyAddresses* attribútummal rendelkező felhasználók szinkronizálása az Azure ad-vel Azure ad Connect használatával történik, engedélyeznie kell a szolgáltatást a felhasználóknak, hogy alternatív Bejelentkezési azonosítóként jelentkezzen be a bérlő számára. Ez a funkció azt jelzi, hogy az Azure AD-beli bejelentkezési kiszolgálók nem csak az UPN-értékekkel ellenőrzik a bejelentkezési nevet, hanem az e-mail-cím *ProxyAddresses* is.

Az előzetes verzió ideje alatt a PowerShell használatával jelenleg csak az e-mail-címmel engedélyezheti a bejelentkezést másodlagos bejelentkezési AZONOSÍTÓként. A következő lépések végrehajtásához *bérlői rendszergazdai* engedélyekre van szükség:

1. Nyisson meg egy PowerShell-munkamenetet rendszergazdaként, majd telepítse a *AzureADPreview* modult az [install-Module][Install-Module] parancsmag használatával:

    ```powershell
    Install-Module AzureADPreview
    ```

    Ha a rendszer kéri, válassza az **Y** elemet a NuGet telepítéséhez vagy nem megbízható adattárból történő telepítéshez.

1. Jelentkezzen be az Azure AD-bérlőbe *bérlői rendszergazdaként* a (z) [AzureAD][Connect-AzureAD] parancsmag használatával:

    ```powershell
    Connect-AzureAD
    ```

    A parancs a fiókjával, a környezettel és a bérlői AZONOSÍTÓval kapcsolatos információkat ad vissza.

1. Ellenőrizze, hogy a *HomeRealmDiscoveryPolicy* -házirend már létezik-e a bérlőben a [Get-AzureADPolicy][Get-AzureADPolicy] parancsmag használatával a következőképpen:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Ha jelenleg nincs beállítva házirend, a parancs nem ad vissza semmit. Ha a rendszer visszaadja a szabályzatot, hagyja ki ezt a lépést, és folytassa a következő lépéssel egy meglévő szabályzat frissítéséhez.

    A *HomeRealmDiscoveryPolicy* házirend bérlőhöz való hozzáadásához használja a [New-AzureADPolicy][New-AzureADPolicy] parancsmagot, és állítsa a *AlternateIdLogin* attribútumot *"engedélyezve" értékre: true (igaz* ), ahogy az alábbi példában látható:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    A szabályzat sikeres létrehozása után a parancs visszaadja a szabályzat AZONOSÍTÓját, ahogy az a következő példában látható:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Ha már van konfigurált házirend, ellenőrizze, hogy az *AlternateIdLogin*   attribútum engedélyezve van-e, ahogy az alábbi példában látható:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Ha a házirend létezik, de a *AlternateIdLogin* attribútum nincs jelen vagy engedélyezve, vagy ha más attribútumok vannak a megőrizni kívánt szabályzatban, frissítse a meglévő szabályzatot a [set-AzureADPolicy][Set-AzureADPolicy] parancsmaggal.

    > [!IMPORTANT]
    > Amikor frissíti a szabályzatot, ügyeljen rá, hogy a régi és az új *AlternateIdLogin* attribútumot is tartalmazza.

    A következő példa hozzáadja a *AlternateIdLogin* attribútumot, és megőrzi a *AllowCloudPasswordValidation* attribútumot, amely esetleg már be van állítva:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Győződjön meg arról, hogy a frissített szabályzat megjeleníti a módosításokat, valamint azt, hogy a *AlternateIdLogin* attribútum mostantól engedélyezve van:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Felhasználói bejelentkezés tesztelése e-mailben

Annak ellenőrzéséhez, hogy a felhasználók bejelentkezhetnek-e e-mail-címmel, az [https://myprofile.microsoft.com][my-profile] e-mail-címük alapján egy felhasználói fiókkal, például a (z `balas@fabrikam.com` ), nem pedig az egyszerű felhasználónévvel is bejelentkezhetnek `balas@contoso.com` . A bejelentkezési élménynek ugyanúgy kell kinéznie, mint az UPN-alapú bejelentkezési eseménnyel.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a felhasználók az e-mail-címükkel ütköznek a bejelentkezési eseményekkel, tekintse át a következő hibaelhárítási lépéseket:

1. Ellenőrizze, hogy a felhasználói fiók rendelkezik-e e-mail-címmel, amely a *ProxyAddresses* attribútumhoz van beállítva a helyszíni AD DS környezetben.
1. Ellenőrizze, hogy a Azure AD Connect konfigurálva van-e, és sikeresen szinkronizálja-e a felhasználói fiókokat a helyszíni AD DS-környezetből az Azure AD-be.
1. Győződjön meg arról, hogy az Azure AD- *HomeRealmDiscoveryPolicy* szabályzata *"enabled"* értékre van állítva a *AlternateIdLogin* attribútum: True:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a hibrid identitásról, például a Azure AD alkalmazás proxyról vagy Azure AD Domain Servicesról, tekintse [meg az Azure ad hibrid identitást a helyszíni számítási feladatok eléréséhez és kezeléséhez][hybrid-overview].

További információ a hibrid identitási műveletekről: [jelszó-kivonatolási szinkronizálás][phs-overview] vagy [átmenő hitelesítés][pta-overview] szinkronizálása.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
