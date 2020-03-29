---
title: Gyakori automatikus méretezési minták áttekintése
description: Ismerje meg a közös minták automatikus méretezése az erőforrás az Azure-ban.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396094"
---
# <a name="overview-of-common-autoscale-patterns"></a>Gyakori automatikus méretezési minták áttekintése
Ez a cikk ismerteti az azure-beli erőforrás méretezéséhez használt közös mintákat.

Az Azure Monitor automatikus skálázása csak [a virtuális gép méretezési készleteire,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az appszolgáltatásra – webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/)és [az API-kezelési szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="lets-get-started"></a>Lehetővé teszi, hogy az induláshoz

Ez a cikk feltételezi, hogy ismeri az automatikus skálázást. Itt [kezdheti el az erőforrás méretezését.][1] Az alábbiakban néhány közös méretezési minták.

## <a name="scale-based-on-cpu"></a>Méretezés a CPU alapján

Van egy webalkalmazás (/VMSS/cloud service szerepkör) és

- A processzor alapján szeretné bővíteni/skálázni.
- Emellett azt szeretné, hogy a példányok minimális száma is legyen.
- Azt is biztosítani szeretné, hogy a méretezhető példányok maximális számát állítsa be.

![Méretezés a CPU alapján][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>A hétköznapokon és a hétvégéken eltérő méretezés

Van egy webalkalmazás (/VMSS/cloud service szerepkör) és

- Alapértelmezés szerint 3 példányt szeretne (hétköznap)
- Nem számít a forgalom a hétvégén, és ezért azt szeretné, hogy skála le 1 esetben hétvégén.

![A hétköznapokon és a hétvégéken eltérő méretezés][3]

## <a name="scale-differently-during-holidays"></a>Másképpen méretezhető ünnepnapokon

Van egy webalkalmazás (/VMSS/cloud service szerepkör) és

- Alapértelmezés szerint a PROCESSZOR-használat alapján fel-le szeretne skálázni
- Az ünnepi időszakban (vagy a vállalkozás számára fontos adott napokon) azonban felül szeretné bírni az alapértelmezett értékeket, és több kapacitással kíván rendelkezni.

![Eltérő méretezés ünnepnapokon][4]

## <a name="scale-based-on-custom-metric"></a>Méretezés egyéni mérőszám alapján

Van egy webes előtér-és egy API-réteg, amely kommunikál a háttér.

- Az API-réteget az előtérben lévő egyéni események alapján szeretné méretezni (például: A pénztári folyamatot a bevásárlókosárban lévő cikkek száma alapján szeretné méretezni)

![Méretezés egyéni mérőszám alapján][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

