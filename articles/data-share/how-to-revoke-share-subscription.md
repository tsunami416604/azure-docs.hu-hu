---
title: Megosztási előfizetés visszavonása az Azure-beli adatmegosztás előzetes verziójában
description: Megtudhatja, hogyan vonhatja vissza a megosztási előfizetést egy címzetttől az Azure-beli adatmegosztás előzetes verziójával.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8ef23ead500acb9e403b38fd52a2d980d12bf4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169051"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Fogyasztó megosztás-előfizetésének visszavonása az Azure-beli adatmegosztás előzetes verziójában

Ez a cikk azt ismerteti, hogyan vonhatja vissza egy vagy több felhasználó megosztási előfizetését az Azure-beli adatmegosztás előzetes verziójával. Ez megakadályozza, hogy a fogyasztó további pillanatképeket indítson. Ha a fogyasztó még nem váltott ki pillanatképet, a megosztás-előfizetés visszavonása után soha nem kapják meg az adatgyűjtést. Ha korábban már elindítottak egy pillanatképet, a legfrissebb adatok a fiókjában maradnak.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-beli adatmegosztás előzetes verziójában navigáljon az elküldett megosztáshoz, és válassza az **előfizetés megosztása** lapot.

![Megosztás-előfizetés visszavonása](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Jelölje be azon címzettek jelölőnégyzetét, akiknek a megosztási előfizetéseit törölni szeretné, majd kattintson a **Visszavonás**gombra. A fogyasztó már nem kap frissítéseket az adatkezeléshez.

## <a name="next-steps"></a>Következő lépések
További információ az [adatmegosztások figyeléséről](how-to-monitor.md).