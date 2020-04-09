---
title: API keresése egyéni fejlesztésű alkalmazáshoz | Azure
description: Az egyéni fejlesztésű Azure AD-alkalmazásban egy adott API eléréséhez szükséges engedélyek konfigurálása
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885616"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Egy egyedi fejlesztésű alkalmazáshoz szükséges api megkeresése

Az API-khoz való hozzáférés hozzáférési hatókörök és szerepkörök konfigurálását igényli. Ha azt szeretné, hogy az erőforrás-alkalmazás webes API-k az ügyfélalkalmazások, konfigurálnia kell a hozzáférési hatókörök és szerepkörök az API-hoz. Ha azt szeretné, hogy egy ügyfélalkalmazás hozzáférjen egy webes API-hoz, konfigurálnia kell az API-hoz való hozzáféréshez szükséges engedélyeket az alkalmazás regisztrációjában.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Erőforrás-alkalmazás konfigurálása webes API-k közzétételére

Amikor elérhetővé teszi a webes API-t, az API megjelenik az **API kiválasztása** listában, amikor engedélyeket ad hozzá egy alkalmazásregisztrációhoz. Hozzáférési hatókörök hozzáadásához kövesse az [alkalmazás konfigurálása webes API-k felfedéséhez](quickstart-configure-app-expose-web-apis.md)című részben ismertetett lépéseket.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Ügyfélalkalmazás konfigurálása webes API-k eléréséhez

Amikor engedélyeket ad hozzá az alkalmazásregisztrációhoz, **hozzáadhat API-hozzáférést** a kitett webes API-khoz. A webes API-k eléréséhez kövesse az [Ügyfélalkalmazás konfigurálása webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)című részben ismertetett lépéseket.

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory alkalmazásjegyzék ismertetése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
