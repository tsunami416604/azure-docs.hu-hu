---
title: VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery a vészhelyreállítás során a konfigurációs kiszolgáló hibáinak elhárítása |} A Microsoft Docs
description: Ez a cikk a konfigurációs kiszolgáló VMware virtuális gépek vészhelyreállítása és a fizikai kiszolgálók üzembe helyezése az Azure-bA az Azure Site Recovery hibaelhárítási információkat szolgáltat.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: ramamill
ms.openlocfilehash: b819783d127c51c0d5f33b2273a37a4180cb13a6
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571571"
---
# <a name="troubleshoot-configuration-server-issues"></a>Konfigurációs kiszolgáló hibáinak elhárítása

Ez a cikk segít a problémák, amikor üzembe helyezése és kezelése a [Azure Site Recovery](site-recovery-overview.md) konfigurációs kiszolgáló. A konfigurációs kiszolgáló akkor használatos, ha a vészhelyreállítás beállítása helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA a Site Recovery használatával. 

## <a name="installation-failures"></a>Telepítési hibák

| **Hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|HIBA: a fiókok betöltése nem sikerült. Hiba: System.IO.IOException: A CS-kiszolgáló telepítésekor és regisztrálásakor nem sikerült beolvasni az átviteli kapcsolatból származó adatokat.| Ellenőrizze, hogy a TLS 1.0 engedélyezve van-e számítógépen. |

## <a name="registration-failures"></a>Regisztrációs hibák

Regisztrációs hibák a l is meg kell hibakeresése < br/ogs %ProgramData%\ASRLogs mappában.

A regisztrációs hibákat a **%ProgramData%\ASRLogs** mappa naplóinak áttekintésével lehet kikeresni.

| **Hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Üzenet: ACS50008: Az SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelációs azonosító: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Időbélyegző: **2016-12-12 14:50:08Z<br>** | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|**09:35:27** :DRRegistrationException kivétel történt a kiválasztott tanúsítvány vészhelyreállítási tárolóinak lekérése során: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: Az SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelációs azonosító: abe9deb8-3e64-464d-8375-36db9816427a<br>Időbélyegző: **2016-05-19 01:35:39Z**<br> | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|06:28:45:Nem sikerült a tanúsítvány előállítása<br>06:28:45:A telepítést nem lehet folytatni. A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Telepítés ismételt futtatása | A telepítés futtatásához helyi rendszergazdaként jelentkezzen be. |
