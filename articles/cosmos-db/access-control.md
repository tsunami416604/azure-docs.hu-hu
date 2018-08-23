---
title: Hozzáférés-vezérlés beállítása az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a hozzáférés-vezérlés az Azure Cosmos DB-ben.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: rafats
ms.openlocfilehash: 0d4595bcf8a9f009dce928d3f3cbc442328ec39b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058701"
---
# <a name="access-control-in-azure-cosmos-db"></a>Hozzáférés-vezérlés az Azure Cosmos DB-hez

Azure Cosmos DB fiók olvasói hozzáférés hozzáadása a felhasználói fiókjával, rendelkeznie egy előfizetés tulajdonosa, hajtsa végre az alábbi lépéseket az Azure Portalon.

1. Nyissa meg az Azure Portalon, és válassza ki az Azure Cosmos DB-fiókját.
2. Kattintson a **hozzáférés-vezérlés (IAM)** fülre, majd **+ Hozzáadás**.
3. Az a **engedélyek hozzáadása** ablaktáblán, a a **szerepkör** jelölje ki **Cosmos DB-fiók olvasói szerepköre**.
4. Az a **hozzáférés hozzárendelése a boxba**válassza **az Azure AD-felhasználó, csoport vagy alkalmazás**.
5. Válassza ki a címtárban, amelyhez hozzáférést szeretne a felhasználó, csoport vagy alkalmazás.  A címtár megjelenített név, e-mail címét vagy objektumazonosítók kereshet.
    A kiválasztott felhasználó, csoport vagy alkalmazás a kijelölt tagok listájában jelenik meg.
6. Kattintson a **Save** (Mentés) gombra.

Az entitás már képes olvasni az Azure Cosmos DB erőforrásokat.
