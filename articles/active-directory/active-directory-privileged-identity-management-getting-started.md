---
title: "Ismerkedés az Azure AD Privileged Identity Management alkalmazással | Microsoft Docs"
description: "Megtudhatja, hogyan kezelhet kiemelt identitásokat az Azure Portal Azure Active Directory Privileged Identity Management alkalmazásával."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 2299db7d-bee7-40d0-b3c6-8d628ac61071
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 17cdff033cc3dbb199d11c3b8ac1acbc92499877
ms.contentlocale: hu-hu
ms.lasthandoff: 07/10/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management használatba vétele
Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ebbe a hatókörbe beletartozik az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure Portal irányítópultjára.

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure AD Privileged Identity Management használata előtt fel kell vennie az alkalmazást az Azure Portal irányítópultjára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki azt a címtárat, amelyben a PIM használatát tervezi.
3. Válassza a **További szolgáltatások** lehetőséget, és a Szűrők szövegmezővel keresse meg az **Azure AD Privileged Identity Management** alkalmazást.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor automatikusan megkapja a címtár **biztonsági rendszergazda** és **kiemelt szerepkörű rendszergazda** szerepkörét. Csak a kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók szerepkör-hozzárendeléseit. Továbbá futtathatja a [biztonsági varázslót](active-directory-privileged-identity-management-security-wizard.md). Ez végigvezeti Önt az első felderítési és hozzárendelési élményen.

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése
Az Azure AD Privileged Identity Management beállítása után az alkalmazás minden megnyitásakor megjelenik a navigációs panel. Ezen a panelen végezheti el az identitáskezelési feladatokat.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

* A **Saját szerepkörök** lehetőség kiválasztással megnyithatja az Önhöz rendelt szerepkörök listáját. Ebben a szakaszban aktiválhatja az Ön számára elérhető szerepköröket.
* A **Kérések jóváhagyása (Előnézet)** a címtárban található felhasználók függőben lévő aktiválási kéréseit jeleníti meg. [Részletek](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
* A **Függőben lévő kérések (Előnézet)** az aktiválásig eljutott aktuális kéréseket jeleníti meg.
* A **Hozzáférés felülvizsgálata** lehetőséggel megtekintheti a függőben lévő hozzáférési felülvizsgálatokat, amelyeket be kell fejeznie, legyen szó akár a saját hozzáféréseiről vagy egy másik felhasználóéról.
* A „Kezelés” szakaszban található **Azure AD-címtár szerepköreinek kezelése** az az irányítópult, amelyen a kiemelt szerepkörök rendszergazdái kezelhetik a szerepkör-hozzárendeléseket, módosíthatják a szerepkör-aktiválási beállításokat, elindíthatják a hozzáférési felülvizsgálatokat stb. Az irányítópulton található beállítások kizárólag a kiemelt szerepkörű rendszergazdák számára érhetők el.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD Privileged Identity Management áttekintése](active-directory-privileged-identity-management-configure.md) további részleteket tartalmaz arról, hogyan kezelheti a rendszergazdai hozzáférést a szervezetében.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

