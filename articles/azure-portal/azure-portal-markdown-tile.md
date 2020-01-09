---
title: Egyéni Markdown csempe használata az Azure-irányítópultokon
description: Megtudhatja, hogyan adhat hozzá Markdown-csempét egy Azure-irányítópulthoz a statikus tartalom megjelenítéséhez
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640142"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Markdown csempe használata az Azure-irányítópultokon egyéni tartalom megjelenítéséhez

Az egyéni, statikus tartalmak megjelenítéséhez hozzáadhat egy Markdown csempét az Azure-irányítópultokhoz. Megjelenítheti például az alapszintű utasításokat, a képeket vagy a Markdown csempével rendelkező hiperhivatkozásokat.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Markdown csempe hozzáadása az irányítópulthoz

1. Válassza ki az **irányítópultot** a Azure Portal oldalsávon. Ha létrehozta az egyéni irányítópultokat, az irányítópult nézetben a legördülő listából válassza ki azt az irányítópultot, amelyben az egyéni Markdown csempének kell megjelennie. Kattintson a Szerkesztés ikonra a **csempék gyűjteményének**megnyitásához.

   ![Az irányítópult szerkesztési nézetét bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. A **csempék gyűjteményében**keresse meg a **Markdown** nevű csempét, és kattintson a **Hozzáadás**gombra. Ekkor megjelenik a csempe az irányítópulton, és megnyílik a **Markdown szerkesztése** panel.

1. Szerkessze a **cím**, az **alcím**és a **tartalom** mezőket a csempe testreszabásához. Az itt látható példában a Markdown csempe úgy lett szerkesztve, hogy az egyéni ügyfélszolgálat adatait jelenítse meg.

   ![A Markdown csempe szerkesztési nézetét bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. A **kész** gombra kattintva zárja be a **Markdown szerkesztése** panelt. A tartalom megjelenik a Markdown csempén, amelyet aztán a jobb alsó sarokban lévő fogantyú húzásával lehet átméretezni.

   ![Az egyéni Markdown csempét ábrázoló képernyőfelvétel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown és korlátozások

A Markdown csempén bármilyen egyszerű szöveg, Markdown szintaxis és HTML-tartalom kombinációja használható. A Azure Portal egy _jelölt_ nevű nyílt forráskódú függvénytárat használ, amely a csempén látható HTML-re alakítja át a tartalmat. A _megjelöléssel_ létrehozott HTML-kódot a portál előre feldolgozta a Megjelenítés előtt. Ez a lépés segít meggyőződni arról, hogy a Testreszabás nem befolyásolja a portál biztonságát vagy elrendezését. Az előzetes feldolgozás során a HTML bármely olyan része törlődik, amely potenciális fenyegetést jelent. A portál a következő típusú tartalmakat nem engedélyezi:

* JavaScript – `<script>` címkék és beágyazott JavaScript-értékelések el lesznek távolítva.
* iframe elemek – `<iframe>` címkék lesznek eltávolítva.
* Style – a `<style>` címkék el lesznek távolítva. A HTML-elemekben található beágyazott stílusú attribútumok nem támogatottak hivatalosan. Előfordulhat, hogy néhány beágyazott stílusú elem működik Önnek, de ha a portál elrendezését zavarja, bármikor leállíthatja a munkát. A Markdown csempe olyan alapszintű, statikus tartalomhoz készült, amely a portál alapértelmezett stílusait használja.

## <a name="next-steps"></a>Következő lépések

* Egyéni irányítópult létrehozásához tekintse meg [az irányítópultok létrehozása és megosztása a Azure Portal](../azure-portal/azure-portal-dashboards.md)
