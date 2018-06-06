---
title: Az Azure Active Directory B2C létrehozása bérlők hibaelhárítása |} Microsoft Docs
description: Problémák és megoldások létrehozásához az Azure Active Directory vagy az Azure Active Directory B2C-bérlő.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 90d9d2fb80dfbd094754850b7d1270a5fafcdd96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712504"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Hibaelhárítás az Azure Active Directory vagy az Azure Active Directory B2C-bérlő létrehozása 

## <a name="create-an-azure-ad-tenant"></a>Az Azure AD-bérlő létrehozása
Ha az Azure Active Directory (Azure AD) bérlő nem hozható létre az első kísérlet alkalmával, próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az Azure támogatási szolgálatához.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása
Ha hibát tapasztal amikor Ön [hozzon létre egy Azure Active Directory B2C (Azure AD B2C) bérlő](active-directory-b2c-get-started.md), próbálja meg a következő beállításokat:

* Ha az Azure AD B2C bérlő nem jelenik meg a bérlők listáját, újra megpróbálja létrehozni a bérlőt.
* Az Azure AD B2C bérlő jelenik meg a bérlők listáját, és a következő hibaüzenet jelenik meg, ha a bérlő törölje és hozza létre újra:

    "Nem tudta befejezni a B2C-bérlő"contosob2c"létrehozását. Látogasson el a [hivatkozás](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) további útmutatást. "
* Vannak ismert problémák törölje a meglévő Azure AD B2C-bérlő és hozza létre újból a ugyanazon a néven. Amikor létrehoz egy új Azure AD B2C-bérlő, a másik tartománynevet kell használnia.
* Ha ezek a megoldások nem működnek, lépjen kapcsolatba az Azure támogatási szolgálatához. További információkért lásd: [fájl támogatási kérelmek az Azure AD B2C](active-directory-b2c-support.md).

