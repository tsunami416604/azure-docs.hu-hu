---
title: Ismerkedés az Azure AD-t a Visual Studio-WebApi-projektek használatába
description: Első lépések a WebApi-projektek az Azure Active Directory használatával, miután csatlakozik, vagy létrehozása a Visual Studio használata az Azure AD-hez kapcsolódó szolgáltatásokkal
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662d958fc7ef6fe5c9d0e61a1d8e48983d10196f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198796"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Ismerkedés az Azure Active Directory (WebApi-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk a további útmutatást nyújt az Active Directory egy ASP.NET WebAPI-projekt keresztül történő hozzáadása után a **Project > csatlakoztatott szolgáltatás** parancsot a Visual Studio. Ha még nem hozzáadott a szolgáltatás a projekthez, bármikor megteheti.

Lásd: [Mi történt a WebAPI-projektemmel?](vs-active-directory-webapi-what-happened.md) módosítások a projekthez a csatlakoztatott szolgáltatás hozzáadása során.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés, hozzáférés-vezérlő megkövetelése

A projektben lévő összes voltak adorned együtt a `[Authorize]` attribútum. Ez az attribútum a felhasználót, hogy hitelesítse a ezek a tartományvezérlők által definiált API-k elérése előtt szükséges. Ahhoz, hogy a tartományvezérlőt a névtelenül elérhető, távolítsa el ezt az attribútumot a vezérlő. Ha szeretne részletesebben, állítsa be az engedélyeket, az attribútum vonatkoznak minden módszer, amely engedélyezési ahelyett hogy a vezérlő osztályhoz.

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek az Azure Active Directory](authentication-scenarios.md)
- [Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása](quickstart-v1-aspnet-webapp.md)
