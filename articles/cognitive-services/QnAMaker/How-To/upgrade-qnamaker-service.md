---
title: QnA Maker szolgáltatás frissítése – QnA Maker
titleSuffix: Azure Cognitive Services
description: Megoszthatja vagy frissítheti QnA Maker szolgáltatásait, hogy jobban kezelhesse az erőforrásokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: df4aa2d6a3c4690fb1fc38b0f4f7d49afccdd657
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640486"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>QnA Maker szolgáltatás megosztása vagy frissítése
Megoszthatja vagy frissítheti QnA Maker szolgáltatásait, hogy jobban kezelhesse az erőforrásokat. 

Megadhatja, hogy a kezdeti létrehozás után a QnA Maker verem egyes összetevőit frissítse. Tekintse meg a függő összetevők és az SKU-kiválasztás részleteit [itt](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Meglévő szolgáltatások megosztása QnA Maker

QnA Maker több Azure-erőforrást hoz létre. A felügyelet és a költségmegosztás előnyeinek csökkentése érdekében az alábbi táblázat segítségével megismerheti, hogy mit tehet és nem oszthat meg:

|Szolgáltatás|Megosztás|
|--|--|
|Cognitive Services|X|
|App Service-csomag|✔|
|App Service-ben|X|
|Application Insights|✔|
|Keresési szolgáltatás|✔|

## <a name="upgrade-qna-maker-management-sku"></a>QnA Maker felügyeleti SKU frissítése

Ha további kérdésekkel és válaszokkal kell rendelkeznie a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét. 

A QnA Maker felügyeleti SKU frissítése:

1. Nyissa meg a QnA Maker erőforrást a Azure Portal, és válassza az **árképzési szintet**.

    ![Erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Válassza ki a megfelelő SKU-t, majd kattintson a **kiválasztás**gombra.

    ![Díjszabás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Az App Service frissítése

 Ha a Tudásbázisban több kérést kell kiszolgálni az ügyfélalkalmazástól, frissítse az App Service díjszabási szintjét.

Az App Service vertikális [fel](https://docs.microsoft.com/azure/app-service/manage-scale-up) -vagy leskálázást végez.

1. Nyissa meg az App Service-erőforrást a Azure Portalban , és szükség szerint válassza a vertikális felskálázás vagy a leskálázás lehetőséget.

    ![QnA Maker app Service-méretezés](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Azure Search szolgáltatás frissítése

Ha sok tudásbázist tervez, frissítse a Azure Search Service díjszabási szintjét. 

Jelenleg nem végezhető el az Azure Search SKU helyben történő frissítése. Létrehozhat azonban egy új Azure Search-erőforrást a kívánt SKU-val, visszaállíthatja az új erőforrást, majd csatolhatja azt a QnA Maker veremhez.

1. Hozzon létre egy új Azure Search-erőforrást a Azure Portalban, és válassza ki a kívánt SKU-t.

    ![Azure Search-erőforrás QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Állítsa vissza az indexeket az eredeti Azure Search-erőforrásból az újat. Tekintse meg a biztonsági másolat-visszaállítási mintakód [itt](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Az adatvisszaállítás után nyissa meg az új Azure Search-erőforrást, válassza a **kulcsok**lehetőséget, és jegyezze fel a **nevet** és a **rendszergazdai kulcsot**.

    ![Azure Search-kulcsok QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Ha az új Azure Search-erőforrást az QnA Maker veremhöz szeretné kapcsolni, lépjen a QnA Maker app Service weboldalára.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Válassza az **Alkalmazásbeállítások** lehetőséget, és cserélje le a **AzureSearchName** és a **AzureSearchAdminKey** mezőket a 3. lépésből.

    ![QnA Maker appservice-beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Indítsa újra az App Service-ben.

    ![QnA Maker appservice újraindítása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker API használata](../Quickstarts/csharp.md)
