---
title: Felügyelt alkalmazások közzététele a portálon keresztül
description: Bemutatja, hogyan használhatja az Azure Portalon egy Azure felügyelt alkalmazás, amely a szervezet tagjainak szánt.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651721"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Szolgáltatáskatalógus-alkalmazás közzététele az Azure Portalon keresztül

Az Azure Portal segítségével közzéteheti a szervezet tagjainak szánt [felügyelt alkalmazásokat.](overview.md) Az informatikai részleg közzétehet például olyan felügyelt alkalmazásokat, amelyek biztosítják a vállalati szabványoknak való megfelelést. Ezeket a felügyelt alkalmazásokat a szolgáltatáskatalóguson keresztül lehet elérni az Azure Marketplace helyett.

## <a name="prerequisites"></a>Előfeltételek

Felügyelt alkalmazás közzétételekor meg kell adnia egy identitást az erőforrások kezeléséhez. Azt javasoljuk, hogy adjon meg egy Azure Active Directory felhasználói csoportot. Az Azure Active Directory felhasználói csoportjának létrehozásáról a [Csoport létrehozása és tagok hozzáadása az Azure Active Directoryban](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című témakörben található. 

A felügyelt alkalmazásdefiníciót tartalmazó .zip fájlnak URI-n keresztül kell elérhetőnek lennie. Javasoljuk, hogy töltse fel a .zip fájlt egy tárolási blobba. 

## <a name="create-managed-application-with-portal"></a>Felügyelt alkalmazás létrehozása portállal

1. A bal felső sarokban válassza a **+ Új**lehetőséget.

   ![Új szolgáltatás](./media/publish-portal/new.png)

1. Keressen **szolgáltatási katalógust**.

1. Az eredmények között görgessen addig, amíg meg nem találja **a Szolgáltatáskatalógus felügyelt alkalmazásdefinícióját.** Válassza ki.

   ![Felügyelt alkalmazásdefiníciók keresése](./media/publish-portal/select-managed-apps-definition.png)

1. A **Létrehozás gombra** a felügyelt alkalmazásdefiníció létrehozásának megkezdéséhez válassza a Létrehozás gombot.

   ![Felügyelt alkalmazásdefiníció létrehozása](./media/publish-portal/create-definition.png)

1. Adja meg a név, a megjelenítendő név, a leírás, a hely, az előfizetés és az erőforráscsoport értékeit. Csomagfájl URI-jának megadásához adja meg a létrehozott zip-fájl elérési útját.

   ![Értékek megadására](./media/publish-portal/fill-application-values.png)

1. Amikor a Hitelesítési és zárolási szint szakaszhoz, válassza az **Engedélyezés hozzáadása**lehetőséget.

   ![Engedélyezés hozzáadása](./media/publish-portal/add-authorization.png)

1. Az erőforrások kezeléséhez válasszon ki egy Azure Active Directory-csoportot, és kattintson az **OK gombra.**

   ![Engedélyezési csoport hozzáadása](./media/publish-portal/add-auth-group.png)

1. Miután megadta az összes értéket, válassza a **Létrehozás gombot.**

   ![Felügyelt alkalmazás létrehozása](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Felügyelt alkalmazáspéldákért olvassa el [a Mintaprojektek az Azure által felügyelt alkalmazásokhoz.](sample-projects.md)
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.