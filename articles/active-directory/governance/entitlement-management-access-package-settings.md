---
title: Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan oszthat meg olyan hivatkozást, amely hozzáférési csomagot kér Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94d32bf26def6832f51b4e09c697bca885162dc2
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174421"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása az Azure AD-jogosultságok kezelésében

A címtár legtöbb felhasználója bejelentkezhet a saját hozzáférési portálra, és automatikusan megtekintheti az általa kérhető hozzáférési csomagok listáját. Ha azonban a külső üzleti partnerek olyan felhasználói, akik még nem szerepelnek a címtárban, el kell küldeni nekik egy hivatkozást, amely a hozzáférési csomag igénylésére használható. 

Ha a hozzáférési csomag katalógusa engedélyezve van a [külső felhasználók számára](entitlement-management-catalog-create.md) , és rendelkezik szabályzattal a [külső felhasználó címtárához](entitlement-management-access-package-request-policy.md), a külső felhasználó a saját hozzáférési portál hivatkozásra kattintva kérheti le a hozzáférési csomagot.

## <a name="share-link-to-request-an-access-package"></a>Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon másolja a **saját hozzáférési portál hivatkozást**.

    ![Hozzáférési csomag áttekintése – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

    Fontos, hogy átmásolja a teljes saját hozzáférési portál hivatkozást, amikor egy belső üzleti partnernek küldi el. Ezzel biztosíthatja, hogy a partner hozzáférjen a címtár portálához a kérelem elvégzéséhez. A hivatkozás `myaccess` karakterrel kezdődik, tartalmaz egy könyvtári mutatót, és egy hozzáférési csomag azonosítójával végződik.

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Küldjön e-mailt, vagy küldje el a külső üzleti partnerének mutató hivatkozást. Megoszthatják a hivatkozást a felhasználókkal a hozzáférési csomag igényléséhez.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomaghoz való hozzáférés kérése](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)