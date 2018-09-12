---
title: Az Azure Stack 1710-es frissítés (Build 20171020.1) |} A Microsoft Docs
description: 'További tudnivalók: Mi az Azure Stack az 1710-es frissítés integrált rendszerek, az ismert problémák és hová töltse le a frissítést.'
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: cf870551a3dbd9b5ea0ef6f886dc6451e43b2c25
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377190"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Az Azure Stack 1710-es frissítés (Build 20171020.1)

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk ismerteti a fejlesztést, és javítja a frissítési csomag, ismert problémák az ebben a kiadásban, valamint a helyét, a frissítés letöltése. Ismert problémák vannak osztva, ismert problémákat közvetlenül kapcsolódik a frissítési folyamat, és a build (telepítés utáni) kapcsolatos ismert problémák.

> [!IMPORTANT]
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="improvements-and-fixes"></a>Fejlesztések és javítások

A frissítés a következő minőségének javítása és javításokat tartalmaz az Azure Stackhez.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>A Windows Server 2016 fejlesztések és javítások

- A Windows Server 2016 frissítései: 2017. október 10 – KB4041691 (14393.1770 operációs rendszer buildszáma. Lásd: [ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691) további információt.

### <a name="additional-quality-improvements-and-fixes"></a>További minőségi fejlesztések és javítások

- Hozzáadott végpont emelt szintű PowerShell-parancsmagok és a Network Time Protocol (NTP) kiszolgáló frissítése.
- Támogatás hozzáadva a a kiemelt végponthoz csak Enough Administration (JEA) végpont modulokat és parancsmag engedélyezési lista frissítése. 
- Rögzített helyi nyelveken hibák a kiemelt végponthoz.
- Mostantól lehetőség van átjáró hitelesítő adatok forgatása.
- Eltávolítja a CBLocalAdmin helyi rendszergazdai fiók. 
- A szívverés értesítési sablon tartalom rögzített, győződjön meg arról, hogy szívverés riasztások munkahelyi megfelelően frissítés után.
- A Fabric erőforrás-szolgáltató kezelése érdekében tett időtúllépések FRU műveletek során rögzített. 
- Megjelent az Azure Resource Manager API-profilok használatához az Azure Stacken fejlesztők számára.
- Le van tiltva a Windows Update szolgáltatás a központi telepítés virtuális gépen (DVM). 
- A csomópont power be-és kikapcsolása műveletek távolítva a felhasználói felület.
- Különböző egyéb teljesítmény- és stabilitási javítások. 
 
## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

Ez a szakasz tartalmazza az 1710-es frissítés telepítése során előforduló ismert problémákat.

> [!IMPORTANT]
> Ha a frissítés sikertelen, amikor később próbálja kell használnia a frissítés folytatásához a `Resume-AzureStackUpdate` parancsmagot a kiemelt végponthoz. A felügyeleti portál használatával nem folytatja a frissítést. (Ez az ebben a kiadásban egy ismert probléma.) További információkért lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).

| Jelenség  | Ok  | Megoldás: |
|---------|---------|---------|
|Egy frissítést, a következőhöz hasonló hiba végrehajtásakor<br>a "Storage gazdagépek indítsa újra a tárolócsomópont" lépés során esetlegesen jelentkező<br> a frissítési művelet terv.<br><br>**{"name": "Indítsa újra a tároló gazdagép", "description": "indítsa újra a<br> tárolási gazdagépek.","errorMessage": "írja be a"Restart"szerepkör<br> "BareMetal"kiváltott kivétel: \n\n a számítógép<br> állomásnév-05 a rendszer kihagyta. Nem sikerült beolvasni a LastBootUpTime<br> keresztül a WMI-szolgáltatás a következő hiba miatt:<br> az RPC-kiszolgáló nem érhető el.<br> (Kivétel HRESULT: 0x800706BA). \nat újraindítás-gazdagép** | A probléma oka egy lehetséges hibás illesztőprogram megtalálható az egyes konfigurációkhoz. | 1. Jelentkezzen be az alaplapi felügyeleti vezérlőnek (BMC) webes felülete, és indítsa újra a gazdagépet, amely azonosítja a hibaüzenetben.<br><br>2. A frissítés folytatásához a rendszerjogosultságú végpont használatával. |
| Amikor frissítést végez, a frissítési folyamat stagnálni jelenik meg<br> nem halad a lépés után, és ". lépés: lépés 2.4 - telepítés futtatása<br> frissítése"a frissítés cselekvési terv.<br><br>Ebben a lépésben egy sorozatát .nupkg a másolási folyamat követi<br> a belső infrastruktúra fájlmegosztások fájlokat. Példa:<br><br>**1 fájlok másolását a content\PerfCollector\VirtualMachines <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Vagy az üzenet jelenik meg:<br><br>**WarningMessage:Task: Hívja meg a kapcsolat "LiveUpdate"<br> szerepkör nem sikerült Cloud\Fabric\VirtualMachines:<br> írja be a "LiveUpdate" kiváltott virtuális-gép szerepkör-<br> kivétel: nem áll elég hely a lemezen .**  | A probléma okozza a lemezeket az Azure-infrastruktúra virtuális gépek és a egy problémát a Windows Server kibővített fájlkiszolgáló (SOFS) egy későbbi frissítés továbbítani betelik a rendszernapló fájljaiban. | Lépjen kapcsolatba segítségért a Microsoft ügyfél- és támogatási (CSS). | 
| Egy frissítést, a következőhöz hasonló hiba végrehajtásakor<br> a lépés során esetlegesen jelentkező "lépés: lépés 2.13.2 - frissítés futtatása<br> *VM_Name*". a frissítés cselekvési tervet. (A virtuális gép<br> név változhat.)<br><br>**ActionPlanInstanceWarning dokumentumokat vagy számítógépnév:<br> WarningMessage:Task: hívja meg "LiveUpdate" illesztő az<br> szerepkör nem sikerült Cloud\Fabric\WAS: írja be a "LiveUpdate" szerepkör<br> '' előfordult kivétel: során hiba tárolási<br> inicializálási: Hiba történt, miközben egy API-t<br> hívja a Microsoft Storage szolgáltatáshoz: {"Message": "időtúllépés<br> történt a Service Fabric segítségével való kommunikáció közben.<br> Kivétel típusa: TimeoutException.<br> Kivételüzenet: művelet túllépte az időkorlátot. "}**  | A probléma okozza, amely egy későbbi frissítésben javítjuk a Windows Server-i/o időkorlátja. | Segítségért forduljon a Microsoft CSS.
| Egy frissítést, a következőhöz hasonló hiba végrehajtásakor<br> fordulhat elő, lépés során "a lépés 21-én az SQL server virtuális gépek újraindítása."<br><br>**Írja be a "LiveUpdateRestart" szerepkör kiváltott virtuális-gép egy<br> kivétel: VerboseMessage: [VirtualMachines:LiveUpdateRestart]<br> adatlekérdezést, az virtuális gép számítógépnév-Sql01. – 10/13/2017 5:11:50 PM VerboseMessage: [virtuális gép: LiveUpdateRestart]<br> virtuális gép van megjelölve, HighlyAvailable. – a 10/13/2017 5:11:50 PM<br> VerboseMessage: [VirtualMachines:LiveUpdateRestart]:<br>MS. A Internal.ServerClusters.ExceptionHelp.Build<br>MS. Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(logikai force), Microsoft.FailoverClusters.PowerShell.<br> A StopClusterResourceCommand.BeginTimedOperation() <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>, Microsoft.FailoverClusters.PowerShell ProcessRecord().<br> FCCmdlet.ProcessRecord() – 10/13/2017 5:11:50 PM figyelmeztetés<br>üzenet: feladat: hívja meg "LiveUpdateRestart" illesztő az<br> sikertelen Cloud\Fabric\VirtualMachines szerepkör:** | A probléma akkor fordulhat elő, ha a virtuális gép nem tudta újraindítani. | Segítségért forduljon a Microsoft CSS.
| Amikor frissítést végez, a következőhöz hasonló hiba fordulhat elő:<br><br>**2017-10-22T01:37:37.5369944Z "SQL" szerepkör "Shutdown" típusú<br> kivételt: Hiba történt a csomópont felfüggesztése<br> "s45r1004-Sql01" kommunikációjához Stop-SQL használatával, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\ Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542,<br> Leállítás, C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>alkalmazások \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br> Deployment\Classes\SQL\SQL.psm1: sor: 50 < ScriptBlock&#62;,<br> <No file>: 18: sor < ScriptBlock&#62;, < nem fájl&#62;: 16. sor** | A probléma akkor fordulhat elő, ha a virtuális gép nem helyezhető el a szerepkörök távozását felfüggesztett állapotba. | Segítségért forduljon a Microsoft CSS.
| Amikor frissítést végez, az alábbi hibák valamelyike fordulhatnak elő:<br><br>**"ADFS" szerepkör "Érvényesítés" típusú kivételt: érvényesítési<br> ADFS/Graph szerepkör hiba miatt sikertelen volt: hiba ellenőrzése az AD FS<br> végpont mintavételi *endpoint_URI*: kivétel hívása<br> " GetResponse"a"0"argumentummal:" a távoli kiszolgáló<br> hibát adott vissza: (503) kiszolgáló nem érhető el. ", Invoke -<br>ADFSGraphValidation**<br><br>**"ADFS" szerepkör "Érvényesítés" típusú kivételt: érvényesítési<br> ADFS/Graph szerepkör hiba miatt sikertelen volt: hiba beolvasása<br> ADFS tulajdonságai: nem sikerült csatlakozni <br>NET.TCP://localhost: 1500/házirend. A csatlakozási kísérlet a következő tartott<br> meg azt az időtartományt, 00:00:02.0498923 számára. TCP-hibakód:<br> 10061: nem sikerült kapcsolatot létesíteni mert a cél<br> gép már visszautasította 127.0.0.1:1500.<br> Invoke-ADFSGraphValidation:** | A frissítési terv nem tudja érvényesíteni az Active Directory összevonási szolgáltatások (AD FS) állapotát. | Segítségért forduljon a Microsoft CSS.

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Ez a szakasz tartalmazza a telepítés utáni kapcsolatos ismert problémák 20171020.1 összeállítása.

### <a name="portal"></a>Portál

- Ez nem lehetséges számítási és tárolási erőforrások megtekintése a felügyeleti portálon. Ez azt jelzi, hogy hiba történt a frissítés telepítése során, és a frissítés nem megfelelően történt sikeres. Ez a probléma akkor fordul elő, ha a Microsoft CSS kapcsolatos segítségért lépjen kapcsolatba.
- A portálon egy üres irányítópult jelenhet meg. Szeretné használni az irányítópultot, kattintson a fogaskerék ikonra a portál jobb felső sarokban, és válassza **alapértelmezett beállítások visszaállítása**.
- Egy erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez a viselkedés várható. Erőforráscsoportok áthelyezése előfizetések között jelenleg nem támogatott.
- Minden munkafolyamathoz, ahol ki kell választania egy előfizetést, erőforráscsoportot vagy a hely egy legördülő lista egy vagy több, az alábbi problémákat tapasztalhatja:

   - A lista tetején egy üres sor jelenhet meg. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő lista elemeinek listája rövid, nem lehet megtekintheti az elem sem.
   - Ha a felhasználó több előfizetéssel rendelkezik, az erőforráscsoport-legördülő lista üres is lehet. 

   Az utolsó két problémák megkerüléséhez írja be az előfizetés vagy (ha tudja) erőforráscsoport nevét, vagy a Powershellt használhatja helyette.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és törölje a felhasználói előfizetések.
- Ön nem tudja megtekinteni az előfizetésben az Azure Stack portálok az engedélyeket. Áthidaló megoldásként használja a Powershellt engedélyek ellenőrizheti.
- A **Service Health** panel nem sikerül betölteni. Ha a Service Health panel megnyitja a rendszergazdai vagy a felhasználói portálon, az Azure Stack egy hibaüzenet jelenik meg, és nem tölt be adatokat. Ez az elvárt működés. Válassza ki, és nyissa meg a Service Health, bár ez a funkció még nem áll rendelkezésre, de hajtják végre az Azure Stack egy jövőbeli verziójában.
 

### <a name="backup"></a>Backup

- Ne engedélyezze az infrastruktúra biztonsági mentését a **infrastruktúra biztonsági mentését** panelen.

### <a name="health-and-monitoring"></a>Állapot és figyelés

- Az infrastruktúra szerepkör-példány újraindítása, ha egy üzenet jelzi, hogy az újraindítás nem jelenhet meg. Azonban az újraindítás ténylegesen sikeres volt.

### <a name="marketplace"></a>Piactér
- Amikor az Azure Stack piactéren elemek hozzáadása használatával próbálja a **hozzáadása az Azure-ból** beállítás, nem minden elem látható le lehet.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és megtekintheti a felügyeleti elemek, például a csomagokat és ajánlatokat. Ezek az elemek nem működik a felhasználók számára is.

### <a name="compute"></a>Compute
- Felhasználók kapnak arra, hogy georedundáns tárolás hozzon létre egy virtuális gépet. E konfiguráció hatására a virtuális gép létrehozása sikertelen lesz.
- Konfigurálhatja a virtuális gép rendelkezésre állási csoport csak az egyik tartalék tartomány és a egy frissítési tartomány.
- Nincs virtuálisgép-méretezési csoportok létrehozása nem marketplace felhasználói felülete. Létrehozhat egy méretezési csoportot a sablonok segítségével.
- A virtual machine scale sets méretezési beállításait a portálon nem érhetők el. Áthidaló megoldásként használhatja [Azure PowerShell-lel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.
 
### <a name="networking"></a>Hálózat
- A portál használatával nem hozható létre egy terheléselosztó nyilvános IP-címmel. Áthidaló megoldásként a PowerShell használatával hozza létre a terheléselosztót.
- A hálózati terheléselosztás létrehozásakor létre kell hoznia a cím hálózati címfordítás (NAT) szabályt. Ha nem, akkor hibaüzenetet kap, amikor megpróbál hozzáadni egy NAT-szabályt, a terheléselosztó létrehozása után.
- Miután létrehozott és a társított IP-címet a virtuális gép, egy virtuális gépet (VM) a nyilvános IP-cím nem társításának megszüntetése. Társításának tűnik, de az előzőleg hozzárendelt nyilvános IP-cím társítva az eredeti virtuális gép marad. Ez akkor fordul elő, akkor is, ha új virtuális gép IP-cím ismételt hozzárendelése (más néven egy *virtuális IP-címcsere*). Az összes jövőbeli próbál meg ezt az IP cím eredményt kapcsolaton keresztül a eredetileg kapcsolódó virtuális gép, és a egy újra csatlakozhat. Az új virtuális gép létrehozása jelenleg új nyilvános IP-címek csak használhatjuk.
 
### <a name="sqlmysql"></a>SQL vagy MySQL
- Bérlők adatbázist is létrehozhat egy új SQL vagy MySQL Termékváltozatot előtt egy órát is igénybe vehet. 
- Közvetlenül az SQL- és MySQL üzemeltető kiszolgálók nem az erőforrás-szolgáltató által végrehajtott elemek létrehozása nem támogatott, és nem megfelelő állapotba eredményezhet.
 
### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a a tárolásierőforrás-szolgáltató, mielőtt azok az első Azure-függvény létrehozása az előfizetésben.
 
### <a name="field-replaceable-unit-fru-procedures"></a>A mező helyettesítő egységet (FRU) eljárások

- A lemezképek offline futtatása frissítés közben nem volt. Cserélje le a skálázási egység csomópont van szüksége, ha használható a OEM hardver szállítójával, hogy a felülírt csomópont van a legutóbbi javítási szintet.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az 1710-es frissítési csomag [Itt](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>További lépések

- Az update management az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
