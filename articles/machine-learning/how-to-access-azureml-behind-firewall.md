---
title: Tűzfal használata
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-munkaterületek Azure-tűzfalakkal való hozzáférésének szabályozása. Ismerje meg azokat a gazdagépeket, amelyeket engedélyezni kell a tűzfalon a Azure Machine Learning megfelelő működéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/27/2020
ms.custom: tracking-python
ms.openlocfilehash: 31daec93352c0e142075a55c61f2b8d3a6d56fab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080230"
---
# <a name="use-workspace-behind-azure-firewall-for-azure-machine-learning"></a>Azure Firewall mögötti munkaterület használata Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Firewallt Azure Machine Learning-munkaterülettel való használatra.

A Azure Firewall a Azure Machine Learning-munkaterülethez és a nyilvános internethez való hozzáférés szabályozására használható. Ha nincs megfelelően konfigurálva, a tűzfal problémákat okozhat a munkaterület használatával. A Azure Machine Learning munkaterület számos különböző állomásnevet használ, amelyek a jelen cikkben olvashatók.

## <a name="network-rules"></a>Hálózatszabályok

A tűzfalon hozzon létre egy hálózati szabályt, amely engedélyezi a cikkben szereplő címekre irányuló és onnan érkező forgalmat.

> [!TIP]
> A hálózati szabály hozzáadásakor állítsa a __protokollt__ bármelyik értékre, és a portokat a következőre: `*` .
>
> A Azure Firewall konfigurálásával kapcsolatos további információkért lásd: [Azure Firewall telepítése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Microsoft-gazdagépek

Az ebben a szakaszban található gazdagépek a Microsoft tulajdonában vannak, és biztosítják a munkaterület megfelelő működéséhez szükséges szolgáltatásokat.

| **Állomásnév** | **Szerep** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | Csoportok betanítása |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | A Azure Machine Learning Studio használja |
| **\*. azureml.ms** | Azure Machine Learning API-k használják |
| **\*. experiments.azureml.net** | A Azure Machine Learningban futó kísérletek használják |
| **\*. modelmanagement.azureml.net** | Modellek regisztrálásához és üzembe helyezéséhez használatos|
| **mlworkspace.azure.ai** | A munkaterületek megtekintésekor használt Azure Portal |
| **\*. aether.ms** | Azure Machine Learning folyamatok futtatásakor használatos |
| **\*. instances.azureml.net** | Számítási példányok Azure Machine Learning |
| **\*. instances.azureml.ms** | Azure Machine Learning számítási példányok, ha a munkaterület saját hivatkozás engedélyezve van |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | Microsoft Container Registry az alapszintű Docker-rendszerképekhez |

## <a name="python-hosts"></a>Python-gazdagépek

Az ebben a szakaszban található gazdagépek a Python-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek. 

| **Állomásnév** | **Szerep** |
| ---- | ---- |
| **anaconda.com** | Conda-csomagok telepítésekor használatos |
| **pypi.org** | Pip-csomagok telepítésekor használatos |

## <a name="r-hosts"></a>R-gazdagépek

A jelen szakaszban található gazdagépek az R-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek.

> [!IMPORTANT]
> Belsőleg az R SDK for Azure Machine Learning Python-csomagokat használ. Így a Python-gazdagépeket is engedélyeznie kell a tűzfalon.

| **Állomásnév** | **Szerep** |
| ---- | ---- |
| **cloud.r-project.org** | A CRAN-csomagok telepítésekor használatos. |

További lépések

* [[Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)]
* [Egy Azure-Virtual Networkon belül biztonságossá teheti az Azure ML-kísérletezést és a feladatok következtetéseit](how-to-enable-virtual-network.md)
