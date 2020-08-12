---
title: Microsoft Enterprise egyszeri bejelentkezéses beépülő modul Apple-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Ismerkedjen meg az iOS-és macOS-eszközökhöz készült Microsoft Azure Active Directory SSO beépülő modullal.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: f98be2ef6a82c099425655fd7e5d25a4358844bf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115441"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO beépülő modul Apple-eszközökhöz (előzetes verzió)

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az *Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul* egyszeri bejelentkezést (SSO) biztosít a Azure Active Directory (Azure ad) fiókokhoz az Apple [vállalati egyszeri bejelentkezés](https://developer.apple.com/documentation/authenticationservices) funkcióját támogató összes alkalmazásban. A Microsoft az Apple-szel szorosan együttműködve fejleszti ezt a beépülő modult az alkalmazás használhatóságának növeléséhez, miközben az Apple és a Microsoft által biztosított legjobb védelmet nyújtja.

Ebben a nyilvános előzetes kiadásban a vállalati egyszeri bejelentkezési beépülő modul csak iOS-eszközökön érhető el, és bizonyos Microsoft-alkalmazásokban van elterjesztve.

A vállalati egyszeri bejelentkezés beépülő modul első használata az új [megosztott eszköz mód](msal-ios-shared-devices.md) funkció.

## <a name="features"></a>Szolgáltatások

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul a következő előnyöket kínálja:

- EGYSZERI bejelentkezést biztosít az Azure AD-fiókokhoz az Apple vállalati egyszeri bejelentkezés funkcióját támogató összes alkalmazásban.
- A rendszer automatikusan leküldi a Microsoft Authenticator, és bármely mobileszköz-kezelési (MDM) megoldással engedélyezhető.

## <a name="requirements"></a>Követelmények

A Microsoft Enterprise SSO beépülő modul használata Apple-eszközökhöz:

- az iOS 13,0-es vagy újabb verziójának telepítve kell lennie az eszközön.
- Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult biztosító Microsoft-alkalmazásnak telepítve kell lennie az eszközön. A nyilvános előzetes verzióban ezek az alkalmazások a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md)tartalmazzák.
- Az eszköznek MDM kell lennie (például Microsoft Intune).
- A konfigurációt az eszközön kell leküldeni, hogy engedélyezze a Microsoft Enterprise SSO beépülő modult az Apple-eszközökön az eszközön. Ezt a biztonsági korlátozást az Apple igényli.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Egyszeri bejelentkezéses bővítmény engedélyezése a mobileszköz-kezeléssel (MDM)

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul engedélyezéséhez az eszközöknek egy MDM szolgáltatáson keresztül kell eljuttatnia a jeleket. Mivel a Microsoft a [Microsoft Authenticator alkalmazásban](..//user-help/user-help-auth-app-overview.md)tartalmazza a vállalati egyszeri bejelentkezés beépülő modult, a Mdm használatával konfigurálhatja az alkalmazást, hogy engedélyezze a Microsoft vállalati egyszeri bejelentkezés beépülő modulját.

Az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul konfigurálásához használja a következő paramétereket:

- **Típus**: átirányítás
- **BŐVÍTMÉNY azonosítója**:`com.microsoft.azureauthenticator.ssoextension`
- **Csoport azonosítója**:`SGGM6D27TK`
- **URL-címek**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

A MDM szolgáltatással Microsoft Intune használhatja a Microsoft vállalati egyszeri bejelentkezés beépülő moduljának konfigurálását. További információt az [Intune konfigurációs dokumentációjában](/intune/configuration/ios-device-features-settings)talál.

## <a name="using-the-sso-extension-in-your-application"></a>Az SSO-bővítmény használata az alkalmazásban

Az Apple-eszközök 1.1.0-es és újabb verziójának [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) verziója támogatja az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult.

Ha az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modul által biztosított megosztott eszköz módot szeretné támogatni, győződjön meg arról, hogy az alkalmazások a MSAL megadott minimálisan szükséges verzióját használják.

## <a name="next-steps"></a>További lépések

További információ az iOS-es megosztott eszköz üzemmódról: [megosztott eszköz mód iOS-eszközökhöz](msal-ios-shared-devices.md).