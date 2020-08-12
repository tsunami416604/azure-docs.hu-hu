---
title: Egyéni fejlesztésű alkalmazáshoz tartozó API keresése | Azure
description: Az egyes API-k eléréséhez szükséges engedélyek konfigurálása az egyéni fejlesztésű Azure AD-alkalmazásokban
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 8c3d79211ce594ec4c175aa6f6b5f8df3a7c4ba8
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115543"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egyéni fejlesztésű alkalmazáshoz szükséges speciális API megkeresése

Az API-khoz való hozzáféréshez hozzáférési hatókörök és szerepkörök konfigurálása szükséges. Ha szeretné elérhetővé tenni az erőforrás-alkalmazás webes API-jait az ügyfélalkalmazások számára, konfigurálnia kell a hozzáférési hatóköröket és a szerepköröket az API-hoz. Ha azt szeretné, hogy egy ügyfélalkalmazás hozzáférjen egy webes API-hoz, konfigurálnia kell az API-k eléréséhez szükséges engedélyeket az alkalmazás regisztrálásához.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Ha a webes API-t teszi elérhetővé, az API a **Select an API (API kiválasztása** ) listában jelenik meg, amikor engedélyeket ad az alkalmazás regisztrálásához. Hozzáférési hatókörök hozzáadásához kövesse az [alkalmazás konfigurálása a webes API-k számára elérhetővé](quickstart-configure-app-expose-web-apis.md)tételéhez című témakör lépéseit.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Ügyfélalkalmazás konfigurálása a webes API-k eléréséhez

Amikor engedélyeket ad az alkalmazás regisztrálásához, **HOZZÁADHAT API-hozzáférést** az elérhető webes API-khoz. A webes API-k eléréséhez kövesse az [ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)című témakör lépéseit.

## <a name="next-steps"></a>További lépések

- [A Azure Active Directory alkalmazás jegyzékfájljának ismertetése](./reference-app-manifest.md)