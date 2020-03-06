---
title: Tudásbázisok migrálása – QnA Maker
titleSuffix: Azure Cognitive Services
description: A Tudásbázis áttelepítéséhez az egyik Tudásbázisból kell exportálni, majd importálni egy másikba.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 942932c229ace82a0bf66da7a5421f936b028088
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302559"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Exportálás-importálás segítségével Tudásbázis áttelepítése

A Tudásbázis áttelepítéséhez az egyik Tudásbázisból kell exportálni, majd importálni egy másikba.

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elkezdené, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Új [QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Tudásbázis migrálása a QnA Makerból
1. Jelentkezzen be [QnA Maker portálra](https://qnamaker.ai).
1. Válassza ki az áttelepíteni kívánt forrás-tudásbázist.

1. A **Beállítások** lapon válassza a **Tudásbázis exportálása** lehetőséget egy. TSV fájl letöltéséhez, amely a forrásként szolgáló Tudásbázis – kérdések, válaszok, metaadatok, követő kérések és a kinyert adatforrások nevét tartalmazza.

1. Válassza a **Tudásbázis létrehozása** lehetőséget a felső menüben, majd hozzon létre egy _üres_ tudásbázist. Üres, mert a létrehozásakor nem lesz URL-cím vagy fájl hozzáadása. Ezeket a rendszer az importálási lépés során adja hozzá a létrehozás után.

    Konfigurálja a tudásbázist. Állítsa be az új tudásbázis nevét. Duplikált nevek használata támogatott, és speciális karaktereket is támogatottak.

    Ne válassza a 4. lépésből származó semmit, mert ezek az értékek felül lesznek írva a fájl importálásakor.

1. Az 5. lépésben válassza a **Létrehozás**elemet.

1. Ebben az új Tudásbázisban nyissa meg a **Beállítások** lapot, és válassza a **Tudásbázis importálása**lehetőséget. Ezzel importálja a kérdéseket, a válaszokat, a metaadatokat, a követő utasításokat, és megőrzi a kinyert adatforrások nevét.

   > [!div class="mx-imgBorder"]
   > [![Tudásbázis importálása](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Tesztelje** az új tudásbázist a teszt panel használatával. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

1. **Tegye közzé** a tudásbázist, és hozzon létre egy csevegési robotot. Ismerje meg, hogyan [teheti közzé a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="chat-logs-and-alterations"></a>Csevegési naplók és változtatások
Kis-és nagybetűket nem megkülönböztető változtatások (szinonimák) nem lesznek automatikusan importálva. A [v4 API](https://go.microsoft.com/fwlink/?linkid=2092179) -k használatával áthelyezheti az új tudásbázisban található módosításokat.

A csevegési naplók nem telepíthetők át, mivel az új Tudásbázis Application Insightst használ a csevegési naplók tárolásához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-To/edit-knowledge-base.md)
