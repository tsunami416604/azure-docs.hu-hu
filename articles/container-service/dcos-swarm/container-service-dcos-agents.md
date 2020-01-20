---
title: ELAVULT DC/OS Agent-készletek a Azure Container Servicehoz
description: A nyilvános és a privát ügynök készletei Azure Container Service DC/OS-fürttel működnek
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278379"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>ELAVULT DC/OS Agent-készletek a Azure Container Servicehoz

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

A Azure Container Service DC/OS-fürtök két készletben, egy nyilvános készletben és egy privát készletben található ügynök-csomópontokat tartalmaznak. Egy alkalmazás üzembe helyezhető bármelyik készletre, ami hatással van a tároló szolgáltatásban lévő gépek közötti kisegítő lehetőségekre. A gépek elérhetővé tehetők az interneten (nyilvános) vagy belső (magán) állapotban. Ez a cikk rövid áttekintést nyújt arról, hogy miért van nyilvános és privát készlet.


* **Privát ügynökök**: a privát ügynök csomópontjai nem irányítható hálózaton futnak. Ez a hálózat csak a felügyeleti zónából vagy a nyilvános zóna peremhálózati útválasztóján keresztül érhető el. Alapértelmezés szerint a DC/OS elindítja az alkalmazásokat a privát ügynök csomópontjain. 

* **Nyilvános ügynökök**: a nyilvános ügynök csomópontjai egy nyilvánosan elérhető hálózaton keresztül futnak a DC/os-alkalmazásokkal és-szolgáltatásokkal. 

A DC/OS hálózati biztonsággal kapcsolatos további információkért lásd a [DC/os dokumentációját](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Ügynök-készletek üzembe helyezése

Azure Container Service a DC/OS-ügynökök készletei a következőképpen jönnek létre:

* A **privát készlet** tartalmazza a [DC/os fürt telepítésekor](container-service-deployment.md)megadott ügynök-csomópontok számát. 

* A **nyilvános készlet** kezdetben előre meghatározott számú ügynök-csomópontot tartalmaz. A rendszer automatikusan hozzáadja ezt a készletet a DC/OS fürt üzembe helyezésekor.

A privát készlet és a nyilvános készlet az Azure virtuálisgép-méretezési csoportok. A készleteket az üzembe helyezés után is átméretezheti.

## <a name="use-agent-pools"></a>Ügynök-készletek használata
Alapértelmezés szerint a **Marathon** minden új alkalmazást üzembe helyez a *privát* ügynök csomópontjain. Az alkalmazás létrehozása során explicit módon telepítenie kell az alkalmazást a *nyilvános* csomópontokra. Válassza a **választható** fület, és adja meg a **Slave_public** az **elfogadható erőforrás-szerepkörök** értékhez. Ezt a folyamatot a [](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) [DC/os](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentációjában találja.

## <a name="next-steps"></a>Következő lépések
* További információ [a DC/os-tárolók kezeléséről](container-service-mesos-marathon-ui.md).

* Megtudhatja, hogyan [nyithatja meg az](container-service-enable-public-access.md) Azure által biztosított tűzfalat a DC/os-tárolók nyilvános elérésének engedélyezéséhez.

