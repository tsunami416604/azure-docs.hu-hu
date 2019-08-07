---
title: Felügyeleti egységek kezelése (előzetes verzió) – Azure Active Directory | Microsoft Docs
description: Felügyeleti egységek használata az engedélyek részletesebb delegálásához Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b4bdced50f806367a53881d5ef0abd0a3710496
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736780"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Felügyeleti egységek felügyelete Azure Active Directoryban (nyilvános előzetes verzió)

Ez a cikk a Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti, amelyek olyan erőforrás-tárolók, amelyek a rendszergazdai engedélyek delegálására használhatók a felhasználók alcsoportjain keresztül, valamint házirendek alkalmazása a felhasználók egy részhalmazára. Az Azure AD-ben a felügyeleti egységek lehetővé teszik a központi rendszergazdák számára az engedélyek delegálását a regionális rendszergazdák számára, vagy a szabályzatok részletességi szinten történő beállítását.

Ez olyan szervezetek esetében hasznos, amelyek független részlegekkel rendelkeznek, például egy olyan nagy Egyetemen, amely számos autonóm iskolából (üzleti iskolából, mérnöki iskolából stb.) tevődik össze, amelyek egymástól függetlenek. Ilyenek például a saját rendszergazdák, akik szabályozzák a hozzáférést, kezelhetik a felhasználókat, és szabályzatokat állíthatnak be kifejezetten a saját részlegük számára. A központi rendszergazdák ezeket a központú rendszergazdákat szeretnék biztosítani az adott részleg felhasználói számára. Pontosabban, a példa használatával a központi rendszergazda létrehozhat egy adott iskolához (üzleti iskolához) tartozó felügyeleti egységet, és feltöltheti azt csak az üzleti iskola felhasználóival. Ezt követően a központi rendszergazda felveheti az üzleti iskolát az informatikai munkatársakat egy hatókörrel rendelkező szerepkörbe, vagyis a vállalati iskolai felügyeleti egység informatikai személyzetének csak az üzleti iskola felügyeleti egységét kell megadnia.

> [!IMPORTANT]
> A felügyeleti egység használatához a felügyeleti egység rendszergazdájának prémium szintű Azure Active Directory licenccel kell rendelkeznie. További információ: [Bevezetés a prémium szintű Azure ad](../fundamentals/active-directory-get-started-premium.md)használatába.
>

A központi rendszergazda szempontjából a felügyeleti egység egy olyan címtár-objektum, amely erőforrásokkal hozható létre és tölthető fel. **Ebben az előzetes kiadásban ezek az erőforrások csak felhasználók lehetnek.** A létrehozás és a feltöltés után a felügyeleti egység hatókörként használható, amely csak a felügyeleti egységben található erőforrásokra korlátozza a megadott engedélyeket.

## <a name="managing-administrative-units"></a>Felügyeleti egységek kezelése

Ebben az előzetes kiadásban felügyeleti egységeket hozhat létre és kezelhet a Windows PowerShell-parancsmagok Azure Active Directory moduljának használatával. Ha többet szeretne megtudni Ennek módjáról, tekintse meg a [felügyeleti egységek használata](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) című témakört.

A szoftverre vonatkozó követelményekkel és az Azure AD-modul telepítésével, valamint a felügyeleti egységek kezelésére szolgáló Azure AD modul-parancsmagokkal kapcsolatos további információkért, beleértve a szintaxist, a paraméterek leírását és a példákat, tekintse meg a következőt: [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>További lépések

[Azure Active Directory kiadások](../fundamentals/active-directory-whatis.md)
