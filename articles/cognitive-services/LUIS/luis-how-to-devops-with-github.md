---
title: A DevOps alkalmazása a GitHubon – LUIS
titleSuffix: Azure Cognitive Services
description: Alkalmazzon DevOps a Language Understanding (LUIS) és a GitHub alkalmazásával.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 6df65040277ac61cca5fb4bf7fce5b5a7b2f3afe
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783760"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>A DevOps alkalmazása a LUIS-alkalmazások fejlesztésére GitHub-műveletek használatával

Nyissa meg a [Luis DevOps-sablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) tárházát egy teljes körű megoldáshoz, amely a Luis DevOps és szoftverfejlesztés ajánlott eljárásait valósítja meg. Ezt a sablon-tárházat használhatja, amely beépített támogatást biztosít a CI/CD-munkafolyamatokhoz, valamint olyan gyakorlatokhoz, amelyek lehetővé teszik a [verziókövetés](luis-concept-devops-sourcecontrol.md), az [automatizált buildek](luis-concept-devops-automation.md), a [tesztelés](luis-concept-devops-testing.md)és a [kiadási felügyelet kezelését](luis-concept-devops-automation.md#release-management) a saját projektje számára a Luis segítségével.

## <a name="the-luis-devops-template-repo"></a>A LUIS DevOps-sablon tárháza

A [Luis DevOps-sablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) tárháza végigvezeti a következő lépéseken:

* **A sablon klónozása** – másolja a sablont a saját GitHub-adattárba.
* **Luis-erőforrások konfigurálása** – létrehozhatja az Azure-beli [Luis Authoring and előrejelzési erőforrásokat](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-azure-subscription#create-resources-in-azure-cli) , amelyeket a folyamatos integrációs munkafolyamatok használni fognak.
* **A CI/CD-munkafolyamatok konfigurálása** – paraméterek konfigurálása a CI/CD-munkafolyamatokhoz és a [GitHub-titkok](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)tárolása.
* Bemutatja **a ["dev Inner loop"](https://mitchdenny.com/the-inner-loop/) ** -t – a fejlesztő frissíti a minta Luis-alkalmazást egy fejlesztési ág használata közben, teszteli a frissítéseket, majd lekéri a módosításokat, és megkeresi a felülvizsgálati jóváhagyást.
* **CI/CD-munkafolyamatok végrehajtása** – [folyamatos integrációs munkafolyamatok végrehajtása egy Luis-alkalmazás létrehozásához és teszteléséhez](luis-concept-devops-automation.md) GitHub-műveletek használatával.
* **Automatikus tesztelés elvégzése** – [automatizált batch-tesztelés végrehajtása egy Luis-alkalmazás](luis-concept-devops-testing.md) számára az alkalmazás minőségének kiértékeléséhez.
* **A Luis-alkalmazás üzembe helyezése** – [folyamatos kézbesítési (CD) feladatok](luis-concept-devops-automation.md#continuous-delivery-cd) végrehajtása a Luis-alkalmazás közzétételéhez.
* **A tárház használata saját projekttel** – elmagyarázza, hogyan használhatja a tárházat saját Luis-alkalmazásával.

## <a name="next-steps"></a>További lépések

* A [Luis DevOps-sablon](https://github.com/Azure-Samples/LUIS-DevOps-Template) -tárház használatával DevOps alkalmazhat a saját projekttel.
* [Verziókövetés és ág-stratégiák a LUIS számára](luis-concept-devops-sourcecontrol.md)
* [A LUIS DevOps tesztelése](luis-concept-devops-testing.md)
* [A LUIS DevOps Automation-munkafolyamatai](luis-concept-devops-automation.md)
