---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028050"
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

Ez a cikk az irányítás megvalósításához elvégzendő feladatokra összpontosít. A fogalmak részletesebb ismertetését [az Azure-beli irányítást](../articles/governance/index.yml) ismertető cikkben találja.