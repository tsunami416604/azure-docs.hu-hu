---
title: Tudásbázisok áttelepítése - QnA Maker
description: A tudásbázis áttelepítéséhez exportálni kell az egyik tudásbázisból, majd importálni kell egy másikba.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258090"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Tudásbázis áttelepítése exportálás-importálás sal

Az áttelepítés egy új tudásbázis létrehozásának folyamata egy meglévő tudásbázisból. Ezt több okból is megteheti:

* biztonsági mentés és visszaállítás folyamata
* CI/CD-folyamat
* régiók áthelyezése

A tudásbázis áttelepítéséhez egy meglévő tudásbázisból kell exportálni, majd egy másikba importálni.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* Új [QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Tudásbázis áttelepítése a QnA Maker-ből
1. Jelentkezzen be a [QnA Maker portálra.](https://qnamaker.ai)
1. Válassza ki az áttelepíteni kívánt eredettudás-bázist.

1. A **Beállítások** lapon válassza a **Tudásbázis exportálása** lehetőséget a származási tudásbázis tartalmát tartalmazó .tsv fájl letöltéséhez - kérdések, válaszok, metaadatok, nyomon követési kérések és az adatforrásnevek, amelyekből kinyertük őket.

1. Válassza a legfelső menü **Tudásbázis létrehozása parancsát,** majd hozzon létre egy _üres_ tudásbázist. Üres, mert amikor létrehozza, nem fog URL-eket vagy fájlokat hozzáadni. Ezek az importálási lépés során, a létrehozás után kerülnek hozzáadásra.

    Konfigurálja a tudásbázist. Csak az új tudásbázis nevét állítsa be. Az ismétlődő nevek támogatottak, és a speciális karakterek is támogatottak.

    Ne válasszon semmit a 4.

1. Az **5.**

1. Ebben az új tudásbázisban nyissa meg a **Beállítások** lapot, és válassza **a Tudásbázis importálása**lehetőséget. Ez importálja a kérdéseket, válaszokat, metaadatokat, nyomon követési utasításokat, és megőrzi azadatforrás neveket, amelyekből kinyerték őket.

   > [!div class="mx-imgBorder"]
   > [![Tudásbázis importálása](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Tesztelje** az új tudásbázist a Teszt panelen. További információ [a tudásbázis teszteléséről.](../How-To/test-knowledge-base.md)

1. **Tegye közzé** a tudásbázist, és hozzon létre egy csevegőrobotot. További információ [a tudásbázis közzétételéről.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programozott módon áttelepítheti tudásbázisát a QnA Maker programból

Az áttelepítési folyamat programozott módon érhető el a következő REST API-k használatával:

**Exportálás**

* [Tudásbázis API letöltése](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importálás**

* [API cseréje (újratöltés ugyanazzal a tudásbázis-azonosítóval)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API létrehozása (betöltés új tudásbázis-azonosítóval)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Csevegési naplók és módosítások
A kis- és nagybetűk et nem megkülönböztető módosítások (szinonimák) importálása nem történik meg automatikusan. A [V4 API-k](https://go.microsoft.com/fwlink/?linkid=2092179) segítségével helyezze át a módosításokat az új tudásbázisban.

A csevegési naplók áttelepítése nem kizárt, mivel az új tudásbázis az Application Insights ot használja a csevegési naplók tárolására.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-To/edit-knowledge-base.md)
