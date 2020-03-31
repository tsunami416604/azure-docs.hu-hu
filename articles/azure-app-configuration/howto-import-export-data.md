---
title: Adatok importálása vagy exportálása az Azure App konfigurációjával
description: Megtudhatja, hogy miként importálhat vagy exportálhat adatokat az Azure App Konfigurációja szolgáltatásba vagy onnan
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056867"
---
# <a name="import-or-export-configuration-data"></a>Konfigurációs adatok importálása vagy exportálása

Az Azure App Configuration támogatja az adatimportálási és -exportálási műveleteket. Ezekkel a műveletekkel tömegeskonfigurációs adatokkal dolgozhat, és adatokat cserélheti az Alkalmazáskonfigurációs tároló és a kódprojekt között. Beállíthatja például az egyik alkalmazáskonfigurációs tárolót tesztelésre, a másikat pedig éles környezetbe. Az alkalmazásbeállításokat átmásolhatja közöttük, így nem kell kétszer adatokat megadnia.

Ez a cikk az adatok alkalmazáskonfigurációval történő importálásához és exportálásához nyújt útmutatót. Ha folyamatos szinkronizálást szeretne beállítani a GitHub-tártárral, tekintse meg a [GitHub-műveletünket.](https://aka.ms/azconfig-gha1)

## <a name="import-data"></a>Adatok importálása

Az importálás egy meglévő forrásból hozza be a konfigurációs adatokat egy Alkalmazáskonfigurációs tárolóba. Az importálási funkcióval adatokat telepítheti át egy Alkalmazáskonfigurációs tárolóba, vagy összesítheti az adatokat több forrásból. Az Alkalmazáskonfiguráció támogatja a JSON-, YAML- vagy tulajdonságfájlból történő importálást.

Adatok importálása az [Azure Portalon](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-import.md)használatával. Az Azure Portalon kövesse az alábbi lépéseket:

1. Tallózással keresse meg az Alkalmazáskonfigurációs tárolót, és válassza az Műveletek menü **Importálás/exportálás** **parancsát.**

1. Az **Importálás** lapon válassza a **Forrásszolgáltatás** > **konfigurációs fájlja lehetőséget.**

1. Válassza **a Nyelvhez** lehetőséget, és válassza ki a kívánt beviteli típust.

1. Jelölje ki a **Mappa** ikont, és keresse meg az importálni kívánt fájlt.

    ![Fájl importálása](./media/import-file.png)

1. Válasszon egy **elválasztót**, és adja meg az importált kulcsnevekhez használandó **előtagot.**

1. Szükség esetén jelöljön ki egy **címkét**.

1. Az importálás befejezéséhez válassza az **Alkalmaz** lehetőséget.

    ![A fájl importálása befejeződött](./media/import-file-complete.png)

## <a name="export-data"></a>Adatok exportálása

Az alkalmazáskonfigurációban tárolt konfigurációs adatok exportálása egy másik célhelyre. Az exportálási funkció valamenyedjen például egy app konfigurációs tárolóban egy olyan fájlba, amely az alkalmazáskóddal van beágyazva a központi telepítés során.

Adatok exportálása az [Azure Portalon](https://portal.azure.com) vagy az [Azure CLI](./scripts/cli-export.md)használatával. Az Azure Portalon kövesse az alábbi lépéseket:

1. Tallózással keresse meg az Alkalmazáskonfigurációs áruházat, és válassza **az Importálás/exportálás**lehetőséget.

1. Az **Exportálás** lapon válassza a **Célszolgáltatás** > **konfigurációs fájlja**lehetőséget.

1. Tetszés szerint adjon meg egy **előtagot,** és jelöljön ki egy **címkét** és egy időpontot a kulcsok exportálásához.

1. Válasszon egy > **fájltípus-elválasztót**. **File type**

1. Az exportálás befejezéséhez válassza az **Alkalmaz** lehetőséget.

    ![A fájl exportálása befejeződött](./media/export-file-complete.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [ASP.NET Core-webalkalmazás létrehozása](./quickstart-aspnet-core-app.md)  
