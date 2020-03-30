---
title: Az Azure AD első lépései a Visual Studio WebApi-projektjeiben
description: Az Azure Active Directory webapi-projektekben való használatának első lépései az Azure AD-hez való csatlakozás vagy az Azure AD létrehozása után a Visual Studio csatlakoztatott szolgáltatásainak használatával
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159454"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Az Azure Active Directory –ismerkedés (WebApi-projektek)

> [!div class="op_single_selector"]
> - [Bevezetés](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk további útmutatást nyújt, miután az Active Directoryt hozzáadta egy ASP.NET WebAPI-projekthez a Visual Studio **Project > Connected Services** parancsán keresztül. Ha még nem adta hozzá a szolgáltatást a projekthez, ezt bármikor megteheti.

Olvassa el a Mi történt a [WebAPI-projekttel?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a vezérlők eléréséhez

A projekt összes vezérlőjét az `[Authorize]` attribútum díszítette. Ehhez az attribútumhoz a felhasználó hitelesítésére van szükség, mielőtt elérne az ezen vezérlők által meghatározott API-khoz. A vezérlő névtelen elérésének engedélyezéséhez távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebb szinten szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely hez engedélyezés szükséges, ahelyett, hogy a vezérlőosztályra alkalmazna.

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Bejelentkezés hozzáadása a Microsofttal egy ASP.NET webalkalmazáshoz](quickstart-v2-aspnet-webapp.md)
