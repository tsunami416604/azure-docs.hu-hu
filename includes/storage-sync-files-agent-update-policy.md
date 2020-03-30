---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123156"
---
Az Azure File Sync ügynök rendszeresen frissül, hogy új funkciókat, és a problémák megoldásához. Azt javasoljuk, hogy konfigurálja a Microsoft Update-et az Azure File Sync ügynök frissítéseinek beszerzésére, amint azok elérhetők.

#### <a name="major-vs-minor-agent-versions"></a>Fő- és alügynök-verziók
* A főügynök-verziók gyakran tartalmaznak új funkciókat, és a verziószám első részeként egyre több funkcióval rendelkeznek. Például: \*2.\*.\*\*
* Az alügynökök verzióit "javításoknak" is nevezik, és gyakrabban adják ki őket, mint a főverziók. Gyakran tartalmaznak hibajavításokat és kisebb fejlesztéseket, de nincsenek új funkciók. Például: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Frissítési útvonalak
Az Azure File Sync ügynök frissítéseinek telepítésének négy jóváhagyott és tesztelt módja van. 
1. **(Preferált) Konfigurálja a Microsoft Update szolgáltatást az ügynökfrissítések automatikus letöltésére és telepítésére.**  
    Mindig javasoljuk, hogy minden Azure File Sync frissítés annak érdekében, hogy hozzáférjenek a kiszolgálóügynök legújabb javításaihoz. A Microsoft Update a frissítések automatikus letöltésével és telepítésével zökkenőmentessé teszi ezt a folyamatot.
2. **Az AfsUpdater.exe fájl segítségével töltse le és telepítse az ügyintézők frissítéseit.**  
    Az AfsUpdater.exe fájl az ügynök telepítési könyvtárában található. Kattintson duplán a végrehajtható fájlra az ügynökfrissítések letöltéséhez és telepítéséhez. 
3. **Javítson egy meglévő Azure File Sync-ügynököt egy Microsoft Update javítófájl vagy egy .msp végrehajtható fájl használatával. A legújabb Azure File Sync frissítési csomag letölthető a [Microsoft Update katalógusból.](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)**  
    A .msp végrehajtható fájl futtatása az Azure File Sync telepítését ugyanazzal a módszerrel frissíti, amelyet a Microsoft Update az előző frissítési útvonalon automatikusan használ. A Microsoft Update javítás telepítése az Azure File Sync telepítésének helybeni frissítését hajtja végre.
4. **Töltse le a legújabb Azure File Sync ügynök telepítőt a [Microsoft letöltőközpontból.](https://go.microsoft.com/fwlink/?linkid=858257)**  
    Meglévő Azure File Sync ügynök telepítésének frissítéséhez távolítsa el a régebbi verziót, majd telepítse a legújabb verziót a letöltött telepítőből. A kiszolgáló regisztrációját, szinkronizálási csoportjait és minden egyéb beállítást az Azure File Sync telepítője karbantart.

#### <a name="automatic-agent-lifecycle-management"></a>Automatikus ügynökéletciklus-kezelés
Az ügynök 6-os verziójával a fájlszinkronizálási csapat bevezetett egy ügynök automatikus frissítési funkcióját. Két mód közül választhat, és megadhat egy karbantartási időszakot, amelyben a frissítés megkísérelhető a kiszolgálón. Ez a funkció úgy van kialakítva, hogy segítsen az ügynök életciklusának kezelésében azáltal, hogy egy védőkorlátot biztosít, amely megakadályozza az ügynök lejáratát, vagy lehetővé teszi a nem-gondot, maradjon aktuális beállítást.
1. Az **alapértelmezett beállítás** megpróbálja megakadályozni az ügynök lejáratát. Az ügynök közzétett lejárati dátumát követő 21 napon belül az ügynök megkísérli az önfrissítést. A lejárat előtt 21 nappal és a kiválasztott karbantartási időszakban hetente egyszer megkezdi a frissítési kísérletet. **Ez a beállítás nem szünteti meg a Microsoft Update rendszeres javításainak szükségességét.**
1. Tetszés szerint kiválaszthatja, hogy az ügynök automatikusan frissíti magát, amint egy új ügynökverzió elérhetővé válik (jelenleg nem alkalmazható a fürtözött kiszolgálókra). Ez a frissítés a kiválasztott karbantartási időszakban történik, és lehetővé teszi, hogy a kiszolgáló kihasználhassa az új funkciókat és fejlesztéseket, amint azok általánosan elérhetővé válnak. Ez az ajánlott, gondtalan beállítás, amely biztosítja a főügynök-verziókat, valamint a rendszeres frissítési javításokat a kiszolgálóra. Minden megjelent ügynök GA minőségű. Ha ezt a lehetőséget választja, a Microsoft a legújabb ügynökverziót fogja eljáratni. A fürtözött kiszolgálók nem tartoznak ide. A repülés befejezése után az ügynök a [Microsoft letöltőközpontban](https://go.microsoft.com/fwlink/?linkid=858257) is elérhetővé válik aka.ms/AFS/agent.

 ##### <a name="changing-the-auto-upgrade-setting"></a>Az automatikus frissítési beállítás módosítása

Az alábbi utasítások bemutatják, hogyan módosíthatja a beállításokat a telepítő befejezése után, ha módosításokat kell végrehajtania.

Nyisson meg egy PowerShell-konzolt, és keresse meg azt a könyvtárat, ahol a szinkronizálási ügynököt telepítette, majd importálja a kiszolgálóparancsmagokat. Alapértelmezés szerint ez valahogy így néz ki:
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

Futtathatja `Get-StorageSyncAgentAutoUpdatePolicy` az aktuális házirend-beállítás ellenőrzését, és meghatározhatja, hogy módosítani kívánja-e.

Ha az aktuális házirend-beállítást a késleltetett frissítési sávra szeretné módosítani, a következőket használhatja:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

Ha az aktuális házirend-beállítást közvetlen frissítési sávra szeretné módosítani, a következőket használhatja:
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Ügynök életciklusa és változáskezelési garanciái
Az Azure File Sync egy felhőalapú szolgáltatás, amely folyamatosan bevezeti az új funkciókat és fejlesztéseket. Ez azt jelenti, hogy egy adott Azure File Sync ügynök verziója csak korlátozott ideig támogatott. A telepítés megkönnyítése érdekében az alábbi szabályok garantálják, hogy elegendő ideje és értesítése van az ügynökfrissítések/-frissítések befogadásához a változáskezelési folyamatban:

- A főügynök-verziók a kezdeti kiadás dátumától számított legalább hat hónapig támogatottak.
- Garantáljuk, hogy legalább három hónapos átfedés van a főügynöki verziók támogatása között. 
- A rendszer legalább három hónappal a lejárat előtt figyelmeztetést ad ki a hamarosan lejárt ügyintézőt használó regisztrált kiszolgálókra. Ellenőrizheti, hogy a regisztrált kiszolgáló az ügynök régebbi verzióját használja-e a Storage Sync Service regisztrált kiszolgálók szakasza alatt.
- Az alügynök-verzió élettartama a társított főverzióhoz van kötve. Például, ha ügynök 3.0-s verziója szabadul fel, ügynök 2-es verziójú. \* minden lesz beállítva, hogy együtt jár le.

> [!Note]
> Az ügynök verziójának lejárati figyelmeztetéssel történő telepítése figyelmeztetést jelenít meg, de sikeres lesz. A lejárt ügynökverzió telepítésére vagy csatlakoztatására tett kísérlet nem támogatott, és le lesz tiltva.
