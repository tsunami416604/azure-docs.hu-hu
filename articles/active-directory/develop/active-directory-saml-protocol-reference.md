---
title: Hogyan használja az Azure AD az SAML protokollt | Microsoft Docs
description: Ez a cikk áttekintést nyújt az egyszeri bejelentkezés és az egyszeri kijelentkezés SAML-profiljairól Azure Active Directory.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88125cfc-45c1-448b-9903-a629d8f31b01
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c53cb7ee7bc76b07059d68962c606cdcf866196
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835269"
---
# <a name="how-azure-ad-uses-the-saml-protocol"></a>Hogyan használja az Azure AD a SAML-protokollt

Azure Active Directory (Azure AD) az SAML 2,0 protokollt használja, hogy az alkalmazások egyszeri bejelentkezéses felhasználói élményt nyújtsanak a felhasználóknak. Az Azure AD [egyszeri bejelentkezési](single-sign-on-saml-protocol.md) és [egyszeri](single-sign-out-saml-protocol.md) kijelentkezési SAML-profiljai elmagyarázzák, hogyan használják az SAML-jogcímeket, protokollokat és kötéseket az Identity Provider szolgáltatásban.

Az SAML protokoll megköveteli az Identitáskezelés (Azure AD) és a szolgáltató (az alkalmazás) számára, hogy saját magukról cseréljen információkat.

Ha egy alkalmazás regisztrálva van az Azure AD-ben, az alkalmazás fejlesztője regisztrálja az összevonási szolgáltatással kapcsolatos információkat az Azure AD-vel. Ez az információ magában foglalja az alkalmazás **átirányítási URI** -ját és **metaadatait** .

Az Azure AD a Cloud Service **metaadat-URI-ját** használja az aláíró kulcs és a kijelentkezési URI lekéréséhez. Az ügyfél megnyithatja az alkalmazást az **Azure ad-> alkalmazás-regisztrációban** , majd a **Beállítások-> tulajdonságaiban**a kijelentkezési URL-címet is frissítheti. Így az Azure AD elküldi a választ a megfelelő URL-címre. 

Azure Active Directory a bérlő-specifikus és a közös (bérlői független) egyszeri bejelentkezéses és egyszeri kijelentkezési végpontokat teszi elérhetővé. Ezek az URL-címek címezhető helyet jelölnek – nem csupán azonosítók –, így a végpontra léphet a metaadatok olvasásához.

* A bérlő-specifikus végpont a következő helyen `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`található:. *A\<bérlőtartományneve >* helyőrző egy Azure ad-bérlő regisztrált tartománynevét vagy TenantID GUID azonosítóját jelöli. Az contoso.com-bérlő összevonási metaadatai például a következő helyen találhatóak: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlőtől független végpont a következő helyen `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`található:. Ebben a végpontban az **általános** név a bérlői tartománynév vagy azonosító helyett jelenik meg.

Az Azure AD által közzétett összevonási metaadatokat tartalmazó dokumentumokkal kapcsolatos információkért lásd: [összevonási metaadatok](azure-ad-federation-metadata.md).
