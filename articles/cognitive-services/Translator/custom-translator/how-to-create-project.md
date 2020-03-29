---
title: Hogyan hozzunk létre egy projektet - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy projektet az Azure Cognitive Services egyéni fordítójában.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836568"
---
# <a name="create-a-project"></a>Projekt létrehozása

A projekt modellek, dokumentumok és tesztek tárolója. Minden projekt automatikusan tartalmazza az adott munkaterületre feltöltött összes dokumentumot, amely a megfelelő nyelvi párral rendelkezik.

A projekt létrehozása az első lépés a modell készítése felé.

## <a name="create-a-project"></a>Projekt létrehozása:

1.  Az [Egyéni fordító](https://portal.customtranslator.azure.ai) portálon kattintson a Projekt létrehozása elemre.

    ![Projekt létrehozása](media/how-to/how-to-create-project.png)

2.  A párbeszédpanelen adja meg a projekttel kapcsolatos alábbi részleteket:

    a.  Projektneve (kötelező): Adjon egyedi, tartalmas nevet a projektnek. Nem szükséges megemlíteni a címben szereplő nyelveket.

    b.  Leírás: Rövid összefoglaló a projektről. Ez a leírás nincs hatással az egyéni fordító vagy az eredményül kapott egyéni rendszer viselkedésére, de segíthet különbséget tenni a különböző projektek között.

    c.  Nyelvpár (kötelező): Válassza ki azt a nyelvet, amelyről fordítást hoz.

    d.  Kategória (kötelező): Válassza ki a projektnek leginkább megfelelő kategóriát. A kategória a lefordítani kívánt dokumentumok terminológiáját és stílusát írja le.

    e.  Kategória leírása: Ebben a mezőben jobban leírhatja azt a mezőt vagy iparágat, amelyben dolgozik. Ha például az Ön kategóriája az orvostudomány, hozzáadhat egy adott dokumentumot, például egy műtétet vagy gyermekgyógyászatot. A leírás nincs hatással az egyéni fordító vagy az eredményül kapott egyéni rendszer viselkedésére.

    f.  Projektcímke: A [projektcímke](workspace-and-project.md#project-labels) megkülönbözteti az azonos nyelvi párt és kategóriával rendelkező projekteket. Ajánlott eljárásként *csak* akkor használjon címkét, ha több projektet tervez ugyanabba a nyelvpárba és ugyanabba a kategóriába, és ezeket a projekteket egy másik CategoryID azonosítóval szeretné elérni. Ne használja ezt a mezőt, ha csak egy kategóriára épít rendszereket. A projektcímke nem kötelező, és nem hasznos a nyelvi párok megkülönböztetéséhez. Ugyanazt a címkét több projekthez is használhatja.

    ![Projekt létrehozása párbeszédpanel](media/how-to/how-to-create-project-dialog.png)

3.  Kattintson a Létrehozás gombra

## <a name="view-project-details"></a>Projektrészleteinek megtekintése

Az Egyéni fordító céloldala a munkaterület első 10 projektjét jeleníti meg. Megjeleníti a projekt nevét, nyelvpárját, kategóriáját, állapotát és BLEU pontszámát.

A projekt kiválasztása után a projekt lapon a következők jelennek meg:

- CategoryID: A CategoryID a WorkspaceID, a projektcímke és a kategóriakód összefűzésével jön létre. A CategoryID a Szövegfordító API-val egyéni fordítások lekérni.

- Vonat gomb: Ezzel a gombbal indítsa el [a képzés a modell.](how-to-train-model.md)

- Dokumentumok hozzáadása gomb: Ezzel a gombbal [dokumentumokat tölthet fel.](how-to-upload-document.md)

- Dokumentumok szűrése gomb: Ezzel a gombbal szűrheti és kereshet egy adott dokumentumot.Filter documents button: Use this button to filter and search for specific document(s).

    ![Projektrészleteinek megtekintése](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>További lépések

- További információ [a projekt kereséséhez, szerkesztéséhez és törléséhez.](how-to-search-edit-delete-projects.md)
- További információ [arról, hogyan tölthet fel dokumentumot](how-to-upload-document.md) fordítási modellek létrehozásához.
