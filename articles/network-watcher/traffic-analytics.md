---
title: Azure-forgalmat Analytics |} Microsoft Docs
description: "Útmutató: Azure hálózati biztonsági csoport folyamata naplók és a forgalom Analytics elemzése."
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: c113bbe646a54813a2885b3a9087a0171220f271
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="traffic-analytics"></a>Traffic Analytics

Forgalom Analytics egy felhőalapú megoldás, amely felhőalapú hálózatokhoz a felhasználói és alkalmazás tevékenységek láthatósága. Forgalom elemzés elemzi a hálózati figyelőt hálózati biztonsági csoport (NSG) folyamat bejegyzéseit, amelyek a forgalom áramlását az Azure felhőben betekintést. A forgalom Analytics segítségével:

- Hálózati tevékenységek jelenítheti meg az Azure-előfizetések között, és azonosíthatja a csatlakozási pontokhoz.
- Biztonsági fenyegetéseket jelezhetnek határozza meg, és a hálózat, például a Megnyitás-portok, internet-hozzáféréssel, és a virtuális gépek (VM) támadó hálózatok csatlakoztatása alkalmazásokat adatokkal biztonságáról.
- Ismerje meg a folyamat forgalmat Azure-régiók és a saját hálózati telepítéséhez a teljesítmény és a kapacitás optimalizálása érdekében az interneten keresztül.
- A rögzítési ponthoz vezető sikertelen kapcsolatok a hálózaton lévő hálózati konfigurációs hibák.

## <a name="why-traffic-analytics"></a>Miért forgalom Analytics?

Elengedhetetlen, figyeléséhez, kezeléséhez, és ismeri a saját hálózati biztonsági szempontból sértetlen biztonsági, megfelelőségi és teljesítmény. A saját környezetben tudatában van kiemelkedő fontosságú védelemmel való ellátásához és optimalizálhatja azt. Gyakran van szükség, hogy a jelenlegi állapotában a hálózat, akik kapcsolódik, mely portokon is nyitva csatlakozik az internethez, a várt hálózati működés, szabálytalan hálózati probléma, és a forgalom hirtelen nő.

Felhőalapú hálózatokhoz eltérnek a helyszíni vállalati hálózatokban, Netflow vagy azzal egyenértékű protokollt támogató útválasztók és kapcsolókat, amelyek a lehetőségét nyújtani az IP-hálózati forgalom gyűjtése lép, vagy kilép egy hálózati adapter esetében. Forgalom folyamata adatok elemzése, hálózati forgalmat, és a kötet elemzésének hozhat létre.

Egy Azure virtuális hálózatot rendelkezik ismertetik, hogy érkező NSG folyamata naplókat, és egyes hálózati adapterek, virtuális gépek és alhálózatok tartozó virtuális IP-forgalom a hálózati biztonsági csoporton keresztül. Nyers NSG elemzésével flow a naplókat, és biztonsági, topológia és geográfiai eszközintelligencia beszúrni, forgalom Analytics adja meg a forgalom áramlását környezetében betekintést. Forgalom Analytics leggyakrabban kommunikáló állomások, legtöbb kommunikáló alkalmazás-protokollokra, a beszélgetést állomás párok, engedélyezett/letiltott forgalom, bejövő/kimenő forgalom, portjain internet, legtöbb blokkoló szabályokat, forgalom vonatkozó információkat biztosít terjesztési / Azure-adatközpontban, virtuális hálózatot, alhálózatot, vagy a támadó hálózatok.

## <a name="key-components"></a>A legfontosabb összetevők 

- **Hálózati biztonsági csoport (NSG)**: felsorolja azokat a szabályokat, amelyek egy Azure virtuális hálózathoz kapcsolódó erőforrások hálózati adatforgalom engedélyezéséhez vagy letiltásához. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC). További információkért lásd: [hálózati biztonsági csoport – áttekintés](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Hálózati biztonsági csoport (NSG) folyamat naplók**: bemenő és kimenő IP-forgalom a hálózati biztonsági csoportok használatával kapcsolatos információk megtekintéséhez. NSG folyamata naplók json formátumban vannak megírva, és megjelenítése a kimenő és bejövő adatfolyamok / szabály alapján, a hálózati adapter a folyamat vonatkozik, 5 rekordos információ a folyamattal (forrás vagy a cél IP-cím forrás vagy a cél-port és protokoll), és ha a forgalom engedélyezve lett, vagy megtagadva. NSG folyamata naplók kapcsolatos további információkért lásd: [NSG folyamata naplók](network-watcher-nsg-flow-logging-overview.md).
- **Naplófájl Analytics**: Azure szolgáltatás, amely figyelési adatokat gyűjt, és tárolja az adatok egy központi tárházban. Ezek az adatok tartalmazhatnak eseményeket, teljesítményadatokat vagy egyéni adatok az Azure API-n keresztül megadott. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz. Alkalmazások figyelése, például a Naplóelemzési alapjaként használatával készített hálózati Teljesítményfigyelő és a forgalom elemzés. További információkért lásd: [analytics jelentkezzen](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **A Naplóelemzési munkaterület jelentkezzen**: Log Analytics egy Azure-fiókra vonatkozó adatok tárolására példánya. Napló Analytics munkaterületekkel kapcsolatos további információkért lásd: [Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Hálózati figyelő**: egy regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása egy Azure hálózati forgatókönyv szintjén feltételek. Ha bekapcsolja NSG folyamata naplók be- és kikapcsolását és a hálózati figyelőt. További információkért lásd: [hálózati figyelőt](network-watcher-monitoring-overview.md#network-watcher).

## <a name="how-traffic-analytics-works"></a>Hogyan működik a forgalom elemzés 

Forgalom Analytics megvizsgálja a nyers NSG folyamata naplókat, és közös forgalom az azonos forrás IP-címe, cél IP-címe, célport és protokoll között összesítésével csökkentett naplók rögzíti. Például a gazdagép-1 (IP-cím: 10.10.10.10) kommunikál a gazdagép 2 (IP-cím: 10.10.20.10), 100 alkalommal 1 óra (például 80-as) és protokollt (például http) segítségével egy adott időszakban. A csökkentett naplóban szerepel egy vagy több bejegyzése, 1 óra porton keresztül egy meghatározott időtartam során 100 alkalommal közölt állomás 1 és 2 gazdagép *80* és protokoll *HTTP*, ahelyett, hogy 100 bejegyzéseket. Csökkentett naplók a Naplóelemzési munkaterület tárolja és földrajzi hely, a biztonság és a topológia információk bővül. A továbbfejlesztett naplók további elemzés kapcsolattípusokból elemzése. Az alábbi képen látható az adatfolyam:

![Hogyan működik a forgalom elemzés](media/traffic-analytics/1.png)

## <a name="supported-regions"></a>Támogatott régiók

Forgalom Analytics előzetes verzió érhető el. Az előzetes funkciók nem rendelkeznek azonos szintű rendelkezésre állást és megbízhatóságot, mint a szolgáltatások általában kiadási.  Az előzetes kiadásban használhat forgalom Analytics NSG-ket, sem a következő régióban: nyugati középső Régiójában, USA keleti régiója, USA keleti régiója 2, északi középső Régiójában, déli középső Régiójában, USA középső RÉGIÓJA, USA nyugati régiója, USA nyugati régiója-2, Nyugat-Európa, Észak-Európa, Nyugat UK, Dél-UK, Kelet-Ausztrália , és Ausztrália délkeleti. A Naplóelemzési munkaterület léteznie kell a nyugati középső Régiójában, USA keleti régiója, Nyugat-Európában, Ausztrália délkeleti vagy déli UK régió.

## <a name="prerequisites"></a>Előfeltételek

### <a name="enable-network-watcher"></a>Engedélyezze a hálózati figyelőt 

Elemezze az adatforgalmat, hogy szüksége van egy meglévő hálózati figyelőt, vagy [engedélyezése az Azure hálózati figyelőjét](network-watcher-create.md) minden régióban, hogy rendelkezik-e az elemezni kívánt NSG-ket forgalmi a. Forgalom Analytics engedélyezhető az NSG-ket egyikében sem található a [támogató régiók](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Regisztrálja újra a hálózati erőforrás-szolgáltató 

Forgalom Analytics az előzetes használata előtt újra regisztrálnia kell a hálózati erőforrás-szolgáltató. Kattintson a **kipróbálás** a következő kód mezőbe az Azure-felhő rendszerhéj megnyitásához. A felhő rendszerhéj automatikusan jelentkezik be, az Azure-előfizetéshez. Miután a felhő rendszerhéj meg nyitva, adja meg a következő parancs futtatásával regisztrálja újra a hálózati erőforrás-szolgáltató:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Válassza ki a hálózati biztonsági csoport 

Ahhoz, hogy a naplózás NSG folyamatot, rendelkeznie kell a hálózati biztonsági csoport az adatfolyamok bejelentkezni. Ha a hálózati biztonsági csoport nem rendelkezik, tekintse meg a [hálózati biztonsági csoport létrehozása](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) kattintva létrehozhat egyet.

Az Azure portál bal oldalán válassza ki a **figyelő**, majd **hálózati figyelőt**, majd válassza ki **NSG folyamata naplók**. Válassza ki a hálózati biztonsági csoportot, hogy engedélyezni szeretné egy NSG folyamata naplóban, az alábbi ábrán látható módon:

![Válassza ki az NSG](media/traffic-analytics/2.png)

Ha mégis megpróbálja forgalom Analytics egy NSG-t, amely minden régióban eltérő engedélyezése a [támogató régiók](#supported-regions), a "Nem található" hibaüzenet. 

## <a name="enable-flow-log-settings"></a>Attribútumfolyam naplófájl-beállítások engedélyezése

Ahhoz, hogy a forgalom napló beállításai között, végre kell hajtania a következő feladatokat:

Az Azure Insights szolgáltató lesz regisztrálva, ha nem már regisztrálva van az előfizetéséhez:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Ha még nem rendelkezik jelentkezik be a NSG folyamata tárolásához Azure Storage-fiók, létre kell hoznia egy tárfiókot. A következő paranccsal létrehozhat egy tárfiókot. A parancs futtatása előtt cserélje le a `<replace-with-your-unique-storage-account-name>` , minden Azure helyszínen, 3 – 24 karakter hosszúságúnak, egyedi névvel használatával csak számokat és kisbetűket tartalmazhatnak. Az erőforráscsoport neve, szükség esetén is módosíthatja.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Válassza ki a következő beállításokat, a képen látható módon:

1. Válassza ki *a* a **állapota**
2. Válassza ki a folyamat-naplók tárolására meglévő tárfiókot. Ha az adatok végtelen tárolni szeretné, a értékre állítani *0*. A tárfiók díja Azure Storage függő díj.
3. Állítsa be **megőrzési** szeretné tárolni az adatokat a napok száma.
4. Válassza ki *a* a **forgalmat, elemzési állapot**.
5. Jelöljön ki egy meglévő Naplóelemzés (OMS) munkaterülettel, vagy **új munkaterület létrehozása** számára hozzon létre egy újat. A Naplóelemzési munkaterület forgalom Analytics tárolására szolgál majd előállítására szolgál a analytics összesített és indexelt adatokat. Ha egy meglévő munkaterülettel, azt már léteznie kell egy a [támogató régiók](#traffic-analytics-supported-regions) és az új lekérdezés nyelvének frissítése után. Ha nem kíván egy meglévő munkaterület frissítése, vagy nem rendelkezik a munkaterületen egy támogatott régióban, hozzon létre egy újat. Lekérdezési nyelv kapcsolatos további információkért lásd: [Azure Naplóelemzés frissítsen az új naplófájl-keresési](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    A Naplóelemzés (OMS) munkaterületen, a forgalom elemzési megoldások és az NSG-k üzemeltetéséhez nem kell ugyanabban a régióban kell. Például előfordulhat, hogy forgalom Analytics Nyugat-Európában régióban munkaterület során előfordulhat, hogy az NSG-k USA keleti régiója, és az USA nyugati régiója. Több NSG-ket az ugyanazon a munkaterületen konfigurálhatók.
6. Kattintson a **Mentés** gombra.

    ![Forgalom analytics engedélyezése](media/traffic-analytics/3.png)

Ismételje meg az előző lépést, amelynek a forgalom elemzés számára engedélyezni szeretné bármely más NSG. Attribútumfolyam naplókból adatokat küldi el a munkaterületet, ezért figyeljen oda arra, hogy a helyi jogszabályok és az Ön országában rendelkezések lehetővé tegyék az adattárolás a régióban, ahol a munkaterületen található.

## <a name="view-traffic-analytics"></a>Nézet forgalom elemzés

Válassza a bal oldali a portál, **minden szolgáltatás**, majd adja meg *figyelő* a a **szűrő** mezőbe. Ha **figyelő** megjelenik a keresési eredmények között, jelölje be. Fedezze fel forgalom elemzés és platformképességei, jelölje be **hálózati figyelőt**, majd **forgalom Analytics (előzetes verzió)**.

![Nézet forgalom elemzés](media/traffic-analytics/4.png)

Az irányítópult először jelennek meg, mert a forgalom Analytics először kell összesíteni elég adat ahhoz, hogy a jelentéssel bíró insights származnia, mielőtt hozhat létre a jelentéseket akár 30 percet is igénybe vehet.

## <a name="usage-scenarios"></a>Felhasználási területek

Ahhoz, hogy a forgalom Analytics teljesen konfigurálása után érdemes feltárása némelyike az alábbiak szerint:

### <a name="find-traffic-hotspots"></a>Forgalom elérési pontokhoz való keresése

**keresni**

- Mely állomások küldésekor és fogadásakor, a legtöbb forgalom?
    - Mely állomások küldésekor és fogadásakor, a legtöbb forgalmat ismertetése segítségével azonosítják a gazdagépeket, amelyek a legtöbb forgalmat feldolgozás alatt.
    - Ha a kötet forgalom megfelelő gazdagép kiértékelheti. A kötet forgalom normál működés, vagy nem azt kell további vizsgálat?
- Bejövő/kimenő forgalom van?
    -   A gazdagép várhatóan több bejövő forgalmat, mint a kimenő, vagy fordítva fogadni?
- A forgalom engedélyezett/letiltott statisztikai adatait.
    - Miért van egy gazdagép engedélyezésére vagy jelentős mennyiségű forgalom blokkolása?

    Válassza ki **további részleteket**a **legtöbb forgalmat állomások**, az alábbi ábrán látható módon:

    ![Forgalom analytics engedélyezése](media/traffic-analytics/5.png)

- Az alábbi képen látható, a virtuális gépek a felső öt talking gazdagépek és a folyamat kapcsolatos információkhoz (engedélyezett – a bejövő/kimenő és a letiltott - bejövő/kimenő forgalom) trendek idő:

    ![](media/traffic-analytics/6.png)

**keresni**

- Ezek a legtöbb conversing állomás párok?
    - Első end-háttérrendszer kommunikáció vagy a szokásostól viselkedését, például a háttér-internetes forgalom például viselkedés várható.
- Engedélyezett vagy letiltott forgalom statisztikák
    - Miért állomás engedélyezésére vagy jelentős forgalmat blokkolja
- A leggyakrabban használt alkalmazás protokoll legtöbb conversing állomás párok között:
    - Ezek az alkalmazások engedélyezettek a hálózaton?
    - Helyesen van konfigurálni az alkalmazások? Akkor használja a megfelelő protokoll kommunikációs? Válassza ki **további részleteket** alatt **legtöbb gyakran használják a beszélgetés**, az alábbi ábrán látható:

    ![](media/traffic-analytics/7.png)

- Az alábbi képen látható, a felső öt témák a trendek idő és a folyamat kapcsolatos részletek például engedélyezett és letiltott a bejövő és kimenő forgalom a beszélgetés pár:

    ![](media/traffic-analytics/8.png)

**keresni**

- Mely alkalmazásprotokoll legtöbb használja őket a környezetben, és melyik conversing állomás párok a legtöbb az alkalmazás protokoll használata?
    - Ezek az alkalmazások engedélyezettek a hálózaton?
    - Helyesen van konfigurálni az alkalmazások? Akkor használja a megfelelő protokoll kommunikációs? Várt működése közös portokon például a 80-as és 443-as. A szabványos kommunikációs szokatlan portja jelennek meg, előfordulhat, hogy igényelnek-e a konfiguráció módosítása. Válassza ki **további részleteket** alatt **alkalmazásprotokollokon felső**, az alábbi képen látható:

    ![](media/traffic-analytics/9.png)

- Az alábbi képek idő megjelenítése a 7. protokollként a legjobb öt 7. protokollok és a folyamat kapcsolatos részletes adatait (például engedélyezett és letiltott a adatfolyamok) trendek:

    ![](media/traffic-analytics/10.png)

    ![](media/traffic-analytics/11.png)

**keresni**

- A kapacitás kihasználtsági trendek egy VPN-átjáró a környezetben.
    - Minden VPN SKU lehetővé teszi, hogy bizonyos mennyiségű sávszélesség. A VPN-átjárók kihasználva?
    - Az átjárók, eléri a kapacitása? Kell frissítenie a következő magasabb SKU?
- A legtöbb conversing gazdagépek, mely VPN-átjárón keresztül ezek mely porton keresztül?
    - Ez a minta nem jelent problémát? Válassza ki **további részleteket** alatt **aktív VPN-kapcsolatok felső**, az alábbi ábrán látható módon:

    ![](media/traffic-analytics/12.png)

- Az alábbi képen látható, az Azure VPN Gateway és a folyamat kapcsolatos részletes adatait (például megengedett folyamatok és portok) a kapacitás kihasználtságát trendek idő:

    ![](media/traffic-analytics/13.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Földrajzi hely forgalomeloszlás megjelenítése

**keresni**

- Egy adatközpont például felső források forgalom adatközpontba, az adatközpont és alkalmazás-protokollokra beszélgetést felső a beszélgetést felső engedélyezetlen hálózatok forgalomeloszlás.
    - Ha egy adott adatközpont további terhelése, megtervezheti, hatékony forgalom terjesztéshez.
    - Ha engedélyezetlen hálózatok vannak beszélgetést az adatközpontban, majd javítsa NSG-szabályok letilthatja azokat.

    Válassza ki **Ide kattintva megtekintheti az élő geomap** alatt **terjesztési forgalom Azure-adatközpont között**, az alábbi ábrán látható módon:

    ![](media/traffic-analytics/14.png)

- A földrajzi térképen jeleníti meg a paraméterek kiválasztásának felső menüszalag, például az adatközpontok (üzembe helyezett/No-telepítési/Active/Inactive/forgalom Analytics engedélyezett/forgalom Analytics nincs engedélyezve) és az aktív Benign/esetleg kártékony forgalmat hozzájáruló országok telepítés:

    ![](media/traffic-analytics/15.png)

- A földrajzi térképen forgalom eloszlását mutatja egy adatközpontba országokból és az egyes földrészekre legyen (jóindulatú forgalom) kék és piros (rosszindulatú forgalmat) sorok színes való kommunikáció során:

    ![](media/traffic-analytics/16.png)

    ![](media/traffic-analytics/17.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Virtuális hálózatok megjelenítése színtartományok forgalom eloszlása

**keresni**

- Egy virtuális hálózat, a Kiszolgálótopológia, a felső források a virtuális hálózat felé irányuló forgalom a virtuális hálózati és alkalmazás-protokollokra beszélgetést felső beszélgetést felső engedélyezetlen hálózatok forgalomeloszlás.
    - Hogy tudnák, melyik virtuális hálózati beszélgetést mely virtuális hálózathoz. Ha a téma nem várt, javíthatóak.
    - Ha engedélyezetlen hálózatok a vannak a virtuális hálózati beszélgetést, blokkolja a rosszindulatú hálózatok NSG-szabályok javíthatja ki.
 
    Válassza ki **Ide kattintva megtekintheti a VNet forgalmát topológia** alatt **virtuális hálózati terjesztési**, az alábbi ábrán látható módon: 

        ![](media/traffic-analytics/18.png)

- A virtuális hálózati topológia paraméterek, például egy virtuális hálózat (Inter virtuális hálózati kapcsolatok/Active/Inactive) kiválasztását, külső kapcsolatot, aktív folyamatok és a virtuális hálózat rosszindulatú adatfolyamok felső menüszalagján látható.
- A virtuális hálózati topológia eloszlását mutatja a forgalom egy virtuális hálózathoz jelenítik adatfolyamok (engedélyezett/letiltott/bejövő/kimenő/Benign/esetleg kártékony) protokoll és hálózati biztonsági csoportok, például:

    ![](media/traffic-analytics/19.png)

    ![](media/traffic-analytics/20.png)

**keresni**

- Forgalomeloszlás száma alhálózat, a Kiszolgálótopológia, a felső források az alhálózat felé irányuló forgalom beszélgetést az alhálózat, és alkalmazás-protokollokra beszélgetést felső felső rouge hálózatok.
    - Ismerik, mely alhálózat mely alhálózathoz beszélgetést. Ha váratlan beszélgetéseket, javíthatja ki a konfigurációt.
    - Ha rouge hálózatok alhálózattal vannak beszélgetést, akkor is NSG-szabályok például blokkolja a rosszindulatú hálózatok konfigurálásával javítható ki.
- Az alhálózatok topológia paraméterek például Active/Inactive alhálózati, külső kapcsolatot, aktív folyamatok és az alhálózat rosszindulatú adatfolyamok kiválasztásának felső menüszalagján látható.
- Az alhálózati topológia eloszlását mutatja a forgalom egy virtuális hálózathoz jelenítik adatfolyamok (engedélyezett/letiltott/bejövő/kimenő/Benign/esetleg kártékony) protokoll és az NSG-k, például:

    ![](media/traffic-analytics/21.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Portok és az internetről érkező forgalmat fogadó virtuális gépek megtekintése

**keresni**

- Megnyitott portok vannak beszélgetést az interneten keresztül?
    - Ha nem várt portok nyitva találhatók, javíthatja ki a konfigurációt:

        ![](media/traffic-analytics/22.png)

        ![](media/traffic-analytics/23.png)

**keresni**

Rendelkezik rosszindulatú forgalmat a környezetében? Ha az azt származó? Ha az azt irányuló?

![](media/traffic-analytics/24.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Az NSG-szabály találatok trendeket megjelenítése

**keresni**

- Mely NSG/szabály rendelkezik a legtöbb találatok?
- Mik a legfontosabb forrás és cél beszélgetés párok ben szereplő NSG?

    ![](media/traffic-analytics/25.png)

- Az alábbi képek idő megjelenítése a találatok NSG-szabályok és a forrás-cél részletes adatai a hálózati biztonsági csoport trendek:

    ![](media/traffic-analytics/26.png)

    ![](media/traffic-analytics/27.png)