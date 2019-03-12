---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 57cec39bde460c6079091490acf541761c61e003
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553633"
---
Nincs egyes objektumtípusoknál az Azure Policy maximális számát. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 250 |
| Hatókör | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1,000 |
| Hatókör | Szabályzat vagy a kezdeményezés-hozzárendelést | 100 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 100 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Szabályzat vagy a kezdeményezés-hozzárendelést | Kizárások (notScope) | 250 |
| Szabályzatbeli szabály | Beágyazott feltételek | 512 |
