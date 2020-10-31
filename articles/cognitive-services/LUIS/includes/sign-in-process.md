---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128171"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis portálra](https://www.luis.ai), válassza ki az országot vagy régiót, és fogadja el a használati feltételeket. Ha ehelyett a **saját alkalmazások** jelennek meg, a Luis-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre. Két lehetőség regisztrálása:

    * Azure-erőforrás használata (ajánlott) – lehetővé teszi a LUIS-fiók összekapcsolását egy új vagy meglévő Azure authoring-erőforrással. Ez egyenértékű a már áttelepített regisztrációval. Később nem kell átlépnie az [áttelepítési folyamaton](../luis-migration-authoring.md#what-is-migration) .

    * Próbaverziós kulcs használata. Ez lehetővé teszi, hogy bejelentkezzen a LUIS-ba olyan próbaverziós erőforrással, amelyet nem kell beállítania. Ha ezt a lehetőséget választja, a [fiók áttelepítésére](../luis-migration-authoring.md#migration-steps) és az alkalmazások egy szerzői erőforráshoz való csatolására lesz szükség.

1. A megjelenő **authoring (szerzői műveletek** ) ablakban keresse meg az Azure-előfizetését és a Luis authoring Resource elemet. Ha nem rendelkezik erőforrással, létrehozhat egy újat.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Válasszon egy Language Understanding authoring Resource típust.":::
    
    Új authoring-erőforrás létrehozásakor adja meg a következő információkat:
    * **Bérlő neve** – az a bérlő, amelyhez az Azure-előfizetés társítva van.
    * **Azure-előfizetés neve** – az erőforrásért fizetendő előfizetés.
    * **Azure-erőforráscsoport neve** – válasszon ki vagy hozzon létre egy egyéni erőforráscsoport-nevet. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
    * **Azure-Erőforrás neve** – az Ön által választott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.


