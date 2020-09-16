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
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 081c07be49178be2415edccbfc2026336eb8a8a5
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604410"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Munkaterület használata tűzfal mögött Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Firewallt a Azure Machine Learning-munkaterülethez és a nyilvános internethez való hozzáférés szabályozásához.   Ha többet szeretne megtudni a Azure Machine Learning biztonságossá tételéről, tekintse meg a [vállalati biztonság Azure Machine learning](concept-enterprise-security.md)

Míg a dokumentumban található információk a [Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)használatára épülnek, a többi tűzfallal is használható. Ha kérdése van a tűzfalon keresztüli kommunikáció engedélyezésével kapcsolatban, tekintse meg az Ön által használt tűzfal dokumentációját.

## <a name="application-rules"></a>Alkalmazásszabályok

A tűzfalon hozzon létre egy olyan _szabályt_ , amely engedélyezi a jelen cikkben szereplő címekre irányuló és onnan érkező forgalmat.

> [!TIP]
> A hálózati szabály hozzáadásakor állítsa a __protokollt__ bármelyik értékre, és a portokat a következőre: `*` .
>
> A Azure Firewall konfigurálásával kapcsolatos további információkért lásd: [Azure Firewall telepítése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

## <a name="routes"></a>Útvonalak

A Azure Machine Learning erőforrásokat tartalmazó alhálózat kimenő útvonalának konfigurálásakor a betanítási környezet biztonságossá tételéhez használja a [kényszerített bújtatás](how-to-secure-training-vnet.md#forced-tunneling) szakasz útmutatását.

## <a name="microsoft-hosts"></a>Microsoft-gazdagépek

Ha nincs megfelelően konfigurálva, a tűzfal problémákat okozhat a munkaterület használatával. A Azure Machine Learning munkaterület egyaránt használ különböző állomásnévket.

Az ebben a szakaszban található gazdagépek a Microsoft tulajdonában vannak, és biztosítják a munkaterület megfelelő működéséhez szükséges szolgáltatásokat.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **login.microsoftonline.com** | Hitelesítés |
| **management.azure.com** | A munkaterület adatainak beolvasásához használatos |
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
| **\*. notebooks.azure.net** | Azure Machine Learning Studióban található jegyzetfüzetek szükségesek. |
| **graph.windows.net** | Jegyzetfüzetekhez szükséges |

> [!TIP]
> Ha összevont identitást szeretne használni, kövesse az [ajánlott eljárásokat Active Directory összevonási szolgáltatások (AD FS) cikk biztonságossá tételéhez](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) .

## <a name="python-hosts"></a>Python-gazdagépek

Az ebben a szakaszban található gazdagépek a Python-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek. 

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Az alapértelmezett csomagok telepítéséhez használatos. |
| **\*. anaconda.org** | A tárház-adatgyűjtéshez használatos. |
| **pypi.org** | Az alapértelmezett indexből származó függőségek listázására használatos, és az indexet a felhasználói beállítások nem írják felül. Ha a rendszer felülírja az indexet, a ** \* . pythonhosted.org**is engedélyeznie kell. |

## <a name="r-hosts"></a>R-gazdagépek

A jelen szakaszban található gazdagépek az R-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek.

> [!IMPORTANT]
> Belsőleg az R SDK for Azure Machine Learning Python-csomagokat használ. Így a Python-gazdagépeket is engedélyeznie kell a tűzfalon.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **cloud.r-project.org** | A CRAN-csomagok telepítésekor használatos. |

## <a name="azure-government-region"></a>Azure Government régió

A Azure Government-régiókhoz szükséges URL-címek.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | Az USA – Arizona régió |
| **usgovvirginia.api.ml.azure.us** | Az USA-Virginia régió |

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](../firewall/tutorial-firewall-deploy-portal.md)
* [Biztonságos Azure ML-kísérletezés és következtetési feladatok egy Azure-beli virtuális hálózaton belül](how-to-network-security-overview.md)
