---
title: Az alkalmazás egyszeri bejelentkezése | Microsoft dokumentumok
description: Egyszeri bejelentkezés konfigurálása egy egyéni alkalmazáshoz, amelyet fejleszt és regisztrál az Azure AD-vel.
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
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702641"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Egyszeri bejelentkezés konfigurálása egy alkalmazáshoz

Az összevont egyszeri bejelentkezés (SSO) engedélyezése az alkalmazásban automatikusan engedélyezve van, amikor az Azure AD-n keresztül id for OpenID Connect, SAML 2.0 vagy WS-Fed. Ha a végfelhasználóknak annak ellenére kell bejelentkezniük, hogy már rendelkeznek egy meglévő munkamenettel az Azure AD-vel, valószínű, hogy az alkalmazás helytelenül van konfigurálva.

* Ha ADAL/MSAL-t használ, győződjön meg arról, hogy a **PromptBehavior** beállítása **automatikus,** nem pedig **mindig.**

* Ha mobilalkalmazást hoz, további konfigurációkra lehet szüksége a közvetített vagy nem felügyelt egyszeri szolgáltatások engedélyezéséhez.

Android esetén [lásd: Cross App SSO engedélyezése Android ban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

iOS esetén [lásd: Cross App SSO engedélyezése iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)rendszerben.

## <a name="next-steps"></a>További lépések

[Azure AD-sso](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[A Cross App SSO engedélyezése Androidban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[A cross app sso engedélyezése iOS rendszerben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Alkalmazások integrálása az AzureAD-ba](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Hozzájárulás és engedélyezés az AzureAD v2.0-s konvergens alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD-veremtúlcsordulás](https://stackoverflow.com/questions/tagged/azure-active-directory)
