---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77372112"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS-portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [LUIS portálra (előzetes verzió),](https://preview.luis.ai)válassza ki az országot, és fogadja el a használati feltételeket. Ha helyette **a Saját alkalmazások** lehetőséget látja, a LUIS-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre.

1. Válassza **az Azure-erőforrás létrehozása** lehetőséget, majd válassza a Szerzői erőforrás létrehozása lehetőséget az alkalmazások **áttelepítéséhez.**

    ![A Nyelvi megértés szerzői erőforrástípusának kiválasztása](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Adja meg az erőforrás adatait.

    ![Szerzői erőforrás létrehozása](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új szerzői erőforrás létrehozásakor**adja meg a következő információkat:

    * **Erőforrás neve** – egyéni név, amelyet a szerzői és előrejelzési végpontlekérdezések URL-címének részeként használ.
    * **Bérlő** – a bérlő, amelyhez az Azure-előfizetés e társított.
    * **Előfizetés neve** – az erőforrásért kiszámlázott előfizetés.
    * **Erőforráscsoport** – egyéni erőforráscsoport által választott vagy létrehozott név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáférés hez és a kezeléshez.
    * **Hely** – a helyválasztás az **erőforráscsoport** kiválasztásán alapul.
    * **Tarifacsomag** – a tarifacsomag határozza meg a maximális tranzakciót másodpercenként és havonta.

1. Megjelenik a létrehozandó erőforrás összegzése. Válassza a **Tovább lehetőséget.**

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-key-selection.png)

1. A megerősítés tanéven válassza a **Folytatás**lehetőséget.

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-continue.png)