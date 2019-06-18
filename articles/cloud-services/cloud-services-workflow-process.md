---
title: A Windows Azure virtuális gép architektúra munkafolyamat |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt az a munkafolyamatokat, a szolgáltatás telepítésekor.
services: cloud-services
documentationcenter: ''
author: genlin
manager: Willchen
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 7c8459a6694663a49203b6ec21a760d3e6bd60c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480747"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure klasszikus virtuális gép architektúra munkafolyamata 
Ez a cikk áttekinti a munkafolyamatok, amelyek üzembe helyezésekor, vagy ha például egy virtuális gép egy Azure-erőforrás frissítése. 

> [!NOTE]
>Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: Resource Manager és klasszikus. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az alábbi ábrán az Azure-erőforrások architektúráját mutatja be.

![Azure-munkafolyamatok](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>A munkafolyamat alapjai
   
**A**. Az RDFE FFE van a kommunikációs útvonal a felhasználó a hálóhoz. Az RDFE (RedDog Front End) a nyilvánosan elérhető API-t, amely a kezelési portálra az előtér és a Service Management API, például a Visual Studio Azure MMC és így tovább.  A felhasználó érkező kérelmek mennek keresztül rdfe-ben. FFE (Fabric előtérbeli), amely a, amely a fabric-parancsok az RDFE érkező kérelmeket. Az RDFE érkező kérelmek próbálja ki a FFE a háló elérni a tartományvezérlőket.

**B**. A hálóvezérlő felelős karbantartásáért, valamint a figyelést az adatközpontban lévő összes erőforrást. A háló operációs rendszer küldő adatokat például a vendég operációs rendszer verziója, szolgáltatás, szolgáltatáskonfiguráció és szolgáltatás állapota a fabric host ügynökökkel kommunikál.

**C**. A gazdagép-ügynök szigeten él a gazdagép OSsystem, és a vendég operációs rendszer beállítása felelős, és a vendégügynökkel folytatott kommunikáció-Vendégügynök (WindowsAzureGuestAgent) annak érdekében, hogy a szerepkör felé az importálni kívánt cél állapot frissítése, és végezze el a Szívveréses ellenőrzi. Ha a gazdagép-ügynök nem kapja meg szívverés válasz 10 percig, a gazdagép-ügynök a vendég operációs rendszer újraindul.

**C2**. WaAppAgent felelős telepítése, konfigurálása és WindowsAzureGuestAgent.exe frissítése.

**D**.  WindowsAzureGuestAgent felelős a következőket:

1. A vendég operációs rendszer, többek között a tűzfal, hozzáférés-vezérlési listák, LocalStorage erőforrások, szolgáltatás és konfigurációs és tanúsítványok konfigurálása. A felhasználói fiókkal, a szerepkör alatt kerül futtatásra biztonsági AZONOSÍTÓJÁNAK beállítása.
2. A kommunikáció a szerepkör állapota a hálóhoz.
3. WaHostBootstrapper indítása és a figyelés, győződjön meg arról, hogy a szerepkör cél állapotban van.

**E**. WaHostBootstrapper felelős:

1. A szerepkör-konfigurációjának olvasása, és a megfelelő feladatokat és folyamatokat állíthat be és futtathat a szerepkör indítása.
2. A gyermek-folyamatok figyelése.
3. A StatusCheck eseményt a szerepköri gazdafolyamata előléptetése.

**F**. A szerepkör van konfigurálva (nem fog futni az SDK 1.2 üzemeltethető WEBMAG szerepkörök) teljes IIS webes szerepkörként fut IISConfigurator. Felelős:

1. A standard szintű IIS-szolgáltatások indítása
2. Az újraírási modul konfigurálása a webes konfiguráció
3. Az alkalmazáskészlet a modell a konfigurált szerepkörének beállítása
4. A DiagnosticStore LocalStorage mappa átirányítása az IIS naplózás beállítása
5. Engedélyek és hozzáférés-vezérlési listák konfigurálása
6. A webhelyen található % roleroot%:\sitesroot\0, valamint az ezen a helyen, az IIS-t futtató apppool mutat. 

**G**. Indítási feladatok határozzák meg a szerepkör-modell és WaHostBootstrapper indította. Indítási feladatok beállítható úgy, hogy az aszinkron módon fut a háttérben, és a gazdagép rendszerindító elindítja az indítási feladat, és egyéb indítási feladatok majd folytassa. Indítási feladatok is konfigurálható, amelyben a gazdagép rendszerindító megvárja az indítási feladat futása befejeződik, és a egy sikeres (0) kilépési kóddal tér vissza a folytatás előtt a következő indítási feladat, egyszerű (alapértelmezett) módban való futtatására.

**H**. Ezeket a feladatokat az SDK részét képezik, és a szerepkör-szolgáltatás definíciós (.csdef) a beépülő modulok vannak meghatározva. Ha ki van bontva, az indítási feladatok, a **DiagnosticsAgent** és **RemoteAccessAgent** különlegessége az, hogy ezek mindegyike két indítási feladatokról, egy normál és azt, amelynek meghatározása egy **/blockStartup** paraméter. A normál indítási feladat indítási háttérfeladatként van definiálva, hogy akkor is fut a háttérben magát a szerepkör futása közben. A **/blockStartup** indítási feladat egy egyszerű indítási feladatként van definiálva, így WaHostBootstrapper megvárja, hogy a folytatás előtt lépjen ki. A **/blockStartup** feladat megvárja, amíg a rendszeres feladat inicializálása befejeződik, és ezután kilép, és folytatja a gazdagép rendszerindító engedélyezése. Ez történik, hogy a diagnosztikai és RDP-hozzáférés konfigurálható (ebben az esetben a /blockStartup feladat keresztül) szerepkör eljárások megkezdése előtt. Ez lehetővé teszi diagnosztikai és RDP-hozzáférésének továbbra is fut, a gazdagép rendszerindító befejezése után az indítási feladatok (ez történik, a Normál tevékenység keresztül).

**E**. WaWorkerHost a normál feldolgozói szerepkörök a standard gazdafolyamat. A gazdagép folyamat üzemelteti, a szerepkör összes DLL-EK és a belépési pont kódot, mint például az ONSTART érvényesség, és futtassa.

**J**. WaWebHost webes szerepkörök esetében a standard szintű gazdafolyamat esetén az SDK 1.2-kompatibilis Hostable Web Core (üzemeltethető WEBMAG) használatára vannak konfigurálva. Szerepkörök az üzemeltethető WEBMAG mód engedélyezéséhez távolítja el az elemet a szolgáltatás definíciós (.csdef). Ebben a módban a szolgáltatást kód és a DLL-ek futtassa a WaWebHost folyamat. (W3wp) az IIS nincs használatban, és nincsenek nincs konfigurálva az IIS-kezelőben, mert az IIS WaWebHost.exe belül található alkalmazáskészletek.

**K**. WaIISHost is the host process for role entry point code for web roles that use Full IIS. Ez a folyamat az első DLL talált, amely betölti a **RoleEntryPoint** osztályt, és végrehajtja a kódot ebből az osztályból (ONSTART érvényesség, futtassa, OnStop). Bármely **RoleEnvironment** az RoleEntryPoint osztályban létrehozott esemény (például StatusCheck és értékre módosult) a folyamat aktiválódnak.

**L**. W3wp a standard szintű IIS feldolgozó folyamatot, akkor használatos, ha a szerepkör teljes IIS használatára van konfigurálva. Ez az alkalmazáskészlet konfigurált IISConfigurator futtatja. Ez a folyamat rendszerben előállított RoleEnvironment eseményeket (például StatusCheck és értékre módosult), amelyet itt hozott létre. Vegye figyelembe, hogy RoleEnvironment események aktiválódnak mindkét helyen (WaIISHost és w3wp.exe), ha mindkét folyamat eseményeinek fizet.

## <a name="workflow-processes"></a>A munkafolyamatok

1. A felhasználó kérést küld, például .cspkg és a .cscfg-fájlok feltöltésével, leállítására erőforrás közli, vagy egy konfigurációs módosítást, és így tovább. Ezt megteheti az Azure Portalon vagy egy eszközt, amely a Service Management API, például a Visual Studio közzététel funkciót. A kérelem ehhez az RDFE kerül minden az előfizetéshez kapcsolódó működnek, és a kérést FFE létesíthet kommunikációt. Az alábbi munkafolyamat lépések a többi olyan telepítsen központilag egy új csomagot, és indítsa el.
2. FFE megkeresi a megfelelő gépkészlet (a affinitáscsoportban vagy földrajzi hely, emellett a bemenetet a hálóhoz, például a gép rendelkezésre állási a ilyen, felhasználói bemenet alapján), és a gépkészlet a fő hálóvezérlő kommunikál.
3. A hálóvezérlő talál olyan gazdagépet, amely rendelkezik a rendelkezésre álló processzormagok (vagy terveket a jövőre beállítása egy új gazdagépre). A service-csomagot és a konfigurációs van másolja a gazdagépen, és a hálóvezérlő kommunikál a gazda operációs rendszer telepítéséhez a csomagot a gazdagép-ügynök (DIP, portok, a vendég operációs rendszer és így tovább konfigurálása).
4. A gazdagép-ügynök elindítja a vendég operációs rendszer, és kommunikál a vendégügynök (WindowsAzureGuestAgent). A gazdagép, győződjön meg arról, hogy működik-e a szerepkör állapotában cél felé, a Vendég szívveréseket küld.
5. WindowsAzureGuestAgent állítja be a vendég operációs rendszer (tűzfal, hozzáférés-vezérlési listák, LocalStorage és így tovább), másolja egy új konfigurációs XML-fájl c:\Config és majd megkezdi a WaHostBootstrapper.
6. Az IIS teljes webes szerepkörök esetében a WaHostBootstrapper IISConfigurator elindul, és utasítja, hogy törli a webes szerepkör minden olyan meglévő alkalmazáskészletek az IIS.
7. WaHostBootstrapper beolvassa a **indítási** E:\RoleModel.xml a feladatokat, és az indítási feladatok végrehajtásának. WaHostBootstrapper megvárja, amíg az összes egyszerű indítási feladat befejeződött és a "sikeres" üzenetet adja vissza.
8. Az IIS teljes webes szerepkörök esetében WaHostBootstrapper IISConfigurator konfigurálása az IIS-alkalmazáskészlet, ismerteti a kívánt helyre mutat `E:\Sitesroot\<index>`, ahol `<index>` egy 0 alapú index a megadott számú <Sites> elem van megadva, a szolgáltatás.
9. WaHostBootstrapper megkezdi a gazdagép a szerepkör típusától függően:
    1. **Feldolgozói szerepkör**: WaWorkerHost.exe el van indítva. WaHostBootstrapper az OnStart() metódus hajtja végre. Ad vissza, miután WaHostBootstrapper elindítja a Run() metódus végrehajtásához egyidejűleg jelöli meg a szerepkör készen áll, és elhelyezi azokat a load balancer rotációja (ha InputEndpoints van megadva). WaHostBootsrapper majd hiányzóra változik, a szerepkör állapotának ellenőrzése hurkot.
    1. **1.2-es SDK üzemeltethető WEBMAG webes szerepkör**: WaWebHost el van indítva. WaHostBootstrapper az OnStart() metódus hajtja végre. Adja vissza, miután WaHostBootstrapper elindítja a Run() metódus végrehajtásához és egyidejűleg jelöli meg a szerepkört, készen áll, és elhelyezi azokat a load balancer elforgatás. WaWebHost kibocsát egy bemelegítési kérelmet (GET /do.rd_runtime_init). Minden webes kérés küldése a WaWebHost.exe. WaHostBootsrapper majd hiányzóra változik, a szerepkör állapotának ellenőrzése hurkot.
    1. **Az IIS webes szerepkör teljes**: aIISHost elindult. WaHostBootstrapper az OnStart() metódus hajtja végre. Adja vissza, miután, elindul a Run() metódus végrehajtásához és egyidejűleg jelöli meg a szerepkör készen áll, és elhelyezi azokat a load balancer elforgatás. WaHostBootsrapper majd hiányzóra változik, a szerepkör állapotának ellenőrzése hurkot.
10. A beérkező webes kérelmek teljes IIS webes szerepkör eseményindítók a W3WP folyamat elindításához, és teljesíteni a kérést, ugyanaz, mint egy helyszíni IIS-környezet az IIS.

## <a name="log-file-locations"></a>Naplófájl helye

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Ez a napló, többek között a kezdődik, az leáll, és az új módosításokat tartalmaz. A szolgáltatás nem változik, ha várhatóan az idő nagy hiányosságok ebben a naplófájlban talál.
- C:\Logs\WaAppAgent.Log.  
Ez a napló ügyfélállapot-frissítés és a szívverés értesítések tartalmaz, és 2-3 másodpercenként frissül.  Ez a napló tartalmaz egy, a példány állapotát megtekintheti a végfelhasználókhoz, és jelzi, ha a példány nem üzemkész állapotban volt.
 
**WaHostBootstrapper**

C:\Resources\Directory\<deploymentID >.<role>. DiagnosticStore\WaHostBootstrapper.log
 
**WaWebHost**

C:\Resources\Directory\<guid>.<role>\WaWebHost.log
 
**WaIISHost**

C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log
 
**IISConfigurator**

C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log
 
**IIS-naplók**

C:\Resources\Directory\<GUID azonosítója >.<role>. DiagnosticStore\LogFiles\W3SVC1
 
**Windows-eseménynaplók**

D:\Windows\System32\Winevt\Logs
 



