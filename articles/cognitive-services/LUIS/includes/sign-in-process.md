---
title: fájlbefoglalás
description: fájlbefoglalás
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541431"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), válassza ki az országot vagy régiót, és fogadja el a használati feltételeket. Ha ehelyett a **saját alkalmazások** jelennek meg, a Luis-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre. A támogatott régiók esetében keresse fel [a létrehozási és közzétételi régiókat és a társított kulcsokat](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

1. Válassza az **Azure-erőforrás létrehozása** lehetőséget, majd válassza az **authoring-erőforrás létrehozása lehetőséget az alkalmazások migrálása érdekében.**

    ![Language Understanding szerzői erőforrás típusának kiválasztása](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Adja meg az erőforrás részleteit.

    ![A képernyőképen az új erőforrás létrehozása a szerzői Műveletek ablaktáblán látható.](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új authoring-erőforrás létrehozásakor**adja meg a következő információkat:

    * **Erőforrás neve** – a kiválasztott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van.
    * **Előfizetés neve** – az erőforráshoz számlázandó előfizetés.
    * **Erőforráscsoport** – a kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
    * **Hely** – a hely kiválasztása az **erőforráscsoport** kiválasztása alapján történik.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.

1. Megjelenik a létrehozandó erőforrás összegzése. Kattintson a **Tovább** gombra.

    ![A képernyőképen a kezdőlapon az Azure-fiókra mutató hivatkozást tartalmazó oldal látható.](../media/sign-in/sign-in-confirm-key-selection.png)

1. Erősítse meg a **Folytatás**lehetőséget.

    ![A képernyőképen az Azure-fiókkal való összekapcsolást követően megjelenik a Kezdőlap.](../media/sign-in/sign-in-confirm-continue.png)
