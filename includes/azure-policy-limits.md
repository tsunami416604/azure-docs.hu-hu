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
ms.openlocfilehash: c3365450c90c4fda37884e8998fad70f5d164244
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006502"
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
| Szabályzat-/Kezdeményezés-hozzárendelések | Kizárások (notScope) | 100 |
| Szabályzat szabálya | Beágyazott feltételes értékek | 512 |
