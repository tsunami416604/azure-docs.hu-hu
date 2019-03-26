---
title: A QnA Maker szolgáltatás – QnA Maker frissítése
titleSuffix: Azure Cognitive Services
description: Ossza meg, vagy a QnA Maker szolgáltatás frissítése annak érdekében, hogy az erőforrások jobb kezelése.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: tulasim
ms.openlocfilehash: cb38fdcfa8cbbfa0b90141c2de2280ff1ef08c3b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437987"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Megosztás vagy a QnA Maker szolgáltatás frissítése
Ossza meg, vagy a QnA Maker szolgáltatás frissítése annak érdekében, hogy az erőforrások jobb kezelése. 

A QnA Maker verem egyes összetevőinek frissítésére a kezdeti létrehozás után kiválaszthatja. A részletek a függő összetevők és a termékváltozat [Itt](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>A QnA Maker meglévő szolgáltatások megosztása

A QnA Maker több Azure-erőforrásokat hoz létre. Annak érdekében, hogy csökkentse a felügyeleti és a költségek megosztását, az alábbi táblázat segítségével megismerheti, hogy és mit nem oszthat meg:

|Szolgáltatás|Megosztás|
|--|--|
|Cognitive Services|X|
|App Service-csomag|✔|
|App Service|X|
|Application Insights|✔|
|Keresési szolgáltatás|✔|

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
