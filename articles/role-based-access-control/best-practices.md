---
title: Gyakorlati tanácsok az Azure RBAC-hoz
description: Gyakorlati tanácsok az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726773"
---
# <a name="best-practices-for-azure-rbac"></a>Gyakorlati tanácsok az Azure RBAC-hoz

Ez a cikk ismerteti az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kapcsolatos gyakorlati tanácsokat. Ezek az ajánlott eljárások az Azure RBAC-mal és az önhöz hasonló ügyfelek tapasztalataiból származnak.

## <a name="only-grant-the-access-users-need"></a>Csak a felhasználók nak szükséges hozzáférés megadása

Az Azure RBAC használatával elkülönítheti a feladatokat a csapaton belül, és csak annyi hozzáférést biztosíthat a felhasználóknak, amelyeka feladataik elvégzéséhez szükségük van. Ahelyett, hogy mindenki számára korlátlan engedélyeket adna az Azure-előfizetéshez vagy -erőforrásokhoz, az egyes műveletekhez hatókört adhat meg.

A hozzáférés-vezérlési stratégia megtervezésekor ajánlott eljárásként csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. A következő ábrán az RBAC használatának egy javasolt mintája látható.

![RBAC és minimális jogosultságok](./media/best-practices/rbac-least-privilege.png)

A szerepkör-hozzárendelések hozzáadásáról a [Szerepkör-hozzárendelések hozzáadása és eltávolítása](role-assignments-portal.md)című témakörben talál további információt.

## <a name="limit-the-number-of-subscription-owners"></a>Az előfizetés-tulajdonosok számának korlátozása

Legfeljebb 3 előfizetés-tulajdonossal kell rendelkeznie, hogy csökkentse a feltört tulajdonos általi jogsértés lehetőségét. Ez a javaslat az Azure Security Centerben figyelhető. A Security Center egyéb identitás- és hozzáférési javaslatait a [Biztonsági javaslatok – útmutató](../security-center/recommendations-reference.md)című témakörben talál.

## <a name="use-azure-ad-privileged-identity-management"></a>Az Azure AD kiemelt identitáskezelése

A kiemelt jogosultságú fiókok védelme a rosszindulatú kibertámadások ellen, az Azure Active Directory kiemelt identitáskezelés (PIM) használatával csökkentheti a jogosultságok expozíciós idejét, és jelentések és riasztások révén növelheti a használatuk láthatóságát. A PIM segít megvédeni a kiemelt jogosultságú fiókokat azáltal, hogy csak időben biztosít kiemelt hozzáférést az Azure AD és az Azure-erőforrásokhoz. A hozzáférés időhöz kötött lehet, amely után a jogosultságok automatikusan visszavonásra kerülnek. 

További információ: [Mi az Azure AD kiemelt identitáskezelés?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>További lépések

- [Az Azure RBAC hibáinak elhárítása](troubleshooting.md)