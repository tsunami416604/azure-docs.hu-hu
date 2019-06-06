---
title: A csoportokat vagy alkalmazásokat – Azure Active Directory hozzáférési felülvizsgálatok elvégzése |} A Microsoft Docs
description: Ismerje meg, hogyan végezheti el csoport tagjainak vagy alkalmazás-hozzáférés az Azure Active Directory hozzáférési felülvizsgálatok hozzáférési felülvizsgálat.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/22/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec3909ffbb624284f999360140b7454098643062
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473368"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>A csoportok a hozzáférési felülvizsgálat befejezése vagy alkalmazásokat az Azure AD hozzáférési felülvizsgálatokkal

Rendszergazdaként Ön [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md) és véleményezők [elvégezhetik a hozzáférési felülvizsgálatot](perform-access-review.md). Ez a cikk ismerteti a hozzáférési felülvizsgálatok eredményeinek megtekintéséhez és a alkalmazni az eredményeket.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda, felhasználó rendszergazdai, biztonsági rendszergazdai vagy biztonsági olvasó

További információkért lásd: [mely felhasználók rendelkeznie kell licencekkel?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="view-an-access-review"></a>Hozzáférési felülvizsgálat megtekintéséhez

Nyomon követheti a folyamat állapotát, a felülvizsgálatot, végezze el az ellenőrzéseket.

1. Jelentkezzen be az Azure Portalon, és nyissa meg a [Identitáskezelést oldal](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. A bal oldali menüben kattintson a **hozzáférési felülvizsgálatokkal**.

1. A listában kattintson a hozzáférési felülvizsgálat.

    Hozzáférés sorozatát megtekintéséhez értékelések, keresse meg a hozzáférési felülvizsgálatot, és az ütemezett ellenőrzések megtalálja a közelgő események.

    Az a **áttekintése** lapon láthatja a folyamat állapotát. Nincs hozzáférési jogosultsága a címtárban módosulnak a felülvizsgálat befejezése.

    ![A hozzáférési felülvizsgálatok folyamatban](./media/complete-access-review/overview-progress.png)

1. Ha meg szeretné szüntetni a hozzáférési felülvizsgálat korábban elérte az ütemezett befejezési dátum, kattintson a **leállítása** gombra.

    Ha a felülvizsgálat leállítása, felülvizsgálók többé nem tud a visszajelzést adni. Felülvizsgálat nem indítható újra, miután leállt.

1. Ha már nem érdekli a hozzáférési felülvizsgálatot, törölheti azt kattintva a **törlése** gombra.

## <a name="apply-the-changes"></a>A módosítások alkalmazása

Ha **eredmények automatikus alkalmazása az erőforrás** engedélyezve volt és a beállításokat az alapján **befejezést követő művelet beállításai**, automatikus – a alkalmazni fogja a felülvizsgálat záró dátuma, vagy ha manuálisan leállítja a felülvizsgálatot követően hajtható végre.

Ha **eredmények automatikus alkalmazása az erőforrás** nem volt engedélyezve a felülvizsgálatot, kattintson a **alkalmaz** manuálisan a módosítások életbe léptetéséhez. Ha a felhasználó hozzáférése meg lett tagadva a felülvizsgálatot, kattintva **alkalmaz**, Azure ad-ben a tagsági és alkalmazás-hozzárendelés eltávolítása.

![Hozzáférési felülvizsgálat módosítások alkalmazása](./media/complete-access-review/apply-changes.png)

A felülvizsgálat állapota változik **befejezve** keresztül például köztes állapotok **alkalmazása** és végül állapotba **eredmények alkalmazva**. Tekintse meg a letiltott felhasználók, ha bármely, a csoport tagsági és alkalmazás-hozzárendelés néhány perc múlva távolít el kell látnia.

Egy konfigurált automatikus felülvizsgálat alkalmazása, vagy válassza **alkalmaz** nincs hatással a egy csoportot, amely egy helyszíni címtár származik, vagy a dinamikus csoportot. Ha szeretné módosítani egy csoportot, amely a helyszíni származik, töltse le az eredményeket, és ezeket a módosításokat alkalmazni a csoport ebben a könyvtárban reprezentációja.

## <a name="retrieve-the-results"></a>Az eredmény lekérése

Egy egyszeri hozzáférési felülvizsgálati eredmények megtekintéséhez kattintson a **eredmények** lapot. Csak a felhasználó hozzáférését, megtekintéséhez kifejezést a keresőmezőbe írja be a megjelenítendő név vagy egyszerű felhasználónév egy felhasználó, akinek hozzáférést felülvizsgálták.

![Hozzáférési felülvizsgálat eredményeinek beolvasása](./media/complete-access-review/retrieve-results.png)

Aktív hozzáférési felülvizsgálatok, amely ismétlődő folyamat előrehaladásának megtekintéséhez kattintson a a **eredmények** lapot.

Ismétlődő hozzáférési felülvizsgálat befejeződött példányához eredményeinek megtekintéséhez kattintson **előzmények megtekintése**, majd válassza ki a listából, befejezett hozzáférési felülvizsgálati példány, meghatározott példányának a példány kezdő és záró dátum alapján. Ez a példány eredményeit szerezhető be a **eredmények** lapot.

Hozzáférési felülvizsgálat eredményeinek lekéréséhez kattintson a **letöltése** gombra. Az eredményül kapott CSV-fájl tekinthet meg, az Excelben vagy más programok telepítése és törlése, nyissa meg az UTF-8 kódolású CSV-fájlok.

## <a name="remove-users-from-an-access-review"></a>Távolítsa el a felhasználókat a hozzáférési felülvizsgálat

 Alapértelmezés szerint a törölt felhasználók 30 napig töröltek maradnak az Azure AD-ban, amely idő alatt szükség esetén egy rendszergazda visszaállíthatja őket.  A felhasználók 30 nap után véglegesen törlődnek.  Ezenkívül egy globális rendszergazda az Azure Active Directory portálon explicit módon [véglegesen törölhet egy közelmúltban törölt felhasználót](../fundamentals/active-directory-users-restore.md) az időszak lejárta előtt.  Egy felhasználó végleges törlése után a felhasználó későbbi adatai el lesznek távolítva az aktív hozzáférési felülvizsgálatokból.  A törölt felhasználókkal kapcsolatos naplózási információk az auditnaplóban maradnak.

## <a name="next-steps"></a>További lépések

- [Felhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-user-access-with-access-reviews.md)
- [Vendégfelhasználói hozzáférés felügyelete az Azure AD hozzáférési felülvizsgálatokkal](manage-guest-access-with-access-reviews.md)
- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása](create-access-review.md)
- [Hozzáférési felülvizsgálat létrehozása Azure AD rendszergazdai szerepkörrel rendelkező felhasználókhoz](../privileged-identity-management/pim-how-to-start-security-review.md)
