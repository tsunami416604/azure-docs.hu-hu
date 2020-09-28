---
title: Azure Defender kiszolgálók számára – az előnyök és funkciók
description: Ismerje meg az Azure Defender for Servers szolgáltatás előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5e4244c0526dbac3bb8e9b2d3856eb01e1b2a2b7
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403493"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Bevezetés az Azure Defender for Servers szolgáltatásba

Az Azure Defender for Servers szolgáltatás a fenyegetések észlelését és a Windows és Linux rendszerű gépek speciális védelmét is biztosítja.

A Windows rendszerben az Azure Defender az Azure-szolgáltatásokkal integrálja a Windows-alapú gépek figyelését és megóvását. Security Center az összes szolgáltatás riasztásait és szervizelési javaslatait egyszerűen használható formátumban jeleníti meg.

A Linux rendszerben az Azure Defender a Linux rendszerű gépek naplózási rekordjait az **auditált**, az egyik leggyakrabban használt Linux-naplózási keretrendszer használatával gyűjti. a naplózott élet a fővonali kernelben. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Milyen előnyökkel jár az Azure Defender a kiszolgálókhoz?

Az Azure Defender for Servers szolgáltatáshoz biztosított fenyegetések észlelési és védelmi képességei a következők:

- **Biztonsági rések felmérése virtuális gépekhez** – a Azure Security Center által tartalmazott sebezhetőségi képolvasót a Qualys működteti. 

    A Qualys "Scanner az Azure-Virtual Machines biztonsági réseinak valós idejű azonosítására szolgáló vezető eszközök egyike. Nincs szüksége Qualys-licencre, vagy akár Qualys-fiókra is – minden a Security Centeron belül zökkenőmentesen kezelhető. [További információ](deploy-vulnerability-assessment-vm.md).

- Igény szerinti **(JIT)** virtuálisgép-hozzáférés – a fenyegetést kezelő szereplők aktívan vadásznak a nyílt felügyeleti portok, például az RDP vagy az SSH számára elérhető gépekről. Az összes virtuális gép potenciális célpont a támadásoknak. Ha egy virtuális gép biztonsága sikeres, a rendszer belépési pontként használja a környezetében lévő további erőforrások támadásához.

    Ha engedélyezi az Azure Defender for Servers-t, a virtuális gépek igény szerinti elérésével zárolhatja a bejövő forgalmat a virtuális gépekre, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, amikor szükséges. [További információ](just-in-time-explained.md).

- A fájlok **integritásának figyelése (FIM)** – a fájl integritásának figyelése (FIM), más néven a változások figyelése, megvizsgálja az operációs rendszer, az alkalmazás szoftvere és mások által a támadásra utaló módosításokat. A rendszer összehasonlító módszert használ annak megállapítására, hogy a fájl aktuális állapota eltér-e a fájl legutóbbi vizsgálatával. Ezt az összehasonlítást kihasználva megállapíthatja, hogy érvényes vagy gyanús módosítások történtek-e a fájlokban.

    Ha engedélyezi az Azure Defender for Servers használatát, a FIM használatával ellenőrizheti a Windows-fájlok, a Windows-beállításjegyzékek és a Linux-fájlok integritását. [További információ](security-center-file-integrity-monitoring.md).

- **Adaptív alkalmazás-vezérlőelemek (AAC)** – az adaptív alkalmazások intelligens és automatizált megoldást biztosítanak a gépek ismert biztonságos alkalmazásainak engedélyezési listájához.

    Ha engedélyezte és konfigurálta az adaptív alkalmazások vezérlőit, akkor biztonsági riasztásokat fog kapni, ha bármely alkalmazás a biztonságosként definiált beállításoktól eltérő módon fut. [További információ](security-center-adaptive-application.md).

- **Adaptív hálózat megerősítése (** NSG) – a hálózati biztonsági csoportok (-ek) alkalmazása az erőforrások felé irányuló és onnan érkező forgalom szűrésére, javítja a hálózat biztonsági helyzetét. Azonban továbbra is előfordulhatnak olyan esetek, amikor a NSG keresztül áramló tényleges forgalom a definiált NSG-szabályok részhalmaza. Ezekben az esetekben a NSG-szabályoknak a tényleges forgalmi minták alapján történő megerősítése révén a biztonsági helyzet további javítása is megvalósítható.

    Az adaptív hálózat megerősítése ajánlásokat biztosít a NSG-szabályok további megerősítéséhez. Egy gépi tanulási algoritmust használ, amely a tényleges forgalom, az ismert megbízható konfiguráció, a veszélyforrások felderítése és más, a biztonsággal kapcsolatos mutatók szempontjából tényezőket tartalmaz, és javaslatokat tesz arra, hogy csak adott IP-/port-rekordok származó forgalmat engedélyezzen. [További információ](security-center-adaptive-network-hardening.md).

- **Integráció a Microsoft Defender komplex veszélyforrások elleni védelemmel (ATP) (csak Windows)** – az Azure Defender a Microsoft Defender komplex veszélyforrások elleni védelem (ATP) szolgáltatással integrálható. Együttesen átfogó végpont-észlelési és-reagálási (EDR) képességeket biztosítanak. [További információ](security-center-wdatp.md).

    > [!IMPORTANT]
    > A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Security Center-t használó Windows-kiszolgálókon.

    Ha a Microsoft Defender ATP fenyegetést észlel, riasztást indít el. A riasztás Security Center jelenik meg. A Security Center a Microsoft Defender ATP-konzolt is elvégezheti, és részletes vizsgálatot végezhet a támadás hatókörének felderítése érdekében. További információ a Microsoft Defender ATP szolgáltatással kapcsolatban: kiszolgálók beléptetése [a Microsoft DEFENDER ATP szolgáltatásba](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

- A **Docker-gazdagép megerősítése** – Azure Security Center azonosítja a IaaS Linux rendszerű virtuális gépeken üzemeltetett nem felügyelt tárolókat, illetve a Docker-tárolókat futtató más Linux-gépeket. Security Center folyamatosan elemzi a tárolók konfigurációit. Ezután összehasonlítja azokat a Center for Internet Security (CIS) Docker-teljesítményteszttel. Security Center tartalmazza a CIS Docker-teljesítményteszt teljes szabályait, és riasztást küld, ha a tárolók nem elégítik ki a vezérlőelemek egyikét sem. [További információ](harden-docker-hosts.md).

- **Fájlok nélküli támadás észlelése (csak Windows)** – a fájl nélküli támadások rosszindulatú hasznos adatokat szúrnak be a memóriába a lemezes ellenőrzési módszerek észlelése érdekében. A támadó adattartalma a feltört folyamatok memóriájában marad, és számos kártékony tevékenységet hajt végre.

  A fájlok közötti támadás észlelése, az automatizált memória kriminalisztikai módszerei azonosítják a fájlokra vonatkozó támadási segédanyagokat, technikákat és viselkedéseket. Ez a megoldás rendszeres időközönként ellenőrzi a gépet, és kinyeri az eredményeket közvetlenül a folyamatok memóriájában. Az adott megállapítások közé tartozik a következők azonosítása: 

  - Jól ismert eszközkészletek és kriptográfiai adatbányászati szoftverek 

  - A héjkód, amely egy kis kódrészlet, amely általában a szoftveres biztonsági rések kiaknázása során hasznos adattartalomként használatos.

  - Rosszindulatú végrehajtható fájl beinjektálása a folyamat memóriájába

  A fájlok közötti támadás észlelése olyan részletes biztonsági riasztásokat hoz létre, amelyek a további folyamat metaadatainak, például a hálózati tevékenység leírását tartalmazzák. Ez felgyorsítja a riasztások osztályozását, korrelációját és az alsóbb rétegbeli válaszadási időt. Ez a megközelítés kiegészíti az Event-alapú EDR megoldásokat, és nagyobb észlelési lefedettséget biztosít.

  A fájlokba nem kerülő támadás észlelésével kapcsolatos riasztások részleteiért tekintse meg a [riasztások hivatkozási táblázatát](alerts-reference.md#alerts-windows).

- **Linux auditált riasztások és log Analytics ügynök integrációja (csak Linux)** – az auditált rendszer egy kernel szintű alrendszerből áll, amely a rendszerhívások figyelésének felelőse. Egy megadott szabálykészlet alapján szűri őket, és üzeneteket küld nekik egy szoftvercsatorna számára. A Security Center a Log Analytics-ügynökön belül az auditált csomag funkcióit integrálja. Ez az integráció lehetővé teszi az auditált események gyűjtését az összes támogatott Linux-disztribúcióban, előfeltételek nélkül.

    a naplózott rekordok gyűjtése, bővítése és összesítése az eseményekre a Linux-ügynök Log Analytics ügynökének használatával történik. Security Center folyamatosan bővíti a Linux-jeleket használó új elemzéseket a Felhőbeli és a helyszíni linuxos gépek kártékony viselkedésének észlelése érdekében. A Windows-képességekhez hasonlóan ezek az elemzések a gyanús folyamatokon, a kétes bejelentkezési kísérleteken, a kernel modul betöltésén és más tevékenységeken is kiterjedhetnek. Ezek a tevékenységek jelezhetik, hogy a gép támadás alatt áll, vagy megsértették.  

    A Linux-riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-linux)tartalmazza.


## <a name="simulating-alerts"></a>Riasztások szimulálása

A riasztások szimulálása a következő forgatókönyvek egyikének letöltésével végezhető el:

- Windows esetén: [Azure Security Center forgatókönyv: biztonsági riasztások](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux esetén: [Azure Security Center forgatókönyv: Linux-észlelések](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan használható az Azure Defender a kiszolgálókhoz. 

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.

- > [!div class="nextstepaction"]
    > [Az Azure Defender engedélyezése](security-center-pricing.md)