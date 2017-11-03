---
title: "Indítási feladatok futtatása az Azure Felhőszolgáltatások |} Microsoft Docs"
description: "Indítási feladatok segítségével, az alkalmazás a cloud service-környezet előkészítéséhez. Ez útmutatást ad, hogy indítási feladatok működése, és hogyan végezheti el őket"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Hogyan lehet konfigurálni és egy felhőalapú szolgáltatás indítási feladatok futtatása
Használhatja az indítási feladatok műveletek végrehajtásához, a szerepkör indítása előtt. Végrehajtani kívánt műveletek közé tartozik egy összetevő telepítésével, COM-összetevők regisztrálása, beállításkulcsok vagy hosszú ideig futó folyamat elindítása.

> [!NOTE]
> Indítási feladatok nem alkalmazhatók, a virtuális gépek, csak a felhőalapú szolgáltatás webes és feldolgozói szerepköröket.
> 
> 

## <a name="how-startup-tasks-work"></a>Indítási feladatok működése
Indítási feladatokat is a szerepkörök kezdődik, és meghatározott előtt végzett műveleteket a [ServiceDefinition.csdef] fájl használatával a [feladat] elemet a [indítási] elem. Indítási feladatok gyakran parancsfájlokat, de ezek is lehetnek konzol alkalmazások, vagy indítsa el a PowerShell-parancsfájlok, kötegelt fájlok.

Környezeti változók adhatók át információk egy indítási tevékenységhez, és helyi tárterület adhatók át információk egy indítási tevékenységhez kívül is használható. Például egy környezeti változó adhatja meg a telepíteni kívánt program elérési útja, és a fájlok csak írható helyi tárhelyet tudja majd olvasni később a szerepkörök.

Az indítási tevékenységhez bejelentkezhetnek adatok és a hibák által megadott könyvtárban a **TEMP** környezeti változó. Az indítási feladat során a **TEMP** környezeti változó feloldása egy olyan a *C:\\erőforrások\\temp\\[guid]. [ rolename]\\RoleTemp* directory, a felhő futtatásakor.

Indítási feladatok is hajtható végre több alkalommal újraindításnál. Például az indítási lesz futtatható a feladat minden alkalommal, amikor a szerepkör újraindul, és a szerepkör újrahasznosítja azt mindig nem tartalmazhatnak újraindítás. Indítási feladatok oly módon, amely lehetővé teszi több alkalommal problémamentesen kell írni.

Indítási feladatok végén szerepelnie kell egy **errorlevel** (vagy a kilépési kód) nulla a rendszerindítási folyamat befejezéséhez. Ha egy indítási tevékenységhez végződik-e egy nem nulla **errorlevel**, a szerepkör nem indul el.

## <a name="role-startup-order"></a>Szerepkör indítási sorrend
Az alábbi lista tartalmazza a szerepkör indítási eljárás az Azure-ban:

1. A példány van megjelölve, **indítása** és nem kap a forgalmat.
2. Minden indítási feladatok végrehajtásának megfelelően a **taskType** attribútum.
   
   * A **egyszerű** feladatok végrehajtásának szinkron módon történik, egyenként.
   * A **háttér** és **előtér** feladatok indított aszinkron módon történik, párhuzamosan a indítási tevékenységhez.  
     
     > [!WARNING]
     > Az IIS nem teljesen konfigurálható a rendszerindítási folyamat indítási tevékenységhez szakaszában, a szerepkör-specifikus adatok nem érhetők el. Indítási feladatokhoz szükséges szerepkör-specifikus adatok használandó [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. A szerepkör gazdagép-folyamat elindul, és a webhely létrehozása IIS-ben.
4. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) metódust.
5. A példány van megjelölve, **készen** és a példányra továbbítódik.
6. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) metódust.

## <a name="example-of-a-startup-task"></a>Példa egy indítási tevékenységhez
Indítási feladatok vannak definiálva a [ServiceDefinition.csdef] fájlban, az a **feladat** elemet. A **commandLine** attribútum határozza meg, a név és a paraméterek az indítási kötegelt fájl vagy a konzol parancs, a **executionContext** attribútum meghatározza, hogy az indítási feladat a jogosultsági szint és a **taskType** attribútum határozza meg a feladat végrehajtásának módját.

Ebben a példában, egy környezeti változó **MyVersionNumber**, az indítási tevékenységhez hoz létre, az értéke "**1.0.0.0**".

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

A következő példában a **Startup.cmd** parancsfájlt ír a sor "a jelenlegi verzió: 1.0.0.0" StartupLog.txt fájl a TEMP környezeti változóban megadott könyvtárban. A `EXIT /B 0` sor biztosítja, hogy az indítási tevékenységhez végződik egy **errorlevel** nulla.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> A Visual Studio a **másolása a kimeneti könyvtárba** kell megadni a indítási kötegfájl tulajdonsága **másolási mindig** győződjön meg arról, hogy az indítási kötegfájl megfelelően a rendszer a projekthez az Azure (**approot\\bin** webes szerepkör, és **approot** feldolgozói szerepkörök).
> 
> 

## <a name="description-of-task-attributes"></a>A feladat attribútumok leírása
A következő szakasz ismerteti a attribútumait a **feladat** eleme a [ServiceDefinition.csdef] fájlt:

**commandLine** -az indítási tevékenységhez tartozó parancssort határozza meg:

* A parancs, opcionális parancssori paraméterek, amely megkezdi az indítási tevékenységhez.
* Ez gyakran a .cmd és .bat kötegelt fájl nevét.
* A feladata a AppRoot viszonyítva\\a központi telepítési mappáját. Környezeti változók meghatározni, hogy az elérési útját és a feladat nem bonthatók ki. A környezeti bővítésének szükség, ha egy kis .cmd parancsfájl, amely behívja a indítási tevékenységhez is létrehozhat.
* A Konzolalkalmazás vagy egy parancsfájlt, amely elindítja a [PowerShell-parancsfájl](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** -adja meg a jogosultsági szint az indítási tevékenységhez. A jogosultsági szint korlátozza, vagy magasabb szintű:

* **korlátozott**  
  Az indítási feladat fut, a szerepkör jogosultságainak. Ha a **executionContext** az attribútum a [futásidejű] elem is **korlátozott**, akkor használja a felhasználói jogosultságok.
* **emelt szintű**  
  Az indítási tevékenységhez rendszergazdai jogosultságokkal futtatja. Ez lehetővé teszi, hogy indítási programok telepítése, az IIS-konfigurációs módosításokat, hajtsa végre a beállításjegyzék módosításait, és más rendszergazdai szintű feladatok, a szerepkör magát a jogosultsági szint növelése nélkül.  

> [!NOTE]
> A jogosultsági szint indítása feladat nem lehet azonos a szerepkör kell.
> 
> 

**taskType** -indítási feladat végrehajtásának módját határozza meg.

* **egyszerű**  
  Feladatok végrehajtásának szinkron módon történik, egyenként, a megadott sorrendben a [ServiceDefinition.csdef] fájlt. Ha egy **egyszerű** indítási tevékenységhez végződik egy **errorlevel** nulla, a következő **egyszerű** indítási feladat végrehajtása. Ha nem látható több **egyszerű** indítási feladatok végrehajtásához, majd a szerepkör indul el.   
  
  > [!NOTE]
  > Ha a **egyszerű** feladat végződik. egy nem nulla **errorlevel**, a példány le lesz tiltva. További **egyszerű** indítási feladatokat, és a szerepkör nem fog elindulni.
  > 
  > 
  
    Annak érdekében, hogy a kötegfájl végződik egy **errorlevel** nulla, a parancs végrehajtása `EXIT /B 0` a kötegelt fájl folyamat végén.
* **háttér**  
  Feladatok aszinkron módon történik, az indítási szerepkör párhuzamosan végrehajtásának.
* **előtérben**  
  Feladatok aszinkron módon történik, az indítási szerepkör párhuzamosan végrehajtásának. A fő különbség a **előtér** és egy **háttér** feladat, hogy egy **előtér** feladat megakadályozza, hogy a szerepkör újrahasznosítása vagy leállítása, amíg a feladat befejeződött. A **háttér** feladatok nincs ezt a korlátozást.

## <a name="environment-variables"></a>Környezeti változók
Környezeti változókat, amelyek olyan információkat adnak át egy indítási tevékenységhez. Az elérési út helyezhetik például blob, amely tartalmazza a program telepítéséhez, vagy a szerepkör által használt portszámok vagy elérhető szolgáltatások vezérléséhez a indítási feladat beállításait.

Nincsenek kétféle környezeti változók indítási feladatokhoz; statikus környezeti változókat és a környezeti változók alapján tagjai a [roleenvironment-et] osztály. A mindkettő a [környezet] szakasza a [ServiceDefinition.csdef] fájlt, és mindkét használja a [változó] elem és **neve** az attribútum.

Statikus környezeti változókat használ a **érték** attribútuma a [változó] elemet. A fenti példában a környezeti változót hoz **MyVersionNumber** statikus értékre van, amely "**1.0.0.0**". Egy másik példa lehet létrehozni egy **StagingOrProduction** környezeti változó, amely kézzel állítható értékeire "**átmeneti**"vagy"**éles**" végrehajtásához különböző indítási műveletek alapján értékének a **StagingOrProduction** környezeti változó.

Ne használjon környezeti változókat a roleenvironment-et osztály tagjai alapján a **érték** attribútuma a [változó] elemet. Ehelyett a [RoleInstanceValue] gyermekelem, a megfelelő **XPath** attribútum-érték, egy környezeti változó, egy adott tagjához alapú létrehozásához használt a [roleenvironment-et] osztály. Értékei a **XPath** attribútum különböző eléréséhez [roleenvironment-et] értékek található [Itt](cloud-services-role-config-xpath.md).

Ahhoz például, hogy hozzon létre egy környezeti változó, amely "**igaz**" Ha a példány fut. a compute emulator és "**hamis**" Ha fut a felhőben, használja a következő [változó] és [RoleInstanceValue] elemei:

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

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan végezhető el [közös indítási feladatok](cloud-services-startup-tasks-common.md) a felhőalapú szolgáltatással.

[Csomag](cloud-services-model-and-package.md) a felhőalapú szolgáltatás.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[feladat]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[indítási]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[futásidejű]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[roleenvironment-et]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
