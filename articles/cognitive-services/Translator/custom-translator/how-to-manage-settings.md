---
title: Hogyan kezelheti a beállításokat? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Hogyan beállítások kezelése, hozzon létre munkaterületet, munkaterület megosztása és egyéni a fordítót az előfizetési kulcs kezelése.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: bbec2205fc6b29476cf531c071b5c2ac4c014bcb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732518"
---
# <a name="how-to-manage-settings"></a>Beállítások kezelése

A Translator egyéni beállítások lapon akkor is hozzon létre egy új munkaterületet, a munkaterületi megosztás és hozzáadása vagy módosítása a Microsoft Translation előfizetési kulcs.

A beállítások lapon elérése:

1. Jelentkezzen be a [egyéni a fordítót](https://portal.customtranslator.azure.ai/) portálon.
2. Egyéni a fordítót portál kattintson a fogaskerék ikonra az oldalsávon.

    ![Hivatkozás beállítása](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>A Microsoft Translator-előfizetés társítása

Szüksége lesz egy Microsoft Translator Text API-előfizetés használatával betanítani vagy modellek üzembe helyezése a munkaterülethez társított kulcs.

Ha nem rendelkezik előfizetéssel, kövesse az alábbi lépéseket:

1. Fizessen elő a Microsoft Translator Text API-t. Ez a cikk bemutatja, hogyan lehet előfizetni a Microsoft Translator Text API számára.
2. Vegye figyelembe a translator kulcsában. A Key1 és Key2 bármelyike elfogadható.
3. Lépjen vissza az egyéni a fordítót portálon.

### <a name="add-existing-key"></a>Meglévő kulcs hozzáadása

1.  Keresse meg a munkaterület a "Beállítások" lap.
2.  Kattintson a kulcs hozzáadása

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key.png)

3. A párbeszédpanelen adja meg a translator-előfizetéséhez tartozó kulcsot, majd kattintson a "Hozzáadás" gombra.

    ![Előfizetési kulcs hozzáadása](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Miután hozzáadott egy kulcsot, módosíthatja vagy törölheti a kulcsot a tetszőleges időpontban.

    ![Előfizetési kulcs hozzáadása](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>A munkaterület kezelése

A munkaterület egy munkaterület létrehozása és az egyéni fordítási rendszerek kiépítésének. Munkaterület több projektek, a modellek és a dokumentumok tartalmazhatnak.

Ha másik része a munkahelyi kell megosztani a különböző emberek, majd a több munkaterület létrehozása akkor lehet hasznos.

## <a name="create-a-new-workspace"></a>Új munkaterület létrehozása

1.  Keresse meg a munkaterület "Beállítások" lap.
2.  Kattintson a "új munkaterület" gombra az "Új munkaterület létrehozása" szakaszban.

    ![Új munkaterület létrehozása](media/how-to/create-new-workspace.png)

4.  A párbeszédpanelen adja meg az új munkaterület nevét.
5.  Kattintson a "Create".

    ![Új munkaterület-párbeszédpanel létrehozása](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>A munkaterület megosztása

Az egyéni a fordítót megoszthatja a munkaterület másokkal, ha másik része a munkahelyi oszthatók meg a különböző emberek igényeinek.

1.  Keresse meg a munkaterület "Beállítások" lap.
2.  A "Megosztás" gombra a "Megosztási beállítások" szakaszban.

    ![Munkaterület megosztása](media/how-to/share-workspace.png)

3.  A párbeszédpanelen adja meg azt szeretné, hogy ez a munkaterület megosztott e-mail-címek vesszővel elválasztott listáját. Ellenőrizze, hogy a megosztott e-mail-címmel rendelkező személy jelentkezzen be az egyéni Translator használatával. Ezután válassza ki a megfelelő szintű megosztási engedéllyel.

4.  A munkaterület még az alapértelmezett neve "Saját munkaterület", ha szükséges, módosítsa a munkaterület megosztása előtt.
5.  Kattintson a "Mentés" gombra.

## <a name="sharing-permissions"></a>Megosztási engedélyek

1.  **Olvasó:** A munkaterület egy olvasó lesz a munkaterület összes adatot megtekintheti.

2.  **Editor:** A munkaterület-szerkesztő tudják dokumentumok hozzáadása, modelleket taníthat be és törölje a dokumentumok és projektek. Adjon hozzá egy előfizetési kulcsot, de nem módosíthatja a munkaterület megosztó, a munkaterület törlése, vagy módosítsa a munkaterület nevét.

3.  **Tulajdonos:** Egy olyan tulajdonost teljes körű engedélyekkel rendelkezik a munkaterületre.

## <a name="change-sharing-permission"></a>Megosztási engedélyek módosítása

Munkaterület meg van osztva, a "Megosztási beállítások" című jelenít meg a munkaterület meg van osztva az összes e-mail-címek. Módosíthatja a meglévő megosztási engedélyt az összes e-mail-cím, ha tulajdonosi hozzáféréssel rendelkezik a munkaterületre.

1.  Mindegyik e-mail "Megosztási beállítások" szakaszban egy legördülő menü mutatja az aktuális jogosultsági szint.

2.  Kattintson a legördülő menüből, és válassza ki az e-mail-címmel a hozzárendelni kívánt új jogosultsági szint.

    ![Megosztási engedélyek beállításait](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, [migrálása a munkaterület és a projekt](how-to-migrate.md) a [Microsoft Translator Hub](https://hub.microsofttranslator.com)
