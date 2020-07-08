---
title: Alapértelmezett válasz beolvasása – QnA Maker
description: A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979970"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker erőforrás alapértelmezett válaszának módosítása

A Tudásbázis alapértelmezett válaszát akkor kell visszaadnia, ha a válasz nem található. Ha ügyfélalkalmazás, például az [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)-t használja, akkor külön alapértelmezett válasz is lehet, ami azt jelzi, hogy a pontszám küszöbértéke nem felel meg a válasznak.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Alapértelmezett válasz beállítása a Tudásbázis létrehozásakor

Új Tudásbázis létrehozásakor az alapértelmezett válasz szöveg az egyik beállítás. Ha úgy dönt, hogy nem állítja be azt a létrehozási folyamat során, később is megváltoztathatja a következő eljárással.

## <a name="change-default-answer-in-qna-maker-portal"></a>Az alapértelmezett válasz módosítása QnA Maker portálon

A rendszer a Tudásbázis alapértelmezett válaszát adja vissza, ha a QnA Maker szolgáltatás nem ad vissza választ.

1. Jelentkezzen be a [QnA Maker portálra](https://www.qnamaker.ai/) , és válassza ki a tudásbázist a listából.
1. A navigációs sávon válassza a **Beállítások** lehetőséget.
1. Módosítsa az **alapértelmezett válasz szöveg** értékét a **Tudásbázis kezelése** szakaszban.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Képernyőkép: QnA Maker portál, beállítások lap, alapértelmezett válasz szövegmezővel kiemelve.":::

1. A módosítás mentéséhez válassza a **Mentés és a betanítás** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Tudásbázis létrehozása](../How-to/manage-knowledge-bases.md)