---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67133034"
---
A Azure Policyhoz tartozó egyes objektumtípusok maximális száma. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol (a(z) | Mi | Maximális darabszám |
|---|---|---|
| Scope | Szabályzatdefiníciók | 500 |
| Scope | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1,000 |
| Scope | Házirend-vagy kezdeményezési hozzárendelések | 100 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 100 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Házirend-vagy kezdeményezési hozzárendelések | Kizárások (notScope) | 400 |
| Szabályzatbeli szabály | Beágyazott feltételesség | 512 |
