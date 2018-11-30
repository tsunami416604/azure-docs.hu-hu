---
title: A PIM - Azure használatának megkezdése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti és az Azure Portalon az Azure AD Privileged Identity Management (PIM) használatának első lépései.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 95582fb62f19dcc34aa03a6550f4dbc46257cf7f
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494342"
---
# <a name="start-using-pim"></a>Ismerkedés a PIM-mel

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM), is felügyelheti, ellenőrizheti és hozzáférés figyelése a szervezeten belül. Ebbe a hatókörbe beletartozik az Azure-erőforrások, az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk ismerteti, hogyan engedélyezheti és a PIM használatának első lépései.

## <a name="prerequisites"></a>Előfeltételek

A PIM használata kell rendelkeznie a következő licencek egyikét:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5

További információkért lásd: [előfizetési követelmények a PIM használatát tervezi](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Első, aki a PIM használata

Ha Ön az első, aki a címtárban a PIM használatát tervezi, akkor automatikusan megkapja a [biztonsági rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) és [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkörökhöz a címtárban. Csak a kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-címtárra vonatkozó szerepkör-hozzárendeléseit. Ezenkívül, dönthet úgy, hogy futtassa a [biztonsági varázsló](pim-security-wizard.md) végigvezeti az első felderítési és hozzárendelési élményen.

## <a name="enable-pim"></a>PIM engedélyezése

A címtár PIM használatának megkezdéséhez először engedélyeznie kell a PIM.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) a címtár globális rendszergazdájaként.

    Egy szervezeti fiókkal globális rendszergazdának kell lennie (például @yourdomain.com), nem Microsoft-fiókkal (például @outlook.com), amely lehetővé teszi a PIM egy könyvtár.

1. Kattintson a **minden szolgáltatás** , és keresse meg a **Azure AD Privileged Identity Management** szolgáltatás.

    ![Az Azure AD Privileged Identity Management szolgáltatásra szolgáltatások összes](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson a PIM a rövid útmutató megnyitásához.

1. Kattintson a lista **hozzájárulás megadása a PIM számára**.

    ![Hozzájárulás megadása a PIM számára](./media/pim-getting-started/consent-pim.png)

1. Kattintson a **saját identitás ellenőrzése** igazolhatja személyazonosságát, az Azure MFA Megismeréséhez. Válasszon egy fiókot, a rendszer.

    ![Fiók kiválasztása](./media/pim-getting-started/pick-account.png)

1. Ha további információt az ellenőrzéshez szükséges, hogy rendszer végigvezeti a folyamat. További információkért lásd: [segítség a kétlépcsős ellenőrzéshez](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![További információ szükséges](./media/pim-getting-started/more-information-required.png)

    Például előfordulhat, hogy megkérdezi, hogy telefonos ellenőrzésének biztosításához.

    ![További biztonsági ellenőrzés](./media/pim-getting-started/additional-security-verification.png)

1. Ha az ellenőrzési folyamat befejezte, kattintson a **hozzájárulás** gombra.

1. A megjelenő üzenetben kattintson **Igen** jóváhagyást a PIM szolgáltatáshoz.

    ![Hozzájárulás megadása a PIM-üzenet](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Iratkozzon fel a PIM az Azure AD-szerepkörök

Miután engedélyezte a PIM a címtáron, kell iratkozzon fel a PIM kezelése az Azure AD-szerepkörökhöz.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure AD-szerepkörök**.

    ![Iratkozzon fel a PIM az Azure AD-szerepkörök](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Kattintson a **regisztráció**.

1. A megjelenő üzenetben kattintson **Igen** , iratkozzon fel a PIM kezelése az Azure AD-szerepkörökhöz.

    ![Iratkozzon fel a PIM az Azure AD-szerepkörök üzenet](./media/pim-getting-started/sign-up-pim-message.png)

    Regisztráció befejezését követően az Azure AD-beállítások engedélyezve lesz. Szükség lehet, hogy frissítse a portált.

    Fedezze fel, és válassza ki az Azure-erőforrások védelme a PIM kapcsolatos információkért lásd: [Fedezze fel az Azure erőforrások kezeléséhez a PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

A PIM beállítása után az identitáskezelési feladatokat is végezhet.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/pim-getting-started/pim-quickstart-tasks.png)

| Feladat + kezelése | Leírás |
| --- | --- |
| **Saját szerepkörök**  | Önhöz rendelt elérhető és aktív szerepköröket listáját jeleníti meg. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket. |
| **Saját kérések** | Megjeleníti a függőben lévő kérések az elérhető szerepkörök hozzárendelésének aktiválására. |
| **Alkalmazás-hozzáférés** | Lehetővé teszi lehetséges késedelmeket és aktiválása után azonnal olyan szerepkört használjon. |
| **Kérések jóváhagyása** | A felhasználók a címtárban jogosult szerepkörök aktiválási kéréseit listáját jeleníti meg, amely jóváhagyásra váró jelölik. |
| **Hozzáférés áttekintése** | Hozzárendelt végrehajtásához, e, lehetőséggel megtekintheti a saját magának vagy valaki más aktív hozzáférési felülvizsgálatok sorolja fel. |
| **Az Azure AD-szerepkörök** | Megjeleníti egy irányítópultot és a kiemelt szerepkörű rendszergazdák számára az Azure AD-címtár szerepkör-hozzárendelések kezeléséhez beállításait. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |
| **Azure-erőforrások** | Megjeleníti egy irányítópultot és a kiemelt szerepkörű rendszergazdák kezelhetik a szerepkör-hozzárendelések az Azure resource beállításait. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>A PIM csempe hozzáadása az irányítópulthoz

Nyissa meg a PIM megkönnyítése, hozzá kell adnia a PIM csempe az Azure portal Irányítópultjára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson a **minden szolgáltatás** , és keresse meg a **Azure AD Privileged Identity Management** szolgáltatás.

    ![Az Azure AD Privileged Identity Management szolgáltatásra szolgáltatások összes](./media/pim-getting-started/pim-all-services-find.png)

1. Kattintson a PIM a rövid útmutató megnyitásához.

1. Ellenőrizze **panel rögzítése az irányítópulton** rögzítése az irányítópulton a PIM rövid panelt.

    ![Panel rögzítése az irányítópulton](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Az Azure-irányítópulton ekkor megjelenik egy csempe ehhez hasonló:

    ![A PIM rövid csempe](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>További lépések

- [A PIM az Azure AD-címtárbeli szerepkörök hozzárendelése](pim-how-to-add-role-to-user.md)
- [Fedezze fel a PIM kezelése Azure-erőforrások](pim-resource-roles-discover-resources.md)
