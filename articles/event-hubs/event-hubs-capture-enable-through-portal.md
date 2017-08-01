---
title: "Az Azure Event Hubs Capture engedélyezése a portálon keresztül | Microsoft Docs"
description: "Engedélyezze az Event Hubs Capture funkciót az Azure Portal használatával."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017


---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Az Event Hubs Capture engedélyezése az Azure Portal használatával

A Capture-t konfigurálhatja az eseményközpont létrehozásakor az [Azure-portálon](https://portal.azure.com). A Capture engedélyezéséhez kattintson a **Be** gombra a portál **Eseményközpont létrehozása** paneljén. Ezután konfigurálhat egy tárfiókot és tárolót a panel **Tároló** szakaszára kattintva. Mivel az Event Hubs Capture szolgáltatások közötti hitelesítést használ a tárolóval, nem kell megadnia egy tárfiók kapcsolati sztringjét. Az erőforrás-választó automatikusan kiválasztja az erőforrás URI-azonosítóját a tárfiókhoz. Az Azure Resource Manager használatakor explicit módon meg kell adnia ezt az URI-t karakterláncként.

Az időkeret alapértelmezett értéke 5 perc. A minimális értéke 1, a maximális 15. A **Méret** ablak 10–500 MB tartománnyal rendelkezik.

![][1]

## <a name="adding-capture-to-an-existing-event-hub"></a>A Capture hozzáadása egy meglévő Event Hubs-eseményközponthoz

A Capture konfigurálható az Event Hubs névterekben található meglévő eseményközpontokon. Ez a funkció nem érhető el a régebbi **üzenetkezelési** vagy **vegyes** típusú névterek esetén. A Capture meglévő eseményközpontban történő engedélyezéséhez, vagy a Capture beállításainak módosításához kattintson a névtérre az **Alapvető szolgáltatások** panel betöltéséhez, majd kattintson arra az eseményközpontra, amelyet engedélyezni kíván, vagy amelyhez módosítani szeretné a Capture-beállítást. Végül kattintson a megnyitott panel **Tulajdonságok** részére, amint az az alábbi ábrán látható:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## <a name="next-steps"></a>Következő lépések

Az Azure Resource Manager-sablonok használatával is konfigurálhatja az Event Hubs Capture-t. További információkért lásd: [Rögzítés funkció engedélyezése az Azure Resource Manager-sablonjának használatával](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

