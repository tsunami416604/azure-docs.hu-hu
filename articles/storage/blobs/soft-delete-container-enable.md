---
title: Tárolók helyreállítható törlésének engedélyezése és kezelése (előzetes verzió)
titleSuffix: Azure Storage
description: A tárolók Soft Delete (előzetes verzió) funkciójának engedélyezésével könnyebben lehet helyreállítani az adatokat, ha azok hibásan lettek módosítva vagy törölve.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f79b198b512547b1d93fbbd458af8251b109e62f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066661"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Tárolók helyreállítható törlésének engedélyezése és kezelése (előzetes verzió)

A tároló-helyreállító törlés (előzetes verzió) védi az adatok véletlen vagy helytelen módosítását vagy törlését. Ha a tároló-helyreállító törlés engedélyezve van egy Storage-fiókhoz, akkor a tároló és annak tartalma a megadott megőrzési időn belül visszaállítható.

Ha egy alkalmazás vagy egy másik Storage-fiók felhasználója véletlenül nem módosíthatja vagy törölheti az adatait, a Microsoft javasolja a tárolók törlésének bekapcsolását. Ez a cikk bemutatja, hogyan engedélyezheti a tárolók Soft delete szolgáltatását. A tárolók helyreállítható törlésével kapcsolatos további részletekért, beleértve az előzetes verzió regisztrálásának módját, lásd: a [tárolók helyreállítható törlése (előzetes verzió)](soft-delete-container-overview.md).

A végpontok közötti adatvédelem érdekében a Microsoft azt javasolja, hogy a blobok és a Blobok verziószámozása (előzetes verzió) esetében is engedélyezze a Soft delete használatát. A Blobok helyreállítható törlésének engedélyezéséről a [Blobok Soft delete engedélyezése és kezelése](soft-delete-blob-enable.md)című témakörben olvashat. A blob verziószámozásának engedélyezésével kapcsolatos további információkért lásd: [blob verziószámozása (előzetes verzió)](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Tároló törlésének engedélyezése

A Storage-fiókhoz bármikor engedélyezheti vagy letilthatja a tárolók törlését a Azure Portal vagy egy Azure Resource Manager sablon használatával.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az alábbi lépéseket követve engedélyezheti a tárolók törlését a Storage-fiókhoz Azure Portal használatával:

1. Az [Azure Portalon](https://portal.azure.com/) lépjen a tárfiókra.
1. Keresse meg az **Adatvédelem** beállításait a **blob Service**alatt.
1. Állítsa be a **tároló Soft delete** tulajdonságát *engedélyezve*értékre.
1. Az **adatmegőrzési szabályzatok**területen határozza meg, hogy az Azure Storage mennyi ideig őrizze meg a Soft-Deleted tárolókat.
1. Mentse a módosításokat.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="A tárolók törlésének engedélyezését bemutató képernyőkép Azure Portal":::

# <a name="template"></a>[Sablon](#tab/template)

Ha egy Azure Resource Manager sablonnal szeretné engedélyezni a tárolók törlését, hozzon létre egy sablont, amely beállítja a **containerDeleteRetentionPolicy** tulajdonságot. A következő lépések azt ismertetik, hogyan lehet sablont létrehozni a Azure Portalban.

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.
1. A **Keresés a piactéren**mezőbe írja be a **sablon központi telepítése**kifejezést, majd nyomja le az **ENTER**billentyűt.
1. Válassza a **template Deployment**lehetőséget, válassza a **Létrehozás**lehetőséget, majd **a szerkesztőben válassza a saját sablon**létrehozása lehetőséget.
1. A sablon szerkesztőjében illessze be a következő JSON-t. Cserélje le a  `<account-name>`   helyőrzőt a Storage-fiók nevére.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. A megőrzési időtartam meghatározása. Az alapértelmezett érték 7.
1. Mentse a sablont.
1. Adja meg a fiókhoz tartozó erőforráscsoportot, majd válassza a **felülvizsgálat + létrehozás**   gombot a sablon telepítéséhez és a tároló törlésének engedélyezéséhez.

## <a name="view-soft-deleted-containers"></a>Lágyan törölt tárolók megtekintése

Ha a helyreállítható törlés engedélyezve van, megtekintheti a Azure Portalban található, lágyan törölt tárolókat. A Soft-Deleted tárolók a megadott megőrzési időtartam alatt láthatók. A megőrzési időszak lejárta után a rendszer véglegesen törli a helyreállított tárolót, és már nem látható.

A Azure Portalban található, lágyan törölt tárolók megtekintéséhez kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal Storage-fiókjához, és tekintse meg a tárolók listáját.
1. Állítsa be a törölt tárolók megjelenítése lehetőséget a listában szereplő törölt tárolók belefoglalásához.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Képernyőfelvétel: a Azure Portalban található, lágyan törölt tárolók megjelenítése":::

## <a name="restore-a-soft-deleted-container"></a>Törölt tároló visszaállítása

A megőrzési időtartamon belül visszaállíthatja a nem törölt tárolót és annak tartalmát. A Azure Portal helyreállítható tárolók visszaállításához kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal Storage-fiókjához, és tekintse meg a tárolók listáját.
1. Jelenítse meg a törölni kívánt tároló helyi menüjét, majd válassza a menü **Törlés törlése** parancsát.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Képernyőfelvétel: a Azure Portal helyreállítható tárolójának visszaállítása":::

## <a name="next-steps"></a>További lépések

- [Tárolók törlése (előzetes verzió)](soft-delete-container-overview.md)
- [A Blobok Soft delete](soft-delete-blob-overview.md)
- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)