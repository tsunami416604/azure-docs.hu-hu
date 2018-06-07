---
title: Hozzáférés-vezérlés beállítása az Azure Cosmos DB |} Microsoft Docs
description: 'Útmutató: a hozzáférés-vezérlés beállítása az Azure Cosmos-Adatbázisba.'
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 2dae2b6291aa7ce18cc6f612b25cd5bdcc1ba753
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611143"
---
# <a name="access-control-in-azure-cosmos-db"></a>Hozzáférés-vezérlés az Azure Cosmos-Adatbázisba

Azure Cosmos DB fiók olvasási hozzáférés a felhasználói fiók hozzáadásához lehet hajtsa végre a következő lépéseket az Azure-portálon előfizetés tulajdonosa.

1. Nyissa meg az Azure-portálon, és válassza ki a Azure Cosmos DB fiókját.
2. Kattintson a **hozzáférés-vezérlés (IAM)** fülre, majd **+ Hozzáadás**.
3. Az a **engedélyek hozzáadása** ablaktáblán, a a **szerepkör** mezőben válassza **Cosmos DB fiók olvasó szerepkört**.
4. Az a **hozzáférés hozzárendelése mezőben**, jelölje be **Azure AD-felhasználó, csoport vagy alkalmazás**.
5. Válassza ki a felhasználó, csoport vagy alkalmazás a címtárban, amelyhez hozzáférést szeretne.  A címtár megjelenített név, e-mail cím vagy objektumazonosítók kereshet.
    A kiválasztott felhasználó, csoport vagy az alkalmazás megjelenik a kijelölt tagok listája.
6. Kattintson a **Save** (Mentés) gombra.

Az entitás elolvashatja Azure Cosmos DB erőforrásokat.
