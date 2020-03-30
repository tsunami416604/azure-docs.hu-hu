---
title: Az Azure-erőforrás-szerepkörökhöz való hozzáférés áttekintése a PIM -Azure AD-ben | Microsoft dokumentumok
description: Ismerje meg, hogyan tekintheti meg az Azure-erőforrás-szerepkörök elérését az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847005"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Az Azure-erőforrás-szerepkörökhöz való hozzáférés áttekintése a kiemelt identitáskezelésben

A kiemelt identitáskezelés (PIM) hozzáférési felülvizsgálatok segítségével biztonságos hozzáférést biztosít a kiemelt szerepkörök az Azure Active Directoryban (Azure AD). Ez a cikk a kiemelt szerepkör-hozzárendelések áttekintésének elvégzéséhez szükséges lépéseket egy Azure AD-hozzáférés-felülvizsgálat.

Ha rendszergazdai szerepkörhöz van rendelve, előfordulhat, hogy a rendszergazda által imitált hozzáférési felülvizsgálatot kell elvégeznie a szerepkör szükségességének megerősítéséhez. A megerősítő kérelem jöhet egy e-mailt, amely tartalmaz egy hivatkozást, vagy megerősítheti az [Azure Portalon.](https://portal.azure.com)

Ha Ön kiemelt jogosultságú szerepkör-rendszergazda, és érdeklődik a hozzáférési felülvizsgálatok iránt, további részleteket a [Hozzáférés-felülvizsgálat indítása](pim-resource-roles-start-access-review.md)című részben talál.

## <a name="approve-or-deny-access"></a>Hozzáférés jóváhagyása vagy megtagadása

A hozzáférés jóváhagyható vagy megtagadható attól függően, hogy továbbra is használja-e ezt a szerepkört. Válassza **a Jóváhagyás** lehetőséget, ha a szerepkörben szeretne maradni, vagy a Megtagadás **lehetőséget,** ha már nincs szüksége a hozzáférésre. Az állapot csak akkor változik, ha a véleményező alkalmazza az eredményeket.

A hozzáférési felülvizsgálat megkereséséhez és befejezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Válassza az **Azure Active Directory** lehetőséget, és nyissa meg a Kiemelt **identitáskezelést.**
1. Válassza **a Hozzáférés áttekintése lehetőséget.**

   ![Képernyőkép a Kiemelt identitáskezelés alkalmazásról, amelyen a Hozzáférés áttekintése panel van kiválasztva](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Jelölje ki a végrehajtani kívánt értékelést.
1. Válassza **a Jóváhagyás** vagy a **Megtagadás**lehetőséget. Az **Ok megadása mezőben**szükség esetén adja meg a döntés üzleti indoklását.

   ![Képernyőkép: A Részletek áttekintése lap](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>További lépések

- [Access-ellenőrzés végrehajtása az Azure AD-szerepkörökről a kiemelt identitáskezelésben](pim-how-to-perform-security-review.md)
