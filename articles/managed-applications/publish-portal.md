---
title: Közzététel az Azure portálon keresztül alkalmazás felügyelt |} Microsoft Docs
description: Bemutatja, hogyan használható az Azure portálon hozzon létre egy Azure felügyelt alkalmazás, amely tagja a szervezet számára készült.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 69d31a7199347574e8866b275ec17ba3997d80c2
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305123"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Az Azure portálon keresztül szolgáltatás katalógus alkalmazás közzététele

Az Azure portál segítségével közzététele [kezelt alkalmazások](overview.md) szolgálnak, hogy a szervezet tagjaira. Például hogy az informatikai részleg, amelyek biztosítják a vállalati szabványoknak való megfelelés felügyelt alkalmazások közzététele. A kezelt alkalmazások a szolgáltatáskatalógus, nem az Azure piactéren keresztül érhetők el.

## <a name="prerequisites"></a>Előfeltételek

Amikor egy kezelt alkalmazás közzététele az erőforrások kezeléséhez identitást adja meg. Azt javasoljuk, hogy megad egy Azure Active Directory felhasználói csoportot. Egy Azure Active Directory felhasználói csoport létrehozásához lásd: [hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](../active-directory/active-directory-groups-create-azure-portal.md). 

A kezelt alkalmazás definícióját tartalmazó .zip fájl URI keresztül elérhetőnek kell lennie. Azt javasoljuk, hogy a storage-blobba töltse fel a .zip fájlt. 

## <a name="create-managed-application-with-portal"></a>Kezelt alkalmazás létrehozása a portállal

1. Válassza ki a bal felső sarokban, **+ új**.

   ![Új szolgáltatás](./media/publish-portal/new.png)

1. Keresse meg **szolgáltatáskatalógus**.

1. Az eredményeket, amíg meg nem látja görgessen **katalógus által felügyelt alkalmazás definíciójának**. Válassza ki azt.

   ![Kezelt alkalmazás-definíciók keresése](./media/publish-portal/select-managed-apps-definition.png)

1. Válassza ki **létrehozása** elindítani a kezelt alkalmazás definícióját létrehozásának folyamatán.

   ![Kezelt alkalmazás-definíció létrehozása](./media/publish-portal/create-definition.png)

1. Adja meg a nevét, megjelenített név, leírás, hely, előfizetés és erőforráscsoportot. A csomagfájl URI adja meg a létrehozott zip-fájl elérési útját.

   ![Adjon meg értékeket](./media/publish-portal/fill-application-values.png)

1. Amikor a hitelesítés és a zárolási szint csoportban, válassza ki a **hozzáadása engedélyezési**.

   ![Adja hozzá az engedélyezési](./media/publish-portal/add-authorization.png)

1. Válasszon egy Azure Active Directory-csoportot, az erőforrások kezelése, és válassza ki **OK**.

   ![Engedélyezési csoport hozzáadása](./media/publish-portal/add-auth-group.png)

1. Ha megadta az értékeket, válassza ki a **létrehozása**.

   ![Felügyelt alkalmazás létrehozása](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Kezelt alkalmazás, tekintse meg a [a kezelt alkalmazások az Azure-hoz](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.