---
title: A Chef az Azure-ral
description: Bevezetés a Chef segítségével az Azure-infrastruktúra tesztelése és konfigurálása
ms.service: virtual-machines-linux
keywords: az Azure, a chef, a devops, a virtuális gépek, a áttekintése, automatizálása
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: be1e7ab953c55581645a9702fc4759cb603e7ecc
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357861"
---
# <a name="using-chef-with-azure"></a>A Chef az Azure-ral
[Chef](http://www.chef.io) egy hatékony automatizálási platform, amely Azure-beli virtuális gép infrastruktúra alakítja a kódot. Chef automatizálja hogyan infrastruktúra van konfigurálva, telepítheti és kezelheti a hálózatba, függetlenül attól, hogy annak méretét.

Ez a cikk ismerteti a Chef segítségével kezelheti az Azure előnyeit.

## <a name="chef-extension-on-azure"></a>Chef kiterjesztése az Azure-ban
A Chef-ügyfél a háttérben szolgáltatásként futó virtuális gép kiépítése a [Chef bővítmény](https://docs.microsoft.com/en-us/azure/chef/chef-extension-portal) az Azure Portal webhelyen. Miután kiépített, ezek a virtuális gépek készen áll a Chef-kiszolgáló által kezelt.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Közvetlenül az Azure Cloud Shellben munkaállomás használata Chef! Futtassa a Chef segédprogramok és az InSpec megfelelő Cloud Shellben. A Chef parancsokat használhat:

* [chef](https://docs.chef.io/ctl_chef.html)
* [megjelenítő kijelzőket a konyhai](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

A parancssori segédprogramok kombinálva a Cloud shellben elérhető eszközök például `git`, `az-cli`, és `terraform`, és az infrastruktúra és a megfelelőség automation írhat a böngészőben.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Infrastruktúra, alkalmazások és platformfüggetlen megfelelés automatizálása
Vállalatok sebességét, a sebesség és a jelenlegi versenyének helyszíne a digital marketplace biztonság van szükség. Együtt a Chef és a Microsoft segítségével a felhasználók, csoportok és vállalatok ezekről az összes elvégzéséhez. Egyetlen platform révén a Chef Automate, mostantól automatizálhatja és folyamatos elérhetővé tétele az infrastruktúra, alkalmazások és megfelelőség a Microsoft hagyatéki között.

## <a name="test-drive-chef-automate-on-azure"></a>Próbálja ki a Chef Automate az Azure-ban
Támogatja a Chef, a [Chef automatizálása az Azure piactér megoldást](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) lehetővé teszi, hogy elkészítheti, telepítheti és kezelheti az infrastruktúra és alkalmazások elősegítése érdekében. Egyetlen kattintással azonnal hozzáférhet a Chef Automate; össze kereskedelmi funkciójához veheti teljes körű áttekintést kaphat az egész flottájáról, folyamatos megfelelőséget, és felügyelheti az összes változást egy egységes munkafolyamat.

## <a name="next-steps"></a>További lépések

* [Windows virtuális gép létrehozása Chef használatával Azure-ban](/azure/virtual-machines/windows/chef-automation)
