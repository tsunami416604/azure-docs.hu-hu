---
title: 'A mesterséges intelligencia (AI) alkalmazások DevOps: folyamatos integráció csővezeték Azure-ban a Docker, Kubernetes & Python Flask-alkalmazás létrehozása'
description: 'A mesterséges intelligencia (AI) alkalmazások DevOps: Azure-ban a Docker és Kubernetes folyamatos integráció folyamat létrehozása'
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: d79453e72bf26ce5d14fac71e35a146381b951fa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655157"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>A mesterséges intelligencia (AI) alkalmazások DevOps: Azure-ban a Docker és Kubernetes folyamatos integrációt folyamat létrehozása
AI kérelmet vonatkoznak-e a munkahelyi, a machine learning modellek és az alkalmazás összeállítása, és adjon meg, a végfelhasználók számára való felhasználására alkalmazásfejlesztők felépítése Adatszakértőkön gyakran két adatfolyamokat. Ebben a témakörben bemutatjuk, hogyan megvalósításához a folyamatos integrációs (CI) / folyamatos kézbesítési (CD) a következő feldolgozási sorban AI alkalmazásokra vonatkozóan. AI alkalmazásra az alkalmazás kódja egy pretrained machine learning (ML) modell a beágyazott kombinációja. Ez a cikk azt egy pretrained modell vannak beolvasása a saját Azure blob storage-fiók, az AWS S3 fiók is. A cikk egy egyszerű python flask-webalkalmazás azt fogja használni.

> [!NOTE]
> Ez az számos módon CI/CD végezheti el. Nincsenek a tooling és egyéb szükséges előfeltételek alábbi kínált alternatívákat. Mivel további tartalmat elkészítéséhez azt fogunk közzé tenni azokat.
>
>

## <a name="github-repository-with-document-and-code"></a>GitHub-tárházban dokumentum és kód
Letöltheti a forráskódot [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). A [részletes oktatóanyag](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) is rendelkezésre áll.

## <a name="pre-requisites"></a>Előfeltételek
Az alábbiakban a CI/CD-feldolgozási folyamat következő szükséges előfeltételek a következők:
* [Visual Studio Team Services Account](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure-tároló szolgáltatás (AKS) fürttel Kubernetes fut](https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Azure-tároló rendszerleíró adatbázisban (ACR) fiók](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal)
* [Telepítse a Kubectl Kubernetes fürt parancsok futtatásához.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Igazolnia kell a konfiguráció beolvasása a ACS-fürthöz. 
* Elágazás a tárház GitHub-fiókjában.

## <a name="description-of-the-cicd-pipeline"></a>A CI/CD-feldolgozási folyamat leírása
A feldolgozási sor megrúgni ki minden új véglegesítés, futtassa a vizsgálati csomag a tesztelési fázisban veszi a legújabb buildjével, ha a csomagok egy Docker-tároló. A tároló majd használatával lett telepítve az Azure tárolószolgáltatás (ACS), és biztonságosan tárolt képek Azure tároló beállításjegyzékben (ACR). ACS Kubernetes fut a tároló fürt kezelése, de Docker Swarm vagy Mesos.

Az alkalmazás biztonságos kéri le a legújabb Azure Storage-fiók és a csomagok modell, amely az alkalmazás részeként. A telepített alkalmazás az alkalmazás kódot és az egyetlen tároló csomagolt ML modellje van. Ez leválasztja az alkalmazást a fejlesztők és az adatok kutatók, győződjön meg arról, hogy az éles alkalmazások mindig fut a legújabb kód legújabb ML-modell.

Az alábbi táblázat a csővezeték architektúráját. 

![Architektúra](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>A CI/CD-feldolgozási folyamat lépései
1. Fejlesztői az ide-hellyel, az alkalmazás kódjának a választott működik.
2. A verziókövetési az általuk választott (VSTS rendelkezik jó támogatása különböző adatforrás-vezérlők) azok véglegesíthesse a kódot
3. Az adatok tudósok külön-külön, a modell fejlesztésével működik.
4. Ha elégedett, ezek teszik közzé a modell, a modell tárházba, ebben az esetben használjuk a blob storage-fiók. Ez sikerült könnyű cserélhetősége érdekében az Azure ML munkaterület modell felügyeleti szolgáltatás a REST API-kon keresztül.
5. Build a github véglegesítési alapján VSTS kezdődött el.
6. Adatcsatorna VSTS-Build Blob tároló lekéri a legfrissebb verzióra, és létrehoz egy tárolót.
7. VSTS titkos lemezképtárba Azure tároló beállításjegyzék leküldéses értesítések a kép
8. Ütemezés szerint (éjszakánként), a kiadás csővezeték kezdődött el.
9. ACR legújabb lemezkép lekért és az ACS Kubernetes fürtön telepítve.
10. Az alkalmazás felhasználók kérelem végighalad a DNS-kiszolgáló.
11. DNS-kiszolgáló továbbítja a kérelmet a terheléselosztóhoz, és visszaküldi a választ felhasználó.

## <a name="next-steps"></a>További lépések
* Tekintse meg a [oktatóanyag]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)) kövesse a részletes adatait, és valósítja meg az alkalmazás a saját CI/CD folyamat.

## <a name="references"></a>Referencia
* [Team adatok tudományos folyamat (TDSP)](https://aka.ms/tdsp)
* [Az Azure Machine Learning (AML)](https://docs.microsoft.com/en-us/azure/machine-learning/service/)
* [A Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Az Azure Kubernetes szolgáltatások (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes)