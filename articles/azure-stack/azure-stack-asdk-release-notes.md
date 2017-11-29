---
title: "A Microsoft Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései |} Microsoft Docs"
description: "Fejlesztések, javítások és Azure verem szoftverfejlesztői készlet kapcsolatos ismert problémák."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: 56d8ad4fecf14dfa69ade43438672c31a4954209
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Az Azure verem szoftverfejlesztői készlet kibocsátási megjegyzései

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

A kibocsátási megjegyzések fejlesztései, javítások és Azure verem szoftverfejlesztői készlet ismert problémáira vonatkozó információkat tartalmazzák. Ha nem biztos abban, hogy melyik verzióját futtatja, akkor [a portál használatával ellenőrizze](azure-stack-updates.md#determine-the-current-version).

## <a name="build-201711221"></a>Build 20171122.1

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

- Tekintse meg a [új szolgáltatásokat és javításokat](azure-stack-update-1711.md#new-features-and-fixes) az Azure verem 1711 frissítés kibocsátási megjegyzései Azure verem szakasza integrált rendszerek.

    > [!IMPORTANT]
    > A elemek egy része szerepel a **új szolgáltatásokat és javításokat** szakasz csak integrált Azure verem rendszerek szempontjából.

### <a name="known-issues"></a>Ismert problémák
 
#### <a name="deployment"></a>Környezet
- Adjon meg egy IP-cím szerint üzembe helyezése során.

#### <a name="infrastructure-management"></a>Infrastruktúra felügyelete
- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.
- Az alaplapi felügyeleti vezérlővel (BMC) IP-cím és a modell nem láthatók a lényeges információk egy méretezési egység csomópont. Ez az elvárt viselkedés Azure verem csomagban.

#### <a name="portal"></a>Portál
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
-  Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

   Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.

- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.
- Az a **szükséges aktiválási** figyelmeztető riasztás részleteit, ne kattintson a hivatkozásra kattintva a **AzureBridge** összetevő. Ha így tesz, a **áttekintése** panel sikertelenül megkísérli betölteni, és nem túllépi az időkorlátot.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem áll az előfizetés engedéllyel megtekintheti a verem Azure portál használatával. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.
 
#### <a name="marketplace"></a>Piactér
- Ha megpróbálja elemek hozzáadására a verem Azure piactér használatával a **hozzáadása az Azure-ból** beállítás, nem minden elem esetleg mások is láthatják letölthető.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre. 
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

#### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
- Lehet, hogy az Azure verem operátorok nem lehet telepíteni, törlése, módosítása a Vnetek vagy a hálózati biztonsági csoportok. A probléma főként látható ugyanazon csomag későbbi frissítési kísérletek. Ennek oka egy frissítést, amely jelenleg vizsgált egy csomagolási kapcsolatos problémát.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
 
#### <a name="usage-and-billing"></a>Használat és számlázás
- Nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

#### <a name="identity"></a>Identitás

Az Azure Active Directory összevonási szolgáltatások (ADFS) környezetben telepített a **azurestack\azurestackadmin** fiók már nincs az alapértelmezett szolgáltató előfizetés tulajdonosának. Való bejelentkezés helyett a **felügyeleti portál / adminmanagement végpont** rendelkező a **azurestack\azurestackadmin**, használhatja a **azurestack\cloudadmin** fiókot, így hogy kezelése, és az alapértelmezett szolgáltató előfizetés használatára.

> [!IMPORTANT]
> Még a **azurestack\cloudadmin** fiók telepített AD FS-környezetben az alapértelmezett szolgáltató előfizetés tulajdonosának, nincs az állomás a távoli asztali engedélyek. Továbbra is használhatja a **azurestack\azurestackadmin** vagy a helyi rendszergazdai fiók bejelentkezési, eléréséhez és a gazdagép kezelése, igény szerint.


## <a name="build-201710201"></a>Build 20171020.1

### <a name="improvements-and-fixes"></a>Kidolgozott fejlesztéseit és javításokat

Kidolgozott fejlesztéseit és javításairól a 20171020.1 build listája, olvassa el a [kidolgozott fejlesztéseit és javításokat](azure-stack-update-1710.md#improvements-and-fixes) az Azure-verem 1710 kibocsátási megjegyzései című integrált rendszerek. A elemek egy része szerepel a "további minőségének javítása és javításokat" szakaszban csak vonatkoznak integrált rendszerek.

Emellett a következő javítások történtek:
- Rögzített problémát, ahol a számítási erőforrás-szolgáltató megjelenik-e a állapota ismeretlen.
- Megtörtént egy probléma javítása ahol kvóták előfordulhat, hogy nem jelennek meg a felügyeleti portál után hozza létre a címzetteket, és később próbálja meg terv a részletek megtekintéséhez.

### <a name="known-issues"></a>Ismert problémák

#### <a name="powershell"></a>PowerShell
- A kiadási AzureRM 1.2.11 PowerShell modul megtörje a változások listájának tartalmaz. További információ a 1.2.10 verzióról verzió, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).
 
#### <a name="deployment"></a>Környezet
- Adjon meg egy IP-cím szerint üzembe helyezése során.

#### <a name="infrastructure-management"></a>Infrastruktúra felügyelete
- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.
- Az alaplapi felügyeleti vezérlővel (BMC) IP-cím és a modell nem láthatók a lényeges információk egy méretezési egység csomópont. Ez az elvárt viselkedés Azure verem csomagban.

#### <a name="portal"></a>Portál
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
-  Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

   Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.

- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.
- Az a **szükséges aktiválási** figyelmeztető riasztás részleteit, ne kattintson a hivatkozásra kattintva a **AzureBridge** összetevő. Ha így tesz, a **áttekintése** panel sikertelenül megkísérli betölteni, és nem túllépi az időkorlátot.
- A felügyeleti portálon, megjelenik egy **sikerült beolvasni a bérlők** a hiba a **értesítések** terület. Ez a hiba nyugodtan figyelmen kívül hagyhatja.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem áll az előfizetés engedéllyel megtekintheti a verem Azure portál használatával. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.
 
#### <a name="marketplace"></a>Piactér
- Ha megpróbálja elemek hozzáadására a verem Azure piactér használatával a **hozzáadása az Azure-ból** beállítás, nem minden elem esetleg mások is láthatják letölthető.
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre. 
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.
- A virtuálisgép-méretezési csoportok skálázási beállításai nem érhetők el a portálon. Áthidaló megoldásként használja [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). PowerShell-verzió különbségek, miatt kell használnia a `-Name` paraméter helyett `-VMScaleSetName`.

#### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
 
#### <a name="sqlmysql"></a>SQL/MySQL 
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
 
#### <a name="usage-and-billing"></a>Használat és számlázás
- Nyilvános IP-használat mérési adatokat jeleníti meg az azonos *EventDateTime* érték az egyes rekordokhoz ahelyett, hogy a *TimeDate* tájékoztat, hogy a rekord létrehozásának stamp. Ezeket az adatokat jelenleg nem használható a nyilvános IP-cím használatának pontos lehetet.

## <a name="build-201709283"></a>Build 20170928.3

### <a name="known-issues"></a>Ismert problémák

#### <a name="powershell"></a>PowerShell
- A kiadási AzureRM 1.2.11 PowerShell modul megtörje a változások listájának tartalmaz. További információ a 1.2.10 verzióról verzió, lásd: a [áttelepítési útmutató](https://aka.ms/azspowershellmigration).

#### <a name="deployment"></a>Környezet
- Adjon meg egy IP-cím szerint üzembe helyezése során.

 #### <a name="infrastructure-management"></a>Infrastruktúra felügyelete
- Ne engedélyezze a infrastruktúra biztonsági mentés a **infrastruktúra biztonsági mentés** panelen.
- A számítási erőforrás-szolgáltató egy ismeretlen állapotát jeleníti meg.
- Az alaplapi felügyeleti vezérlővel (BMC) IP-cím és a modell nem láthatók a lényeges információk egy méretezési egység csomópont. Ez az elvárt viselkedés Azure verem csomagban. 
   
#### <a name="portal"></a>Portál
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
- Megjelenik egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.
- Az a **szükséges aktiválási** figyelmeztető riasztás részleteit, ne kattintson a hivatkozásra kattintva a **AzureBridge** összetevő. Ha így tesz, a **áttekintése** panel sikertelenül megkísérli betölteni, és nem túllépi az időkorlátot.
- Kvóták előfordulhat, hogy nem jelennek meg a felügyeleti portálon után hozza létre a címzetteket, és próbálja meg később nézet a csomag részletes adatait. Megoldás a **szolgáltatások és a kvóták**, kattintson a **Hozzáadás**, és adjon hozzá egy új bejegyzést.
- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem tudunk az előfizetéséhez, a Azure verem portálok engedélyek megtekintése. A probléma megoldásához engedélyek Powershell használatával ellenőrizheti.
  
#### <a name="marketplace"></a>Piactér
- Felhasználók megkeresheti a teljes piactérre előfizetés nélkül, és láthatja például tervek és ajánlatok felügyeleti elemeket. Ezek az elemek nem működőképes a felhasználók számára is.
 
#### <a name="compute"></a>Számítás
- Felhasználók rendszer felajánlja a lehetőséget a virtuális gép létrehozása a georedundáns tárolást. E konfiguráció hatására a virtuális gép nem hozható létre.
- Beállíthatja, hogy a virtuális gép rendelkezésre állási csoportban, csak az egyik tartalék tartomány, és egy frissítési tartomány.
- Nincs nincs Piactéri élmény virtuálisgép-méretezési csoportok létrehozásához. A skála beállítása egy sablon használatával hozhat létre.

#### <a name="networking"></a>Hálózat
- Nyilvános IP-címek a terheléselosztó nem létrehozása a portál használatával. A probléma megoldásához a PowerShell hozhat létre a terheléselosztó hasonló adataival.
- Hálózati terheléselosztó létrehozásakor létre kell hoznia a hálózati cím címfordítási (NAT) szabály. Ha ezt elmulasztja, kapunk hiba történt egy NAT-szabály hozzáadása a terheléselosztó létrehozása után.
- A **hálózati**, ha **kapcsolat** egy VPN-kapcsolat beállítása **VNet – VNet** van megadva, a lehetséges kapcsolattípus. Válassza ezt a beállítást. Jelenleg csak a **pont-pont (IPsec)** lehetőség.
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.

#### <a name="sqlmysql"></a>SQL/MySQL
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.
