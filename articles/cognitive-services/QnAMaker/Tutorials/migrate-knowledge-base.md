---
title: Tudásbázisok migrálása – QnA Maker
description: A Tudásbázis áttelepítéséhez az egyik Tudásbázisból kell exportálni, majd importálni egy másikba.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80258090"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Tudásbázis migrálása export-import használatával

Az áttelepítés egy új Tudásbázis létrehozásának folyamata egy meglévő Tudásbázisból. Ezt több okból is elvégezheti:

* biztonsági mentési és visszaállítási folyamat
* CI/CD-folyamat
* régiók áthelyezése

A Tudásbázis áttelepítéséhez egy meglévő Tudásbázisból kell exportálni, majd importálni egy másikba.

## <a name="prerequisites"></a>Előfeltételek

* Mielőtt elkezdené, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* Új [QnA Maker szolgáltatás](../How-To/set-up-qnamaker-service-azure.md) beállítása

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Tudásbázis migrálása a QnA Makerból
1. Jelentkezzen be [QnA Maker portálra](https://qnamaker.ai).
1. Válassza ki az áttelepíteni kívánt forrás-tudásbázist.

1. A **Beállítások** lapon válassza a **Tudásbázis exportálása** lehetőséget egy. TSV fájl letöltéséhez, amely a forrásként szolgáló Tudásbázis – kérdések, válaszok, metaadatok, követő kérések és a kinyert adatforrások nevét tartalmazza.

1. Válassza a **Tudásbázis létrehozása** lehetőséget a felső menüben, majd hozzon létre egy _üres_ tudásbázist. Üres, mert a létrehozásakor nem lesz URL-cím vagy fájl hozzáadása. Ezeket a rendszer az importálási lépés során adja hozzá a létrehozás után.

    Konfigurálja a tudásbázist. Állítsa be az új tudásbázis nevét. Az ismétlődő nevek támogatottak, és a speciális karakterek is támogatottak.

    Ne válassza a 4. lépésből származó semmit, mert ezek az értékek felül lesznek írva a fájl importálásakor.

1. Az 5. lépésben válassza a **Létrehozás**elemet.

1. Ebben az új Tudásbázisban nyissa meg a **Beállítások** lapot, és válassza a **Tudásbázis importálása**lehetőséget. Ezzel importálja a kérdéseket, a válaszokat, a metaadatokat, a követő utasításokat, és megőrzi a kinyert adatforrások nevét.

   > [!div class="mx-imgBorder"]
   > [![Tudásbázis importálása](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Tesztelje** az új tudásbázist a teszt panel használatával. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

1. **Tegye közzé** a tudásbázist, és hozzon létre egy csevegési robotot. Ismerje meg, hogyan [teheti közzé a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Tudásbázis programozott áttelepíthető QnA Maker

Az áttelepítési folyamat programozott módon elérhető a következő REST API-k használatával:

**Exportálás**

* [Tudásbázis API letöltése](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importálás**

* [Az API cseréje (a reload ugyanazzal a Tudásbázis-AZONOSÍTÓval)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [API létrehozása (Betöltés új Tudásbázis-AZONOSÍTÓval)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Csevegési naplók és változtatások
Kis-és nagybetűket nem megkülönböztető változtatások (szinonimák) nem lesznek automatikusan importálva. A [v4 API](https://go.microsoft.com/fwlink/?linkid=2092179) -k használatával áthelyezheti az új tudásbázisban található módosításokat.

A csevegési naplók nem telepíthetők át, mivel az új Tudásbázis Application Insightst használ a csevegési naplók tárolásához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-To/edit-knowledge-base.md)
