---
title: Hogyan lehet kezelni a beállításokat? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A beállítások kezelése, munkaterület létrehozása, munkaterület megosztása és előfizetési kulcs kezelése az Egyéni fordítóban.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219510"
---
# <a name="how-to-manage-settings"></a>A beállítások kezelése

Az Egyéni fordító beállításai lapon létrehozhat egy új munkaterületet, megoszthatja a munkaterületet, és hozzáadhatja vagy módosíthatja a Microsoft Translation előfizetési kulcsot.

A beállítások lap elérése:

1. Jelentkezzen be az [egyéni fordítóportálra.](https://portal.customtranslator.azure.ai/)
2. Az Egyéni fordító portálon kattintson a fogaskerék ikonra az oldalsávon.

    ![Hivatkozás beállítása](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Microsoft Translator-előfizetés társítása

A modellek betanításához vagy üzembe helyezéséhez rendelkeznie kell egy Microsoft Translator Text API-előfizetési kulcsral a munkaterülethez társítva.

Ha nem rendelkezik előfizetéssel, kövesse az alábbi lépéseket:

1. Iratkozzon fel a Microsoft Translator Text API-ra. Ez a cikk bemutatja, hogyan lehet előfizetni a Microsoft Translator Text API-ra.
2. Jegyezze fel a fordítói előfizetés kulcsát. A Key1 vagy a Key2 bármelyike elfogadható.
3. Keresse vissza az egyéni fordító portált.

### <a name="add-existing-key"></a>Meglévő kulcs hozzáadása

1.  Nyissa meg a munkaterület "Beállítások" lapját.
2.  Kattintson a Kulcs hozzáadása gombra

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key.png)

3. A párbeszédpanelen adja meg a fordítói előfizetés kulcsát, majd kattintson a "Hozzáadás" gombra.

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key-dialog.png)
4.  A kulcs hozzáadása után bármikor módosíthatja vagy törölheti a kulcsot.

    ![Előfizetési kulcs a hozzáadás után](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Saját munkaterület kezelése

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához szükséges munkaterület. Egy munkaterület több projektet, modellt és dokumentumot is tartalmazhat.

Ha a munka különböző részét meg kell osztani a különböző személyekkel, akkor több munkaterület létrehozása hasznos lehet.

## <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

1.  Nyissa meg a munkaterület "Beállítások" lapját.
2.  Kattintson az "Új munkaterület létrehozása" szakasz "Új munkaterület létrehozása" gombjára.

    ![Új munkaterület létrehozása](media/how-to/create-new-workspace.png)

4.  A párbeszédpanelen adja meg az új munkaterület nevét.
5.  Kattintson a "Létrehozás" gombra.

    ![Új munkaterület létrehozása párbeszédpanel](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>A munkaterület megosztása

Az Egyéni fordítóban megoszthatja munkaterületét másokkal, ha a munka különböző részét meg kell osztania különböző emberekkel.

1.  Nyissa meg a munkaterület "Beállítások" lapját.
2.  Kattintson a "Megosztási beállítások" szakasz "Megosztás" gombjára.

    ![Munkaterület megosztása](media/how-to/share-workspace.png)

3.  A párbeszédpanelen adja meg azoknak az e-mail címeknek a vesszővel elválasztott listáját, amelyekkel meg szeretné osztani ezt a munkaterületet. Győződjön meg arról, hogy megosztja az e-mail címet, amellyel a személy bejelentkezik az egyéni fordítóba. Ezután válassza ki a megosztási engedély megfelelő szintjét.

4.  Ha a munkaterület továbbra is a "Saját munkaterület" alapértelmezett névvel rendelkezik, a munkaterület megosztása előtt módosítania kell azt.
5.  Kattintson a "Mentés" gombra.

## <a name="sharing-permissions"></a>Megosztási engedélyek

1.  **Olvasó:** A munkaterület olvasója megtekintheti a munkaterület összes információját.

2.  **Szerkesztő:** A munkaterület egyik szerkesztője dokumentumokat adhat hozzá, modelleket taníthat be, és törölhet dokumentumokat és projekteket. Hozzáadhatnak egy előfizetési kulcsot, de nem módosíthatják, hogy kivel van megosztva a munkaterület, törölhetik a munkaterületet, és nem módosíthatják a munkaterület nevét.

3.  **Tulajdonos:** A tulajdonos teljes engedélyekkel rendelkezik a munkaterülethez.

## <a name="change-sharing-permission"></a>Megosztási engedély módosítása

Ha egy munkaterület meg van osztva, a "Megosztási beállítások" szakasz ban látható az összes olyan e-mail cím, amelyen ez a munkaterület meg van osztva. Módosíthatja az egyes e-mail-címekhez meglévő megosztási engedélyeket, ha tulajdonosi hozzáféréssel rendelkezik a munkaterülethez.

1.  A "Megosztási beállítások" szakaszban minden e-mailben egy legördülő menümutatja az aktuális jogosultsági szintet.

2.  Kattintson a legördülő menüre, és válassza ki az adott e-mail címhez rendelni kívánt új jogosultsági szintet.

    ![Engedélybeállítások megosztása](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>További lépések

- [A munkaterület és a projekt áttelepítése a](how-to-migrate.md) Microsoft Translator [Hubról](https://hub.microsofttranslator.com)
