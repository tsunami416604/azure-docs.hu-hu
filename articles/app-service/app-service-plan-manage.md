---
title: Az Azure App Service-csomag kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan kezelheti az App Service-csomag különböző feladatok végrehajtására.
keywords: App Service-ben, az azure app Service-ben, méretezhető, app service-csomag módosítása, létrehozása, felügyelete, kezelése
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: f426982163a5e49264bc4f222f6869d9cbb40c89
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166066"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Az Azure App Service-csomag kezelése

Egy [Azure App Service-csomag](azure-web-sites-web-hosting-plans-in-depth-overview.md) biztosít az App Service-alkalmazások futtatásához szükséges erőforrásokat. Ez az útmutató bemutatja, hogyan kezelheti az App Service-csomag.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha van egy App Service Environment-környezet, az [App Service-csomag létrehozása az App Service-környezet](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Üres App Service-csomagot hozhat létre, vagy létrehozhat egy tervet az alkalmazások létrehozásának részeként.

1. Az a [az Azure portal](https://portal.azure.com), jelölje be **új** > **Web + mobil**, majd válassza ki **webalkalmazás** vagy más típusú App Service-alkalmazás.

1. Válasszon ki egy meglévő App Service-csomag, vagy hozzon létre egy csomagot az új alkalmazás.

   ![Alkalmazás létrehozása az Azure Portalon.][createWebApp]

   A csomag létrehozásához:

   a. Válassza ki **[+] létrehozása új**.

      ![Hozzon létre egy App Service-csomagot.][createASP] 

   b. A **App Service-csomag**, adja meg a csomag nevét.

   c. A **hely**, válassza ki a megfelelő helyére.

   d. A **tarifacsomag**, válasszon egy megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **az összes megtekintése** több díjszabás a beállítások, például a **ingyenes** és **megosztott**. Miután kiválasztotta a tarifacsomagot, kattintson a **kiválasztása** gombra.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Alkalmazás áthelyezése egy másik App Service-csomag

Áthelyezheti egy alkalmazást egy másik App Service-csomag, mindaddig, amíg a forrás terv és a célelőfizetésbe a _ugyanazt az erőforráscsoportot és a földrajzi régió_.

> [!NOTE]
> Az Azure minden egyes új App Service-csomag egy telepítési egység, belsőleg nevű egy webtárhely helyez üzembe. Minden egyes régió rendelkezhet számos webtárhelyének, de az alkalmazás csak helyezheti át ugyanazon webtárhelyen létrehozott tervek között. App Service-környezet egy elkülönített webtárhely, így a alkalmazások áthelyezhetők az azonos App Service-környezet a csomagok között, de nem terveket, a másik App Service Environment-környezetek között.
>
> A webtárhely azt szeretné, hogy a csomag létrehozásakor nem adható meg, de győződjön meg arról, hogy egy csomag jön létre egy meglévő csomagot, ugyanazon webtárhelyhez meg lehet. Röviden minden csomag létre ugyanabban az erőforráscsoportban, és a régió kombináció a rendszer üzembe helyezi az azonos webtárhely. Például egy csomagot az erőforráscsoportban A és B régióban létrehozta, majd bármely ezután létrehoz egy erőforráscsoportban és régióban B terv helyezünk üzembe az azonos webtárhely. Vegye figyelembe, hogy csomagok nem helyezhetők át webtárhelyének után jönnek létre, ezért nem helyezhető át a csomag be "az azonos webtárhely" egy másik séma szerint áthelyezi egy másik erőforráscsoportot.
> 

1. Az a [az Azure portal](https://portal.azure.com), tallózással az áthelyezni kívánt alkalmazást.

1. A menüben keresse meg a **App Service-csomag** szakaszban.

1. Válassza ki **módosítása App Service-csomag** megnyitásához a **App Service-csomag** választó.

   ![Az App Service-csomag választó.][change] 

1. Az a **App Service-csomag** választó, válassza egy meglévő szeretne áthelyezni az alkalmazás be.   

A **válassza ki az App Service-csomag** lapon látható csak az ugyanabban az erőforráscsoportban és az aktuális alkalmazás App Service-csomag és a földrajzi régióban lévő terveket.

Minden csomag saját díjszabási szinttel rendelkezik. Egy webhely, például áthelyezése egy **ingyenes** a réteg egy **Standard** szint lehetővé teszi, hogy az a funkciók és erőforrások rendelt összes alkalmazás a **Standard** szint. Azonban egy alkalmazást áthelyezését egy magasabb többrétegű terv többrétegű alacsonyabb díjcsomagra azt jelenti, hogy már nem bizonyos szolgáltatások elérését. Ha az alkalmazás egy szolgáltatás, amely nem érhető el a célként megadott csomag használja, hibaüzenet, amely azt mutatja, melyik szolgáltatást használja, amely nem érhető el. 

Például, ha SSL-tanúsítványok az alkalmazások egyikét használja, előfordulhat, hogy a hibaüzenetet látja:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Ebben az esetben az alkalmazás viheti át a célelőfizetésbe, mielőtt kell vagy:
- Tarifacsomag kiválasztása a cél-csomag vertikális **alapszintű** vagy újabb verziója.
- Távolítsa el az alkalmazás összes SSL-kapcsolatot.

## <a name="move-an-app-to-a-different-region"></a>Alkalmazás áthelyezése egy másik régióba

A régió, amelyben az alkalmazás fut. az a régió van App Service-csomag. Az App Service-csomag régió azonban nem módosítható. Az alkalmazás futtatása egy másik régióban szeretné, ha egy másik alkalmazás-e a Klónozás. A Klónozás teszi az alkalmazás egy új vagy meglévő App Service-csomag bármelyik régióban.

Annak **Alkalmazásklónozási** a a **Fejlesztőeszközök** a menü részét.

> [!IMPORTANT]
> A Klónozás vannak bizonyos korlátai. Itt olvashat őket [Azure App Service-alkalmazás klónozását](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Méretezés App Service-csomag

Vertikális felskálázás akár egy App Service csomag a tarifacsomag, lásd: [az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md).

Horizontális felskálázási példányok száma egy alkalmazást, lásd: [példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service-csomag törlése

Az utolsó alkalmazás az App Service-csomag törlésekor a váratlan költségek elkerülése az App Service is törli a terv alapértelmezés szerint. Ha ehelyett tartsa a terv választja, akkor kell megváltoztatnia a terv **ingyenes** szinten, ezért nem díjkötelesek.

> [!IMPORTANT]
> App Service-csomagok, amelyek nem találhatók hozzájuk rendelt alkalmazások továbbra is költségekkel mivel folyamatosan fenntartjuk konfigurált Virtuálisgép-példányok.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure-beli alkalmazás vertikális felskálázása](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
