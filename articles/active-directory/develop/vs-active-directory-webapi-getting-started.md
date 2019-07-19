---
title: Ismerkedés az Azure AD-vel a Visual Studio WebApi projektjeiben
description: A Azure Active Directory használatának első lépései a WebApi-projektekben az Azure AD-vel való csatlakozás vagy a Visual Studio csatlakoztatott szolgáltatásaival való létrehozása után
services: active-directory
ms.service: active-directory
ms.subservice: develop
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
ms.openlocfilehash: 662e0a3e85684287c636b8d0b512b072c978dd3f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320738"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Ismerkedés a Azure Active Directoryrel (WebApi-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta Active Directoryt egy ASP.NET WebAPI projekthez a Visual Studio **project > Connected Services** parancsával. Ha még nem adta hozzá a szolgáltatást a projekthez, bármikor megteheti.

Megtudhatja [, mi történt a WebAPI-projekttel?](vs-active-directory-webapi-what-happened.md) a csatlakoztatott szolgáltatás hozzáadásakor a projekten végrehajtott módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a hozzáférés-vezérléshez

A projekt összes vezérlőjét az `[Authorize]` attribútummal díszítették. Ez az attribútum megköveteli a felhasználó hitelesítését, mielőtt hozzáfér az ezen vezérlők által definiált API-khoz. Annak engedélyezéséhez, hogy a vezérlő névtelenül legyen elérhető, távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebben szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely az engedélyezést igényli a vezérlő osztályra való alkalmazása helyett.

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v1-aspnet-webapp.md)
