---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 80bb07d850628f07ebc37a39da1294399804d8f5
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165136"
---
Az Azure File Sync ügynök rendszeresen adhat hozzá új funkciókat, és problémák megoldására frissül. Azt javasoljuk, hogy konfigurálja a Microsoft Update elérhető az Azure File Sync ügynök le a frissítéseket.

#### <a name="major-vs-minor-agent-versions"></a>Nagyobb vagy kisebb ügynökverziók
* Fő ügynökverziók gyakran új szolgáltatásokat tartalmaznak, és egyre több rendelkezik, mint a verziószám első része. Például: *2.\*.\**
* Kisebb ügynökverziók "javítások" is nevezik, és a főbb verziók gyakrabban kiadott. Hibajavítások és a kisebb fejlesztések, de nincs tervben új szolgáltatások gyakran tartalmaznak. Például:  *\*.3.\**

#### <a name="upgrade-paths"></a>Frissítési útvonalak
Három jóváhagyott és tesztelt módon az Azure File Sync ügynök frissítéseinek telepítéséhez. A frissítési útvonalak működik a fő-és a kisebb.
1. **(Javasolt) Automatikus frissítések letöltése és telepítése az ügynök a Microsoft Update konfigurálása.**  
    Javasoljuk, hogy mindig minden Azure-fájlszinkronizálás frissítés biztosíthatja, hogy a legújabb javításokat a hozzáférést a kiszolgáló ügynök véve. A Microsoft Update használatával Ez a folyamat zökkenőmentes, automatikus frissítések letöltése és telepítése az Ön számára.
2. **Egy meglévő Azure File Sync ügynök javítása a Microsoft Update javítófájl vagy egy végrehajtható .msp használatával. A legfrissebb Azure File Sync csomagot tölthető le a [a Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Egy végrehajtható .msp futó ugyanazzal a módszerrel a korábbi frissítési útvonalat a Microsoft Update által automatikusan használt frissíti az Azure File Sync telepítése. A Microsoft Update-javítás alkalmazása egy helyszíni frissítését az Azure File Sync telepítésének hajtsa végre.
3. **Töltse le a legújabb Azure File Sync ügynök telepítőt a a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). A telepítő letöltési Microsoft Installer-csomagot, vagy egy végrehajtható .msi.**  
    Egy meglévő Azure File Sync-ügynök telepítése, frissítése majd telepítse a legújabb verziót a letöltött telepítőt, és eltávolítja a régebbi verziót. A kiszolgáló regisztrálása, szinkronizálási csoportok és a további beállításokat az Azure File Sync Installer karbantartása.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Az ügynök életciklusát, és módosítsa a felügyeleti garanciák
Az Azure File Sync egy felhőalapú szolgáltatás, amely lehetővé teszi, hogy folyamatosan új szolgáltatások és funkciók bevezetése. Ez azt jelenti, hogy egy adott Azure File Sync ügynök verziója csak korlátozott ideig csak támogatható. Az üzembe helyezés megkönnyítéséhez garantálja, hogy a következő szabályok elegendő időt és az értesítési megfelelően agent frissítések/frissítése a változáskezelési folyamatot a rendelkezik:

- A kezdeti kiadás legalább hat hónapig fő ügynök verziókat támogatja.
- Garantáljuk, hogy legalább három hónappal a fő ügynökök verzióinak a támogatása között átfedés van. 
- A rendszer figyelmeztetést regisztrált kiszolgálók használatával egy-az-hamarosan lejárt ügynök legalább három hónappal a lejárat előtt. Ha egy regisztrált kiszolgálón az ügynök egy régebbi verzióját használja a Storage Sync Service regisztrált kiszolgálók szakaszában ellenőrizhetők.
- Egy kisebb ügynökverzió élettartama társított főverziója van kötve. Például, ha az ügynök 3.0-s verziója, ügynökverziók 2. \* összes értékre lesz beállítva együtt jár le.

> [!Note]
> Egy ügynök verziójának telepítése egy lejárati figyelmeztetéssel figyelmeztetést jelenít meg, de sikeres. Próbál meg telepíteni, vagy kapcsolódjon egy lejárt ügynök verziója nem támogatott, és le lesz tiltva.