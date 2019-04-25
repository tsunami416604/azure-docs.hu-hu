---
title: SAP Hana az Azure-ban (nagyméretű példányok) méretezése |} A Microsoft Docs
description: Méretezés az SAP Hana az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7d403c284eefc1351aabb632977832f2bf11757a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477122"
---
# <a name="sizing"></a>Méretezés

Nagyméretű HANA-példányt a méretezési semmiben nem különbözik Hana általános méretezése. A meglévő és üzembe helyezte a rendszereket, amelyeket meg szeretne áthelyezése más relációsadatbázis-kezelő rendszer HANA, SAP Itt számos olyan jelentést, amelyek a meglévő SAP-rendszerek futnak. Ha az adatbázis Hana-hoz, ezek a jelentések ellenőrizze az adatokat, és a HANA-példány memóriakövetelményei kiszámítása. Ezek a jelentések futtatása, és szerezze be a legújabb javítások vagy verziók további információkért olvassa el az alábbi SAP-megjegyzések:

- [SAP-Jegyzetnek #1793345 – SAP Suite on HANA méretezése](https://launchpad.support.sap.com/#/notes/1793345)
- [Megjegyzés: #1872170 - Suite on HANA, és S/4 HANA méretezési jelentés SAP](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-Jegyzetnek #2121330 – gyakori kérdések: SAP BW on HANA jelentés méretezése](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP BW on HANA méretezési jelentés #1736976 - Megjegyzés](https://launchpad.support.sap.com/#/notes/1736976)
- [Megjegyzés: #2296290 – új méretezési a jelentés az SAP BW on HANA SAP](https://launchpad.support.sap.com/#/notes/2296290)

Zöldmezős megvalósításokhoz, a gyors Szimbólumméretező SAP HANA épülő SAP-szoftverek végrehajtásának memóriaigényének kiszámításához érhető el.

HANA memóriakövetelményei növelje az adatmennyiség növekedésével. Vegye figyelembe az aktuális memóriafogyasztás segít előre jelezni, mi legyen a jövőben. A memória-követelmények alapján, majd leképezheti az igény szerinti a HANA nagyméretű példány termékváltozatok egyikébe.

**Következő lépések**
- Tekintse meg [bevezetési követelmények](hana-onboarding-requirements.md)