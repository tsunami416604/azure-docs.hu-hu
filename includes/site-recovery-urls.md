---
title: fájlbefoglalás
description: fájlbefoglalás
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179644"
---
Name | Kereskedelmi URL-cím | Kormányzati URL-cím | Description
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | A hozzáférés-vezérléshez és az identitáskezeléshoz használatos a Azure Active Directory használatával. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz.
Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz.
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez.
Telemetria (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | A telemetria esetében használatos.
Időszinkronizálás | ``time.windows.com`` | ``time.nist.gov`` | A rendszer és a globális idő közötti időszinkronizálást vizsgálja az összes üzemelő példányban.


