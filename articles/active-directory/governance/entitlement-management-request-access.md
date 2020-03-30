---
title: Hozzáférési csomag kérése – Azure AD-jogosultságkezelés
description: Megtudhatja, hogy miként kérhet hozzáférést egy hozzáférési csomaghoz az Azure Active Directory jogosultságkezelési szolgáltatásában a Saját hozzáférési portál használatával.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261748"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférés kérése egy hozzáférési csomaghoz az Azure AD-jogosultságkezelésben

Az Azure AD jogosultságkezelés, egy hozzáférési csomag lehetővé teszi az erőforrások és szabályzatok egyszeri beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartama alatt. 

A hozzáférési csomagkezelő konfigurálhatja úgy a házirendeket, hogy a felhasználók nak jóváhagyásra van szükségük a csomagokhoz való hozzáféréshez. A hozzáférési csomaghoz hozzáférést igénylő felhasználók hozzáférési kérelmet nyújthatnak be a hozzáféréshez. Ez a cikk a hozzáférési kérelmek elküldésének módját ismerteti.

## <a name="sign-in-to-the-my-access-portal"></a>Bejelentkezés a Saját Hozzáférés portálra

Az első lépés az, hogy jelentkezzen be a My Access portál, ahol hozzáférést kérhet egy hozzáférési csomaghoz.

**Előfeltételi szerepkör:** Kérelmező

1. Keressen egy e-mailt vagy egy üzenetet attól a projekt- vagy vállalkozásmenedzsertől, akivel dolgozik. Az e-mailnek tartalmaznia kell egy hivatkozást arra a hozzáférési csomagra, amelyhez hozzáférésre lesz szüksége. A kapcsolat `myaccess`a programmal kezdődik, tartalmaz egy címtár-emlékeztetőt, és egy hozzáférési csomag azonosítójával végződik.  (Az Egyesült Államok kormánya, a domain lehet `https://myaccess.microsoft.us` helyette.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Nyissa meg a hivatkozást.

1. Jelentkezzen be a Saját hozzáférés portálra.

    Ügyeljen arra, hogy a szervezeti (munkahelyi vagy iskolai) fiókot használja. Ha nem vagy biztos benne, érdeklődj a projektednél vagy a vállalkozásmenedzserednél.

## <a name="request-an-access-package"></a>Hozzáférési csomag kérése

Miután megtalálta a hozzáférési csomagot a Saját Hozzáférés portálon, kérelmet nyújthat be.

**Előfeltételi szerepkör:** Kérelmező

1. Keresse meg a hozzáférési csomagot a listában.  Ha szükséges, kereshet egy keresési karakterlánc beírásával, majd a **Név**, **Katalógus**vagy **Erőforrások** szűrő kiválasztásával.

    ![Saját Hozzáférési portál – Erőforrás-keresés](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Kattintson a pipára a hozzáférési csomag kijelöléséhez.

1. A **Hozzáférés kérése** ablaktábla megnyitásához kattintson a Hozzáférés kérése elemre.

    ![Saját Hozzáférési portál – Access-csomagok](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Ha az **Üzleti indoklás** mező megjelenik, írja be a hozzáférés szükségességének indoklását.

1. Ha **a Kérelem adott időszakra?** engedélyezve van, válassza az **Igen** vagy **a Nem**lehetőséget.

1. Ha szükséges, adja meg a kezdő és a záró dátumot.

    ![Saját hozzáférési portál – Hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Ha elkészült, kattintson **a Küldés** gombra a kérelem elküldéséhez.

1. Kattintson **az Előzmények kérése** gombra a kérések és az állapot listájának megtekintéséhez.

    Ha a hozzáférési csomag jóváhagyást igényel, a kérelem most függőben lévő jóváhagyási állapotban van.

### <a name="select-a-policy"></a>Szabályzat kiválasztása

Ha olyan hozzáférési csomaghoz kér hozzáférést, amely több, több házirendet is alkalmaz, előfordulhat, hogy a rendszer megkéri, hogy válasszon ki egy házirendet. Egy hozzáférésicsomag-kezelő például konfigurálhat egy hozzáférési csomagot két házirenddel a belső alkalmazottak két csoportjához. Előfordulhat, hogy az első házirend 60 napig engedélyezi a hozzáférést, és jóváhagyást igényel. A második házirend 2 napig engedélyezheti a hozzáférést, és nem igényel jóváhagyást. Ha ebben a forgatókönyvben találkozik, ki kell választania a használni kívánt házirendet.

![Saját hozzáférési portál – Hozzáférés kérése - több szabályzat](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Kérelem újraküldése

Amikor hozzáférést kér egy hozzáférési csomaghoz, előfordulhat, hogy a kérés tanusítják, vagy a kérés lejár, ha a jóváhagyók nem válaszolnak időben. Ha hozzáférésre van szüksége, próbálja meg újra, majd küldje el újra a kérelmet. Az alábbi eljárás bemutatja, hogyan küldhet újra hozzáférési kérelmet:

**Előfeltételi szerepkör:** Kérelmező

1. Jelentkezzen be a **Saját hozzáférés** portálra.

1. Kattintson az **előzmények kérése** parancsra a bal oldali navigációs menüben.

1. Keresse meg azt a hozzáférési csomagot, amelyhez újra kérelmet küld be.

1. A hozzáférési csomag kijelöléséhez kattintson a pipára.

1. Kattintson a kék **Nézet** hivatkozásra a kijelölt hozzáférési csomag jobb oldalán.
    
    ![Hozzáférési csomag kiválasztása és nézethivatkozás](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    A hozzáférési csomag kéréselőzményeivel jobbra megnyílik egy ablaktábla.
    
    ![Az Újraküldés gomb kiválasztása](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Kattintson az **Újraküldés** gombra az ablaktábla alján.

## <a name="cancel-a-request"></a>Kérelem visszavonása

Ha hozzáférési kérelmet küld, és a kérelem még **mindig függőben lévő jóváhagyási** állapotban van, visszavonhatja a kérelmet.

**Előfeltételi szerepkör:** Kérelmező

1. A Saját hozzáférés portál bal oldalán kattintson az **Előzmények kérése** parancsra a kérések listájának és állapotának megtekintéséhez.

1. Kattintson **View** a megszakítani kívánt kérelem nézethivatkozására.

1. Ha a kérelem még **függőben lévő jóváhagyási** állapotban van, a Kérelem **visszavonása gombra** kattintva visszavonhatja a kérést.

    ![Saját hozzáférési portál – Kérés visszavonása](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kattintson **az Előzmények kérése gombra** a kérelem megszakításának megerősítéséhez.

## <a name="next-steps"></a>További lépések

- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
- [Folyamat- és e-mail-értesítések kérése](entitlement-management-process.md)
