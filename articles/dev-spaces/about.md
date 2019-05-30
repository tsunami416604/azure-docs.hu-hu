---
title: Az Azure fejlesztői, szóközök bemutatása
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Az Azure fejlesztői, szóközök bemutatása
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, a kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 3e887409518f5cc97238a0168213a7918e318c04
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394012"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Az Azure fejlesztési tárolóhelyek egy gyors, iteratív Kubernetes fejlesztési környezetet biztosít az Azure Kubernetes Service (AKS) fürtök csapatok számára. Együttműködhet a csapata egy megosztott AKS-fürtben. Azure fejlesztési tárolóhelyek is lehetővé teszi, hogy tesztelje az alkalmazás összes összetevőjét az aks-ben végez replikálást, vagy utánzási függőségek mentése nélkül. Iteratív futtathat, és közvetlenül az aks-ben a minimális fejlesztői gép telepítést tárolók hibakeresés.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést

Az Azure fejlesztési tárolóhelyek segítségével csapatai az ügyfelek közvetlenül a teljes mikroszolgáltatási architektúra vagy az aks-ben futó alkalmazás fejlesztését és azok mikroszolgáltatás-alkalmazások gyors ismétlését összpontosítsanak. Az Azure fejlesztési tárolóhelyek lehetőséget egymástól elkülönítve a mikroszolgáltatási architektúra részeit anélkül, hogy befolyásolná a többi az AKS-fürtöt, vagy más fejlesztők is biztosít. Az Azure fejlesztési tárolóhelyek fejlesztése és tesztelése alacsonyabb szintű fejlesztési és tesztelési környezetben, és nem javasolt éles környezetben az AKS-fürtök.

Mivel csapatok együttműködve a teljes alkalmazást, és közvetlenül az aks-ben, az Azure fejlesztési tárolóhelyek együttműködés:

* Minimalizálja a helyi gép beállítása
* Csökken a csapat új fejlesztők idő beállítása
* Egy csapat sebesség révén gyorsabban iteráció növekszik
* Kevesebb redundáns fejlesztése és integrációja környezetek óta a csapat tagjai megoszthat egy fürt
* Szükségtelenné teszi a replikálása, vagy a függőségek utánzása
* Együttműködés javítja a fejlesztői csapatok, valamint a csapatok működnek, mint például a DevOps-csapatok között

Az Azure fejlesztési tárolóhelyek azokat az eszközöket a projektek a Docker és a Kubernetes eszközök létrehozásához biztosít. Az eszközkészlet segítségével egyszerűen hozzáadhatja az új és meglévő alkalmazások fejlesztési szóközzel és más AKS-fürtök is.

Azure fejlesztői tárolóhelyek működésével kapcsolatos további információkért lásd: [hogyan Azure fejlesztési tárolóhelyek működik, és konfigurált][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Támogatott régiók és konfigurációk

Az Azure Dev Spacest csak az **USA keleti régiója**, **USA 2. keleti régiója**, **USA középső régiója**, **USA 2. nyugati régiója**, **Észak-Európa**, **Nyugat-Európa**, **az Egyesült Királyság déli régiója**, **Délkelet-Ázsia**, **Kelet-Ausztrália**, **Közép-Kanada** és **Kelet-Kanada** régió AKS-fürtjei támogatják. Az Azure Dev Spaces támogatja az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) és a [Visual Studio Code](https://code.visualstudio.com/download) használatát Linux, MacOS és Windows 8 vagy újabb rendszerű gépeken (ha azokon telepítve van az [Azure Dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)) az alkalmazások AKS-en való létrehozásához és futtatásához. Emellett támogatja a [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) telepítve a Windows 8-as vagy nagyobb. A Visual Studio 2019 szüksége lesz az Azure-fejlesztési számítási feladatot. A Visual Studio 2017, szüksége lesz a webalkalmazás-fejlesztési számítási feladatot és [Visual Studio Tools for Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>További lépések

További információ a gyors, iteratív fejlesztési csapat fejlesztési rövid útmutatóban az Azure fejlesztési tárolóhelyek csapatok számára.

> [!div class="nextstepaction"]
> [Fejlesztői csapat a rövid útmutató](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
