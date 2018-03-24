---
title: Az Azure Traffic Analytics kapcsolatos gyakori kérdések |} Microsoft Docs
description: Egyes forgalom Analytics leggyakrabban feltett kérdésekre adott válaszok.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: b9090f91db11b5bde53f3652028030201f135fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Gyakori kérdések a forgalom elemzés

1.  Mik azok a forgalom Analytics használatához szükséges előfeltételek?

    Forgalom elemzés szükséges a következő előfeltételeknek:

    - Engedélyezett hálózati figyelőt előfizetés
    - NSG folyamata naplók a figyelni kívánt NSG-k engedélyezve
    - Egy Azure Storage-fiók tárolására nyers flog naplók
    - Az olvasási és írási hozzáférés egy Naplóelemzés (OMS) munkaterületen
    - Ön fiókjához társítva kell lennie a Microsoft.Network szolgáltató a következő műveleteket:

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  Mely Azure-régiók találhatók forgalom Analytics?

    Az előzetes kiadásban használhat forgalom elemzés a következő az NSG-k **támogató régiók**: nyugati középső Régiójában, USA keleti régiója, USA keleti régiója 2. régiója, északi középső Régiójában, déli középső Régiójában, USA középső RÉGIÓJA, USA nyugati régiója, USA nyugati régiója-2, Nyugat-Európában, Észak-Európa , Nyugati UK, Dél-UK, Kelet-Ausztrália és Ausztrália délkeleti. A Naplóelemzési munkaterület léteznie kell a nyugati középső Régiójában, USA keleti régiója, Nyugat-Európában, Ausztrália délkeleti, vagy a déli UK régió.

3.  Az NSG-ket engedélyezhető a folyamatot is naplókat lehet, mint az OMS-munkaterület különböző régiókban?

    Igen

4.  Egyetlen munkaterület konfigurálható több NSG-ket?

    Igen

5.  Használhatja a meglévő OMS-munkaterület?

    Igen, ha egy meglévő munkaterületen válassza ki, győződjön meg arról, ezért az áttelepítés új lekérdezés nyelvének. Ha nem szeretné frissíteni a munkaterület; Hozzon létre egy újat kell. Az új lekérdezési nyelv kapcsolatos további információkért lásd: [Azure Naplóelemzés frissítsen az új naplófájl-keresési](../log-analytics/log-analytics-log-search-upgrade.md).

6.  Azure Storage-fiókom kell egy előfizetés, és az OMS-munkaterület egy másik előfizetésben kell?

    Igen

7.  Tárolhatok nyers naplók a másik előfizetésben található másik tárolási fiókot?

    Nem. Tárolhatja a nyers naplók bármely tárfiókban, amelyben engedélyezve van egy NSG folyamata naplókhoz, azonban mind a tárfiók és a nyers naplók kell lennie a azonos előfizetésbe és azonos térségbe.

8.  Ha egy NSG konfigurálása a forgalom elemzés közben kapok "Nem található" hibaüzenet, hogyan szeretnék kiderítését?

    Válasszon egy támogatott régióban kérdés 2 szerepel. Ha egy nem támogatott régióban, a "Nem található" hibaüzenetet kap.

9.  Az NSG-folyamat naplók azért kapom NSG állapota "Betöltése sikertelen", mint teendőkre?

    A Microsoft.Insights szolgáltató működéséhez a naplózás folyamata regisztrálnia kell. Ha nem biztos abban, hogy hogy regisztrálva van-e a Microsoft.Insights szolgáltató vagy nem az előfizetéséhez, cserélje le a *xxxxx-xxxxx-xxxxxx-xxxx* a következő parancsot, és futtassa a következő parancsokat a PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. A megoldás I konfigurált. Miért nem jelenik meg semmit az irányítópulton?

    Az irányítópult megjelenését, amikor első alkalommal eltarthat legfeljebb 30 perc. A megoldás először kell összesíteni elég adat ahhoz, hogy a jelentéssel bíró insights származnia ahhoz, azok a jelentések jönnek létre. 

11.  Ha a következő hibaüzenet: "nem található adatokról a munkaterületen kijelölt alatt az időtartam alatt. Próbálja meg módosítani a időtartam alatt, vagy válasszon másik munkaterület", hogyan hárítható el azt?

        Próbálja meg a következő beállításokat:
        - Próbálja meg módosítani a felső parancssávon alatt az időtartam alatt.
        - A felső sávon különböző Log Analytics-munkaterület kiválasztása
        - Nyissa meg forgalom Analytics 30 perc után Ha nemrég engedélyezve
    
        Ha a probléma továbbra is fennáll, a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Ha a következő hibaüzenet: "1.) a NSG elemzése flow naplók először. Ez a folyamat 20-30 percet is igénybe vehet. Ellenőrizze újra egy kis idő múlva. 2.) Ha a fenti lépést nem működik, és a munkaterületen a szabad SKU alatt, majd ellenőrizze a munkaterület használatát itt ellenőrzése túllépte a kvótát, ellenkező esetben tekintse meg a gyakran feltett kérdésekre további információk", hogyan hárítható el azt?

        Előfordulhat, hogy a hibaüzenet a következő okok miatt:
        - Forgalom Analytics előfordulhat, hogy nemrég engedélyezve van, és előfordulhat, hogy kevés az adat ahhoz, hogy a jelentéssel bíró insights származnia ahhoz, azok a jelentések is generálható kell összesítése. Ebben az esetben próbálja meg újra a 30 perc után
        - Az OMS-munkaterület a szabad SKU alatt áll, és a kvótakorlát megszegése azt. Ebben az esetben előfordulhat, hogy kell használnia a Termékváltozat munkaterületeinek nagyobb kapacitással rendelkező átjáróeszközt.
    
        Ha a probléma továbbra is fennáll, a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Ha a következő hibaüzenet: "tűnik tudunk az erőforrások adatait (topológia) és a tranzakciós adatokat. Eközben Ide kattintva tekintse meg az erőforrások adatait, majd – gyakori kérdések további információért tekintse át", hogyan oldja meg az?

        Ön az erőforrások információi az irányítópulton; azonban folyamat kapcsolatos jelenleg nincs jelen. Adatok nem találhatók az erőforrások között nincs kommunikáció adatfolyamok miatt. 60 perc várakozás és újbóli állapotának ellenőrzéséhez. Ha biztos benne, hogy létezik-e erőforrások közötti kommunikációs forgalom, majd a aggodalomra a [felhasználói hang fórum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14.  Hogyan áron a forgalom Analytics?

        Forgalom Analytics forgalmi díjas csökkentett naplók növelésére és a továbbfejlesztett naplók tárolása a Naplóelemzési munkaterület. A képen forgalom Analytics nem számlázása történik a csökkentett naplók lehetőségeinek azonban munkaterület az adatok tárolási közzétett ütemben számlázási vonatkoznak. Ez a válasz frissíti az forgalom Analytics díjszabása elérhetővé válik.

15.  Hogyan navigálhatnak meg, hogy a földrajzi nézet a billentyűzet használata-e?

        A földrajzi-lap két fő részeket tartalmazza:
    
        - **Transzparens**: az a földrajzi térképen tetején helyezett szalagcím lehetővé teszi a terjesztési forgalomszűrők például Deployment/No telepítési/Active/Inactive/forgalom Analytics engedélyezve/forgalom Analytics nem engedélyezett/forgalom gombokkal kiválasztása ország/Benign/esetleg kártékony/engedélyezett forgalmat, és jelmagyarázat információkat. A megadott gombok kiválasztását a megfelelő szűrő a térképen, például ha a felhasználó megadja a fejléc alatt a "Aktív" szűrő gombra, majd a térkép kiemeli az "Active" adatközpontokban a központi telepítésben.
        - **Térkép**: a fejléc alatt a térkép a szakasz bemutatja Azure adatközpontjaiban és országok forgalom elosztását.
    
        **Billentyűparancsokkal szalagcím**
    
        - Alapértelmezés szerint az a földrajzi-társítási oldalán a szalagcím érték a szűrő "Azure tartományvezérlők" gombra.
        - Lehetőségre, és navigáljon a másik szűrők gomb, használhatja a `Tab` vagy `Right arrow` kulcsot következő áthelyezése. Keresse meg a visszafelé, használja vagy `Shift+Tab` vagy a `Left arrow` kulcs. Navigációs irány sorrendjének balról jobbra, az alsó felső követi.
        - Nyomja meg a `Enter` vagy `Down` nyílbillentyűt kijelölt szűrőt alkalmazza. Szűréses választás és a központi telepítés alapján, egy vagy több csomópont térkép szakaszban vannak kiemelve.
            - Váltás **szalagcím** és **térkép**, nyomja le az ENTER `Ctrl+F6`.
        
        **Billentyűparancsokkal a térképen**
    
        - Miután bármely szűrőt a szalagcím a választott és megnyomva `Ctrl+F6`, fókusz egyik kijelölt csomópont (**Azure datacenter** vagy **ország vagy régió**) a leképezési nézetben.
        - Navigáljon a térkép többi kijelölt csomópont használhatja a `Tab` vagy `Right arrow` a kulcsok az előre haladást, és `Shift+Tab` vagy a `Left arrow` visszafelé adatátviteli kulcsát.
        - A térkép összes kijelölt csomópont kiválasztásához használja a `Enter` vagy `Down arrow` kulcs.
        - Az ilyen csomópontokon kiválasztását, a fókusz a **információk eszköz mezőben** a csomópont. Alapértelmezés szerint fókusz mozgatása a lezárt gombra a **információk eszköz mezőben**. További navigálni **mezőben** megtekintéséhez használja `Right` és `Left arrow` -kulcsok előre és hátra, illetve helyezze át. Nyomja le `Enter` rendelkezik a célzott parancsgomb ugyanaz, mintha a **információk eszköz mezőben**.
        - Nyomja le `Tab` közben a fókusz a van a **információ eszköz írja be**, a fókusz a végpontok az azonos kontinensen, mint a kijelölt csomópont található. Használhatja a `Right` és `Left arrow` kulcsok ezeket a végpontokat navigálni.
        - Keresse meg más folyamat végpontok/földrészekre fürt, használja a `Tab` az előre és `Shift+Tab` az előző verziókkal való mozgás.
        - Miután koncentrál `Continent clusters`, használja a `Enter` vagy `Down` nyílbillentyűk jelölje ki a végpontok a kontinensre fürtben található. Navigálni a végpontok és a kontinensre fürt információ írja be a Bezárás gombra, vagy használja a `Right` vagy `Left arrow` előre és hátra mozgás, illetve kulcsát. A tetszőleges végpontot használhatja `Shift+L` váltson át a kapcsolat sor a kijelölt csomópont a végpontnak. Nyomja le `Shift+L` újra a kijelölt végponttanúsítvány nyit meg.
        
        Bármely szakaszában:
    
        - `ESC` a kibontott kijelölés bezárása.
        - A `UP Arrow` kulcs hajtja végre a művelet, mintha `ESC`. A `Down arrow` kulcs hajtja végre a művelet, mintha `Enter`.
        - Használjon `Shift+Plus` a nagyításhoz, és `Shift+Minus` kicsinyítéshez.
