---
title: Hogyan használja a Microsoft Identity platform az SAML protokollt
description: Ez a cikk áttekintést nyújt az egyszeri bejelentkezés és az egyszeri kijelentkezés SAML-profiljairól Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552798"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Hogyan használja a Microsoft Identity platform az SAML protokollt

A Microsoft Identity platform az SAML 2,0 protokollt használja, amely lehetővé teszi, hogy az alkalmazások egyszeri bejelentkezéses felhasználói élményt nyújtsanak a felhasználóknak. Az Azure AD egyszeri bejelentkezési és [egyszeri kijelentkezési](single-sign-out-saml-protocol.md) SAML-profiljai elmagyarázzák, hogyan használják az SAML- [jogcímeket](single-sign-on-saml-protocol.md) , protokollokat és kötéseket az Identity Provider szolgáltatásban.

Az SAML protokoll megköveteli az Identitáskezelő (Microsoft Identity platform) és a szolgáltató (az alkalmazás) számára a saját magukkal kapcsolatos információk cseréjét.

Ha egy alkalmazás regisztrálva van az Azure AD-ben, az alkalmazás fejlesztője regisztrálja az összevonási szolgáltatással kapcsolatos információkat az Azure AD-vel. Ez az információ magában foglalja az alkalmazás **átirányítási URI** -ját és **metaadatait** .

A Microsoft Identity platform a Cloud Service **metaadat-URI-ját** használja az aláíró kulcs és a kijelentkezési URI lekéréséhez. Az ügyfél megnyithatja az alkalmazást az **Azure ad-> alkalmazás-regisztrációban** , majd a **Beállítások-> tulajdonságaiban**a kijelentkezési URL-címet is frissítheti. Így a Microsoft Identity platform a megfelelő URL-címre küldheti el a választ. 

Azure Active Directory a bérlő-specifikus és a közös (bérlői független) egyszeri bejelentkezéses és egyszeri kijelentkezési végpontokat teszi elérhetővé. Ezek az URL-címek címezhető helyet jelölnek – nem csupán azonosítók –, így a végpontra léphet a metaadatok olvasásához.

* A bérlő-specifikus végpont a következő helyen található: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . A *\<TenantDomainName>* helyőrző egy Azure ad-bérlő regisztrált tartománynevét vagy TenantID GUID azonosítóját jelöli. Az contoso.com-bérlő összevonási metaadatai például a következő helyen találhatóak:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlőtől független végpont a következő helyen található: `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . Ebben a végpontban az **általános** név a bérlői tartománynév vagy azonosító helyett jelenik meg.

Az Azure AD által közzétett összevonási metaadatokat tartalmazó dokumentumokkal kapcsolatos információkért lásd: [összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md).
