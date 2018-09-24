---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: eadff6f61bd9e5979ae835230de53562ff8542d1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003901"
---
### <a name="virtual-machines"></a>Virtuális gépek

|  |  |
|---------|---------|
| [Egyéni VM-lemezképek engedélyezése egy adott erőforráscsoportból](../articles/governance/policy/samples/allow-custom-vm-image.md) |  Ehhez az egyéni rendszerképeknek jóváhagyott erőforráscsoportból kell származniuk. Meg kell adnia a jóváhagyott erőforráscsoport nevét. |
| [Tárfiókok és virtuális gépek engedélyezett termékváltozatai](../articles/governance/policy/samples/allowed-skus-storage.md) | Ehhez a tárfiókoknak és a virtuális gépeknek engedélyezett termékváltozatokat kell használniuk. Beépített szabályzatok használatával biztosítja az engedélyezett termékváltozatok alkalmazását. Megadhatja az engedélyezett virtuálisgép-termékváltozatok és az engedélyezett tárfiók-termékváltozatok tömbjét. |
| [Jóváhagyott VM-lemezképek](../articles/governance/policy/samples/allowed-custom-images.md) | Ehhez csak jóváhagyott egyéni rendszerképek helyezhetők üzembe a környezetben. Meg kell adnia egy jóváhagyott lemezazonosítókat tartalmazó tömböt. |
| [Naplózás hiányzó bővítmény esetén](../articles/governance/policy/samples/audit-ext-not-exist.md) | Naplózza, ha egy bővítmény nincs telepítve egy virtuális gépen. Meg kell adnia a bővítmény kiadóját és típusát a bővítmény telepítési állapotának ellenőrzéséhez. |
| [Nem engedélyezett virtuálisgép-bővítmények](../articles/governance/policy/samples/not-allowed-vm-ext.md) | Megtiltja a megadott bővítmények használatát. Meg kell adnia egy tiltott bővítménytípusokat tartalmazó tömböt. |