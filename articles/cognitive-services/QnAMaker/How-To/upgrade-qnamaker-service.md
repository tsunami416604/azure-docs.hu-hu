---
title: Frissítse a kérdések és válaszok készítő szolgáltatást – Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: A kérdések és válaszok készítő szolgáltatás frissítése
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e8d3713d6729c4e30da9a64a382e9d5a647dfefd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348647"
---
# <a name="upgrade-your-qna-maker-service"></a>A kérdések és válaszok készítő service frissítése
Ha szeretné, frissítse a kérdések és válaszok készítő verem egyes összetevők a kezdeti létrehozás után. A Részletek területen találja a függő összetevők és SKU kijelölés [Itt](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Frissítse a kérdések és válaszok készítő felügyeleti Termékváltozat
A kérdések és válaszok készítő felügyeleti SKU frissítése:
1. Nyissa meg a kérdések és válaszok készítő erőforrás az Azure portálon, és válassza ki **tarifacsomag**.

    ![Kérdések és válaszok készítő erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Válassza ki a megfelelő SKU, és nyomja le az **válasszon**.

    ![Kérdések és válaszok készítő díjszabása](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>App service frissítése
Is [vertikális felskálázás](https://docs.microsoft.com/azure/app-service/web-sites-scale) vagy az App service csökkentheti.

1. Nyissa meg az alkalmazás szolgáltatás-erőforrást az Azure portálon, és válassza ki **vertikális felskálázás** vagy **csökkentheti** lehetőség, szükség szerint.

    ![Kérdések és válaszok készítő app service méretezési](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Azure Search szolgáltatás frissítése
Jelenleg nem hajtható végre egy a hely frissítését az Azure keresési Termékváltozat. Azonban a kívánt SKU hozzon létre egy új erőforrást az Azure search, állítsa vissza az adatokat az új erőforráshoz, és csatolja a kérdések és válaszok készítő verem.

1. Hozzon létre egy új az Azure search-erőforrást az Azure portálon, és válassza ki a kívánt Termékváltozat.

    ![Kérdések és válaszok készítő Azure keresési erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Az indexek vissza az eredeti az Azure search-erőforrás a az újjal. Lásd: a biztonságimásolat-visszaállítással mintakód [Itt](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Miután visszaállította az adatokat, nyissa meg az új Azure search erőforrást válassza **kulcsok**, és jegyezze fel a **neve** és a **adminisztrációs kulcsot**.

    ![Kérdések és válaszok készítő Azure keresési kulcsok](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Az új erőforrást az Azure search összekapcsolása a kérdések és válaszok készítő verem, nyissa meg a kérdések és válaszok készítő alkalmazás szolgáltatás.

    ![Kérdések és válaszok készítő App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Válassza ki **Alkalmazásbeállítások** , és cserélje le a **AzureSearchName** és **AzureSearchAdminKey** mezők a 3. lépéssel.

    ![Kérdések és válaszok készítő App Service-beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Indítsa újra az App service.

    ![Kérdések és válaszok készítő App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kérdések és válaszok készítő API használata](../Quickstarts/csharp.md)
