---
title: Fiókkezelés – Azure VMware-megoldás a CloudSimple-portálon
description: Ismerteti, hogyan kezelheti a fiókokat az Azure VMware-megoldásban a CloudSimple-portálon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025367"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Fiókok kezelése az Azure VMware-megoldásban a CloudSimple-portálon

A CloudSimple szolgáltatás létrehozásakor létrehoz egy fiókot a CloudSimple. A fiók társítva van az Azure-előfizetéséhez, ahol a szolgáltatás található. Az előfizetéshez tartozó tulajdonosi és közreműködői szerepkörökkel rendelkező felhasználók hozzáférhetnek a CloudSimple-portálhoz. A CloudSimple szolgáltatáshoz társított Azure-előfizetés azonosítója és a bérlő azonosítója a fiókok oldalon található.

A CloudSimple-portál fiókjainak kezeléséhez [nyissa meg a portált](access-cloudsimple-portal.md) , és válassza a **fiók** lehetőséget az oldalsó menüben.

Válassza az **Összefoglalás** lehetőséget a vállalat CloudSimple-konfigurációjával kapcsolatos információk megtekintéséhez. Megjelenik a felhő-konfiguráció jelenlegi kapacitása, beleértve a privát felhők számát, a teljes tárterületet, a vSphere, a csomópontok számát és a számítási magok számát. Ha az aktuális konfiguráció nem felel meg az összes igénynek, a rendszer hivatkozást tartalmaz a további csomópontok megvásárlásához.

## <a name="email-alerts"></a>E-mailes riasztások

Bármely olyan személy e-mail-címét felveheti, akit értesíteni szeretne a saját Felhőbeli konfiguráció változásairól.

1. A **további e-mail-riasztások** területen kattintson az **új hozzáadása**lehetőségre.
2. Adja meg az e-mail-címet.
3. Nyomja le az ENTER billentyűt.  

Egy bejegyzés eltávolításához kattintson az **X**gombra.

## <a name="cloudsimple-operator-access"></a>CloudSimple-kezelő hozzáférése

Az operátori hozzáférés beállítása lehetővé teszi, hogy a CloudSimple segítséget nyújtson a CloudSimple-portálra való bejelentkezést lehetővé tevő támogatási szakembernek.  A beállítás alapértelmezés szerint engedélyezve van. A támogatási mérnök által az ügyfél-fiókba való bejelentkezéskor végrehajtott összes művelet rögzítve van, és elérhetővé válik a **tevékenység** > **naplózása** lapon végzett felülvizsgálathoz.

Kattintson a **CloudSimple operátor hozzáférés engedélyezve** váltógomb lehetőségre a hozzáférés be-és kikapcsolása.
