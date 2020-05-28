---
title: Dokumentum részletei – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A dokumentumok listája oldalon a munkaterület első 10 dokumentuma látható. Az egyes dokumentumoknál megjelenik a név, a párosítás, a típus, a nyelv, a feltöltés időbélyegzője és a dokumentumot feltöltött felhasználó e-mail-címe.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 22c982babbed1322e97127379be75e7b070ed81f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994242"
---
# <a name="view-document-details"></a>Dokumentum részleteinek megtekintése

A dokumentumok listája oldalon a munkaterület első 10 dokumentuma látható. Az egyes dokumentumoknál megjelenik a név, a párosítás, a típus, a nyelv, a feltöltés időbélyegzője és a dokumentumot feltöltött felhasználó e-mail-címe.

Kattintson egy egyéni dokumentumra a dokumentum részletei lap megtekintéséhez. A dokumentum részletei lap a dokumentumból kinyert mondatok listáját jeleníti meg.

- Alapértelmezés szerint a "forrás" nyelv van kiválasztva a legördülő mezőben, de a mondatok megtekinthetők a célként megadott nyelven.
- Alapértelmezés szerint a rendszer 20 mondatot jelenít meg oldalanként. Az oldalak közötti kereséshez használhatja a tördelés vezérlőelemet.

![dokumentum részletei](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Dokumentum törlése

A dokumentum törléséhez a felhasználónak munkaterület-tulajdonosnak kell lennie. Továbbá, ha a dokumentumot egy olyan modell használja, amely a betanítási folyamat bármely részén vagy a telepítési folyamat bármely részén szerepel, a dokumentumot nem lehet törölni.

1. Ugrás a dokumentum lapra
2.  Vigye a kurzort bármelyik dokumentum-rekordra, és kattintson a Kuka ikonra.

    ![Dokumentum törlése](media/how-to/how-to-delete-document-1.png)

3.  Erősítse meg a törlést.

    ![Törlés megerősítése](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>További lépések

- Útmutató a [modellek betanításához](how-to-train-model.md).
