---
title: "Hol találhatók a szükséges egyéni által fejlesztett alkalmazás adott API |} Microsoft Docs"
description: "Hozzá kell férnie egy adott API-nak az egyéni engedélyek fejlesztése az Azure AD-alkalmazást"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e3d15db2e00c2cd5b52dd6c486f61ee88d922d79
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egy adott API szükséges egyéni által fejlesztett alkalmazás megkeresése

API-k eléréséhez szükséges hozzáférési hatókörök és a szerepkörök konfigurációja. Ha az erőforrás alkalmazás webes API-k ügyfélalkalmazások számára elérhetővé tenni kívánt, hozzáférési hatókörök és szerepkörök konfigurálása az API-t szeretné. Ha azt szeretné, hogy egy webes API-k elérésére ügyfélalkalmazást, kell konfigurálni az engedélyeket, hogy az API-alkalmazás regisztrációja.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Egy erőforrás-alkalmazások – ezek általában a webes API-k konfigurálása

Amikor teszi ki a webes API-t, jeleníthető meg az API-t a **API kiválasztása** listára, amikor engedélyeket ad hozzá egy alkalmazás regisztrálása. A hozzáférési hatókörök hozzáadásához kövesse a témakörben ismertetett lépéseket [hozzáférési hatókörök hozzáadása az erőforrás alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>A webes API-k elérésére ügyfélalkalmazás konfigurálása

Amikor az alkalmazás regisztrálása az engedélyeket, lehet **API-hozzáférés hozzáadása** feladatban felfedett webes API-k számára. Webes API-k eléréséhez kövesse a témakörben ismertetett lépéseket [adja hozzá a hitelesítő adatokat vagy webes API-k hozzáférési engedélye a](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Következő lépések

-   [Alkalmazások integrálása az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Az Azure Active Directory alkalmazásjegyzékének megismerése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


