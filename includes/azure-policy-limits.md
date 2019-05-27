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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119300"
---
Nincs egyes objektumtípusoknál az Azure Policy maximális számát. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol (a(z) | Mi | Maximális darabszám |
|---|---|---|
| Scope | Szabályzatdefiníciók | 250 |
| Scope | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1,000 |
| Scope | Szabályzat vagy a kezdeményezés-hozzárendelést | 100 |
| Szabályzat-definíció | Paraméterek | 20 |
| Kezdeményezésdefiníció | Házirendek | 100 |
| Kezdeményezésdefiníció | Paraméterek | 100 |
| Szabályzat vagy a kezdeményezés-hozzárendelést | Kizárások (notScope) | 250 |
| Szabályzatbeli szabály | Beágyazott feltételek | 512 |
