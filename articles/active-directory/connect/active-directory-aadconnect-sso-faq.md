---
title: "Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyakori kérdések |} Microsoft Docs"
description: "Tudnivalók Azure Active Directory zökkenőmentes egyszeri bejelentkezést gyakran feltett kérdésekre adott válaszokat."
services: active-directory
keywords: "Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: billmath
ms.openlocfilehash: a91e46d3aa0b04015b60837ae4aef020acd42279
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakran ismételt kérdések

Ebben a cikkben oldjuk gyakori kérdésekkel kapcsolatos Azure Active Directory zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO). Vissza az új tartalom ellenőrizni.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Milyen bejelentkezési módszerek zökkenőmentes SSO működnek?

Zökkenőmentes SSO kombinálva, vagy a [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-synchronization.md) vagy [áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) bejelentkezési módszerek. Azonban ez a szolgáltatás az Active Directory összevonási szolgáltatások (ADFS) nem használható.

## <a name="is-seamless-sso-a-free-feature"></a>Lehetővé teszi az zökkenőmentes SSO szabad?

Zökkenőmentes SSO szabad funkció, nem kell használni az Azure AD bármely fizetős verziója.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Is zökkenőmentes SSO elérhető a [Microsoft Azure Németország felhő](http://www.microsoft.de/cloud-deutschland) és a [a Microsoft Azure Government felhő](https://azure.microsoft.com/features/gov/)?

Nem. Zökkenőmentes SSO csak érhető el a világméretű az Azure AD-példányban.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Milyen alkalmazások előnyeit `domain_hint` vagy `login_hint` zökkenőmentes SSO-paraméter képességét?

A lista az alkalmazások, amelyek ezeket a paramétereket, és a meglévők közül, amelyek nem küldenek fordítása végezzük. Ha olyan alkalmazásokkal rendelkezik, érdekelt, ossza meg velünk megjegyzéseit szakaszában.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Zökkenőmentes egyszeri Bejelentkezést támogatja `Alternate ID` , a felhasználónév helyett `userPrincipalName`?

Igen. Zökkenőmentes egyszeri Bejelentkezést támogatja `Alternate ID` konfigurálásakor az Azure AD Connectben látható felhasználónévként [Itt](active-directory-aadconnect-get-started-custom.md). Nem minden Office 365-alkalmazások támogatják a `Alternate ID`. Tekintse meg a támogatási nyilatkozattal az adott alkalmazás dokumentációját.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Kívánt Windows 10-eszközök regisztrálása az Azure ad-vel, az AD FS használata nélkül. Használható is zökkenőmentes SSO helyett?

Ebben a forgatókönyvben Igen, van szüksége a verzió a 2.1-es vagy újabb a [munkahelyhez ügyfél](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hogyan lehet I váltása Kerberos visszafejtési kulcsa a `AZUREADSSOACC` számítógépfiók?

Fontos, hogy a Kerberos visszafejtési kulcsa gyakran váltása a `AZUREADSSOACC` (amely az Azure AD) létrehozott számítógépfiók a helyszíni Active Directory-erdőben.

>[!IMPORTANT]
>Erősen ajánlott, hogy Ön váltása a Kerberos visszafejtési kulcs legalább 30 nap.

Kövesse az alábbi lépéseket a helyszíni kiszolgálón az Azure AD Connect futtató:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>1. lépés Szerezze be az AD-erdőhöz, ahol engedélyezve van zökkenőmentes SSO listát

1. Első lépésként töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ezután töltse le és telepítse a [64 bites Azure Active Directory-modul Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
4. Importálja a zökkenőmentes SSO PowerShell modult használja a következő parancsot: `Import-Module .\AzureADSSO.psd1`.
5. PowerShell futtatása rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Ez a parancs egy előugró ablak, a bérlő globális rendszergazdai hitelesítő adatokat adjon meg.
6. Hívás `Get-AzureADSSOStatus`. Ez a parancs listáját jeleníti meg, akkor az AD-erdőkkel (nézze meg a "Tartományok" lista) a, amelyhez ez a funkció engedélyezve van.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>2. lépés A Kerberos visszafejtési kulcs az egyes úgy lett beállítva, az AD-erdő frissítése

1. Hívás `$creds = Get-Credential`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatait a tervezett AD-erdőben.
2. Hívás `Update-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs frissíti a Kerberos visszafejtési kulcs a `AZUREADSSOACC` számítógép fiókját az adott Active Directory-erdőben, és frissíti az Azure AD-ben.
3. Ismételje meg a fenti lépéseket minden beállította a szolgáltatást az AD-erdőben.

>[!IMPORTANT]
>Győződjön meg arról, hogy _nem_ futtassa a `Update-AzureADSSOForest` parancs egynél többször. Ellenkező esetben a szolgáltatás nem működik a időpontig, a felhasználók Kerberos-jegyek lejár, és a helyszíni Active Directory rendszer kiadását.

## <a name="how-can-i-disable-seamless-sso"></a>Hogyan letilthatja zökkenőmentes SSO?

Zökkenőmentes SSO Azure AD Connect használatával lehet letiltani.

Futtassa az Azure AD Connect, válassza a "Módosítás felhasználói bejelentkezési oldalának", és kattintson a "Tovább gombra". Ezután törölje a jelet az "Egyszeri bejelentkezés engedélyezése a" lehetőséget. Kövesse a varázsló lépéseit. A varázsló befejezése után zökkenőmentes egyszeri bejelentkezés le van tiltva a bérlő.

Azonban egy üzenet jelenik meg, hogy a következő képernyőn:

"Egyszeri bejelentkezés nem engedélyezett, de további manuális lépéseket karbantartás elvégezni. További információ"

A folyamat befejezéséhez kövesse az alábbi manuális lépéseket a helyszíni kiszolgálón az Azure AD Connect futtató:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>1. lépés Szerezze be az AD-erdőhöz, ahol engedélyezve van zökkenőmentes SSO listát

1. Első lépésként töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ezután töltse le és telepítse a [64 bites Azure Active Directory-modul Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
4. Importálja a zökkenőmentes SSO PowerShell modult használja a következő parancsot: `Import-Module .\AzureADSSO.psd1`.
5. PowerShell futtatása rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Ez a parancs egy előugró ablak, a bérlő globális rendszergazdai hitelesítő adatokat adjon meg.
6. Hívás `Get-AzureADSSOStatus`. Ez a parancs listáját jeleníti meg, akkor az AD-erdőkkel (nézze meg a "Tartományok" lista) a, amelyhez ez a funkció engedélyezve van.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>2. lépés Törölje kézzel a `AZUREADSSOACCT` számítógépfiókkal minden AD-erdőben, melyek megjelennek a listában.

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-sso-quick-start.md) - létrehozásához, és az Azure AD zökkenőmentes SSO futtatása.
- [**Műszaki mélyreható** ](active-directory-aadconnect-sso-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
