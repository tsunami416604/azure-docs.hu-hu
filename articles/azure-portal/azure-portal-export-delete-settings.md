---
title: Exportálja vagy törlése az Azure portál beállításai |} A Microsoft Docs
description: Ismerje meg, hogyan exportálhatja, vagy törölje a felhasználói beállításokat, a privát irányítópultok és az egyéni beállítások az Azure Portalon.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361951"
---
# <a name="export-or-delete-user-settings"></a>Felhasználói beállítások exportálása vagy törlése

Használhatja beállításainak és funkcióinak az Azure Portalon egyéni környezetet biztosít. Az egyéni beállításokkal kapcsolatos információkat az Azure-ban tárolt. Exportálja, vagy törölje a következő felhasználói adatokat:

* Személyes irányítópultok az Azure Portalon
* Felhasználói beállítások, például kedvenc előfizetéseket vagy címtárakat és utoljára bejelentkezett directory
* Témák és az egyéb egyéni portál beállításai

Célszerű exportálhatja, és tekintse át a beállításokat, mielőtt törölné ezeket. Az irányítópultok újraépítése, vagy egyéni beállítások megismétlése időigényes lehet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportálja, vagy törölje a portál beállításait

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A portál a fejlécben, válassza ki a **beállítások**.

    ![A beállítások fogaskerék bemutató képernyőkép](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Válassza ki **az összes beállítás exportálása** vagy **törli az összes beállítás és saját irányítópult**.

    ![Képernyőkép a portal exportálása és beállítások törlése](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      A következő táblázat ismerteti ezeket a műveleteket.

      | Műveletek | Leírás |
      | --- | --- |
      | **Az összes beállítás exportálása** | Létrehoz egy .JSON kiterjesztésű fájlt, amely tartalmazza a felhasználói beállítások, például a színtémát, a Kedvencek és a privát irányítópultok.|
      | **Az összes beállítás és saját irányítópult törlése** | Személyes irányítópultok és egyéb egyéni beállítások a portálon végrehajtott mutató összes hivatkozást törli. |

> [!NOTE]
> Felhasználói beállítások dinamikus jellegét és az adatsérülés kockázatának miatt beállításokat nem lehet importálni a a .JSON kiterjesztésű fájlt.
>
>


## <a name="next-steps"></a>További lépések

* [Azure-irányítópultok létrehozása és megosztása](azure-portal-dashboard-share-access.md)
* [Hozzáadása, eltávolítása és rendezése a Kedvencek](azure-portal-add-remove-sort-favorites.md)
