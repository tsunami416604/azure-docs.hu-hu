---
title: Indítási feladatok futtatása az Azure Cloud Services szolgáltatásban | Microsoft dokumentumok
description: Az indítási feladatok segítenek előkészíteni a felhőszolgáltatási környezetet az alkalmazásszámára. Ez bemutatja, hogyan működnek az indítási feladatok, és hogyan lehet
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360310"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Indítási feladatok konfigurálása és futtatása felhőalapú szolgáltatáshoz
Az indítási feladatok segítségével műveleteket hajthat végre a szerepkör indítása előtt. A végrehajtandó műveletek közé tartozik egy összetevő telepítése, COM-összetevők regisztrálása, beállítási kulcsok beállítása vagy egy hosszú ideig futó folyamat elindítása.

> [!NOTE]
> Az indítási feladatok nem alkalmazhatók a virtuális gépekre, csak a Cloud Service Web és a Worker szerepkörökre.
> 
> 

## <a name="how-startup-tasks-work"></a>Az indítási feladatok működésének megmunkálata
Az indítási feladatok olyan műveletek, amelyeket a szerepkörök megkezdése előtt hajtanak végre, és amelyeket a [ServiceDefinition.csdef] fájlban az [Indítás] [elemEn] belüli Feladat elem használatával határoznak meg. Gyakran indítási feladatok kötegelt fájlokat, de lehetkonzolos alkalmazások, vagy kötegelt fájlokat, amelyek elindítják a PowerShell-parancsfájlok.

A környezeti változók adatokat továbbítanak egy indítási feladatba, és a helyi tároló segítségével adatokat továbbíthat nak egy indítási feladatból. Egy környezeti változó megadhatja például a telepíteni kívánt program elérési útját, és a fájlok at a helyi tárolóba írhatja, amelyeket később a szerepkörök elolvashatnak.

Az indítási feladat naplózhatja az adatokat és a hibákat a **TEMP** környezeti változó által megadott könyvtárba. Az indítási feladat során a **TEMP** környezeti változó a *C:\\Erőforrások\\ideiglenes hőmérséklete\\[guid][ rolename]\\RoleTemp* könyvtárat, amikor fut a felhőben.

Az indítási feladatok két újraindítás között többször is végrehajthatók. Például az indítási feladat a szerepkör minden egyes újraindításakor fut, és a szerepkör-újraindítás pedig nem feltétlenül jár újraindítással. Az indítási feladatokat úgy kell megírni, hogy azok problémamentesen fussanak többször is.

Az indítási feladatok nak nulla **hibaszinttel** (vagy kilépési kóddal) kell végződniük ahhoz, hogy az indítási folyamat befejeződjön. Ha egy indítási feladat nem nulla **hibaszinttel**végződik, a szerepkör nem indul el.

## <a name="role-startup-order"></a>Szerepkör indítási sorrendje
Az alábbi lista a szerepkör indítási eljárás az Azure-ban:

1. A példány **indításként** van megjelölve, és nem fogad forgalmat.
2. Minden indítási feladat végrehajtása a **taskType** attribútumuk szerint történik.
   
   * Az **egyszerű** feladatok végrehajtása szinkron módon történik, egyenként.
   * A **háttér-** és **előtérfeladatok** aszinkron módon, az indítási feladattal párhuzamosan indulnak el.  
     
     > [!WARNING]
     > Előfordulhat, hogy az IIS nincs teljesen konfigurálva az indítási feladat szakaszában az indítási folyamat során, ezért előfordulhat, hogy a szerepkör-specifikus adatok nem érhetők el. A szerepkörspecifikus adatokat igénylő indítási feladatoknak a [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))programot kell használniuk.
     > 
     > 
3. A szerepkör-gazdagép folyamata elindul, és a hely az IIS-ben jön létre.
4. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) metódus neve.
5. A példány **készként** van megjelölve, és a forgalom a példányhoz lesz irányítva.
6. A [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metódus neve.

## <a name="example-of-a-startup-task"></a>Példa indítási feladatra
Az indítási feladatok a [ServiceDefinition.csdef] fájlban, a **Feladat** elemben vannak definiálva. A **commandLine** attribútum az indítási kötegfájl vagy a konzolparancs nevét és paramétereit adja meg, a **executionContext** attribútum megadja az indítási feladat jogosultsági szintjét, a **taskType** attribútum pedig a feladat végrehajtásának módját.

Ebben a példában egy **myVersionNumber**környezeti változó jön létre az indítási feladathoz, és az **"1.0.0.0**" értékre van állítva.

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

A következő példában a **Startup.cmd** kötegfájl a TEMP környezeti változó által megadott könyvtárban lévő StartupLog.txt fájlba írja az "Aktuális verzió 1.0.0.0" sort. A `EXIT /B 0` sor biztosítja, hogy az indítási feladat nulla **hibaszinttel** végződjön.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> A Visual Studio,a **Másolás kimeneti könyvtárba** tulajdonság az indítási kötegfájl ban kell állítani a **Másolás mindig,** hogy megbizonyosodjon arról, hogy az indítási kötegfájl megfelelően telepítve van a projekt az Azure-ban **(approot\\bin** webes szerepkörök, és **approot** a feldolgozói szerepkörök).
> 
> 

## <a name="description-of-task-attributes"></a>Feladatattribútumok leírása
Az alábbiakban a [ServiceDefinition.csdef] fájl **Feladat** elemének attribútumait ismertetik:

**commandLine** - Az indítási feladat parancssorát adja meg:

* A parancs, választható parancssori paraméterekkel, amely elindítja az indítási feladatot.
* Ez gyakran egy .cmd vagy .bat kötegfájl fájlneve.
* A feladat a központi\\telepítés Hez tartozó AppRoot bin mappához viszonyítva történik. A környezeti változók nincsenek kibontva a feladat elérési útjának és fájljának meghatározásában. Ha szükség van a környezet bővítésére, létrehozhat egy kis .cmd parancsfájlt, amely meghívja az indítási feladatot.
* Lehet egy konzolalkalmazás vagy egy batch fájl, amely elindítja a [PowerShell-parancsfájlt.](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)

**executionContext** - Megadja az indítási feladat jogosultsági szintjét. A jogosultsági szint korlátozható vagy megemelhető:

* **Korlátozott**  
  Az indítási feladat a szerepkörrel megegyező jogosultságokkal fut. Ha a [Runtime] elem **executionContext** attribútuma is **korlátozott,** a rendszer felhasználói jogosultságokat használ.
* **Emelkedett**  
  Az indítási feladat rendszergazdai jogosultságokkal fut. Ez lehetővé teszi, hogy az indítási feladatok programokat telepítsenek, módosítsák az IIS-konfigurációt, elvégezzék a rendszerleíró adatbázis módosításait és más rendszergazdai szintű feladatokat anélkül, hogy növelnék magának a szerepkörnek a jogosultsági szintjét.  

> [!NOTE]
> Az indítási feladat jogosultsági szintjének nem kell megegyeznie magával a szerepkörrel.
> 
> 

**taskType** - Az indítási feladat végrehajtásának módját adja meg.

* **Egyszerű**  
  A feladatok végrehajtása a [ServiceDefinition.csdef] fájlban megadott sorrendben, egyenként történik. Ha egy **egyszerű** indítási feladat nulla **hibaszinttel** végződik, a következő **egyszerű** indítási feladat végrehajtásra kerül. Ha nincs több **egyszerű** indítási feladat végrehajtása, akkor maga a szerepkör indul el.   
  
  > [!NOTE]
  > Ha az **egyszerű** feladat nem nulla **hibaszinttel**végződik, a példány blokkolva lesz. Az ezt követő **egyszerű** indítási feladatok és maga a szerepkör nem indulnak el.
  > 
  > 
  
    Annak érdekében, hogy a kötegfájl nulla **hibaszinttel** végződjön, a kötegfájl-folyamat végén hajtsa végre a parancsot. `EXIT /B 0`
* **Háttér**  
  A feladatok végrehajtása aszinkron módon történik, párhuzamosan a szerepkör indításával.
* **Előtér**  
  A feladatok végrehajtása aszinkron módon történik, párhuzamosan a szerepkör indításával. Az **előtérben** lévő és a **háttérfeladat** közötti legfontosabb különbség az, hogy **egy előtérfeladat** megakadályozza a szerepkör újrahasznosítását vagy leállítását a feladat befejezése ig. A **háttérfeladatokra** nem vonatkozik ez a korlátozás.

## <a name="environment-variables"></a>Környezeti változók
A környezeti változók az adatok indítási feladatokhoz való átadására szolgálnak. Például elhelyezheti a telepítandó programot tartalmazó blob elérési útját, vagy a szerepkör által használt portszámokat, vagy az indítási feladat szolgáltatásainak vezérlésére szolgáló beállításokat.

Az indítási feladatokhoz kétféle környezeti változó létezik; statikus környezeti változók és környezeti változók a [RoleEnvironment] osztály tagjai alapján. Mindkettő a [ServiceDefinition.csdef] fájl [Környezet] szakaszában található, és mindkettő a [Variable] elemet és a **name** attribútumot használja.

A statikus környezeti változók a [Változó] elem **értékattribútumát** használja. A fenti példa létrehozza a **MyVersionNumber** környezeti változót, amelynek statikus értéke "**1.0.0.0**". Egy másik példa az lenne, hogy hozzon létre egy **StagingOrProduction** környezeti változó, amely manuálisan beállíthatja az **"átmeneti**" vagy "**éles**" értékek et a **StagingOrProduction** környezeti változó értéke alapján különböző indítási műveletek végrehajtásához.

A RoleEnvironment osztály tagjain alapuló környezeti változók nem használják a [Változó] elem **értékattribútumát.** Ehelyett a [RoleInstanceValue] gyermekelem, a megfelelő **XPath** attribútumértékkel, a [RoleEnvironment] osztály egy adott tagja alapján létrehozott környezeti változók at használ. Az **XPath** attribútum különböző [RoleEnvironment] értékek eléréséhez vezető értékei [itt](cloud-services-role-config-xpath.md)találhatók.

Ha például olyan környezeti változót szeretne létrehozni, amely "**true**" a számítási emulátorban fut, és "**false**" a felhőben való futtatáskor, használja a következő [Változó] és [RoleInstanceValue] elemeket:

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
Ismerje meg, hogyan hajthat végre néhány [gyakori indítási feladatot](cloud-services-startup-tasks-common.md) a felhőszolgáltatással.

[Csomagolja](cloud-services-model-and-package.md) be a felhőszolgáltatást.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tevékenység]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Indítás]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Futtatókörnyezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Környezet]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Változó]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue érték]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment környezet]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



