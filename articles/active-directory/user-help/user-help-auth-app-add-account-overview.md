---
title: A fiókok Microsoft Authenticator alkalmazáshoz való hozzáadásának áttekintése – Azure Active Directory | Microsoft Docs
description: A munkahelyi és a személyes fiókok Microsoft Authenticator alkalmazásba való felvételének áttekintése kétfaktoros ellenőrzéshez.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: olhaun
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: d16699729e62953cb2369f39ecfe759aab663caf
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382647"
---
# <a name="overview-about-adding-accounts-to-the-microsoft-authenticator-app"></a>A fiókok Microsoft Authenticator alkalmazásba való felvételének áttekintése

A Microsoft Authenticator alkalmazás segít bejelentkezni a fiókjába, ha kétfaktoros ellenőrzést használ. A kétfaktoros ellenőrzés segítségével biztonságosabban férhet hozzá a fiókokhoz, különösen a bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejthető, ellopott vagy sérült, a kétfaktoros ellenőrzés egy további biztonsági lépés, amely segít a fiók védelmében azáltal, hogy nehezebbé teszi a más személyek számára a betörést.

> [!Important]
> A Microsoft Authenticator alkalmazás minden olyan alkalmazással működik, amely kétfaktoros ellenőrzést használ, és minden olyan fiókot, amely támogatja az időalapú egyszeri jelszavas (TOTP) szabványokat.
>
> Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

A Microsoft Authenticator alkalmazást többféleképpen is használhatja, többek között:

- A felhasználónévvel és a jelszóval való bejelentkezés után adjon meg egy második ellenőrzési módszert.

- Jelszó kérése nélkül biztosíthatja a bejelentkezést a felhasználónévvel és a mobileszköz használatával az ujjlenyomattal, a Face vagy a PIN-kóddal.

## <a name="what-account-types-can-i-add"></a>Milyen típusú fiókokat adhatok hozzá

A kétfaktoros ellenőrzés bekapcsolható sok olyan fiók esetében, amelyet naponta használ, beleértve a következőket:

| Fióktípus | Leírás |
| ------------ | ----------- |
| Munkahelyi vagy iskolai fiókok | Az a fiók, amelyet a munkahelyi vagy iskolai szervezete adott alain@contoso.commeg, például:. |
| Személyes fiókok | A saját maga által megnyitott Microsoft-és nem Microsoft-fiókok, például a Outlook.com, az Xbox LIVE, a Google, a Facebook és egyebek. |

## <a name="in-this-section"></a>Ebben a szakaszban

| Cikk |Leírás |
| ------|------------|
| [Munkahelyi vagy iskolai fiók hozzáadása](user-help-auth-app-add-work-school-account.md) | Ismerteti, hogyan adhat hozzá munkahelyi vagy iskolai fiókot a Microsoft Authenticator alkalmazáshoz kétfaktoros ellenőrzéshez. |
| [Személyes Microsoft-fiók hozzáadása](user-help-auth-app-add-personal-ms-account.md) | Ismerteti, hogyan adható hozzá a Microsoft személyes fiókjai a kétfaktoros ellenőrzéshez és a telefonos bejelentkezéshez (más néven a jelszó nélküli bejelentkezéshez). |
| [A nem Microsoft-fiók hozzáadása](user-help-auth-app-add-non-ms-account.md) | Útmutató a nem a Microsofttól származó személyes fiókok hozzáadásához a kétfaktoros ellenőrzéshez. |
| [Fiók manuális hozzáadása](user-help-auth-app-add-account-manual.md) | Ismerteti, hogyan lehet manuálisan hozzáadni a fiókját a Microsoft Authenticator alkalmazáshoz, ha a kamera nem tudja rögzíteni a QR-kódot. |
