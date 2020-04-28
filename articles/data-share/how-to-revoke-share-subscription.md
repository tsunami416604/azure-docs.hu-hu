---
title: Megosztási előfizetés visszavonása az Azure-beli adatmegosztásban
description: Megtudhatja, hogyan vonhatja vissza a megosztási előfizetést egy címzetttől az Azure-adatmegosztás használatával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73476380"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Fogyasztó megosztás-előfizetésének visszavonása az Azure-beli adatmegosztásban

Ez a cikk azt ismerteti, hogyan vonhatja vissza egy vagy több felhasználó megosztási előfizetését az Azure-adatmegosztás használatával. Ez megakadályozza, hogy a fogyasztó további pillanatképeket indítson. Ha a fogyasztó még nem váltott ki pillanatképet, a megosztás-előfizetés visszavonása után soha nem kapják meg az adatgyűjtést. Ha korábban már elindítottak egy pillanatképet, a legfrissebb adatok a fiókjában maradnak.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-adatmegosztás területen navigáljon az elküldett megosztáshoz, és válassza az **előfizetés megosztása** lapot.

![Megosztás-előfizetés visszavonása](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Jelölje be azon címzettek jelölőnégyzetét, akiknek a megosztási előfizetéseit törölni szeretné, majd kattintson a **Visszavonás**gombra. A fogyasztó már nem kap frissítéseket az adatkezeléshez.

## <a name="next-steps"></a>További lépések
További információ az [adatmegosztások figyeléséről](how-to-monitor.md).