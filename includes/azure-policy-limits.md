---
title: fájlbefoglalás
description: fájlbefoglalás
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854078"
---
A Azure Policyhoz tartozó egyes objektumtípusok maximális száma. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 500 |
| Hatókör | Kezdeményezési definíciók | 200 |
| Bérlő | Kezdeményezési definíciók | 2500 |
| Hatókör | Házirend-vagy kezdeményezési hozzárendelések | 200 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 1000 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Házirend-vagy kezdeményezési hozzárendelések | Kizárások (notScope) | 400 |
| Házirend-szabály | Beágyazott feltételesség | 512 |
| Szervizelési feladat | Erőforrások | 500 |
