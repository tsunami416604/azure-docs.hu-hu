---
title: "Hogyan lehet elvégezni egy áttekintése |} Microsoft Docs"
description: "Miután az Azure AD Privileged Identity Management indította egy áttekintése, megtudhatja, hogyan befejeződését, és az eredmények megtekintése"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: abc2d3dd-afd5-42cf-8a17-6c11f5674c35
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: ca2a1c7c287e4cf6b1b50cfb0068861b6f525596
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Hogyan lehet elvégezni egy az Azure AD Privileged Identity Management áttekintése
Kiemelt szerepkörű rendszergazda egyszer emelt szintű hozzáférés is tekintheti a [biztonsági felülvizsgálat elindult](active-directory-privileged-identity-management-how-to-start-security-review.md). Az Azure AD Privileged Identity Management (PIM) automatikusan elküld egy e-mailt arra kéri a felhasználót a hozzáférésüket áttekintéséhez. Ha a felhasználó nem kapott e-mailt, elküldheti azokat az utasításokat [egy biztonsági felülvizsgálat végrehajtása](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Után a biztonsági felülvizsgálat időszak alatt, vagy a felhasználók végzett, az önálló tekintse át, kövesse a cikkben a felülvizsgálati kezelésére, és az eredmények megtekintéséhez.

## <a name="manage-security-reviews"></a>Biztonsági felülvizsgálat kezelése
1. Lépjen a [Azure-portálon](https://portal.azure.com/) válassza ki a **Azure AD Privileged Identity Management** alkalmazás az irányítópulton.
2. Válassza ki a **értékelést hozzáférési** szakasza az irányítópulton.
3. Válassza ki a kezelni kívánt áttekintése.

A hozzáférés felülvizsgálati részletei panel lehetőség áll rendelkezésre egy szám felülvizsgálat kezeléséhez.

![PIM hozzáférés felülvizsgálati gombok – képernyőkép][1]

### <a name="remind"></a>Emlékeztesse
Ha egy áttekintése be van állítva, így a felhasználók maguk, tekintse át a **emlékeztetése** gomb értesítést küld. 

### <a name="stop"></a>Leállítás
Hozzáférés az összes értékelést rendelkezik befejező dátumát, de használhatja a **leállítása** gomb korai befejezéséhez. A felhasználók még nem ellenőrzött időpontig, ha azok nem fogja tudni a felülvizsgálati befejezése után. Nem indítható újra áttekintése után a rendszer leállt.

### <a name="apply"></a>Jelentkezés
A rendszer egy áttekintése után, mert elérte a záró dátum, vagy manuálisan, megszakította a **alkalmaz** gomb valósítja meg a felülvizsgálati eredményeit. A felhasználói hozzáférés megtagadva a felülvizsgálat alatt, ha ez a lépés, amely a szerepkör-hozzárendelés, azzal eltávolítja az.  

### <a name="export"></a>Exportálás
Ha a biztonsági ellenőrzés eredményének manuálisan alkalmazni kívánt, exportálhatja a felülvizsgálati. A **exportálása** gomb indul el egy CSV-fájl letöltése. Az eredményeket az Excel vagy más programok, nyissa meg a CSV-fájlok is kezelheti.

### <a name="delete"></a>Törlés
Ha nem a felülvizsgálati további iránt érdeklődik, törölje azt. A **törlése** gomb a felülvizsgálati eltávolítja a PIM alkalmazást.

> [!IMPORTANT]
> Akkor lesz nem megjelenik egy figyelmeztetés, még törlése előtt, ezért arról, hogy szeretné-e felülvizsgálat törlése. 

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
