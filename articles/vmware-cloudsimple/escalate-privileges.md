---
title: Azure VMware-megoldás a CloudSimple által – A CloudSimple jogosultságainak eszkalálódása
description: Ez a témakör azt ismerteti, hogy miként lehet elévülni a CloudSimple engedélyeit a felügyeleti funkciók végrehajtásához a Private Cloud vCenter ben
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025333"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>A CloudSimple jogosultságainak eszkalálása a felügyeleti funkciók végrehajtásához a Private Cloud vCenter ben

A CloudSimple jogosultságok megközelítés célja, hogy a vCenter-felhasználók számára a szokásos műveletek végrehajtásához szükséges jogosultságokat. Bizonyos esetekben a felhasználónak további jogosultságokra lehet szüksége egy adott feladat végrehajtásához.  A vCenter-sSO-felhasználók jogosultságait korlátozott ideig bővítheti.

A jogosultságok eszkalálódásának okai a következők lehetnek:

* Az identitásforrások konfigurációja
* Felhasználókezelés
* Elosztott portcsoport törlése
* VCenter-megoldások (például biztonsági mentési alkalmazások) telepítése
* Szolgáltatásfiókok létrehozása

> [!WARNING]
> Az eszkalált privilegizált állapotban végrehajtott műveletek hátrányosan befolyásolhatják a rendszert, és a rendszer elérhetetlenné válását eredményezhetik. Csak a szükséges műveleteket hajtsa végre az eszkalációs időszakban.

A CloudSimple portálon a cloudowner a cloudowner helyi felhasználó jogosultságait a vCenter SSO.From the CloudSimple portal, [escalate privileges](escalate-private-cloud-privileges.md) for the CloudOwner local user on the vCenter SSO.  A távoli felhasználók jogosultságát csak akkor fokozhatja, ha a vCenteren további identitásszolgáltató van konfigurálva.  A jogosultságok eszkalációja magában foglalja a kiválasztott felhasználó hozzáadását a vSphere beépített Rendszergazdák csoporthoz.  Csak egy felhasználó rendelkezhet eszkalált jogosultságokkal.  Ha egy másik felhasználó jogosultságait kell elerőlenie, először gyengítse az aktuális felhasználók jogosultságait.

A további identitásforrásokból származó felhasználókat hozzá kell adni a CloudOwner csoport tagjaiként.

> [!CAUTION]
> Új felhasználókat csak a *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* vagy *cloud-global-vm-admin-group csoporthoz*kell hozzáadni.  A *Rendszergazdák* csoportba hozzáadott felhasználók automatikusan törlődnek.  Csak a szolgáltatásfiókokat kell hozzáadni *a Rendszergazdák* csoporthoz, és a szolgáltatásfiókok nem használhatók a vSphere webes felhasználói felületére való bejelentkezéshez.

Az eszkalációs időszak során a CloudSimple automatikus figyelést használ a kapcsolódó riasztási értesítésekkel a környezet véletlen változásainak azonosítására.
