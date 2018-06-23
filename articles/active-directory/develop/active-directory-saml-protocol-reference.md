---
title: Az Azure AD SAML protokoll referenciája |} Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Active Directoryban egyszeri bejelentkezéshez, és egyetlen Sign-Out SAML-profil.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock
ms.openlocfilehash: 26984244c0ffa5e665394e903ba95b0487f214a8
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316909"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Hogyan használja az Azure Active Directory a az SAML protokoll
Az Azure Active Directory (Azure AD) használja a SAML 2.0 protokoll egy egyszeri bejelentkezéses élményt biztosíthat a felhasználók alkalmazások esetén. A [egyszeri bejelentkezés](active-directory-single-sign-on-protocol-reference.md) és [kijelentkezési egyetlen](active-directory-single-sign-out-protocol-reference.md) SAML-profilok az Azure AD azt ismertetik, hogyan SAML helyességi feltételek, a protokollok és a kötéseket az identitás-szolgáltató szolgáltatás használatosak.

SAML protokoll megköveteli az identitásszolgáltató (az Azure AD) és a szolgáltató (az alkalmazás) maguk vonatkozó adatokat.

Egy alkalmazás regisztrálása az Azure ad-val esetén az alkalmazás fejlesztőjének összevonási kapcsolatos információk az Azure ad-val regisztrálja. Ezen információk közé tartozik a **átirányítási URI-** és **metaadatok URI** az alkalmazás.

Az Azure AD használja a felhőalapú szolgáltatás **metaadatok URI** az aláírási kulcs és a kijelentkezési URI beolvasása. Ha az alkalmazás nem támogatja a metaadatok URI, a fejlesztőnek kell-e arra, hogy a kijelentkezési URI a Microsoft támogatási szolgálatához, és az aláírási kulcs.

Az Azure Active Directory bérlői-specifikus és közös (bérlői független) egy bejelentkezés és az egyetlen kijelentkezési végpontok mutatja. URL-címmel rendelkező helyek képviselő – nincsenek csak azonosítók--, nyissa meg a végpont metaadatainak olvasásához.

* A bérlő-specifikus végpont itt található: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. A *<TenantDomainName>* helyőrző egy regisztrált tartománynév vagy a TenantID GUID Azonosítóját az Azure AD-bérlő. Például az összevonási metaadatok a contoso.com bérlő jelenleg: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlői független végpont itt található: `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. A végpont címét **közös** jelenik meg ahelyett, hogy a bérlői tartomány neve vagy azonosítója.

Az összevonási metaadatok dokumentumokat az Azure AD-közzétevő kapcsolatos információkért lásd: [összevonási metaadatok](active-directory-federation-metadata.md).
