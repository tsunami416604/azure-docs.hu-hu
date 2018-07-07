---
title: Az Azure Container Service DC/OS-ügynökkészletek
description: A nyilvános és privát ügynökként működése egy Azure Container Service DC/OS-fürttel
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9dda6d45caf69734eb135779c8bac00fea721efd
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901058"
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Az Azure Container Service DC/OS-ügynökkészletek
Az Azure Container Service DC/OS fürtök ügynökcsomópontok két készleteket, a nyilvános készlet és a egy privát készletet tartalmaz. Egy alkalmazás vagy a készlethez, ez hatással lenne a kisegítő lehetőségek között a gépek a container service-ben is telepíthető. A gépek lehetnek közzétéve az interneten (nyilvános) vagy tartani a belső (magánhálózati). Ez a cikk rövid áttekintést ad meg, miért azonban nyilvános és privát.


* **Privát ügynökök**: privát ügynök csomópontok futtatása nem irányítható hálózaton keresztül. Ehhez a hálózathoz csak érhető el, a rendszergazda zónából, vagy a nyilvános zónákhoz peremhálózati útválasztó keresztül. Alapértelmezés szerint DC/OS elindítja az alkalmazások privát ügynök csomópontokon. 

* **Nyilvános ügynökök**: nyilvános ügynökcsomópontok DC/OS-alkalmazások és szolgáltatások futtatása egy nyilvánosan elérhető-e a hálózaton keresztül. 

További információ a DC/OS-hálózati biztonság: a [DC/OS dokumentációt](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Ügynökkészletek üzembe helyezése

Az Azure Container Service DC/OS-ügynökkészletek módon jönnek létre:

* A **privát készlet** adjon meg mikor ügynökcsomópontok számát tartalmazza, [a DC/OS fürt üzembe helyezéséhez](container-service-deployment.md). 

* A **nyilvános készlet** kezdetben az ügynökcsomópontok előre meghatározott számát tartalmazza. Van a DC/OS fürt üzembe helyezésekor a rendszer automatikusan hozzáadja a készlethez.

A privát készlet és a nyilvános készlet is az Azure-beli virtuálisgép-méretezési csoportok. Ezen készletek átméretezhető üzembe helyezés után.

## <a name="use-agent-pools"></a>Használja az ügynökkészletek
Alapértelmezés szerint **Marathon** bármely új alkalmazást helyez üzembe a *privát* ügynökcsomóponttal. Az alkalmazás számára explicit módon telepíteni kívánt a *nyilvános* csomópontok az alkalmazás létrehozása során. Válassza ki a **nem kötelező** lapra, és adja meg **slave_public** számára a **elfogadott erőforrás-szerepkörökkel** értéket. Ez a folyamat dokumentált [Itt](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) és a a [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentációját.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [a DC/OS-tárolók kezelése](container-service-mesos-marathon-ui.md).

* Ismerje meg, hogyan [megnyitja a tűzfalat](container-service-enable-public-access.md) tárolóit DC/OS nyilvános hozzáférésének engedélyezéséhez az Azure által biztosított.

