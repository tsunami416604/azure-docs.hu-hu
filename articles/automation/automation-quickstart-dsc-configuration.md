---
title: Azure rövid útmutató – Virtuális gép konfigurálása DSC-vel | Microsoft Docs
description: LAMP-verem konfigurálása Linux rendszerű virtuális gépen a Desired State Configurationnel
services: automation
ms.subservice: dsc
keywords: dsc, konfigurálás, automatizálás
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 1a146ab7c05d200b71a33a72fa6362c3cf62629a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457518"
---
# <a name="configure-a-virtual-machine-with-desired-state-configuration"></a>Virtuális gép konfigurálása a kívánt állapotkonfigurációval

Az Azure Automation state configuration engedélyezésével kezelheti és figyelheti a Windows- és Linux-kiszolgálók konfigurációit a kívánt állapotkonfiguráció (DSC) használatával. A kívánt konfigurációtól elsodródó konfigurációk azonosíthatók vagy automatikusan javíthatók. Ez a rövid útmutató végigvezeti a Linux rendszerű virtuális gépek előkészítésén és a LAMP-vermek DSC-vel való üzembe helyezésén.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)
* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
* Red Hat Enterprise Linux, CentOS vagy Oracle Linux rendszert futtató Azure Resource Manager-alapú virtuális gép (nem klasszikus). A virtuális gépek létrehozásával kapcsolatos útmutatásért tekintse meg [az első Linux virtuális gép Azure Portallal való létrehozását](../virtual-machines/linux/quick-create-portal.md) bemutató cikket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az https://portal.azure.comAzure-ba a segítségével.

## <a name="onboard-a-virtual-machine"></a>Virtuális gép előkészítése

Számos különböző módszer létezik a gép fedélzetének és a DSC engedélyezésének. Ez a rövid útmutató az Automation-fiókon keresztüli előkészítést részletezi. A [bevezetési](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding) cikk elolvasásával többet is megtudhat a gépek állapotkonfigurációba történő be- és bedeszkázására szolgáló különböző módszerekről.

1. Az Azure Portal bal oldali panelén válassza az **Automation-fiókok** elemet. Ha nem látható a bal oldali ablaktáblában, kattintson a **Minden szolgáltatás** elemre, és keressen az eredményül kapott nézetben.
1. Válasszon egy Automation-fiókot a listában.
1. Az Automation-fiók bal oldali panelén válassza az **Állapotkonfiguráció (DSC)** elemet.
2. Kattintson a **Hozzáadás** elemre a virtuális gép kiválasztását lehetővé tevő oldal megnyitásához.
3. Keresse meg azt a virtuális gépet, amelyhez engedélyezni szeretné a DSC-t. A keresőmező és a szűrési lehetőségek használatával kereshet meg egy adott virtuális gépet.
4. Kattintson a virtuális gépre, majd a **Csatlakozás gombra**
5. Válassza ki a virtuális géphez megfelelő DSC-beállításokat. Ha már készített egy konfigurációt, megadhatja `Node Configuration Name`azt . Beállíthatja a [konfigurációs módot](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaConfig) a gép konfigurációs viselkedésének vezérléséhez.
6. Kattintson az **OK** gombra. Amíg a DSC-bővítmény telepítve van a virtuális `Connecting`gépre, az állapot a .

![Azure-beli virtuális gép előkészítése a DSC-hez](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Modulok importálása

A modulok DSC-erőforrásokat tartalmaznak, és sok megtalálható a [PowerShell-galériában.](https://www.powershellgallery.com) A konfigurációkban használt erőforrásokat a fordítás előtt importálni kell az Automation-fiókba. Ebben az oktatóanyagban az **nx** nevű modulra van szükség.

1. Az Automation-fiók bal oldali ablaktáblájában válassza a **Moduloktár** lehetőséget a **Megosztott erőforrások csoportban.**
1. Keresse meg az importálandó modult a `nx`nevének egy részének beírásával: .
1. Kattintson az importálni szeretne a modulra.
1. Kattintson **az Importálás gombra.**

![DSC-modul importálása](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>A konfiguráció importálása

Ez a rövid útmutató a gépen Apache HTTP-kiszolgálót, MySQL-t és PHP-t konfiguráló DSC-konfigurációt használ. Lásd: [DSC-konfigurációk](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations).

A szövegszerkesztőbe írja be a következőket, és mentse helyileg **AMPServer.ps1 néven.**

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

A konfiguráció importálása:

1. Az Automation-fiók bal oldali panelén válassza az **Állapotkonfiguráció (DSC)** elemet, majd kattintson a **Konfigurációk** lapra.
2. Kattintson a **+Hozzáadás** gombra.
3. Jelölje ki az előző lépésben mentett konfigurációs fájlt.
4. Kattintson az **OK** gombra.

## <a name="compile-a-configuration"></a>Konfiguráció fordítása

Ahhoz, hogy egy csomóponthoz hozzálehessen rendelni, le kell fordítania egy DSC-konfigurációt egy csomópontkonfigurációba (MOF-dokumentum). A fordítás érvényesíti a konfigurációt, és lehetővé teszi a paraméterértékek bevitelét. A konfiguráció kondiállapot-összeállításáról a [Konfigurációk összeállítása az állapotkonfigurációban (Konfigurációk összeállítása) (Konfigurációk összeállítása az állapotkonfigurációban) (Konfigurációk összeállítása) témakörben](automation-dsc-compile.md)olvashat bővebben.

1. Az Automation-fiók bal oldali ablaktáblájában válassza az **Állapotkonfiguráció (DSC)** lehetőséget, majd kattintson a **Konfigurációk** fülre.
1. Válassza ki `LAMPServer`a konfigurációt .
1. A menüpontokon válassza a **Fordítás** lehetőséget, majd kattintson az **Igen**gombra.
1. A Konfiguráció nézetben egy új fordítási feladat várakozik. Amikor a feladat sikeresen elkészül, továbbléphet a következő lépésre. Ha vannak hibák, akkor kattintson a fordítási feladat a részletekért.

## <a name="assign-a-node-configuration"></a>Csomópont-konfiguráció hozzárendelése

Lefordított csomópont-konfigurációt rendelhet egy DSC-csomóponthoz. A hozzárendelés a konfigurációt alkalmazza a gépre, és figyeli vagy automatikusan korrigálja az adott konfigurációból való eltolódást.

1. Az Automation-fiók bal oldali ablaktáblájában válassza az **Állapotkonfiguráció (DSC)** lehetőséget, majd kattintson a **Csomópontok** fülre.
1. Válassza ki azt a csomópontot, amelyhez konfigurációt szeretne rendelni.
1. Kattintson a **Csomópont-konfiguráció hozzárendelése** elemre
1. Válassza ki a `LAMPServer.localhost` csomópont konfigurációját, és kattintson az **OK**gombra. Az Állapotkonfiguráció most hozzárendeli a lefordított konfigurációt a `Pending`csomóponthoz, és a csomópont állapota a- ra változik. A következő időszakos ellenőrzéskor a csomópont lekéri a konfigurációt, alkalmazza azt, és jelenti az állapotot. A csomópont beállításaitól függően akár 30 percet is igénybe vehet, amíg a csomópont lekéri a konfigurációt. 
1. Az azonnali ellenőrzés kényszerítéséhez futtassa a következő parancsot helyileg a Linux rendszerű virtuális gépen: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Csomópont-konfiguráció hozzárendelése](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Csomópont állapotának megtekintése

Megtekintheti az összes állapotkonfiguráció által felügyelt csomópont állapotát az Automation-fiókban. Az információ az **Állapotkonfiguráció (DSC)** elemre kattintva, majd a Csomópontok fülre kattintva **jelenik** meg. A megjelenítést állapot, csomópontkonfiguráció vagy névkeresés szerint szűrheti.

![DSC-csomópont állapota](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Linux virtuális gép állapotkonfigurációba, létrehozott egy konfigurációt a LAMP verem, és üzembe helyezte a konfigurációt a virtuális gép. Ha meg szeretné tudni, hogyan használhatja az Azure Automation állapotkonfigurációját a folyamatos üzembe helyezés engedélyezéséhez, folytassa a cikkel:

> [!div class="nextstepaction"]
> [Folyamatos üzembe helyezés virtuális gépre a DSC és a Chocolatey használatával](./automation-dsc-cd-chocolatey.md)

* Ha többet szeretne megtudni a PowerShell DSC-ről, olvassa el a [PowerShell kívánt állapotkonfigurációjának áttekintése című témakört.](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview)
* Ha többet szeretne tudni az állapotkonfiguráció PowerShellből történő kezeléséről, olvassa el az [Azure PowerShell.](https://docs.microsoft.com/powershell/module/azurerm.automation/)
* Ha meg szeretné tudni, hogyan továbbíthat DSC-jelentéseket az Azure Monitor naplóiba jelentéskészítésés riasztási célokra, olvassa el [a DSC-jelentés továbbítása az Azure Monitor-naplókba.](automation-dsc-diagnostics.md)