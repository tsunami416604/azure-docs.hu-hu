---
title: Adatimportálás vagy-Exportálás az Azure app Configuration szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan importálhatja vagy exportálhatja az Azure-alkalmazások konfigurációjának adatait
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 64fcc8396fc1b771d0095ee595fd177d7fe99b58
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899517"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása vagy exportálása

Az Azure-alkalmazás konfigurációja támogatja az adatok importálási és exportálási műveleteit. Ezekkel a műveletekkel tömegesen dolgozhat a konfigurációs adatokkal, és az alkalmazások konfigurációs tárolója és a kód projektje között cserélheti az adatait. Beállíthat például egy alkalmazás-konfigurációs tárolót tesztelésre, egy másikat pedig éles környezetben. Ezután átmásolhatja az alkalmazás beállításait egy fájl segítségével, így nem kell kétszer megadnia az adatbevitelt.

Ez a cikk útmutatást nyújt az alkalmazások alkalmazás-konfigurációval történő importálásához és exportálásához.

## <a name="import-data"></a>Adatok importálása

Az Importálás a konfigurációs adatokat egy meglévő forrásból egy alkalmazás konfigurációs tárolójába helyezi át, a manuális bevitel helyett. Az importálás függvény használatával áttelepítheti az adatokat egy alkalmazás-konfigurációs tárolóba, vagy összesítheti a különböző forrásokból származó adatokat. Az alkalmazás konfigurációja támogatja az importálást JSON, YAML vagy Properties fájlból.

Az adatimportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-import.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás**lehetőséget.

2. Az **Importálás** lapon válassza a **forrás szolgáltatás** > **konfigurációs fájl**elemet.

3. Válasszon **nyelvi** > **fájltípust**.

4. Válassza ki a **mappa** ikont, és tallózással keresse meg az importálni kívánt fájlt.

    ![Fájl importálása](./media/import-file.png)

5. Válasszon ki egy **elválasztó karaktert**, és opcionálisan adja meg az importált kulcsok nevének **előtagjaként használni kívánt előtagot** .

6. Opcionálisan kijelölhet egy **címkét**.

7. Az importálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![Az importálandó fájl befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Az Exportálás az alkalmazás konfigurációjában tárolt konfigurációs adatot egy másik célhelyre írja. Az Exportálás függvénnyel például egy alkalmazás konfigurációs tárolójában lévő adatmentést egy olyan fájlba, amely az alkalmazás kódjába van beágyazva az üzembe helyezés során.

Az adatexportálás az [Azure Portal](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-export.md)használatával történhet. A Azure Portal hajtsa végre az alábbi lépéseket:

1. Keresse meg az alkalmazás konfigurációs tárolóját, és válassza az **Importálás/exportálás**lehetőséget.

2. Az **Exportálás** lapon válassza a **cél szolgáltatás** > **konfigurációs fájl**elemet.

3. Opcionálisan megadhat egy **előtagot** , és kiválaszthat egy **címkét** és egy időpontot az exportálandó kulcsok számára.

4. Válassza ki a **fájltípus** > **elválasztót**.

5. Az Exportálás befejezéséhez kattintson az **alkalmaz** gombra.

    ![A fájl exportálása befejeződött](./media/export-file-complete.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [ASP.NET Core Webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
