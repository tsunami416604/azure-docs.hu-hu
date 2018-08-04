---
title: Ismerkedés az Azure AD-t a Visual Studio-WebApi-projektek használatába
description: Első lépések a WebApi-projektek az Azure Active Directory használatával, miután csatlakozik, vagy létrehozása a Visual Studio használata az Azure AD-hez kapcsolódó szolgáltatásokkal
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: 6ddc1e8f7520135092eefbcbf2b4a2b3f1026e66
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494713"
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
