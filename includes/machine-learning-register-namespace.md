---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445209"
---
* Új munkaterület létrehozásakor automatikusan létrehozhatók a munkaterület által igényelt szolgáltatások, vagy meglévő szolgáltatások is használhatók. Ha a __meglévő szolgáltatásokat más Azure-előfizetésből__ kívánja használni, mint a munkaterületet, regisztrálnia kell a Azure Machine learning névteret a szolgáltatásokat tartalmazó előfizetésben. Ha például létrehoz egy olyan munkaterületet az előfizetésben, amely a B előfizetéshez tartozó Storage-fiókot használ, akkor a Azure Machine Learning névteret regisztrálni kell a B előfizetésben, mielőtt a Storage-fiókot a munkaterülethez használhassa.

    A Azure Machine Learning erőforrás-szolgáltatója a __Microsoft. MachineLearningService__. Az [Azure Resource Providers és types](../articles/azure-resource-manager/management/resource-providers-and-types.md)  című cikkből megtudhatja, hogyan tekintheti meg, hogy regisztrálva van-e, és hogyan regisztrálhat.

    > [!IMPORTANT]
    > Ez csak a munkaterület létrehozása során biztosított erőforrásokra vonatkozik; Azure Storage-fiókok, Azure Container register, Azure Key Vault és Application Insights.