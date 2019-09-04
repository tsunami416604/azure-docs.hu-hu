---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 09/02/2018
ms.author: diberry
ms.openlocfilehash: 00eb6f40e77cbdb64249600e35ca8531f1a744a8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258137"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), válassza ki az országát, és fogadja el a használati feltételeket.
1. Válassza az **Azure-erőforrás létrehozása** lehetőséget, majd válassza az **authoring-erőforrás létrehozása lehetőséget az alkalmazások migrálása érdekében.**

    ![Language Understanding szerzői erőforrás típusának kiválasztása](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Adja meg az erőforrás részleteit.

    ![Szerzői erőforrás létrehozása](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új authoring-erőforrás létrehozásakor**adja meg a következő információkat: 

    * **Erőforrás neve** – a kiválasztott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van. 
    * **Előfizetés neve** – az erőforráshoz számlázandó előfizetés.
    * **Erőforráscsoport** – a kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez. 
    * **Hely** – a hely kiválasztása az **erőforráscsoport** kiválasztása alapján történik.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.

1. Megjelenik a létrehozandó erőforrás összegzése. Kattintson a **Tovább** gombra.

    ![Szerzői erőforrás létrehozása](./media/sign-in-confirm-key-selection.png)

1. A 3. lépés megerősítő. A **Folytatás**gombra kattintva erősítse meg a kijelölést. 

    ![Szerzői erőforrás létrehozása](./media/sign-in-confirm-continue.png)