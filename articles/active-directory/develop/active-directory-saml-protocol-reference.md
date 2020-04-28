---
title: Hogyan használja az Azure AD a SAML-protokollt
description: Ez a cikk áttekintést nyújt az egyszeri bejelentkezés és az egyszeri kijelentkezés SAML-profiljairól Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885650"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hogyan használja az Azure AD a SAML-protokollt

Azure Active Directory (Azure AD) az SAML 2,0 protokollt használja, hogy az alkalmazások egyszeri bejelentkezéses felhasználói élményt nyújtsanak a felhasználóknak. Az Azure AD egyszeri bejelentkezési és [egyszeri kijelentkezési](single-sign-out-saml-protocol.md) SAML-profiljai elmagyarázzák, hogyan használják az SAML- [jogcímeket](single-sign-on-saml-protocol.md) , protokollokat és kötéseket az Identity Provider szolgáltatásban.

Az SAML protokoll megköveteli az Identitáskezelés (Azure AD) és a szolgáltató (az alkalmazás) számára, hogy saját magukról cseréljen információkat.

Ha egy alkalmazás regisztrálva van az Azure AD-ben, az alkalmazás fejlesztője regisztrálja az összevonási szolgáltatással kapcsolatos információkat az Azure AD-vel. Ez az információ magában foglalja az alkalmazás **átirányítási URI** -ját és **metaadatait** .

Az Azure AD a Cloud Service **metaadat-URI-ját** használja az aláíró kulcs és a kijelentkezési URI lekéréséhez. Az ügyfél megnyithatja az alkalmazást az **Azure ad-> alkalmazás-regisztrációban** , majd a **Beállítások-> tulajdonságaiban**a kijelentkezési URL-címet is frissítheti. Így az Azure AD elküldi a választ a megfelelő URL-címre. 

Azure Active Directory a bérlő-specifikus és a közös (bérlői független) egyszeri bejelentkezéses és egyszeri kijelentkezési végpontokat teszi elérhetővé. Ezek az URL-címek címezhető helyet jelölnek – nem csupán azonosítók –, így a végpontra léphet a metaadatok olvasásához.

* A bérlő-specifikus végpont a következő helyen `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`található:. A * \<bérlőtartományneve>* helyőrző egy Azure ad-bérlő regisztrált tartománynevét vagy TenantID GUID azonosítóját jelöli. Az contoso.com-bérlő összevonási metaadatai például a következő helyen találhatóak:https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlőtől független végpont a következő helyen `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`található:. Ebben a végpontban az **általános** név a bérlői tartománynév vagy azonosító helyett jelenik meg.

Az Azure AD által közzétett összevonási metaadatokat tartalmazó dokumentumokkal kapcsolatos információkért lásd: [összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md).
