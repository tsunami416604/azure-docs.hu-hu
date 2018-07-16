---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740559"
---
[Az Azure-szabályzatok](/azure/azure-policy/) segítségével ellenőrizheti, hogy az előfizetés összes erőforrása megfelel-e a vállalati szabványoknak. A szabályzatokkal csökkentheti a költségeket, ha az üzembehelyezési beállításokat a jóváhagyott erőforrástípusokra és SKU-kra korlátozza. Meghatározhat szabályokat és műveleteket az erőforrásaihoz, és a rendszer automatikusan kikényszeríti ezeket az üzembe helyezés során. Szabályozhatja például az üzembe helyezett erőforrások típusát. Korlátozhatja az erőforrások számára jóváhagyott helyeket is. Egyes szabályzatok megtiltanak bizonyos műveleteket, más szabályzatok pedig beállítják egy művelet naplózását.

A szabályzat a szerepköralapú hozzáférés-vezérlést (RBAC) egészíti ki. Az RBAC a felhasználói hozzáférésre összpontosít, és egy alapértelmezés szerint a tiltásra épülő, külön-külön engedélyezési utasításokat fogadó rendszer. A szabályzat az erőforrás tulajdonságaira összpontosít az üzembe helyezés alatt és után. Ez egy alapértelmezés szerint megengedő, külön-külön tiltási utasításokat fogadó rendszer.

A szabályzatokkal kapcsolatban két fogalmat kell megérteni – a *szabályzatdefiníciót* és a *szabályzat-hozzárendelést*. Egy szabályzatdefiníció ismerteti a kikényszeríteni kívánt felügyeleti feltételeket. Egy szabályzat-hozzárendelés érvénybe helyez egy szabályzatdefiníciót egy adott hatókörben.

![Szabályzatok hozzárendelése](./media/resource-manager-governance-policy/policy-concepts.png)

Az Azure számos beépített, módosítás nélkül használható szabályzatdefiníciót tartalmaz. Paraméterértékeket átadva határozhatja meg a hatókörében engedélyezett értékeket. Ha a beépített szabályzatdefiníció nem felel meg az elvárásainak, [létrehozhat egyéni szabályzatdefiníciókat](../articles/azure-policy/create-manage-policy.md).
