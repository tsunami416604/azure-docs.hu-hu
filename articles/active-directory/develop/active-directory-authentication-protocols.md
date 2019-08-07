---
title: Azure Active Directory hitelesítési protokollok | Microsoft Docs
description: A Azure Active Directory (AD) által támogatott hitelesítési protokollok áttekintése
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f292d4804adf7e1a58e5c2097f689aac182ff783
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835491"
---
# <a name="azure-active-directory-authentication-protocols"></a>Hitelesítési protokollok Azure Active Directory
A Azure Active Directory (Azure AD) számos, a legszélesebb körben használt hitelesítési és engedélyezési protokollt támogat. A szakasz témakörei a támogatott protokollokat és azok megvalósítását írják le az Azure AD-ben. A témakörökbe beletartozik a támogatott jogcím-típusok áttekintése, az összevonási metaadatok használatának bemutatása, részletes OAuth 2,0. és a SAML 2,0 protokoll dokumentációját, valamint egy hibaelhárítási szakaszt.

## <a name="authentication-protocols-articles-and-reference"></a>A hitelesítési protokollok cikkei és referenciái
* [Fontos információk az Azure ad](active-directory-signing-key-rollover.md) -beli aláírási kulcs átváltásáról – Ismerje meg az Azure ad aláírási kulcsának átváltási ritmusát, a kulcs automatikus frissítését, valamint a leggyakoribb alkalmazás-forgatókönyvek frissítését ismertető témakört.
* [Támogatott jogkivonatok és](v1-id-and-access-tokens.md) jogcímek – Ismerje meg az Azure ad által felmerülő jogkivonatokban lévő jogcímeket.
* [Összevonási metaadatok](azure-ad-federation-metadata.md) – megismerheti az Azure ad által generált metaadatokat tartalmazó dokumentumok megkeresését és értelmezését.
* [OAuth 2,0 az Azure ad-ben](v1-protocols-oauth-code.md) – Ismerje meg a OAuth 2,0 megvalósítását az Azure ad-ben.
* [OpenID Connect 1,0](v1-protocols-openid-connect-code.md) – Ismerje meg, hogyan használható az OAuth 2,0, hitelesítési protokoll a hitelesítéshez.
* [Szolgáltatások közötti hívások ügyfél](v1-oauth2-client-creds-grant-flow.md) -hitelesítő adatokkal – megtudhatja, hogyan használhatja a OAuth 2,0-ügyfél hitelesítő adatait a szolgáltatás és a szolgáltatás közötti hívások megadására.
* [A szolgáltatás és a szolgáltatás közötti hívások hívása folyamaton](v1-oauth2-on-behalf-of-flow.md) keresztül – megtudhatja, hogyan használhatja a OAuth 2,0-et a szolgáltatás és a szolgáltatás közötti hívásokhoz.
* [SAML protokoll referenciája](active-directory-saml-protocol-reference.md) – Ismerje meg az Azure ad egyszeri bejelentkezéssel és egyszeri kijelentkezési SAML-profiljaival kapcsolatos tudnivalókat.

## <a name="see-also"></a>Lásd még:
[Azure Active Directory fejlesztői útmutató](v1-overview.md)

[Active Directory kód mintái](sample-v1-code.md)
