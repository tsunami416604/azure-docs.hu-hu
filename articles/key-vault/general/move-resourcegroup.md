---
title: Tároló áthelyezése másik erőforráscsoporthoz Azure Key Vault | Microsoft Docs
description: Útmutató a kulcstartó más erőforráscsoporthoz való áthelyezéséhez.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: fe8051d551077666c06ac033f22303fd643ac602
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585733"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Azure Key Vault áthelyezése az erőforráscsoportok között

## <a name="overview"></a>Áttekintés

A kulcstartók áthelyezése az erőforráscsoportok között egy támogatott kulcstartó-funkció. Ha egy kulcstartót az erőforráscsoportok között helyez át, a Key Vault tűzfala vagy a hozzáférési házirend konfigurációja nem lesz hatással. A csatlakoztatott alkalmazásoknak és egyszerű szolgáltatásoknak továbbra is a kívánt módon kell működniük.

## <a name="design-considerations"></a>Kialakítási szempontok

Előfordulhat, hogy a szervezete az erőforráscsoport szintjén végrehajtotta a kényszerítést vagy a kizárást Azure Policy. Előfordulhat, hogy az erőforráscsoport olyan házirend-hozzárendeléseket tartalmaz, amelyekben a kulcstartó jelenleg létezik, és azt az erőforráscsoportot, amelyben a kulcstartót helyezi át. A házirend-követelmények ütközésének lehetősége van az alkalmazások megszakítására.

### <a name="example"></a>Példa

Van egy olyan alkalmazás, amely a Key vaulthoz csatlakozik, és két évig érvényes tanúsítványokat hoz létre. Az erőforráscsoport, amelyen át szeretné helyezni a kulcstartót, rendelkezik egy olyan szabályzat-hozzárendeléssel, amely letiltja az egy évnél hosszabb ideig érvényes tanúsítványok létrehozását. Miután áthelyezte a kulcstartót az új erőforráscsoporthoz, az Azure-szabályzat-hozzárendelés letiltja azt a műveletet, amely két évre érvényes tanúsítványt hoz létre.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy a Azure Portal Azure Policy lapjára mutat, és tekintse meg az aktuális erőforráscsoport házirend-hozzárendeléseit, valamint azt az erőforráscsoportot, amelybe áthelyezi, és ellenőrizze, hogy nincsenek-e eltérések.

## <a name="procedure"></a>Eljárás

1. Bejelentkezés az Azure Portalra
2. Navigáljon a kulcstartóhoz
3. Kattintson az "áttekintés" fülre
4. Válassza az "áthelyezés" gombot
5. Válassza a "áthelyezés másik erőforráscsoporthoz" lehetőséget a legördülő listából.
6. Válassza ki azt az erőforráscsoportot, amelyben át szeretné helyezni a kulcstartót
7. Az erőforrások áthelyezésével kapcsolatos figyelmeztetés nyugtázása
8. Válassza az OK lehetőséget

Key Vault ekkor kiértékeli az erőforrás-áthelyezés érvényességét, és riasztást küld az esetleges hibákról. Ha nem talál hibát, az erőforrás áthelyezése befejeződik. 
