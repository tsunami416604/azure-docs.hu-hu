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
ms.openlocfilehash: 0a54dfdb810ea578c1e7c8fcc7ca0343e72164ae
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50964601"
---
Az Azure Policy egyes objektumtípusainak van egy maximális száma. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 250 |
| Hatókör | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1000 |
| Hatókör | Szabályzat-/Kezdeményezés-hozzárendelések | 100 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 100 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Szabályzat-/Kezdeményezés-hozzárendelések | Kizárások (notScope) | 250 |
| Szabályzat szabálya | Beágyazott feltételes értékek | 512 |
