---
title: Hozzáférés-vezérlés delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan delegálhat hozzáférési szabályozást a rendszergazdáktól a csomagkezelő és a projektmenedzserek eléréséhez, hogy azok képesek legyenek a hozzáférés kezelésére.
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
ms.openlocfilehash: c7a2b6bfdb4904e11ffba3a9fe1097c7f5cfe9d6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170736"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management-preview"></a>Hozzáférési szabályozás delegálása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha a katalógusban lévő hozzáférési csomagok létrehozását és felügyeletét szeretné delegálni, adja hozzá a felhasználókat az Access Package Manager szerepkörhöz. A hozzáférési csomag kezelőjének tisztában kell lennie azzal, hogy a felhasználóknak hozzáférést kell kérniük a katalógus erőforrásaihoz. Ha például egy projekthez katalógust használ, akkor a projekthez tartozó érdeklődő a katalógushoz tartozó Access Package Manager lehet.  Az Access Package Managers nem tud erőforrásokat felvenni a katalógusba, de a hozzáférési csomagokat és házirendeket egy katalógusban kezelheti.  A hozzáférési csomag kezelőjének delegálásakor az adott személy a következőkért felelős:

- A felhasználó által a katalógusban szereplő erőforrásokhoz tartozó szerepkörök
- Kinek lesz hozzáférése
- Akinek jóvá kell hagynia a hozzáférési kérelmeket
- Mennyi ideig tart a projekt?

Ez a videó áttekintést nyújt arról, hogyan delegálhatja a hozzáférési szabályozást a katalógus tulajdonosától a Package Manager eléréséhez.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Katalógus tulajdonosaként delegált egy Access Package Manager

Az alábbi lépéseket követve rendelhet hozzá egy felhasználót az Access Package Manager szerepkörhöz:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez hozzá szeretné adni a rendszergazdákat.

1. A bal oldali menüben kattintson a **szerepkörök és rendszergazdák**elemre.

    ![A szerepkörök és a rendszergazdák katalógusa](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kattintson a **hozzáférési csomag kezelői hozzáadása** lehetőségre a szerepkörök tagjainak kiválasztásához.

1. A tagok hozzáadásához kattintson a **kiválasztás** gombra.

## <a name="remove-an-access-package-manager"></a>Hozzáférési csomag kezelőjének eltávolítása

A következő lépésekkel távolíthat el egy felhasználót a Access Package Manager szerepkörből:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez hozzá szeretné adni a rendszergazdákat.

1. A bal oldali menüben kattintson a **szerepkörök és rendszergazdák**elemre.

1. Vegyen fel egy pipát az eltávolítani kívánt Access Package Manager mellett.

1. Kattintson az **Eltávolítás**gombra.

## <a name="next-steps"></a>Következő lépések

- [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md)
