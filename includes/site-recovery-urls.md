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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999185"
---
Név | Kereskedelmi URL-cím | Kormányzati URL-cím | Leírás
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | A hozzáférés-vezérléshez és az identitáskezeléshoz használatos a Azure Active Directory használatával. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz.
Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz.
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez.
Telemetria (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | A telemetria esetében használatos.
Időszinkronizálás | ``time.windows.com`` | ``time.nist.gov`` | A rendszer és a globális idő közötti időszinkronizálást vizsgálja az összes üzemelő példányban.


