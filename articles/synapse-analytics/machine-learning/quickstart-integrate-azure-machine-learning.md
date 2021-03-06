---
title: 'Rövid útmutató: Azure Machine Learning munkaterület összekapcsolása'
description: A szinapszis-munkaterület összekapcsolása egy Azure Machine Learning munkaterülettel
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: a5d84b936679cdd5ebd5bec4938d6db7fcb641e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455505"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása a Szinapszisban

Ebben a rövid útmutatóban egy Azure szinapszis Analytics-munkaterületet csatol egy Azure Machine Learning munkaterülethez. A munkaterületek összekapcsolása lehetővé teszi, hogy kihasználja Azure Machine Learning a szinapszis különböző tapasztalataiból.

Egy Azure Machine Learning munkaterületre való hivatkozás például lehetővé teszi a következő élményeket:

- A Azure Machine Learning-folyamatokat a szinapszis-folyamatok lépéseként futtathatja. További információ: [Azure Machine learning folyamatok végrehajtása](/azure/data-factory/transform-data-machine-learning-service).

- Az adatok a Azure Machine Learning modell beállításjegyzékből való kibővítésével és a modellnek a szinapszis SQL-készletekből való kiértékelésével gazdagíthatja adatait. További részletek: [oktatóanyag: gépi tanulási modell pontozási varázslója a SZINAPSZIS SQL-készletekhez](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
- A [szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.
- [Azure Machine learning-munkaterület](/azure/machine-learning/how-to-manage-workspace).
- A társított szolgáltatás létrehozásához használhatja az engedélyeket (vagy egy olyan személy kérelmét, aki rendelkezik engedélyekkel). Vegye figyelembe, hogy ezt a szolgáltatásnevet a Azure Machine Learning-munkaterület közreműködői szerepköréhez kell rendelni.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ez a lépés létrehoz egy új egyszerű szolgáltatásnevet. Ha egy meglévő szolgáltatásnevet szeretne használni, kihagyhatja ezt a lépést.
1. Nyissa meg az Azure Portalt. 

1. Nyissa meg **Azure Active Directory**  ->  **Alkalmazásregisztrációk**.

1. Kattintson az **Új regisztráció** elemre. Ezután kövesse a felhasználói felületen megjelenő utasításokat egy új alkalmazás regisztrálásához.

1. Az alkalmazás regisztrálása után. Titkos kód létrehozása az alkalmazáshoz. Nyissa **meg az alkalmazás**  ->  **tanúsítványát & titkos kulcsát**. A titkos kulcs létrehozásához kattintson az **ügyfél titkos kulcsának hozzáadása** elemre. Tartsa biztonságban a titkos kulcsot, és később lesz használatban.

   ![Titkos kód készítése](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Hozzon létre egy egyszerű szolgáltatásnevet az alkalmazáshoz. Nyissa **meg az alkalmazás**  ->  **áttekintését** , majd kattintson az **egyszerű szolgáltatás létrehozása** elemre. Bizonyos esetekben ez az egyszerű szolgáltatásnév automatikusan létrejön.

   ![Egyszerű szolgáltatás létrehozása](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Adja hozzá a szolgáltatásnevet a Azure Machine Learning munkaterület "közreműködője" néven. Vegye figyelembe, hogy ehhez a Azure Machine Learning munkaterület tulajdonosának kell lennie az erőforráscsoport számára.

   ![Közreműködő szerepkör kiosztása](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

1. A szinapszis munkaterületen, ahol létre szeretné hozni az új Azure Machine learning társított szolgáltatást, lépjen a **felügyelet**  ->  **társított szolgáltatás** elemre, és hozzon létre egy új társított szolgáltatást "Azure Machine learning" típussal.

   ![Társított szolgáltatás létrehozása](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Töltse ki az űrlapot:

   - Egyszerű szolgáltatásnév azonosítója: ez az alkalmazás **alkalmazás-(ügyfél-) azonosítója** .
  
     > [!NOTE]
     > Ez nem az alkalmazás neve. Ezt az azonosítót az alkalmazás Áttekintés oldalán találja. Ennek a "81707eac-ab38-406u-8f6c-10ce76a568d5" kifejezéshez hasonló hosszú karakterláncnak kell lennie.

   - Egyszerű szolgáltatásnév kulcsa: az előző szakaszban létrehozott titok.

3. Kattintson a **Kapcsolódás tesztelése** elemre annak ellenőrzéséhez, hogy a konfiguráció megfelelő-e. Ha a kapcsolatok tesztelése sikeres, kattintson a **Mentés** gombra.

   Ha a kapcsolatok tesztelése nem sikerült, győződjön meg arról, hogy az egyszerű szolgáltatásnév és a titkos kód helyes, és próbálkozzon újra.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: gépi tanulási modell pontozása varázsló – dedikált SQL-készlet](tutorial-sql-pool-model-scoring-wizard.md)
- [Az Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)
