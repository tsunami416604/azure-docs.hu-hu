---
title: Hozzáférés-vezérlés delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan delegálhat hozzáférési szabályozást a rendszergazdáktól az alkotók és a projektmenedzserek számára, hogy azok képesek legyenek a hozzáférés kezelésére.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170762"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Hozzáférés-vezérlés delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha olyan felhasználók számára kíván delegálni, akik nem rendszergazdák, így saját katalógusokat hozhatnak létre, ezeket a felhasználókat hozzáadhatja az Azure AD jogosultságok kezelése által definiált katalógus-létrehozó szerepkörhöz. Hozzáadhat egyéni felhasználókat, vagy hozzáadhat egy csoportot is, amelynek tagjai ezután létrehozhatnak katalógusokat.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>Rendszergazdaként delegált katalógus létrehozója

Kövesse az alábbi lépéseket egy felhasználó hozzárendeléséhez a katalógus-létrehozó szerepkörhöz.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben, a **jogosultságok kezelése** szakaszban kattintson a **Beállítások**elemre.

1. Kattintson a **Szerkesztés** gombra.

    ![Katalógus-készítők hozzáadására szolgáló beállítások](./media/entitlement-management-delegate/settings-delegate.png)

1. A jogosultság-kezelés **delegálása** szakaszban kattintson a **katalógus-létrehozók hozzáadása** lehetőségre azon felhasználók vagy csoportok kiválasztásához, akikre a jogosultsági felügyeleti szerepkört delegálni kívánja.

1. Kattintson a **Kiválasztás** gombra.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>Következő lépések

- [Erőforrások katalógusának létrehozása és kezelése](entitlement-management-catalog-create.md)
- [Hozzáférési szabályozás delegálása a csomagkezelő-kezelők eléréséhez](entitlement-management-delegate-managers.md)

