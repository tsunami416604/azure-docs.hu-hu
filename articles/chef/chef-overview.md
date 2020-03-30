---
title: A Chef használata az Azure-ral
description: Bevezetés az Azure-infrastruktúra konfigurálásához és teszteléséhez a Chef használatába
keywords: az úr, szakács, devops, virtuális gépek, áttekintés, automatizálás
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586342"
---
# <a name="using-chef-with-azure"></a>A Chef használata az Azure-ral
[A Chef](https://www.chef.io) egy hatékony automatizálási platform, amely az Azure virtuálisgép-infrastruktúráját kóddá alakítja. A Chef automatizálja az infrastruktúra konfigurálásának, üzembe helyezésének és kezelésének módját a hálózaton keresztül, méretétől függetlenül.

Ez a cikk ismerteti az Azure-infrastruktúra kezelésére a Chef használatának előnyeit.

## <a name="chef-extension-on-azure"></a>Chef-bővítmény az Azure-ban
Üzembe helyezhet egy virtuális gépet a Chef-ügyfélszolgáltatással háttérszolgáltatásként futva az [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) [chef-bővítményével.](https://docs.microsoft.com/azure/chef/chef-extension-portal) A kiépítés után ezek a virtuális gépek készen állnak a Chef-kiszolgáló általi felügyelt használatra.

## <a name="chef-cloud-shell"></a>Chef Felhő shell
Használja a Chef Workstation-t közvetlenül az Azure Cloud Shellben! Futtassa az összes Chef segédprogramot és inspec-et közvetlenül a Cloud Shell-ből. A Chef parancsokat a következő innen használhatja:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [Konyha](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [Kés](https://docs.chef.io/knife.html)
* [szakácsstílus](https://docs.chef.io/cookstyle.html)
* [séf által működtetett](https://www.chef.sh/docs/chef-workstation/getting-started/)

Kombinálja a parancssegédprogramokat a Cloud Shellben `git` `az-cli`elérhető `terraform`egyéb eszközökkel, például a , és a , és írja meg az infrastruktúra és a megfelelőség automatizálását a böngészőből.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Az infrastruktúra, az alkalmazások és az egyetlen platformnak való megfelelés automatizálása
A vállalatoknak sebességre, sebességre és biztonságra van szükségük ahhoz, hogy a digitális piacon versenyezzenek. A Chef és a Microsoft együtt segít az egyéneknek, a csapatoknak és a vállalatoknak mindezeket a dolgok elérésében. Egyetlen platformmal, a Chef Automate-tel automatizálhatja és folyamatosan biztosíthatja infrastruktúráját, alkalmazásait és megfelelőségét a Microsoft-birtokain.

## <a name="test-drive-chef-automate-on-azure"></a>Tesztvezetés Chef Automate az Azure-ban
A Chef által támogatott [Chef Automate Azure Marketplace-megoldás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) lehetővé teszi az infrastruktúra és az alkalmazások közös kiépítését, üzembe helyezését és kezelését. Egy kattintással azonnali hozzáférést biztosít a Chef Automate összes kereskedelmi funkciójához; teljes körű láthatóságot biztosít a teljes flottában, lehetővé teszi a folyamatos megfelelést, és egységes munkafolyamattal kezelheti az összes változást.

## <a name="next-steps"></a>További lépések

* [Windows os virtuális gép létrehozása az Azure-ban a Chef használatával](chef-automation.md)
