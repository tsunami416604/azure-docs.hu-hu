---
title: Előzetes verzió tudásbázisok – Qna Maker áttelepítése
titleSuffix: Azure Cognitive Services
description: QnA Maker ingyenes előzetes verzióban létrehozott tudásbázisok kell át kell helyezni a QnA Maker általánosan elérhető A QnA Maker előzetes a január 2019 elavulttá válik.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 08373449b2e61da503ab785eda7d6df80d6d1c16
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227930"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Exportálás-importálás segítségével Tudásbázis áttelepítése
A QnA Maker általános rendelkezésre állás bejelentett 2018. május 7., a \\\build\ konferencián. A QnA Maker általánosan elérhető az Azure-ban létrehozott új architektúrákra rendelkezik. QnA Maker ingyenes előzetes verzióban létrehozott tudásbázisok kell át kell helyezni a QnA Maker általánosan elérhető A QnA Maker előzetes a január 2019 elavulttá válik. A QnA Maker GA a változásokkal kapcsolatos további információkért lásd: a QnA Maker GA közlemény [blogbejegyzés](https://aka.ms/qnamakerga-blog).

A QnA Maker most már rendelkezik egy [díjszabási modell](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/).

Előfeltételek
> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
> * A telepítő egy új [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Tudásbázis át a QnA Maker betekintő portálon
1. Navigáljon a [QnA Maker betekintő portálon](https://aka.ms/qnamaker-old-portal
) , majd kattintson a **saját szolgáltatások**.
2. Válassza ki a Tudásbázis, amelyeket szeretné áttelepíteni a Szerkesztés ikonra kattintva.

    ![Tudásbázis szerkesztése](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Kattintson a **töltse le a Tudásbázis** a Tudásbázis - tartalmat .tsv-fájl letöltésére kérdések, válaszok, metaadatokat, és az adatforrás neve, amely könyvtárban találhatók.

    ![Töltse le a Tudásbázis](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai) az azure hitelesítő adatait, és kattintson a **Tudásbázis létrehozása**.
    
5. Ha még nem hozott létre a QnA Maker szolgáltatást, válassza ki a **hozzon létre egy kérdés-válasz szolgáltatás**. Ellenkező esetben a QnA Maker szolgáltatást választhat a legördülő listákból a 2. lépésben. Válassza ki a QnA Maker szolgáltatást, amely a Tudásbázis fogja futtatni.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Hozzon létre egy üres Tudásbázis 

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Duplikált nevek használata támogatott, és speciális karaktereket is támogatottak.
    - Kihagyás feltöltése fájlokat vagy URL-címeket szeretne az előzetes verzió Tudásbázis adatait használják. Most létre fog hozni egy üres Tudásbázis.

7. Kattintson a **Létrehozás** gombra.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

8. Az új Tudásbázis, nyissa meg a **beállítások** lapra, majd kattintson a **importálás Tudásbázis**. Importálja a kérdések, válaszok és metaadatokat, és megőrzi az adatforrásnevek, amelyből könyvtárban találhatók.

   ![Importálás Tudásbázis](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Teszt** az új Tudásbázis a teszt panelt. Ismerje meg, hogyan [a Tudásbázis tesztelése](../How-To/test-knowledge-base.md).
10. **Közzététel** a Tudásbázis. Ismerje meg, hogyan [közzéteheti a tudásbázist](../How-To/publish-knowledge-base.md).
11. Alább a végpontot használja az alkalmazás vagy robot kódjában. Itt látható, hogyan [hozzon létre egy QnA robotot](../Tutorials/create-qna-bot.md).

    ![A QnA Maker értékek](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

Ezen a ponton minden a Tudásbázis-tartalmat – kérdések, válaszok és metaadatokat, és a forrásfájlok az URL-címeket, nevét és az új Tudásbázis importálásakor. 

## <a name="chatlogs-and-alterations"></a>Chatlogs és változásokból
Átalakítások (szinonimák) nem lesznek automatikusan importálva. Használja a [V2 API-k](https://aka.ms/qnamaker-v2-apis) exportálhatja a módosításokat az előzetes verzió verem és a [V4 API-k](https://aka.ms/qnamaker-v4-apis) cserélje le az új veremben.

Nincs semmilyen módon nem lehet áttelepíteni chatlogs, mivel az új verem az Application Insights chatlogs tárolásához. Letöltheti a chatlogs, azonban a [betekintő portálon](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-To/edit-knowledge-base.md)
