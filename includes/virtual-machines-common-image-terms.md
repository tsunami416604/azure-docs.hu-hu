---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743367"
---
## <a name="terminology"></a>Terminológia

A Piactéri lemezkép az Azure-ban a következő attribútumokkal rendelkezik:

* **A Publisher** -szervezet, amely a lemezkép létrehozása. Példák: Kanonikus, MicrosoftWindowsServer
* **Ajánlat** -közzétevő által létrehozott kapcsolódó képek csoport nevét. Példák: Ubuntu Server WindowsServer
* **SKU** - ajánlatot, például egy terjesztési jelentős kiadása példányát. Példák: 16.04-es lts verzió, 2016-Datacenter
* **Verzió** -SKU lemezkép verziószámát. 

Programozott módon telepít egy virtuális gép egy Piactéri lemezképhez kikereséséhez ezeket az értékeket, külön-külön paraméterekként megadni, vagy bizonyos eszközök fogadja el a kép *URN*. A URN összevonja ezeket az értékeket, a kettőspont (:) elválasztva: *Publisher*:*kínálnak*:*Sku*:*verzió*. A URN lecserélheti a verziószámot a "legújabb", amely kiválasztja, hogy a lemezkép legújabb verziója. 

Ha a kép közzétevőjének további licenc és vásárlási feltételeket biztosít, fogadniuk a feltételeket és engedélyezni kell a programozott telepítés. Is kell megadnia *csomag megvásárlásától* paraméterek programozott módon a virtuális gépek telepítésekor. Lásd: [piactér feltételeket a lemezkép telepítése](#deploy-an-image-with-marketplace-terms).