---
title: Az Azure-foglalások használati adatainak letöltésével kapcsolatos problémák elhárítása
description: Ez a cikk segítséget nyújt annak megértéséhez, hogy a fenntartott példány használati adatai miért nem tölthetők le az Azure Portalról, illetve segítséget nyújt a probléma elhárításához is.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147326"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Az Azure-foglalások használati adatainak letöltésével kapcsolatos problémák elhárítása

Ez a cikk segítséget nyújt annak megértéséhez, hogy a fenntartott példány használati adatai miért nem tölthetők le az Azure Portalról, illetve segítséget nyújt a probléma elhárításához is.

## <a name="symptoms"></a>Hibajelenségek

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és lépjen a **Foglalások** területre.
1. Válasszon ki egy foglalást.
1. A foglalás áttekintési oldalán az alapértelmezett nézet az elmúlt hét nap használati adatait jeleníti meg. A foglalás használati adatainak letöltéséhez válassza a **Letöltés CSV-fájlként** lehetőséget.
1. Ha módosítja az időtartományt, a **Letöltés CSV-fájlként** lehetőség inaktívvá válik.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="A Letöltés CSV-fájlként lehetőség inaktív állapotát bemutató példa" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Ok

Technikai korlátozások miatt az Azure nem támogatja a hét napnál hosszabb időszakra vonatkozó adatok letöltését. A sok foglalással rendelkező ügyfelek hosszú időszakai nagy mennyiségű adatot hoznak létre. Az adatok API-kkal történő visszaadása megterheli az Azure-erőforrásokat.

## <a name="solution"></a>Megoldás

Tisztában vagyunk azzal, hogy az ügyfelek hosszabb időszakokra vonatkozó adatokat szeretnének letölteni. Jelenleg azonban nincs mód hosszú időszakokra vonatkozó használati adatok letöltésére.

## <a name="next-steps"></a>Következő lépések

- További információ a fenntartott példányokról: [Mi az az Azure Reservations?](save-compute-costs-reservations.md).