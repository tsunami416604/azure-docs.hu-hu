---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289189"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Ajánlott biztonsági eljárások az eszközök internetes hálózatához (IoT)

Szigorú biztonsági jellegű stratégia védelme az eszközök internetes hálózata (IoT) infrastruktúrát igényel. Ez a stratégia megköveteli, hogy a felhőbeli adatok védelmére, a nyilvános interneten keresztül az átvitel során adatintegritásának védheti meg és biztonságos eszközök kiépítése. Az egyes rétegek a nagyobb biztonság az infrastruktúra átfogó épít fel.

## <a name="secure-an-iot-infrastructure"></a>Biztonságos IoT-infrastruktúrát

Ez a biztonsági jellegű stratégia az alkalmazáskódok fejlesztésének és aktív részvételét a gyártási, fejlesztés és üzembe helyezési IoT-eszközök és infrastruktúra által végrehajtani. Következő ezen a játékosok magas szintű leírását.

* **IoT hardver gyártója/integráló**: ezek a játékosok általában a gyártók IoT hardver parancsfájlműveletekkel rendszerintegrátorok hardver összeállítása a különböző gyártók vagy szállítók hardver egy IoT-környezet biztosítása gyártott vagy más szállítók által integrált.

* **IoT-megoldás fejlesztői**: IoT-megoldások fejlesztésének általában végzi el a megoldás fejlesztőjének. A fejlesztői. Előfordulhat, hogy rész-csapat vagy a tevékenység-fókuszú rendszerintegrátor (SI). Az IoT-megoldás fejlesztőjének különféle komponenseinek használatát a teljesen új IoT-megoldás fejlesztése, különböző megoldásszolgáltatóknál vagy a nyílt forráskódú összetevők integrálása, vagy elfogadja a kisebb betanítás megoldásgyorsítók.

* **IoT-megoldás deployer**: után az IoT-megoldás létrehozása, üzembe helyezhetők a mező szükséges. E folyamat magában foglalja a központi telepítési hardver, az eszközök összekapcsolása, és -megoldások a hardveres eszközökön vagy a felhőben.

* **IoT-megoldás operátor**: után az IoT-megoldás üzemel, a hosszú távú működésre, monitorozásra, frissítések és karbantartás van szükség. Ezeket a feladatokat egy csapat, amely magában foglalja az információk technológiai szakemberek, hardver műveletek és karbantartás csapatok és tartomány szakértőitől, akik a helyes működése általános IoT-infrastruktúra figyelése hajtható végre.

Az alábbi szakaszok minden ezeket a játékosok fejlesztése, telepítése és üzemeltetése egy biztonságos IoT-infrastruktúrát érdekében ajánlott eljárások.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT hardver gyártója/rendszerintegrátor

Az alábbiakban az IoT-hardvergyártói és hardver rendszerintegrátorok vonatkozó ajánlott eljárásokat.

* **Minimális követelményeknek megfelelő hardver hatókör**: A hardvertervezést tartalmaznia kell a a hardver, és semmi további művelethez szükséges minimális funkciók. Például, hogy tartalmazzák az USB-porttal, csak ha az eszköz működéséhez szükséges. Ezek a kiegészítő szolgáltatások nyissa meg a nemkívánatos kihasználó támadási vektoroktól el kell kerülni a eszközt.

* **Hogy a koncepció meghamisítása hardveres**: mechanizmus észleli a fizikai illetéktelen módosítás, például az eszköz fedőlap megnyitásával vagy egy részét az eszköz eltávolítása a hozhat létre. Ezek jelek átállítani a feltöltött a felhőbe, amely a kezelők ezek az események sikerült riasztása az adatfolyam részét.

* **Hozhat létre biztonságos hardver körül**: lehetővé teszi, ha COGS, biztonságos és titkosított tároló biztonsági funkciókat hozhat létre vagy indítsa el a platformmegbízhatósági modul (TPM) alapú funkció. Ezek a funkciók teszi az eszközöket több biztonságos, és az általános IoT-infrastruktúra védelme érdekében.

* **Győződjön meg, a frissítések biztonságos**: az eszköz teljes élettartama során belső vezérlőprogramok frissítése is elkerülhetetlen. Eszközök biztonságos elérési úttal, frissítések és a titkosítási folytatják a belső vezérlőprogram verzióinak létrehozása lehetővé teszi az eszköz biztonságos alatt és után frissítéseket.

## <a name="iot-solution-developer"></a>IoT-megoldás fejlesztői

Az ajánlott eljárások az IoT-megoldás fejlesztők számára a következők:

* **Hajtsa végre a szoftvert biztonságos fejlesztési módszertanába**: biztonságos szoftverfejlesztés földön felfelé szem előtt tartva biztonság, a kezdetektől a projekt egészen a végrehajtási, a tesztelés és a központi telepítés szükséges. A lehetőségek a platformok, nyelvek és eszközök összes befolyásolja az ezt a módszert. A Microsoft biztonsági fejlesztési életciklus részletes módszert biztosít a biztonságos szoftverek készítése.

* **Válassza ki a nyílt forráskódú szoftverek körültekintően**: megoldások gyors fejlesztése lehetőséget kínál a nyílt forráskódú szoftver. Nyílt forráskódú szoftverek választhassa, vegye figyelembe az egyes nyílt forráskódú összetevők a közösségi tevékenység szintjét. Egy aktív Közösség biztosítja, hogy a szoftver támogatja-e, és, hogy a problémák felderítése és foglalkozik. Azt is megteheti előfordulhat, hogy egy homályos és inaktív nyílt forráskódú szoftverek projekt nem támogatott, és a problémák vannak nem valószínűleg lesz felderítve.

* **Körültekintően integrálása**: számos szoftver biztonsági hibára derült címen a határt, könyvtárakat és API-k léteznek. Funkciók, amelyek nem feltétlenül szükséges a jelenlegi üzemelő példány továbbra is lehet egy API-réteget keresztül érhető el. Általános biztonságának biztosítása érdekében ügyeljen arra, hogy ellenőrizze a biztonsági hibára derült való integráció összetevők összes felületek.

## <a name="iot-solution-deployer"></a>IoT-megoldás deployer

Ajánlott eljárások az IoT-megoldás telepítőket a következők:

* **Hardver biztonságos üzembe**: IoT központi telepítések szükség lehet a hardver nem biztonságos helyen, például nyilvános szóközöket vagy felügyeletlen területi üzembe helyezni. Az ilyen helyzetekben, győződjön meg arról, hogy hardver üzembe helyezési hamisíthatatlan legnagyobb mértékben. Ha USB- és más portok elérhetők a hardverre, győződjön meg arról, biztonságosan jelez. Számos támadási vektorok használhatja ezeket az adatokat a belépési pontok.

* **Hitelesítési kulcsok biztonsága**: üzembe helyezés során az egyes eszközök megköveteli az eszköz azonosítóját, és hozzárendelt hitelesítési kulcsokat, a felhőalapú szolgáltatás által létrehozott. Biztonságban ezeket a kulcsokat fizikailag az üzembe helyezés után is. Feltört kulcs révén egy meglévő eszközt, hogy egy rosszindulatú eszköz is használható.

## <a name="iot-solution-operator"></a>IoT-megoldás operátor

Az ajánlott eljárások az IoT-megoldás operátorok a következők:

* **A rendszer naprakészen**: Győződjön meg arról, hogy a eszköz-operációsrendszerek és az összes eszköz-illesztőprogramok frissítése a legújabb verzióra. Ha bekapcsolja az automatikus frissítések a Windows 10 (IoT- és a többi termékváltozat), a Microsoft tartja azt naprakész, IoT-eszközök kezeléséről egy biztonságos operációs rendszert. Más operációs rendszereket (ilyen például a Linux rendszeren) tartja naprakészen segít biztosítani, hogy azok is védettek rosszindulatú támadások ellen.

* **Kártékony tevékenységek ellen védelmet biztosító**: Ha engedélyezi az operációs rendszert, telepítse a legújabb a víruskereső és kártevőirtó funkciókat minden eszköz operációs rendszere. Ez a gyakorlat segíthet elhárítani a fenyegetéseket a legtöbb külső. A legtöbb modern operációs rendszerek ellen védheti megfelelő lépésekkel.

* **Gyakran az audit**: naplózás IoT biztonsággal kapcsolatos infrastruktúra kulcs akkor, ha a biztonsági incidensekre való válaszadást. A legtöbb operációs rendszer adja meg a beépített eseménynaplózás, át kell tekinteni gyakori, hogy nem biztonsági hiba lépett fel. Naplózási információ elküldhető külön telemetriai adatfolyamként a felhőalapú szolgáltatás, ahol azok elemezhetők.

* **Az IoT-infrastruktúrájának védelme a fizikailag**: A legrosszabb biztonsági támadások ellen IoT-infrastruktúrát eszközökhöz való fizikai hozzáférés indul el. Egy fontos biztonsági gyakorlat, hogy rosszindulatú USB-porttal, valamint egyéb fizikai hozzáférés elleni védelem érdekében. Fizikai hozzáférést, például USB-port használatát az egyik kulcsról a észlelésétől kapcsolatos problémák esetén fellépő bejelentkezik. Újra a Windows 10-es (IoT- és a többi termékváltozat) lehetővé teszi, hogy ezek az események részletes naplózás.

* **Felhőhöz tartozó hitelesítő adatok védelme**: Felhőbeli hitelesítő adatok konfigurálása és a egy IoT-környezet működtetése is esetleg hozzáférést és IoT-rendszer veszélyeztetheti a legegyszerűbb módja. A hitelesítő adatok védelme érdekében gyakran változnak a jelszót, és ne használja ezeket a hitelesítő adatokat nyilvános gépeken.

Különböző IoT-eszközök képességei eltérőek lehetnek. Bizonyos eszközök közös asztali operációs rendszert futtató számítógépeken, és előfordulhat, hogy a bizonyos eszközök rendkívül leegyszerűsített operációs rendszert futtató. A leírt ajánlott biztonsági eljárások korábban ezek az eszközök különböző mértékben alkalmazandó lehet. Ha meg van adva, további biztonsági és telepítési ajánlott eljárásait, ezek az eszközök gyártók kell követni.

Egyes régebbi és a korlátozott eszközök előfordulhat, hogy nem készített kifejezetten az IoT-környezet számára. Ezek az eszközök előfordulhat, hogy nem áll rendelkezésre az adatok titkosítását, az internettel való csatlakozáshoz, vagy adja meg a speciális naplózási képességét. Ezekben az esetekben modern és biztonságos helyszíni átjáró összesített adatok örökölt eszközökről, és a biztonságot, ezek az eszközök az interneten keresztül csatlakozó szükséges. Helyszíni átjárók biztosíthat biztonságos hitelesítési, a titkosított munkamenetek egyeztetését, a felhőben, és számos egyéb biztonsági funkciókról a parancsok fogadását.