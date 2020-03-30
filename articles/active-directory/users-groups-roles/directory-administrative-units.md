---
title: Felügyeleti egységek kezelése (előzetes verzió) - Azure AD | Microsoft dokumentumok
description: Felügyeleti egységek használata az Azure Active Directory engedélyeinek részletesebb delegálásához
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 11/13/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4958975a080a98fb900bf317229ba9eda795d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74028415"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Felügyeleti egységek kezelése az Azure Active Directoryban (előzetes verzió)

Ez a cikk az Azure Active Directory (Azure AD) felügyeleti egységeit ismerteti. A felügyeleti egység egy Azure AD-erőforrás, amely más Azure AD-erőforrások tárolója is lehet. Ebben az előzetes verzióban ezek az erőforrások csak felhasználók lehetnek. Egy rendszergazdai egység hatókörrel rendelkező felhasználói fiók rendszergazdája például frissítheti a profiladatokat, alaphelyzetbe állíthatja a jelszavakat, és csak a felügyeleti egységben rendelhet licenceket a felhasználókhoz.

A felügyeleti egységek segítségével rendszergazdai engedélyeket delegálhat a felhasználók részhalmazai felett, és házirendeket alkalmazhat a felhasználók egy részhalmazára. A felügyeleti egységek segítségével engedélyeket delegálhat a regionális rendszergazdáknak, vagy részletes en állíthatja be a házirendet.

## <a name="deployment-scenario"></a>Központi telepítési forgatókönyv

A felügyeleti egységek független részlegekkel rendelkező szervezeteknél lehetnek hasznosak. Tekintsük a példát egy nagy egyetem, amely áll a sok autonóm iskolák (School of Business, School of Engineering, és így tovább), hogy minden saját informatikai rendszergazdák, akik szabályozzák a hozzáférést, a felhasználók kezelése, és állítsa be a politikákat az iskola. A központi ügyintéző létrehozhategy adminisztratív egységet az Üzleti Iskola számára, és csak az üzleti diákokkal és a személyzettel népesítheti be azt. Ezután a központi rendszergazda hozzáadhatja a Business school IT-személyzet egy hatókörrel rendelkező szerepkör, amely rendszergazdai engedélyeket csak az Azure AD-felhasználók számára az üzleti iskola felügyeleti egység.

## <a name="license-requirements"></a>Licenckövetelmények

A felügyeleti egységek használatához minden felügyeleti egység rendszergazdájához Azure Active Directory Prémium licenc szükséges. További információ: [Az Azure AD Premium – első lépések.](../fundamentals/active-directory-get-started-premium.md)

## <a name="managing-administrative-units"></a>Felügyeleti egységek kezelése

Ebben az előzetes verzióban csak úgy hozhat létre és kezelhet felügyeleti egységeket, ha az Azure Active Directory module for Windows PowerShell-parancsmagokat használja a Felügyeleti egységek használata című dokumentumban leírtak [szerint.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

A szoftverkövetelményekről és az Azure AD modul telepítéséről, valamint a felügyeleti egységek kezeléséhez szükséges Azure AD modul parancsmagjairól, beleértve a szintaxist, a paraméterleírásokat és példákat, az [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

[Azure Active Directory-kiadások](../fundamentals/active-directory-whatis.md)
