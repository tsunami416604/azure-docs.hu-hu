---
title: Indítási feladatok futtatása az Azure Cloud Servicesben |} A Microsoft Docs
description: Indítási feladatok segítségével, az alkalmazás a cloud service-környezet előkészítése. Ez azzal foglalkozunk, hogy indítási feladatok működését, és hogyan javíthatja azokat
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeconnoc
ms.openlocfilehash: 59bfa83ab3432adb7a4df5112367f87014a0b292
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58917617"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Futtassa az indítási feladatok felhőszolgáltatásokhoz és konfigurálása
Indítási feladatok segítségével hajtsa végre a műveleteket a szerepkör elindítása előtt. Esetlegesen végrehajtani kívánt műveletek közé tartozik egy összetevő telepítése, COM-összetevők regisztrálása, beállításkulcsokat vagy hosszú ideig futó folyamat indítása.

> [!NOTE]
> Indítási feladatok nem vonatkoznak, a virtuális gépek, csak felhőalapú szolgáltatás webes és feldolgozói szerepkörök.
> 
> 

## <a name="how-startup-tasks-work"></a>Indítási feladatok működése
Indítási feladatok olyan műveletek, amelyeket a rendszer a szerepkörök megkezdése és meghatározott előtt a [ServiceDefinition.csdef] fájl használatával a [Tevékenység] elemen belül a [Indítás] elem. Gyakori indítási feladatok olyan a batch-fájlokat, de lehet is konzolon alkalmazásokat, vagy indítsa el a PowerShell-parancsfájlok, kötegfájlok.

A környezeti változók adhatók át információk egy indítási feladat, és helyi tárhely adhatók át információk egy indítási feladat nem használható. Például egy környezeti változóhoz adhatja meg a telepíteni kívánt program elérési útja, és a fájlok helyi tárba, amelyeket majd később a szerepkörök írhatók.

Az indítási feladat jelentkezhetnek adatok és a hibák által megadott könyvtárban a **TEMP** környezeti változót. Az indítási feladat során a **TEMP** környezeti változó feloldja a *C:\\erőforrások\\temp\\[guid]. [ rolename]\\RoleTemp* könyvtár, amikor a felhőben futó.

Az indítási feladatok két újraindítás között többször is végrehajthatók. Például az indítási feladat a szerepkör minden egyes újraindításakor fut, és a szerepkör-újraindítás pedig nem feltétlenül jár újraindítással. Indítási feladatok olyan módon, amely lehetővé teszi, hogy problémamentesen többször kell írni.

Indítási feladatok kell végződnie egy **errorlevel** (vagy kilépési kód) nulla az indítási folyamat befejezéséhez. Ha egy indítási feladat nullától eltérő végződik-e **errorlevel**, a szerepkör nem indul el.

## <a name="role-startup-order"></a>Szerepkör indítási sorrend
Az alábbi lista tartalmazza a szerepkör indítási eljárás az Azure-ban:

1. A példány van megjelölve **kezdő** forgalom nem kapják meg.
2. Az összes indítási feladatok végrehajtásának a következők szerint azok **taskType** attribútum.
   
   * A **egyszerű** feladatok végrehajtásának szinkron módon történik, egyenként.
   * A **háttér** és **előtérbeli** feladatok elindulnak, aszinkron módon, párhuzamosan a indítási feladatához.  
     
     > [!WARNING]
     > Az IIS nem teljes mértékben konfigurálható az indítási tevékenység szakasza a rendszerindítási folyamat során, előfordulhat, hogy a szerepkör-specifikus adatok nem érhető el. Indítási feladatok, amelyhez szükséges szerepkör-specifikus használjon [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. A szerepkör a gazdagép folyamat elindul, és a webhely létrehozása IIS-ben.
4. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) módszert hívja meg.
5. A példány van megjelölve **készen** és adatforgalmat a példányhoz.
6. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) módszert hívja meg.

## <a name="example-of-a-startup-task"></a>Egy indítási feladat – példa
Indítási feladatok vannak meghatározva a [ServiceDefinition.csdef] fájlban, az a **feladat** elemet. A **commandLine** attribútum meghatározza, hogy a név és a paraméterek az indítási batch fájl vagy -konzol parancs, a **executionContext** attribútum meghatározza, hogy az indítási feladat a jogosultsági szintet, és a **taskType** attribútum meghatározza, hogy a feladat végrehajtásának módját.

Ebben a példában egy környezeti változó **MyVersionNumber**, az indítási feladat létrehozása és értékre "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

A következő példában a **Startup.cmd** parancsfájlt ír a sor "jelenlegi verzió: 1.0.0.0" StartupLog.txt fájl a TEMP környezeti változó által megadott könyvtárban. A `EXIT /B 0` sor biztosítja, hogy az indítási feladat végződik- **errorlevel** nulla.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> A Visual Studióban a **Copy to Output Directory** kell állítani az indítási kötegfájlt tulajdonsága **mindig Másolás** győződjön meg arról, hogy az indítási kötegfájlt megfelelően üzemel az Azure-ban (aprojekthez**approot\\bin** webes szerepkörök esetében és **approot** a feldolgozói szerepkörök).
> 
> 

## <a name="description-of-task-attributes"></a>Feladatattribútumok leírása
A következő szakasz ismerteti a attribútumai a **feladat** eleme a [ServiceDefinition.csdef] fájlt:

**commandLine** – Itt adhatja meg az indítási tevékenység parancssora:

* A parancs nem kötelező parancssori paraméterek, amely megkezdi az indítási feladat.
* Ez gyakran a .cmd vagy .bat kötegfájl fájlneve.
* A feladat van az AppRoot könyvtárhoz viszonyítva\\Bin mappát a központi telepítéshez. A környezeti változók nem bonthatók ki az elérési útját és a tevékenység meghatározásában. Ha környezeti-bővítése szükséges, létrehozhat egy kis .cmd-parancsfájl, amely meghívja ezt az indítási feladat.
* Egy konzolalkalmazás vagy egy parancsfájlt, amely elindítja a [PowerShell-parancsprogram](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** – az indítási tevékenység a jogosultsági szintet határoz meg. A jogosultsági szint korlátozott lehet vagy emelt szintű:

* **limited**  
  Az indítási feladat fut, mint a szerepkör ugyanazokkal a jogosultságokkal. Ha a **executionContext** az attribútum a [Modul] elem is **korlátozott**, használja a rendszer a felhasználói jogosultságok.
* **elevated**  
  Az indítási feladat fut, rendszergazdai jogosultságokkal. Ez lehetővé teszi az indítási feladatok programok telepítése, az IIS-konfigurációs változtatásokat, elvégezheti a beállításjegyzék-módosításokat, és egyéb rendszergazdai szintű feladatokat, a szerepkör magát a jogosultsági szintet növelése nélkül.  

> [!NOTE]
> Egy indítási feladat a jogosultsági szintet nem lehet ugyanaz, mint maga a szerepkört kell.
> 
> 

**taskType** – Itt adhatja meg egy indítási feladat végrehajtásának módját.

* **Egyszerű**  
  Feladatok végrehajtásának szinkron módon történik, egyenként, a megadott sorrendben a [ServiceDefinition.csdef] fájlt. Ha egy **egyszerű** végződik indítási feladat egy **errorlevel** nulla, a következő **egyszerű** indítási feladat hajtja végre. Ha nem több **egyszerű** indítási feladatok végrehajtásához, majd magát a szerepkör indul.   
  
  > [!NOTE]
  > Ha a **egyszerű** feladat nullától eltérő végződik **errorlevel**, a példány le lesz tiltva. Ezt követő **egyszerű** indítási feladatokról és a szerepkör magát, nem fog elindulni.
  > 
  > 
  
    Annak érdekében, hogy a kötegfájlt végződik- **errorlevel** nulla, hajtsa végre a parancsot `EXIT /B 0` a batch-fájl folyamat végén.
* **background**  
  Feladatok aszinkron módon végrehajtásának párhuzamosan, a szerepkör elindítása.
* **előtér**  
  Feladatok aszinkron módon végrehajtásának párhuzamosan, a szerepkör elindítása. A fő különbség a között egy **előtér** és a egy **háttér** feladata, hogy egy **előtér** feladat megakadályozza, hogy a szerepkör újrahasznosítását vagy leáll, amíg a feladat befejeződött. A **háttér** feladatok nem rendelkezik ezzel a korlátozással.

## <a name="environment-variables"></a>Környezeti változók
A környezeti változók módon adhatók át információk egy indítási feladat. Például az elérési út lehet helyezni egy blobot, amely tartalmaz egy programot szeretne telepíteni, vagy a szerepkör által használt portszámok, vagy az indítási feladat funkcióinak vezérléséhez beállításai.

Két fajtája van az indítási feladatok; a környezeti változók statikus környezeti változókat és a környezeti változók alapján tagjai a [RoleEnvironment] osztály. A mindkettő a [környezet] szakaszában a [ServiceDefinition.csdef] fájlt, és mindkét használni a [A változó] elem és **neve** attribútum.

Statikus környezeti változókat használ a **érték** attribútuma a [A változó] elemet. A fenti példában a környezeti változót hoz **MyVersionNumber** egy állandó érték, amelynek "**1.0.0.0**". Egy másik példa lehet létrehozni egy **StagingOrProduction** környezeti változót, amely kézzel állítható be az értékét "**átmeneti**"vagy"**éles**" végrehajtásához eltérő indítási műveleteket értéke alapján a **StagingOrProduction** környezeti változót.

Ne használjon környezeti változókat a RoleEnvironment osztály tagjai alapján a **érték** attribútuma a [A változó] elemet. Ehelyett a [RoleInstanceValue] gyermekelemet, a megfelelő **XPath** attribútum értéke, egy adott tagjához alapú környezeti változó létrehozásához használt az [RoleEnvironment] osztály. Az értékek a **XPath** eléréséhez különböző attribútum [RoleEnvironment] értékek találhatók [Itt](cloud-services-role-config-xpath.md).

Például hozzon létre egy környezeti változó, amely "**igaz**" a compute emulatorban, a példány futása közben és "**hamis**" fut a felhőben, ha használja a következő [A változó] és [RoleInstanceValue] elemek:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan hajthat végre az egyes [gyakori indítási feladatok](cloud-services-startup-tasks-common.md) a felhőalapú szolgáltatáshoz.

[Csomag](cloud-services-model-and-package.md) a Felhőszolgáltatás.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tevékenység]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Indítás]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Modul]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[A változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
