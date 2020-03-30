---
title: Fiókkezelés – Azure VMware-megoldás a CloudSimple portálon
description: A fiókok kezelése az Azure VMware-megoldáson a CloudSimple portálon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025367"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Fiókok kezelése az Azure VMware-megoldáson a CloudSimple portálon

Amikor létrehozza a CloudSimple szolgáltatást, létrehoz egy fiókot a CloudSimple-en. A fiók az Azure-előfizetéshez van társítva, ahol a szolgáltatás található. Az előfizetésben tulajdonosi és közreműködői szerepkörrel rendelkező összes felhasználó hozzáférhet a CloudSimple portálhoz. Az Azure-előfizetés-azonosító és a CloudSimple szolgáltatáshoz társított bérlői azonosító a Fiókok lapon találhatók.

A CloudSimple portálon lévő fiókok kezeléséhez [érje el a portált,](access-cloudsimple-portal.md) és válassza az oldalmenü **Fiók parancsát.**

Válassza **az Összegzés** lehetőséget a vállalat CloudSimple-konfigurációjával kapcsolatos információk megtekintéséhez. A felhőkonfiguráció jelenlegi kapacitása látható, beleértve a privát felhők számát, a teljes tárhelyet, a vSphere fürtkonfigurációt, a csomópontok számát és a számítási magok számát. Egy hivatkozás t tartalmaz további csomópontok vásárlásához, ha az aktuális konfiguráció nem felel meg az összes igényeinek.

## <a name="email-alerts"></a>E-mailes riasztások

Megadhat e-mail címeket azoknak a személyeknek, akiket értesíteni szeretne a magánfelhő-konfiguráció változásairól.

1. A **További e-mail értesítések** területen kattintson az **Új hozzáadása**gombra.
2. Adja meg az e-mail címet.
3. Nyomja meg a Return billentyűt.  

Bejegyzés eltávolításához kattintson az **X**gombra.

## <a name="cloudsimple-operator-access"></a>CloudSimple operátori hozzáférés

Az operátori hozzáférési beállítás lehetővé teszi a CloudSimple számára, hogy segítsen a hibaelhárításban azáltal, hogy lehetővé teszi egy támogatási szakember számára, hogy jelentkezzen be a CloudSimple portálra.  A beállítás alapértelmezés szerint engedélyezve van. A támogatási szakember által végrehajtott összes műveletet, amikor bejelentkezik az ügyfélfiókjába, rögzíti, és elérhető az ellenőrzéshez a > **Tevékenységnaplózás** oldalon. **Activity**

Kattintson a **CloudSimple operátori hozzáférés engedélyezve** váltáshoz a hozzáférés be- és kikapcsolására.
