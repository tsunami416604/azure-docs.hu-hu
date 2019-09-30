---
title: Windows rendszerű virtuális asztali munkamenet-gazdagépek automatikus méretezése – Azure
description: Útmutató az automatikus skálázási parancsfájl beállításához a Windows rendszerű virtuális asztali munkamenet-gazdagépekhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: f0d847596ef21af67973b6572737e27e1d015991
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676490"
---
# <a name="automatically-scale-session-hosts"></a>Munkamenetgazdák automatikus méretezése

Számos, az Azure-beli Windows rendszerű virtuális asztali környezet esetében a virtuális gépek költségei a Windows rendszerű virtuális asztali telepítés teljes költségének jelentős részét jelentik. A költségek csökkentése érdekében érdemes leállítani és felszabadítani a munkamenet-gazda virtuális gépeket (VM-EK) a használaton kívüli órákban, majd újraindítani őket a maximális kihasználtsági idő alatt.

Ez a cikk egy egyszerű méretezési parancsfájlt használ a munkamenet-gazdagép virtuális gépei automatikus méretezéséhez a Windows rendszerű virtuális asztali környezetben. Ha többet szeretne megtudni a skálázási szkript működéséről, tekintse meg a [skálázási parancsfájl működése](#how-the-scaling-script-works) című szakaszt.

## <a name="prerequisites"></a>Előfeltételek

A parancsfájl futtatásához szükséges környezetnek a következőkkel kell rendelkeznie:

- Egy Windows rendszerű virtuális asztali bérlő és fiók, vagy egy egyszerű szolgáltatásnév, amely a bérlő (például az RDS közreműködő) lekérdezéséhez szükséges engedélyekkel rendelkezik.
- A Windows rendszerű virtuális asztali szolgáltatásban konfigurált és regisztrált Munkamenetcímtár-készlet virtuális gépek.
- Egy további virtuális gép, amely a Feladatütemező használatával futtatja az ütemezett feladatot, és hálózati hozzáféréssel rendelkezik a munkamenet-gazdagépekhez. Ezt a rendszer a dokumentum későbbi részében a méretezési virtuális gép néven említi.
- Az ütemezett feladatot futtató virtuális gépre telepített [Microsoft Azure Resource Manager PowerShell-modul](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .
- Az ütemezett feladatot futtató virtuális GÉPRE telepített [Windows virtuális asztali PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) .

## <a name="recommendations-and-limitations"></a>Javaslatok és korlátozások

A skálázási parancsfájl futtatásakor tartsa szem előtt az alábbi dolgokat:

- Ez a skálázási parancsfájl csak egy, a skálázási parancsfájlt futtató ütemezett feladat példányán lévő egy gazdagépet képes kezelni.
- A skálázási parancsfájlokat futtató ütemezett feladatoknak olyan virtuális gépen kell lenniük, amely mindig be van kapcsolva.
- Hozzon létre egy külön mappát a skálázási parancsfájl minden példányához és a konfigurációhoz.
- Ez a szkript nem támogatja a többtényezős hitelesítést igénylő Azure AD felhasználói fiókokkal való bejelentkezést rendszergazdaként a Windows rendszerű virtuális asztalhoz. Javasoljuk, hogy az egyszerű szolgáltatásokat használja a Windows Virtual Desktop szolgáltatás és az Azure eléréséhez. [Ezt az oktatóanyagot](create-service-principal-role-powershell.md) követve hozzon létre egy egyszerű szolgáltatásnevet és egy szerepkör-hozzárendelést a PowerShell használatával.
- Az Azure SLA-garanciája csak a rendelkezésre állási csoportba tartozó virtuális gépekre vonatkozik. A dokumentum aktuális verziója egy olyan környezetet ír le, amely egyetlen virtuális géppel hajtja végre a skálázást, ami esetleg nem felel meg a rendelkezésre állási követelményeknek.

## <a name="deploy-the-scaling-script"></a>A skálázási parancsfájl üzembe helyezése

A következő eljárásokkal megtudhatja, hogyan helyezheti üzembe a skálázási parancsfájlt.

### <a name="prepare-your-environment-for-the-scaling-script"></a>A környezet előkészítése a méretezési parancsfájlhoz

Először készítse elő a környezetet a skálázási parancsfájlhoz:

1. Jelentkezzen be a virtuális gépre (a méretezési virtuális gépre), amely az ütemezett feladatot egy tartományi rendszergazdai fiókkal fogja futtatni.
2. Hozzon létre egy mappát a méretezési virtuális gépen a skálázási parancsfájl és a konfigurációjának tárolásához (például **C: \\scaling-HostPool1**).
3. Töltse le a **basicScale. ps1**, a **config. XML**és az **functions-PSStoredCredentials. Ps1** fájlt, valamint a **PowershellModules** mappát a [skálázási parancsfájl-tárházból](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) , és másolja azokat a 2. lépésben létrehozott mappába. A skálázási virtuális gépre való másolás előtt két fő módszert kell megszereznie a fájlok beszerzéséhez:
    - A git-tárház klónozása a helyi gépre.
    - Tekintse meg az egyes fájlok **nyers** verzióját, másolja és illessze be az egyes fájlok tartalmát egy szövegszerkesztőbe, majd mentse a fájlokat a megfelelő fájlnévvel és fájltípussal. 

### <a name="create-securely-stored-credentials"></a>Biztonságos tárolt hitelesítő adatok létrehozása

Ezután létre kell hoznia a biztonságos tárolt hitelesítő adatokat:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Importálja az RDS PowerShell-modult a következő parancsmag futtatásával:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Nyissa meg a szerkesztési ablaktáblát, töltse be a **Function-PSStoredCredentials. ps1** fájlt, majd futtassa a teljes parancsfájlt (F5)
4. Futtassa a következő parancsmagot:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    **Adja meg például a következőt: set-változó-Name – scope Global-Value "c: \\scaling-HostPool1"**
5. Futtassa a **New-StoredCredential-\$KeyPath** parancsmagot. Ha a rendszer kéri, adja meg a Windows rendszerű virtuális asztali hitelesítő adatait, amely jogosult a gazdagép lekérdezésére (az alkalmazáskészletet a **config. XML**fájlban lehet megadni).
    - Ha más egyszerű szolgáltatásnevet vagy standard fiókot használ, akkor minden egyes fiók esetében futtassa a **New-StoredCredential \$KeyPath** parancsmagot egyszer, hogy helyi tárolt hitelesítő adatokat hozzon létre.
6. A **Get-StoredCredential-List** futtatásával erősítse meg, hogy a hitelesítő adatok sikeresen létrejöttek.

### <a name="configure-the-configxml-file"></a>A config. xml fájl konfigurálása

Adja meg a megfelelő értékeket a következő mezőkben, hogy frissítse a méretezési parancsfájl beállításait a config. xml fájlban:

| Mező                     | Leírás                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Az Azure AD-bérlő azonosítója, amely társítja azt az előfizetést, amelyben a munkamenet-gazda virtuális gépei futnak     |
| AADApplicationId              | Egyszerű szolgáltatásnév alkalmazásának azonosítója                                                       |
| AADServicePrincipalSecret     | Ezt megadhatja a tesztelési fázisban, de az **functions-PSStoredCredentials. ps1** hitelesítő adatainak létrehozása után üresen kell hagyni.    |
| currentAzureSubscriptionId    | Annak az Azure-előfizetésnek az azonosítója, amelyben a munkamenet-gazda virtuális gépek futnak                        |
| tenantName                    | Windows rendszerű virtuális asztali bérlő neve                                                    |
| hostPoolName                  | Windows rendszerű virtuális asztali címkészlet neve                                                 |
| RDBroker                      | A WVD szolgáltatás URL-címe, alapértelmezett érték: https: \//rdbroker. WVD. microsoft. com             |
| Felhasználónév                      | Az egyszerű szolgáltatásnév alkalmazásának azonosítója (ez lehet ugyanaz a szolgáltatás, mint a AADApplicationId) vagy a standard felhasználó a multi-Factor Authentication használata nélkül |
| isServicePrincipal            | Az elfogadott értékek: **true** vagy **false**. Azt jelzi, hogy a használt hitelesítő adatok második halmaza egy egyszerű szolgáltatásnév vagy egy normál fiók. |
| BeginPeakTime                 | A csúcsérték-használat idejének kezdete                                                            |
| EndPeakTime                   | A csúcsérték-használati idő végén                                                              |
| TimeDifferenceInHours         | Időbeli eltérés a helyi idő és az UTC között (óra)                                   |
| SessionThresholdPerCPU        | A munkamenetek maximális száma a CPU-küszöbérték alapján, amely meghatározza, hogy egy új munkamenet-gazda virtuális gép mikor induljon el csúcsidőben.  |
| MinimumNumberOfRDSH           | A futtatni kívánt gazdagépek minimális száma a használaton kívüli időszakban             |
| LimitSecondsToForceLogOffUser | Azon időtartam másodpercben, ameddig a felhasználók kijelentkezése előtt várni kell. Ha a 0 értékre van állítva, a felhasználók nem kénytelenek kijelentkezni.  |
| LogOffMessageTitle            | A felhasználónak elküldött üzenet címe, mielőtt kijelentkezésre kényszerülnek                  |
| LogOffMessageBody             | A felhasználóknak a kijelentkezésük előtt küldött figyelmeztető üzenet törzse. Például: "Ez a számítógép X percen belül le fog állni. Mentse a munkáját, és jelentkezzen ki. |

### <a name="configure-the-task-scheduler"></a>A Feladatütemező konfigurálása

A Configuration. xml fájl konfigurálása után a Feladatütemezőt úgy kell konfigurálnia, hogy rendszeres időközönként futtassa az basicScaler. ps1 fájlt.

1. Indítsael a Feladatütemezőt.
2. A Feladatütemező ablakban válassza a **feladat létrehozása..** . lehetőséget.
3. **A feladat létrehozása** párbeszédpanelen válassza az **általános** lapot, adjon meg egy **nevet** (például "dinamikus RDSH"), válassza a Futtatás lehetőséget, **hogy a felhasználó bejelentkezett-e vagy sem** , és **a legmagasabb jogosultságokkal fusson**.
4. Lépjen az **Eseményindítók** lapra, majd válassza az **új...** lehetőséget.
5. Az **új trigger** párbeszédablak **Speciális beállítások**területén jelölje be a **Feladat ismétlése** jelölőnégyzetet, és válassza ki a megfelelő időtartamot és időtartamot (például **15 perc** vagy **határozatlan**idő).
6. Válassza a **műveletek** fület, és kattintson az **új...** lehetőségre.
7. Az **új művelet** párbeszédpanelen írja be a **PowerShell. exe fájlt** a **program/parancsfájl** mezőbe, majd írja be a **C: \\scaling @ No__t-5basicScale. ps1** parancsot az **argumentumok hozzáadása (nem kötelező)** mezőbe.
8. Lépjen a **feltételek** és **Beállítások** lapokra, majd kattintson az **OK gombra** az egyes beállítások alapértelmezett beállításainak elfogadásához.
9. Adja meg annak a rendszergazdai fióknak a jelszavát, amelyben futtatni szeretné a skálázási parancsfájlt.

## <a name="how-the-scaling-script-works"></a>A skálázási parancsfájl működése

Ez a skálázási parancsfájl egy config. XML fájlból olvassa be a beállításokat, beleértve a maximális kihasználtsági időszak kezdetét és végét a nap folyamán.

A maximális kihasználtság ideje alatt a parancsfájl ellenőrzi a munkamenetek aktuális számát és a jelenlegi futó RDSH kapacitását az egyes gazdagépek esetében. Kiszámítja, hogy a futó munkamenet-gazdagép virtuális gépei rendelkeznek-e elegendő kapacitással a meglévő munkamenetek támogatásához a config. xml fájlban meghatározott SessionThresholdPerCPU paraméter alapján. Ha nem, a parancsfájl további munkamenet-gazdagép virtuális gépeket indít el a gazdagép-készletben.

A nem maximális kihasználtsági idő alatt a parancsfájl meghatározza, hogy a munkamenet-gazdagép virtuális gépei melyik MinimumNumberOfRDSH paraméter alapján legyenek leállítva a config. xml fájlban. A parancsfájl úgy állítja be a munkamenet-gazdagép virtuális gépei számára a kiürítési módot, hogy megakadályozza az új munkamenetek kapcsolódását a gazdagépekhez. Ha a config. xml fájlban a **LimitSecondsToForceLogOffUser** paramétert nem nulla értékű pozitív értékre állítja, akkor a parancsfájl értesíti a jelenleg bejelentkezett felhasználókat, hogy mentse a munkát, várjon a beállított időtartamra, majd kényszerítse ki a felhasználókat a kijelentkezésre. Ha az összes felhasználói munkamenet ki lett jelentkezve egy munkamenet-gazda virtuális gépen, a parancsfájl leállítja a kiszolgálót.

Ha a config. xml fájlban a **LimitSecondsToForceLogOffUser** paramétert nullára állítja, a parancsfájl lehetővé teszi a munkamenet-konfiguráció beállítását a gazdagép-készlet tulajdonságaiban a felhasználói munkamenetek kijelentkezésének kezeléséhez. Ha egy munkamenet-gazda virtuális gépen vannak munkamenetek, akkor a munkamenet-gazda virtuális gép nem fut. Ha nincsenek munkamenetek, a szkript leállítja a munkamenet-gazda virtuális gépet.

A szkriptet úgy tervezték, hogy rendszeres időközönként fusson a skálázható virtuálisgép-kiszolgálón a Feladatütemező használatával. A Távoli asztali szolgáltatások környezet méretétől függően válassza ki a megfelelő időintervallumot, és ne feledje, hogy a virtuális gépek indítása és leállítása hosszabb időt is igénybe vehet. Azt javasoljuk, hogy 15 percenként futtassa a skálázási parancsfájlt.

## <a name="log-files"></a>Naplófájlok

A skálázási parancsfájl két naplófájlt hoz létre, a **WVDTenantScale. log** és a **WVDTenantUsage. log**fájlt. A **WVDTenantScale. log** fájl naplózza az eseményeket és a hibákat (ha vannak) a skálázási parancsfájl minden végrehajtása során.

A **WVDTenantUsage. log** fájl rögzíti a magok aktív számát és a virtuális gépek aktív számát minden alkalommal, amikor végrehajtja a skálázási parancsfájlt. Ezekkel az információkkal megbecsülheti Microsoft Azure virtuális gépek tényleges használatát és a költségeket. A fájl vesszővel tagolt értékként van formázva, és minden egyes elem a következő információkat tartalmazza:

>idő, alkalmazáskészlet, magok, virtuális gépek

A fájl neve módosítható úgy is, hogy egy. csv kiterjesztéssel rendelkezzen, betöltve a Microsoft Excelben, és elemezze.
