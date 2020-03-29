---
title: Az Azure Portal beállításainak exportálása vagy törlése
description: Megtudhatja, hogyan exportálhatja vagy törölheti a felhasználói beállításokat, a privát irányítópultokat és az egyéni beállításokat az Azure Portalon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900753"
---
# <a name="export-or-delete-user-settings"></a>Felhasználói beállítások exportálása vagy törlése

Az Azure Portal beállításaival és szolgáltatásaival egyéni élményt hozhat létre. Az egyéni beállításokkal kapcsolatos információkat az Azure tárolja. A következő felhasználói adatokat exportálhatja vagy törölheti:

* Privát irányítópultok az Azure Portalon
* Felhasználói beállítások, például kedvenc előfizetések vagy könyvtárak, valamint az utolsó bejelentkezett könyvtár
* Témák és egyéb egyéni portálbeállítások

A törlés előtt célszerű exportálni és áttekinteni a beállításokat. Az irányítópultok újraépítése vagy az egyéni beállítások újbóli létrehozása időigényes lehet.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>A portál beállításainak exportálása vagy törlése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A portál fejlécében válassza ![a](media/azure-portal-export-delete-settings/settings-icon.png) Beállítások ikon **Beállítások**lehetőséget.

1. Válassza **az Összes beállítás exportálása** vagy Az összes beállítás és privát irányítópult **törlése**lehetőséget.

    ![Az Azure Portal beállításai és beállításai](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Az alábbi táblázat ezeket a műveleteket ismerteti.

      | Műveletek | Leírás |
      | --- | --- |
      | **Az összes beállítás exportálása** | Létrehoz egy *.json* fájlt, amely tartalmazza a felhasználói beállításokat, például a színtémát, a kedvenceket és a privát irányítópultokat.|
      | **Az összes beállítás és privát irányítópult törlése** | Törli a privát irányítópultokra mutató összes hivatkozást és a portálra megadott egyéb egyéni beállításokat. |

> [!NOTE]
> A felhasználói beállítások dinamikus jellege és az adatsérülés kockázata miatt a *.json* fájlból nem importálhat beállításokat.
>
>

## <a name="next-steps"></a>További lépések

* [Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával](azure-portal-dashboard-share-access.md)
* [Kedvencek hozzáadása, eltávolítása és átrendezése](azure-portal-add-remove-sort-favorites.md)
