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
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001620"
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

