---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/28/2018
ms.author: raynew
ms.openlocfilehash: f7d6c3f68618fec839ccff06b73ba44d106999d2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38765538"
---
| Name (Név) | Kereskedelmi URL-címe | Kormányzati URL-címe | Leírás |
|---|---|---|---|
| Azure AD | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Hozzáférés-vezérléshez és identitáskezeléshez AAD használatával |
| Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz |
| Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz |
| Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez |
| Telemetriai adatok (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Telemetria használt |

``time.nist.gov`` és ``time.windows.com`` rendszer és a központi telepítések a globális idő közötti időszinkronizálás ellenőrzéséhez használt.


