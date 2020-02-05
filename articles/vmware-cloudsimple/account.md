---
title: Fiókkezelés – Azure VMware Solutions (AVS) portál
description: Ismerteti, hogyan kezelheti a fiókokat az Azure VMware Solutions (AVS) portálon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025367"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Fiókok kezelése az Azure VMware Solutions (AVS) portálon

Az AVS szolgáltatás létrehozásakor létrejön egy fiók az AVS-ben. A fiók társítva van az Azure-előfizetéséhez, ahol a szolgáltatás található. Az előfizetéshez tartozó tulajdonosi és közreműködői szerepkörökkel rendelkező felhasználók hozzáférhetnek az AVS-portálhoz. Az AVS szolgáltatáshoz társított Azure-előfizetés azonosítója és a bérlő azonosítója a fiókok oldalon található.

A fiókok az AVS-portálon való kezeléséhez [nyissa meg a portált](access-cloudsimple-portal.md) , és válassza a **fiók** lehetőséget az oldalsó menüben.

Válassza az **Összefoglalás** lehetőséget a vállalat AVS-konfigurációjával kapcsolatos információk megtekintéséhez. Megjelenik a felhő-konfiguráció jelenlegi kapacitása, beleértve az AVS privát felhők számát, a teljes tárterületet, a vSphere, a csomópontok számát és a számítási magok számát. Ha az aktuális konfiguráció nem felel meg az összes igénynek, a rendszer hivatkozást tartalmaz a további csomópontok megvásárlásához.

## <a name="email-alerts"></a>Értesítő e-mailek

Bármely olyan személy e-mail-címét felveheti, akit értesíteni szeretne az AVS Private Cloud konfigurációjának módosításairól.

1. A **további e-mail-riasztások** területen kattintson az **új hozzáadása**lehetőségre.
2. Adja meg az e-mail-címet.
3. Nyomja le az ENTER billentyűt.  

Egy bejegyzés eltávolításához kattintson az **X**gombra.

## <a name="avs-operator-access"></a>AVS-kezelő hozzáférése

A kezelői hozzáférés beállítása lehetővé teszi, hogy az AVS segítséget nyújtson a hibaelhárításhoz azáltal, hogy egy támogatási szakember engedélyezi a bejelentkezést az AVS-portálra. A beállítás alapértelmezés szerint engedélyezve van. A támogatási mérnök által az ügyfél fiókjába való bejelentkezéskor végrehajtott összes művelet rögzítve van, és elérhető a **tevékenység** > a **napló** oldalán.

Kattintson az **AVS-kezelő hozzáférés engedélyezve** váltógomb lehetőségre a hozzáférés be-vagy kikapcsolásához.
