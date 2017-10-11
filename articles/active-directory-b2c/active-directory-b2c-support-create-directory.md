---
title: "Az Azure Active Directory B2C: Létrehozása bérlők hibaelhárítása |} Microsoft Docs"
description: "Problémák és megoldások létrehozásához az Azure Active Directory vagy az Azure Active Directory B2C-bérlő."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 81af4536fc223319369aff262d42149cfbf1a1e9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
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

