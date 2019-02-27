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
ms.openlocfilehash: 33ba01440f599e2f019db46c304b0658632d9342
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885283"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása és exportálása

Alkalmazáskonfiguráció támogatja az Azure data importálhatja, és exportálási műveletet. Ezek lehetővé teszik, hogy a konfigurációs adatok tömeges, valamint az alkalmazáskonfiguráció közötti exchange-adatok tárolására, és a projekt code használata. Például beállíthatja egy alkalmazás konfigurációs adattároló teszteléshez és a egy másikat az éles környezetben, és másolja Alkalmazásbeállítások közöttük a beágyazandó fájlra, hogy nem kell kétszer megadnia az adatokat.

Ez a cikk egy útmutatót biztosít az alkalmazás konfigurációs adatok importálása és exportálása.

## <a name="import-data"></a>Adatok importálása

Importálási alkalmazás konfigurációs adatokat tárolhatja őket manuálisan megadása helyett egy meglévő forrásból konfigurációs elérhetővé teszi. Az importálás funkció segítségével az alkalmazás a konfigurációs adattároló vagy összesített adatokat a különböző forrásokból származó adatokat áttelepíteni. Alkalmazás konfigurációja támogatja a JSON, YAML vagy tulajdonságok fájlból.

Adatok használatával importálhatja a [az Azure portal](https://aka.ms/azconfig/portal) vagy [Azure CLI-vel](./scripts/cli-import.md). Az Azure Portalról kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás a konfigurációs adattároló, és kattintson a **Import/Export**.

2. Az a **importálás** lapra, majd **szolgáltatás forrás** és **konfigurációs fájl**.

3. Válasszon **nyelv** és **Fájltípus**.

4. Kattintson a **mappa** ikonra, és keresse meg az importálandó fájl.

    ![Fájl importálása](./media/import-file.png)

5. Válasszon egy **elválasztó** és igény szerint adjon meg egy **előtag** importált kulcsnevek használandó.

6. Opcionálisan válassza ki a **címke**.

7. Kattintson a **alkalmaz** az importálás befejeződik.

    ![Fájl importálása befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Exportálás ír egy másik célhelyre Alkalmazáskonfiguráció tárolt konfigurációs adatait. Az exportálási funkció használata, például egy alkalmazás a konfigurációs adattároló egy fájlba, a rendszer beágyazza az alkalmazáskódban az üzembe helyezés során az adatok mentéséhez.

Adatok segítségével exportálhatja a [az Azure portal](https://aka.ms/azconfig/portal) vagy [Azure CLI-vel](./scripts/cli-export.md). Az Azure Portalról kövesse az alábbi lépéseket:

1. Keresse meg az alkalmazás a konfigurációs adattároló, és kattintson a **Import/Export**.

2. Az a **exportálása** lapra, majd **szolgáltatási cél** és **konfigurációs fájl**.

3. Igény szerint adjon meg egy **előtag** , és válassza a **címke** és a egy-időponthoz kulcsok exportálható.

4. Válasszon egy **Fájltípus** és **elválasztó**.

5. Kattintson a **alkalmaz** az Exportálás befejeződik.

    ![Teljes fájl exportálása](./media/export-file-complete.png)

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: ASP.NET-webalkalmazás létrehozása](quickstart-aspnet-core-app.md)  
