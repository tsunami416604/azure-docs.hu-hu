---
title: Az Android az Azure Active Directory-alapú hitelesítés
description: További tudnivalók a támogatott forgatókönyveket és a megoldások konfigurálását tanúsítvány alapú hitelesítést követelményei az Android-eszközök
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: ef58fd6c4701f367c6b8664c9cf9ee76e15fbd70
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33880745"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Az Android az Azure Active Directory-alapú hitelesítés

Android-eszközök a tanúsítványalapú hitelesítést (CBA) segítségével hitelesítéséhez az Azure Active Directory ügyféltanúsítvány használatával az eszközön való csatlakozáskor:

* Office-mobilalkalmazások például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync (EAS) ügyfeleket

Ez a funkció beállítása szükségtelenné teszi adjon meg egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazásokat a mobileszközön.

Ez a témakör a követelményeket és a támogatott forgatókönyveket CBA konfigurálásához a felhasználók számára a bérlő az Office 365 Enterprise, vállalati, oktatási, USA szövetségi kormányzatának, Kína iOS(Android)-eszközön, és Németország tervez.

Ez a funkció érhető el az előzetes verzió az Office 365 US Government védelmet és szövetségi csomagokban.

## <a name="microsoft-mobile-applications-support"></a>A Microsoft mobilalkalmazás-támogatás

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Jelölőnégyzet][1] |
| Intune vállalati portál |![Jelölőnégyzet][1] |
| Microsoft Teams |![Jelölőnégyzet][1] |
| OneNote |![Jelölőnégyzet][1] |
| OneDrive |![Jelölőnégyzet][1] |
| Outlook |![Jelölőnégyzet][1] |
| Power BI |![Jelölőnégyzet][1] |
| Skype Vállalati verzió |![Jelölőnégyzet][1] |
| Word / Excel / PowerPoint |![Jelölőnégyzet][1] |
| Yammer |![Jelölőnégyzet][1] |

### <a name="implementation-requirements"></a>Megvalósítás követelményei

Az eszköz operációs rendszere verziójúnak kell lennie az Android 5.0 (Egyszerű fejrészalakzat) vagy újabb verzió.

Az összevonási kiszolgáló be kell állítani.

Az Azure Active Directory ügyféltanúsítvány visszavonni az AD FS jogkivonat a következő jogcímeket kell rendelkeznie:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (A sorozatszámát az ügyféltanúsítvány)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Az ügyfél-tanúsítvány kiállítója karakterlánc)

Az Azure Active Directory ezeket a jogcímeket, hogy a frissítési jogkivonat ad hozzá, ha azok elérhetők az AD FS jogkivonat (vagy bármely más SAML-jogkivonat). Ha a frissítési token érvényesíthető, ezek az információk segítségével ellenőrizze a visszavont tanúsítványok.

Ajánlott eljárásként a szervezete az AD FS hibalapok frissítenie kell a következő információkat:

* A Microsoft Authenticator telepíti az Android vonatkozó követelmény.
* Útmutatás felhasználói tanúsítvány beszerzésére.

További információkért lásd: [az AD FS bejelentkezési oldalainak testreszabása](https://technet.microsoft.com/library/dn280950.aspx).

Néhány Office-alkalmazások (a modern hitelesítést) küldése "*kérdezzen rá a bejelentkezési =*" az Azure AD a kérelemben. Alapértelmezés szerint az Azure AD lefordítja "*kérdezzen rá a bejelentkezési =*"az AD FS, a kérelem"*wauth = usernamepassworduri*" (kéri U/P hitelesítés elvégzéséhez AD FS) és "*wfresh = 0*" (az AD FS kéri figyelmen kívül hagyja az egyszeri bejelentkezési állapotát, és egy friss hitelesítést). Ha szeretné engedélyezni a tanúsítvány alapú hitelesítést ezekhez az alkalmazásokhoz, módosítania az Azure AD alapértelmezés. Állítsa be a "*PromptLoginBehavior*"az összevont tartományt beállításait úgy, hogy"*letiltott*".
Használhatja a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmag, ez a feladat végrehajtásához:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatását.

Egyes Exchange ActiveSync-alkalmazások Android 5.0 (Egyszerű fejrészalakzat) vagy újabb rendszeren támogatott. Határozza meg, ha az e-mail alkalmazás támogatja ezt a szolgáltatást, lépjen kapcsolatba az alkalmazás fejlesztőjének.

## <a name="next-steps"></a>További lépések

Ha Tanúsítványalapú hitelesítés konfigurálása a környezetben, tekintse meg a [első lépések az Android-alapú hitelesítés](active-directory-certificate-based-authentication-get-started.md) utasításokat.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
