---
title: "Azure API Management-szolgáltatások telepítése több Azure-régiók |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet telepíteni az Azure API Management szolgáltatáspéldány több Azure-régiók."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 1c39fee739c2f5fd4b928e1e76e1ea57f072b5f8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Az Azure API Management szolgáltatáspéldány üzembe helyezése több Azure-régiók
Az API Management több területi környezetben, amely lehetővé teszi, hogy egyetlen API management szolgáltatás szét a kívánt Azure-régiók tetszőleges számú API közzétevők támogatja. Ezzel csökkenthető a kérelem által érzékelt késleltetés földrajzilag elosztott API fogyasztók és a szolgáltatás rendelkezésre állása is támogatja, ha egy régió tartozik offline állapotba kerül. 

Amikor egy API-kezelés szolgáltatás a kezdetben létrejön, csak az egyik tartalmaz [egység] [ unit] és egy egyetlen Azure-régió, amelyhez az elsődleges régióban kijelölt fájlcsoportban helyezkedik el. További régiókban könnyen felveheti az Azure portálon keresztül. Az API Management átjárókiszolgáló minden régió telepít, és hívás forgalmat a rendszer a legközelebbi átjáró irányítsa. Ha egy régiót offline állapotba kerül, akkor kimenő forgalomról automatikusan újra irányított, a következő legközelebbi átjáró. 

> [!IMPORTANT]
> Több területi telepítési érhető el csak a  **[prémium] [ Premium]**  réteg.
> 
> 

## <a name="add-region"></a>API-kezelés szolgáltatás példányt telepítése egy új régióban
> [!NOTE]
> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Az Azure portálon keresse meg a **és az árképzés** az API Management szolgáltatáspéldány a lap. 

![Skála lap][api-management-scale-service]

Egy új régió telepíteni, kattintson a **+ Hozzáadás régió** az eszköztáron.

![Adja hozzá a régió][api-management-add-region]

A legördülő listából válassza ki a helyet, és állítsa be a csúszkával egységek száma.

![Adja meg a egység][api-management-select-location-units]

Kattintson a **Hozzáadás** helyezhető el a kiválasztott a helyek táblában. 

Ismételje meg ezt a folyamatot, amíg a konfigurált összes hellyel rendelkezik, és kattintson a **mentése** a telepítési folyamat elindításához az eszköztáron.

## <a name="remove-region"></a>Egy API-kezelés szolgáltatás példány törlése
Az Azure portálon keresse meg a **és az árképzés** az API Management szolgáltatáspéldány a lap. 

![Skála lap][api-management-scale-service]

Szeretné eltávolítani a helyet nyissa meg a helyi menü használatával a **...**  gomb a tábla jobb oldali végén. Válassza ki a **törlése** lehetőséget.

![Távolítsa el a régió][api-management-remove-region]

A törlés jóváhagyásához, és kattintson a **mentése** a módosítások életbe léptetéséhez.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Get started with Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

