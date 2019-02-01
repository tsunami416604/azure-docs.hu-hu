---
title: Adminisztratív egységek kezelése (előzetes verzió) – az Azure Active Directory |} A Microsoft Docs
description: Felügyeleti egységek használatával az Azure Active Directoryban engedélyek részletesebb delegáláshoz
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 52b71e557ade54cdefd349b8981100210833185a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508453"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Adminisztratív egységek kezelése az Azure Active Directoryban (nyilvános előzetes verzió)

Ez a cikk ismerteti felügyeleti egységek formájában – egy új Azure Active Directory (Azure AD) tartalmazó tároló rendszergazdai engedélyek delegálása felhasználók alkészletek keresztül, és házirendek alkalmazása a felhasználók egy alhalmazára használható erőforrásokat. Az Azure Active Directory felügyeleti egységek engedélyezése központi rendszergazdák regionális rendszergazdák engedélyeket delegálhatnak vagy részletes szabályzat beállítása.

Ez akkor hasznos, független részlegek, például nagy egyetemen, amely számos autonóm iskolák (iskolai üzleti, mérnöki iskolai és így tovább) egymástól független áll a szervezetben. Az ilyen osztályok rendelkezik a saját informatikai rendszergazdák számára a hozzáférést, felhasználók kezelése és a kifejezetten az osztály a szabályzatok beállítását. Központi rendszergazdák tudják szeretne megadni a részlegszintű keresztül az adott részlegek számára a rendszergazdák engedélyeket. Pontosabban, ebben a példában használja, egy központi rendszergazdák, például egy adott iskolai (üzleti iskola) a felügyeleti egység létrehozása, és csak az üzleti felhasználók iskolai való feltöltéséhez. Egy központi felügyeleti adhat hozzá az üzleti iskolai informatikai személyzetet tart fenn a hatókörrel rendelkező szerepkörhöz, más szóval, adja meg az informatikai munkatársak üzleti school felügyeleti engedélyekre csak az iskolai rendszergazda részleg keresztül.

> [!IMPORTANT]
> Felügyeleti egységek használata szükséges a felügyeleti egység hatókörébe tartozó rendszergazdát, hogy rendelkezik egy Azure Active Directory Premium-licencet, és az alapszintű Azure Active Directory licenceket az összes felhasználó számára a felügyeleti egység. További információkért lásd: [Ismerkedés az Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


A központi felügyeleti szempontból a felügyeleti egység a Deleted object által létrehozott, és az erőforrások feltöltve. **Ebben az előzetes kiadásban csak a felhasználók ezeket az erőforrásokat is lehet.** Miután létrehozott és feltöltött, a felügyeleti egység használható hatóköreként a rendelt engedélyek korlátozása csak a felügyeleti egység található erőforrások felett.

## <a name="managing-administrative-units"></a>Adminisztratív egységek kezelése
Ez az előzetes kiadásban hozzon létre, és az Azure Active Directory modul a Windows PowerShell-parancsmagok használatával adminisztratív egységek kezelése. További információ a megtenni, olvassa el [felügyeleti egységek használata](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)

További információ a szoftverek és az Azure AD-modul telepítése és kezelése a felügyeleti egység, beleértve a szintaxist, paramétereik leírásának és a példákat az Azure AD-modul parancsmagjai tájékoztatást lásd: [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0).

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory-kiadások](../fundamentals/active-directory-whatis.md)
