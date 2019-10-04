---
title: Hogyan találhatja meg egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez |} A Microsoft Docs
description: Az adott API-t az egyéni eléréséhez szükséges engedélyek konfigurálásának módjáról az Azure AD alkalmazást fejlesztett ki.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2492a9346585698132e7fd9cfcde068ffd60ebc5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476166"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez megkeresése

API-k a hozzáférést a hozzáférési hatókörök és szerepkörök van szükség. Ha szeretné elérhetővé tenni az erőforrás alkalmazás webes API-kat az ügyfélalkalmazások számára, a hozzáférési hatókörök és szerepkörök beállítása az API-t szeretné. Ha azt szeretné, hogy egy ügyfélalkalmazás egy webes API-k elérésére, kell eléréséhez az alkalmazás regisztrációját az API-engedélyek konfigurálása.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Amikor elérhetővé teszi a webes API-t, jeleníthető meg az API-t a **API kiválasztása** listában engedélyek egy alkalmazásregisztráció való hozzáadásakor. A hozzáférési hatókörök hozzáadásához kövesse az ismertetett lépéseket [egy alkalmazást úgy teszi közzé a webes API-k](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Webes API-k eléréséhez egy ügyfélalkalmazás konfigurálása

Engedélyeket ad hozzá az alkalmazás regisztrációját, ha **API-hozzáférés hozzáadása** közzétett webes API-k. Webes API-k eléréséhez kövesse az ismertetett lépéseket [ügyfélalkalmazás webes API-k eléréséhez konfigurálása](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>További lépések

-   [Az Azure Active Directory alkalmazásjegyzékének megismerése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


