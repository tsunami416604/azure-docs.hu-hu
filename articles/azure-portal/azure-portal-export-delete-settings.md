---
title: Azure Portal Beállítások exportálása vagy törlése
description: Megtudhatja, hogyan exportálhatja vagy törölheti a felhasználói beállításokat, a saját irányítópultokat és az egyéni beállításokat a Azure Portal.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76900753"
---
# <a name="export-or-delete-user-settings"></a>Felhasználói beállítások exportálása vagy törlése

Egyéni felhasználói élmény létrehozásához használhatja a Azure Portal beállításait és funkcióit. Az egyéni beállításokkal kapcsolatos információkat az Azure tárolja. A következő felhasználói adatértékeket exportálhatja vagy törölheti:

* Privát irányítópultok a Azure Portal
* Felhasználói beállítások, például kedvenc előfizetések vagy címtárak, valamint az utolsó bejelentkezett címtár
* Témák és egyéb egyéni portál beállítások

A törlés előtt érdemes exportálni és áttekinteni a beállításait. Az irányítópultok újraépítése vagy az egyéni beállítások elvégzése időigényes lehet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>A portál beállításainak exportálása vagy törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A portál fejlécében válassza a beállítások ikon ![](media/azure-portal-export-delete-settings/settings-icon.png) **beállításai**lehetőséget.

1. Válassza **az összes beállítás exportálása** vagy **az összes beállítás és privát irányítópultok törlése**lehetőséget.

    ![Azure Portal beállítások és beállítások beállításai](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      A következő táblázat ismerteti ezeket a műveleteket.

      | Műveletek | Leírás |
      | --- | --- |
      | **Az összes beállítás exportálása** | Létrehoz egy *. JSON* fájlt, amely tartalmazza a felhasználói beállításokat, például a színes témát, a kedvenceket és a privát irányítópultokat.|
      | **Az összes beállítás és a privát irányítópultok törlése** | Törli a privát irányítópultokra és a portálon végzett egyéb egyéni beállításokra mutató hivatkozásokat. |

> [!NOTE]
> A felhasználói beállítások dinamikus jellege és az adatsérülés kockázata miatt nem importálhatja a beállításokat a *. JSON* fájlból.
>
>

## <a name="next-steps"></a>További lépések

* [Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával](azure-portal-dashboard-share-access.md)
* [Kedvencek hozzáadása, eltávolítása és átrendezése](azure-portal-add-remove-sort-favorites.md)
