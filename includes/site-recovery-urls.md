---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: ae8eebf9667f2bc03fdc1082fb38c19c5c645c10
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47060691"
---
**Name (Név)** | **Kereskedelmi URL-címe**  | **Kormányzati URL-címe** | **Leírás** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Hozzáférés-vezérléshez és identitáskezeléshez AAD használatával 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz 
Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz 
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez 
Telemetriai adatok (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Telemetria használt 
Időszinkronizálás | ``time.windows.com`` | ``time.nist.gov`` | Ssed rendszer és a központi telepítések a globális idő közötti időszinkronizálás ellenőrzéséhez.


