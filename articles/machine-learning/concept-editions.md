---
title: Nagyvállalati és alapszintű kiadások Azure Machine Learning
description: Ismerje meg a Azure Machine Learning kiadásai közötti különbségeket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508602"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>A nagyvállalati (előzetes verzió) és a Azure Machine Learning alapszintű kiadásai 

Azure Machine Learning két, a gépi tanulási igényeihez igazított kiadást kínál. Ezek a kiadások határozzák meg, hogy mely gépi tanulási eszközök érhetők el a fejlesztők és az adatszakértők számára a munkaterületről.

## <a name="choose-an-edition"></a>Kiadás kiválasztása

A kiadást a munkaterület létrehozásakor rendeli hozzá. Az ügyfelek felelősek a számítási és egyéb Azure-erőforrásokért felmerülő költségekért. Útmutató a [Azure Machine learning költségeinek kezeléséhez](concept-plan-manage-cost.md).

Megtudhatja, hogyan [frissíthet egy alapszintű munkaterületet nagyvállalati (előzetes verzió) kiadásra](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Az egyes kiadások

### <a name="data-for-machine-learning-capabilities"></a>A Machine Learning képességeinek adatvédelme  

| Képességek                     | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Címkézés: [címkézési projektek létrehozása és kezelése](tutorial-labeling.md) a Studióban (web)                                                | Mind                     |
| Címkézés: Labeler a Studióban (web)                                    | Mind                     |
| Címkézés: saját munkaerő használata                               | Mind                     |
| Címkézés: [ml támogatott képek besorolása és objektumok észlelése](how-to-label-images.md)                  | Csak Enterprise Edition |
| Adatkészletek és adattárolók: létrehozás és kezelés Pythonban                       | Mind                     |
| Adatkészletek és adattárolók: létrehozás és kezelés a Studióban (web)                         | Mind                     |
| Drift: adatkészlet-figyelők megtekintése és kezelése a Pythonban                           | Mind                     |
| Drift: adatkészlet-figyelők megtekintése és kezelése a Studióban (web)                            | Csak Enterprise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Automatizált betanítási képességek (AutoML)

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [AutoML-kísérletek](how-to-configure-auto-train.md) létrehozása és futtatása jegyzetfüzetekben               | Mind                     |
| [AutoML-kísérletek létrehozása és futtatása a Studióban (web)](how-to-use-automated-ml-for-ml-models.md)   | Csak Enterprise Edition |
| Piacvezető AutoML-előrejelzési képességek             | Csak Enterprise Edition |
| A Deep learning és az egyéb fejlett tanulók támogatása | Csak Enterprise Edition |
| Nagyméretű adatok támogatása besorolási és regressziós feladatok (legfeljebb 100 GB)                     | Csak Enterprise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Felelős Machine Learning

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Modell-magyarázat](how-to-machine-learning-interpretability-automl.md)                                              | Mind                     |
| [Különbözeti adatvédelem](how-to-differential-privacy.md)                          | Mind                     |
| Egyéni címkék az adatlapok megvalósításához    | Mind                     |
| Méltányos AzureML-integráció                                      | Mind                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Képességek kiépítése és betanítása

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code-integráció                                                     | Mind                     |
| Megerősítő tanulás                                                             | Mind                     |
| Kísérletezés felhasználói felülete                                                                 | Mind                     |
| Jupyter, JupyterLab-integráció                                                    | Mind                     |
| Python SDK-támogatás                                                                 | Mind                     |
| R SDK-támogatás                                                                      | Mind                     |
| ML-folyamatok: létrehozás, Futtatás és közzététel Pythonban                           | Mind                     |
| ML-folyamatok: folyamatok ütemezett futtatásának létrehozása, szerkesztése és törlése a Pythonban| Mind                     |
| ML-folyamatok: folyamat-végpontok létrehozása a Python SDK-ban                                   | Mind                     |
| ML-folyamatok: a Futtatás részleteinek megtekintése a Studióban (web)                                              | Mind                     |
| ML-folyamatok: létrehozás, Futtatás, megjelenítés és közzététel a Designerben                  | Csak Enterprise Edition |
| ML-folyamatok: folyamat-végpontok létrehozása a Designerben | Csak Enterprise Edition |
| Felügyelt számítási példányok integrált jegyzetfüzetekhez                                 | Mind                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Üzembe helyezési és modellezési képességek

| Képességek                            | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Az Azure DevOps bővítmény a Machine Learning és a Azure ML CLI                 | Mind                     |
| [Event Grid-integráció](how-to-use-event-grid.md)                                                             | Mind                     |
| Azure Stream Analytics integrálása Azure Machine Learning                       | Mind                     |
| ML-folyamatok létrehozása az SDK-ban                                                         | Mind                     |
| A Batch-következtetések                                                                  | Mind                     |
| FPGA-alapú Hardware Accelerated Models                                             | Mind                     |
| Modell profilkészítése                                                                    | Mind                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Biztonság, irányítás és vezérlési képességek

| Képességek     | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](how-to-assign-roles.md) támogatása                                           | Mind                     |
| [Virtual Network (VNet)-](how-to-enable-virtual-network.md) támogatás a számítási feladatokhoz                                         | Mind                     |
| Pontozási végpont hitelesítése                                                    | Mind                     |
| [Munkaterület privát hivatkozása](how-to-configure-private-link.md)                                                            | Mind                     |
| [Kvótakezelés](how-to-manage-quotas.md) a munkaterületek között                                                 | Csak Enterprise Edition |

## <a name="next-steps"></a>További lépések

További információ a Azure Machine Learning [Edition áttekintése és díjszabása lapon](https://azure.microsoft.com/pricing/details/machine-learning/)elérhető tudnivalókról. 

Megtudhatja, hogyan [frissíthet egy alapszintű munkaterületet nagyvállalati verzióra](how-to-manage-workspace.md#upgrade). 
