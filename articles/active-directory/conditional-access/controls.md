---
title: Egyéni vezérlők az Azure AD feltételes hozzáférésében
description: Ismerje meg, hogyan működnek az egyéni vezérlők az Azure Active Directory feltételes hozzáférésében.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8c149279a755eb186a3fdc7891e9b511d18c7f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050549"
---
# <a name="custom-controls-preview"></a>Egyéni vezérlők (előzetes verzió)

Az egyéni vezérlők az Azure Active Directory előzetes verziói. Egyéni vezérlők használata esetén a felhasználók átirányításra kerülnek egy kompatibilis szolgáltatásba, hogy megfeleljenek az Azure Active Directoryn kívüli hitelesítési követelményeknek. A vezérlő teljesítése érdekében a felhasználó böngészője átlesz irányítva a külső szolgáltatásra, elvégzi a szükséges hitelesítést, majd visszairányítja az Azure Active Directoryba. Az Azure Active Directory ellenőrzi a választ, és ha a felhasználó sikeresen hitelesítettvagy érvényesített, a felhasználó folytatja a feltételes hozzáférési folyamat.

> [!NOTE]
> Az Egyéni vezérlés funkció módosításairól a 2020. [What's new update](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)

## <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozása

Az Egyéni vezérlők a jóváhagyott hitelesítésszolgáltatók korlátozott készletével működik együtt. Egyéni vezérlő létrehozásához először vegye fel a kapcsolatot a használni kívánt szolgáltatóval. Minden nem microsoftos szolgáltató saját folyamattal és követelményekkel rendelkezik a regisztrációhoz, az előfizetéshez vagy más módon a szolgáltatás részévé váláshoz, valamint annak jelzéséhez, hogy integrálni kíván a feltételes hozzáféréssel. Ezen a ponton a szolgáltató json formátumban egy adatblokkot biztosít. Ezek az adatok lehetővé teszik a szolgáltató és a feltételes hozzáférés együtt dolgozni a bérlő, létrehozza az új vezérlőt, és meghatározza, hogyan feltételes hozzáférés tudja megmondani, ha a felhasználók sikeresen elvégezték az ellenőrzést a szolgáltatóval.

Másolja a JSON-adatokat, majd illessze be a kapcsolódó szövegdobozba. Ne módosítsa a JSON-t, hacsak nem érti meg kifejezetten a módosítást. Bármilyen módosítás megszakíthatja a kapcsolatot a szolgáltató és a Microsoft között, és potenciálisan zárolhatja Önt és felhasználóit a fiókokból.

Az egyéni vezérlőelem létrehozásának lehetősége a **Feltételes hozzáférés** lap **Kezelés** szakaszában található.

![Vezérlés](./media/controls/82.png)

Az **Új egyéni vezérlő elemre**kattintva megnyílik egy panel, amelyen a vezérlő JSON-adatai nak szövegdoboza található.  

![Vezérlés](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Egyéni vezérlő törléséhez először meg kell győződnie arról, hogy nem használatos a feltételes hozzáférési házirendben. A teljes teljesítményt követően:

1. Ugrás az Egyéni vezérlők listára
1. Kattintson...  
1. Válassza a **Törlés** elemet.

## <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

Egyéni vezérlő szerkesztéséhez törölnie kell az aktuális vezérlőt, és új vezérlőt kell létrehoznia a frissített adatokkal.

## <a name="known-limitations"></a>Ismert korlátozások

Az egyéni vezérlők nem használhatók az Identity Protection olyan automatizálásával, amely azure-beli többtényezős hitelesítést, Azure AD önkiszolgáló jelszó-visszaállítást (SSPR) igényel, megfelel a többtényezős hitelesítési jogcímkövetelményeinek, vagy nem emelheti a szerepköröket a kiemelt jogosultságú Identitáskezelő (PIM).

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)

- [Bejelentkezési viselkedés szimulálása a Feltételes hozzáférés Mi ha eszközzel](troubleshoot-conditional-access-what-if.md)
