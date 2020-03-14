---
title: A beállítások kezelése -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Beállítások kezelése, munkaterület létrehozása, munkaterület megosztása és előfizetési kulcs kezelése egyéni fordítóban.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219510"
---
# <a name="how-to-manage-settings"></a>Beállítások kezelése

Az egyéni fordítói beállítások lapon létrehozhat egy új munkaterületet, megoszthatja a munkaterületet, és hozzáadhatja vagy módosíthatja a Microsoft fordítási előfizetési kulcsát.

A beállítások lap elérése:

1. Jelentkezzen be az [Egyéni Translator](https://portal.customtranslator.azure.ai/) portálra.
2. Az egyéni Translator Portalon kattintson a fogaskerék ikonra az oldalsávon.

    ![Hivatkozás beállítása](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Microsoft Translator-előfizetés társítása

A munkaterülethez társított Microsoft Translator Text API előfizetési kulccsal kell rendelkeznie a modellek betanításához vagy üzembe helyezéséhez.

Ha nem rendelkezik előfizetéssel, kövesse az alábbi lépéseket:

1. Fizessen elő a Microsoft Translator Text APIra. Ez a cikk bemutatja, hogyan fizethet elő a Microsoft Translator Text APIra.
2. Jegyezze fel a fordítói előfizetés kulcsát. A Key1 vagy a Key2 egyike elfogadható.
3. Váltson vissza az egyéni Translator portálra.

### <a name="add-existing-key"></a>Meglévő kulcs hozzáadása

1.  Navigáljon a munkaterület "beállítások" lapjára.
2.  Kattintson a kulcs hozzáadása lehetőségre.

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key.png)

3. A párbeszédpanelen adja meg a fordító-előfizetés kulcsát, majd kattintson a Hozzáadás gombra.

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key-dialog.png)
4.  A kulcs hozzáadása után bármikor módosíthatja vagy törölheti a kulcsot.

    ![Előfizetés kulcsa a Hozzáadás után](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Munkaterület kezelése

A munkaterület az egyéni fordítási rendszer összeállításához és létrehozásához használható munkaterület. A munkaterületek több projektet, modellt és dokumentumot is tartalmazhatnak.

Ha a munka különböző részeit különböző személyekkel kell megosztani, akkor lehet, hogy több munkaterület létrehozása is hasznos lehet.

## <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

1.  Navigáljon a munkaterület "beállítások" lapjára.
2.  Az "új munkaterület létrehozása" szakaszban kattintson az "új munkaterület" gombra.

    ![Új munkaterület létrehozása](media/how-to/create-new-workspace.png)

4.  A párbeszédpanelen adja meg az új munkaterület nevét.
5.  Kattintson a Létrehozás gombra.

    ![Új munkaterület létrehozása párbeszédpanel](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Munkaterület megosztása

Az egyéni fordítóban megoszthatja a munkaterületet másokkal, ha a munka különböző részeit különböző személyekkel kell megosztani.

1.  Navigáljon a munkaterület "beállítások" lapjára.
2.  Kattintson a megosztás gombra a "megosztási beállítások" szakaszban.

    ![Munkaterület megosztása](media/how-to/share-workspace.png)

3.  A párbeszédpanelen adja meg a munkaterület által megosztott e-mail-címek vesszővel tagolt listáját. Győződjön meg arról, hogy a személy által az egyéni Fordítóba való bejelentkezéshez használt e-mail-címet használja. Ezután válassza ki a megfelelő szintű megosztási engedélyt.

4.  Ha a munkaterület még a "saját munkaterület" nevű alapértelmezett névvel rendelkezik, a munkaterület megosztása előtt módosítania kell azt.
5.  Kattintson a Save (Mentés) gombra.

## <a name="sharing-permissions"></a>Megosztási engedélyek

1.  **Olvasó:** A munkaterület olvasói megtekinthetik a munkaterület összes adatát.

2.  **Szerkesztő:** A munkaterületen lévő szerkesztők hozzáadhatnak dokumentumokat, betanítási modelleket és törölhetnek dokumentumokat és projekteket. Hozzáadhatnak egy előfizetési kulcsot, de nem módosíthatják, hogy a munkaterület hogyan legyen megosztva, törölje a munkaterületet, vagy módosítsa a munkaterület nevét.

3.  **Tulajdonos:** A tulajdonos teljes hozzáféréssel rendelkezik a munkaterülethez.

## <a name="change-sharing-permission"></a>Megosztási engedély módosítása

Ha egy munkaterület meg van osztva, a "megosztási beállítások" szakasz megjeleníti az összes e-mail-címet, amelyet a munkaterület megosztva használ. Ha tulajdonosi hozzáféréssel rendelkezik a munkaterülethez, módosíthatja az egyes e-mail-címek meglévő megosztási engedélyeit.

1.  Az egyes e-mailek "megosztási beállítások" szakaszában a legördülő menü megjeleníti az aktuális jogosultsági szintet.

2.  Kattintson a legördülő menüre, és válassza ki az új jogosultsági szintet, amelyet hozzá szeretne rendelni ehhez az e-mail-címéhez.

    ![Megosztási engedély beállításai](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan telepítheti át munkaterületét és projektjét](how-to-migrate.md) a [Microsoft Translator hub](https://hub.microsofttranslator.com) használatával
