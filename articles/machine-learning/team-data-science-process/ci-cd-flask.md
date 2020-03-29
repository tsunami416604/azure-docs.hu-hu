---
title: CI/CD-folyamat létrehozása az Azure-folyamatok segítségével – Csapatadat-elemzési folyamat
description: Hozzon létre egy folyamatos integrációs és folyamatos kézbesítési folyamatot a mesterséges intelligencia (AI) alkalmazásokhoz a Docker és a Kubernetes használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721829"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>CI/CD-folyamatok létrehozása AI-alkalmazásokhoz az Azure Pipelines, a Docker és a Kubernetes használatával

A mesterséges intelligencia (AI) alkalmazás egy előre betanított gépi tanulási (ML) modellbe ágyazott alkalmazáskód. Egy AI-alkalmazáshoz mindig két munkafolyam van: Az adatszakértők létreadik az ML-modell, az alkalmazásfejlesztők pedig létreépítik az alkalmazást, és elérhetővé teszik azt a végfelhasználók számára. Ez a cikk bemutatja, hogyan valósítható meg a folyamatos integrációs és folyamatos kézbesítési (CI/CD) folyamat egy AI-alkalmazás, amely beágyazza a gépi tanulási modell az alkalmazás forráskódjába. A mintakód és az oktatóanyag egy Python Flask webalkalmazást használ, és egy előre betanított modellt egy privát Azure blob storage-fiókból. Használhat Egy AWS S3 tárfiókot is.

> [!NOTE]
> A következő folyamat a CI/CD számos módja. Vannak alternatívák, hogy ezt a szerszámot, és az előfeltételeket.

## <a name="source-code-tutorial-and-prerequisites"></a>Forráskód, oktatóanyag és előfeltételek

Letöltheti [a forráskódot](https://github.com/Azure/DevOps-For-AI-Apps) és a [részletes oktatóanyagot](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) a GitHubról. Kövesse az oktatóanyag lépéseit a CI/CD-folyamat megvalósításához a saját alkalmazásához.

A letöltött forráskód és oktatóanyag használatához a következő előfeltételekre van szükség: 

- A GitHub-fiókhoz tartozó [forráskódtár-tárház](https://github.com/Azure/DevOps-For-AI-Apps)
- [Egy Azure DevOps Szervezet](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Egy Azure Container Szolgáltatás Kubernetes (AKS) fürthöz](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) parancsok futtatásához és konfiguráció lehívásához az AKS-fürtből 
- [Egy Azure Container Registry (ACR) fiók](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD csővezeték összegzése

Minden új Git véglegesítése elindítja a Build-folyamatot. A build biztonságosan lekéri a legújabb ml-modellt egy blob storage-fiókból, és egyetlen tárolóban csomagolja az alkalmazáskóddal. Az alkalmazásfejlesztési és adatelemzési munkafolyamok ez a függetlenítése biztosítja, hogy az éles alkalmazás mindig a legújabb kód futtatásához szükséges a legújabb rendszerértékelési modellel. Ha az alkalmazás megfelel a tesztelésen, a folyamat biztonságosan tárolja a buildrendszerképet egy Docker-tárolóban az ACR-ben. A kiadási folyamat ezután telepíti a tárolót az AKS használatával. 

## <a name="cicd-pipeline-steps"></a>CI/CD-folyamat lépései

Az alábbi ábra és lépések a CI/CD-folyamat architektúráját ismertetik:

![CI/CD-folyamat architektúra](./media/ci-cd-flask/architecture.png)

1. A fejlesztők az általuk választott IDE-ben dolgoznak az alkalmazáskódon.
2. A fejlesztők véglegesítik a kódot az Azure Repos, A GitHub vagy más Git forrásvezérlő szolgáltató. 
3. Külön-külön, adatszakértők dolgoznak a fejlődő ml-modell.
4. Az adatszakértők közzéteszik a kész modellt egy modelltárházban, ebben az esetben egy blob storage-fiókban. 
5. Az Azure-folyamatok a Git véglegesítése alapján indítja el a buildet.
6. A build folyamat lekéri a legújabb ML-modell blob storage-ból, és létrehoz egy tárolót.
7. The pipeline pushes the build image to the private image repository in ACR.
8. A kiadási folyamat elindul a sikeres build alapján.
9. A folyamat lekéri a legújabb lemezképet az ACR-ből, és telepíti azt a Kubernetes-fürtön az AKS-en.
10. Az alkalmazás felhasználói kérelmei a DNS-kiszolgálón keresztül haladnak.
11. A DNS-kiszolgáló továbbítja a kérelmeket egy terheléselosztónak, és visszaküldi a válaszokat a felhasználóknak.

## <a name="see-also"></a>Lásd még

- [Csapatadat-elemzési folyamat (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Azure Machine Learning (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes-szolgáltatások (AKS)](/azure/aks/intro-kubernetes)
