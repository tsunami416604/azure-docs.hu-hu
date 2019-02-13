---
title: (ELAVULT) Az Azure Container Service DC/OS-ügynökkészletek
description: A nyilvános és privát ügynökként működése egy Azure Container Service DC/OS-fürttel
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113787"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(ELAVULT) Az Azure Container Service DC/OS-ügynökkészletek

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az Azure Container Service DC/OS fürtök ügynökcsomópontok két készleteket, a nyilvános készlet és a egy privát készletet tartalmaz. Egy alkalmazás vagy a készlethez, ez hatással lenne a kisegítő lehetőségek között a gépek a container service-ben is telepíthető. A gépek lehetnek közzétéve az interneten (nyilvános) vagy tartani a belső (magánhálózati). Ez a cikk rövid áttekintést ad meg, miért azonban nyilvános és privát.


* **Privát ügynökök**: Privát ügynök csomópontok futtatása nem irányítható hálózaton keresztül. Ehhez a hálózathoz csak érhető el, a rendszergazda zónából, vagy a nyilvános zónákhoz peremhálózati útválasztó keresztül. Alapértelmezés szerint DC/OS elindítja az alkalmazások privát ügynök csomópontokon. 

* **Nyilvános ügynökök**: Nyilvános ügynök csomópontokon futnak a DC/OS-alkalmazások és szolgáltatások egy nyilvánosan elérhető-e a hálózaton keresztül. 

További információ a DC/OS-hálózati biztonság: a [DC/OS dokumentációt](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Ügynökkészletek üzembe helyezése

Az Azure Container Service DC/OS-ügynökkészletek módon jönnek létre:

* A **privát készlet** adjon meg mikor ügynökcsomópontok számát tartalmazza, [a DC/OS fürt üzembe helyezéséhez](container-service-deployment.md). 

* A **nyilvános készlet** kezdetben az ügynökcsomópontok előre meghatározott számát tartalmazza. Van a DC/OS fürt üzembe helyezésekor a rendszer automatikusan hozzáadja a készlethez.

A privát készlet és a nyilvános készlet is az Azure-beli virtuálisgép-méretezési csoportok. Ezen készletek átméretezhető üzembe helyezés után.

## <a name="use-agent-pools"></a>Használja az ügynökkészletek
Alapértelmezés szerint **Marathon** bármely új alkalmazást helyez üzembe a *privát* ügynökcsomóponttal. Az alkalmazás számára explicit módon telepíteni kívánt a *nyilvános* csomópontok az alkalmazás létrehozása során. Válassza ki a **nem kötelező** lapra, és adja meg **slave_public** számára a **elfogadott erőforrás-szerepkörökkel** értéket. Ez a folyamat dokumentált [Itt](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) és a a [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentációját.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [a DC/OS-tárolók kezelése](container-service-mesos-marathon-ui.md).

* Ismerje meg, hogyan [megnyitja a tűzfalat](container-service-enable-public-access.md) tárolóit DC/OS nyilvános hozzáférésének engedélyezéséhez az Azure által biztosított.

