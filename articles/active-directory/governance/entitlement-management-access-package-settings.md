---
title: Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan oszthat meg olyan hivatkozást, amely hozzáférési csomagot kér Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392331"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférési csomag igénylésére szolgáló hivatkozás megosztása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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