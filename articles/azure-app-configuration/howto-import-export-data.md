---
title: Az Azure-alkalmazások konfigurációjának importálása vagy exportálása
description: Ismerje meg, hogyan importálhatja vagy exportálhatja az Azure-alkalmazások konfigurációjának adatait
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278271"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása vagy exportálása

Az Azure-alkalmazás konfigurációja támogatja az adatok importálási és exportálási műveleteit. Ezekkel a műveletekkel tömegesen dolgozhat a konfigurációs adatokkal, és az alkalmazások konfigurációs tárolója és a kód projektje között cserélheti az adatait. Beállíthat például egy alkalmazás-konfigurációs tárolót tesztelésre, egy másikat pedig éles környezetben. Átmásolhatja az alkalmazások beállításait, így nem kell kétszer megadnia az adatbevitelt.

Ez a cikk útmutatást nyújt az alkalmazások alkalmazás-konfigurációval történő importálásához és exportálásához.

## <a name="import-data"></a>Adatok importálása

Az importálással konfigurációs adatok importálhatók egy meglévő forrásból származó alkalmazás-konfigurációs tárolóba. Az importálás függvény használatával áttelepítheti az adatokat egy alkalmazás-konfigurációs tárolóba, vagy összesítheti a különböző forrásokból származó adatokat. Az alkalmazás konfigurációja támogatja az importálást JSON, YAML vagy Properties fájlból.

Az adatimportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-import.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás** lehetőséget az **Operations (műveletek** ) menüből.

1. Az **Importálás** lapon válassza a **forrás szolgáltatás** > **konfigurációs fájl**elemet.

1. Válassza ki **a nyelvet** , és válassza ki a kívánt bemeneti típust.

1. Válassza ki a **mappa** ikont, és tallózással keresse meg az importálni kívánt fájlt.

    ![Fájl importálása](./media/import-file.png)

1. Válasszon ki egy **elválasztó karaktert**, és opcionálisan adja meg az importált kulcsok nevének **előtagjaként használni kívánt előtagot** .

1. Opcionálisan kijelölhet egy **címkét**.

1. Az importálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![Az importálandó fájl befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Az Exportálás az alkalmazás konfigurációjában tárolt konfigurációs adatot egy másik célhelyre írja. Az Exportálás függvénnyel például egy alkalmazás konfigurációs tárolójában lévő adatmentést egy olyan fájlba, amely az alkalmazás kódjába van beágyazva az üzembe helyezés során.

Az adatexportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-export.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás**lehetőséget.

1. Az **Exportálás** lapon válassza a **cél szolgáltatás** > **konfigurációs fájl**elemet.

1. Opcionálisan megadhat egy **előtagot** , és kiválaszthat egy **címkét** és egy időpontot az exportálandó kulcsok számára.

1. Válassza ki a **fájltípus** > **elválasztót**.

1. Az Exportálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![A fájl exportálása befejeződött](./media/export-file-complete.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core Webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
