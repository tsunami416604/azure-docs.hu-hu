---
title: Ajánlott eljárások az Azure RBAC
description: Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatának ajánlott eljárásai.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 00fd00112dad9efc31cea83f69bb458a8e1ca935
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617374"
---
# <a name="best-practices-for-azure-rbac"></a>Ajánlott eljárások az Azure RBAC

Ez a cikk az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatának ajánlott eljárásait ismerteti. Ezek az ajánlott eljárások az Azure RBAC és az ügyfelek, például saját tapasztalataiból származnak.

## <a name="only-grant-the-access-users-need"></a>Csak a hozzáférést igénylő felhasználóknak kell megadnia

Az Azure RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szükségük van. Ahelyett, hogy mindenki számára korlátlan engedélyeket adna az Azure-előfizetéshez vagy -erőforrásokhoz, az egyes műveletekhez hatókört adhat meg.

A hozzáférés-vezérlési stratégia megtervezésekor ajánlott eljárásként csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. Ne rendeljen szélesebb hatókörű szerepköröket, még akkor is, ha az kezdetben kényelmesebbnek tűnik. Egyéni szerepkörök létrehozásakor csak a felhasználóknak szükséges engedélyeket kell tartalmazniuk. A szerepkörök és hatókörök korlátozásával korlátozhatja, hogy milyen erőforrásokra van veszélye, ha a rendszerbiztonsági tag még mindig biztonságban van.

Az alábbi ábrán egy javasolt minta látható az Azure RBAC használatához.

![Az Azure RBAC és a legalacsonyabb jogosultság](./media/best-practices/rbac-least-privilege.png)

További információ a szerepkör-hozzárendelések hozzáadásáról: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure Portal használatával](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Az előfizetés-tulajdonosok számának korlátozása

Legfeljebb 3 előfizetés-tulajdonossal rendelkezhet, hogy csökkentse a feltört tulajdonos általi illetéktelen behatolás lehetőségét. Ezt a javaslatot Azure Security Center lehet figyelni. A Security Center egyéb identitási és hozzáférési javaslataival kapcsolatban lásd: [biztonsági javaslatok – útmutató](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management használata

A rendszerjogosultságú fiókok rosszindulatú cyber-támadások elleni védelme érdekében Azure Active Directory Privileged Identity Management (PIM) használatával csökkentheti a jogosultságok expozíciós idejét, és a jelentésekkel és riasztásokkal növelheti a használat láthatóságát. A PIM az Azure AD-hez és az Azure-erőforrásokhoz való, igény szerinti privilegizált hozzáférés biztosításával segíti a Kiemelt fiókok elleni védekezést. A hozzáférés időkorlátja lehet, amely után a rendszer automatikusan visszavonja a jogosultságokat. 

További információ: [Mi az Azure ad Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="next-steps"></a>További lépések

- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)