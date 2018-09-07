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
ms.openlocfilehash: d0d72a9ef592579b21d082111ab8ad00f8995de2
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028431"
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