---
title: Web-, mobil-és API-Azure Government | Microsoft Docs
description: Ez összehasonlítja a szolgáltatásokat és útmutatást a Azure Government alkalmazások fejlesztéséhez
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396356"
---
# <a name="azure-government-web--mobile"></a>Azure Government Web és mobil

Ez a cikk a Azure Government-környezet webes és mobil szolgáltatásait ismerteti, amelyek a nyilvános verziótól eltérő szolgáltatás-változatokat, valamint a környezettel kapcsolatos szempontokat ismertetik.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

Az [Azure Cognitive Search](https://docs.microsoft.com/azure/search/) általánosan elérhető Azure Governmentban. A nyilvános kormányzati adatokkal történő keresési funkciók önállóan történő feltárásához látogasson el a [Content Search és az Intelligence](https://documentsearch.azurewebsites.net/home/) webhelyére, válassza ki az "Amerikai Egyesült államokbeli fellebbviteli körzet 1." adathalmazt, majd válasszon egyet a bemutató lehetőségei közül.

A kormányzati keresési alkalmazásokban széles körben elfogadott keresési funkciók közé tartoznak a [kognitív képességek](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), amelyek hasznosak a szerkezet és az információk nagy, nem megkülönböztethető szöveges dokumentumokból való kinyeréséhez.

Az alapszintű lekérdezési szintaxis, amely nagy mennyiségű tartalom keresésére nyújt lekérdezéseket, az alkalmazások fejlesztői számára is fontos. Az Azure Cognitive Search két szintaxist támogat: [egyszerű](https://docs.microsoft.com/azure/search/query-simple-syntax) és [teljes](https://docs.microsoft.com/azure/search/query-lucene-syntax). A [lekérdezési kifejezések példáit](https://docs.microsoft.com/azure/search/search-query-simple-examples) áttekintheti egy tájoláshoz.

### <a name="variations"></a>Változata
A Azure Governmentban létrehozott keresési szolgáltatások végpontja a következő:

| Service Type | Azure Public | Azure Government |
| ------------ | ------------ | ---------------- |
| Azure Cognitive Search szolgáltatás |\*. search.windows.net |\*. search.windows.us|

A nyilvános felhőben általánosan elérhető és előzetes verziójú funkciók is elérhetők Azure Governmentban.

### <a name="considerations"></a>Megfontolások

Az alábbi információk az Azure Cognitive Search Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| ----------------------------------- | --------------------------------------- |
| Az Azure Cognitive Search tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet. | Az Azure Cognitive Search metaadatok nem tartalmazhatnak export-vezérelt adatokat. Ez a metaadatok tartalmazzák a szolgáltatás létrehozásakor és karbantartásakor megadott összes konfigurációs adatát. Ne adja meg a szabályozott/vezérelt adattípusokat a következő mezőkben: előfizetés neve, Erőforráscsoport, szolgáltatásnév, erőforrásnevek, erőforrás-címkék vagy bármely olyan objektum, amelyet a Cognitive Search (indexek, indexelő, adatforrások, szinonimák térképek és szakértelmével) neve vagy leírása tartalmaz. Ne tartalmazzon bizalmas adatokat az API részeként elküldhető keresési/lekérdezési sztringekben a REST APIba érkező HTTP-fejlécekben.|

## <a name="app-services"></a>App Services
### <a name="variations"></a>Változata
Az Azure App Services általánosan elérhető Azure Governmentban.

A Azure Governmentban létrehozott Azure App Service-alkalmazások címe eltér a nyilvános felhőben létrehozott alkalmazásokkal:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
| App Service |\*. azurewebsites.net |\*. azurewebsites.us|
| Egyszerű szolgáltatásnév azonosítója| abfa0a7c-a6b6-4736-8310-5855508787cd | 6a02c803-dafd-4136-b4c3-5a6f318b4714 |

A nyilvános felhőben elérhető néhány App Service funkció még nem érhető el Azure Governmentban:

- Problémák diagnosztizálása és megoldása
- Környezet
    - Üzembe helyezési lehetőségek: csak a helyi git-tárház és a külső tárház érhető el.
    - Központi telepítési központ
- Beállítások
    - Application Insights
- Fejlesztői eszközök
    - Teljesítményteszt
    - Erőforrás-kezelő
    - PHP-hibakeresés
- Támogatási & hibaelhárítás
    - App Service Advisor
- App Service-tanúsítvány


### <a name="considerations"></a>Megfontolások
A következő információk a App Service Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
| A Azure App Service belül megadott, tárolt és feldolgozott adatértékek exportálható adatkezelést is tartalmazhatnak. A Azure App Serviceon belül futó bináris fájlok. Statikus hitelesítő elemek, például jelszavak és intelligenskártya-PIN-kódok az Azure platform összetevőihez való hozzáféréshez. Az Azure platform összetevőinek kezeléséhez használt tanúsítványok titkos kulcsa. SQL-kapcsolatok karakterláncai Egyéb biztonsági információk/titkos kulcsok, például tanúsítványok, titkosítási kulcsok, főkulcsok és az Azure-szolgáltatásokban tárolt tárolási kulcsok. |A metaadatok nem tartalmazhatnak exportálásra vezérelt adatokat. Ez a metaadatok tartalmazzák a Azure App Service létrehozásakor és karbantartásakor megadott összes konfigurációs adatát. Ne adja meg a szabályozott/vezérelt adattípusokat a következő mezőkben: erőforráscsoportok, erőforrásnevek, erőforrás-Címkék|

## <a name="api-management"></a>API Management
A szolgáltatásról és használatáról az [Azure API Management dokumentációjában](../api-management/index.yml)talál további információt.

### <a name="variations"></a>Változata

Az Azure API Management Service általánosan elérhető Azure Governmentban. Azok a szolgáltatások, amelyek jelenleg nem érhetők el a API Management szolgáltatásban a Azure Government esetén:

- Azure AD B2C integráció 
    - Az Azure AD B2C-nal való integráció nem érhető el Azure Government 

A Azure Government Azure-API Management elérésének URL-címei eltérőek:

| Service Type | Azure Public | Azure Government |
| --- | --- | --- |
|Átjáró API Management| \*. azure-api.net| \*. azure-api.us|
|API Management portál | \*. portal.azure-api.net |\*. portal.azure-api.us| 
|API Management-kezelés| \*. management.azure-api.net |\*. management.azure-api.us|

### <a name="considerations"></a>Megfontolások
Az alábbi információk az Azure API Management Service Azure Government határát azonosítják:

| Szabályozott/vezérelt adatvédelem | A szabályozott/vezérelt adathozzáférés nem engedélyezett |
| --- | --- |
|Az Azure API Management szolgáltatásban tárolt és feldolgozott összes adathalmaz Azure Government-szabályozású adattal is rendelkezhet.|Az Azure API Management szolgáltatás metaadatainak nem tartalmazhatnak export-vezérelt adatokat. Ne adja meg a szabályozott/vezérelt adattípusokat a következő mezőkben: API Management szolgáltatásnév, előfizetés neve, Erőforráscsoport, erőforrás-címkék.|

## <a name="next-steps"></a>További lépések
Kiegészítő információk és frissítések esetén a [Microsoft Azure Government blogra](https://blogs.msdn.microsoft.com/azuregov/) fizethet elő.

