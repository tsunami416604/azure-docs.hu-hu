---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0b3af1bd7b3ab48074432d9e39552d72c6664b98
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124731"
---
Amikor erőforrásokat helyez üzembe az Azure-ban, elképesztően rugalmasan dönthet arról, milyen típusú erőforrásokat szeretne üzembe helyezni, hol legyenek és hogyan állítja be őket. Előfordulhat azonban, hogy ez a rugalmasság több lehetőséget teremt, mint amennyit a szervezeten belül engedélyezni szeretne. Amikor erőforrások Azure-ban való üzembe helyezését mérlegeli, esetleg felmerülhetnek az alábbi kérdések:

* Hogyan felelhetek meg az egyes országokban az adatok elkülönítésére vonatkozó jogi követelményeknek?
* Hogyan szabályozhatom a költségeket?
* Hogyan biztosíthatom, hogy senki ne módosítson akarata ellenére egy kritikus rendszert?
* Hogyan követhetem nyomon és számlázhatom pontosan a költségeket?

A cikk ezekkel a kérdésekkel foglalkozik. Konkrétan a következőket fogja tenni:

> [!div class="checklist"]
> * Hozzárendel felhasználókat szerepkörökhöz, és hozzárendeli a szerepköröket egy hatókörhöz, hogy a felhasználók engedélyt kapjanak az elvárt műveletek elvégzésére, de másra ne.
> * Az előfizetésében lévő erőforrásokra konvenciókat előíró szabályzatokat alkalmaz.
> * Zárolja a rendszer szempontjából kritikus fontosságú erőforrásokat.
> * Felcímkézi az erőforrásokat, hogy nyomon követhesse őket a szervezet számára jelentéssel bíró értékek szerint.

Ez a cikk az irányítás megvalósításához elvégzendő feladatokra összpontosít. A fogalmak részletesebb ismertetését [az Azure-beli irányítást](../articles/security/governance-in-azure.md) ismertető cikkben találja. 
