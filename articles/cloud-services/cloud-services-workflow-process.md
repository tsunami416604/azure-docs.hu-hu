---
title: A Windows Azure virtuálisgép-architektúra munkafolyamata | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a munkafolyamat-folyamatokról egy szolgáltatás üzembe helyezésekor.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162146"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>A Klasszikus Windows Azure virtuálisgépek architektúrájának munkafolyamata 
Ez a cikk áttekintést nyújt a munkafolyamat-folyamatok, amelyek akkor jelentkeznek, amikor egy Azure-erőforrás, például egy virtuális gép telepítése vagy frissítése. 

> [!NOTE]
>Az Azure két különböző üzembe helyezési modellt hoz létre és dolgozik az erőforrásokkal: Erőforrás-kezelő és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az alábbi ábra az Azure-erőforrások architektúráját mutatja be.

![Azure-munkafolyamat](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>A munkafolyamat alapjai
   
**A.** RDFE / FFE a kommunikációs útvonal a felhasználó tól a hálóhoz. RDFE (RedDog Front End) a nyilvánosan elérhető API, amely a felügyeleti portál és a Service Management API előtér-, például a Visual Studio, az Azure MMC és így tovább.  A felhasználó tól érkező összes kérés az RDFE-n keresztül megy. FFE (Fabric Front End) az a réteg, amely lefordítja az RDFE-ből származó kérelmeket hálóparancsokká. Az RDFE minden kérése az FFE-n keresztül érhető el, hogy elérje a hálóvezérlőket.

**B.** A hálóvezérlő felelős az adatközpont összes erőforrásának karbantartásáért és figyeléséért. Kommunikál a hálógazdagép-ügynökökkel a háló operációs rendszer ének küldése, például a vendég operációsrendszer-verzió, a szolgáltatáscsomag, a szolgáltatás konfigurációja és a szolgáltatás állapota.

**C.** A gazdaügynök él a gazdaoperációs rendszer, és felelős a vendég operációs rendszer beállítása és a vendégügynök (WindowsAzureGuestAgent) annak érdekében, hogy frissítse a szerepkör felé a tervezett cél állapot, és nem szívverés ellenőrzéseket a vendégügynök. Ha az állomásügynök 10 percig nem kap szívverési választ, az állomásügynök újraindítja a vendég operációs rendszert.

**C2**. A WaAppAgent felelős a WindowsAzureGuestAgent.exe telepítéséért, konfigurálásáért és frissítéséért.

**Sz .**  A WindowsAzureGuestAgent felelős a következőkért:

1. A vendég operációs rendszer konfigurálása, beleértve a tűzfalat, az ACL-eket, a LocalStorage erőforrásokat, a szolgáltatáscsomagot és -konfigurációt, valamint a tanúsítványokat.
2. Annak a felhasználói fióknak a biztonsági azonosítójának beállítása, amely alatt a szerepkör futni fog.
3. A szerepkör állapotának kommunikálása a hálóval.
4. WaHostBootstrapper indítása és figyelése annak érdekében, hogy a szerepkör a cél állapotban van.

**E.** WaHostBootstrapper felelős:

1. A szerepkör-konfiguráció olvasása, valamint a szerepkör konfigurálásához és futtatásához szükséges összes feladat és folyamat elindítása.
2. Az összes gyermekfolyamat figyelése.
3. A StatusCheck esemény felemelése a szerepkörgazda folyamatán.

**F.** Az IISConfigurator akkor fut, ha a szerepkör teljes IIS webes szerepkörként van konfigurálva (nem fog futni az SDK 1.2 HWC szerepkörökhöz). Felelős a következőkért:

1. A szabványos IIS-szolgáltatások elindítása
2. Az újraíró modul konfigurálása a webes konfigurációban
3. Az AppPool beállítása a szolgáltatásmodellben beállított szerepkörhöz
4. Az IIS-naplózás beállítása a DiagnosticStore LocalStorage mappára mutat
5. Engedélyek és Hozzáférés-engedélyek konfigurálása
6. A webhely a %roleroot%:\sitesroot\0 mappában található, és az AppPool erre a helyre mutat az IIS futtatásához. 

**G.** Az indítási feladatokat a szerepkörmodell határozza meg, és a WaHostBootstrapper indítja el. Az indítási feladatok beállíthatók úgy, hogy a háttérben aszinkron módon fussanak, és a gazdagép bootstrapper elindítja az indítási feladatot, majd folytatja a többi indítási feladatot. Az indítási feladatok egyszerűen (alapértelmezett) módban is konfigurálhatók, amelyben a gazdagép bootstrapper megvárja, amíg az indítási feladat befejeződik, és sikeres (0) kilépési kódot ad vissza, mielőtt folytatná a következő indítási feladatot.

**H**. Ezek a feladatok az SDK részét képezik, és a szerepkör szolgáltatásdefiníciójában (.csdef) bővítményként vannak definiálva. Indítási feladatokká bontva a **DiagnosticsAgent** és a **RemoteAccessAgent** egyedi, mivel mindegyik két indítási feladatot határoz meg, egy normál és egy **/blockStartup** paramétert. A normál indítási feladat háttérindítási feladatként van definiálva, így a háttérben is futtatható, miközben maga a szerepkör fut. A **/blockStartup** indítási feladat egyszerű indítási feladatként van definiálva, így a WaHostBootstrapper a folytatás előtt megvárja, amíg kilép. A **/blockStartup** feladat megvárja, amíg a normál feladat inicializálása befejeződik, majd kilép, és engedélyezi a gazdagép bootstrapper folytatását. Ez úgy történik, hogy a diagnosztika és az RDP-hozzáférés konfigurálható a szerepkör-folyamatok megkezdése előtt (ez a /blockStartup feladaton keresztül történik). Ez azt is lehetővé teszi, hogy a diagnosztika és az RDP-hozzáférés továbbra is fut, miután a gazdagép bootstrapper befejezte az indítási feladatokat (ez a Normál feladaton keresztül történik).

**Én... nem tudom, mit mondjak** WaWorkerHost a normál feldolgozói szerepkörök szabványos gazdagépfolyamata. Ez a gazdafolyamat a szerepkör összes DEL-kódját és belépési pontkódját,például az OnStart és a Run-t üzemelteti.

**J.** WaWebHost a szabványos gazdagép folyamat webes szerepkörök, ha úgy vannak beállítva, hogy az SDK 1.2-kompatibilis Hostable Web Core (HWC). A szerepkörök úgy engedélyezhetik a HWC módot, hogy eltávolítja az elemet a szolgáltatásdefinícióból (.csdef). Ebben a módban a szolgáltatás összes kódja és DL-je a WaWebHost folyamatból fut. Az IIS (w3wp) nincs használatban, és nincsenek konfigurált AppPool-ok az IIS-kezelőben, mert az IIS a WaWebHost.exe webhelyen található.

**K.** A WaIISHost a teljes IIS-t használó webes szerepkörök szerepkör-belépési pontkódjának gazdafolyamata. Ez a folyamat betölti az első DLL-t, amely a **RoleEntryPoint** osztályt használja, és végrehajtja a kódot ebből az osztályból (OnStart, Run, OnStop). A **RoleEntryPoint** osztályban létrehozott RoleEnvironment események (például StatusCheck és Changed) ebben a folyamatban jelennek meg.

**L.** A W3WP az a szabványos IIS munkavégző folyamat, amelyakkor használatos, ha a szerepkör a Teljes IIS használatára van konfigurálva. Ez az IISConfigurator-ból konfigurált AppPool-t futtatja. Az itt létrehozott RoleEnvironment események (például statuscheck és módosított) ebben a folyamatban jelennek meg. Vegye figyelembe, hogy a RoleEnvironment események mindkét helyen (WaIISHost és w3wp.exe) indulnak el, ha mindkét folyamat eseményeire előfizet.

## <a name="workflow-processes"></a>Munkafolyamat-folyamatok

1. A felhasználó kérést küld, például ".cspkg" és ".cscfg" fájlokat tölt fel, és azt mondja egy erőforrásnak, hogy álljon le, vagy változtasson a konfiguráción, és így tovább. Ez az Azure Portalon vagy a Service Management API-t használó eszköz, például a Visual Studio közzétételi szolgáltatássegítségével végezhető el. Ez a kérés az RDFE-hez kerül az előfizetéssel kapcsolatos összes munka teljesítéséhez, majd a kérés nek az FFE-nek való közléséhez. A munkafolyamat további lépései egy új csomag üzembe helyezése és indítása.
2. Az FFE megkeresi a megfelelő gépkészletet (az ügyfél-bevitel alapján, például affinitáscsoport vagy földrajzi hely, valamint a hálóbemenet, például a gép rendelkezésre állása), és kommunikál a fő hálóvezérlővel a gépkészletben.
3. A hálóvezérlő megkeresi a rendelkezésre álló CPU-magokkal (vagy egy új állomást) tartalmazó állomást. A szolgáltatáscsomag és a konfiguráció másolva az állomásra, és a háló vezérlő kommunikál a gazdaügynök a gazdaoperációs rendszer a csomag központi telepítéséhez (konfigurálása DIPs, portok, vendég operációs rendszer, és így tovább).
4. A gazdaügynök elindítja a vendég operációs rendszert, és kommunikál a vendégügynökkel (WindowsAzureGuestAgent). Az állomás szívveréseket küld a vendégnek, hogy megbizonyosodjon arról, hogy a szerepkör a célállapot felé halad.
5. A WindowsAzureGuestAgent beállítja a vendég operációs rendszert (tűzfal, ACLs, LocalStorage és így tovább), átmásol egy új XML-konfigurációs fájlt a c:\Config mappába, majd elindítja a WaHostBootstrapper folyamatot.
6. A teljes IIS webes szerepkörök esetében a WaHostBootstrapper elindítja az IISConfiguratort, és közli vele, hogy törölje a webes szerepkörhöz már meglévő AppPoolokat az IIS-ből.
7. A WaHostBootstrapper beolvassa az **Indítási** feladatokat az E:\RoleModel.xml fájlból, és megkezdi az indítási feladatok végrehajtását. WaHostBootstrapper megvárja, amíg az összes egyszerű indítási feladatok befejeződtek, és visszatért a "siker" üzenetet.
8. A teljes IIS webes szerepkörök esetében a WaHostBootstrapper megmondja az IISConfiguratornak, hogy konfigurálja az IIS AppPoolt, és a webhelyet a , a `E:\Sitesroot\<index>` `<index>` pontjára pedig a 0 alapú indexet a szolgáltatáshoz definiált `<Sites>` elemek számába.
9. A WaHostBootstrapper a szerepkör típusától függően elindítja a gazdafolyamatot:
    1. **Feldolgozói szerepkör**: A WaWorkerHost.exe elindult. A WaHostBootstrapper végrehajtja az OnStart() metódust. Miután visszatér, WaHostBootstrapper elindítja a Run() metódus végrehajtását, majd egyidejűleg megjelöli a szerepkört Ready, és behelyezi a terheléselosztó rotációjába (ha az InputEndpoints definiálva van). WaHostBootsrapper majd bemegy egy hurok ellenőrzése a szerep állapotát.
    1. **SDK 1.2 HWC webes szerepkör**: WaWebHost elindul. A WaHostBootstrapper végrehajtja az OnStart() metódust. Miután visszatér, WaHostBootstrapper elindítja a Run() metódus végrehajtását, majd egyidejűleg megjelöli a szerepkört Ready, és behelyezi a terheléselosztó rotációba. A WaWebHost bemelegedési kérelmet ad ki (GET /do.rd_runtime_init). Minden webes kérést a WaWebHost.exe küld. WaHostBootsrapper majd bemegy egy hurok ellenőrzése a szerep állapotát.
    1. **Teljes IIS webes szerepkör:** az aIISHost elindult. A WaHostBootstrapper végrehajtja az OnStart() metódust. Miután visszatér, megkezdi a Run() metódus végrehajtását, majd ezzel egyidejűleg készen jelöli meg a szerepkört, és behelyezi a terheléselosztó elforgatásába. WaHostBootsrapper majd bemegy egy hurok ellenőrzése a szerep állapotát.
10. A Teljes IIS webes szerepkörbe érkező webes kérelmek elindítják az IIS-t a W3WP-folyamat elindításához és a kérés kiszolgálásához, ugyanúgy, mint egy helyszíni IIS-környezetben.

## <a name="log-file-locations"></a>Naplófájl helyei

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ez a napló a szolgáltatás módosításait tartalmazza, beleértve az indításokat, a leállásokat és az új konfigurációkat. Ha a szolgáltatás nem változik, akkor nagy időbeli szünetek rejlehetnek ebben a naplófájlban.
- C:\Logs\WaAppAgent.Log.  
Ez a napló állapotfrissítéseket és szívverési értesítéseket tartalmaz, és 2-3 másodpercenként frissül.  Ez a napló a példány állapotának egy korábbi nézetét tartalmazza, és megmondja, hogy a példány mikor nem volt Kész állapotban.
 
**WaHostBootstrapper között**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost (WaWebHost)**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost között**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISKonátor**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-naplók**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows eseménynaplók**

`D:\Windows\System32\Winevt\Logs`
 



