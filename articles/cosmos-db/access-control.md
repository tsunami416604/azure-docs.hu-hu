---
title: "Hozzáférés-vezérlés beállítása az Azure Cosmos DB |} Microsoft Docs"
description: "Útmutató: a hozzáférés-vezérlés beállítása az Azure Cosmos-Adatbázisba."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: mimig
ms.openlocfilehash: 5ef4a12c8229d8801a5d9749514a69c2c1e19499
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
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
