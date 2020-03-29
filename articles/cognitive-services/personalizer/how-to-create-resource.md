---
title: Személyre szabó-erőforrás létrehozása
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran van újrabetanítva, és mennyi adatot tárolnak.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336057"
---
# <a name="create-a-personalizer-resource"></a>Személyre szabó-erőforrás létrehozása

A Personalizer erőforrás ugyanaz, mint a Personalizer tanulási hurok. Minden egyes tulajdonostartományhoz vagy tartalomterülethez egyetlen erőforrás vagy tanulási hurok jön létre. Ne használjon több tartalmi területet ugyanabban a ciklusban, mert ez megzavarja a tanulási hurkot, és rossz előrejelzéseket biztosít.

Ha azt szeretné, hogy a Personalizer egy weboldal több tartalomterületéhez válassza ki a legjobb tartalmat, mindegyikhez használjon más tanulási hurkot.


## <a name="create-a-resource-in-the-azure-portal"></a>Erőforrás létrehozása az Azure Portalon

Hozzon létre egy Personalizer erőforrást minden visszajelzési hurokhoz.

1. Jelentkezzen be az [Azure portálra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző hivatkozás a Personalizer szolgáltatás **Létrehozás** lapjára mutat.
1. Adja meg a szolgáltatás nevét, válasszon ki egy előfizetést, a helyet, a tarifacsomagot és az erőforráscsoportot.

    > [!div class="mx-imgBorder"]
    > ![Az Azure Portal használatával személyre szabászati erőforrást, más néven tanulási hurkot hozhat létre.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Az erőforrás létrehozásához válassza a **Létrehozás** lehetőséget.

1. Az erőforrás üzembe helyezése után válassza az Ugrás az **erőforráshoz** gombot a Personalizer erőforrás hoz.

1. Válassza ki az erőforrás **gyorskezdőlapját,** majd másolja a végpont és a kulcs értékeit. Az erőforrás-végpontra és a kulcsra is szüksége van a Rang és jutalom API-k használatához.

1. Válassza ki az új erőforrás **konfigurációja** lapját [a tanulási ciklus konfigurálásához.](how-to-settings.md)

## <a name="create-a-resource-with-the-azure-cli"></a>Erőforrás létrehozása az Azure CLI-vel

1. Jelentkezzen be az Azure CLI-be a következő paranccsal:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy erőforráscsoportot, egy logikai csoportosítást az összes Azure-erőforrás kezeléséhez, amelyet a Personalizer erőforrással kíván használni.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Hozzon létre egy új Personalizer erőforrást, _tanulási hurkot,_ a következő paranccsal egy meglévő erőforráscsoporthoz.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Ez egy JSON-objektumot ad vissza, amely tartalmazza az **erőforrás-végpontot.**

1. Az **erőforráskulcs**levételéhez használja az alábbi Azure CLI parancsot.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Az erőforrás-végpontra és a kulcsra is szüksége van a Rang és jutalom API-k használatához.

## <a name="next-steps"></a>További lépések

* [Konfigurálás](how-to-settings.md) Személyre szabó tanulási hurok