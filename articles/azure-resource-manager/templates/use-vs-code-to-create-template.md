---
title: Sablonok létrehozása a Visual Studio Code használatával
description: Ismerje meg a Visual Studio Code és a Azure Resource Manager Tools bővítmény telepítését és használatát.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273630"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával

A Visual Studio Code egy egyszerű, többplatformos, nyílt forráskódú szerkesztő. A Azure Resource Manager sablon-eszközök bővítmény a Resource Manager-sablonok fejlesztéséhez készült beépülő modul. A bővítmény nyelvi támogatást biztosít a sablonokhoz az IntelliSense, a Szintaxiskiemelés, a beágyazott Súgó és számos más nyelvi funkció biztosításához. Együttesen biztosítják a sablonok ajánlott fejlesztési élményét.

## <a name="install-visual-studio-code"></a>A Visual Studio Code telepítése

A Visual Studio Code támogatja a MacOS, a Windows és a Linux rendszert.  A [Visual Studio Code](https://code.visualstudio.com/)-ból is telepíthető.

## <a name="install-resource-manager-tools-extension"></a>Resource Manager-eszközök bővítményének telepítése

1. Nyissa meg a Visual Studio Code-ot.
1. A bal oldali menüben válassza a **bővítmények** lehetőséget. Vagy a **nézet** menüben válassza a **bővítmények** lehetőséget a bővítmények panel megnyitásához.

    ![A Visual Studio Code Resource Manager Tools bővítmény telepítése](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Keresse meg a **Resource Managert**.
1. **Azure Resource Manager eszközök**területen válassza a **telepítés** lehetőséget.

## <a name="the-extension-features"></a>A bővítmény funkciói

### <a name="colorization-for-template-language-expressions"></a>A sablon nyelvi kifejezései színezése

A paraméterek, változók, függvények, nevek és kifejezések színe a következő képernyőképen látható módon történik:

![Visual Studio Code Resource Manager-eszközök bővítményeinek színezése](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

A sablon vázlat nézete megkönnyíti a nagyméretű sablonok közötti átjárást.

### <a name="intellisense"></a>IntelliSense

A Resource Manager-sablon kiterjesztése a függvények neveinek, paramétereinek, változóinak és hivatkozásainak a lehetséges befejezését tudja. A beírt IntelliSense-javaslatok beírásakor. Ha folytatja a karakterek beírását, a rendszer szűri a tagok (változók, metódusok stb.) listáját úgy, hogy csak a beírt karaktereket tartalmazó tagokat tartalmazza. Nyomja le a **Tab** billentyűt, vagy **adja meg** a kijelölt tag beszúrását.

- Beépített függvények nevei

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-függvények](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Paraméterek referenciái

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-paraméterek](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Változó referenciái

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-változók](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- resourceGroup () tulajdonságai

    ![Visual Studio Code Resource Manager Tools Extensions IntelliSense-függvények](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Emellett az IntelliSense az előfizetés () tulajdonságaival és az objektumok változóinak tulajdonságaival is működik.

### <a name="signature-help-for-function-parameters"></a>A függvény paramétereinek aláírása – Súgó

Ha a függvények neve fölé mutat, a bővítmény az aláírás súgóját jeleníti meg a függvények paramétereinek megjelenítéséhez.

![A Visual Studio Code Resource Manager Tools Extensions Signature függvénye](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ugrás a változó és a paraméterek hivatkozásainak definíciója

A definícióra ugorhat a **Ctrl + kattintással**, vagy a helyi menüben, ahogy az a képernyőképen is látható: ![Visual Studio Code Resource Manager-eszközök bővítmények pont a definíció](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

A definíciót a **Ctrl + Alt + kattintás billentyűkombinációval**is megnyithatja.

### <a name="peek-for-variable-and-parameter-definitions"></a>A változók és paraméterek definícióinak betekintése

A bepillantott szerkesztő megnyitásához használja a helyi menüt az előző képernyőképen látható módon.

Az alábbi képernyőfelvételen a betekintési szerkesztő látható:

![Visual Studio Code Resource Manager-eszközök bővítményei betekintés szerkesztő](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>A változók és paraméterek összes hivatkozásának megkeresése

Az összes hivatkozás megkereséséhez használja a helyi menüt az előző képernyőképen látható módon.

Az alábbi képernyőfelvételen a hivatkozások kiemelése látható:

![A Visual Studio Code Resource Manager-eszközök bővítményei megkeresik az összes hivatkozást](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>A változók és paraméterek összes hivatkozásának átnevezése

A változók és paraméterek összes hivatkozásának átnevezéséhez használja a helyi menüt az előző képernyőképen látható módon.

### <a name="hover-for-parameter-description"></a>A paraméter leírásának mutatója

![Visual Studio Code Resource Manager-eszközök bővítményei hover-definíció](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Zárójel egyeztetése

Ha a kurzor közel van az egyikhez, az egyező zárójelek ki vannak emelve. Ha egy kapcsos zárójelre kattint, a hozzá tartozó kapcsos zárójel is ki van emelve, ahogy az alábbi képernyőképen is látható:

![Visual Studio Code Resource Manager-eszközök bővítményeinek megfeleltetése](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Hibák és figyelmeztetések megjelenítése

A bővítmény által azonosított hibák a következők:

- Nem definiált paraméterek hivatkozásai
- Nem definiált változó referenciái
- Ismeretlen függvények nevei
- a Reference () függvény használata a változó definíciójában
- Helytelen számú argumentum a függvényekben

A figyelmeztetések a következők:

- Nem használt paraméterek
- Nem használt változók

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager sablonok létrehozásáról az [oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](template-tutorial-create-first-template.md)című témakörben olvashat bővebben.
- A Visual Studio Code használatával a gyors útmutatóban a következő témakörben talál további információt [: Azure Resource Manager sablonok létrehozása Visual Studio Code használatával](quickstart-create-templates-use-visual-studio-code.md)
