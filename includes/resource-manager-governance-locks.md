---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a69a739f36854cacd6b361ca2bd17d904e9c4c96
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38753676"
---
Erőforrás-zárolások letiltja a véletlen törlését vagy módosítását kiemelt fontosságú erőforrásokat a szervezetben. Az erőforrás-zárolások a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaznak. 

A zárolási szintet **CanNotDelete** (nem törölhető) vagy **ReadOnly** (csak olvasható) értékre állíthatja be. A portálon, a zárolás szintek jelennek meg, **törlése** és **csak olvasható** jelölik.

* **Védve** azt jelenti, hogy a jogosult felhasználók továbbra is olvasni és módosítani az erőforrást, de azokat nem lehet törölni az erőforrást. 
* **Csak olvasható** azt jelenti, hogy a jogosult felhasználók olvashatják egy erőforrást, de nem lehet törölni vagy az erőforrás frissítése. A zárolás alkalmazása hasonlít az összes jogosult felhasználó által adott engedélyek korlátozása a **olvasó** szerepkör. 

> [!TIP]
> Legyen óvatos, ha alkalmazása egy **ReadOnly** zárolás. Bizonyos műveletek, amelyek úgy tűnik, például olvasási műveletekhez ténylegesen szükséges további műveleteket. Ha például egy **ReadOnly** a storage-fiók zárolása megakadályozza, hogy minden felhasználó a kulcsok listázása. A lista kulcsok művelet POST-kérés történik, mert a visszaadott kulcsok érhetők el írási műveletek. A **ReadOnly** egy App Service erőforrás zárolása megakadályozza, hogy a Visual Studio Server Explorer fájl az erőforrás jelenik meg, mert a kapcsolati írási hozzáférésre van szüksége.

Amikor alkalmazza a zárolást, egy szülő hatókörben, a hatókörön belüli összes erőforrás azonos zárolási öröklik. Hozzáadását a későbbiekben még akkor is, erőforrások a zárolási öröklik a szülő. A legszigorúbb zárolást az öröklési ciklus élvez elsőbbséget.

Erőforrás-kezelő zárolások csak vonatkozik, amelyek a felügyeleti sík, olyan küldött műveleteket tartalmaz, amely egy operations `https://management.azure.com`. A zárolás nem korlátozza a hogyan feldolgozni az erőforrásokat a saját funkciók. Erőforrás-módosítások korlátozva, de az erőforrás-műveletek nem korlátozott. Például egy SQL-adatbázis írásvédett zárolásának megakadályozza, hogy Ön általi törlését vagy módosítását az adatbázis. Ez nem akadályozza meg a létrehozása, frissítése vagy törlése az adatbázis adatait. Adatok tranzakciók nem engedélyezettek, mert a nem kap meg ezek a műveletek `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Akik hozhatók létre, vagy a szervezet zárolások törlése
A felügyeleti zárolások létrehozásához vagy törléséhez hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/locks/*` műveletekhez. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.
