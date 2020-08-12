---
title: Alkalmazás egyszeri bejelentkezésének konfigurálása
description: Az egyszeri bejelentkezés konfigurálása egy olyan egyéni alkalmazáshoz, amelyet Ön fejleszt és regisztrál az Azure AD-ben.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 465adbb71abaa45160399ecba2ebfb692a8307c2
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120677"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz

Az összevont egyszeri bejelentkezés (SSO) engedélyezése az alkalmazásban automatikusan engedélyezve van, ha az egyesítő az OpenID Connect, az SAML 2,0 vagy a WS-fed használatával végzi az Azure AD-t. Ha a végfelhasználóknak be kell jelentkezniük annak ellenére, hogy már rendelkezik egy meglévő munkamenettel az Azure AD-vel, előfordulhat, hogy az alkalmazás hibásan van konfigurálva.

* Ha ADAL/MSAL használ, győződjön meg arról, hogy a **PromptBehavior** beállítása **automatikus** , és nem **mindig**.

* Ha Mobile-alkalmazást készít, további konfigurációkra lehet szükség a közvetített vagy nem felügyelt egyszeri bejelentkezés engedélyezéséhez.

Android esetén lásd: a [Cross app SSO engedélyezése Androidon](../azuread-dev/howto-v1-enable-sso-android.md).<br>

IOS esetén lásd: a [Cross app SSO engedélyezése iOS-ben](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>További lépések

[Azure AD SSO](../manage-apps/what-is-single-sign-on.md)<br>

[A Cross app SSO engedélyezése Androidon](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[A Cross app SSO engedélyezése iOS-ben](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Alkalmazások integrálása a AzureAD-be](./quickstart-register-app.md)<br>

[Engedélyek és hozzájárulás a Microsoft-identitásplatform végpontján](./v2-permissions-and-consent.md)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)