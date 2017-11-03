---
title: "Ismerkedés a Microsoft Azure-felhő Foundry |} Microsoft Docs"
description: "A Microsoft Azure OSS vagy döntő felhő Foundry futtató"
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 94fbde7707ea9a91076780fdefc3f5a827e0e7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry az Azure-ban

Felhő Foundry egy nyílt forráskódú platform,--szolgáltatás (PaaS) létrehozása, telepítése és a különböző nyelv és keretrendszer 12-tényező alkalmazások operációs rendszer. Ez a dokumentum ismerteti a felhő Foundry futó Azure, és hogyan kezdheti meg a beállításait.

## <a name="cloud-foundry-offerings"></a>Foundry felhőajánlatokat

Nincsenek kétféle futtatható Azure-felhő Foundry: nyílt forráskódú felhő Foundry (OSS CF) és döntő felhő Foundry (PCF). OSS CF van egy teljes mértékben [nyílt forráskódú](https://github.com/cloudfoundry) felhő Foundry verziója kezeli a felhő Foundry Foundation. Döntő felhő Foundry egy vállalati felhő Foundry döntő szoftver Inc. terjesztése Úgy tekintünk, a két ajánlatok közötti különbségek egy részét.

### <a name="open-source-cloud-foundry"></a>Nyílt forráskódú felhő Foundry

Telepítése OSS felhő Foundry Azure először telepítése BOSH igazgató, és majd a felhő Foundry telepítésével használatával a [utasításokat a Githubon](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). OSS CF használatával kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](https://docs.cloudfoundry.org/) a felhő Foundry Foundation által biztosított.

A következő közösségi csatornákon keresztül elérhető legjobb OSS CF támogatást nyújt a Microsoft:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>a bosh-azure-cpi csatorna [felhő Foundry Slackhez](https://slack.cloudfoundry.org/)
- [CF-bosh levelezési lista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub problémák – a [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) és [service broker](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Az Azure-erőforrások, például a felhő Foundry futtató virtuális gépek támogatása szintjét az Azure támogatási foglalt alapul. Legjobb közösségi támogatás csak a felhő Foundry-specifikus összetevőkre vonatkozik.

### <a name="pivotal-cloud-foundry"></a>Foundry döntő felhő

Döntő felhő Foundry tartalmazza az ugyanazon alapvető platformok OSS terjesztési, valamint egyéb felügyeleti eszközök és a nagyvállalati támogatással készlete. Azure PCF futtatja, be kell szerezni Pivotal licencet. Az Azure piactérről PCF ajánlatot 90 napos próba licenc is tartozik.

Az eszközök tartalmazzák a [döntő Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), egy webes alkalmazás, amely egyszerűbbé teszi a központi telepítés és a felhő Foundry Foundation felügyeleti és [kulcsfontosságú alkalmazások Manager](https://docs.pivotal.io/pivotalcf/console/), a webes alkalmazás a felhasználók és az alkalmazások kezelésére.

A fenti OSS CF felsorolt támogatási csatornák mellett a PCF licenc felügyeleti lehetőségeket kínál Pivotal támogatás kérése. A Microsoft és Pivotal is engedélyezte a támogatási munkafolyamatok, amelyek lehetővé teszik, kérjen segítséget a bármelyik fél és a lekérdezés irányított megfelelően attól függően, hogy hol helyezkedik el a problémát.

## <a name="azure-service-broker"></a>Az Azure Service Broker

Felhő Foundry javasolja a ["tizenkét tényezős alkalmazás"](https://12factor.net/) módszertan használatával, amely elősegíti a tiszta választani egymástól a állapot nélküli alkalmazások folyamatokat és az állapotalapú alkalmazások és szolgáltatások biztonsági szolgáltatások. [Szolgáltatás brókerek](https://docs.cloudfoundry.org/services/api.html) kiépítéséhez és biztonsági szolgáltatások köthető alkalmazások egységes módon kínálnak. A [Azure service broker](https://github.com/Azure/meta-azure-service-broker) nyújt a kulcs Azure-szolgáltatások díjairól ezt a csatornát, beleértve az Azure storage és az Azure SQL némelyike.

Ha döntő felhő Foundry használ, a service broker is [csempe elérhető](https://docs.pivotal.io/azure-sb/installing.html) a döntő hálózatról.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

### <a name="visual-studio-team-services-plugin"></a>Visual Studio Team Services beépülő modul

Felhő Foundry kiválóan alkalmas a gyors szoftverfejlesztői, beleértve a folyamatos integrációt (CI) és a folyamatos kézbesítési (CD) használatát. Ha a Visual Studio Team Services (VSTS) segítségével a projektek kezelését, és szeretne CI/CD-ről be a következő feldolgozási sorban célzó felhőalapú Foundry, használja a [VSTS felhő Foundry összeállítási kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). A beépülő modul segítségével egyszerűen konfigurálható és felhő Foundry való telepítésének automatizálásához e futó Azure vagy egy másik környezetben.

## <a name="next-steps"></a>Következő lépések

- [Az Azure piactérről döntő felhő Foundry telepítése](https://azure.microsoft.com/en-us/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Foundry az Azure-felhőbe alkalmazás telepítése](./cloudfoundry-deploy-your-first-app.md)