---
title: Dokumentum részletei - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A dokumentumlista-oldalon a munkaterület első 10 dokumentuma látható. Minden dokumentum esetében megjeleníti a dokumentumot feltöltő felhasználó nevét, párosítását, típusát, nyelvét, feltöltési időbélyegzőjét és e-mail címét.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595578"
---
# <a name="view-document-details"></a>Dokumentum részleteinek megtekintése

A dokumentumlista-oldalon a munkaterület első 10 dokumentuma látható. Minden dokumentum esetében megjeleníti a dokumentumot feltöltő felhasználó nevét, párosítását, típusát, nyelvét, feltöltési időbélyegzőjét és e-mail címét.

Kattintson egy adott dokumentumra a dokumentum részleteit tartalmazó oldal megtekintéséhez. A dokumentum részletei oldalon a dokumentumból kivont mondatok listája látható.

- Alapértelmezés szerint a "forrás" nyelv van kiválasztva a legördülő mezőben, de válthat a mondatok célnyelven való megtekintéséhez.
- Alapértelmezés szerint oldalanként 20 mondat jelenik meg. A lapozás vezérlővel tallózhat az oldalak között.

![dokumentum részletei](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Dokumentum törlése

A dokumentum törléséhez a felhasználónak munkaterület-tulajdonosnak kell lennie a dokumentum törléséhez. Emellett ha egy dokumentumot egy modell használ, amely a betanítási folyamat vagy a központi telepítési folyamat bármely részén található, a dokumentum nem törölhető.

1. Ugrás a dokumentumoldalra
2.  Mutasson bármely dokumentumrekordra, és kattintson a kuka ikonra.

    ![Dokumentum törlése](media/how-to/how-to-delete-document-1.png)

3.  Törlés megerősítése.

    ![Törlés megerősítése](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>További lépések

- Ismerje [meg, hogyan kell betanítani egy modellt](how-to-train-model.md).
