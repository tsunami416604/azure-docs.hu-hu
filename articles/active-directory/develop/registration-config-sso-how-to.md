---
title: Az alkalmazás egyszeri bejelentkezése
description: Egyszeri bejelentkezés konfigurálása egy egyéni alkalmazáshoz, amelyet fejleszt és regisztrál az Azure AD-vel.
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
ms.openlocfilehash: 630be1676d73410f1b0d376b163a8599299ddbe2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883151"
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
