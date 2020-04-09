---
title: Hogyan használja az Azure AD a SAML-protokollt
description: Ez a cikk áttekintést nyújt az egyszeri bejelentkezési és egyszeri kijelentkezési SAML-profilok az Azure Active Directoryban.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 44d06030d8015d2df9499ce903eb9cb06e1ef27a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885650"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hogyan használja az Azure AD a SAML-protokollt

Az Azure Active Directory (Azure AD) az SAML 2.0 protokoll használatával teszi lehetővé az alkalmazások számára, hogy egyetlen bejelentkezési élményt biztosítsanak a felhasználók számára. Az Azure AD egyszeri bejelentkezési és [egyszeri kijelentkezési SAML-profiljai](single-sign-out-saml-protocol.md) ismertetik, hogyan használják az SAML-állításokat, protokollokat és kötéseket az identitásszolgáltató szolgáltatásában. [Single Sign-On](single-sign-on-saml-protocol.md)

SAML protokoll megköveteli az identitásszolgáltató (Azure AD) és a szolgáltató (az alkalmazás) információt cserélnek magukról.

Ha egy alkalmazás regisztrálva van az Azure AD-vel, az alkalmazás fejlesztője regisztrálja az összevonással kapcsolatos információkat az Azure AD-vel. Ez az információ tartalmazza az alkalmazás **átirányítási URI-** és **metaadat-URI-ját.**

Az Azure AD a felhőszolgáltatás **metaadat-URI-ját** használja az aláíró kulcs és a kijelentkezésURI lekéréséhez. Az ügyfél megnyithatja az alkalmazást az **Azure AD -> app regisztráció,** majd a **Beállítások -> tulajdonságok**, frissíthetik a kijelentkezési URL-címet. Ily módon az Azure AD küldhet a választ a megfelelő URL-címre. 

Az Azure Active Directory bérlő-specifikus és gyakori (bérlő-független) egyszeri bejelentkezési és egyszeri kijelentkezési végpontokat. Ezek az URL-címek címezhető helyeket jelölnek – ezek nem csak azonosítók –, így a végpontra léphet a metaadatok olvasásához.

* A bérlő-specifikus végpont a `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`helyen található. A * \<TenantDomainName>* helyőrző egy regisztrált tartománynevet vagy egy Azure AD-bérlő bérlőazonosító guid-ját jelöli. Például a contoso.com bérlő összevonási metaadatai a következő helyen vannak:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlőtől független végpont `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`a helyen található. Ebben a végpontcímben **a leggyakoribb** a bérlői tartománynév vagy -azonosító helyett jelenik meg.

Az Azure AD által közzétett összevonási metaadat-dokumentumokról az [Összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md)című témakörben talál.
