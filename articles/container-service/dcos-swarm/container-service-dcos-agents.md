---
title: "Azure Tárolószolgáltatási DC/OS-ügynök készleteket"
description: "A nyilvános és titkos ügynök készletek működéséről az Azure tároló szolgáltatás DC/OS-fürt"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: e82a6c1ee2d45cd07f4e87c43ad4fb1149ef555c
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Tárolószolgáltatási DC/OS-ügynök készleteket
Azure Tárolószolgáltatási DC/OS fürtök ügynök csomópontok két rendelkezik, egy nyilvános készlet és egy személyes készletet tartalmaz. Egy alkalmazás vagy a készletbe, a tárolószolgáltatás gépek közötti kisegítő érintő is telepíthető. A gépek kommunikál az internettel (nyilvános), vagy belső (magánhálózati) tartani. Ez a cikk rövid áttekintést nyújt, ezért azonban nyilvános és titkos.


* **Személyes ügynökök**: titkos ügynök csomópontokat átszervezhető nem irányítható hálózathoz. Ez a hálózat csak érhető el, az admin zónából, vagy a nyilvános zóna útválasztón keresztül. Alapértelmezés szerint a DC/OS indít alkalmazások titkos ügynök csomópontján. 

* **Nyilvános ügynökök**: nyilvános ügynök csomópontok DC/OS-alkalmazások és szolgáltatások futtatása egy nyilvánosan elérhető a hálózaton keresztül. 

A DC/OS hálózati biztonsággal kapcsolatos további információkért lásd: a [DC/OS-dokumentáció](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Ügynök-készletek központi telepítése

A DC/OS-ügynök készletek az Azure Tárolószolgáltatás jönnek létre az alábbiak szerint:

* A **titkos készlet** tartalmazza, hogy mikor megadja ügynök csomópontok száma, [a DC/OS-fürt üzembe](container-service-deployment.md). 

* A **nyilvános készlet** kezdetben a egy előre meghatározott számú ügynök csomópontot tartalmaz. Ha a DC/OS-fürt ki van építve a rendszer automatikusan hozzáadja a készlet.

A privát készlet és a nyilvános készlet olyan Azure virtuálisgép-méretezési készlet. Telepítés után a készletek is átméretezhetők.

## <a name="use-agent-pools"></a>Ügynök-készletek használatára
Alapértelmezés szerint **Marathon** bármely új alkalmazás telepíti a *titkos* ügynök csomópontok. Explicit módon telepítheti az alkalmazást, hogy a *nyilvános* csomópontok az alkalmazás létrehozása közben. Válassza ki a **nem kötelező** lapra, és írja be **slave_public** a a **elfogadott erőforrás szerepkörök** érték. Ez a folyamat dokumentált [Itt](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) és a a [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentációját.

## <a name="next-steps"></a>Következő lépések
* Tudjon meg többet az [a DC/OS-tárolók kezelése](container-service-mesos-marathon-ui.md).

* Megtudhatja, hogyan [nyissa meg a tűzfal](container-service-enable-public-access.md) a DC/OS-tárolók nyilvános hozzáférés engedélyezése az Azure által biztosított.

