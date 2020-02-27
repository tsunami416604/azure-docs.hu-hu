---
title: Személyre szabott erőforrás létrehozása
description: A szolgáltatás konfigurációja magában foglalja, hogy a szolgáltatás hogyan kezeli a jutalmakat, milyen gyakran vizsgálja a szolgáltatás, milyen gyakran történik a modell újratanítása és mennyi az adattárolás.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624230"
---
# <a name="create-a-personalizer-resource"></a>Személyre szabott erőforrás létrehozása

A személyre szabott erőforrás ugyanaz, mint a személyre szabott tanulási hurok. Minden egyes tulajdonos tartományhoz vagy tartalmi területhez létrejön egy erőforrás vagy egy tanulási hurok. Ne használjon több tartalmi területet ugyanabban a hurokban, mert ez megzavarja a tanulási ciklust, és gyenge előrejelzéseket biztosít.

Ha azt szeretné, hogy a személyre szabott tartalom legyen kiválasztva egy weblap több tartalmi területéhez, használjon egy másik tanulási ciklust.


## <a name="create-a-resource-in-the-azure-portal"></a>Erőforrás létrehozása a Azure Portalban

Hozzon létre egy személyre szabott erőforrást minden visszajelzési hurokhoz.

1. Jelentkezzen be az [Azure portálra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Az előző hivatkozás a személyre szabási szolgáltatás **Létrehozás** lapjára lép.
1. Adja meg a szolgáltatás nevét, válassza ki az előfizetést, a helyet, az árképzési szintet és az erőforráscsoportot.

    > [!div class="mx-imgBorder"]
    > ![a Azure Portal használatával személyre szabott erőforrást hozhat létre, más néven tanulási ciklust.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Válassza a **Létrehozás** lehetőséget az erőforrás létrehozásához.

1. Miután telepítette az erőforrást, kattintson az **Ugrás erőforrásra** gombra a személyre szabott erőforráshoz való ugráshoz. Nyissa meg az új erőforrás **konfigurációs** lapját a [tanulási hurok konfigurálásához](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Erőforrás létrehozása az Azure CLI-vel

1. Jelentkezzen be az Azure CLI-be a következő paranccsal:

    ```bash
    az login
    ```

1. Hozzon létre egy erőforráscsoportot, amely a személyre szabott erőforrással használni kívánt összes Azure-erőforrás kezelésére szolgáló logikai csoport.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Hozzon létre egy új személyre szabott erőforrást, egy _tanulási ciklust_a következő paranccsal egy meglévő erőforráscsoporthoz.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Következő lépések

* [Konfigurálás](how-to-settings.md) Személyre szabott tanulási hurok