---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5e2a74f69f575f4b9b332b98d7e19089418b29ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585215"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), válassza ki az országát, és fogadja el a használati feltételeket. Ha ehelyett a **saját alkalmazások** jelennek meg, a Luis-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre.

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

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-key-selection.png)

1. Erősítse meg a **Folytatás**lehetőséget.

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-continue.png)