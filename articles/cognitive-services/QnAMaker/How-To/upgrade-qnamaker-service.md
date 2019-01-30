---
title: A QnA Maker szolgáltatás – QnA Maker frissítése
titleSuffix: Azure Cognitive Services
description: A QnA Maker verem egyes összetevőinek frissítésére a kezdeti létrehozás után kiválaszthatja.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 5cef98fedc0e2e29582c8cde8c75837880f1cf58
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216064"
---
# <a name="upgrade-your-qna-maker-service"></a>QnA Maker-szolgáltatás frissítése
A QnA Maker verem egyes összetevőinek frissítésére a kezdeti létrehozás után kiválaszthatja. A részletek a függő összetevők és a termékváltozat [Itt](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>A QnA Maker felügyeleti Termékváltozat frissítése

Szüksége lesz a további kérdéseit és válaszait a Tudásbázisban, ha az aktuális szint feletti a QnA Maker szolgáltatást a tarifacsomag frissítése. 

A QnA Maker felügyeleti Termékváltozat frissítése:

1. Nyissa meg a QnA Maker erőforrást az Azure Portalon, és válassza ki **tarifacsomag**.

    ![A QnA Maker erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Válassza ki a megfelelő Termékváltozat és nyomja le a **kiválasztása**.

    ![A QnA Maker – díjszabás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Az App service frissítése

 Ha az ügyfél alkalmazásából további kérések kiszolgálására kell a Tudásbázis, frissítse az app service-tarifacsomag.

Is [vertikális felskálázás](https://docs.microsoft.com/azure/app-service/web-sites-scale) - vagy leskálázás az App Service-ben.

1. Nyissa meg az App service-erőforrást az Azure Portalon, és válassza ki **vertikális felskálázás** vagy **vertikális leskálázás** szükség szerint lehetőség.

    ![A QnA Maker alkalmazás szolgáltatásskálázás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Az Azure Search szolgáltatás frissítése

Ha azt tervezi, szeretné, hogy sok tudásbázisok, frissítse az Azure Search szolgáltatás tarifacsomag. 

Jelenleg nem hajtható végre egy a hely frissítését az Azure keresési Termékváltozat. Azonban az Azure search új erőforrás létrehozása a kívánt Termékváltozat, állítsa vissza az adatokat az új erőforráshoz, és összekapcsolása a QnA Maker verem.

1. Az Azure search új erőforrás létrehozása az Azure Portalon, és válassza ki a kívánt Termékváltozatot.

    ![A QnA Maker Azure search-erőforrás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Az indexek visszaállítása az eredeti az Azure search-erőforrás az újat. Tekintse meg a biztonsági mentés visszaállítási mintakód [Itt](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Az adatok visszaállítása után nyissa meg az új Azure search erőforrás válassza **kulcsok**, és jegyezze fel a **neve** és a **adminisztrációs kulcsot**.

    ![A QnA Maker Azure search-kulcsok](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Az új Azure search-erőforrás összekapcsolása a QnA Maker vermet, nyissa meg a QnA Maker App Service-ben.

    ![Az App Service a QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Válassza ki **Alkalmazásbeállítások** , és cserélje le a **AzureSearchName** és **AzureSearchAdminKey** mezőket a 3. lépés.

    ![A QnA Maker az App Service-beállítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Indítsa újra az App Service-ben.

    ![A QnA Maker az App Service-újraindítás](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A QnA Maker API használata](../Quickstarts/csharp.md)
