---
title: 'Oktatóanyag: az Azure szinapszis Cognitive Services előfeltételei'
description: Oktatóanyag a Cognitive Services Azure Szinapszisban való használatához szükséges előfeltételek konfigurálásához
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: eef65db05ab94b5b8de5ff82c2c51dba0730f170
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222173"
---
# <a name="tutorial-pre-requisites-for-using-cognitive-services-in-azure-synapse"></a>Oktatóanyag: előfeltételek a Cognitive Services Azure Szinapszisban való használatához

Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az előfeltételeket az Azure szinapszis Cognitive Services biztonságos kihasználásához.

Ez az oktatóanyag az alábbiakkal foglalkozik:
> [!div class="checklist"]
> - Hozzon létre egy Cognitive Services erőforrást. Például Text Analytics vagy anomália detektor.
> - Tárolja a hitelesítési kulcsot, hogy az erőforrások Cognitive Services az Azure Key vaultban, és konfigurálja az Azure szinapszis munkaterülethez való hozzáférést.
> - Hozzon létre Azure Key Vault társított szolgáltatást az Azure szinapszis Analytics-munkaterületen.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services erőforrás létrehozása

Az [Azure Cognitive Services](../../cognitive-services/index.yml) számos különböző típusú szolgáltatást tartalmaz. Az alábbiakban néhány példát láthat a szinapszis-oktatóanyagokra.

### <a name="create-an-anomaly-detector-resource"></a>Anomália-detektor erőforrásának létrehozása
[Anomália-detektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) létrehozása Azure Portalban.

![Anomália-detektor létrehozása](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

### <a name="create-a-text-analytics-resource"></a>Text Analytics erőforrás létrehozása
Hozzon létre egy [text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) erőforrást a Azure Portalban.

![Szöveges elemzés létrehozása](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

## <a name="create-key-vault-and-configure-secrets-and-access"></a>Key Vault létrehozása és a titkok és hozzáférés konfigurálása

1. Hozzon létre egy [Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault) a Azure Portalban.
2. Lépjen a **Key Vault-> hozzáférési házirendek** elemre, és adja meg az [Azure SZINAPSZIS-munkaterület MSI](../security/synapse-workspace-managed-identity.md) -engedélyeit a titkos kódok olvasásához Azure Key Vault.

>Győződjön meg arról, hogy a házirend módosításai mentve lettek. Ez a lépés könnyen kihagyható.

![Hozzáférési szabályzat hozzáadása](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Nyissa meg a kognitív szolgáltatás erőforrását, például a **anomália-> kulcsokat és a végpontot**, másolja a két kulcs egyikét a vágólapra.

4. Új titok létrehozásához nyissa meg a **Key Vault-> titkot** . Adja meg a titok nevét, majd illessze be az előző lépésben szereplő kulcsot az "érték" mezőbe. Végül kattintson a **Create** (Létrehozás) gombra.

![Titkos kód létrehozása](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

> Ügyeljen rá, hogy jegyezze fel vagy jegyezze fel ezt a titkos nevet! Később akkor fogja használni, amikor az Azure szinapszis studióból csatlakozik Cognitive Serviceshoz.

## <a name="create-azure-keyvault-linked-service-in-azure-synapse"></a>Azure Key Vault-beli társított szolgáltatás létrehozása az Azure Szinapszisban

1. Nyissa meg a munkaterületet az Azure szinapszis Studióban. Navigáljon a **> társított szolgáltatások** elemre. Hozzon létre AB "Azure Key Vault" társított szolgáltatást, amely az imént létrehozott Key Vault mutat. Ezt követően ellenőrizze a kapcsolatokat úgy, hogy a "kapcsolatok tesztelése" gombra kattint, és ellenőrzi, hogy zöld színű-e. Ha valami rendben működik, kattintson a "létrehozás" gombra, majd kattintson az összes közzététele gombra a módosítás mentéséhez.
![Társított szolgáltatások](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Most már készen áll arra, hogy folytassa az Azure Cognitive Services-élmény az Azure szinapszis Studióban való használatának egyik oktatóanyagával.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: érzelmek elemzése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: anomáliák észlelése az Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis DEDIKÁLT SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md).
- [Az Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)