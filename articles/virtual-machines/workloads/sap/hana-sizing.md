---
title: SAP HANA méretezése az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: SAP HANA méretezése az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1750f1d61028cb186b02251b551b7a798e1df9d6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967533"
---
# <a name="sizing"></a>Méretezés

A HANA nagyméretű példány méretezése nem különbözik a HANA általános méretével. A meglévő és központilag telepített rendszerek esetében, amelyeket más RDBMS kíván áthelyezni a HANA-be, az SAP számos jelentést biztosít, amelyek a meglévő SAP-rendszereken futnak. Ha az adatbázis a HANA-ba kerül, ezek a jelentések a HANA-példányra vonatkozó adatmennyiséget és a memória követelményeit számítják ki. A jelentések futtatásával és a legújabb javítások vagy verziók beszerzésével kapcsolatos további információkért olvassa el a következő SAP-megjegyzéseket:

- [SAP-Megjegyzés #1793345 – az SAP Suite méretezése a HANA-on](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-Megjegyzés #1872170-Suite on HANA és S/4 HANA méretezési jelentés](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-Megjegyzés #2121330 – gyakori kérdések: SAP BW a HANA-méretezési jelentésben](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-Megjegyzés #1736976 – jelentés méretezése a BW on HANA-ben](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-Megjegyzés #2296290 – a BW-hez készült új méretezési jelentés a HANA-on](https://launchpad.support.sap.com/#/notes/2296290)

A zöld mezők megvalósításához az SAP Quick Sizeer elérhetővé válik a HANA-on futó SAP-szoftverek megvalósításához szükséges memória-követelmények kiszámításához.

A HANA adatmennyiség növekedésével kapcsolatos memória-követelmények Vegye figyelembe, hogy a jelenlegi memóriahasználat alapján várhatóan megjósolhatja, mi lesz a jövőben. A memóriára vonatkozó követelmények alapján leképezheti az igényét az egyik HANA nagyméretű példány SKU-ra.

**Következő lépések**
- A bevezetési [követelmények](hana-onboarding-requirements.md) átirányítása