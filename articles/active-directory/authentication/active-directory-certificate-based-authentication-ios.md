---
title: IOS – az Azure Active Directory ügyféltanúsítvány-alapú hitelesítés
description: További információ a támogatott forgatókönyvek és megoldások konfigurálásával Tanúsítványalapú hitelesítés követelményeinek az iOS-eszközök
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
ms.openlocfilehash: cda1b1c2a484f3aa627b8b9cf486528d13f27be8
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369428"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>IOS-en az Azure Active Directory Tanúsítványalapú hitelesítés

iOS-eszközök tanúsítványalapú hitelesítést (CBA) használatával hitelesíti az Azure Active Directory ügyféltanúsítvány használatával az eszközén való csatlakozáskor:

* Office-mobilalkalmazások például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync (EAS) típusú ügyfelek

Ez a funkció konfigurálása szükségtelenné meg kell adnia egy felhasználónév és jelszó kombinációjával egyes mail és a Microsoft Office-alkalmazások a mobileszközén.

Ez a témakör a támogatott forgatókönyveket és a követelményeket egy iOS(Android) eszközön a felhasználók számára az Office 365 nagyvállalati verzió, vállalati, oktatási, US Government, Kína bérlők CBA konfigurálásához, és a Németországi csomagok.

Ez a funkció érhető el előzetes verzióban érhető el az Office 365 US Government Defense és szövetségi csomagoknak.

## <a name="microsoft-mobile-applications-support"></a>A Microsoft-mobilalkalmazások támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Az Azure Information Protection alkalmazással |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Az Intune vállalati portál |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Microsoft Teams |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| OneNote |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| OneDrive |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Outlook |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Power BI |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Skype Vállalati verzió |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Word / Excel / PowerPoint |![Az alkalmazás támogatási hangsúlyozva pipa][1] |
| Yammer |![Az alkalmazás támogatási hangsúlyozva pipa][1] |

## <a name="requirements"></a>Követelmények

Az eszköz operációs rendszerének verziója kell lennie az iOS 9-es vagy újabb verzió

Egy összevonási kiszolgálót kell konfigurálni.

A Microsoft Authenticator szükség az Office-alkalmazások IOS-eszközökön.

Az Azure Active Directory ügyféltanúsítvány visszavonása esetén az AD FS jogkivonat a következő jogcímeket kell rendelkeznie:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (A sorozatszám az ügyféltanúsítvány)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Az ügyfél-tanúsítvány kiállítója karakterlánc)

Az Azure Active Directory ezeket a jogcímeket, a frissítési jogkivonatot, hozzáadja az, ha elérhetők a az AD FS jogkivonat (vagy bármely más SAML-jogkivonat). A frissítési jogkivonatot érvényesíteni kell, ha ezek az információk segítségével ellenőrizze a visszavont tanúsítványok.

Ajánlott eljárásként frissítenie kell az AD FS hibalapok a szervezet a következő információkat:

* A követelmény az IOS-eszközökön a Microsoft Authenticator telepítése
* Útmutató a felhasználói tanúsítvány beszerzésének módját.

További információkért lásd: [az AD FS bejelentkezési oldalainak testreszabása](https://technet.microsoft.com/library/dn280950.aspx).

Egyes Office-alkalmazások (a modern hitelesítés engedélyezve) küldése "*kérdezzen rá a bejelentkezési =*" az Azure AD a kérelemben. Alapértelmezés szerint az Azure AD a rendszer lefordítja "*kérdezzen rá a bejelentkezési =*"az AD FS, a kérelem"*wauth = usernamepassworduri*" (kéri, hajtsa végre a felhasználónév/jelszó-hitelesítés AD FS) és "*wfresh = 0*" (az AD FS kéri hagyja figyelmen kívül az egyszeri bejelentkezés állapotát, és hajtsa végre egy friss hitelesítést). Ha szeretné ezeket az alkalmazásokat a Tanúsítványalapú hitelesítés engedélyezése, módosítania az Azure AD alapértelmezés. Állítson be a "*PromptLoginBehavior*"beállításaiban az összevont tartományt"*letiltott*".
Használhatja a [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmagot a feladat végrehajtásához:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync-ügyfelek támogatása

Az iOS 9-es vagy újabb a natív IOS-es mail ügyfél támogatott. Minden Exchange ActiveSync alkalmazások annak megállapításához, hogy ez a funkció támogatott, forduljon az alkalmazás fejlesztője.

## <a name="next-steps"></a>További lépések

Ha azt szeretné, Tanúsítványalapú hitelesítés konfigurálása a környezetben, lásd: [Android rendszeren – Tanúsítványalapú hitelesítés első lépései](../authentication/active-directory-certificate-based-authentication-get-started.md) útmutatást.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
