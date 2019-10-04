---
title: Megosztási előfizetés visszavonása az Azure-beli adatmegosztás előzetes verziójában
description: Megosztási előfizetés visszavonása
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 08a48202c26df1c24216572b1a52ac45506c6229
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326538"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Fogyasztó megosztás-előfizetésének visszavonása az Azure-beli adatmegosztás előzetes verziójában

Ez a cikk azt ismerteti, hogyan vonhatja vissza egy vagy több felhasználó megosztási előfizetését az Azure-beli adatmegosztás előzetes verziójával. Ez megakadályozza, hogy a fogyasztó további pillanatképeket indítson. Ha a fogyasztó még nem váltott ki pillanatképet, a megosztás-előfizetés visszavonása után soha nem kapják meg az adatgyűjtést. Ha korábban már elindítottak egy pillanatképet, a legfrissebb adatok a fiókjában maradnak.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-beli adatmegosztás előzetes verziójában navigáljon az elküldett megosztáshoz, és válassza az **előfizetés megosztása** lapot.

![Megosztás-előfizetés visszavonása](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Jelölje be azon címzettek jelölőnégyzetét, akiknek a megosztási előfizetéseit törölni szeretné, majd kattintson a **Visszavonás**gombra. A fogyasztó már nem kap frissítéseket az adatkezeléshez.

## <a name="next-steps"></a>További lépések
További információ az adatmegosztások [figyeléséről](how-to-monitor.md).