---
title: "Frissítés Azure verem 1712 |} Microsoft Docs"
description: "További tudnivalók az Azure-verem 1712 frissítés Újdonságok integrált rendszerek, az ismert problémák és letöltéséről a frissítést."
services: azure-stack
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: andredm
ms.openlocfilehash: 344fe7496a9129ea1653881a72139fea8a202ff0
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="azure-stack-1712-update"></a>Az Azure verem 1712 frissítés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk fejlesztést és a frissítési csomag ismert problémái ebben a kiadásban, valamint a frissítés letöltése helyét. Ismert problémák vannak osztva ismert problémák közvetlenül kapcsolódik a frissítési folyamat, és a build (telepítés utáni) kapcsolatos ismert problémák.

> [!IMPORTANT]
> Ez a csomag csak akkor alkalmazható, integrált Azure verem rendszerekhez. Ez a csomag nem vonatkoznak a Azure verem szoftverfejlesztői készlet.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure verem 1712 frissítés buildszáma **180106.1**. Ha az ügyfél telepítve van **180103.2** korábban, nem kell alkalmazni a **180106.1**.

## <a name="before-you-begin"></a>Előkészületek

> [!IMPORTANT]
> Ne hozzon létre virtuális gépek a 1712 telepítési folyamat során. Lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) további részleteket.

### <a name="prerequisites"></a>Előfeltételek

Először telepítenie kell az Azure-verem [1711 frissítése](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) frissítés alkalmazása előtt.

### <a name="post-update-steps"></a>Frissítés utáni lépések

A frissítés is kell telepíteni belső vezérlőprogram-frissítésekre OEM partnertől a 1712 Azure verem frissítés telepítésének befejezése után.

> [!NOTE]
> Tekintse meg az OEM partner webhely töltheti le a frissítéseket.

### <a name="new-features-and-fixes"></a>Új szolgáltatásokat és javításokat

A frissítés tartalmazza a következő fejlesztéseket és javításokat Azure verem.

#### <a name="new-features"></a>Új funkciók

- Teszt-AzureStack parancsmag segítségével Azure verem felhőbe privilegizált végpont keresztül érhető el
- Azure verem leválasztott telepítésénél regisztrálása
- A tanúsítvány és a felhasználói fiók lejárati figyelési riasztások
- A BMC-jelszó Elforgatás EGP a hozzáadott frissítés-BMCCredential parancsmag
- Igény szerinti naplózás támogatásához a hálózati naplózás frissítések
- Támogatja a lemezkép-visszaállítási művelet a virtuális gép méretezik beállítása (VMSS)
- Teljes képernyős mód ERCS virtuális gépen CloudAdmin bejelentkezés engedélyezése
- Bérlők automatikusan aktiválhatja Windows virtuális gépek

#### <a name="fixes"></a>Javítások

- Javítsa ki a karbantartási csomópont működési állapot megjelenített javítási futtatása során
- Javítsa ki a megfelelő nyilvános IP-cím használati rekordokat időpontot vagy dátumot stamp módosításait
- Különböző más teljesítmény, stabilitás és biztonsági javítások
- Forrásidő és Defender privilegizált végpont modul hibajavítások

#### <a name="windows-server-2016-new-features-and-fixes"></a>Windows Server 2016 új szolgáltatásokat és javításokat

- [Január, 3.-2018 – KB4056890 (operációsrendszer-verzióval 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - A frissítés a szoftverfrissítések a következők javításait tartalmazza az iparágban használt biztonsági problémára szerint [MSRC biztonsági tanácsadó ADV 180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>A frissítési folyamat szolgáltatással kapcsolatos ismert problémák

Ez a szakasz a 1712 frissítés telepítése során előforduló ismert problémákat.

1. **Jelenség:** Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt: *"szerepkör típusú,"CheckHealth"a"Virtuális gép"jelenik meg, kivétel: \n\nVirtual gépet az állapot-ellenőrzéssel az - ACS01 hozott létre a következő hibák. \nThere Virtuálisgép-adatok beolvasása a gazdagépek hiba történt. Kivétel részletei: \nGet-VM: A művelet nem sikerült Node03 számítógépen: A WS-Management szolgáltatás nem tudja feldolgozni a kérelmet. A WMI \nservice vagy a WMI-szolgáltató ismeretlen hibát adott vissza: HRESULT 0x8004106c. "*
    1. **OK:** probléma okozza-e egy Windows Server-hiba, amely a célja, hogy a Windows server soron következő frissítések figyelembe venni.
    2. **Megoldás:** kapcsolatfelvétel a Microsoft ügyfélszolgálatával és a támogatási szolgálathoz (CSS) segítségért.
<br><br>
2. **Jelenség:** Azure verem operátorok a következő hiba jelenhet meg a frissítési folyamat alatt:*"engedélyezése a virtuális gép nem sikerült, hiba történt a gazdagép-Node03 csomóponton kezdőérték gyűrű: [állomás-Node03] csatlakozás a távoli kiszolgálóhoz gazdagép-Node03 miatt sikertelen volt a a következő hibaüzenet jelenik meg: A Rendszerfelügyeleti webszolgáltatások ügyfélprogram kapott egy HTTP-kiszolgáló hibaállapot (500), de a távoli szolgáltatás nem tartalmazza az összes egyéb információkat a hiba. "*
    1. **OK:** probléma okozza-e egy Windows Server-hiba, amely a célja, hogy a Windows server soron következő frissítések figyelembe venni. 
    2. **Megoldás:** kapcsolatfelvétel a Microsoft ügyfélszolgálatával és a támogatási szolgálathoz (CSS) segítségért.
<br><br>

### <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Ez a szakasz telepítés utáni build szolgáltatással kapcsolatos ismert problémák **180106.1**.

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
- Néhány Piactéri elemek el ebben a kiadásban kompatibilitási megfontolások miatt. Ezek lesznek ismét engedélyezni kell további ellenőrzése után.
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
- Belső Load Balancing (ILB) nem megfelelően kezeli a MAC-címek háttér virtuális gépekhez, amely Linux-példányra.

#### <a name="sqlmysql"></a>SQL/MySQL
- Egy óraba bérlők adatbázisok létrehozhat egy új SQL- vagy MySQL SKU is igénybe vehet.
- Elemet közvetlenül az SQL és MySQL futtató kiszolgálókat, az erőforrás-szolgáltató által el nem végzett létrehozása nem támogatott, és nem megfelelő állapot azt eredményezheti.

    > [!NOTE]
    > Nem kell hatása a meglévő SQL vagy MySQL erőforrás szolgáltató felhasználók számára az Azure-verem integrált rendszerek a 1712 verzióra való frissítésekor. Továbbra is használható a jelenlegi SQL vagy MySQL erőforrás szolgáltató buildek, amíg egy új Azure verem frissítés érhető el.

#### <a name="app-service"></a>App Service
- A felhasználó regisztrálnia kell a storage erőforrás-szolgáltató, ahhoz, hogy az első Azure-függvény létre az előfizetést.

#### <a name="identity"></a>Identitás

Az Azure Active Directory összevonási szolgáltatások (ADFS) környezetben telepített a **azurestack\azurestackadmin** fiók már nincs az alapértelmezett szolgáltató előfizetés tulajdonosának. Való bejelentkezés helyett a **felügyeleti portál / adminmanagement végpont** rendelkező a **azurestack\azurestackadmin**, használhatja a **azurestack\cloudadmin** fiókot, így hogy kezelése, és az alapértelmezett szolgáltató előfizetés használatára.

> [!IMPORTANT]
> Annak ellenére, hogy a **azurestack\cloudadmin** fiók telepített AD FS-környezetben az alapértelmezett szolgáltató előfizetés tulajdonosának, nincs az állomás a távoli asztali engedélyek. Továbbra is használhatja a **azurestack\azurestackadmin** vagy a helyi rendszergazdai fiók bejelentkezési, eléréséhez és a gazdagép kezelése, igény szerint.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure verem 1712 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>További információ

A Microsoft közzétett segítségével figyelheti, és folytathatja a használatával a privilegizált végpont (EGP) frissítés 1712 telepített frissítések.

- Tekintse meg a [figyelése Azure-készletben a rendszerjogosultságú végpont dokumentációnak frissítések](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Lásd még

- Lásd: [kezelheti a frissítéseket az Azure-verem áttekintés](azure-stack-updates.md) a frissítéskezelés Azure verem áttekintését.
- Lásd: [Azure verem frissítések alkalmazása](azure-stack-apply-updates.md) Azure verem frissítések alkalmazásával kapcsolatos további információt.
