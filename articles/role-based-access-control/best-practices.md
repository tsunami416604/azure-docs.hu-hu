---
title: Ajánlott eljárások az Azure RBAC
description: Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatának ajánlott eljárásai.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726773"
---
# <a name="best-practices-for-azure-rbac"></a>Ajánlott eljárások az Azure RBAC

Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatának ajánlott eljárásait ismerteti. Ezek az ajánlott eljárások az Azure RBAC és az ügyfelek, például saját tapasztalataiból származnak.

## <a name="only-grant-the-access-users-need"></a>Csak a hozzáférést igénylő felhasználóknak kell megadnia

Az Azure RBAC segítségével elkülönítheti a feladatait a csapaton belül, és csak a felhasználók számára biztosíthatja a feladatok elvégzéséhez szükséges hozzáférés mennyiségét. Ahelyett, hogy mindenki számára korlátlan engedélyeket adna az Azure-előfizetéshez vagy -erőforrásokhoz, az egyes műveletekhez hatókört adhat meg.

A hozzáférés-vezérlési stratégia megtervezésekor ajánlott eljárásként csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. A következő ábrán az RBAC használatának egy javasolt mintája látható.

![RBAC és minimális jogosultságok](./media/best-practices/rbac-least-privilege.png)

További információ a szerepkör-hozzárendelések hozzáadásáról: [szerepkör-hozzárendelések hozzáadása vagy eltávolítása](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Az előfizetés-tulajdonosok számának korlátozása

Legfeljebb 3 előfizetés-tulajdonossal rendelkezhet, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. Ezt a javaslatot Azure Security Center lehet figyelni. A Security Center egyéb identitási és hozzáférési javaslataival kapcsolatban lásd: [biztonsági javaslatok – útmutató](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management használata

A rendszerjogosultságú fiókok rosszindulatú cyber-támadások elleni védelme érdekében Azure Active Directory Privileged Identity Management (PIM) használatával csökkentheti a jogosultságok expozíciós idejét, és a jelentésekkel és riasztásokkal növelheti a használat láthatóságát. A PIM az Azure AD-hez és az Azure-erőforrásokhoz való, igény szerinti privilegizált hozzáférés biztosításával segíti a Kiemelt fiókok elleni védekezést. A hozzáférés időkorlátja lehet, amely után a rendszer automatikusan visszavonja a jogosultságokat. 

További információ: [Mi az Azure ad Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>További lépések

- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)