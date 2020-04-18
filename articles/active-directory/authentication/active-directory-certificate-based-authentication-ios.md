---
title: Tanúsítványalapú hitelesítés iOS rendszeren – Azure Active Directory
description: Ismerje meg a támogatott forgatókönyveket és az Azure Active Directory tanúsítványalapú hitelesítésének iOS-eszközökkel rendelkező megoldásokban történő konfigurálásához szükséges követelményeket
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639631"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory tanúsítványalapú hitelesítés iOS rendszeren

A biztonság növelése érdekében az iOS-eszközök tanúsítványalapú hitelesítéssel (CBA) hitelesíthetik magukat az Azure Active Directory (Azure AD) szolgáltatásban az eszközükön lévő ügyféltanúsítvány használatával, amikor a következő alkalmazásokhoz vagy szolgáltatásokhoz csatlakoznak:

* Office mobilalkalmazások, például a Microsoft Outlook és a Microsoft Word
* Exchange ActiveSync (EAS) ügyfelek

A tanúsítványok használata szükségtelenné teszi, hogy felhasználónevet és jelszót adjon meg bizonyos levelezési és Microsoft Office-alkalmazásokban a mobileszközén.

Ez a cikk ismerteti a cba iOS-eszközön történő konfigurálásának követelményeit és támogatott forgatókönyveit. Az iOS-hez készült CBA az Azure nyilvános felhőiben, a Microsoft Government Cloudban, a Microsoft Cloud Germany-ben és a Microsoft Azure China 21Vianet-en érhető el.

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

A CBA iOS-szel való használatához a következő követelmények és szempontok érvényesek:

* A készülék operációs rendszerének iOS 9 vagy újabb verziónak kell lennie.
* Az iOS-es Office-alkalmazásokhoz Microsoft Authenticator szükséges.
* Létre kell hozni egy identitás-beállítást a macOS-kulcskarikában, amely tartalmazza az ADFS-kiszolgáló hitelesítési URL-címét. További információt a [Identitásbeállítás létrehozása a Mac-es kulcskarikásban című témakörben talál.](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)

Az Active Directory összevonási szolgáltatások (ADFS) alábbi követelményei és szempontjai érvényesek:

* Az ADFS-kiszolgálót engedélyezni kell a tanúsítványhitelesítéshez, és összevont hitelesítést kell használni.
* A tanúsítványnak bővített kulcshasználatot (EKU) kell használnia, és a felhasználó egyszerű felhasználónevét kell tartalmaznia a *Tulajdonos alternatív nevében (NT egyszerű név)*.

## <a name="configure-adfs"></a>A DFS konfigurálása

Ahhoz, hogy az Azure AD visszavonjon egy ügyféltanúsítványt, az ADFS-jogkivonatnak a következő jogcímekkel kell rendelkeznie. Az Azure AD hozzáadja ezeket a jogcímeket a frissítési jogkivonathoz, ha azok elérhetők az ADFS-jogkivonatban (vagy bármely más SAML-jogkivonatban). Ha a frissítési jogkivonatot érvényesíteni kell, ezt az információt használja a visszavonás ellenőrzése:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- adja hozzá az ügyféltanúsítvány sorozatszámát
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- adja hozzá a karakterláncot a kibocsátó az ügyfél tanúsítvány

Ajánlott eljárásként a szervezet ADFS-hibalapjait is frissítenie kell a következő információkkal:

* A Microsoft Authenticator iOS rendszerre történő telepítésének követelménye.
* Útmutató a felhasználói tanúsítvány bekésezéséhez.

További információt [az AD FS bejelentkezési lapjának testreszabása című témakörben](https://technet.microsoft.com/library/dn280950.aspx)talál.

## <a name="use-modern-authentication-with-office-apps"></a>Modern hitelesítés használata az Office-appokkal

Egyes, modern hitelesítéssel `prompt=login` rendelkező Office-alkalmazások kérésben küldhetnek az Azure AD-nek. Alapértelmezés szerint az Azure `prompt=login` AD fordítja a `wauth=usernamepassworduri` kérelemben az ADFS-hez (kéri `wfresh=0` az ADFS-t, hogy tegye meg az U/P Auth) és (kéri az ADFS figyelmen kívül hagyja az SSO-állapot, és egy friss hitelesítést). Ha engedélyezni szeretné a tanúsítványalapú hitelesítést ezekhez az alkalmazásokhoz, módosítsa az azure-beli AD alapértelmezett viselkedését.

Az alapértelmezett viselkedés frissítéséhez állítsa a '*PromptLoginBehavior*' parancsot az összevont tartomány beállításaiban *Letiltva*értékre. Az [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) parancsmag segítségével végrehajthatja ezt a feladatot, ahogy az a következő példában látható:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Az Exchange ActiveSync-ügyfelek támogatása

IOS 9 vagy újabb rendszeren a natív iOS levelezőprogram támogatott. Annak megállapításához, hogy ez a szolgáltatás az összes többi Exchange ActiveSync-alkalmazás számára támogatott-e, forduljon az alkalmazás fejlesztőjéhez.

## <a name="next-steps"></a>További lépések

A tanúsítványalapú hitelesítés konfigurálásához a környezetben, olvassa el a [Tanúsítványalapú hitelesítés első lépéseit.](active-directory-certificate-based-authentication-get-started.md)

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
