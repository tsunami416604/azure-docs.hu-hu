---
title: Bevezetés az Azure dev Spaces használatába
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Bevezetés az Azure dev Spaces használatába
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e166cb9368a14590cbeaabda05d05696032dd94f
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73061307"
---
# <a name="azure-dev-spaces"></a>Azure Dev Spaces

Az Azure dev Spaces egy gyors, ismétlődő Kubernetes-fejlesztési élmény az Azure Kubernetes Service (ak)-fürtökben található csapatok számára. Közös AK-fürtben dolgozhat a csapatával. Az Azure dev Spaces lehetővé teszi az alkalmazás összes összetevőjének az AK-ban való tesztelését anélkül, hogy a függőségeket replikálni vagy kigúnyolni kellene. A tárolók futtatásához és hibakereséséhez közvetlenül az AK-ban minimális fejlesztői iteratív kell telepíteni.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést

Az Azure dev Spaces lehetővé teszi a csapatok számára, hogy a Service-alkalmazás fejlesztésére és gyors iterációra összpontosítsanak azáltal, hogy a csapatok közvetlenül a teljes, AK-ban futó Service-architektúrával vagy alkalmazással működnek. Az Azure dev Spaces lehetővé teszi, hogy a rendszer elkülönítetten frissítse a szolgáltatás-architektúra egyes részeit anélkül, hogy ez hatással lenne a többi AK-fürtre vagy más fejlesztőre. Az Azure dev Spaces fejlesztésre és tesztelésre szolgál az alacsonyabb szintű fejlesztési és tesztelési környezetekben, és nem a termelési AK-fürtökön való futtatásra készült.

Mivel a csapatok a teljes alkalmazással dolgozhatnak, és együttműködnek közvetlenül az AK-ban, az Azure dev Spaces szolgáltatásban:

* A helyi gép beállításának csökkentése
* A csapat új fejlesztői számára beállított idő csökkentése
* Gyorsabb iterációval növeli a csapat sebességét
* Csökkenti a redundáns fejlesztési és integrációs környezetek számát, mivel a csapattagok megoszthatnak egy fürtöt
* Megszünteti a függőségek replikálásának vagy kimodellezésének szükségességét
* Javítja a fejlesztési csapatok és a velük dolgozó csapatok együttműködését, például a DevOps csapatait

Az Azure dev Spaces lehetővé teszi a Docker-és Kubernetes-eszközök létrehozását a projektekhez. Ez az eszköz lehetővé teszi új és meglévő alkalmazások egyszerű hozzáadását a fejlesztői és egyéb AK-fürtökhöz.

Az Azure dev Spaces működésével kapcsolatos további információkért lásd: [Az Azure dev Spaces működése és konfigurálása][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Támogatott régiók és konfigurációk

Az Azure dev-tárhelyeket csak az **USA keleti**régiójában, az **USA 2. keleti**régiójában, az USA **déli középső**régiójában, az USA **2. nyugati**régiójában, észak- **Európában**, **Nyugat-Európában**, **Egyesült Királyság déli régiója**, **Kelet-Ázsiaban**,  **Délkelet-Ázsia**, **Kelet-Ausztrália**, **Közép-Kanada**és Kelet- **Kanada** régiók. Az Azure Dev Spaces támogatja az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) és a [Visual Studio Code](https://code.visualstudio.com/download) használatát Linux, MacOS és Windows 8 vagy újabb rendszerű gépeken (ha azokon telepítve van az [Azure Dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)) az alkalmazások AKS-en való létrehozásához és futtatásához. Emellett támogatja a Windows 8 vagy újabb rendszerre telepített [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) használatát is. A Visual Studio 2019 esetében szüksége lesz az Azure-fejlesztési számítási feladatra. A Visual Studio 2017 esetében szüksége lesz a webes fejlesztési számítási feladatra és a [Visual Studio-eszközökre a Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet az Azure fejlesztői útmutatóval rendelkező csapatok gyors, ismétlődő fejlesztéséről.

> [!div class="nextstepaction"]
> [A Team Development gyors üzembe helyezése](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
