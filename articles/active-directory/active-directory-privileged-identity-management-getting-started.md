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
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 6945529b8189c270ca6e5c41be0cd0d9caa37cb4
ms.lasthandoff: 02/28/2017

---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management használatba vétele
Az Azure Active Directory (AD) Privileged Identity Management segítségével kezelheti, irányíthatja és felügyelheti a szervezeten belüli hozzáféréseket. Ebbe beletartozik az Azure AD és más online Microsoft-szolgáltatások, például az Office 365 vagy a Microsoft Intune erőforrásaihoz való hozzáférés.

Ez a cikk bemutatja, hogyan veheti fel az Azure AD PIM alkalmazást az Azure Portal irányítópultjára.

## <a name="add-the-privileged-identity-management-application"></a>A Privileged Identity Management alkalmazás felvétele
Az Azure AD Privileged Identity Management használata előtt fel kell vennie az alkalmazást az Azure Portal irányítópultjára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a címtára globális rendszergazdájaként.
2. Ha a szervezet több címtárral rendelkezik, akkor kattintson a felhasználónevére az Azure Portal jobb felső sarkában. Válassza ki azt a címtárat, amelyben a PIM használatát tervezi.
3. Válassza a **További szolgáltatások** lehetőséget, és a Szűrők szövegmezővel keresse meg az **Azure AD Privileged Identity Management** alkalmazást.
4. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra. Megnyílik a Privileged Identity Management alkalmazás.

Ha Ön az első, aki a címtárban az Azure AD Privileged Identity Management alkalmazást használja, akkor a [biztonság varázsló](active-directory-privileged-identity-management-security-wizard.md) végigvezeti a hozzárendelés kezdeti lépésein. Ezután automatikusan Ön lesz a címtár első **biztonsági rendszergazdája** és **kiemelt szerepkörű rendszergazdája**. Csak a kiemelt szerepkörű rendszergazdák férhetnek hozzá az alkalmazáshoz, hogy más rendszergazdák hozzáférését kezeljék.  

## <a name="navigate-to-your-tasks"></a>A feladatok megkeresése
Az Azure AD Privileged Identity Management beállítása után az alkalmazás minden megnyitásakor megjelenik a navigációs panel. Ezen a panelen végezheti el az identitáskezelési feladatokat.

![Felső szintű PIM-tevékenységek – képernyőkép](./media/active-directory-privileged-identity-management-getting-started/pim_tasks.png)

* A **Saját szerepkörök aktiválása** lehetőség kiválasztással megnyithatja az Önhöz rendelt szerepkörök listáját. Itt aktiválhatja az Ön számára elérhető szerepköröket.
* A **Kiemelt szerepkörök kezelése** az az irányítópult, amelyen a kiemelt szerepkörök rendszergazdái kezelhetik a szerepkör-hozzárendeléseket, módosíthatják a szerepkör-aktiválási beállításokat, elindíthatják a hozzáférési felülvizsgálatokat stb. Az irányítópulton található beállítások kizárólag a kiemelt szerepkörű rendszergazdák számára érhetők el.
* Az **Emelt szintű hozzáférés felülvizsgálata** lehetőséggel megtekintheti a függőben lévő hozzáférési felülvizsgálatokat, amelyeket be kell fejeznie, legyen szó akár a saját hozzáféréseiről vagy egy másik felhasználóéról. 

## <a name="next-steps"></a>Következő lépések
[Az Azure AD Privileged Identity Management áttekintése](active-directory-privileged-identity-management-configure.md) további részleteket tartalmaz arról, hogyan kezelheti a rendszergazdai hozzáférést a szervezetében.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png

