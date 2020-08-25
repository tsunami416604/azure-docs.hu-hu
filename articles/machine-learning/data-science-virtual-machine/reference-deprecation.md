---
title: 'Hivatkozás: Data Science Virtual Machine rendszerkép-nyugdíjazások'
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science Virtual Machinet érintő nyugdíjazások részletei
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816320"
---
# <a name="reference-retirements-of-dsvm-images"></a>Hivatkozás: DSVM-lemezképek kivonása

Az alábbiakban megbeszéljük a rendszerképek kivonását (elavult), valamint az Azure-Data Science Virtual Machine közelgő nyugdíjazások kezelésére vonatkozó javaslatokat.

## <a name="why-we-retire-dsvm-images"></a>Miért érdemes kivonni a DSVM-lemezképeket

A Data Science Virtual Machine jelenleg két kiadással rendelkezik:

* Ubuntu
* Windows Server

A kiadások DSVM-képe egy adott operációsrendszer-verzióra épül, például Ubuntu 18,04 LTS. Idővel az operációs rendszer _verziójának_ karbantartási időszaka megszűnik, és/vagy az adatelemzési eszközök már nem támogatják a régebbi operációsrendszer-verziókat. Ahhoz, hogy a DSVM-lemezkép naprakész legyen a legújabb operációs rendszerekkel és adatelemzési eszközökkel, új DSVM-lemezképet szabadítunk fel az operációs rendszer újabb verziói alapján.

## <a name="how-we-retire-dsvm-images"></a>DSVM-rendszerképek kivonása

Kiállítunk egy _nyugdíjazási bejelentést_ , amelyben a meglévő DSVM-felhasználók értesítést kapnak (e-mailben) a közelgő DSVM-rendszerképek megszüntetéséről. A nyugdíjazási bejelentés részletesen ismerteti a _kivonulási dátumot_ (ezt követően a rendszerkép nem érhető el) és a kockázatcsökkentő javaslatokat (például egy újabb DSVM-rendszerképre való frissítést).

A _bejelentés_ napján elrejtjük a képet a piactéren, hogy:

1. a rendszer megakadályozza, hogy az új felhasználók akaratlanul kiépítsék a kivont DSVM-rendszerképet.
2. a meglévő felhasználók a kivonulási időpontig használhatják az ARM-telepítéseket.

A rejtett rendszerkép a _kivonulási dátumig_ az operációs rendszer javításait fogja kapni, de __nem__ kap frissítéseket az adatelemzési eszközökhöz és keretrendszerekhöz. A kivezetési _napon_a rendszerkép nem lesz elérhető az ARM-környezetekben.

Az előfizetésben található kiépített DSVM-rendszerkép továbbra is működni fog a lejárati dátum után. Javasoljuk azonban, hogy frissítsen a legújabb DSVM-lemezképre, hogy a legújabb operációs rendszerekkel és adatelemzési eszközökkel rendelkezzen.

## <a name="impact"></a>Hatás

Az előfizetésében meglévő DSVM kiépített lemezképek továbbra is működőképesek lesznek a lejárati dátum után. Azt javasoljuk azonban, hogy a felhasználók a Azure Portal vagy ARM sablonnal frissítse a DSVM-rendszerképét az újabb verzióra.

> [!WARNING]
> A Virtual Machine Scale Sets használatával kiépített DSVM-lemezképek nem lesznek felskálázásra a lejárati dátum után.
>
> Azok az ARM-sablonok, amelyek nem lettek frissítve az új DSVM-rendszerkép részleteivel, nem fognak települni a lejárati dátum után.

## <a name="mitigating-upcoming-retirements"></a>Közelgő nyugdíjazások enyhítése

Ebben a szakaszban a közelgő nyugdíjazások mérséklését tárgyaljuk.

### <a name="upgrade-windows-2016-dsvm"></a>A Windows 2016 DSVM frissítése

Ha a meglévő Windows 2016 DSVM adatlemezét Windows 2019 DSVM szeretné áttelepíteni, hajtsa végre a következő lépéseket:

1. Hozzon létre egy új Windows 2019 DSVM az [itt](./provision-vm.md#create-your-dsvm)látható utasításokat követve.
1. Leválaszthatja a meglévő adatlemezeket a Windows 2016-rendszerképből [ezen utasítások](../../virtual-machines/windows/detach-disk.md)alapján.
1. Csatlakoztassa a lemezt az előző lépésben a Windows 2019-lemezképhez az [alábbi utasítások](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)használatával.

### <a name="upgrade-ubuntu-1604-dsvm"></a>Ubuntu 16,04 DSVM frissítése

Javasoljuk, hogy a meglévő Ubuntu 16,04-Dsvm frissítse az [ubuntu 18,04 DSVM-kiadásra](./dsvm-ubuntu-intro.md).
