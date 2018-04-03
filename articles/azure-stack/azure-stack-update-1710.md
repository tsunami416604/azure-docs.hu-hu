---
title: Az Azure verem 1710 frissítés (Build 20171020.1) |} Microsoft Docs
description: További tudnivalók az Azure-verem 1710 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Az Azure verem 1710 frissítés (Build 20171020.1)

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és a frissítési csomag ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák vannak osztva ismert problémák közvetlenül kapcsolódik a frissítési folyamat, és a build (telepítés utáni) kapcsolatos ismert problémák.

> [!IMPORTANT]
> Ez a csomag csak integrált Azure verem rendszerek van. Ez a csomag nem vonatkoznak Azure verem szoftverfejlesztői készlet.

## <a name="improvements-and-fixes"></a>Kidolgozott fejlesztéseit és javításokat

A frissítés tartalmazza a következő minőségének javítása és javításokat Azure verem.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 kidolgozott fejlesztéseit és javításokat

- Windows Server 2016 frissítéseit: 2017. október 10 – KB4041691 (14393.1770 operációsrendszer-verzióval. Lásd: [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) további információt.

### <a name="additional-quality-improvements-and-fixes"></a>További minőségének javítása és a javítások

- Kiemelt végpont PowerShell-parancsmagok hibaelhárítása, és frissítse a Network Time Protocol (NTP) kiszolgálót hozzá.
- Támogatása frissíteni a rendszerjogosultságú végpont csak elég adminisztrációs (JEA) végpont modulok és a parancsmag engedélyezett. 
- A privilegizált végpont rögzített helyi nyelvi hibák.
- Mostantól lehetőség van forgassa el az átjáró hitelesítő adatokat.
- Eltávolítja a CBLocalAdmin helyi rendszergazdai fiók. 
- A szívverés értesítési sablon tartalmat rögzített győződjön meg arról, hogy szívverés a riasztások munkahelyi megfelelően frissítés után.
- Rögzített kezelésére időtúllépések FRU műveletek során a háló erőforrás-szolgáltató. 
- Mostantól lehetőség van a felhő fejlesztők Azure Resource Manager API-profilok használatához Azure veremben.
- Le van tiltva a Windows Update szolgáltatás a központi telepítés virtuális gépen (DVM). 
- A csomópont power be-és kikapcsolása műveletek eltávolítja a felhasználói felületen.
- Különböző egyéb teljesítményét és stabilitását javításokat. 
 
## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák

Ez a szakasz a 1710 frissítés telepítése során előforduló ismert problémákat.

> [!IMPORTANT]
> Ha a frissítés sikertelen, amikor később próbálja kell használnia a frissítés folytatásához a `Resume-AzureStackUpdate` a rendszerjogosultságú végpont parancsmagjával. A felügyeleti portál használatával nem folytatja a frissítést. (Ez az ebben a kiadásban egy ismert probléma.) További információkért lásd: [figyelése Azure verem használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).

| Jelenség  | Ok  | Megoldás: |
|---------|---------|---------|
|Egy adott frissítés a következőhöz hasonló hiba végrehajtásakor<br>Előfordulhat, hogy a "Tárolási állomások indítsa újra a tárolási csomópontnak" lépés során elő<br> a frissítési művelet terv.<br><br>**{"name": "Indítsa újra a tárolási gazdagép", "description": "indítsa újra a<br> tárolási állomások.","errorMessage": "írja be a"Restart"szerepkör<br> "BareMetal"jelenik meg, kivétel: \n\n a számítógép<br> állomásnév-05 a rendszer kihagyja. Nem sikerült beolvasni a LastBootUpTime<br> keresztül a WMI-szolgáltatás a következő hiba miatt:<br> az RPC-kiszolgáló nem érhető el.<br> (Exception from HRESULT: 0x800706BA).\nat Restart-Host** | A probléma oka egy lehetséges hibás illesztőprogram bizonyos konfigurációk szerepel. | 1. Jelentkezzen be az alaplapi felügyeleti vezérlővel (BMC) webhelyről, majd indítsa újra a gazdagép, amely azonosítja a hibaüzenet.<br><br>2. A frissítés folytatásához a kiemelt végpont használatával. |
| Amikor egy frissítést végez, a frissítési folyamat úgy tűnik, hogy megrekedésének kezelése<br> nem halad a lépés után és ". lépés: lépés 2.4 - telepítés futtatása<br> frissítés"a frissítés műveleti terv.<br><br>Ez a lépés .nupkg a másolási folyamat több követi<br> a belső infrastruktúra fájlmegosztások fájlokat. Példa:<br><br>**1 fájlok másolását a content\PerfCollector\VirtualMachines <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Vagy az üzenet jelenik meg:<br><br>**WarningMessage:Task: Meghívása "LiveUpdate" illesztőfelület<br> szerepkör sikertelen Cloud\Fabric\VirtualMachines:<br> írja be a "LiveUpdate" szerepkör jelenik meg:-VirtualMachines egy<br> kivétel: nincs elég hely a lemezen .**  | A probléma oka egy infrastruktúra-virtuális gép és a Windows Server kibővített fájl (sofs-sel) egy későbbi frissítés továbbítani problémát lemezek betelőben naplófájlok. | Forduljon a Microsoft ügyfélszolgálata és a támogatási szolgálathoz (CSS) segítségért. | 
| Egy adott frissítés a következőhöz hasonló hiba végrehajtásakor<br> a lépés során "lépés: lépés 2.13.2 - frissítés futtatása<br> *VM_Name*". a frissítés műveleti terv. (A virtuális gép<br> név változhat.)<br><br>**ActionPlanInstanceWarning dokumentumokat vagy számítógépnév:<br> WarningMessage:Task: meghívása "LiveUpdate" illesztőfelület<br> szerepkör sikertelen Cloud\Fabric\WAS: írja be a "LiveUpdate" szerepkör<br> "Rendszer" kivételt okozott: Hiba történt tárolási<br> inicializálási: Hiba történt, miközben az API-k<br> Microsoft Storage szolgáltatás hívása: {"Üzenet": "időtúllépés<br> Service Fabric folytatott kommunikáció során történt.<br> Kivétel típusa: TimeoutException.<br> Kivételüzenet: művelet túllépte az időkorlátot. "}**  | A problémát az okozza, amely egy későbbi frissítés javítja a Windows Server egy i/o-időtúllépés. | Kérjen segítséget a Microsoft CSS.
| Egy adott frissítés a következőhöz hasonló hiba végrehajtásakor<br> akkor fordulhat elő. lépése során "lépés 21 indítsa újra az SQL server virtuális gépen."<br><br>**Írja be a "LiveUpdateRestart" szerepkör jelenik meg:-VirtualMachines egy<br> kivétel: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> VM MachineName lekérdezése-Sql01. - 10/13/2017 5:23:50 óra VerboseMessage: [virtuális gépek vannak: LiveUpdateRestart]<br> VM van megjelölve, HighlyAvailable. – 10/13/2017 5:23:50 óra<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart]:<br>MS. A Internal.ServerClusters.ExceptionHelp.Build<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(logikai force), Microsoft.FailoverClusters.PowerShell.<br> A StopClusterResourceCommand.BeginTimedOperation() <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>: Microsoft.FailoverClusters.PowerShell ProcessRecord().<br> FCCmdlet.ProcessRecord() - 10/13/2017 5:23:50 óra figyelmeztetés<br>üzenet: feladat: meghívása "LiveUpdateRestart" illesztőfelület<br> szerepkör sikertelen Cloud\Fabric\VirtualMachines:** | A probléma akkor fordulhat elő, ha a virtuális gép nem indítható újra. | Kérjen segítséget a Microsoft CSS.
| Amikor egy frissítést végez, a következőhöz hasonló hiba fordulhat elő:<br><br>**2017-10-szerepkör típusú, "Leállítás" a "SQL" 22T01:37:37.5369944Z<br> kivételt okozott: Hiba történt a csomópont felfüggesztése<br> "s45r1004-Sql01" helyen Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542:<br> Leállítás, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>alkalmazások \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: sor: 50 < ScriptBlock&#62;,<br> <No file>: sor 18: < ScriptBlock&#62;, < nincs fájl&#62;: 16. sor** | A probléma akkor fordulhat elő, ha a virtuális gép nem helyezhető, a szerepkörök kiürítésére felfüggesztett állapotba. | Kérjen segítséget a Microsoft CSS.
| Frissítés végrehajtásakor vagy a következő hibák fordulhatnak elő:<br><br>**"AD FS" szerepkör "Ellenőrzés" típusú kivételt okozott: érvényesítési<br> hiba miatt sikertelen volt az AD FS/Graph-szerepkör: Hiba történt az AD FS ellenőrzésekor<br> végpont mintavételi *endpoint_URI*: kivétel hívása<br> " GetResponse"a"0"argumentummal:" a távoli kiszolgáló<br> hibát adott vissza: (503-as) kiszolgáló nem érhető el. "Invoke-<br>ADFSGraphValidation**<br><br>**"AD FS" szerepkör "Ellenőrzés" típusú kivételt okozott: érvényesítési<br> hiba miatt sikertelen volt az AD FS/Graph-szerepkör: hiba beolvasása<br> az AD FS tulajdonságok: nem sikerült kapcsolódni <br>NET.TCP://localhost: 1500 és házirend. A kapcsolódási kísérlet tartott<br> a 00:00:02.0498923 egy időtartamának. TCP hibakód<br> 10061: nem sikerült kapcsolatot létesíteni mert a cél<br> gép már visszautasította 127.0.0.1:1500.<br> Invoke-ADFSGraphValidation:** | A frissítés műveleti terv az Active Directory összevonási szolgáltatások (AD FS) állapotát nem lehet érvényesíteni. | Kérjen segítséget a Microsoft CSS.

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Ez a szakasz a telepítés utáni kapcsolatos ismert problémák 20171020.1 felépítéséhez.

### <a name="portal"></a>Portál

- Nem lehet a számítási és tárolási erőforrások megtekintése a felügyeleti portálon. Ez azt jelzi, hogy hiba történt a frissítés telepítése során, és, hogy a frissítés nem megfelelően történt a következő sikeres. Ha a probléma akkor fordul elő, lépjen kapcsolatba Microsoft CSS segítségért.
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
- Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

   Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem tudunk az előfizetéséhez, a Azure verem portálok engedélyek megtekintése. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.
- A **szolgáltatásának állapota** panel betöltése sikertelen. Ha a szolgáltatás állapotát panel megnyitásához a rendszergazda vagy a felhasználói portál, Azure verem egy hibaüzenet jelenik meg, és nem betölti az információt. Ez az elvárt működés. Válassza ki, és nyissa meg a szolgáltatás állapotát, de ez a funkció még nem érhető el, de hajtják végre az Azure-verem egy jövőbeli verziójában.
 

### <a name="backup"></a>Biztonsági mentés

- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.

### <a name="health-and-monitoring"></a>Állapot- és figyelés

- Ha újraindítja az infrastruktúra-szerepkör példánya, az újraindítás sikertelen jelző üzenet jelenhet meg. Azonban az újraindítás ténylegesen sikeresen befejeződött.

### <a name="marketplace"></a>Piactér
- Ha megpróbálja elemek hozzáadására a verem Azure piactér használatával a **hozzáadása az Azure-ból** beállítás, nem minden elem esetleg mások is láthatják letölthető.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.

### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre.
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.
 
### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.
 
### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
 
### <a name="field-replaceable-unit-fru-procedures"></a>A mező cserélhető Cisco egységet (FRU) eljárások

- Offline lemezképek futtatása frissítés közben nem volt. Ha ki kell cserélni a skálázási egység csomópont, ellenőrizze, hogy a kicserélt csomópont rendelkezik a legújabb javításokkal a OEM hardver szállítójával közösen.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti a 1710 csomag [Itt](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>További lépések

- Frissítéskezelés Azure verem áttekintését lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md).
- Frissítések alkalmazásával kapcsolatos információkért lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md).
