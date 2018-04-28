---
title: Ismerkedés az Azure AD-t a Visual Studio WebApi projektek
description: Ismerkedés az Azure Active Directoryval WebApi projektek, miután csatlakozik, vagy hozzon létre egy Visual Studio használatával Azure AD kapcsolódó szolgáltatások
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
ms.openlocfilehash: 109de9fb78ae3abfc09a37c6b8cb38c554f7613c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Ismerkedés az Azure Active Directoryban (WebApi projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk útmutatást további Active Directory egy ASP.NET WebAPI projekthez hozzáadása után a **Project > kapcsolódó szolgáltatások** a Visual Studio parancsot. Ha a szolgáltatás már nem felvételét a projekthez, bármikor is megteheti.

Lásd: [Mi történt a WebAPI projekt?](vs-active-directory-webapi-what-happened.md) amikor a csatlakoztatott szolgáltatás hozzáadása a projekthez végzett módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hozzáférés-vezérlők hitelesítést igénylő

A projekt lévő összes rendelkező volt adorned a `[Authorize]` attribútum. Ez az attribútum a felhasználónak hitelesíteni az API-kat, ezek a tartományvezérlők által meghatározott elérése előtt. Ahhoz, hogy a tartományvezérlőt a névtelenül elérhető, távolítsa el ezt az attribútumot a vezérlő. Ha szeretne egy részletesebb szinten adja meg az engedélyeket, az attribútumot minden módszer, amely helyett telepítené azt a vezérlő osztályhoz engedélyezési alkalmazza.

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása](guidedsetups/active-directory-aspnetwebapp-v1.md)
