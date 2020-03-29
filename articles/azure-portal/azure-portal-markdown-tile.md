---
title: Egyéni markdown csempe használata az Azure irányítópultjain
description: Megtudhatja, hogy miként adhat hozzá markdown csempét egy Azure-irányítópulthoz statikus tartalom megjelenítéséhez
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310713"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Egyéni tartalom megjelenítéséhez használjon markdown csempét az Azure irányítópultjain

Az Azure-irányítópultokhoz hozzáadhat egy markdown csempét az egyéni statikus tartalom megjelenítéséhez. Megjelenítheti például az alapvető utasításokat, a képet vagy a hivatkozások készletét egy markdown csempén.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Markdown csempe hozzáadása az irányítópulthoz

1. Válassza **ki az irányítópultot** az Azure Portal oldalsávján.

   ![Képernyőkép a portál oldalsávjáról](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Ha egyéni irányítópultokat hozott létre, az irányítópult nézetben a legördülő menüben válassza ki azt az irányítópultot, amelyen az egyéni jelölőcsempe megjelenik. A **Csempetár**megnyitásához kattintson a szerkesztés ikonra .

   ![Az irányítópult szerkesztési nézetét ábrázoló képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. A **Mozaiktárban**keresse meg a **Markdown** nevű csempét, és válassza a **Hozzáadás**lehetőséget. A csempe hozzáadódik az irányítópulthoz, és megnyílik a **Markdown szerkesztése** ablaktábla.

1. Adja meg a **Cím** és **a Felirat**mező értékét, amely egy másik mezőre lépés után jelenik meg a csempén.

   ![Képernyőkép a cím és a felirat beírásának eredményeiről](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Válassza ki a markdown tartalom: **Szövegközi szerkesztés** vagy **Tartalom beszúrása URL-cím használatával lehetőség ként.**

   - Válassza **a Szövegközi szerkesztés lehetőséget,** ha közvetlenül szeretné megadni a markdown parancsot.

      ![A beágyazott tartalom bevitelét bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Ha az interneten tárolt meglévő markdown-tartalmat szeretné használni, jelölje be a **Tartalom beszúrása URL-cím használatával** jelölőnégyzetet.

      ![Az URL-cím beírását bemutató képernyőkép](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > A nagyobb biztonság érdekében létrehozhat egy markdown fájlt, és tárolhatja egy [Azure storage-fiók blobjában, ahol a titkosítás engedélyezve van,](../storage/common/storage-service-encryption.md)majd mutasson a fájlra az URL-beállítás használatával. A markdown tartalom titkosítva van a tárfiók titkosítási beállításaival. Csak a fájlhoz engedéllyel rendelkező felhasználók láthatják a markdown-tartalmat az irányítópulton.

1. A **Kész** lehetőséget választva elvetheti a **Markdown szerkesztése** ablaktáblát. A tartalom megjelenik a Markdown csempén, amelyet a jobb alsó sarokban lévő fogópont húzásával átméretezhet.

   ![Képernyőkép az egyéni markdown csempéről](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown-tartalom funkciók és korlátozások

A markdown csempén az egyszerű szöveg, a Markdown szintaxis és a HTML-tartalom tetszőleges kombinációját használhatja. Az Azure Portal egy _megjelölt_ nyílt forráskódú könyvtárat használ a tartalom HTML-vé alakításához, amely a csempén jelenik meg. A _megjelölt_ HTML-t a portál előre feldolgozza a renderelés előtt. Ezzel a lépéssel biztosíthatja, hogy a testreszabás ne befolyásolja a portál biztonságát vagy elrendezését. Az előfeldolgozás során a HTML bármely olyan része, amely potenciális fenyegetést jelent, eltávolításra kerül. A portál a következő tartalomtípusokat nem engedélyezi:

* JavaScript `<script>` – címkék és inline JavaScript értékelések eltávolításra kerülnek.
* iframe `<iframe>` - címkék törlődnek.
* Stílus `<style>` - címkék törlődnek. A HTML-elemek szövegközi stílusattribútumai hivatalosan nem támogatottak. Előfordulhat, hogy egyes szövegközi stíluselemek működnek az Ön számára, de ha zavarják a portál elrendezését, bármikor leállhatnak. A Markdown csempe alapszintű statikus tartalomhoz készült, amely a portál alapértelmezett stílusait használja.

## <a name="next-steps"></a>További lépések

* Egyéni irányítópult létrehozásához olvassa el az [Irányítópultok létrehozása és megosztása az Azure Portalon című témakört.](../azure-portal/azure-portal-dashboards.md)
