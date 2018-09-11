---
title: Cloud Foundry-hoz a Microsoft Azure-ban – első lépések |} A Microsoft Docs
description: A Microsoft Azure nyílt Forráskódú vagy a Pivotal Cloud Foundry futtatása
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 18aa5ab16a1690fdcead46ebc15d72837f62612d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302469"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry az Azure-ban

A Cloud Foundry egy olyan nyílt forráskódú „szolgáltatásként nyújtott platform” (PaaS), amely különböző nyelveken és keretrendszerekben fejlesztett, 12 faktoros alkalmazások létrehozását, telepítését és üzemeltetését teszi lehetővé. Ez a dokumentum ismerteti a Cloud Foundry szolgáltatást futtató Azure-ra, és hogyan kezdheti különféle lehetőségeit.

## <a name="cloud-foundry-offerings"></a>Cloud Foundry-ajánlatok

Nincsenek a Cloud Foundry az Azure-ban futtatható kétféle: nyílt forráskódú Cloud Foundry (OSS CF) és a Pivotal Cloud Foundry (PCF). Nyílt Forráskódú cf-hez van egy teljes mértékben [nyílt forráskódú](https://github.com/cloudfoundry) Cloud Foundry verziója kezeli a Cloud Foundry alapítványhoz. A Pivotal Cloud Foundry egy, a Cloud Foundry Pivotal szoftver Inc. vállalati terjesztés Hogy tekintsen meg néhányat a két ajánlatokat közötti különbségeket.

### <a name="open-source-cloud-foundry"></a>Nyílt forráskódú Cloud Foundry-hoz

Nyílt Forráskódú Cloud Foundry az Azure-ban először üzembe helyezése egy BOSH igazgató, és üzembe kell helyezni a Cloud Foundry, telepítheti használatával a [a GitHub utasításait](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Nyílt Forráskódú CF használatával kapcsolatos további tudnivalókért lásd: a [dokumentáció](https://docs.cloudfoundry.org/) a Cloud Foundry alapítvány által biztosított.

A Microsoft legjobb támogatás a nyílt Forráskódú cf-hez a következő közösségi csatornákon keresztül biztosít:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>csatorna bosh – azure-cpi a [Cloud Foundry Slack](https://slack.cloudfoundry.org/)
- [CF-bosh levelezőlistára](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problémák – a [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) és [service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Az Azure-erőforrások, például a virtuális gépek, amelyiken futtatja a Cloud Foundry-hoz, a támogatás szintjét az Azure-támogatási szerződés alapján. Legjobb közösségi támogatás csak a Cloud Foundry-specifikus összetevőkre vonatkozik.

### <a name="pivotal-cloud-foundry"></a>A Pivotal Cloud Foundry-hoz

A Pivotal Cloud Foundry tartalmazza az ugyanazon alapvető platformot, mint a nyílt Forráskódú terjesztési néhány egyéb felügyeleti eszközök és a vállalati szintű támogatással együtt. A PCF futtatásához az Azure-ban, meg kell szerezni a Pivotal licenc. A PCF-ajánlat az Azure marketplace-ről egy 90 napos próbaidőszaki licencre is tartalmaz.

Az eszközök között [Pivotal az Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), egy webalkalmazás, amely leegyszerűsíti az üzembe helyezését és felügyeletét egy a Cloud Foundry alapítvány és [Pivotal alkalmazások Manager](https://docs.pivotal.io/pivotalcf/console/), egy webalkalmazás kezeléséhez a felhasználók és alkalmazások.

A fenti nyílt Forráskódú cf-hez felsorolt támogatási csatornák mellett a PCF licenc feljogosítja a Pivotal forduljon a támogatási. A Microsoft és a Pivotal is engedélyezte támogatási munkafolyamatok, amelyek lehetővé teszik, hogy bármelyik fél kapcsolatba segítségért és rendelkezik a megfelelő irányításához, attól függően, hol helyezkedik el a problémát megkeresését.

## <a name="azure-service-broker"></a>Azure Service Broker

A cloud Foundry arra ösztönzi a ["tizenkét tényezős alkalmazásfejlesztési"](https://12factor.net/) módszer, amely elősegíti a állapot nélküli alkalmazások, folyamatok és az állapotalapú szolgáltatások biztonsági tiszta elkülönítése. [Szolgáltatás közvetítők](https://docs.cloudfoundry.org/services/api.html) üzembe helyezhető, és kötést létrehozni alkalmazásokat támogató szolgáltatások egységes módon kínálnak. A [az Azure service broker](https://github.com/Azure/meta-azure-service-broker) biztosít néhány a legfontosabb Azure-szolgáltatásokat a csatornát, beleértve az Azure storage és Azure SQL segítségével.

A Pivotal Cloud Foundry használja, ha a service broker is [elérhető csempeként](https://docs.pivotal.io/azure-sb/installing.html) a Pivotal hálózat.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

### <a name="azure-devops-services-plugin"></a>Az Azure DevOps-szolgáltatásokkal beépülő modulja

A cloud Foundry egy jól Agilis szoftverfejlesztés, beleértve a folyamatos integrációs (CI) és a folyamatos továbbítás (CD) használatát. Ha az Azure DevOps-szolgáltatásokkal használata kezelheti a projektjeit, és szeretne másolatot CI/CD-ről folyamat a Cloud Foundry célzó, használja a [Azure fejlesztési és üzemeltetési szolgáltatásokat a Cloud Foundry hozzon létre bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). A beépülő modul segítségével egyszerűen konfigurálható és automatizálja az üzembe helyezést, a Cloud Foundry-hoz, üzemelő Azure-ban vagy egy másik környezetre.

## <a name="next-steps"></a>További lépések

- [Az Azure Marketplace-ről a Pivotal Cloud Foundry üzembe helyezése](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Alkalmazás üzembe helyezése a Cloud Foundry az Azure-ban](./cloudfoundry-deploy-your-first-app.md)
