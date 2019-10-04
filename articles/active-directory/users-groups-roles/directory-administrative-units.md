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
ms.openlocfilehash: 58b61186a876af90c812ec7faf41fa9f5b14bf4e
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336925"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Felügyeleti egységek felügyelete Azure Active Directoryban (előzetes verzió)

Ez a cikk a Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti. A felügyeleti egység olyan Azure AD-erőforrás, amely más Azure AD-erőforrások tárolója lehet. Ebben az előzetes kiadásban ezek az erőforrások csak felhasználók lehetnek. Például egy felügyeleti egység – a hatókörrel rendelkező felhasználói fiók rendszergazdája frissítheti a profil adatait, alaphelyzetbe állíthatja a jelszavakat, és hozzárendelheti a licenceket a felhasználók számára a felügyeleti egységben.

A felügyeleti egységekkel rendszergazdai engedélyeket delegálhat a felhasználók részhalmazán, és házirendeket alkalmazhat a felhasználók egy részhalmazára. A felügyeleti egységekkel engedélyeket delegálhat a regionális rendszergazdáknak, vagy megadhatja a szabályzatot egy részletességi szinten.

## <a name="deployment-scenario"></a>Üzembe helyezési forgatókönyv

A felügyeleti egységek olyan szervezeteknél lehetnek hasznosak, amelyek független részlegekkel rendelkeznek. Vegyünk például egy olyan nagy egyetemet, amely számos autonóm iskola (üzleti iskola, mérnöki iskola stb.) számára készült, és a saját informatikai rendszergazdái rendelkeznek a hozzáférés szabályozásához, a felhasználók kezeléséhez és a szabályzatok beállításához. A központi rendszergazda létrehozhat egy felügyeleti egységet az üzleti iskola számára, és feltöltheti azt csak az üzleti tanulók és munkatársak számára. Ezután a központi rendszergazda hozzáadhatja az üzleti iskola informatikai részlegét egy hatókörrel rendelkező szerepkörhöz, amely csak az Azure AD-felhasználók számára biztosít rendszergazdai engedélyeket a Business School felügyeleti egységben.

## <a name="license-requirements"></a>Licenckövetelmények

A felügyeleti egységek használatához prémium szintű Azure Active Directory licencre van szükség az egyes felügyeleti egységek rendszergazdái számára. További információ: [Bevezetés a prémium szintű Azure ad](../fundamentals/active-directory-get-started-premium.md)használatába.

## <a name="managing-administrative-units"></a>Felügyeleti egységek kezelése

Ebben az előzetes kiadásban a felügyeleti egységek létrehozásához és kezeléséhez az egyetlen lehetőség a Azure Active Directory modul használata a Windows PowerShell-parancsmagokhoz a [felügyeleti egységek](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0) használata című témakörben leírtak szerint.

További információ a szoftverekre vonatkozó követelményekről és az Azure AD-modul telepítéséről, valamint a felügyeleti egységek kezelésére szolgáló Azure AD modul-parancsmagokról, beleértve a szintaxist, a paraméterek leírását és a példákat lásd: [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>További lépések

[Azure Active Directory kiadások](../fundamentals/active-directory-whatis.md)
