---
title: Munkahelyi vagy iskolai fiók bezárása nem felügyelt Azure AD-címtárban
description: Munkahelyi vagy iskolai fiók bezárása nem felügyelt Azure-beli Active Directoryban.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815722"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>A munkahelyi vagy iskolai fiók bezárása nem felügyelt könyvtárban

Ha ön egy nem felügyelt Azure Active Directory (Azure AD) szervezet felhasználója, és már nem kell használnia az adott szervezettől származó alkalmazásokat, vagy fenntartania a társítást, bármikor bezárhatja a fiókját. A nem felügyelt könyvtárnak nincs globális rendszergazdája. A nem felügyelt címtárban lévő felhasználók saját maguk zárhatják le a fiókjukat anélkül, hogy a rendszergazdával kellene felvenniük a kapcsolatot.

A nem felügyelt címtárban lévő felhasználók gyakran az önkiszolgáló regisztráció során jönnek létre. Ilyen lehet például egy szervezet információs dolgozója, aki feliratkozik egy ingyenes szolgáltatásra. Az önkiszolgáló regisztrációról a Mi [az Azure Active Directoryön való önkiszolgáló regisztráció?](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Előkészületek

A fiók bezárása előtt erősítse meg a következő elemeket:

* Győződjön meg arról, hogy egy nem felügyelt Azure AD-címtár felhasználója. Nem zárhatja be a fiókját, ha felügyelt könyvtárhoz tartozik. Ha felügyelt címtárhoz tartozik, és meg szeretné szüntetni a fiókját, forduljon a rendszergazdához. Arról, hogy miként állapíthatja meg, hogy nem felügyelt könyvtárhoz tartozik-e, [olvassa el a Felhasználó törlése a nem felügyelt bérlőből című témakört.](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)

* Mentse a megtartani kívánt adatokat. Az exportálási kérelem elküldéséről a [Rendszer által létrehozott naplók elérése és exportálása a nem felügyelt bérlők számára](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)című témakörben talál további információt.

> [!WARNING]
> A fiók megszüntetése visszafordíthatatlan. A fiók megszüntetésekor minden személyes adat törlődik. A továbbiakban nem férhet hozzá fiókjához és a fiókjához társított adatokhoz.

## <a name="close-your-account"></a>Fiók lezárása

Nem felügyelt munkahelyi vagy iskolai fiók bezárásához kövesse az alábbi lépéseket:

1. Jelentkezzen be [a fiók bezárásához](https://go.microsoft.com/fwlink/?linkid=873123)a bezárni kívánt fiók használatával.

1. Az **Adatkérések csoportban**válassza **a Fiók bezárása lehetőséget.**

    ![Saját adatkérések - Fiók lezárása](./media/users-close-account/close-account.png)

1. Tekintse át a megerősítő üzenetet, majd válassza az **Igen**lehetőséget.

    ![Saját adatkérések - Lezárás megerősítése](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>További lépések

- [Mi az Önkiszolgáló regisztráció az Azure Active Directoryra?](directory-self-service-signup.md)
- [A felhasználó törlése nem felügyelt bérlőből](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [A rendszer által létrehozott naplók elérése és exportálása nem felügyelt bérlőnél](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
