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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
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
