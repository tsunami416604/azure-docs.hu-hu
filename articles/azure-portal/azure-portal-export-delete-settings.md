---
title: Azure Portal Beállítások exportálása vagy törlése | Microsoft Docs
description: Megtudhatja, hogyan exportálhatja vagy törölheti a felhasználói beállításokat, a saját irányítópultokat és az egyéni beállításokat a Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: f033af37985077f4d8df9d541b55764df0c75eda
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640176"
---
# <a name="export-or-delete-user-settings"></a>Felhasználói beállítások exportálása vagy törlése

Egyéni felhasználói élmény létrehozásához használhatja a Azure Portal beállításait és funkcióit. Az egyéni beállításokkal kapcsolatos információkat az Azure tárolja. A következő felhasználói adatértékeket exportálhatja vagy törölheti:

* Privát irányítópultok a Azure Portal
* Felhasználói beállítások, például kedvenc előfizetések vagy címtárak, valamint az utolsó bejelentkezett címtár
* Témák és egyéb egyéni portál beállítások

A törlés előtt érdemes exportálni és áttekinteni a beállításait. Az irányítópultok újraépítése vagy az egyéni beállítások elvégzése időigényes lehet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>A portál beállításainak exportálása vagy törlése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A portál fejlécében válassza a **Beállítások**lehetőséget.

    ![A portál beállítási fogaskerékét bemutató képernyőkép](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. Válassza **az összes beállítás exportálása** vagy **az összes beállítás és privát irányítópultok törlése**lehetőséget.

    ![A portál exportálási és törlési beállításait bemutató képernyőkép](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      A következő táblázat ismerteti ezeket a műveleteket.

      | Műveletek | Leírás |
      | --- | --- |
      | **Az összes beállítás exportálása** | Létrehoz egy. JSON fájlt, amely tartalmazza a felhasználói beállításokat, például a színes témát, a kedvenceket és a privát irányítópultokat.|
      | **Az összes beállítás és a privát irányítópultok törlése** | Törli a privát irányítópultokra és a portálon végzett egyéb egyéni beállításokra mutató hivatkozásokat. |

> [!NOTE]
> A felhasználói beállítások dinamikus jellege és az adatsérülés kockázata miatt nem importálhatja a beállításokat a. JSON fájlból.
>
>


## <a name="next-steps"></a>Következő lépések

* [Azure-irányítópultok létrehozása és megosztása](azure-portal-dashboard-share-access.md)
* [Kedvencek hozzáadása, eltávolítása és rendezése](azure-portal-add-remove-sort-favorites.md)
