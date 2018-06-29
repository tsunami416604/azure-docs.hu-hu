---
title: 'App Service – áttekintés: Azure verem |} Microsoft Docs'
description: Az App Service Azure veremben áttekintése
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 42258a352b05d97341f20c13bf837e1398dd8979
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099962"
---
# <a name="app-service-on-azure-stack-overview"></a>Az Azure Stack App Service szolgáltatásának áttekintése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure App Service Azure veremben egy Azure verem használható Microsoft Azure platform,--szolgáltatás (PaaS) elérhető. A szolgáltatás lehetővé teszi, hogy az ügyfelek - belső vagy külső – hozzon létre web API és az Azure Functions bármilyen platformra vagy eszközre az alkalmazások. Ezek az alkalmazások integrálása a helyszíni alkalmazások, és az üzleti folyamatok automatizálása. Az Azure verem a felhő üzemeltetői is futtathatók alkalmazások vevői teljes körűen felügyelt virtuális gépek (VM), az általuk választott megosztott Virtuálisgép-erőforrások vagy dedikált virtuális gépek.

Az Azure App Service lehetővé teszi, hogy automatizálja az üzleti folyamatokat és az üzemeltetési felhőalapú API-k. Egyetlen integrált szolgáltatásként Azure App Service lehetővé teszi különböző összetevők--webhelyek, RESTful API-k és üzleti folyamatok--kombinálhatja az egyetlen megoldás.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Miért kínálnak az Azure App Service Azure veremben?

Az App Service legfontosabb funkciói és képességei többek között az alábbiak:

- **Több nyelv és keretrendszer**: az App Service kiváló támogatást nyújt az ASP.NET, Node.js, Java, PHP és Python rendelkezik. Futtathatja a Windows PowerShell és egyéb parancsfájlokat vagy végrehajtható fájlok App Service virtuális gépeken.
- **DevOps optimalizálás**: folyamatos integrációt és a GitHub, helyi Git vagy bitbucket szolgáltatásokhoz központi telepítési beállítása. Támogatja a tesztelési és átmeneti környezet a frissítéseket. Kezelheti az alkalmazásokat, az App Service-ben az Azure PowerShell vagy a platformfüggetlen parancssori felület (CLI) használatával.
- **Visual Studio-integráció**: a Visual Studio dedikált eszközei leegyszerűsítik a létrehozása és alkalmazások telepítése.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben

App Service alkalmazás számos különböző, amelyek célja, hogy egy adott munkaterhelés gazdagép kínálja:

- [Webalkalmazások](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) webhelyek és webalkalmazások üzemeltetéséhez.
- [API-alkalmazások](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) RESTful API-k üzemeltetéséhez.
- Az Azure Functions eseményvezérelt, kiszolgáló nélküli munkaterhelések üzemeltetéséhez.

A word alkalmazás Itt a számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. A „webalkalmazás” alatt például általában mind a számítási erőforrásokat, mind az alkalmazáskódot szokták érteni, amelyek a böngészőben együttesen hoznak létre működő funkciót. De az App Service webalkalmazás Azure verem biztosít az alkalmazás kódjában futtató számítási erőforrások.

Az alkalmazás különböző több App Service apps állhat. Például ha egy RESTful API végén, és az alkalmazás egy előtér-webkiszolgáló áll, akkor:

- mindkettőt (a kezelőfelületet és az api-t is) üzembe helyezheti egyetlen webalkalmazásban;
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban.

   ![A figyelési adatok App Service áttekintése](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Mit nevezünk App Service-csomagnak?

Az App Service erőforrás-szolgáltató, amely az Azure App Service használja ugyanazt a kódot használja. Ennek eredményeképpen egyes általánosan használt fogalmakat ismertető érdemes leíró vannak. Az App Service-ben az alkalmazások árképzési tárolója az App Service-csomag neve. Azt jelenti, hogy az alkalmazások tárolására szolgáló dedikált virtuális gépek csoportja tartalmazza. Egy adott előfizetésen belül rendelkezhet több App Service-csomagokról.

Nincsenek megosztott és dedikált munkavállalók Azure-ban. A megosztott worker támogatja a több-bérlős nagy sűrűségű alkalmazást futtató, és megosztott dolgozó csak egyetlen halmazát. Dedikált kiszolgálókra csak egy bérlő által használt, és térjen három méretben kaphatók: kicsi, közepes és nagy. A helyszíni felhasználók igényeinek mindig nem leírt adott kifejezések használatával. Az App Service Azure veremben erőforrás-szolgáltató rendszergazdák elérhetővé kívánnak munkavégző rétegek adhat meg. A egyedi üzemeltetési igények alapján meghatározhatja a megosztott munkavállalók több példányban vagy dedikált munkavállalók más-más részhalmazához. Munkavégző szintű definíciók segítségével is majd meghatározzák a saját termékváltozatok árképzési.

## <a name="portal-features"></a>A portál jellemzői

App Service Azure veremben az Azure App Service használ a felhasználói felület, ugyanez vonatkozik a háttérrendszer működésében. Azonban bizonyos funkciók le vannak tiltva, és működési Azure verem nem. Az Azure-specifikus elvárásainak, vagy azokat a funkciókat, hogy jelenleg nem érhetők el az Azure-verem.

## <a name="next-steps"></a>További lépések

- [Az App Service Azure veremben megkezdése előtt](azure-stack-app-service-before-you-get-started.md)
- [Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md), mint például a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider-deploy.md) és a [MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md).
