---
title: Az SAP HANA méretezése az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Az SAP HANA méretezése az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616908"
---
# <a name="sizing"></a>Méretezés

A HANA nagy példány méretezése nem különbözik a HANA általában méretezéséből. A meglévő és üzembe helyezett rendszerek, amelyek szeretné áthelyezni a többi RDBMS hana, az SAP számos jelentést biztosít, amelyek a meglévő SAP-rendszereken futó. Ha az adatbázis hana-ra kerül, ezek a jelentések ellenőrzik az adatokat, és kiszámítják a HANA-példány memóriaigényét. A jelentések futtatásáról és a legújabb javítások ról vagy verzióikról a következő SAP-megjegyzésekben olvashat bővebben:

- [SAP Note #1793345 - Az SAP Suite méretezése a HANA-n](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite a HANA és S/4 HANA méretezési jelentés](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - GYAKORI KÉRDÉSEK: SAP BW a HANA méretezési jelentéséről](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Megjegyzés #1736976 - A BW méretezési jelentése a HANA-ra](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Új méretezési jelentés a BW számára a HANA-n](https://launchpad.support.sap.com/#/notes/2296290)

A zöldmezős implementációk, SAP Quick Sizer áll rendelkezésre az SAP-szoftverek hana-n alapuló sap-szoftverek megvalósításának memóriakövetelményeinek kiszámításához.

A HANA memóriakövetelményei az adatmennyiség növekedésével növekszik. Legyen tudatában a jelenlegi memóriafelhasználás, hogy segítsen megjósolni, mi lesz a jövőben. A memóriaigények alapján ezután leképezheti az igényét a HANA nagy példánysek egyikéhez.

**További lépések**
- Tekintse meg [a bevezetési követelményeket](hana-onboarding-requirements.md)