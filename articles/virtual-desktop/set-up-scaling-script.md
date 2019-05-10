---
title: Automatikus méretezés a virtuális Windows Desktop előzetes verziójához munkamenet-gazdagépek – Azure
description: Ismerteti, hogyan állíthatja be az automatikus skálázási parancsfájl Windows virtuális asztal előzetes munkamenet gazdagépek.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7687abf5fc4af0eea9fa6aa210cfd6734cec2b36
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410572"
---
# <a name="automatically-scale-session-hosts"></a>Munkamenetgazdák automatikus méretezése

Számos Windows virtuális asztal előzetes központi telepítés az Azure-ban a virtuális gép költségek pedig a virtuális asztali Windows központi telepítési díja jelentős részét. Költségek csökkentése érdekében érdemes, állítsa le és a használat csúcsidőn kívüli időszakokban szabadítsa fel a munkamenet gazdagép virtuális gépeken (VM), majd indítsa újra őket az alacsony kínálat csúcsidőben használat.

Ez a cikk egy egyszerű méretezési szkript használatával a Windows virtuális asztali környezet munkamenet gazdagép virtuális gépeinek automatikus skálázása. A méretezési parancsfájl működésével kapcsolatos további tudnivalókért tekintse meg a [a méretezési parancsfájl működése](#how-the-scaling-script-works) szakaszban.

## <a name="prerequisites"></a>Előfeltételek

A környezetben, ahol a szkript futtatása a következő műveleteket kell rendelkeznie:

- Windows virtuális asztal bérlő és a fiók vagy lekérdezni az adott bérlőn (például a távoli asztali szolgáltatások közreműködő) engedélyekkel egyszerű szolgáltatások.
- Munkamenet gazdagépre a virtuális gépek készletté konfigurálva, és regisztrálta a Windows virtuális asztal szolgáltatásban.
- Egy további virtuális gép, amely az ütemezett feladat fut a Feladatütemező keresztül, és munkamenet-gazdagépek hálózati hozzáféréssel rendelkezik. Ez a dokumentum későbbi szakaszában a nyújtaniuk scaler VM, lesz.
- A [a Microsoft Azure Resource Manager PowerShell-modul](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) telepítve az ütemezett feladat fut a virtuális gépen.
- A [Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) telepítve az ütemezett feladat fut a virtuális gépen.

## <a name="recommendations-and-limitations"></a>Javaslatok és korlátozások

A méretezési parancsfájl futtatásakor tartsa szem előtt az alábbiakat:

- Ez a skálázási parancsfájl egy gazdagép-készletet az ütemezett feladat a méretezési parancsfájlt futtató példányonként csak kezelésére is alkalmas.
- Az ütemezett feladatok, amelyek skálázási szkriptek futtatása virtuális gépen, amely mindig be van kapcsolva kell lennie.
- Hozzon létre egy külön mappát, a méretezési parancsfájl, és a konfiguráció minden egyes példányánál.
- Ez a szkript nem támogatja az Azure AD felhasználói fiókokkal, amelyek a többtényezős hitelesítés megkövetelése Windows virtuális asztali rendszergazdaként jelentkezik be. Azt javasoljuk, hogy a virtuális asztali Windows-szolgáltatás és az Azure eléréséhez használt egyszerű szolgáltatásokat. Hajtsa végre a [ebben az oktatóanyagban](create-service-principal-role-powershell.md) egy egyszerű szolgáltatást, és a egy szerepkör-hozzárendelés létrehozása a PowerShell használatával.
- Az Azure SLA-garanciára csak a virtuális gépet egy rendelkezésre állási csoportban vonatkozik. A dokumentum aktuális verzióját környezet ismerteti az egyetlen virtuális gép a méretezés során előfordulhat, hogy nem felel meg a rendelkezésre állási követelmények vonatkoznak.

## <a name="deploy-the-scaling-script"></a>A méretezési parancsfájl központi telepítése

Az alábbi eljárások megtudhatja, hogyan helyezhet üzembe a méretezési parancsfájlt.

### <a name="prepare-your-environment-for-the-scaling-script"></a>A méretezési parancsfájl a környezet előkészítése

Először készítsen elő a környezetet a méretezési parancsfájl:

1. Jelentkezzen be a virtuális gép (scaler VM), amely egy tartományi rendszergazdai fiók az ütemezett feladat fog futni.
2. Hozzon létre egy mappát a scaler virtuális Géphez a méretezési parancsfájl és konfigurációja tárolására (például **C:\\skálázás HostPool1**).
3. Töltse le a **basicScale.ps1**, **soubor Config.XML v**, és **Functions-PSStoredCredentials.ps1** fájlokat, és a **PowershellModules** a mappa a [parancsprogram-tárház skálázás](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) , és másolja őket abba a mappába, a 2. lépésben létrehozott. Kétféleképpen elsődleges beszerzése a fájlokat átmásolhatja őket a virtuális gép scaler előtt:
    - A git-tárház helyi számítógépre történő klónozásához.
    - Nézet a **Raw** minden egyes fájl verziója másolja és illessze be az egyes fájlok egy szövegszerkesztőbe, majd mentse a fájlokat a megfelelő fájl neve és a fájl típusa. 

### <a name="create-securely-stored-credentials"></a>Biztonságosan tárolt hitelesítő adatok létrehozása

Következő lépésként hozza létre a biztonságosan tárolt hitelesítő adatokat lesz szüksége:

1. Nyissa meg rendszergazdaként egy PowerShell ISE-ben.
2. A távoli asztali szolgáltatások PowerShell-modul importálása a következő parancsmagot:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Nyissa meg a Szerkesztés panelre, és a terhelés a **függvény-PSStoredCredentials.ps1** fájlt.
4. Futtassa a következő parancsmagot:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Például **változó beállítása – Name KeyPath-globális hatókör-érték "c:\\skálázás HostPool1"**
5. Futtassa a **New-StoredCredential - KeyPath \$KeyPath** parancsmagot. Amikor a rendszer kéri, adja meg a gazdagép készlet lekérdezése engedélyek Windows virtuális asztal hitelesítő adatait (a gazdagép készlet van megadva a **soubor Config.XML v**).
    - Ha különböző szolgáltatásnevek vagy standard szintű fiókot használ, futtassa a **New-StoredCredential - KeyPath \$KeyPath** parancsmag után minden hozza létre a helyi fiók tárolt hitelesítő adatokat.
6. Futtatás **Get-StoredCredentials-lista** ellenőrizze a hitelesítő adatok sikeresen létrejöttek.

### <a name="configure-the-configxml-file"></a>Soubor Config.XML v fájl konfigurálása

Adja meg a megfelelő értékek be a soubor Config.XML v méretezési parancsfájl-beállítások frissítése a következő mezőket:

| Mező                     | Leírás                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Az Azure AD-bérlő azonosítója, amely összekapcsolja az előfizetést, ahol a munkamenetgazda virtuális gépek futtatása     |
| AADApplicationId              | Egyszerű szolgáltatás azonosítója                                                       |
| AADServicePrincipalSecret     | Ez a tesztelési fázis során lehet megadni, de üres kell tartani, miután létrehozta a hitelesítő adatok **Functions-PSStoredCredentials.ps1**    |
| currentAzureSubscriptionId    | Ahol a munkamenetgazda virtuális gépek futtatása az Azure-előfizetés azonosítója                        |
| tenantName                    | Windows virtuális asztal bérlő neve                                                    |
| hostPoolName                  | Windows virtuális asztali állomás készlet neve                                                 |
| RDBroker                      | URL-címet a WVD szolgáltatásba, alapértelmezett érték https:\//rdbroker.wvd.microsoft.com             |
| Felhasználónév                      | A szolgáltatás egyszerű alkalmazás azonosítója (azt a lehetséges, hogy az azonos szolgáltatásnév AADApplicationId a) vagy általános jogú felhasználó a multi-factor authentication használata nélkül |
| isServicePrincipal            | Elfogadott értékek a következők **igaz** vagy **hamis**. Azt jelzi, hogy a második használt hitelesítő adatok, egy szolgáltatásnév vagy egy standard szintű fiók. |
| BeginPeakTime                 | Amikor használati csúcsidőszak kezdete                                                            |
| EndPeakTime                   | Ha használat csúcsidőszak vége                                                              |
| TimeDifferenceInHours         | Időeltérése helyi idő és az UTC-t, (óra)                                   |
| SessionThresholdPerCPU        | Azt határozza meg, amikor egy új virtuális gép munkamenetgazda kell elindítani alacsony kínálat csúcsidőben CPU küszöbérték-munkamenetek maximális száma.  |
| MinimumNumberOfRDSH           | Gazdagép-készlet virtuális gépek futtatásának folytatásához a használat csúcsidőn kívüli időszakban minimális száma             |
| LimitSecondsToForceLogOffUser | Jelentkezzen ki, hogy a felhasználók időszakát (másodpercben) száma. Ha az értéke 0, a felhasználó nem kényszerítette jelentkezzen ki.  |
| LogOffMessageTitle            | A felhasználó azokat Ön kényszerített kijelentkezés előtt küldött üzenet címe                  |
| LogOffMessageBody             | A felhasználóknak már kijelentkeztetése előtt küldött figyelmeztető üzenet törzse. Például "ezen a számítógépen állítsa le az X perc. Mentse a munkáját, és jelentkezzen ki." |

### <a name="configure-the-task-scheduler"></a>A Feladatütemező beállítása

Miután a konfigurációs .xml-fájlt, kell futtatni a basicScaler.ps1 rendszeres időközönként a Feladatütemező beállítása.

1. Indítsa el **Feladatütemező**.
2. Az a **Feladatütemező** ablakban válassza **feladat létrehozása...**
3. A **feladat létrehozása** párbeszédpanelen válassza ki a **általános** lapra, adja meg egy **neve** (például "Dinamikus RDSH"), válassza ki a **fut, ha van bejelentkezett felhasználó vagy nem** és **legmagasabb jogosultságokkal**.
4. Nyissa meg a **eseményindítók** lapfülre, majd válassza ki **új...**
5. Az a **új eseményindító** párbeszédpanelen, a **speciális beállítások**, ellenőrizze **feladat ismétlése minden** , és válassza ki a megfelelő időszakot és időtartama (például **15 perc** vagy **határozatlan ideig**).
6. Válassza ki a **műveletek** fülre, és **új...**
7. Az a **új művelet** párbeszédpanelen adja meg **powershell.exe** be a **Program/szkript** mezőbe, majd adja meg **C:\\skálázás\\ RDSScaler.ps1** be a **hozzáadása (nem kötelező) argumentumok** mező.
8. Nyissa meg a **feltételek** és **beállítások** lapokat, és válassza ki **OK** , fogadja el az alapértelmezett beállításokat az egyes.
9. Adja meg a jelszót a rendszergazdai fiók tervezi a méretezési parancsfájl futtatásához.

## <a name="how-the-scaling-script-works"></a>A méretezési parancsfájl működése

Ez a skálázási szkript soubor Config.XML v fájlból, beleértve a kezdő és a nap folyamán csúcs használati időszak vége beállításainak beolvasása.

Csúcs használati idő alatt a parancsfájl ellenőrzi az aktuális száma a munkamenetek és minden állomás készlet aktuális futó távoli asztali Munkamenetgazda kapacitás. A futó virtuális gépek munkamenetgazda van-e elegendő kapacitással támogatja a meglévő munkameneteket a soubor Config.XML v fájlban meghatározott SessionThresholdPerCPU paraméter alapján számítja ki. Ha nem, a szkript elindít további munkamenetgazda virtuális gépek gazdagép-készletben.

A használat csúcsidőn kívüli időszakban a parancsfájl meghatározza, hogy melyik munkamenetgazda, virtuális gépek állítsa le a soubor Config.XML v fájlban az MinimumNumberOfRDSH paraméter alapján. A szkript állítja be a munkamenet a kiürítési módot, hogy az új munkamenetek a gazdagépekhez csatlakozó virtuális gépek kiszolgálására. Ha a **LimitSecondsToForceLogOffUser** paramétert a soubor Config.XML v fájlban nullától eltérő pozitív értéket, a parancsfájl értesíti bármelyik mentse munkáját, várjon a beállított időn, és utána a felhasználók jelenleg be van jelentkezve a a felhasználók is jelentkezzen ki. Után minden felhasználói munkamenetet a virtuális gép munkamenet gazdagépen, a parancsfájl a kiszolgáló leáll.

Ha a **LimitSecondsToForceLogOffUser** nulla soubor Config.XML v fájlban paramétert, a parancsfájl lehetővé teszi a munkamenet-konfigurációs beállítás, a fogadó kezelni a felhasználói munkamenetek ki aláírási készlet tulajdonságait. Ha egy munkamenetben virtuális gazdagép minden olyan munkamenetek, akkor hagyja a munkamenetgazda virtuális gép futtatása. Ha nincs minden olyan munkamenetek, a parancsfájl a munkamenetgazda virtuális gép leáll.

A parancsprogram futtatása rendszeres időközönként scaler VM-kiszolgálón a Feladatütemező segítségével célja. Válassza ki a megfelelő időtartam alatt a távoli asztali szolgáltatások környezet mérete alapján, és ne feledje, hogy kezdő és a virtuális gépek leállítása eltarthat egy ideig. Azt javasoljuk, hogy a méretezési szkript futtatása 15 percenként.

## <a name="log-files"></a>Naplófájlok

A méretezési szkript létrehoz két naplófájl **WVDTenantScale.log** és **WVDTenantUsage.log**. A **WVDTenantScale.log** fájlban naplózza az események és hibák (ha van) minden egyes méretezési parancsfájl végrehajtása közben.

A **WVDTenantUsage.log** fájl lesz felvéve az aktív magok száma és aktív számú virtuális gépet minden alkalommal, amikor a méretezési parancsprogram futtatása. Ezen információk használatával a tényleges használat a Microsoft Azure virtuális gépek és a költségek becslése. A fájl vannak formázva, vesszővel tagolt értékek, az egyes elemek, amely tartalmazza a következő információkat:

>idő, a gazdagép-készlet, a magok, a virtuális gépek

A fájl nevét is módosíthatja szeretné, hogy a .csv kiterjesztésre, betölti a Microsoft Excelt, és elemezheti.
