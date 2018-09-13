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
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723478"
---
**Name (Név)** | **Kereskedelmi URL-címe**  | **Kormányzati URL-címe** | **Leírás** |
--- | --- | --- | ---
Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Hozzáférés-vezérléshez és identitáskezeléshez AAD használatával 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz 
Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz 
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez 
Telemetriai adatok (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Telemetria használt 
Időszinkronizálás | ``time.windows.com`` | ``time.nist.gov`` | Ssed rendszer és a központi telepítések a globális idő közötti időszinkronizálás ellenőrzéséhez.


