---
title: A Cloud Foundry – első lépések a Microsoft Azure-ban
description: OsS vagy Pivotal Cloud Foundry futtatása a Microsoft Azure-on
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970051"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry az Azure-ban

A Cloud Foundry egy olyan nyílt forráskódú „szolgáltatásként nyújtott platform” (PaaS), amely különböző nyelveken és keretrendszerekben fejlesztett, 12 faktoros alkalmazások létrehozását, telepítését és üzemeltetését teszi lehetővé. Ez a dokumentum ismerteti a Cloud Foundry Azure-beli futtatásának lehetőségeit, valamint az első lépések módját.

## <a name="cloud-foundry-offerings"></a>Cloud Öntödei ajánlatok

A Cloud Foundry két formája érhető el az Azure-ban: a nyílt forráskódú Cloud Foundry (OSS CF) és a Pivotal Cloud Foundry (PCF). Az OSS CF a Cloud Foundry teljesen [nyílt forráskódú](https://github.com/cloudfoundry) változata, amelyet a Cloud Foundry Foundation kezel. A Pivotal Cloud Foundry a Cloud Foundry vállalati disztribúciója a Pivotal Software Inc.-től. Megvizsgáljuk a két ajánlat közötti különbségeket.

### <a name="open-source-cloud-foundry"></a>Nyílt forráskódú felhőöntöde

Az OSS Cloud Foundry-t az Azure-ban úgy telepítheti, hogy először üzembe helyez egy BOSH-igazgatót, majd telepíti a Cloud Foundry-t a [GitHubon megadott utasítások](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)alapján. Ha többet szeretne megtudni [az](https://docs.cloudfoundry.org/) OSS CF használatáról, olvassa el a Cloud Foundry Foundation dokumentációját.

A Microsoft a következő közösségi csatornákon keresztül biztosítja az OSS CF legjobb támogatását:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>bosh-azure-cpi csatorna a [Cloud Foundry Slack-en](https://slack.cloudfoundry.org/)
- [cf-bosh levelezési lista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- GitHub-problémák a [CPI-hez](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) és a [szolgáltatásközvetítőhez](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> Az Azure-erőforrások, például a Cloud Foundry futtatásához helyt álló virtuális gépek támogatásának szintje az Azure támogatási szerződésén alapul. A legjobb közösségi támogatás csak a Cloud Foundry-specifikus összetevőkre vonatkozik.

### <a name="pivotal-cloud-foundry"></a>Pivotal felhőöntöde

A Pivotal Cloud Foundry ugyanazt az alapvető platformot tartalmazza, mint az OSS-disztribúció, valamint egy sor saját felügyeleti eszközt és vállalati támogatást. PcF futtatásához az Azure-ban, be kell szereznie egy licencet a Pivotal. Az Azure piactér PCF-ajánlata 90 napos próbalicencet tartalmaz.

Az eszközök közé tartozik [a Pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), egy webes alkalmazás, amely leegyszerűsíti a Cloud Foundry alapítvány telepítését és felügyeletét, valamint a Pivotal Apps [Manager,](https://docs.pivotal.io/pivotalcf/console/)egy webes alkalmazás a felhasználók és alkalmazások kezelésére.

A fenti OSS CF támogatási csatornákon kívül a PCF licenc feljogosítja Önt arra, hogy támogatást nyújtson a Pivotal-hoz. A Microsoft és a Pivotal olyan támogatási munkafolyamatokat is engedélyezett, amelyek lehetővé teszik, hogy bármelyik félhez forduljon segítségért, és a kérdés megfelelő módon továbbítsa a kérdést attól függően, hogy hol van a probléma.

## <a name="azure-service-broker"></a>Azure Service Broker

A Cloud Foundry ösztönzi a ["tizenkét faktorú alkalmazás"](https://12factor.net/) módszertant, amely elősegíti az állapotnélküli alkalmazási folyamatok és az állapotalapú háttérszolgáltatások tiszta elkülönítését. [A szolgáltatásbrókerek](https://docs.cloudfoundry.org/services/api.html) egységes módot kínálnak a háttérszolgáltatások alkalmazásokhoz való kiépítésére és kötéséhez. Az [Azure-szolgáltatásközvetítő](https://github.com/Azure/meta-azure-service-broker) néhány kulcsfontosságú Azure-szolgáltatást biztosít ezen a csatornán keresztül, beleértve az Azure storage-t és az Azure SQL-t.

Ha pivotal cloud öntödét használ, a szolgáltatásközvetítő a Pivotal Network [csempéjeként](https://docs.pivotal.io/azure-sb/installing.html) is elérhető.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

### <a name="azure-devops-services-plugin"></a>Az Azure DevOps-szolgáltatások beépülő modul

Cloud Foundry kiválóan alkalmas agilis szoftverfejlesztés, beleértve a folyamatos integráció (CI) és a folyamatos szállítás (CD). Ha az Azure DevOps Services segítségével kezeli a projekteket, és ci/CD-folyamatot szeretne beállítani a Cloud Foundry-ra, használhatja az [Azure DevOps Services Cloud Foundry build bővítményt.](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension) A beépülő modul egyszerűvé teszi a felhőalapú öntödei központi telepítések konfigurálását és automatizálását, akár az Azure-ban, akár más környezetben futnak.

## <a name="next-steps"></a>További lépések

- [Pivotal Cloud Foundry üzembe helyezése az Azure Piactérről](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Alkalmazás üzembe helyezése a Cloud Foundry szolgáltatásban az Azure-ban](./cloudfoundry-deploy-your-first-app.md)
