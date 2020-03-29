---
title: (ELAVULT) Az Azure Container Service dc/operációs rendszerügynök-készletei
description: A nyilvános és a magánügynök-készletek hogyan működnek együtt egy Azure Container Service DC/OS-fürttel?
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278379"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(ELAVULT) Az Azure Container Service dc/operációs rendszerügynök-készletei

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Az Azure Container Service dc/os fürtjei két készletben, egy nyilvános készletben és egy privát készletben tartalmaznak ügynökcsomópontokat. Egy alkalmazás telepíthető bármelyik készletbe, ami hatással van a tárolószolgáltatásban lévő gépek közötti hozzáférhetőségre. A gépek lehetnek kitéve az internet (nyilvános) vagy tartott belső (magán). Ez a cikk rövid áttekintést ad arról, hogy miért vannak nyilvános és privát medencék.


* **Magánügynökök**: A magánügynök-csomópontok nem irányítható hálózaton keresztül futnak. Ez a hálózat csak a felügyeleti zónából vagy a nyilvános zóna peremhálózati útválasztóján keresztül érhető el. Alapértelmezés szerint a DC/OS elindítja az alkalmazásokat a privát ügynök csomópontokon. 

* **Nyilvános ügynökök:** A nyilvános ügynöki csomópontok dc/os alkalmazásokat és szolgáltatásokat futtatnak nyilvánosan elérhető hálózaton keresztül. 

A DC/OS hálózati biztonságáról a [DC/OS dokumentációjában](https://docs.mesosphere.com/)olvashat bővebben.

## <a name="deploy-agent-pools"></a>Ügynökkészletek telepítése

A DC/OS ügynökkészletek az Azure Container Service-ben a következőképpen jönnek létre:

* A **privát készlet** a [DC/OS fürt telepítésekor](container-service-deployment.md)megadott ügynökcsomópontok számát tartalmazza. 

* A **nyilvános készlet** kezdetben egy előre meghatározott számú ügynökcsomópontokat tartalmaz. Ez a készlet automatikusan hozzáadódik, amikor a DC/OS fürt ki van építve.

A privát készlet és a nyilvános készlet Azure virtuálisgép-méretezési készletek. Ezeket a készleteket az üzembe helyezés után átméretezheti.

## <a name="use-agent-pools"></a>Ügynökkészletek használata
Alapértelmezés szerint **a Marathon** minden új alkalmazást telepít a magánügynök-csomópontokra. *private* Az alkalmazás létrehozása során explicit módon kell telepítenie az alkalmazást a *nyilvános* csomópontokra. Válassza a **Választható** lapot, és adja meg **slave_public** az **Elfogadott erőforrásszerepkörök** értékhez. Ezt a folyamatot [itt](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) és a [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentációjában dokumentálja.

## <a name="next-steps"></a>További lépések
* További információ [a DC/OS tárolók kezeléséről.](container-service-mesos-marathon-ui.md)

* Ismerje meg, hogyan [nyithatja meg az](container-service-enable-public-access.md) Azure által biztosított tűzfalat a dc/os tárolók nyilvános elérésének engedélyezéséhez.

