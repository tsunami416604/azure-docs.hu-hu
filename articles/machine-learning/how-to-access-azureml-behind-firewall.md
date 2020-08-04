---
title: Tűzfal használata
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-munkaterületek Azure-tűzfalakkal való hozzáférésének szabályozása. Ismerje meg azokat a gazdagépeket, amelyeket engedélyezni kell a tűzfalon a Azure Machine Learning megfelelő működéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 27b625dfa31b366d95922e1dd0bad7fda6e86ed4
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87540070"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Munkaterület használata tűzfal mögött Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Firewallt Azure Machine Learning-munkaterülettel való használatra.

> [!IMPORTANT]
> Míg a dokumentumban található információk a Azure Firewall használatára épülnek, a többi tűzfallal is használható. Ha kérdése van a tűzfalon keresztüli kommunikáció engedélyezésével kapcsolatban, tekintse meg az Ön által használt tűzfal dokumentációját.

A Azure Firewall a Azure Machine Learning-munkaterülethez és a nyilvános internethez való hozzáférés szabályozására használható. Ha nincs megfelelően konfigurálva, a tűzfal problémákat okozhat a munkaterület használatával. A Azure Machine Learning munkaterület számos különböző állomásnevet használ, amelyek a jelen cikkben olvashatók.

## <a name="network-rules"></a>Hálózatszabályok

A tűzfalon hozzon létre egy hálózati szabályt, amely engedélyezi a cikkben szereplő címekre irányuló és onnan érkező forgalmat.

> [!TIP]
> A hálózati szabály hozzáadásakor állítsa a __protokollt__ bármelyik értékre, és a portokat a következőre: `*` .
>
> A Azure Firewall konfigurálásával kapcsolatos további információkért lásd: [Azure Firewall telepítése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule).

## <a name="microsoft-hosts"></a>Microsoft-gazdagépek

Az ebben a szakaszban található gazdagépek a Microsoft tulajdonában vannak, és biztosítják a munkaterület megfelelő működéséhez szükséges szolgáltatásokat.

| **Állomásnév** | **Cél** |
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
| **your-acr-server-name.azurecr.io** | Csak akkor szükséges, ha a Azure Container Registry a virtuális hálózat mögött van. Ebben a konfigurációban a Microsoft-környezetből a saját előfizetésében található ACR-példányhoz privát hivatkozás jön létre. Használja az ACR-kiszolgáló nevét az Azure Machine Learning munkaterülethez. |

## <a name="python-hosts"></a>Python-gazdagépek

Az ebben a szakaszban található gazdagépek a Python-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek. 

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **anaconda.com** | Az alapértelmezett csomagok telepítéséhez használatos. |
| **\*. anaconda.org** | A tárház-adatgyűjtéshez használatos. |
| **pypi.org** | Az alapértelmezett indexből származó függőségek listázására használatos, és az indexet a felhasználói beállítások nem írják felül. Ha a rendszer felülírja az indexet, a ** \* . pythonhosted.org**is engedélyeznie kell. |

## <a name="r-hosts"></a>R-gazdagépek

A jelen szakaszban található gazdagépek az R-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek.

> [!IMPORTANT]
> Belsőleg az R SDK for Azure Machine Learning Python-csomagokat használ. Így a Python-gazdagépeket is engedélyeznie kell a tűzfalon.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **cloud.r-project.org** | A CRAN-csomagok telepítésekor használatos. |

További lépések

* [[Azure Firewall üzembe helyezése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md)]
* [Egy Azure-Virtual Networkon belül biztonságossá teheti az Azure ML-kísérletezést és a feladatok következtetéseit](how-to-enable-virtual-network.md)
