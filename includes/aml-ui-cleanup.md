---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 66f5c72fcabb62e21f0110cb981b7271244c0648
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799898"
---
>[!IMPORTANT]
>A létrehozott erőforrásokat használhatja más Azure Machine Learning oktatóanyagok és útmutatók előfeltételeiként.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott bármit használni, törölje a teljes erőforráscsoportot, így nem számítunk fel díjat:

1. A Azure Portal az ablak bal oldalán válassza az **erőforráscsoportok** lehetőséget.
 
   ![Erőforráscsoport törlése az Azure Portalon](./media/aml-ui-cleanup/delete-resources.png)

1. A listából válassza ki a létrehozott erőforráscsoportot.

1. Válassza az **Erőforráscsoport törlése** elemet.

Az erőforráscsoport törlése a tervezőben létrehozott összes erőforrást is törli.  

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A tervezőben, ahol létrehozta a kísérletet, törölje az egyes objektumokat úgy, hogy kiválasztja őket, majd a **Törlés** gombra kattint.

Az itt létrehozott számítási cél *automatikusan automatikus méretezést* végez a nulla csomópontokra, amikor az nincs használatban. Ez a költségek csökkentése. Ha törölni szeretné a számítási célt, hajtsa végre a következő lépéseket:

![Eszközök törlése](./media/aml-ui-cleanup/delete-asset.png)

Az adatkészletek törölhető a munkaterületről az egyes adatkészletek kiválasztásával, majd a **regisztráció megszüntetése**lehetőség kiválasztásával.

![Adatkészlet regisztrációjának törlése](./media/aml-ui-cleanup/unregister-dataset.png)

Adatkészlet törléséhez navigáljon a Storage-fiókhoz a Azure Portal vagy a Storage Explorer használatával, és manuálisan törölje ezeket az eszközöket.


