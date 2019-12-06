---
title: Egyedi API-k megkeresése az egyéni fejlesztésű alkalmazásokhoz | Microsoft Docs
description: Az egyes API-k eléréséhez szükséges engedélyek konfigurálása az egyéni fejlesztésű Azure AD-alkalmazásokban
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c223df28f8082125d48f1e3619088de5cf2687
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844600"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egyéni fejlesztésű alkalmazáshoz szükséges speciális API megkeresése

Az API-khoz való hozzáféréshez hozzáférési hatókörök és szerepkörök konfigurálása szükséges. Ha szeretné elérhetővé tenni az erőforrás-alkalmazás webes API-jait az ügyfélalkalmazások számára, konfigurálnia kell a hozzáférési hatóköröket és a szerepköröket az API-hoz. Ha azt szeretné, hogy egy ügyfélalkalmazás hozzáférjen egy webes API-hoz, konfigurálnia kell az API-k eléréséhez szükséges engedélyeket az alkalmazás regisztrálásához.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Ha a webes API-t teszi elérhetővé, az API a **Select an API (API kiválasztása** ) listában jelenik meg, amikor engedélyeket ad az alkalmazás regisztrálásához. Hozzáférési hatókörök hozzáadásához kövesse az [alkalmazás konfigurálása a webes API-k számára elérhetővé](quickstart-configure-app-expose-web-apis.md)tételéhez című témakör lépéseit.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Ügyfélalkalmazás konfigurálása a webes API-k eléréséhez

Amikor engedélyeket ad az alkalmazás regisztrálásához, **HOZZÁADHAT API-hozzáférést** az elérhető webes API-khoz. A webes API-k eléréséhez kövesse az [ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)című témakör lépéseit.

## <a name="next-steps"></a>Következő lépések

- [A Azure Active Directory alkalmazás jegyzékfájljának ismertetése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
