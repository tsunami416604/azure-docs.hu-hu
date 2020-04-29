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
ms.openlocfilehash: d1e7fa1ed1649508f0d4923db8817d17ad556ca1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67179049"
---
Amikor erőforrásokat helyez üzembe az Azure-ban, elképesztően rugalmasan dönthet arról, milyen típusú erőforrásokat szeretne üzembe helyezni, hol legyenek és hogyan állítja be őket. Előfordulhat azonban, hogy ez a rugalmasság több lehetőséget teremt, mint amennyit a szervezeten belül engedélyezni szeretne. Amikor erőforrások Azure-ban való üzembe helyezését mérlegeli, esetleg felmerülhetnek az alábbi kérdések:

* Hogyan megfelel az egyes országokban/régiókban tárolt adatszuverenitásra vonatkozó jogi követelményeknek?
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
