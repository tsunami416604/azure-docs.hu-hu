---
title: Android rendszeren az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés
description: További információ a támogatott forgatókönyvek és megoldások konfigurálásával Tanúsítványalapú hitelesítés követelményeinek az Android-eszközök
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.openlocfilehash: 3127d4d80368723d117226c98ad51637724906b1
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080940"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Android rendszeren az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés

Android-eszközök tanúsítványalapú hitelesítést (CBA) használatával hitelesíti az Azure Active Directory ügyféltanúsítvány használatával az eszközén való csatlakozáskor:

* Office-mobilalkalmazások például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync (EAS) típusú ügyfelek

Ez a funkció konfigurálása szükségtelenné meg kell adnia egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazások a mobileszközén.

Ez a témakör a támogatott forgatókönyveket és a követelményeket egy iOS(Android) eszközön a felhasználók számára az Office 365 nagyvállalati verzió, vállalati, oktatási, US Government, Kína bérlők CBA konfigurálásához, és a Németországi csomagok.

Ez a funkció érhető el előzetes verzióban érhető el az Office 365 US Government Defense és szövetségi csomagoknak.

## <a name="microsoft-mobile-applications-support"></a>A Microsoft-mobilalkalmazások támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Az Azure Information Protection alkalmazással |![Jelölőnégyzet][1] |
| Az Intune vállalati portál |![Jelölőnégyzet][1] |
| Microsoft Teams |![Jelölőnégyzet][1] |
| OneNote |![Jelölőnégyzet][1] |
| OneDrive |![Jelölőnégyzet][1] |
| Outlook |![Jelölőnégyzet][1] |
| Power BI |![Jelölőnégyzet][1] |
| Skype Vállalati verzió |![Jelölőnégyzet][1] |
| Word / Excel / PowerPoint |![Jelölőnégyzet][1] |
| Yammer |![Jelölőnégyzet][1] |

### <a name="implementation-requirements"></a>Megvalósítás követelményei

Az eszköz operációs rendszerének verziója kell lennie (Lollipop) Android 5.0 és újabb verziók.

Egy összevonási kiszolgálót kell konfigurálni.

Az Azure Active Directory ügyféltanúsítvány visszavonása esetén az AD FS jogkivonat a következő jogcímeket kell rendelkeznie:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (A sorozatszám az ügyféltanúsítvány)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Az ügyfél-tanúsítvány kiállítója karakterlánc)

Az Azure Active Directory ezeket a jogcímeket, a frissítési jogkivonatot, hozzáadja az, ha elérhetők a az AD FS jogkivonat (vagy bármely más SAML-jogkivonat). A frissítési jogkivonatot érvényesíteni kell, ha ezek az információk segítségével ellenőrizze a visszavont tanúsítványok.

Ajánlott eljárásként frissítenie kell az AD FS hibalapok a szervezet a következő információkat:

* A Microsoft Authenticator telepítése Android vonatkozó követelmény.
* Útmutató a felhasználói tanúsítvány beszerzésének módját.

További információkért lásd: [az AD FS bejelentkezési oldalainak testreszabása](https://technet.microsoft.com/library/dn280950.aspx).

Egyes Office-alkalmazások (a modern hitelesítés engedélyezve) küldése "*kérdezzen rá a bejelentkezési =*" az Azure AD a kérelemben. Alapértelmezés szerint az Azure AD a rendszer lefordítja "*kérdezzen rá a bejelentkezési =*"az AD FS, a kérelem"*wauth = usernamepassworduri*" (kéri, hajtsa végre a felhasználónév/jelszó-hitelesítés AD FS) és "*wfresh = 0*" (az AD FS kéri hagyja figyelmen kívül az egyszeri bejelentkezés állapotát, és hajtsa végre egy friss hitelesítést). Ha szeretné ezeket az alkalmazásokat a Tanúsítványalapú hitelesítés engedélyezése, módosítania az Azure AD alapértelmezés. Állítsa be a "*PromptLoginBehavior*"beállításaiban az összevont tartományt"*letiltott*".
Használhatja a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmagot a feladat végrehajtásához:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatása

Egyes Exchange ActiveSync-alkalmazások Android 5.0-s (Lollipop) vagy újabb rendszeren támogatott. Határozza meg, ha az e-mail-alkalmazás támogatja-e ezt a szolgáltatást, lépjen kapcsolatba az alkalmazás fejlesztője.

## <a name="next-steps"></a>További lépések

Ha azt szeretné, Tanúsítványalapú hitelesítés konfigurálása a környezetben, lásd: [Android rendszeren – Tanúsítványalapú hitelesítés első lépései](active-directory-certificate-based-authentication-get-started.md) útmutatást.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
