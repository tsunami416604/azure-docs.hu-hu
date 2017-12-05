---
title: "Frissítés Azure verem 1711 |} Microsoft Docs"
description: "További tudnivalók az Azure-verem 1711 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: andredm
ms.openlocfilehash: b9f45462fb108ff9cc9039cdb0d0a9ef318fc218
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="azure-stack-1711-update"></a>Az Azure verem 1711 frissítés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és a frissítési csomag ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák vannak osztva ismert problémák közvetlenül kapcsolódik a frissítési folyamat, és a build (telepítés utáni) kapcsolatos ismert problémák.

> [!IMPORTANT]
> Ez a csomag csak akkor alkalmazható, integrált Azure verem rendszerekhez. Ez a csomag nem vonatkoznak a Azure verem szoftverfejlesztői készlet.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure verem 1711 frissítés buildszáma **171201.3**.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

Először telepítenie kell az Azure-verem [1710 frissítése](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) frissítés alkalmazása előtt.

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

A frissítés tartalmazza a következő fejlesztéseket és javításokat Azure verem.

#### <a name="new-features"></a>Új funkciók

- Megoldás sablonok Syndicating támogatása
- Az Azure verem Graph-naplózás és a hibakezelés
- Kapcsolja be a gazdagép be- és kikapcsolása lehetőséget
- Felhasználók is most Windows virtuális gépek automatikus aktiválása
- A hozzáadott kiemelt végpont PowerShell-parancsmag használatával a BitLocker helyreállítási kulcsok megőrzési célokra beolvasása
- A kapcsolat nélküli lemezképek frissítését infrastruktúra frissítésekor támogatása

#### <a name="fixes"></a>Javítások

- Rögzített kialakult versenyhelyzet a DNS-ben mező cserélhető Cisco egységet (FRU) és a naplózás is frissített fürt
- Hárítsa el a disable-állomás mező cserélhető Cisco egységet (FRU) újraindítás képessége
- Különböző más teljesítmény, a biztonság és stabilitását javítások

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 új szolgáltatásokat és javításokat

- [2017. november 14 – KB4048953 (operációsrendszer-verzióval 14393.1884)](https://support.microsoft.com/help/4048953)
 
### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák

Ez a szakasz a 1711 frissítés telepítése során előforduló ismert problémákat.


1. **Jelenség:** Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt: *"name: a telepítés frissítés.", "description": "Install frissítés a Gazdagépeken és Infra virtuális gépekről.", "errorMessage": "írja be a"LiveUpdate"szerepkör" Virtuális gép jelenik meg, kivétel: \n\nThere nincs elég hely a disk.\n\nat <ScriptBlock>, <No file>: line22 ","status":"Error","startTimeUtc":" 2017-11-10T16:46:59.123Z ","endTimeUtc":" 2017-11-10T19:20:29.669Z ","lépések": []"*
    2. **OK:** kevés a szabad lemezterület egy vagy több Azure verem infrastruktúra részét képező virtuális gépek okozta probléma
    3. **Megoldás:** kapcsolatfelvétel a Microsoft ügyfélszolgálatával és a támogatási szolgálathoz (CSS) segítségért.
<br><br>
2. **Jelenség:** Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt:*kivétel "ExtractToFile" hívás a "3" argumentummal: "a folyamat nem éri el a fájlt a(z) <\\<machineName>-ERCS01\C$ \ Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll > "*
    1. **OK:** probléma okozza, ha egy Rendszerjogosultságú végpont (EGP) használatával, amely korábban a portálról frissítésének folytatása folytatódik.
    2. **Megoldás:** kapcsolatfelvétel a Microsoft ügyfélszolgálatával és a támogatási szolgálathoz (CSS) segítségért.
<br><br>
3. **Jelenség:**Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt:*"szerepkör típusú,"CheckHealth"a"Virtuális gép"jelenik meg, egy kivétel: \n\nVirtual gépet az állapot-ellenőrzéssel a <machineName>– előállított ACS01 a a következő hibák. \nThere sikerült beolvasni a Virtuálisgép-adatok gazdagépekről. Kivétel részletei: \nGet-VM: A művelet nem sikerült Node03 számítógépen: A WS-Management szolgáltatás nem tudja feldolgozni a kérelmet. A WMI \nservice vagy a WMI-szolgáltató ismeretlen hibát adott vissza: HRESULT 0x8004106c ".*
    1. **OK:** probléma okozza-e egy Windows Server-hiba, amely a célja, hogy a Windows server soron következő frissítések figyelembe venni.
    2. **Megoldás:** kapcsolatfelvétel a Microsoft ügyfélszolgálatával és a támogatási szolgálathoz (CSS) segítségért.
<br><br>
4. **Jelenség:**Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt:*""URP"szerepkör"DefenderUpdate"típusú kivételt okozott: sikertelen első verziójában \\SU1FileServer\SU1_Public\ DefenderUpdates\x64\{fájlnév} .exe után 60 kísérletek másolási AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: 262" sor*
    1. **OK:** probléma okozza-e a Windows Defender definíciófrissítések hibás vagy hiányos háttérben történő letöltését.
    2. **Megoldás:** legfeljebb 8 óra elteltével a frissítés folytatásához győződjön kísérlet eltelt próbálja az első frissítés óta.

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Ez a szakasz telepítés utáni build szolgáltatással kapcsolatos ismert problémák **20171201.3**.

#### <a name="portal"></a>Portál

- Nem lehet a számítási és tárolási erőforrások megtekintése a felügyeleti portálon. Ez azt jelzi, hogy hiba történt a frissítés telepítése során, és, hogy a frissítés nem megfelelően történt a következő sikeres. Ha a probléma akkor fordul elő, lépjen kapcsolatba Microsoft CSS segítségért.
- A portál üres irányítópult jelenhet meg. Az irányítópult helyreállításához válassza ki a fogaskerék ikonra a portál jobb felső sarokban, majd válassza ki **alapértelmezett beállításainak visszaállítása**.
- Erőforráscsoport tulajdonságainak megtekintésekor a **áthelyezése** gomb le van tiltva. Ez az elvárt viselkedés. Erőforráscsoportok áthelyezése másik előfizetések jelenleg nem támogatott.
- Egyetlen olyan munkafolyamat, ahol ki kell választania egy előfizetés, a csoport vagy a hely egy legördülő listában, a legalább egy, az alábbi problémákat tapasztalhatja:

   - Megjelenik egy üres sort a lista tetején. Továbbra is kell tudni jelöljön ki egy elemet várt módon.
   - Ha a legördülő listán szereplő elemek listája rövid, nem lehet a cikk bármelyike megtekintheti.
   - Ha több felhasználó-előfizetéssel rendelkezik, az erőforrás csoport legördülő lista üres is lehet. 

        > [!NOTE]
        > Az utolsó két problémák megoldása érdekében adhatja meg az előfizetés vagy az erőforráscsoport (ha tudja) neve, vagy a PowerShell segítségével helyette.

- Felhasználói előfizetések eredmények az árva erőforrások törlése. A probléma megoldásához először törölnie a felhasználói erőforrásokat és a teljes erőforráscsoport, és törölje a felhasználó előfizetések.
- Nem tudunk az előfizetéséhez, a Azure verem portálok engedélyek megtekintése. A probléma megoldásához engedélyek PowerShell használatával ellenőrizheti.

#### <a name="health-and-monitoring"></a>Állapot- és figyelés

- Ha újraindítja az infrastruktúra-szerepkör példánya, az újraindítás sikertelen jelző üzenet jelenhet meg. Azonban az újraindítás ténylegesen sikeresen befejeződött.

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
- Egy nyilvános IP-címet a virtuális gép (VM) nem társítását, miután a virtuális gép létrehozása és társított IP-címet. Disassociation tűnik, de a korábban hozzárendelt nyilvános IP-cím marad az eredeti virtuális társítva. Ez akkor fordul elő, akkor is, ha egy új virtuális géphez az IP-cím ismételt hozzárendelése (más néven a *virtuális IP-címcsere*). Az összes jövőbeni megpróbál a kapcsolaton keresztül a eredetileg társított virtuális Gépet, és nem egy IP-cím eredményén keresztül kapcsolódni. Új virtuális gépek létrehozására jelenleg, új nyilvános IP-címek csak használhatjuk.
- Lehet, hogy az Azure verem operátorok nem lehet telepíteni, törlése, módosítása a Vnetek vagy a hálózati biztonsági csoportok. A probléma főként látható ugyanazon csomag későbbi frissítési kísérletek. Ennek oka egy frissítést, amely jelenleg vizsgált egy csomagolási kapcsolatos problémát.
 
#### <a name="sqlmysql"></a>SQL/MySQL
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet. 
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.
 
#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.

#### <a name="identity"></a>Identitás

Az Azure Active Directory összevonási szolgáltatások (ADFS) környezetben telepített a **azurestack\azurestackadmin** fiók már nincs az alapértelmezett szolgáltató előfizetés tulajdonosának. Való bejelentkezés helyett a **felügyeleti portál / adminmanagement végpont** rendelkező a **azurestack\azurestackadmin**, használhatja a **azurestack\cloudadmin** fiókot, így hogy kezelése, és az alapértelmezett szolgáltató előfizetés használatára.

> [!IMPORTANT]
> Annak ellenére, hogy a **azurestack\cloudadmin** fiók telepített AD FS-környezetben az alapértelmezett szolgáltató előfizetés tulajdonosának, nincs az állomás a távoli asztali engedélyek. Továbbra is használhatja a **azurestack\azurestackadmin** vagy a helyi rendszergazdai fiók bejelentkezési, eléréséhez és a gazdagép kezelése, igény szerint.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure verem 1711 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>További információ

A Microsoft közzétett segítségével figyelheti, és folytathatja a használatával a privilegizált végpont (EGP) frissítés 1711 telepített frissítések.

- Tekintse meg a [figyelése Azure-készletben a rendszerjogosultságú végpont dokumentációnak frissítések](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Lásd még:

- Lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md) a frissítéskezelés Azure verem áttekintését.
- Lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md) Azure verem frissítések alkalmazásával kapcsolatos további információt.
