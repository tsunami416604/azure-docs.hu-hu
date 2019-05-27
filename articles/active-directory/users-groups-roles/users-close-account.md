---
title: Zárja be a munkahelyi vagy iskolai fiókkal, egy nem felügyelt címtárban – Azure Active Directory |} A Microsoft Docs
description: Hogyan gombra kattintva zárja be a munkahelyi vagy iskolai fiókot egy nem felügyelt Azure Active Directoryban.
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
ms.openlocfilehash: 39b359ef7feeaec541ba17e98a5d1e9b74c6403a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65958000"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Zárja be a munkahelyi vagy iskolai fiókkal, egy nem felügyelt címtárban

Ha egy felhasználó egy nem felügyelt Azure Active Directory (Azure AD)-szervezetnél, és már nem kell használnia a szervezetben lévő alkalmazásokat, vagy bármely társítva, bármikor bezárhatja a fiók karbantartása. Egy nem felügyelt címtár globális rendszergazda nem rendelkezik. Egy nem felügyelt címtár felhasználói fiókjukat a saját, zárja be anélkül, hogy a rendszergazda kapcsolatba kellene.

Egy nem felügyelt címtár felhasználóinak gyakran során jönnek létre önkiszolgáló regisztráció. Például lehet egy Infomunkás a szervezeten belüli egy ingyenes szolgáltatás szolgáltatásért. Önkiszolgáló kapcsolatos további információkért lásd: [önkiszolgáló Mi az Azure Active Directory-előfizetés?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Előkészületek

A fiók megszüntetése előtt ellenőrizze a következő elemek:

* Győződjön meg arról, hogy egy felhasználó egy nem felügyelt Azure AD-címtárban. A fiók nem zárható be, ha egy felügyelt címtár tartozik. Ha egy felügyelt címtár tartozik, és bezárja a fiókját, meg kell forduljon a rendszergazdához. Tartozik-e a nem felügyelt címtárhoz megállapításával kapcsolatos további információkért lásd: [törölje a felhasználót a nem felügyelt bérlőt](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Mentse a megtartani kívánt adatokat. Hogyan lehet elküldeni az exportálási kérelem kapcsolatos információkért lásd: [elérése és exportálása a rendszer által létrehozott naplók nem felügyelt bérlők](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> A fiók megszüntetése nem vonható vissza. Ha megszünteti a fiókot, az összes személyes adatot törlődni fog. Már nem kell fiókját és adatait a fiókjához való hozzáférést.

## <a name="close-your-account"></a>Fiók megszüntetése

Zárja be egy nem felügyelt munkahelyi vagy iskolai fiókját, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [megszünteti a fiókot](https://go.microsoft.com/fwlink/?linkid=873123), a gombra kattintva zárja be a kívánt fiók használatával.

1. A **saját kérelmek**válassza **zárja be a fiók**.

    ![Saját adatok kérések - fiók bezárása](./media/users-close-account/close-account.png)

1. Tekintse át a megerősítést kérő üzenet, majd **Igen**.

    ![Zárja be az adatokat kér - megerősítése](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>További lépések

- [Mi az önkiszolgáló regisztráció az Azure Active Directory?](directory-self-service-signup.md)
- [A nem felügyelt bérlőt a felhasználó törlése](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Elérése és exportálása a rendszer által létrehozott naplók a nem felügyelt bérlők számára](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
