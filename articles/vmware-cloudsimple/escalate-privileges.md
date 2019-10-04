---
title: Azure VMware-megoldás CloudSimple szerint – CloudSimple jogosultságok kiemelése
description: Ismerteti, hogyan lehet megtekinteni a CloudSimple engedélyeket a rendszergazdai feladatok elvégzéséhez a saját Felhőbeli vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 524772578ad724e969bbeab0be0a3edcf32a845f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619611"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>CloudSimple-jogosultságok kiemelése rendszergazdai funkciók elvégzéséhez a saját Felhőbeli vCenter

A CloudSimple-jogosultságok megközelítése úgy lett kialakítva, hogy a vCenter-felhasználók számára a normál műveletek elvégzéséhez szükséges jogosultságokat adja. Bizonyos esetekben előfordulhat, hogy a felhasználók további jogosultságokat igényelnek egy adott feladat elvégzéséhez.  Egy vCenter SSO-felhasználó jogosultságait korlátozott időtartamra is kiterjesztheti.

A jogosultságok kiterjesztésének okai az alábbiak lehetnek:

* Az Identity sources konfigurálása
* Felhasználókezelés
* Elosztott port csoport törlése
* VCenter-megoldások (például biztonsági mentési alkalmazások) telepítése
* Szolgáltatásfiókok létrehozása

> [!WARNING]
> Az emelt szintű jogosultságokkal rendelkező államban végrehajtott műveletek hátrányosan befolyásolhatják a rendszereket, és a rendszerek elérhetetlenné válását okozhatják. Csak a szükséges műveleteket hajtsa végre a eszkalációs időszak alatt.

A CloudSimple-portálon [](escalate-private-cloud-privileges.md) a CloudOwner helyi felhasználó jogosultságait a vCenter SSO-ra kell kibővíteni.  A távoli felhasználó jogosultságát csak akkor lehet megemelni, ha további Identitáskezelés van konfigurálva a vCenter-on.  A jogosultságok eszkalációja magában foglalja a kiválasztott felhasználó hozzáadását a vSphere beépített rendszergazdák csoportjához.  Csak egy felhasználó rendelkezhet megadható jogosultságokkal.  Ha egy másik felhasználó jogosultságait kell megadnia, először az aktuális felhasználók jogosultságait kell kibővíteni.

A további személyazonossági forrásokból származó felhasználókat hozzá kell adni a CloudOwner csoport tagjaként.

A eszkalációs időszak alatt a CloudSimple az automatizált figyelést használja a kapcsolódó riasztási értesítésekkel, hogy azonosítsa a környezet véletlen módosításait.
