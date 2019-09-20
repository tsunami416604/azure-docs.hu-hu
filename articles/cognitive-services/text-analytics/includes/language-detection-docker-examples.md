---
title: Nyelvfelismerés Container Docker-példák
titleSuffix: Azure Cognitive Services
description: Nyelvfelismerés Container Docker-példák
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 6e09a19ceb7b892a8ec405b348700b65c9d55ec4
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148425"
---
### <a name="language-detection-container-docker-examples"></a>Nyelvfelismerés Container Docker-példák

A következő Docker-példák a Nyelvfelismerés tárolóra vonatkoznak.

#### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Példa naplózásra 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
