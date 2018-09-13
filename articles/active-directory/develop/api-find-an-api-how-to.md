---
title: Hogyan találhatja meg egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez |} A Microsoft Docs
description: Az adott API-t az egyéni eléréséhez szükséges engedélyek konfigurálásának módjáról az Azure AD alkalmazást fejlesztett ki.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 8d0b9f219d7a0bc61e3d12acfaae6015963401f9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724045"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez megkeresése

API-k a hozzáférést a hozzáférési hatókörök és szerepkörök van szükség. Ha szeretné elérhetővé tenni az erőforrás alkalmazás webes API-kat az ügyfélalkalmazások számára, a hozzáférési hatókörök és szerepkörök beállítása az API-t szeretné. Ha azt szeretné, hogy egy ügyfélalkalmazás egy webes API-k elérésére, kell eléréséhez az alkalmazás regisztrációját az API-engedélyek konfigurálása.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Webes API-k elérhetővé egy erőforrás-alkalmazás konfigurálása

Amikor elérhetővé teszi a webes API-t, jeleníthető meg az API-t a **API kiválasztása** listában engedélyek egy alkalmazásregisztráció való hozzáadásakor. A hozzáférési hatókörök hozzáadásához kövesse az ismertetett lépéseket [az erőforrás-alkalmazás hozzáadása a hozzáférési hatókörök](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Webes API-k eléréséhez egy ügyfélalkalmazás konfigurálása

Engedélyeket ad hozzá az alkalmazás regisztrációját, ha **API-hozzáférés hozzáadása** közzétett webes API-k. Webes API-k eléréséhez kövesse az ismertetett lépéseket [adja hozzá a hitelesítő adatokat vagy webes API-k elérésére jogosult](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>További lépések

-   [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Az Azure Active Directory alkalmazásjegyzékének megismerése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


