---
title: Folyamatos integráció Azure folyamat – a csoportos adatelemzési folyamat létrehozása
description: 'Fejlesztési és üzemeltetési mesterséges intelligencia (AI) alkalmazások esetén: Folyamatos integrációs folyamat létrehozása az Azure-ban a Docker és a Kubernetes használatával'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: d99149f8112c19a07208523a1ee26ba1c36e5362
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474224"
---
# <a name="creating-continuous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Folyamatos integrációs folyamat létrehozása az Azure-ban a Docker, Kubernetes- és Python Flask-alkalmazás
AI-alkalmazások a munkahelyi, machine learning-modellek és az alkalmazás létrehozása és a végfelhasználók számára való használata adatokhoz hozzáférést biztosító alkalmazás-fejlesztők az Adatszakértők gyakran két adatfolyamot vannak. Ebben a cikkben bemutatjuk, hogyan valósíthat meg egy folyamatos integrációs (CI) / folyamatos kézbesítési (CD) folyamat AI-alkalmazáshoz. AI-alkalmazások beépítve imagenet machine learning (gépi tanulás) modell alkalmazáskód kombinációját. Ebben a cikkben azt is pretrained modell beolvasása a saját Azure blob storage-fiók, annak oka az AWS S3 szintű fiók is lehet. A cikk egy egyszerű python flask-webalkalmazás használjuk.

> [!NOTE]
> Ez az egyik többféleképpen is elvégezhető a CI/CD-ről. Nincsenek alternatív megoldások az eszközök és egyéb alábbi előfeltételekkel. Ahogy további tartalmat kifejlesztett, mi pedig közzétesszük azokat.
>
>

## <a name="github-repository-with-document-and-code"></a>A dokumentum és a kód a GitHub-adattár
Letöltheti a forráskódot a [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). A [részletes oktatóanyag](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) is rendelkezésre áll.

## <a name="pre-requisites"></a>Előfeltételek
Az alábbiakban a CI/CD-folyamat a következő előfeltételei a következők:
* [Az Azure DevOps-szervezet](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Futó Kubernetes Azure Container Service (AKS)-fürt](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Az Azure Container Registry (ACR) fiók](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Telepítse a Kubectl a Kubernetes-fürt parancsok futtatásához.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Szükségünk lesz ez az ACS-fürt konfigurációs beolvasni. 
* A GitHub-fiók a tárház elágaztatása.

## <a name="description-of-the-cicd-pipeline"></a>A CI/CD-folyamat leírása
A folyamat megrúgni ki minden egyes új véglegesítés, a vizsgálati csomag futni, ha a tesztelési fázisban vesz igénybe a legújabb buildben az csomagok Docker-tárolóban. A tároló majd helyezünk üzembe az Azure Container Service (ACS) és képek a rendszer biztonságosan tárolja az Azure Container Registry (ACR). Az ACS Kubernetes fut tárolófürt kezeléséhez, de választhat a Docker Swarm és a Mesos.

Az alkalmazás biztonságos kér le a legújabb modellre az Azure Storage-fiók és a csomagok, amelyek az alkalmazás részeként. Az üzembe helyezett alkalmazást a kód és a gépi Tanulási modellek egyetlen tárolót csomagolt rendelkezik. Ez leválasztja az alkalmazást a fejlesztők és az adatelemző szakemberek, győződjön meg arról, hogy az éles alkalmazások mindig fut a legújabb kódot a legújabb gépi Tanulási modellek.

A folyamat architektúra az alábbi táblázat a. 

![Architektúra](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>A lépések a CI/CD-folyamat
1. Fejlesztői működik az IDE az alkalmazás kódjának a választott eszközükön.
2. Ezek véglegesíthesse a kódot verziókövetési az általuk választott (az Azure DevOps rendelkezik jó támogatást nyújt a különböző adatforrás-vezérlők)
3. Külön-külön az adatszakértő dolgozhat a modell fejlesztéséhez.
4. Miután Boldog, akkor közzétesszük a modellt egy modell tárházba, ebben az esetben használjuk egy blob storage-fiók. 
5. Egy megkezdődik a fordítási az Azure DevOps, a github véglegesítési alapján.
6. Az Azure DevOps-Buildelési folyamat lekéri a legújabb modellre a Blob-tárolóba, és létrehoz egy tárolót.
7. Az Azure DevOps leküldi a lemezképet az Azure Container Registry privát rendszerkép-adattárhoz
8. A beállított ütemezés szerint (éjszakai) kezdődjön kibocsátási folyamatok.
9. Legújabb rendszerképet az ACR-ből beolvasott, és a telepített ACS Kubernetes-fürt.
10. Az alkalmazás felhasználói jóváhagyás végighalad a DNS-kiszolgáló.
11. DNS-kiszolgáló a terheléselosztó továbbítja, és visszaküldi a választ felhasználói.

## <a name="next-steps"></a>További lépések
* Tekintse meg a [oktatóanyag](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) kövesse a részleteket, és a saját CI/CD-folyamat az alkalmazás végrehajtása.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat (TDSP)](https://aka.ms/tdsp)
* [Az Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Az Azure DevOps](https://www.visualstudio.com/vso/)
* [Az Azure Kubernetes-szolgáltatás (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
