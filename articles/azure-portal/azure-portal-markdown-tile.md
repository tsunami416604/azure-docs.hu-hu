---
title: Használja az egyéni markdown csempe az Azure-irányítópultok
description: Markdown csempe felvétele egy Azure-irányítópultra a statikus tartalom megjelenítése
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 29c6dc0f7e71508e7b4ecb4e0f1d5c849df7a68c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904846"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Egyéni tartalom megjelenítéséhez használja az Azure-irányítópultok egy markdown-csempe

Markdown csempe az Azure-irányítópultok megjelenítése egyéni, a statikus tartalom is hozzáadhat. Megjelenítheti például alapvető útmutatás látható, kép vagy egy markdown-csempe hivatkozások csoportja.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Markdown csempe hozzáadása az irányítópulthoz

1. Válassza ki **irányítópult** az Azure portal oldalsávon. Bármilyen egyéni irányítópultok, irányítópult-nézet az létrehozott, ha használatával a listából válassza ki az irányítópultot, az egyéni markdown-csempe meg kell jelennie. A Szerkesztés ikonra nyissa meg a **Csempetárral**.

  ![Képernyőkép megjelenítő irányítópult szerkesztő nézete](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Az a **Csempetárral**, keresse meg a csempe nevű **Markdown** kattintson **Hozzáadás**. A csempe jelenik meg az irányítópultot és a **szerkesztése Markdown** panel nyílik meg.

1. Szerkessze a **cím**, **alcím**, és **tartalom** mezőket, testreszabása a csempe. Az itt látható példában a markdown-csempe megjelenítése egyéni információk az ügyfélszolgálatnak módosítva lett.

  ![Markdown csempe Szerkesztés nézetben ábrázoló képernyőfelvétel](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Válassza ki **kész** elvetése érdekében az **szerkesztése Markdown** ablaktáblán. A tartalom megjelenik a Markdown-csempe, amely majd a jobb alsó sarkában fogantyúját méretezhető.

  ![Bemutató egyéni markdown-csempe képernyőképe](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown-tartalom képességei és korlátozásai

A markdown-csempe szövegként, a Markdown-szintaxis és a HTML-tartalom tetszőleges kombinációját használhatja. Az Azure Portalon használja egy nyílt forráskódú kódtár nevű _megjelölve_ HTML, amely a csempe jelenik meg a tartalom átalakításához. Által előállított HTML _megjelölve_ előre dolgozza fel a portál előtt így jelenik meg. Ezzel a lépéssel győződjön meg arról, hogy a testreszabás során nem érinti a biztonsági vagy a portál elrendezését. Bármely része a HTML-kódot, amely a potenciális fenyegetést jelent előtti a feldolgozás során a rendszer eltávolítja. A következő típusú tartalmat nem engedélyezett a portál által:

* JavaScript – `<script>` címkék és a beágyazott JavaScript értékelések törlődni fog.
* IFRAME - `<iframe>` címkék törlődni fog.
* Style - `<style>` címkék törlődni fog. Soron belüli HTML-elemek attribútumokat hivatalosan nem támogatottak. Előfordulhat, hogy néhány beágyazott prvky style munkára fogásához, de ha zavarják a portál elrendezését, sikerült leáll, bármikor dolgozhat. A Markdown-csempe a portál alapértelmezett stílusait használó, alapszintű, a statikus tartalom szól.

## <a name="next-steps"></a>További lépések

* Egyéni irányítópult létrehozásával kapcsolatban lásd: [irányítópultok létrehozása és megosztása az Azure Portalon](../azure-portal/azure-portal-dashboards.md)
