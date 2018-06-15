---
title: Az Azure AD SAML protokoll referenciája |} Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Active Directoryban egyszeri bejelentkezéshez, és egyetlen Sign-Out SAML-profil.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.openlocfilehash: 1fcc854362428d4ae32e815ccbf611f4562399ad
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26615775"
---
# <a name="how-azure-active-directory-uses-the-saml-protocol"></a>Hogyan használja az Azure Active Directory a az SAML protokoll
Az Azure Active Directory (Azure AD) használja a SAML 2.0 protokoll egy egyszeri bejelentkezéses élményt biztosíthat a felhasználók alkalmazások esetén. A [egyszeri bejelentkezés](active-directory-single-sign-on-protocol-reference.md) és [kijelentkezési egyetlen](active-directory-single-sign-out-protocol-reference.md) SAML-profilok az Azure AD azt ismertetik, hogyan SAML helyességi feltételek, protokollok és kötések fogja használni az identitás-szolgáltató szolgáltatás.

SAML protokoll megköveteli az identitásszolgáltató (az Azure AD) és a szolgáltató (az alkalmazás) maguk vonatkozó adatokat.

Egy alkalmazás regisztrálása az Azure ad-val esetén az alkalmazás fejlesztőjének összevonási kapcsolatos információk az Azure ad-val regisztrálja. Ez magában foglalja a **átirányítási URI-** az alkalmazás.

Az Azure Active Directory bérlői-specifikus és közös (bérlői független) egy bejelentkezés és az egyetlen kijelentkezési végpontok mutatja. URL-címmel rendelkező helyek képviselő – nincsenek csak egy azonosítók--, nyissa meg a végpont metaadatainak olvasásához.

* A bérlő-specifikus végpont itt található: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.  A <TenantDomainName> helyőrző egy regisztrált tartománynév vagy a TenantID GUID Azonosítóját az Azure AD-bérlő. Például az összevonási metaadatok a contoso.com bérlő jelenleg: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlői független végpont itt található: `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. A végpont címét **közös** megjelenik, helyett egy bérlői tartomány neve vagy azonosítója.

Az Azure AD-közzétevő összevonási metaadatok dokumentumok kapcsolatos információkért lásd: [összevonási metaadatok](active-directory-federation-metadata.md).
