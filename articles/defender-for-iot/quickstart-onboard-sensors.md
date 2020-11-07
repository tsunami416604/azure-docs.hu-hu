---
title: 'Gyors útmutató: érzékelők beléptetése az Azure Defenderben IoT'
description: Ismerje meg, hogyan hozhatja be az Azure Defender IoT-üzembe helyezéséhez szükséges érzékelők bevezetését.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: dafa2f231581ee7e7fa7a59a9c9320f6f2a67038
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357684"
---
# <a name="quickstart-deploy-and-onboard-a-sensor"></a>Gyors útmutató: érzékelő üzembe helyezése és előkészítése

Ez a cikk áttekintést nyújt az érzékelő telepítési folyamatáról. Az érzékelőknek be kell jelentkezniük az Azure Defender for IoT portálra.

Ehhez a folyamathoz meg kell adni egy előre konfigurált érzékelőt, vagy egy hitelesített érzékelő berendezést kell bekapnia, és saját kezűleg kell telepítenie az érzékelő szoftvert.

Ha hitelesített érzékelő berendezéssel dolgozik, javasoljuk, hogy a Kezdés előtt tekintse át az [Azure Defender for IoT hardveres specifikációs útmutatóját](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .


> [!NOTE]
> Az Azure Defender for IoT helyszíni felügyeleti konzolját nem kell előkészíteni.


A bevezetési érzékelőkkel a következőket teheti:

|||
|------ | ----------- |
| **Adja meg az érzékelő nevét** | Nevezze el az előkészíteni kívánt érzékelőt, és társítsa azt egy IoT Hubhoz vagy előfizetéshez.<br /><br />További információért lásd a bevezetési **érzékelők** című témakört.|
|**Előfizetés és a véglegesített eszközök számának kiválasztása**|Válassza ki az előfizetést és az előfizetés által érintett eszközök számát. Adja meg a 1000-os növekmények számát.|
| **Érzékelő-felügyeleti mód definiálása** | Itt adhatja meg, hogy a rendszer hol jelenítse meg az eszköz, a riasztás és az érzékelő által észlelt egyéb adatokat. Ezt a rendszer a definiált **érzékelő-kezelési mód** alapján határozza meg.<br /><br />**Helyileg felügyelt mód** : az érzékelő által észlelt információk az érzékelő konzolon jelennek meg. Az észlelési információk a helyszíni felügyeleti konzollal is megoszthatók, ha az érzékelő csatlakoztatva van hozzá.<br /><br />**Felhőalapú felügyelt mód** : az érzékelő által észlelt információk az érzékelő konzolon jelennek meg. Emellett a riasztási információk egy IoT Hub keresztül érkeznek, és más Azure-szolgáltatásokkal is megoszthatók, például az Azure Sentinel használatával.<br />További információért tekintse meg a bevezető **érzékelőt** . |
| **Érzékelő aktiválási fájljának beolvasása** | A **helyileg felügyelt** érzékelők esetében egy aktiválási fájl használható az adott szenzoros aktiválási időszakok kezelésére.<br /><br />A **felhőben felügyelt** érzékelők esetében az érzékelő és a IoT hub közötti kapcsolat az aktiválási fájl. További információ: előkészítési **érzékelők**. |
| **Aktiválási fájl feltöltése az érzékelőbe** | Az aktiválási fájlokat fel kell tölteni a vállalati érzékelőkbe. A Hálózatfigyelő és az érzékelő konzol funkciói nem lesznek elérhetők az aktiválási fájl feltöltése előtt. További információ: szenzor- **aktiválási fájlok feltöltése**. |
| **Az érzékelő hálózati paramétereinek frissítése az aktiválás előtt** | Az érzékelő telepítésekor meghatározott paraméterek frissítése. További információ: szenzor- **aktiválási fájlok feltöltése**.|


**Érzékelő üzembe helyezése:**

1. Nyissa meg az Azure Defender for IoT alkalmazást a Azure Portal.

2. Válassza az első **lépések lehetőséget.**

3. A **Hálózat felderítése** szakaszban válassza a **beállítás** lehetőséget.

   ![A IoT készült Azure Defender felderíti a hálózati nézetet](media/updates/image5.png)

4. Válassza ki az érzékelő beszerzésének lehetőségét.

   ![Azure Defender a IoT hálózati érzékelő nézetéhez](media/updates/image6.png)

  - **Előre konfigurált érzékelő vásárlása: a** Microsoft és a Arrow is partneri kapcsolattal rendelkezik az előre konfigurált érzékelők biztosításához. Előre konfigurált érzékelő megvásárlásához lépjen a következőre: <hardware.sales@arrow.com> . Az érzékelő a létesítmény számára lesz továbbítva. A legfrissebb verzió van telepítve.

  - **Saját készülék használata (ISO-telepítés)** : a megoldás hitelesített készülékeken fut. A Certified berendezés megvásárlásakor használja az [Azure Defender for IoT hardveres specifikációs útmutatóját](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .

    - Válasszon egy verziót a **verzió kiválasztása** menüből.

    - Válassza a **Letöltés** lehetőséget, és mentse a fájlt. Az ISO-rendszerkép letöltésével és az érzékelő szoftver telepítésével kapcsolatos információkért tekintse meg az **Azure Defender for IoT telepítési útmutatóját** .

5. Miután telepítette a szoftvert az érzékelőre, vagy egy előre konfigurált érzékelőt kapott, hajtsa végre a hálózati telepítési feladatokat. A részletekért tekintse meg a [hálózati beállítási útmutatót](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="onboard-a-sensor"></a>Érzékelő előkészítése

Az érzékelőknek be kell jelentkezniük az Azure Defender for IoT portálra. A bevezetés két fázisban történik:

1. Regisztrálja az érzékelőt az Azure Defender for IoT portálon.

2. Töltse le az érzékelőhöz tartozó aktiválási fájlt. A fájl később fel lesz töltve az érzékelőbe.

**A bevezető oldal elérése:**

1. Navigáljon Microsoft Azure Defender IoT – **első lépések** oldalára.

2. Válassza ki a **2** . lehetőséget az Azure Defender for IoT.

   ![Képernyőfelvétel a külső oldal nézetéről](media/updates/image7.png)

3. Megnyílik az előkészítő **érzékelő** oldal. Lehetőséget biztosít az érzékelő regisztrálására és az aktiválási fájl letöltésére.

   ![Képernyőfelvétel a külső érzékelőről – oldal nézet](media/quickstart/onboard-sensors.png)

**Az érzékelő regisztrálása:**

1. Válassza ki az érzékelő nevét. A felhőben felügyelt érzékelők esetében az itt megadott név az érzékelő konzolján megjelenő nevet alkalmazza: Ez a név nem módosítható a konzolról. A helyileg felügyelt érzékelők esetében az itt alkalmazott név az Azure-ban lesz tárolva, de az érzékelő konzolon is frissíthető. Javasoljuk, hogy adja meg a név részeként telepített érzékelő IP-címét, vagy használjon egy könnyen azonosítható nevet. Így könnyebben nyomon követhető és konzisztens az Azure Defender for IoT portál regisztrációs neve, valamint az érzékelő konzolon megjelenő üzembe helyezett érzékelő IP-címe.

2. Válasszon egy előfizetést az előfizetés legördülő listából.
3. A **véglegesített eszközök** mezőben adja meg az előfizetés által érintett eszközök számát. Ezt a számot újra meg kell adnia minden olyan érzékelőnél, amely ugyanahhoz az előfizetéshez van társítva.  Ha például az "A" előfizetés 6000-eszközökhöz van társítva, akkor az "A" előfizetéshez társított minden érzékelő esetében adja meg a 6000 értéket.   
4. Válassza ki a szenzor-kezelési módot a **Cloud összekapcsolás** váltógomb használatával. Ha a váltógomb be van kapcsolva, az érzékelőt a **felhő felügyeli**. Ha a váltógomb ki van kapcsolva, a rendszer **helyileg felügyeli** az érzékelőt.


| Érzékelő-felügyeleti mód | Leírás                                                |
| ---------------------- | ---------------------------------------------------------  |
| **Felhő által felügyelt**          | Az érzékelő által észlelt információk az érzékelő konzolon jelennek meg. Emellett a riasztási információk egy IoT Hub keresztül érkeznek, és más Azure-szolgáltatásokkal is megoszthatók, például az Azure Sentinel használatával.<br /><br />Válassza ki az érzékelőhöz társítandó IoT Hub.<br /><br />A felhőben felügyelt aktiválási fájlt fel kell töltenie a Cloud Managed sensors szolgáltatásba.<br /><br />További részletekért lásd: **Sensor-aktiválási fájlok feltöltése** . |
| **Helyileg felügyelt**        | A helyileg felügyelt érzékelők által észlelt információk az érzékelő konzolján jelennek meg. Ha gapped hálózaton dolgozik, és szeretné, hogy a több helyileg felügyelt érzékelők által észlelt összes információ egységes legyen, működjön együtt a helyszíni felügyeleti konzollal.<br /><br />A *helyileg felügyelt* érzékelők egy Azure-előfizetéshez vannak társítva, és az aktiválás lejárati idejét érzékelő útmutatást tartalmaznak.<br /><br />Válassza ki az érzékelőhöz társítandó előfizetést.<br /><br />Fel kell töltenie egy helyileg felügyelt aktiválási fájlt az egyes érzékelőkbe. További részletekért lásd: **Sensor-aktiválási fájlok feltöltése** . |

5. Válassza a **Regisztráció** lehetőséget.

6. Az aktiválási fájl letöltése lapon válassza az **aktiválási fájl letöltése** lehetőséget.

   ![Képernyőfelvétel a bevezetési érzékelők nézetéről](media/updates/image9.png)

7. Mentse a fájlt. A Fájlnév formátuma: `<hub_name>_<sensor_name>` . Az érzékelő neve a fent megadott névre utal.

8. Válassza a **Befejezés** lehetőséget.

9. Az érzékelő előkészítési folyamatának elvégzéséhez töltse fel a fájlt az érzékelőbe az Azure Defender for IoT-konzolról.
 
## <a name="upload-a-sensor-activation-file"></a>Sensor-aktiválási fájl feltöltése

Ez a cikk azt ismerteti, hogyan tölthet fel egy aktiválási fájlt az érzékelőbe. Az érzékelő bevezetéséhez meg kell kapnia a fájlt.

**Helyileg felügyelt aktiválási fájlok**

A helyileg felügyelt érzékelők egy Azure-előfizetéshez vannak társítva.  A helyileg felügyelt érzékelőkhöz tartozó aktiválási fájl lejárati dátumot tartalmaz. Egy hónappal ezen időpont előtt egy figyelmeztető üzenet jelenik meg az érzékelő konzoljának tetején. A figyelmeztetés addig marad, amíg nem frissítette az aktiválási fájlt.

Az aktiválási fájl lejárta után továbbra is használhatja az Azure Defender IoT funkcióit.

**Felhőben felügyelt aktiválási fájlok**

A felhőben kezelt érzékelők egy Azure-IoT Hubhoz vannak társítva. Ezek az érzékelők nem korlátozódnak az aktiválási fájl időtartamára. A felhőalapú felügyelt érzékelőkhöz tartozó aktiválási fájl a IoT Hubhoz való kapcsolódás biztosítására szolgál.

További információ az IoT-Hubokról: [Tudnivalók a IoT hubról](../iot-hub/about-iot-hub.md).

**Az aktiválási fájl feltöltése:**

1. Ellenőrizze, hogy rendelkezik-e az alábbiakkal:

   - Az érzékelő IP-címe – a telepítés során definiálva

   - Az érzékelőhöz szükséges felhasználói bejelentkezési hitelesítő adatok

2. Az érzékelő IP-címének használatával elérheti az Azure Defender for IoT-konzolt a böngészőjében.

3. Jelentkezzen be az Azure Defender for IoT-érzékelő konzolra.

   ![Az Azure Defender IoT-konzoljának bejelentkezési nézete](media/updates/image11.png)

4. A sikeres bejelentkezés után megnyílik az aktiválási képernyő. Válassza a feltöltés lehetőséget, és válassza ki a mentett aktiválási fájlt.

   ![Az Azure Defender for IoT aktiválási nézete](media/updates/image12.png)

5. Hagyja jóvá a feltételeket és a kikötéseket.

6. Válassza az **aktiválás** lehetőséget. Az érzékelő hálózati konfigurációs paraméterei a Szoftvertelepítés során vagy egy előre konfigurált érzékelő megvásárlásakor lettek definiálva. A következő paraméterek lettek definiálva:
   - IP-cím
   - DNS  
   - Alapértelmezett átjáró
   - Alhálózati maszk
   - Állomásnév
 
   Előfordulhat, hogy frissítenie kell ezt az információt az érzékelő aktiválása előtt, mert:
   - Módosítania kell a definiált előre konfigurált paramétereket  
   - Ha újra szeretné konfigurálni a hálózati paramétereket, a telepítés után megadhatja a proxybeállításokat az érzékelő aktiválása előtt.

7. Válassza az **érzékelő hálózati konfiguráció** hivatkozását az aktiválás párbeszédpanelben.  

   ![A hálózat konfigurációjának szerkesztése nézet képernyőképe](media/updates/image13.png)

8. Megjelenik a telepítés során meghatározott paraméterek. Egy lehetőség is rendelkezésre áll, amely meghatározza a proxyt. Szükség szerint frissítse a frissítést, és válassza a **Mentés** lehetőséget.
 
 
## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan telepíthet és helyezhet üzembe érzékelőket. Ha többet szeretne megtudni az első lépésekről, tekintse meg a következő cikket:

- [Bevezetés](getting-started.md)
