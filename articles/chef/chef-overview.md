---
title: A Chef használata az Azure-ban
description: Bevezetés a Chef használatára az Azure-infrastruktúra konfigurálásához és teszteléséhez
keywords: Azure, Chef, devops, Virtual Machines, áttekintés, automatizálás
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158009"
---
# <a name="using-chef-with-azure"></a>A Chef használata az Azure-ban
A [Chef](https://www.chef.io) egy hatékony automatizálási platform, amely átalakítja a virtuális gépek infrastruktúráját az Azure-ba programkódba. A Chef automatizálja az infrastruktúra konfigurálását, üzembe helyezését és felügyeletét a hálózaton keresztül, függetlenül attól, hogy mekkora a mérete.

Ez a cikk a Chef Azure-infrastruktúra kezelésére való használatának előnyeit ismerteti.

## <a name="chef-extension-on-azure"></a>Chef-bővítmény az Azure-ban
Hozzon létre egy, az Azure Portalon a [Chef bővítménnyel](https://docs.microsoft.com/azure/chef/chef-extension-portal) rendelkező Chef-ügyféllel rendelkező virtuális gépet. A kiépítés után ezek a virtuális gépek készen állnak a Chef-kiszolgálók általi felügyeletre.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Chef-munkaállomás használata közvetlenül a Azure Cloud Shellban! Futtassa az összes Chef-segédprogramot és az Inspect Cloud Shellból. A Chef parancsait a következő helyekről használhatja:

* [chef](https://docs.chef.io/ctl_chef.html)
* [konyhai](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

A parancssori segédprogramokat kombinálhatja a Cloud Shellban elérhető egyéb eszközökkel, például a `git`, a `az-cli`és a `terraform`, valamint az infrastruktúra és a megfelelőségi automatizálás megírásával a böngészőből.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Infrastruktúra, alkalmazások és megfelelőség automatizálása egy platformmal
A vállalatoknak sebességet, sebességet és biztonságot kell megkövetelniük a digitális piactéren való versenyzéshez. A Chef és a Microsoft segítséget nyújt a felhasználóknak, a csapatoknak és a vállalatoknak az összes ilyen dolog megvalósításában. Az egyik platformon a Chef automatizálható, így automatizálhatja és folyamatosan továbbíthatja infrastruktúráját, alkalmazásait és megfelelőségét a Microsoft-birtokon.

## <a name="test-drive-chef-automate-on-azure"></a>Az Azure-beli Chef-automatizálás tesztelése
A Chef által támogatott [Chef Azure Marketplace megoldás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) lehetővé teszi az infrastruktúra és az alkalmazások közös fejlesztését, üzembe helyezését és kezelését. Egyetlen kattintással azonnali hozzáférést kap a Chef automatizálható összes kereskedelmi szolgáltatásához; teljes körű láthatóságot nyerhet a teljes flottában, engedélyezheti a folyamatos megfelelőséget, és kezelheti az összes változást egy egyesített munkafolyamattal.

## <a name="next-steps"></a>Következő lépések

* [Windows rendszerű virtuális gép létrehozása az Azure-ban Chef használatával](/azure/virtual-machines/windows/chef-automation)
