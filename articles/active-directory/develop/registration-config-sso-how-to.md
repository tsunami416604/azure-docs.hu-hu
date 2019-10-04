---
title: Az alkalmazás egyszeri bejelentkezésének konfigurálása | Microsoft Docs
description: Az egyszeri bejelentkezés konfigurálása egy olyan egyéni alkalmazáshoz, amelyet Ön fejleszt és regisztrál az Azure AD-ben.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320960"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz

Az összevont egyszeri bejelentkezés (SSO) engedélyezése az alkalmazásban automatikusan engedélyezve van, ha az egyesítő az OpenID Connect, az SAML 2,0 vagy a WS-fed használatával végzi az Azure AD-t. Ha a végfelhasználóknak be kell jelentkezniük annak ellenére, hogy már rendelkezik egy meglévő munkamenettel az Azure AD-vel, előfordulhat, hogy az alkalmazás hibásan van konfigurálva.

* Ha ADAL/MSAL használ, győződjön meg arról, hogy a **PromptBehavior** beállítása **automatikus** , és nem **mindig**.

* Ha Mobile-alkalmazást készít, további konfigurációkra lehet szükség a közvetített vagy nem felügyelt egyszeri bejelentkezés engedélyezéséhez.

Android esetén lásd: a [Cross app SSO engedélyezése Androidon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

IOS esetén lásd: a [Cross app SSO engedélyezése iOS-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>További lépések

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[A Cross app SSO engedélyezése Androidon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[A Cross app SSO engedélyezése iOS-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Alkalmazások integrálása a AzureAD-be](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[A AzureAD 2.0-val konvergált alkalmazások beleegyezése és engedélyezése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
