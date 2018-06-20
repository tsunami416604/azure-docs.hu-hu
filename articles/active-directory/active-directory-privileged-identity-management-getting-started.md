---
title: Ismerkedés az Azure AD Privileged Identity Management alkalmazással | Microsoft Docs
description: Megtudhatja, hogyan kezelhet kiemelt identitásokat az Azure Portal Azure Active Directory Privileged Identity Management alkalmazásával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: protection
ms.topic: get-started-article
ms.workload: identity
ms.date: 09/17/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 882f76b8f6529e2a6e72a5bbc1b24842150e1af0
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233237"
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management használatba vétele

Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ebbe a hatókörbe beletartozik az Azure-erőforrások, az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure Portal irányítópultjára.

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele

Az Azure AD Privileged Identity Management használata előtt fel kell vennie az alkalmazást az Azure Portal irányítópultjára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki azt a címtárat, amelyben a PIM használatát tervezi.
3. Válassza a **Minden szolgáltatás** lehetőséget, és a Szűrő szövegmezővel keresse meg az **Azure AD Privileged Identity Management** elemet.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor automatikusan megkapja a címtár **biztonsági rendszergazda** és **kiemelt szerepkörű rendszergazda** szerepkörét. Csak a kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-címtárra vonatkozó szerepkör-hozzárendeléseit. Továbbá futtathatja a [biztonsági varázslót](active-directory-privileged-identity-management-security-wizard.md). Ez végigvezeti Önt az első felderítési és hozzárendelési élményen.

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése

Az Azure AD Privileged Identity Management beállítása után az alkalmazás minden megnyitásakor megjelenik a navigációs panel. Ezen a panelen végezheti el az identitáskezelési feladatokat.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- A **Saját szerepkörök** megjeleníti az Önhöz rendelt elérhető és aktív szerepköröket. Itt aktiválhatja az Önhöz rendelt elérhető szerepköröket.
- A **Kérések jóváhagyása (Előnézet)** a címtárban található felhasználók számára elérhető Azure AD-címtár szerepkörök aktiválási kéréseit jeleníti meg, amelyeket Önnek kell jóváhagynia. [Részletek](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- A **Függőben lévő kérések (Előnézet)** az elérhető szerepkörök hozzárendelésének aktiválására irányuló függőben lévő kéréseket jeleníti meg.
- A **Hozzáférés felülvizsgálata** lehetőséggel megtekintheti azon aktív hozzáférési felülvizsgálatok listáját, amelyeknek a befejezésére Ön van kijelölve, legyen szó a saját hozzáféréseiről vagy egy másik felhasználóéról.
- A bal oldali navigációs menü kezelési szakaszában található **Azure AD-címtár szerepköreinek kezelése** azt az irányítópultot jeleníti meg, amelyen a kiemelt szerepkörök rendszergazdái kezelhetik a szerepkör-hozzárendeléseket, módosíthatják a szerepköraktiválási beállításokat, elindíthatják a hozzáférési felülvizsgálatokat stb. Ez az irányítópult kizárólag a kiemelt szerepkörrel rendelkező rendszergazdák számára érhető el. Ezek a felhasználók a Saját nézet nevű speciális irányítópulthoz is hozzáférhetnek. A Saját nézet irányítópult csak az irányítópulthoz hozzáférő felhasználó információit jeleníti meg az egész bérlő helyett.
- A bal oldali navigációs menü kezelési szakaszában található **Azure-erőforrásszerepkörök (Előnézet)** azon előfizetési erőforrások listáját jeleníti meg, amelyekhez szerepkör-hozzárendelése van. 

## <a name="next-steps"></a>További lépések
[Az Azure AD Privileged Identity Management áttekintése](active-directory-privileged-identity-management-configure.md) további részleteket tartalmaz arról, hogyan kezelheti a rendszergazdai hozzáférést a szervezetében.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
