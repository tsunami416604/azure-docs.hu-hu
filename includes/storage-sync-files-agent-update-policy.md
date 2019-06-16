---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 57ba55ce284030a4103077553b0dcfce01a93678
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125754"
---
Az Azure File Sync ügynök rendszeresen adhat hozzá új funkciókat, és problémák megoldására frissül. Azt javasoljuk, hogy konfigurálja a Microsoft Update elérhető az Azure File Sync ügynök le a frissítéseket.

#### <a name="major-vs-minor-agent-versions"></a>Nagyobb vagy kisebb ügynökverziók
* Fő ügynökverziók gyakran új szolgáltatásokat tartalmaznak, és egyre több rendelkezik, mint a verziószám első része. Példa: \*2.\*.\*\*
* Kisebb ügynökverziók "javítások" is nevezik, és a főbb verziók gyakrabban kiadott. Hibajavítások és a kisebb fejlesztések, de nincs tervben új szolgáltatások gyakran tartalmaznak. Például: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Frissítési útvonalak
Négy jóváhagyott és tesztelt módon az Azure File Sync ügynök frissítéseinek telepítéséhez. 
1. **(Javasolt) Automatikus frissítések letöltése és telepítése az ügynök a Microsoft Update konfigurálása.**  
    Javasoljuk, hogy mindig minden Azure-fájlszinkronizálás frissítés biztosíthatja, hogy a legújabb javításokat a hozzáférést a kiszolgáló ügynök véve. A Microsoft Update használatával Ez a folyamat zökkenőmentes, automatikus frissítések letöltése és telepítése az Ön számára.
2. **Az ügynök-frissítések letöltése és telepítése AfsUpdater.exe használja.**  
    A AfsUpdater.exe az ügynök telepítési könyvtárában található. Kattintson duplán a végrehajtható fájl letöltése és telepítése az ügynök-frissítések. 
3. **Egy meglévő Azure File Sync ügynök javítása a Microsoft Update javítófájl vagy egy végrehajtható .msp használatával. A legfrissebb Azure File Sync csomagot tölthető le a [a Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Egy végrehajtható .msp futó ugyanazzal a módszerrel a korábbi frissítési útvonalat a Microsoft Update által automatikusan használt frissíti az Azure File Sync telepítése. A Microsoft Update-javítás alkalmazása egy helyszíni frissítését az Azure File Sync telepítésének hajtsa végre.
4. **Töltse le a legújabb Azure File Sync ügynök telepítőt a a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Frissíthet meglévő telepítést Azure File Sync ügynök, távolítsa el a régebbi verziót, és majd telepítse a legújabb verziót a letöltött telepítőt. A kiszolgáló regisztrálása, szinkronizálási csoportok és a további beállításokat az Azure File Sync Installer karbantartása.

#### <a name="automatic-agent-lifecycle-management"></a>Automatikus ügynök-életciklus-felügyelete
Az ügynök 6-os verzió a file sync csapat egy ügynök automatikus frissítési funkció vezetett be. Válassza ki a két mód közül választhat, és adja meg a karbantartási időszak, amelyben a frissítés lehetséges a kiszolgálón. Ez a funkció célja segítséget megadásával vagy egy megakadályozza, hogy az ügynököt a lejárati vízoldalon ügynök életciklus-felügyeletének, vagy maradjon egy gördülékeny, ami lehetővé teszi a aktuális beállítás.
1. A **alapértelmezett beállítás** megkísérli az ügynököt a lejárati elkerülése érdekében. Az ügynök közzétett lejárati dátumának 21 napban az ügynök megpróbálja helyi frissítése. Olyan megkísérli a lejárat előtt, és a kiválasztott karbantartási időszakban 21 napban hetente egyszer el is indítja. **Ez a beállítás nem szükségtelenné teszi reguláris a Microsoft Update-javítások véve.**
2. Igény szerint kiválaszthatja, hogy az ügynök automatikusan frissíti magát, amint elérhetővé válik egy új ügynök verziója (fürtözött kiszolgálók jelenleg nem vonatkozik). Ez a frissítés fog fordulhat elő, a kijelölt karbantartási időszak alatt, és lehetővé teszi a kiszolgáló számára, hogy új szolgáltatásait és fejlesztéseit, amint azok általánosan elérhetővé válnak. Ez az az ajánlott, megbízható beállítása, amely jelentős ügynökverziók, valamint a rendszeres frissítés javítások a kiszolgálóhoz biztosít. Minden ügynök, amely a jelenleg általánosan elérhető minőségét. Ezt a beállítást, ha a Microsoft eszközmegfelelőségre fogja, hogy a legújabb ügynök verziója. Fürtözött kiszolgálók ki vannak zárva. Fokozatos bevezetéssel befejeződése után az ügynök is válnak elérhetővé a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Az ügynök életciklusát, és módosítsa a felügyeleti garanciák
Az Azure File Sync egy felhőalapú szolgáltatás, amely folyamatosan a új szolgáltatásait és fejlesztéseit mutatja be. Ez azt jelenti, hogy egy adott Azure File Sync ügynök verziója csak korlátozott ideig csak támogatható. Az üzembe helyezés megkönnyítéséhez a következő szabályok garantálja a elegendő időt és értesítési megfelelően agent frissítések/frissítése a változáskezelési folyamatot a:

- A kezdeti kiadás legalább hat hónapig fő ügynök verziókat támogatja.
- Garantáljuk, hogy legalább három hónappal a fő ügynökök verzióinak a támogatása között átfedés van. 
- A rendszer figyelmeztetést regisztrált kiszolgálók használatával egy-az-hamarosan lejárt ügynök legalább három hónappal a lejárat előtt. Ha egy regisztrált kiszolgálón az ügynök egy régebbi verzióját használja a Storage Sync Service regisztrált kiszolgálók szakaszában ellenőrizhetők.
- Egy kisebb ügynökverzió élettartama társított főverziója van kötve. Például, ha az ügynök 3.0-s verziója, ügynökverziók 2. \* összes értékre lesz beállítva együtt jár le.

> [!Note]
> Egy ügynök verziójának telepítése egy lejárati figyelmeztetéssel figyelmeztetést jelenít meg, de sikeres. Próbál meg telepíteni, vagy kapcsolódjon egy lejárt ügynök verziója nem támogatott, és le lesz tiltva.
