---
title: Azure VMware-megoldások (AVS) – az AVS-jogosultságok megemelése
description: Leírja, hogyan lehet az AVS-engedélyeket az AVS Private Cloud vCenter rendszergazdai funkcióinak végrehajtására felvenni
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025333"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>AVS-jogosultságok kiemelése rendszergazdai funkciók végrehajtásához az AVS Private Cloud vCenter

Az AVS-jogosultságok megközelítése úgy van kialakítva, hogy a vCenter-felhasználók számára a normál működéshez szükséges jogosultságokat adja. Bizonyos esetekben előfordulhat, hogy a felhasználók további jogosultságokat igényelnek egy adott feladat elvégzéséhez. Egy vCenter SSO-felhasználó jogosultságait korlátozott időtartamra is kiterjesztheti.

A jogosultságok kiterjesztésének okai az alábbiak lehetnek:

* Az Identity sources konfigurálása
* Felhasználói felügyelet
* Elosztott port csoport törlése
* VCenter-megoldások (például biztonsági mentési alkalmazások) telepítése
* Szolgáltatásfiókok létrehozása

> [!WARNING]
> Az emelt szintű jogosultságokkal rendelkező államban végrehajtott műveletek hátrányosan befolyásolhatják a rendszereket, és a rendszerek elérhetetlenné válását okozhatják. Csak a szükséges műveleteket hajtsa végre a eszkalációs időszak alatt.

Az AVS-portálon a vCenter SSO CloudOwner helyi felhasználójának [jogosultságait](escalate-private-cloud-privileges.md) is megadhatja. A távoli felhasználó jogosultságát csak akkor lehet megemelni, ha további Identitáskezelés van konfigurálva a vCenter-on. A jogosultságok eszkalációja magában foglalja a kiválasztott felhasználó hozzáadását a vSphere beépített rendszergazdák csoportjához. Csak egy felhasználó rendelkezhet megadható jogosultságokkal. Ha egy másik felhasználó jogosultságait kell megadnia, először az aktuális felhasználók jogosultságait kell kibővíteni.

A további személyazonossági forrásokból származó felhasználókat hozzá kell adni a CloudOwner csoport tagjaként.

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group*szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.
A eszkalációs időszak alatt az AVS a kapcsolódó riasztási értesítések automatikus figyelésével azonosítja a környezet véletlen módosításait.
