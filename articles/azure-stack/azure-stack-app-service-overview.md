---
title: 'App Service-ben – áttekintés: Azure Stack |} A Microsoft Docs'
description: Az Azure Stack App Service-ben – áttekintés
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079157"
---
# <a name="app-service-on-azure-stack-overview"></a>Az Azure Stack App Service szolgáltatásának áttekintése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure App Service az Azure Stacken egy elérhető az Azure Stack a Microsoft Azure platform –-szolgáltatásként (PaaS) ajánlat. A szolgáltatás lehetővé teszi, hogy az ügyfelei számára – belső vagy külső – létrehozása a webes API-t és az Azure Functions alkalmazások bármilyen platformra vagy eszközre. Ezek az alkalmazások integrálása a helyszíni alkalmazások, és az üzleti folyamatok automatizálása. Az Azure Stack-felhő üzemeltetői futtatható ügyfélalkalmazások teljes körűen felügyelt virtuális gépeken (VM), a megosztott VM-erőforrások és a dedikált virtuális gépek közötti választás.

Az Azure App Service lehetővé teszi, hogy automatizálja az üzleti folyamatokat és üzemeltetési felhőalapú API-k. Egyetlen integrált szolgáltatásként az Azure App Service lehetővé teszi, hogy kombinálja a különböző összetevők – websites, a RESTful API-k és üzleti folyamatokat – egyetlen megoldásban.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Miért kínálnak az Azure App Service az Azure Stack?

Az App Service legfontosabb funkciói és képességei többek között az alábbiak:

- **Több nyelv és keretrendszer**: App Service kiváló támogatást nyújt az ASP.NET, Node.js, Java, PHP és Python rendelkezik. Az App Service virtuális gépeken a Windows PowerShell és egyéb parancsfájlokat vagy futtatható fájlokat is futtathatja.
- **DevOps-optimalizálás**: állítsa be a folyamatos integrációt és üzembe helyezés a GitHub, a helyi Git vagy a bitbucket használatával. Léptesse elő a tesztelési és átmeneti környezet frissítéseit. App Service-ben az alkalmazások kezelése az Azure PowerShell-lel vagy a platformfüggetlen parancssori felület (CLI) használatával.
- **Visual Studio-integráció**: a Visual Studio dedikált eszközei leegyszerűsítik a létrehozása és alkalmazások telepítése.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben

Az App Service számos típusú, alkalmazások, amelyek célja, hogy egy adott számítási feladatot kínálja:

- [Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) webhelyek és webalkalmazások üzemeltetéséhez.
- [Az API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) RESTful API-k üzemeltetéséhez.
- Az Azure Functions eseményvezérelt, kiszolgáló nélküli számítási feladatok futtatásához.

Itt a word alkalmazás számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. A „webalkalmazás” alatt például általában mind a számítási erőforrásokat, mind az alkalmazáskódot szokták érteni, amelyek a böngészőben együttesen hoznak létre működő funkciót. Azonban az App Service webalkalmazás a számítási erőforrások, amelyek az alkalmazás kódjának futtatásához biztosít Azure Stack.

Az alkalmazás több, különböző típusú App Service-alkalmazások is állhat. Például ha az alkalmazás webes kezelőfelületből áll, és a egy RESTful API vége, akkor:

- mindkettőt (a kezelőfelületet és az api-t is) üzembe helyezheti egyetlen webalkalmazásban;
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban.

   ![A monitorozási adatok App Service áttekintése](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Mit nevezünk App Service-csomagnak?

Az App Service erőforrás-szolgáltató, amely az Azure App Service-ben használja ugyanazt a kódot használja. Ennek eredményeképpen néhány általánosan használt fogalmat ismertető érdemes vannak. Az App Service-ben a díjszabási tároló alkalmazások számára az App Service-csomag neve. Azt jelöli, hogy a dedikált virtuális gépek halmazát jelenti az alkalmazások tárolására szolgál. Egy adott előfizetésen belül több App Service-csomagok is rendelkezhet.

Az Azure-ban vannak közös vagy dedikált feldolgozó. Megosztott feldolgozói nagy sűrűségű több-bérlős alkalmazás üzemeltetését is támogatja, és megosztott munkavállalók csak egy halmaza. Dedikált kiszolgálókat csak egy bérlő által használt, és három méretben származnak: kicsi, közepes és nagy. A helyi ügyfelek igényeit mindig nem ismerteti ezeket a kifejezéseket használatával. Az App Service az Azure Stacken erőforrás-szolgáltató rendszergazdák adhatja meg a feldolgozói rétegek szeretne elérhetővé tenni. Üzemeltetési egyedi igényei alapján, megadhatja a megosztott feldolgozó több példányban, vagy dedikált feldolgozó más-más részhalmazához. Feldolgozó szintű definíciók használatával, majd definiálhat a saját díjköteles termékváltozatok.

## <a name="portal-features"></a>A portál jellemzői

Az Azure Stack App Service-ben használja az ugyanazon felhasználói felület, amely az Azure App Service-ben, ugyanez vonatkozik a háttérrendszerrel. Néhány funkció azonban le vannak tiltva, és nem működik az Azure Stackben. Az Azure-ra vonatkozó elvárásokat vagy ezek a funkciók igénylő szolgáltatások nem érhető el az Azure Stackben.

## <a name="next-steps"></a>További lépések

- [Mielőtt elkezdené, az Azure Stack App Service-szel](azure-stack-app-service-before-you-get-started.md)
- [Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)

Is kipróbálhatja más [platform platformszolgáltatási (PaaS) szolgáltatásokra](azure-stack-tools-paas-services.md), mint például a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider-deploy.md) és a [MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md).
