---
title: Megosztás-előfizetés visszavonása az Azure Data Share-ben
description: Ismerje meg, hogyan vonhatja vissza a megosztási előfizetést egy címzetttől az Azure Data Share használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476380"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Fogyasztói részvény-előfizetés visszavonása az Azure Data Share-ben

Ez a cikk bemutatja, hogyan vonhatja vissza a megosztási előfizetést egy vagy több fogyasztótól az Azure Data Share használatával. Ez megakadályozza, hogy a fogyasztó további pillanatképeket indítson. Ha az fogyasztó még nem indított el pillanatképet, soha nem kapja meg az adatokat, miután a megosztási előfizetés visszavonásra kerül. Ha korábban aktiváltak egy pillanatképet, a legfrissebb adatok a fiókjukban maradnak.

## <a name="navigate-to-a-sent-data-share"></a>Navigálás elküldött adatmegosztásra

Az Azure Data Share-ben keresse meg az elküldött megosztást, és válassza az **Előfizetések megosztása** lapot.

![Megosztási előfizetés visszavonása](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Jelölje be azokat a címzetteket, akiknek a megosztási előfizetéseit törölni szeretné, jelölje be a **Visszavonás gombra.** Az adatszolgáltatás a továbbiakban nem kap frissítéseket az adatairól.

## <a name="next-steps"></a>További lépések
További információ [az adatmegosztások figyeléséről.](how-to-monitor.md)