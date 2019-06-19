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
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179644"
---
Name (Név) | Kereskedelmi URL-címe | Kormányzati URL-címe | Leírás
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Hozzáférés-vezérléshez és identitáskezeléshez kezelés használhatja az Azure Active Directoryval. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Replikációs adatátvitelhez és összehangoláshoz.
Replikáció | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Replikációkezelési műveletekhez és összehangoláshoz.
Storage | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | A replikált adatokat tároló tárfiókhoz való hozzáféréshez.
Telemetriai adatok (nem kötelező) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Telemetriai adatokat használja.
Időszinkronizálás | ``time.windows.com`` | ``time.nist.gov`` | Használja a rendszer és a központi telepítések a globális idő közötti időszinkronizálás ellenőrzéséhez.


