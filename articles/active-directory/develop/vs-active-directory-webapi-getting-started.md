---
title: "Ismerkedés az Azure AD-t a Visual Studio WebApi projektek |} Microsoft Docs"
description: "Ismerkedés az Azure Active Directoryval WebApi projektek, miután csatlakozik, vagy hozzon létre egy Visual Studio használatával Azure AD kapcsolódó szolgáltatások"
services: active-directory
documentationcenter: 
author: kraigb
manager: mtillman
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 3d510c193ab8c7e65340275017cb2dcd4c76def0
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Get Started with Azure Active Directory és a Visual Studio kapcsolódó szolgáltatások (WebApi projektek)
> [!div class="op_single_selector"]
> * [Első lépések](vs-active-directory-webapi-getting-started.md)
> * [mi történt](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Hozzáférés-vezérlők hitelesítést igénylő
A projekt lévő összes rendelkező volt adorned a **engedélyezés** attribútum. Ez az attribútum a felhasználónak hitelesíteni az API-kat, ezek a tartományvezérlők által meghatározott elérése előtt. Ahhoz, hogy a tartományvezérlőt a névtelenül elérhető, távolítsa el ezt az attribútumot a vezérlő. Ha szeretne egy részletesebb szinten adja meg az engedélyeket, az attribútumot minden módszer, amely helyett telepítené azt a vezérlő osztályhoz engedélyezési alkalmazza.

## <a name="next-steps"></a>Következő lépések
[További tudnivalók az Azure Active Directoryban](https://azure.microsoft.com/services/active-directory/)

