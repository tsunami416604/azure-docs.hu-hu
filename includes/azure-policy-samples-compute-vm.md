---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664526"
---
### <a name="virtual-machines"></a>Virtuális gépek

|  |  |
|---------|---------|
| [Egyéni VM-lemezképek engedélyezése egy adott erőforráscsoportból](../articles/azure-policy/scripts/allow-custom-vm-image.md) |  Ehhez az egyéni rendszerképeknek jóváhagyott erőforráscsoportból kell származniuk. Meg kell adnia a jóváhagyott erőforráscsoport nevét. |
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](../articles/azure-policy/scripts/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [Jóváhagyott VM-lemezképek](../articles/azure-policy/scripts/allowed-custom-images.md) | Ehhez csak jóváhagyott egyéni rendszerképek helyezhetők üzembe a környezetben. Meg kell adnia egy jóváhagyott lemezazonosítókat tartalmazó tömböt. |
| [Naplózás hiányzó bővítmény esetén](../articles/azure-policy/scripts/audit-ext-not-exist.md) | Naplózza, ha egy bővítmény nincs telepítve egy virtuális gépen. Meg kell adnia a bővítmény kiadóját és típusát a bővítmény telepítési állapotának ellenőrzéséhez. |
| [Nem engedélyezett virtuálisgép-bővítmények](../articles/azure-policy/scripts/not-allowed-vm-ext.md) | Megtiltja a megadott bővítmények használatát. Meg kell adnia egy tiltott bővítménytípusokat tartalmazó tömböt. |