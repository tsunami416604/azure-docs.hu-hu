---
title: Bevezetés az Azure dev spaces-be
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Ismerje meg, hogy az Azure Dev Spaces hogyan biztosít gyors, ismétlődő Kubernetes fejlesztési élményt az Azure Kubernetes szolgáltatásfürtök csapatai számára
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes szolgáltatás, tárolók, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 586b19070ec36517add21f7aac86ddf15121be2d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240701"
---
# <a name="introduction-to-azure-dev-spaces"></a>Bevezetés az Azure dev spaces-be

Az Azure Dev Spaces gyors, ismétlődő Kubernetes fejlesztési élményt nyújt az Azure Kubernetes-szolgáltatások (AKS) fürtjein lévő csapatok számára. Az Azure Dev Spaces azt is lehetővé teszi, hogy hibakeresés és az alkalmazás összes összetevője az AKS minimális fejlesztői gép beállítása, anélkül, replikálása vagy gúnyolódása függőségek.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hogyan egyszerűsíti le az Azure Dev Spaces a Kubernetes fejlesztést

Az Azure Dev Spaces segítségével a csapatok a mikroszolgáltatási alkalmazásfejlesztésére és gyors iterációjára összpontosíthatnak azáltal, hogy lehetővé teszik a csapatok számára, hogy közvetlenül együttműködjenek a teljes mikroszolgáltatási architektúrájukkal vagy az AKS-ben futó alkalmazásukkal. Az Azure Dev Spaces is lehetővé teszi, hogy önállóan frissítse a mikroszolgáltatási architektúra egyes részeit elszigetelten anélkül, hogy befolyásolná az AKS-fürt többi részét vagy más fejlesztőket. Az Azure Dev Spaces alacsonyabb szintű fejlesztési és tesztelési környezetekben való fejlesztéshez és teszteléshez szolgál, és nem éles AKS-fürtökön való futtatásra szolgál.

Mivel a csapatok a teljes alkalmazással dolgozhatnak, és közvetlenül az AKS-ben működhetnek együtt, az Azure dev Spaces:Since teams can work with the entire application and collaboration directly in AKS, Azure Dev Spaces:

* Minimálisra csökkenti a helyi számítógép beállítását
* Csökkenti a csapat új fejlesztőinek beállítási idejét
* Növeli a csapat sebességét a gyorsabb iterációval
* Csökkenti a redundáns fejlesztési és integrációs környezetek számát, mivel a csapattagok megoszthatják a fürtöt
* Megszünteti a függőségek replikálásának vagy makettjének szükségességét
* Javítja az együttműködést a fejlesztőcsapatok és a csapatokkal, például a DevOps-csapatok között

Az Azure Dev Spaces eszközöket biztosít a Docker- és Kubernetes-eszközök létrehozásához a projektekhez. Ez az eszközelvégezhető lehetővé teszi új és meglévő alkalmazások egyszerű hozzáadását a fejlesztési területhez és más AKS-fürtökhöz.

Az Azure Dev Spaces működéséről az Azure Dev Spaces működésének és [konfigurálásának című témakörben talál további][how-dev-spaces-works]információt.

## <a name="supported-regions-and-configurations"></a>Támogatott régiók és konfigurációk

Az Azure dev spaces-t csak [bizonyos régiókban][supported-regions]az AKS-fürtök támogatják. Az Azure Dev Spaces támogatja az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) és a [Visual Studio Code](https://code.visualstudio.com/download) használatát Linux, MacOS és Windows 8 vagy újabb rendszerű gépeken (ha azokon telepítve van az [Azure Dev Spaces bővítmény](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)) az alkalmazások AKS-en való létrehozásához és futtatásához. Támogatja a Windows 8 vagy nagyobb rendszerre telepített [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) használatát is. A Visual Studio 2019-hez szüksége lesz az Azure Development számítási feladatra. A Visual Studio 2017-ben szüksége lesz a Web Development munkaterhelésre és a [Visual Studio Tools for Kubernetes-re.](https://aka.ms/get-vsk8stools)

## <a name="next-steps"></a>További lépések

Tudjon meg többet a gyors, ismétlődő fejlesztés csapatok az Azure Dev Spaces a csapatfejlesztési rövid útmutató.

> [!div class="nextstepaction"]
> [Rövid útmutató a csapatfejlesztéshez](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
