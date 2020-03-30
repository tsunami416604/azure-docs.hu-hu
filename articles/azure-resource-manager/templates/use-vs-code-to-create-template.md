---
title: Sablonok létrehozása a Visual Studio-kód dal
description: Ismerje meg, hogyan telepítheti és használhatja a Visual Studio-kódot és az Azure Resource Manager Tools bővítményt.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273630"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok létrehozása a Visual Studio-kód dal

Visual Studio Code egy könnyű, többplatformos, nyílt forráskódú szerkesztő. Az Azure Resource Manager sabloneszközök bővítmény egy plugin a Resource Manager sablon fejlesztés. A kiterjesztés nyelvi támogatást ad a sablonokhoz, hogy intellisense-t, szintaxiskiemelést, in-line segítséget és sok más nyelvi funkciót biztosítson. Együtt biztosítják az ajánlott sablonfejlesztési élményt.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

A Visual studio Code támogatja a MacOS, a Windows és a Linux rendszert.  A [Visual Studio Code](https://code.visualstudio.com/)programból telepíthető.

## <a name="install-resource-manager-tools-extension"></a>Az Erőforráskezelő eszközök bővítmény telepítése

1. Nyissa meg a Visual Studio Code-ot.
1. Válassza a bal oldali menü **Bővítmények parancsát.** Vagy a **Nézet** menüben válassza a **Bővítmények** lehetőséget a Bővítmények ablaktábla megnyitásához.

    ![A Visual Studio Code Resource Manager Tools bővítményének telepítése](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. **Erőforrás-kezelő**keresése .
1. Válassza **a Telepítés** lehetőséget az Azure Resource Manager Tools **csoportban.**

## <a name="the-extension-features"></a>A bővítmény funkciói

### <a name="colorization-for-template-language-expressions"></a>Sablonnyelvi kifejezések színezése

A paraméterek, változók, függvények, nevek és kifejezések színkóddal vannak elhangolva, ahogy az a következő képernyőképen látható:

![Visual Studio Code Resource Manager eszközök kiterjesztések színezés](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

A sablon vázlatnézete megkönnyíti a nagy sablonok közötti navigálást.

### <a name="intellisense"></a>Intellisense

Az Erőforrás-kezelő sablonbővítmény ismeri a függvénynevek, paraméterek, változók és hivatkozások lehetséges befejezéséit. Az IntelliSense javaslatok gépelés közben jelennek meg. Ha folytatja a karakterek beírását, a tagok listája (változók, metódusok stb.) csak a beírt karaktereket tartalmazó tagokat tartalmazza. **A Tab** vagy az Enter billentyű **lenyomásával** a kijelölt tagot szúrja be.

- Beépített függvénynevek

    ![Visual Studio Code Resource Manager eszközök kiterjesztések intellisense funkciók](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Paraméterhivatkozások

    ![Visual Studio Code Resource Manager eszközök kiterjesztések intellisense paraméterek](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Változó hivatkozások

    ![Visual Studio Code Resource Manager eszközök kiterjesztések intellisense változók](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup() tulajdonságai

    ![Visual Studio Code Resource Manager eszközök kiterjesztések intellisense funkciók](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Ezen túlmenően, intellisense is működik subscription() tulajdonságok és a tulajdonságok referenciák változók, amelyek objektumok.

### <a name="signature-help-for-function-parameters"></a>Aláírási súgó a függvényparaméterekhez

Ha a függvénynevek fölé viszi az egérmutatót, a bővítmény megjeleníti a függvényparaméterek aláírási súgóját.

![Visual Studio Code Resource Manager eszközök kiterjesztések aláírás funkció](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ugrás a definícióra változó- és paraméterhivatkozások esetén

A definícióra a **Ctrl+Click**billentyűkombinációval vagy a képernyőképen látható ![helyi menü használatával ugorhat a definícióra: A Visual Studio Code Resource Manager eszközbővítményei a definícióra lépnek.](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

A definíciót a **Ctrl+Alt+Click**billentyűkombinációval nyithatja meg.

### <a name="peek-for-variable-and-parameter-definitions"></a>Betekintés változó- és paraméterdefiníciókba

A betekintő szerkesztő megnyitásához használja a helyi menüt az előző képernyőképen látható módon.

A következő képernyőképen a betekintő szerkesztő látható:

![Visual Studio Code Resource Manager eszközök bővítmények peek szerkesztő](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>A változókra és paraméterekre vonatkozó összes hivatkozás megkeresése

Az összes hivatkozás megkereséséhez használja a helyi menüt az előző képernyőképen látható módon.

A következő képernyőkép bemutatja, hogyan vannak kiemelve a hivatkozások:

![A Visual Studio Kóderőforrás-kezelő eszközbővítményei az összes hivatkozást megtalálják](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Változók és paraméterek összes hivatkozásának átnevezése

A változókra és paraméterekre vonatkozó összes hivatkozás átnevezéséhez használja a helyi menüt az előző képernyőképen látható módon.

### <a name="hover-for-parameter-description"></a>Rámutatás a paraméter leírására

![Visual Studio Code Resource Manager eszközök kiterjesztések hover definition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Kapcsos zárójel egyeztetés

A megfelelő zárójelek kiemelve jelennek meg, amint a kurzor az egyik közelébe kerül. Amikor egy kapcsos zárójelre kattint, a megfelelő kapcsos zárójel is kilesz emelve az alábbi képernyőképen látható módon:

![Visual Studio Code Resource Manager eszközök kiterjesztések kapcsos zárójel megfelelő](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Hibák és figyelmeztetések megjelenítése

A kiterjesztés által azonosított hibák a következők:

- Nem definiált paraméterhivatkozások
- Nem definiált változóhivatkozások
- Ismeretlen függvénynevek
- reference() függvényhasználat a változó definíciójában
- A függvényekben lévő argumentumok helytelen száma

A figyelmeztetések a következőket tartalmazzák:

- Nem használt paraméterek
- Nem használt változók

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager-sablonok létrehozásáról az [Oktatóanyag: Az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md)című témakörben olvashat.
- A Visual Studio-kód használatával történő rövid útmutató t, olvassa el a [rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio-kód használatával című témakört.](quickstart-create-templates-use-visual-studio-code.md)
