---
title: Microsoft Enterprise Egyszeri bejelentkezés Apple-eszközökhöz
titleSuffix: Microsoft identity platform | Azure
description: Ismerje meg a Microsoft Azure Active Directory egyszeri bejelentkezést iOS és macOS eszközökhöz.
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
ms.openlocfilehash: 7233a0bba5cf30eab018bfa744a7322303300604
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550317"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise Egyszeri bejelentkezés Apple-eszközökhöz (előzetes verzió)

> [!NOTE]
> Ez a funkció nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az *Apple-eszközök Höz készült Microsoft Enterprise Egyszeri bejelentkezés* egyszeri bejelentkezést (SSO) biztosít az Active Directory-fiókokhoz az Apple enterprise single [sign-on](https://developer.apple.com/documentation/authenticationservices) funkcióját támogató összes alkalmazásban. A Microsoft szorosan együttműködött az Apple-lel, hogy kifejlessze ezt a beépülő modult, hogy növelje az alkalmazás használhatóságát, miközben a lehető legjobb védelmet nyújtja az Apple és a Microsoft számára.

Ebben a nyilvános előzetes verzióban a vállalati egyszeri bejelentkezés csak iOS-eszközökön érhető el, és bizonyos Microsoft-alkalmazásokban van terjesztve.

Az Enterprise SSO beépülő modul első használata az új [megosztott eszköz mód funkcióval](msal-ios-shared-devices.md) történik.

## <a name="features"></a>Szolgáltatások

Az Apple-eszközök Microsoft Enterprise SSO beépülő modulja a következő előnyökkel jár:

- Egyszeri bejelentkezést biztosít az Active Directory-fiókokhoz az Apple vállalati egyszeri bejelentkezési funkcióját támogató összes alkalmazás számára.
- Automatikusan megjelenik a Microsoft Hitelesítőben, és bármely mobileszköz-felügyeleti (MDM) megoldás sal engedélyezhető.

## <a name="requirements"></a>Követelmények

A Microsoft Enterprise Egyszeri bejelentkezés beépülő modul használata Apple-eszközökhöz:

- Az iOS 13.0-s vagy újabb verziót telepíteni kell az eszközre.
- Az eszközre telepíteni kell egy Microsoft-alkalmazást, amely az Apple-eszközök Microsoft Enterprise SSO beépülő modulját biztosítja. A Nyilvános előzetes verzió ban ezek az alkalmazások közé tartozik a [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md).
- Az eszköznek MDM-en kell lennie (például a Microsoft Intune-nal).
- A konfigurációt le kell nyomni az eszközre, hogy engedélyezze a Microsoft Enterprise Egyszeri bejelentkezést az Apple-eszközökhöz az eszközön. Erre a biztonsági megkötésre az Apple-nek szüksége van.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Az SSO-bővítmény engedélyezése mobileszköz-felügyelettel (MDM)

Ahhoz, hogy a Microsoft Enterprise Egyszeri bejelentkezés beépülő modult engedélyezhesse az Apple-eszközökhöz, az eszközöknek jelet kell küldeniük egy MDM-szolgáltatáson keresztül. Mivel a Microsoft a [Microsoft Authenticator alkalmazásban](..//user-help/user-help-auth-app-overview.md)tartalmazza a vállalati egyszeri bejelentkezés beépülő modult, az MDM segítségével konfigurálja az alkalmazást a Microsoft Enterprise Egyszeri bejelentkezés engedélyezéséhez.

A Microsoft Enterprise Egyszeri bejelentkezés Apple-eszközökhöz való konfigurálásához használja az alábbi paramétereket:

- **Típus**: Átirányítás
- **Mellékazonosító:**`com.microsoft.azureauthenticator.ssoextension`
- **Csapat azonosítója:**`SGGM6D27TK`
- **URL-címek:**
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

A Microsoft Intune mdm-szolgáltatásként való használatával megkönnyítheti a Microsoft Enterprise Egyszeri bejelentkezés modul konfigurálását. További információt az [Intune konfigurációs dokumentációjában](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)talál.

## <a name="using-the-sso-extension-in-your-application"></a>Az SSO kiterjesztés használata az alkalmazásban

A [Microsoft Authentication Library (MSAL) az Apple 1.1.0-s](https://github.com/AzureAD/microsoft-authentication-library-for-objc) vagy újabb verziójú applei eszközökhöz támogatja a Microsoft Enterprise Egyszeri bejelentkezést az Apple-eszközökhöz.

Ha támogatni szeretné az Apple-eszközök Microsoft Enterprise SSO beépülő modulja által biztosított megosztott eszközmódot, győződjön meg arról, hogy az alkalmazások az MSAL meghatározott minimálisan szükséges verzióját használják.

## <a name="next-steps"></a>További lépések

Az iOS megosztott eszközmódjáról az [iOS-eszközök megosztott eszközüzemmódja](msal-ios-shared-devices.md)című témakörben talál további információt.
