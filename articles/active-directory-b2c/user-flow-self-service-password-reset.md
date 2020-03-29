---
title: Önkiszolgáló jelszó-visszaállítás az Azure Active Directory B2C-ben | Microsoft dokumentumok
description: Bemutatja, hogyan állíthatja be az önkiszolgáló jelszó-visszaállítást az ügyfelek számára az Azure Active Directory B2C-ben
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183108"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Önkiszolgáló jelszó-visszaállítás beállítása az ügyfelek számára

Az önkiszolgáló jelszó-visszaállítási funkcióval a helyi fiókokra regisztrált ügyfelek saját maguk is visszaállíthatják jelszavukat. Ez jelentősen csökkenti a terhet a kisegítő személyzet, különösen akkor, ha az alkalmazás több millió ügyfél használja rendszeresen. Jelenleg az ellenőrzött e-mail cím használata az egyetlen támogatott helyreállítási módszer.

> [!NOTE]
> Ez a cikk a V1 **Bejelentkezés** felhasználói folyamat környezetében használt önkiszolgáló jelszó-visszaállításra vonatkozik, amely a **Helyi fiók signin** identitásszolgáltatóként használja. Ha teljesen testreszabható jelszó-visszaállítási felhasználói folyamatokra van szüksége az alkalmazásból, olvassa el [ezt a cikket.](user-flow-overview.md)
>
>

Alapértelmezés szerint a címtárban nincs bekapcsolva az önkiszolgáló jelszó-visszaállítás. Az alábbi lépésekkel kapcsolhatja be:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) előfizetési rendszergazdaként. Ez ugyanaz a munkahelyi vagy iskolai fiók, vagy ugyanaz a Microsoft-fiók, amelyet a címtár létrehozásához használt.
2. Nyissa meg az **Azure Active Directoryt** (a bal oldali navigációs sávon).
3. Görgessen le a beállítások panelen, és válassza a **Jelszó alaphelyzetbe állítása**lehetőséget.
4. Állítsa **be az Önkiszolgáló jelszó-visszaállítás beállítását** **az Összes**értékre.
5. Kattintson az oldal tetején lévő **Mentés** elemre. Ennyi az egész!

A teszteléshez használja a "Futtatás most" funkciót minden olyan bejelentkezési felhasználói folyamaton, amely helyi fiókkal rendelkezik identitásszolgáltatóként. A helyi fiók bejelentkezési lapján (ahol megad egy e-mail címet és jelszót, vagy egy felhasználónevet és egy jelszót) a **Nem érhető el a fiókhoz elemre** kattintva ellenőrizheti az ügyfélélményt.

> [!NOTE]
> Az önkiszolgáló jelszó-visszaállítási lapok a [vállalati márkajelzési funkcióval](../active-directory/fundamentals/customize-branding.md)testreszabhatók.
>
>

