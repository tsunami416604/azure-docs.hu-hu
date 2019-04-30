---
author: rockboyfor
ms.service: site-recovery
ms.topic: include
origin.date: 10/26/2018
ms.date: 12/10/2018
ms.author: v-yeche
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119745"
---
### <a name="installation-failures"></a>Telepítési hibák
| **Például szolgáló hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|HIBA: a fiókok betöltése nem sikerült. Hiba: System.IO.IOException: Nem sikerült adatokat olvasni az átviteli kapcsolatból telepítésekor és regisztrálásakor a CS-kiszolgáló.| Ellenőrizze, hogy a TLS 1.0 engedélyezve van-e számítógépen. |

### <a name="registration-failures"></a>Regisztrációs hibák
A regisztrációs hibákat a **%ProgramData%\ASRLogs** mappa naplóinak áttekintésével lehet kikeresni.

| **Például szolgáló hibaüzenet** | **Javasolt művelet** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Üzenet: ACS50008: SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelációazonosító: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Időbélyeg: **2016-12-12 14:50:08Z<br>** | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|**09:35:27** : DRRegistrationException minden vész-helyreállítási tár tartalmának beolvasása a kiválasztott tanúsítvány tett kísérlet során:: Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML-jogkivonat érvénytelen.<br>Nyomkövetési azonosító: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelációs azonosító: abe9deb8-3e64-464d-8375-36db9816427a<br>Időbélyeg: **2016-05-19 01:35:39Z**<br> | Győződjön meg arról, hogy a rendszeróra által mutatott idő legfeljebb 15 perccel tér el a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.|
|06:28:45:Nem sikerült a tanúsítvány előállítása<br>06:28:45:A telepítést nem lehet folytatni. A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Telepítés ismételt futtatása | A telepítés futtatásához helyi rendszergazdaként jelentkezzen be. |