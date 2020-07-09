---
title: Gyakori automatikus méretezési minták áttekintése
description: Ismerje meg az Azure-beli erőforrás automatikus méretezésének leggyakoribb mintáit.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75396094"
---
# <a name="overview-of-common-autoscale-patterns"></a>Gyakori automatikus méretezési minták áttekintése
Ez a cikk az Azure-beli erőforrások méretezésének általános mintáit ismerteti.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="lets-get-started"></a>Lehetővé teszi az első lépéseket

Ez a cikk azt feltételezi, hogy már ismeri az automatikus skálázást. [Itt megkezdheti az erőforrás skálázását][1]. Az alábbiakban néhány gyakori méretezési minta látható.

## <a name="scale-based-on-cpu"></a>Skálázás CPU alapján

Van egy webalkalmazása (/VMSS/cloud szolgáltatási szerepkör) és

- A PROCESSZORon alapuló méretezést vagy méretezést szeretné elvégezni.
- Emellett biztosítani szeretné, hogy a példányok minimális száma legyen.
- Továbbá győződjön meg arról, hogy maximális korlátot adott meg a méretezhető példányok számára.

![Skálázás CPU alapján][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Eltérő méretezés hétköznapokon és hétvégeken

Van egy webalkalmazása (/VMSS/cloud szolgáltatási szerepkör) és

- Alapértelmezés szerint 3 példányt kíván használni (hétköznapokon)
- Nem várható, hogy a forgalom a hétvégén történik, és így hétvégeken 1 példányra szeretné méretezni.

![Eltérő méretezés hétköznapokon és hétvégeken][3]

## <a name="scale-differently-during-holidays"></a>Eltérő skálázás a munkaszüneti napokon

Van egy webalkalmazása (/VMSS/cloud szolgáltatási szerepkör) és

- Alapértelmezés szerint a CPU-használat alapján szeretné felskálázást végezni
- Azonban az ünnepi időszakban (vagy a vállalat számára fontos meghatározott napokon) felül szeretné bírálni az alapértékeket, és nagyobb kapacitással kell rendelkeznie az Ön rendelkezésére.

![Eltérő méretezés a munkaszüneti napokon][4]

## <a name="scale-based-on-custom-metric"></a>Méretezés egyéni metrika alapján

Van egy webes kezelőfelülete és egy API-szintje, amely kommunikál a háttérrel.

- Az API-szintet az előtérben lévő egyéni események alapján szeretné méretezni (például: szeretné méretezni a fizetési folyamatot a bevásárlókocsi elemeinek száma alapján)

![Méretezés egyéni metrika alapján][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

