---
title: A Tudásbázis - kérdések és válaszok készítő - kognitív Azure-szolgáltatások létrehozása |} Microsoft Docs
description: A Tudásbázis létrehozása
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349905"
---
# <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

Kérdések és válaszok készítő teszi az olyan nagyon egyszerű bevezetni a Tudásbázis létrehozása meglévő adatforrásaiból. Hozzon létre egy új kérdések és válaszok készítő Tudásbázis gyakran ismételt kérdések lap, a termékek laborútmutatókkal, a strukturált dokumentumok, vagy vegye fel őket besorolást.

## <a name="steps"></a>Lépések

1. Első lépésként jelentkezzen be a [kérdések és válaszok készítő portal](https://qnamaker.ai) , az azure hitelesítő adatait, és kattintson a **hozzon létre új szolgáltatást**.

    ![Hozzon létre KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Ha még nem hozott létre a kérdések és válaszok készítő szolgáltatás, válassza ki a **kérdések és válaszok szolgáltatás létrehozása**. Ellenkező esetben a kérdések és válaszok készítő szolgáltatás választhat legördülő listákat a 2. Válassza ki a kérdések és válaszok készítő szolgáltatást futtató kiszolgálón a Tudásbázis.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Adja meg a következő adatokat a Tudásbázis létrehozásához.

    ![Set adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - A szolgáltatás adjon egy **nevét.** Duplikált nevek használata támogatott, és speciális karaktereket is támogatott.
    - Illessze be a kibontani a URL-címmel. További információ a támogatott adatforrások típusai [Itt](../Concepts/data-sources-supported.md).
    - Alternatív megoldásként tölt fel, amelyek adatokat ki kell olvasni. Tekintse meg a [árakról](https://aka.ms/qnamaker-pricing
) megtekintéséhez, hogy hány dokumentumokat is hozzáadhat.
    - Ha szeretné manuálisan adja hozzá a QnAs, kihagyhatja a fájlok csatolása.

4. Kattintson a **Létrehozás** gombra.

    ![Hozzon létre KB](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Adatok kinyerni néhány percet vesz igénybe.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Ha a Tudásbázis sikeresen létrejött, a rendszer átirányítja a a **Tudásbázis** lap.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis importálása](../Tutorials/migrate-knowledge-base.md)
