---
title: Magas rendelkezésre állás az adatkezelési átjáróval az Azure Data Factoryban |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan horizontális felskálázása adatkezelési átjáró további csomópontokat, és a méretezési csoport hozzáadásával, amely képes futni a csomóponton egyidejűleg futó feladatainak számát növelésével beállítása.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 08e7341bfd1c384e41e6d3f1bd7810552899849a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092191"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Az adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség (előzetes verzió)
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [saját üzemeltetésű integrációs modul az](../create-self-hosted-integration-runtime.md). 


Ez a cikk segítséget nyújt az adatkezelési átjárót magas rendelkezésre állás és méretezhetőség megoldás konfigurálása / integrációja.    

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már ismeri az integrációs modul (korábbi adatkezelési átjáró) alapjait. Ha nem, tekintse meg [adatkezelési átjáró](data-factory-data-management-gateway.md).
> 
> **Az előzetes verziójú funkció az adatkezelési átjáró verziója 2.12.xxxx.x és újabb hivatalosan támogatott**. Ellenőrizze, hogy a verzió 2.12.xxxx.x használ vagy újabb. Az adatkezelési átjáró legújabb verziójának letöltése [Itt](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Áttekintés
Adatok felügyeleti átjárók több helyszíni gépek a portálról egyetlen logikai átjáró telepített társíthat. Ezek a gépek nevezzük **csomópontok**. Akár rendelkezhet **négy csomópont** társított logikai átjáró. Több csomópont (a telepített átjáró a helyszíni gépek) rendelkező logikai átjáró előnyei a következők:  

- Adatok áthelyezése a helyszíni és a felhő között teljesítményének növelése adattárakban.  
- Ha valamilyen okból leáll az egyik csomópontot, más csomópontok találhatók továbbra is az adatok áthelyezése. 
- Ha az egyik csomópontot kell karbantartás céljából offline állapotba kell helyezni, más csomópontok találhatók továbbra is az adatok áthelyezése.

Beállíthatja a számát **egyidejű adatok mozgását feladatok** , amely képes futni a csomóponton, vertikális felskálázása az adatok áthelyezését a helyszíni és a felhő között a funkció adattárakban. 

Az Azure portal használatával, ezeken a csomópontokon, amely segít eldönteni, hogy adja hozzá, vagy eltávolít egy csomópontot a logikai átjáró állapotának figyelése. 

## <a name="architecture"></a>Architektúra 
A következő ábra a méretezhetőség architektúrájának áttekintése és a rendelkezésre állási funkció az adatkezelési átjárót a nyújtja: 

![Az adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logikai átjáró** az átjáró hozzá egy data factoryt az Azure Portalon. Korábban csak egy helyszíni Windows-gépen sikerült társíthat logikai átjáró telepített adatkezelési átjáró. Ez a helyszíni átjárót tartalmazó számítógépen csomópontoknak nevezzük. Most már hozzárendelheti akár **négy fizikai csomópont** logikai átjáróval. Több csomóponttal rendelkező logikai átjáró nevezzük egy **több csomópontos átjárót**.  

Ezek a csomópontok **aktív**. Adatok mozgását feladatok adatok áthelyezése a helyszíni és a felhő között, az összes tud feldolgozni adattárakban. Az egyik csomópontot dispatcher és a feldolgozó is jár el. Más csomópontok a csoportok a munkavégző csomópontok. A **dispatcher** csomópont lekéri az adatokat adattovábbítási tevékenységek/feladatok felhőalapú szolgáltatásából, és továbbítja őket a munkavégző csomópontok (beleértve a saját maga). A **feldolgozó** csomópont végrehajtja az adatok mozgását feladatok adatok áthelyezése a helyszíni és a felhő között adattárakban. Minden csomópont feldolgozók. Csak egy csomópont lehet küldő és a feldolgozó.    

Előfordulhat, hogy általában megkezdi egy csomóponttal és **horizontális felskálázása** adhat hozzá további csomópontokat, a meglévő csomópontokon vannak túlterhelte és az adatok mozgását terhelés mellett. Emellett **vertikális felskálázás** növelje futni a csomóponton engedélyezett egyidejű feladatok számát egy átjárócsomópont adatok mozgását képességét. Ez a funkció egy egycsomópontos átjárón (akkor is, ha a skálázhatóság és rendelkezésre állás funkció nincs engedélyezve) is érhető el. 

Több csomóponttal rendelkező átjáró biztosítja, hogy a tároló hitelesítő adatainak szinkronizálása csomópontokon. A csomópontok közötti kapcsolati probléma van, ha a hitelesítő adatok szinkronizálva lehet. Amikor beállít egy átjárót használó helyszíni adatokat tároló hitelesítő adatait, hitelesítő adatok a kezelő/munkavégző csomópont, menti. A diszpécser csomópont szinkronizál más feldolgozó csomópontokat. Ez a folyamat más néven **hitelesítő adatok szinkronizálása**. Csomópontok közötti kommunikációs csatorna lehet **titkosított** egy nyilvános SSL/TLS-tanúsítvány. 

## <a name="set-up-a-multi-node-gateway"></a>A több csomópontos átjáró beállítása
Ez a szakasz azt feltételezi, hogy elvégezte a következő két cikkeket vagy ezekben a cikkekben fogalmakat ismerős: 

- [Az adatkezelési átjáró](data-factory-data-management-gateway.md) – az átjáró részletes áttekintést nyújt.
- [Adatok áthelyezése a helyszíni és a felhő között adattárak](data-factory-move-data-between-onprem-and-cloud.md) – egy részletes útmutató egy átjáró használatával egy egyetlen csomóponttal rendelkező útmutató tartalmazza.  

> [!NOTE]
> Mielőtt telepít egy adatkezelési átjárón egy helyszíni Windows-gépen, tekintse meg a felsorolt Előfeltételek [a fő cikk](data-factory-data-management-gateway.md#prerequisites).

1. Az a [forgatókönyv](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), logikai átjáró hozható létre, amikor engedélyezi a **magas rendelkezésre állás és méretezhetőség** funkció. 

    ![Az adatkezelési átjáró - engedélyezése magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Az a **konfigurálása** lapon, vagy használjon **Expressz telepítés** vagy **manuális telepítés** telepítsen egy átjárót az első csomópontot (egy helyszíni Windows-számítógép) mutató hivatkozást.

    ![Az adatkezelési átjáró - kifejezett vagy manuális telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Az Expressz telepítés lehetőséget használja, ha a csomópontok közötti kommunikáció a titkosítás nélkül történik. A csomópont neve megegyezik a gép nevét. Ha titkosítani kell a csomópontok közötti kommunikáció, vagy adjon meg egy tetszőleges csomópont nevet szeretne, használja a manuális telepítést. Csomópont neve később nem szerkeszthető.
3. Ha úgy dönt, **Expressz telepítés**
    1. A következő üzenet jelenik meg az átjáró sikeres telepítése után:

        ![Az adatkezelési átjáró – sikeres Gyorstelepítés](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Adatok kezelése a Configuration Manager indítása az átjáró a következő [ezek az utasítások](data-factory-data-management-gateway.md#configuration-manager). Láthatja, hogy az átjáró neve, csomópont nevét, állapotát, stb.

        ![Az adatkezelési átjáró – sikeres telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Ha úgy dönt, **manuális telepítés**:
    1. A Microsoft Download Centert, telepítse az átjárót a gépen futtassa a telepítési csomag letöltése.
    2. Használja a **hitelesítési kulcs** származó a **konfigurálása** lap regisztrálja az átjárót.
    
        ![Az adatkezelési átjáró – sikeres telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Az a **új átjárócsomópont** lapon megadhat egy egyéni **neve** annak az átjárócsomópontnak. Alapértelmezés szerint ugyanaz, mint a számítógép neve a csomópont neve.    

        ![Az adatkezelési átjáró - nevének megadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. A következő lapon választhatja ki, hogy **csomópontok közötti kommunikáció titkosítás engedélyezése**. Kattintson a **kihagyása** letiltásához (alapértelmezett) titkosítást.

        ![Az adatkezelési átjáró - titkosítás engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Titkosítási mód módosítása csak támogatott, ha egyetlen átjáró csomópont található a logikai átjáró. Az átjáró több csomóponttal rendelkezik a titkosítási mód módosításához tegye a következőket: egy csomópont kivételével minden csomóponton törölni, módosíthatja a titkosítási módot, és ezután vegye fel újra a csomópontokat.
        > 
        > Lásd: [TLS/SSL-tanúsítvány követelményei](#tlsssl-certificate-requirements) TLS/SSL-tanúsítvány használatára vonatkozó követelmények szakaszában listáját. 
    5. Az átjáró sikeres telepítése után kattintson a Configuration Manager indítása:
    
        ![Manuális telepítés - launch configuration Managerben](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. a kapcsolat állapotát jeleníti meg, amely csomóponton (a helyszíni Windows gép), láthatja a Data Management Gateway Configuration Manager **átjárónév**, és **csomópontnév**.  

        ![Az adatkezelési átjáró – sikeres telepítés](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Ha az átjáró-beli virtuális gépen szeretne kiépíteni, [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ez a szkript létrehoz egy logikai átjárót, hoz létre virtuális gépeket telepített adatkezelési átjáró szoftverekkel rendelkező, és regisztrálja őket a logikai átjáró. 
6. Az Azure Portalon, indítsa el a **átjáró** oldalon: 
    1. A data factory kezdőlapján a portálon kattintson **társított szolgáltatások**.
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Válassza ki a **átjáró** megtekintéséhez a **átjáró** oldalon:
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Megjelenik a **átjáró** oldalon:   

        ![Egyetlen csomópont nézet átjáró](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kattintson a **csomópont hozzáadása** egy csomópont hozzáadása a logikai átjáró az eszköztáron. Expressz telepítés használni kíván, ha a helyszíni gépen, amely az átjáró-csomópontként hozzáadódik ehhez a lépéshez. 

    ![Az adatkezelési átjáró - csomópontmenü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Az első fürtcsomópont beállítása lépések hasonlóak. A Configuration Manager felhasználói felületén állítsa be a csomópont nevét, ha úgy dönt, hogy a manuális telepítési lehetőség teszi lehetővé: 

    ![Configuration Manager - telepítés második átjáró](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Miután az átjáró sikeres telepítését a csomóponton, a Configuration Manager eszközt a következő képernyő jelenik meg:  

    ![Configuration Manager - telepítés második átjáró sikeres](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Ha megnyitja a **átjáró** lapot a portálon, láthatja az átjárócsomópontok két most: 

    ![Átjáró két csomópont a portálon](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Egy átjáró csomópont törléséhez kattintson **törlése csomópont** az eszköztáron válassza ki a törölni, és kattintson a kívánt **törlése** az eszköztáron. Ez a művelet törli a kijelölt csomópont a csoportból. Vegye figyelembe, hogy ez a művelet nem távolítja el az átjáró felügyeleti szoftverek a csomópontból (a helyi Windows-gépen). Használat **programok hozzáadását vagy eltávolítását** a Vezérlőpulton a helyszíni az átjáró eltávolítása. Átjáró eltávolításakor a csomópont automatikusan törlődik a portálon.   

## <a name="upgrade-an-existing-gateway"></a>Meglévő átjáró frissítése
Frissíthet egy meglévő átjáró magas rendelkezésre állás és méretezhetőség funkcióját. Ez a funkció csak az adatkezelési átjárót verzió rendelkező csomópontok működik > = 2.12.xxxx. Láthatja, hogy a gépen telepített adatkezelési átjáró verziója a **súgó** fülre a Data Management Gateway Configuration Manager. 

1. Az átjáró a helyszíni gépen a legújabb verzióra frissítéséhez hajtsa végre a következő letöltésével és a telepítőcsomag egy MSI-csomag futtatása a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Lásd: [telepítési](data-factory-data-management-gateway.md#installation) című szakasz részletezi.  
2. Keresse meg az Azure Portalon. Indítsa el a **adat-előállító lap** a data Factory. Indítsa el a társított szolgáltatások csempén kattintson a **társított szolgáltatások lap**. Válassza ki az átjáró indítása a **átjáró lapján**. Kattintson a gombra, és engedélyezze **előzetes verziójú funkció** az alábbi képen látható módon: 

    ![Az adatkezelési átjáró – előzetes verziójú funkció engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. A portálon az előzetes verziójú funkció engedélyezését követően zárja be az összes. Nyissa meg újra a **átjáró lapján** az új előzetes felhasználói felületének (UI) megtekintéséhez.
 
    ![Az adatkezelési átjáró - enable előzetes funkció sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Az adatkezelési átjáró – felhasználói felület előzetes verzió](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > A frissítés során az első csomópontjának neve, a gép nevét. 
3. Ezután adjon hozzá egy csomópontot. Az a **átjáró** kattintson **csomópont hozzáadása**.  

    ![Az adatkezelési átjáró - csomópontmenü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Kövesse az utasításokat az előző szakaszban állíthatja be a csomópontot. 

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

- A gazdagépen az átjáró energiasémát beállítani, hogy a gép hibernálásra nem. A gazdagép frissítéséből szeretnénk, ha az átjáró nem fog válaszolni kérelmek.
- Készítsen biztonsági másolatot az átjáró társított tanúsítvány.
- Győződjön meg arról az összes csomópont hasonló konfiguráció az ideális teljesítmény a (javasolt). 
- Adja hozzá a magas rendelkezésre állás biztosítása érdekében legalább két csomóponttal.  

### <a name="tlsssl-certificate-requirements"></a>A TLS/SSL-tanúsítvány követelményei
Az alábbiakban az integrációsmodul-csomópontot integrációs közötti kommunikáció tehető biztonságossá használt TLS/SSL-tanúsítvány követelményei:

- A tanúsítványnak kell lennie egy nyilvánosan megbízható X509 v3 tanúsítvány. Azt javasoljuk, hogy a nyilvános (külső) hitelesítésszolgáltató (CA) által kiállított tanúsítványokat használ.
- Minden egyes az integration runtime csomópontja meg kell bízniuk ezt a tanúsítványt, valamint az ügyfélszámítógépen, amelyen fut a hitelesítőadat-kezelő alkalmazást. 
  > [!NOTE]
  > Hitelesítőadat-kezelő alkalmazást használja a másolás varázsló hitelesítő adatok biztonságos beállításakor / Azure-portálon. És ez lehet a helyszíni ugyanazon a hálózaton belül bármely gépről aktivált / titkos adattár.
- A rendszer támogatja a helyettesítő tanúsítványokat. Ha a tartománynév **node1.domain.contoso.com**, használhatja ***. domain.contoso.com** a tanúsítvány tulajdonos neve.
- A SAN-tanúsítványok használata nem ajánlott, mert a tulajdonos alternatív neveket csak az utolsó elem fogja használni, és minden más figyelmen kívül jelenlegi korlátozás miatt. Például van egy SAN-tanúsítvány, amelynek SAN vannak **node1.domain.contoso.com** és **node2.domain.contoso.com**, ezzel a tanúsítvánnyal csak használhatja a gépet, amelynek FQDN-je **node2.domain.contoso.com**.
- Támogatja az SSL-tanúsítványokra vonatkozó Windows Server 2012 R2 által támogatott bármely kulcsának mérete.
- Tanúsítvány használata a CNG kulcsok nem támogatottak.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: Ha szeretné nem engedélyezhető a titkosítás?
Titkosítás engedélyezése adhat hozzá, bizonyos ezért az infrastruktúra (tulajdonos nyilvános tanúsítvány) költség, kihagyhatja a titkosításának engedélyezésével az alábbi esetekben:
- Ha az integrációs modul megbízható hálózathoz, vagy a hálózaton, például IP küszöbértéküket átlátható titkosítási fut. Mivel ez a csatorna kommunikáció csak korlátozott megbízható hálózaton belül, előfordulhat, hogy nem kell további titkosítási.
- Ha az integrációs modul nem fut éles környezetben. Ez segít a TLS/SSL-tanúsítvány költségek csökkentésére.


## <a name="monitor-a-multi-node-gateway"></a>Több csomópontos átjárót figyelése
### <a name="multi-node-gateway-monitoring"></a>Több csomópontos átjárót figyelése
Az Azure Portalon megtekintheti az átjárócsomópontok állapotainak mellett minden egyes csomóponton közel valós idejű pillanatképét erőforrás-használat (CPU, memória, network(in/out), stb.). 

![Az adatkezelési átjáró – több csomópont figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Engedélyezheti a **speciális beállítások** a a **átjáró** oldalon tekintheti meg az olyan speciális metrikákkal **hálózati**(be/ki), **szerepkör & hitelesítő adatok állapota**, hasznos lehet a hibaelhárítás során átjáró, amely és **egyidejű feladatok** (futó / Limit) amely is lehet módosított / során ennek megfelelően módosított teljesítményhangolás. Az alábbi táblázat ismerteti az oszlopok a **Átjárócsomópontok** lista:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Name (Név) | A logikai átjáró és a csomópontok kívánt átjáróval társított neve.  
status | A logikai átjáró és az átjáró csomópontok állapotát. Példa: Online/Offline/korlátozott/stb. A fenti állapotok megjelenése kapcsolatos információkért lásd: [átjáró állapota](#gateway-status) szakaszban. 
Verzió | A logikai átjáró, és minden egyes átjárócsomópont verzióját mutatja. A logikai átjáró verziója határozza meg a csoport csomópontjának többsége verzióján alapul. Ha nincs a logikai átjáró beállításai, és verzió száma azonos a logikai átjáró függvény csak a csomópontok különböző verziójú csomópontok megfelelően. Mások a korlátozott módban van, és manuálisan kell frissíteni, (csak abban az esetben az automatikus frissítés nem működik). 
Elérhető memória | Rendelkezésre álló memória egy átjáró-csomóponton. Ez az érték közel valós idejű pillanatképet. 
Processzorkihasználtság | CPU-kihasználtság egy átjáró-csomópont. Ez az érték közel valós idejű pillanatképet. 
Hálózatkezelés (In/Out) | A hálózathasználat egy átjáró-csomópont. Ez az érték közel valós idejű pillanatképet. 
Egyidejű feladatok (futó / Limit) | Feladatok és minden egyes csomóponton futó feladatok száma. Ez az érték közel valós idejű pillanatképet. Korlát azt jelzi, hogy az egyidejű feladatok maximális száma minden egyes csomópont esetében. Ez az érték van megadva a mérete alapján. A vertikális felskálázása a speciális alkalmazási egyidejű feladat-végrehajtási korlát növeléséhez, CPU / memória / hálózati kevésbé használt, de a tevékenység időkorlátja. Ez a funkció egy egycsomópontos átjárón (akkor is, ha a skálázhatóság és rendelkezésre állás funkció nincs engedélyezve) is érhető el. További információkért lásd: [szempontok méretezése](#scale-considerations) szakaszban. 
Szerepkör | –-Dispatcher és feldolgozói szerepkörök két típusa van. Minden csomópont a dolgozók, ami azt jelenti, hogy az összes felhasználásuk feladatok végrehajtásához. Nincs dispatcher csak egy csomópont, amely lekéréses feladatok/feladatok a cloud servicesből, és melyik másik munkavégző csomópontok (beleértve a saját maga) használható. 

![Az adatkezelési átjáró - speciális figyelés több csomópont](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Átjáró állapota

Az alábbi táblázat ismerteti a lehetséges állapotok egy **átjárócsomópont**: 

status  | Megjegyzések és forgatókönyvek
:------- | :------------------
Online | Csomópont csatlakoztatva a Data Factory szolgáltatásban.
Offline | Csomópont offline állapotban.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | Kapcsolat nem látható probléma miatt. HTTP-port 8050 probléma, a service bus kapcsolódási probléma vagy a hitelesítő adatok szinkronizálási problémája miatt lehet. 
Inaktív | Csomópontnak számít eltér a többi legtöbb csomópont konfigurációjának konfigurációban.<br/><br/> Egy csomópont inaktív lehet, ha a többi csomópont nem tud kapcsolódni. 


Az alábbi táblázat ismerteti a lehetséges állapotok egy **logikai átjáró**. Az átjáró állapotának ellenőrzéséhez az átjárócsomópontok állapotainak függ. 

status | Megjegyzések
:----- | :-------
Regisztrálni kell | Nem csomópont még regisztrálva van a logikai átjáró
Online | Az Átjárócsomópontok online állapotban.
Offline | Nincsenek csomópont online állapotú.
Korlátozott | Ezt az átjárót nem minden csomópontja kifogástalan állapotban vannak. Ez az állapot nem figyelmezteti, hogy egyes csomópontok esetleg nem működik! <br/><br/>Hitelesítő adatok szinkronizálási problémát a kezelő/munkavégző csomópont okozhatja. 

### <a name="pipeline-activities-monitoring"></a>Folyamat / tevékenységek figyelése
Az Azure portal egy figyelési lehetőség, a részletes csomópont szintű adatok folyamatot biztosít. Például ez jeleníti meg, mely tevékenységek melyik csomópont futtatunk. Ezt az információt is hasznos információkat egy adott csomópont teljesítményére, mondja ki a hálózati sávszélesség-szabályozás miatt. 

![Az adatkezelési átjáró - folyamatok figyelése több csomópont](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Az adatkezelési átjáró - folyamat részletei](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Méretezési szempontok

### <a name="scale-out"></a>Horizontális felskálázás
Ha a **elérhető memória pedig kevés** és a **CPU-használata túl magas**, új csomópont hozzáadása segíti a horizontális felskálázást a terhelés gépek között. Ha a tevékenység időtúllépési vagy átjáró csomópont offline állapotú miatt nem működik, segít a Ha csomópontot ad hozzá az átjárót.
 
### <a name="scale-up"></a>Vertikális felskálázás
A rendelkezésre álló memória és a Processzor nem használhatók jól, de a kihasználatlan kapacitásért értéke 0, érdemes a vertikális felskálázáshoz, amely képes futni a csomóponton egyidejű feladatok számának növelése. Érdemes azt is, vertikális felskálázás akár, amikor a tevékenységek időtúllépésekbe ütközzenek, mert az átjáró túl van terhelve. Ahogy az az alábbi képen is látható, a maximális kapacitás egy csomópont lehet növelni. Javasoljuk, hogy kétszeresére, a következővel kell kezdődnie.  

![Az adatkezelési átjáró – méretezési szempontok](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Ismert problémák/használhatatlanná tévő változásai

- Jelenleg legfeljebb négy fizikai átjárócsomópontok egyetlen logikai átjáróval rendelkezhet. Ha négynél több csomópont van szüksége a teljesítmény szempontjából, küldjön egy e-mailek [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Az aktuális logikai átjáró váltani egy másik logikai átjáró hitelesítési kulccsal újból egy átjárócsomópont nem regisztrálható. Az újbóli regisztrációt, távolítsa el az átjárót a csomópontról, telepítse újra az átjárót és más logikai átjáró hitelesítési kulccsal regisztrálja. 
- Ha HTTP-proxy szükség az átjáró összes csomóponthoz, állítsa be a proxy diahost.exe.config és diawp.exe.config, és győződjön meg, hogy minden csomópont ugyanolyan diahost.exe.config és diawip.exe.config a Kiszolgálókezelő használatával. Lásd: [konfigurálja a proxybeállításokat](data-factory-data-management-gateway.md#configure-proxy-server-settings) című szakasz részletezi. 
- Csomópontok közötti kommunikáció a Gateway Configuration Manager titkosítási mód megváltoztatásához törölje a portálon egy kivételével az összes csomópont. Vissza a titkosítási mód megváltoztatása után adja hozzá a csomópontot.
- Egy hivatalos SSL-tanúsítvány használatára, ha úgy dönt, hogy a csomópontok közötti kommunikációs csatorna titkosítása. Önaláírt tanúsítvány kapcsolódási problémákat okozhat, előfordulhat, hogy ugyanazt a tanúsítványt nem megbízható más gépeken hitelesítő hatóság listájában. 
- Egy logikai átjáró átjáró csomópont nem lehet regisztrálni, ha a csomópont verziója alacsonyabb, mint a logikai átjáró verziója. Törölje a logikai átjáró összes csomópontján Portalról, hogy egy alacsonyabb verzióra node(downgrade) regisztrálhatja azt. Ha töröl egy logikai átjáró összes csomópontján, manuálisan telepíti, és regisztrálja a logikai átjáróhoz tartozó új csomópontokat. Expressz telepítés nem támogatott ebben az esetben.
- Expressz telepítés nem használhat egy meglévő logikai átjáró, amely továbbra is felhőbeli hitelesítő adatokat használ a csomópontok telepítésével. Ellenőrizze a hitelesítő adatok tárolására a Gateway Configuration Managerből a beállítások lapon.
- Expressz telepítés nem használható a meglévő logikai átjáró, amely engedélyezve van a csomópontok közötti titkosítás-csomópontok telepítésével. A titkosítási mód beállítása magában foglalja a tanúsítványok manuális hozzáadásával, mert az Expressz telepítés nincs további lehetőség. 
- Egy fájl másolásához a helyszíni környezetből, ne használjon \\localhost vagy C:\files többé a localhost vagy a helyi meghajtón óta nem érhető el minden csomóponton keresztül. Ehelyett használjon \\ServerName\files fájlok azon hely meghatározásához.


## <a name="rolling-back-from-the-preview"></a>Az előzetes verzióra való visszaállítása 
Szeretné visszaállítani az előzetes verzióról, törölje az összes csomóponton, de egy csomópontot. Nem számít, hogy melyik csomópontokon törölni, de ellenőrizze, hogy legalább egy csomópont a logikai átjárón. Csomópont eltávolítása az átjárót a gépen, vagy az Azure portal használatával törölheti. Az Azure Portalon az a **adat-előállító** társított szolgáltatás elindításához kattintson a **társított szolgáltatásokat** lapot. Válassza ki az átjáró indítása a **átjáró** lapot. Az átjáró oldalán láthatja a csomópontok kívánt átjáróval társított. A lap lehetővé teszi az átjáró csomópont törlése.
 
Miután törölt, kattintson a **előzetes verziójú funkciók** a azonos oldala az Azure portal és a tiltsa le az előzetes verziójú funkció. Egy csomópontos általánosan elérhető (nyilvánosan elérhetők) átjárót az átjáró alaphelyzetbe állítása.


## <a name="next-steps"></a>További lépések
Tekintse át a következő cikkeket:
- [Az adatkezelési átjáró](data-factory-data-management-gateway.md) – az átjáró részletes áttekintést nyújt.
- [Adatok áthelyezése a helyszíni és a felhő között adattárak](data-factory-move-data-between-onprem-and-cloud.md) – egy részletes útmutató egy átjáró használatával egy egyetlen csomóponttal rendelkező útmutató tartalmazza. 
