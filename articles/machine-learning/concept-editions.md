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
ms.openlocfilehash: aa754868677802b7d0000045f22090fbca62d9b6
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927419"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>A Azure Machine Learning nagyvállalati és alapszintű kiadásai 

Azure Machine Learning két, a gépi tanulási igényeihez igazított kiadást kínál. Ezek a kiadások határozzák meg, hogy mely gépi tanulási eszközök érhetők el a fejlesztők és az adatszakértők számára a munkaterületről.

## <a name="choose-an-edition"></a>Kiadás kiválasztása

A kiadást a munkaterület létrehozásakor rendeli hozzá. Az ügyfelek felelősek a számítási és egyéb Azure-erőforrásokért felmerülő költségekért. Útmutató a [Azure Machine learning költségeinek kezeléséhez](concept-plan-manage-cost.md).

Megtudhatja, hogyan [frissíthet egy alapszintű munkaterületet nagyvállalati verzióra](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Az egyes kiadások

### <a name="data-for-machine-learning-capabilities"></a>A Machine Learning képességeinek adatvédelme  

| Képességek                     | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Címkézés: [címkézési projektek létrehozása és kezelése](tutorial-labeling.md) a Studióban (web)                                                | Az összes                     |
| Címkézés: Labeler a Studióban (web)                                    | Az összes                     |
| Címkézés: saját munkaerő használata                               | Az összes                     |
| Címkézés: [ml támogatott képek besorolása és objektumok észlelése](how-to-label-images.md)                  | Csak Enterprise Edition |
| Adatkészletek és adattárolók: létrehozás és kezelés Pythonban                       | Az összes                     |
| Adatkészletek és adattárolók: létrehozás és kezelés a Studióban (web)                         | Az összes                     |
| Drift: adatkészlet-figyelők megtekintése és kezelése a Pythonban                           | Az összes                     |
| Drift: adatkészlet-figyelők megtekintése és kezelése a Studióban (web)                            | Csak Enterprise Edition |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Automatizált betanítási képességek (AutoML)

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [AutoML-kísérletek](how-to-configure-auto-train.md) létrehozása és futtatása jegyzetfüzetekben               | Az összes                     |
| [AutoML-kísérletek létrehozása és futtatása a Studióban (web)](how-to-use-automated-ml-for-ml-models.md)   | Csak Enterprise Edition |
| Piacvezető AutoML-előrejelzési képességek             | Csak Enterprise Edition |
| A Deep learning és az egyéb fejlett tanulók támogatása | Csak Enterprise Edition |
| Nagyméretű adatok támogatása besorolási és regressziós feladatok (legfeljebb 100 GB)                     | Csak Enterprise Edition |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Felelős Machine Learning

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Modell-magyarázat](how-to-machine-learning-interpretability-automl.md)                                              | Az összes                     |
| [Különbözeti adatvédelem](how-to-differential-privacy.md)                          | Az összes                     |
| Egyéni címkék az adatlapok megvalósításához    | Az összes                     |
| Méltányos AzureML-integráció                                      | Az összes                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Képességek kiépítése és betanítása

| Képességek    | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code-integráció                                                     | Az összes                     |
| Megerősítő tanulás                                                             | Az összes                     |
| Kísérletezés felhasználói felülete                                                                 | Az összes                     |
| Jupyter, JupyterLab-integráció                                                    | Az összes                     |
| Python SDK-támogatás                                                                 | Az összes                     |
| R SDK-támogatás                                                                      | Az összes                     |
| ML-folyamatok: létrehozás, Futtatás és közzététel Pythonban                           | Az összes                     |
| ML-folyamatok: folyamatok ütemezett futtatásának létrehozása, szerkesztése és törlése a Pythonban| Az összes                     |
| ML-folyamatok: folyamat-végpontok létrehozása a Python SDK-ban                                   | Az összes                     |
| ML-folyamatok: a Futtatás részleteinek megtekintése a Studióban (web)                                              | Az összes                     |
| ML-folyamatok: létrehozás, Futtatás, megjelenítés és közzététel a Designerben                  | Csak Enterprise Edition |
| ML-folyamatok: folyamat-végpontok létrehozása a Designerben | Csak Enterprise Edition |
| Felügyelt számítási példányok integrált jegyzetfüzetekhez                                 | Az összes                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Üzembe helyezési és modellezési képességek

| Képességek                            | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| Az Azure DevOps bővítmény a Machine Learning és a Azure ML CLI                 | Az összes                     |
| [Event Grid-integráció](how-to-use-event-grid.md)                                                             | Az összes                     |
| Azure Stream Analytics integrálása Azure Machine Learning                       | Az összes                     |
| ML-folyamatok létrehozása az SDK-ban                                                         | Az összes                     |
| A Batch-következtetések                                                                  | Az összes                     |
| FPGA-alapú Hardware Accelerated Models                                             | Az összes                     |
| Modell profilkészítése                                                                    | Az összes                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Biztonság, irányítás és vezérlési képességek

| Képességek     | Kiadás                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](how-to-assign-roles.md) támogatása                                           | Az összes                     |
| [Virtual Network (VNet)-](how-to-enable-virtual-network.md) támogatás a számítási feladatokhoz                                         | Az összes                     |
| Pontozási végpont hitelesítése                                                    | Az összes                     |
| [Munkaterület privát hivatkozása](how-to-configure-private-link.md)                                                            | Az összes                     |
| [Kvótakezelés](how-to-manage-quotas.md) a munkaterületek között                                                 | Csak Enterprise Edition |

## <a name="next-steps"></a>További lépések

További információ a Azure Machine Learning [Edition áttekintése és díjszabása lapon](https://azure.microsoft.com/pricing/details/machine-learning/)elérhető tudnivalókról. 

Megtudhatja, hogyan [frissíthet egy alapszintű munkaterületet nagyvállalati verzióra](how-to-manage-workspace.md#upgrade). 
