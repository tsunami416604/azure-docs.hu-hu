---
title: Egyéni vezérlők az Azure AD feltételes hozzáférésben
description: Ismerje meg, hogyan működnek az egyéni vezérlők a Azure Active Directory feltételes hozzáférésben.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d926d027b59a225d76f6a73ab3ee4ec09b595e5
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253307"
---
# <a name="custom-controls-preview"></a>Egyéni vezérlők (előzetes verzió)

Az egyéni vezérlők a Azure Active Directory előzetes verziójú képességei. Egyéni vezérlők használatakor a rendszer átirányítja a felhasználókat egy kompatibilis szolgáltatáshoz, hogy megfeleljenek a Azure Active Directoryon kívüli hitelesítési követelményeknek. Ennek a vezérlőnek a kielégítéséhez a rendszer átirányítja a felhasználó böngészőjét a külső szolgáltatáshoz, végrehajtja a szükséges hitelesítést, majd átirányítja a Azure Active Directoryre. Azure Active Directory ellenőrzi a választ, és ha a felhasználó hitelesítése vagy ellenőrzése sikeres volt, a felhasználó folytatja a feltételes hozzáférés folyamatát.

> [!NOTE]
> További információ az egyéni vezérlési képességre tervezett változásokról: [február 2020](../fundamentals/whats-new.md#upcoming-changes-to-custom-controls)Újdonságok.

## <a name="creating-custom-controls"></a>Egyéni vezérlők létrehozása

Az egyéni vezérlők a jóváhagyott hitelesítésszolgáltatók korlátozott készletével működnek. Egyéni vezérlő létrehozásához először kapcsolatba kell lépnie a használni kívánt szolgáltatóval. Az egyes nem Microsoft-szolgáltatók saját folyamattal és követelményekkel rendelkeznek a szolgáltatás regisztrálásához, előfizetéséhez vagy egyéb részévé tételéhez, és jelezniük kell, hogy a feltételes hozzáféréssel szeretne integrálni. Ezen a ponton a szolgáltató JSON formátumú adatblokkot biztosít Önnek. Ez az információ lehetővé teszi, hogy a szolgáltató és a feltételes hozzáférés együtt működjön a bérlő számára, létrehozza az új vezérlőt, és meghatározza, hogy a feltételes hozzáférés Hogyan állapítható meg, hogy a felhasználók sikeresen végrehajtották-e az ellenőrzést a szolgáltatóval.

Másolja a JSON-adatforrást, majd illessze be a kapcsolódó szövegmezőbe. Ne módosítsa a JSON-t, kivéve, ha explicit módon megértette az Ön által végzett módosítást. A változtatások miatt a szolgáltató és a Microsoft közötti kapcsolat megszakadhat, és előfordulhat, hogy az Ön és a felhasználók is zárolják magukat a fiókból.

Az egyéni vezérlő létrehozásának lehetősége a **feltételes hozzáférés** lap **kezelés** szakaszában található.

![Szabályozás](./media/controls/82.png)

Az **új egyéni vezérlő**elemre kattintva megnyílik egy panel, amely a vezérlőelem JSON-adataihoz tartozó szövegmezővel rendelkezik.  

![Szabályozás](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Egyéni vezérlők törlése

Egyéni vezérlő törléséhez először gondoskodnia kell arról, hogy ne legyen használatban semmilyen feltételes hozzáférési házirendben. Ha elkészült:

1. Ugrás az egyéni vezérlők listára
1. Kattintson a...  
1. Válassza a **Törlés** elemet.

## <a name="editing-custom-controls"></a>Egyéni vezérlők szerkesztése

Egyéni vezérlő szerkesztéséhez törölnie kell a jelenlegi vezérlőt, és létre kell hoznia egy új vezérlőt a frissített információkkal.

## <a name="known-limitations"></a>Ismert korlátozások

Egyéni vezérlők nem használhatók az Identity Protection automatizálásához, amely megköveteli az Azure Multi-Factor Authentication, az Azure AD önkiszolgáló jelszó-visszaállítását (SSPR), a többtényezős hitelesítési jogcím követelményeinek kielégítését, illetve a szerepkörök emelt szintű Identity Managerben (PIM) való emelését.

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

- [Csak jelentési mód](concept-conditional-access-report-only.md)

- [Bejelentkezési viselkedés szimulálása a feltételes hozzáférési What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
