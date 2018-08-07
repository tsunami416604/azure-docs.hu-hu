---
title: Az Azure AD SAML-protokoll referenciája |} A Microsoft Docs
description: Ez a cikk az Azure Active Directoryban az egyszeri bejelentkezést és egyszeri kijelentkezéses SAML profilok áttekintést nyújt.
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
ms.openlocfilehash: 067924294838459c866a0603ab092d139f1e6331
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579231"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Hogyan használja az Azure Active Directory a SAML-protokoll
Az Azure Active Directory (Azure AD) használja a SAML 2.0 protokoll lehetővé teszik az alkalmazások egyszeri bejelentkezési felhasználói élményt nyújtson lehetőséget a felhasználók számára. A [egyszeri bejelentkezés](single-sign-on-saml-protocol.md) és [egyszeri kijelentkezéshez](single-sign-out-saml-protocol.md) az Azure AD SAML-profilok ismertetik a SAML helyességi feltételek, a protokollok és kötések használata az identity provider szolgáltatásban.

SAML-protokoll szükséges az identitásszolgáltató (az Azure AD) és a szolgáltató (az alkalmazás) maguk vonatkozó adatokat.

Amikor egy alkalmazás regisztrálása az Azure AD a alkalmazásfejlesztő összevonási szolgáltatással kapcsolatos információkat az Azure ad-ben regisztrálja. Ezen információk közé tartozik a **átirányítási URI-t** és **metaadatok URI** az alkalmazás.

Az Azure AD a felhőalapú szolgáltatást használ **metaadatok URI** lekérni az aláíró kulcs és a kijelentkezési URI-t. Ha az alkalmazás nem támogatja a metaadatok URI-t, a fejlesztőnek kapcsolatba kell lépnie a Microsoft támogatást biztosít a kijelentkezési URI és aláírási kulcs.

Az Azure Active Directory bérlőspecifikus és közös (bérlő független) egyetlen bejelentkezéshez és az egyszeri kijelentkezési végpont tesz elérhetővé. URL-címmel rendelkező helyek képviselő – azok nem csupán azonosítók--, nyissa meg a végpont metaadatainak olvasásához.

* A bérlő-specifikus végpont nem található: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. A *<TenantDomainName>* helyőrző regisztrált tartománynévvel vagy Azure AD-bérlő TenantID GUID-Azonosítóját jelöli. Ha például a contoso.com bérlőt összevonási metaadatait jelenleg: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlő független végpont nem található: `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. A végpont címe a **közös** jelenik meg ahelyett, hogy a bérlői tartomány neve vagy azonosítója.

Az Azure AD-közzétevő összevonási metaadatok dokumentumok kapcsolatos információkért lásd: [összevonási metaadatok](azure-ad-federation-metadata.md).
