---
title: Hogyan találhatja meg egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez |} A Microsoft Docs
description: Az adott API-t az egyéni eléréséhez szükséges engedélyek konfigurálásának módjáról az Azure AD alkalmazást fejlesztett ki.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 306d16dd306fc569181e0334e6674a9c935aac1f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182221"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egy adott API-t egy egyénileg fejlesztett alkalmazásba történő üzembe helyezéshez megkeresése

API-k a hozzáférést a hozzáférési hatókörök és szerepkörök van szükség. Ha szeretné elérhetővé tenni az erőforrás alkalmazás webes API-kat az ügyfélalkalmazások számára, a hozzáférési hatókörök és szerepkörök beállítása az API-t szeretné. Ha azt szeretné, hogy egy ügyfélalkalmazás egy webes API-k elérésére, kell eléréséhez az alkalmazás regisztrációját az API-engedélyek konfigurálása.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Amikor elérhetővé teszi a webes API-t, jeleníthető meg az API-t a **API kiválasztása** listában engedélyek egy alkalmazásregisztráció való hozzáadásakor. A hozzáférési hatókörök hozzáadásához kövesse az ismertetett lépéseket [az erőforrás-alkalmazás hozzáadása a hozzáférési hatókörök](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Webes API-k eléréséhez egy ügyfélalkalmazás konfigurálása

Engedélyeket ad hozzá az alkalmazás regisztrációját, ha **API-hozzáférés hozzáadása** közzétett webes API-k. Webes API-k eléréséhez kövesse az ismertetett lépéseket [adja hozzá a hitelesítő adatokat vagy webes API-k elérésére jogosult](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).

## <a name="next-steps"></a>További lépések

-   [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Az Azure Active Directory alkalmazásjegyzékének megismerése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


