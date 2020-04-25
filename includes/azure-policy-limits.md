---
title: fájl belefoglalása
description: fájl belefoglalása
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/23/2020
ms.author: dacoulte
ms.openlocfilehash: 04e607296478520298a2febab61a7edac911a59c
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132055"
---
A Azure Policyhoz tartozó egyes objektumtípusok maximális száma. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 500 |
| Hatókör | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1,000 |
| Hatókör | Házirend-vagy kezdeményezési hozzárendelések | 100 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 100 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Házirend-vagy kezdeményezési hozzárendelések | Kizárások (notScope) | 400 |
| Házirend-szabály | Beágyazott feltételesség | 512 |
| Szervizelési feladat | További források | 500 |
