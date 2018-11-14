---
title: A dokumentum adatai – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A dokumentum-lista oldalára bemutatja az első 10 dokumentum a munkaterületén. Minden egyes dokumentumok esetében megjeleníti a neve, párosítás, típus, nyelv, feltöltés időbélyeg és a dokumentum feltöltése a felhasználó e-mail-címe.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: how to edit a model
ms.openlocfilehash: fe108831f173eb31af79a2f8e5f7faf57015c38a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627451"
---
# <a name="view-document-details"></a>A dokumentum részleteinek megtekintése

A dokumentum-lista oldalára bemutatja az első 10 dokumentum a munkaterületén. Minden egyes dokumentumok esetében megjeleníti a neve, párosítás, típus, nyelv, feltöltés időbélyeg és a dokumentum feltöltése a felhasználó e-mail-címe.

Kattintson egy adott dokumentumot a dokumentum Részletek lap megtekintéséhez. A dokumentum Részletek lap kibontott mondatokat a dokumentumból listáját jeleníti meg.

- A "forrás" alapértelmezés szerint nyelvi van jelölve, a legördülő mezőben, de itt válthat a Célnyelv mondatokat megtekintése. 
- 20 mondatokat alapértelmezés szerint egy-egy lapon jelennek meg. Tördelés vezérlő segítségével a lapok közötti Tallózás.

![a dokumentum adatai](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Dokumentum törlése

Felhasználó törlése a dokumentumot, a dokumentum törlése egy munkaterület tulajdonosának kell lennie. Emellett ha egy dokumentum használja egy modell a betanítási folyamat bármely része és a telepítési folyamat bármely része szerepel, a dokumentum nem lehet törölni.

1. Nyissa meg a dokumentum lapját
2.  Vigye a mutatót a dokumentum rekordot, és kattintson a Kuka ikonra.

    ![Dokumentum törlése](media/how-to/how-to-delete-document-1.png)

3.  Törlésének megerősítése.

    ![Törlés jóváhagyása](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, [a modell betanításához hogyan](how-to-train-model.md).
