---
title: Az Azure App konfigurációs adatok importálása vagy exportálása |} A Microsoft Docs
description: Ismerje meg, hogyan importálhat vagy exportálhat adatokat, vagy az Azure-alkalmazások konfigurálása
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 377c5088d39821e87412c517540b3190b0a14a00
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393273"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása vagy exportálása

Alkalmazáskonfiguráció támogatja az Azure data importálhatja, és exportálási műveletet. Ezek a műveletek használható a konfigurációs adatok tömeges és az exchange-adatok az alkalmazás a konfigurációs adattároló és kód között projekt. Például beállíthatja egy alkalmazás konfigurációs adattároló tesztelési és a egy másikat az éles környezetben. Majd másolhatja az alkalmazásbeállítások között egy fájlra, hogy nem kell kétszer adja meg az adatokat.

Ez a cikk egy útmutatót biztosít az alkalmazás konfigurációs adatok importálása és exportálása.

## <a name="import-data"></a>Adatok importálása

Importálása biztosítható konfigurációs egy alkalmazás-konfigurációs adatokat tárolni egy meglévő forrásból, azt manuálisan megadása helyett. Az importálás funkció segítségével az alkalmazás a konfigurációs adattároló vagy összesített adatokat a különböző forrásokból származó adatokat áttelepíteni. Alkalmazás a konfiguráció támogatja a JSON, YAML vagy tulajdonságok fájlból.

Adatok importálása a használatával a [az Azure portal](https://portal.azure.com) vagy a [Azure CLI-vel](./scripts/cli-import.md). Az Azure Portalról kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás a konfigurációs adattároló, és válassza ki **Import/Export**.

2. Az a **importálás** lapon jelölje be **szolgáltatás forrás** > **konfigurációs fájl**.

3. Válassza ki **nyelv** > **Fájltípus**.

4. Válassza ki a **mappa** ikonra, és keresse meg az importálandó fájl.

    ![Fájl importálása](./media/import-file.png)

5. Válassza ki a **elválasztó**, és igény szerint adjon meg egy **előtag** importált kulcsnevek használandó.

6. Kiválaszthat egy **címke**.

7. Válassza ki **alkalmaz** az importálás befejezésére.

    ![Fájl importálása befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Exportálás ír egy másik célhelyre Alkalmazáskonfiguráció tárolt konfigurációs adatait. Exportálás funkció segítségével, például egy alkalmazás a konfigurációs adattároló egy fájlba, az üzembe helyezés során az alkalmazás kódjában beágyazott adatok mentéséhez.

Adatok exportálása akár a [az Azure portal](https://portal.azure.com) vagy a [Azure CLI-vel](./scripts/cli-export.md). Az Azure Portalról kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás a konfigurációs adattároló, és válassza ki **Import/Export**.

2. Az a **exportálása** lapon jelölje be **szolgáltatási cél** > **konfigurációs fájl**.

3. Igény szerint adjon meg egy **előtag** , és válassza ki a **címke** és a egy-időponthoz kulcs exportálható.

4. Válassza ki a **Fájltípus** > **elválasztó**.

5. Válassza ki **alkalmaz** az Exportálás befejeződik.

    ![Befejezett fájl exportálása](./media/export-file-complete.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
