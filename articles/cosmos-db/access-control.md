---
title: Hozzáférés-vezérlés beállítása az Azure Cosmos DB |} Microsoft Docs
description: 'Útmutató: a hozzáférés-vezérlés beállítása az Azure Cosmos-Adatbázisba.'
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 740f1ca560207ada95dd03fbecc7f9940ee7b2a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
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
