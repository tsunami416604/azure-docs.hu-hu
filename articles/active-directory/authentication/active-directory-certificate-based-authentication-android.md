---
title: Android tanúsítványalapú hitelesítés – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és a tanúsítványalapú hitelesítés konfigurálásának követelményeit az Android-eszközökkel rendelkező megoldásokban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e0427398d05cbe9f76249ec8f7c25568d566d5d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654381"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory tanúsítványalapú hitelesítés Androidon

Az Android-eszközök tanúsítványalapú hitelesítés (CBA) segítségével hitelesíthetik magukat az Azure Active Directory ban az eszközükön lévő ügyféltanúsítvány használatával, amikor a következőkhöz csatlakoznak:

* Office mobilalkalmazások, például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync (EAS) ügyfelek

A szolgáltatás konfigurálásával nincs szükség felhasználónév és jelszó kombináció megadására bizonyos levelezési és Microsoft Office-alkalmazásokban a mobileszközön.

Ez a témakör az Office 365 Enterprise, Business, Education, US Government, China és Germany csomagok bérlőinek felhasználói számára iOS(Android) eszközön a CBA konfigurálásának követelményeit és támogatott forgatókönyveit tartalmazza.

Ez a funkció előzetes verzióban érhető el az Office 365-beli egyesült államokbeli kormányzati védelmi és szövetségi csomagokban.

## <a name="microsoft-mobile-applications-support"></a>A Microsoft mobilalkalmazás-támogatása

| Alkalmazások | Támogatás |
| --- | --- |
| Azure Information Protection alkalmazás |![Az alkalmazás támogatásának jelzése][1] |
| Intune Vállalati portál |![Az alkalmazás támogatásának jelzése][1] |
| Microsoft Teams |![Az alkalmazás támogatásának jelzése][1] |
| OneNote |![Az alkalmazás támogatásának jelzése][1] |
| OneDrive |![Az alkalmazás támogatásának jelzése][1] |
| Outlook |![Az alkalmazás támogatásának jelzése][1] |
| Power BI |![Az alkalmazás támogatásának jelzése][1] |
| Skype Vállalati verzió |![Az alkalmazás támogatásának jelzése][1] |
| Word / Excel / PowerPoint |![Az alkalmazás támogatásának jelzése][1] |
| Yammer |![Az alkalmazás támogatásának jelzése][1] |

### <a name="implementation-requirements"></a>Végrehajtási követelmények

A készülék operációs rendszerének Android 5.0 (Lollipop) és újabb verziónak kell lennie.

Az összevonási kiszolgálót konfigurálni kell.

Ahhoz, hogy az Azure Active Directory visszavonjon egy ügyféltanúsítványt, az ADFS-jogkivonatnak a következő jogcímekkel kell rendelkeznie:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Az ügyféltanúsítvány sorozatszáma)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Az ügyféltanúsítvány kibocsátójának karakterlánca)

Az Azure Active Directory hozzáadja ezeket a jogcímeket a frissítési jogkivonathoz, ha azok elérhetők az ADFS-jogkivonatban (vagy bármely más SAML-jogkivonatban). Ha a frissítési jogkivonatot érvényesíteni kell, ezt az információt használja a visszavonás ellenőrzése.

Ajánlott eljárásként frissítenie kell a szervezet ADFS-hibalapjait a következő információkkal:

* A Microsoft Hitelesítő Android-alapú telepítésének követelménye.
* Útmutató a felhasználói tanúsítvány bekésezéséhez.

További információt [az AD FS bejelentkezési lapok testreszabása című témakörben talál.](https://technet.microsoft.com/library/dn280950.aspx)

Egyes Office-alkalmazások (modern hitelesítés engedélyezve) kérésükben elküldik a '*prompt=login ' (prompt=login'* üzenetet az Azure AD-nek. Alapértelmezés szerint az Azure AD lefordítja a '*prompt=login*' a kérelemben az ADFS-hez "*wauth=usernamepassworduri*' (kéri az ADFS-t, hogy tegye meg az U/P Auth- t) és a '*wfresh=0*' (kéri az ADFS-t, hogy figyelmen kívül hagyja az SSO-állapotot, és végezze el a friss hitelesítést). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítania kell az azure-beli AD alapértelmezett viselkedését. Állítsa a '*PromptLoginBehavior*' beállítást az összevont tartomány beállításaiban a '*Letiltva*' értékre.
Az [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmag segítségével hajthatja végre a feladatot:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Az Exchange ActiveSync-ügyfelek támogatása

Bizonyos Exchange ActiveSync alkalmazások Android 5.0 (Lollipop) vagy újabb rendszeren támogatottak. Annak megállapításához, hogy a levelezőalkalmazás támogatja-e ezt a funkciót, forduljon az alkalmazás fejlesztőjéhez.

## <a name="next-steps"></a>További lépések

Ha a környezetében tanúsítványalapú hitelesítést szeretne konfigurálni, olvassa el az [Útmutató az Android tanúsítványalapú hitelesítéshez](active-directory-certificate-based-authentication-get-started.md) című témakört.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
