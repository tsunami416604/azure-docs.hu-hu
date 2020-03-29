---
title: Tanúsítványalapú hitelesítés iOS rendszeren – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és a tanúsítványalapú hitelesítés konfigurálásának követelményeit az iOS-eszközökkel rendelkező megoldásokban
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f9e7d71ab660c4df6f65d6bebe1d3854086bdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848799"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory tanúsítványalapú hitelesítés iOS rendszeren

Az iOS-eszközök tanúsítványalapú hitelesítéssel (CBA) hitelesíthetik magukat az Azure Active Directoryban az eszközükön lévő ügyféltanúsítvány használatával, amikor a következőkhöz csatlakoznak:

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

## <a name="requirements"></a>Követelmények

A készülék operációs rendszerének iOS 9 és újabb verziónak kell lennie

Az összevonási kiszolgálót konfigurálni kell.

Az iOS-es Office-alkalmazásokhoz Microsoft Authenticator szükséges.

Ahhoz, hogy az Azure Active Directory visszavonjon egy ügyféltanúsítványt, az ADFS-jogkivonatnak a következő jogcímekkel kell rendelkeznie:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(Az ügyféltanúsítvány sorozatszáma)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(Az ügyféltanúsítvány kibocsátójának karakterlánca)

Az Azure Active Directory hozzáadja ezeket a jogcímeket a frissítési jogkivonathoz, ha azok elérhetők az ADFS-jogkivonatban (vagy bármely más SAML-jogkivonatban). Ha a frissítési jogkivonatot érvényesíteni kell, ezt az információt használja a visszavonás ellenőrzése.

Ajánlott eljárásként frissítenie kell a szervezet ADFS-hibalapjait a következő információkkal:

* A Microsoft Hitelesítő iOS-re történő telepítésének követelménye
* Útmutató a felhasználói tanúsítvány bekésezéséhez.

További információt [az AD FS bejelentkezési lapok testreszabása című témakörben talál.](https://technet.microsoft.com/library/dn280950.aspx)

Egyes Office-alkalmazások (modern hitelesítés engedélyezve) kérésükben elküldik a '*prompt=login ' (prompt=login'* üzenetet az Azure AD-nek. Alapértelmezés szerint az Azure AD lefordítja a '*prompt=login*' a kérelemben az ADFS-hez "*wauth=usernamepassworduri*' (kéri az ADFS-t, hogy tegye meg az U/P Auth- t) és a '*wfresh=0*' (kéri az ADFS-t, hogy figyelmen kívül hagyja az SSO-állapotot, és végezze el a friss hitelesítést). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítania kell az azure-beli AD alapértelmezett viselkedését. Csak állítsa a "*PromptLoginBehavior*" az összevont tartomány beállításait a '*Disabled*'.
Az [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmag segítségével hajthatja végre a feladatot:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Az Exchange ActiveSync-ügyfelek támogatása

IOS 9 vagy újabb rendszeren a natív iOS levelezőprogram támogatott. Az összes többi Exchange ActiveSync-alkalmazás esetén, ha meg szeretné állapítani, hogy ez a szolgáltatás támogatott-e, forduljon az alkalmazás fejlesztőjéhez.

## <a name="next-steps"></a>További lépések

Ha a környezetében tanúsítványalapú hitelesítést szeretne konfigurálni, olvassa el az [Útmutató az Android tanúsítványalapú hitelesítéshez](../authentication/active-directory-certificate-based-authentication-get-started.md) című témakört.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
