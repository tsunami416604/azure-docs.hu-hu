---
title: A Chef használata az Azure-ban
description: Bevezetés a Chef használatára az Azure-infrastruktúra konfigurálásához és teszteléséhez
keywords: Azure, Chef, devops, Virtual Machines, áttekintés, automatizálás
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586342"
---
# <a name="using-chef-with-azure"></a>A Chef használata az Azure-ban
A [Chef](https://www.chef.io) egy hatékony automatizálási platform, amely átalakítja a virtuális gépek infrastruktúráját az Azure-ba programkódba. A Chef automatizálja az infrastruktúra konfigurálását, üzembe helyezését és felügyeletét a hálózaton keresztül, függetlenül attól, hogy mekkora a mérete.

Ez a cikk a Chef Azure-infrastruktúra kezelésére való használatának előnyeit ismerteti.

## <a name="chef-extension-on-azure"></a>Chef-bővítmény az Azure-ban
Hozzon létre egy olyan virtuális gépet, amelynek a Chef-ügyfele háttér-szolgáltatásként fut a [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)a [Chef bővítménnyel](https://docs.microsoft.com/azure/chef/chef-extension-portal) . A kiépítés után ezek a virtuális gépek készen állnak a Chef-kiszolgálók általi felügyeletre.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Chef-munkaállomás használata közvetlenül a Azure Cloud Shellban! Futtassa az összes Chef-segédprogramot és az Inspect Cloud Shellból. A Chef parancsait a következő helyekről használhatja:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [konyhai](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [kés](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [Chef – Futtatás](https://www.chef.sh/docs/chef-workstation/getting-started/)

A parancssori segédprogramokat kombinálhatja a Cloud Shellban elérhető egyéb eszközökkel, például a `git`, a `az-cli`és a `terraform`, valamint az infrastruktúra és a megfelelőségi automatizálás megírásával a böngészőből.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Infrastruktúra, alkalmazások és megfelelőség automatizálása egy platformmal
A vállalatoknak sebességet, sebességet és biztonságot kell megkövetelniük a digitális piactéren való versenyzéshez. A Chef és a Microsoft segítséget nyújt a felhasználóknak, a csapatoknak és a vállalatoknak az összes ilyen dolog megvalósításában. Az egyik platformon a Chef automatizálható, így automatizálhatja és folyamatosan továbbíthatja infrastruktúráját, alkalmazásait és megfelelőségét a Microsoft-birtokon.

## <a name="test-drive-chef-automate-on-azure"></a>Az Azure-beli Chef-automatizálás tesztelése
A Chef által támogatott [Chef Azure Marketplace megoldás](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) lehetővé teszi az infrastruktúra és az alkalmazások közös fejlesztését, üzembe helyezését és kezelését. Egyetlen kattintással azonnali hozzáférést kap a Chef automatizálható összes kereskedelmi szolgáltatásához; teljes körű láthatóságot nyerhet a teljes flottában, engedélyezheti a folyamatos megfelelőséget, és kezelheti az összes változást egy egyesített munkafolyamattal.

## <a name="next-steps"></a>Következő lépések

* [Windows rendszerű virtuális gép létrehozása az Azure-ban Chef használatával](chef-automation.md)
