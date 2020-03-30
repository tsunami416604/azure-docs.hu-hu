---
title: Magas rendelkezésre állás az Azure Data Factory adatkezelési átjárójával
description: Ez a cikk bemutatja, hogyan skálázhatja ki az adatkezelési átjárót további csomópontok hozzáadásával és a csomóponton futtatható egyidejű feladatok számának növelésével.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065196"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség (előzetes verzió)
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [önkiszolgáló integrációs futásidejű t.](../create-self-hosted-integration-runtime.md) 


Ez a cikk segít a magas rendelkezésre állású és méretezhetőségi megoldás konfigurálása az adatkezelési átjáró/ integráció segítségével.    

> [!NOTE]
> Ez a cikk feltételezi, hogy már ismeri az integrációs futásidejű (korábbi adatkezelési átjáró) alapjait. Ha nem, olvassa el az [Adatkezelési átjáró](data-factory-data-management-gateway.md).
> 
> **Ez az előzetes verzió funkció hivatalosan is támogatott az Adatkezelési átjáró 2.12.xxxx.x és újabb verziója.** Ellenőrizze, hogy a 2.12.xxxx.x vagy újabb verziót használja-e. Töltse le az Adatkezelési átjáró legújabb verzióját [itt.](https://www.microsoft.com/download/details.aspx?id=39717)

## <a name="overview"></a>Áttekintés
A több helyszíni gépen telepített adatkezelési átjárókat egyetlen logikai átjáróval társíthatja a portálról. Ezeket a gépeket **csomópontoknak nevezik.** Logikai átjáróhoz legfeljebb **négy csomópont** társítható. A logikai átjáróhoz több csomópont (helyszíni gépek) logikai átjáróesetén a következő előnyökkel jár:  

- A helyszíni és a felhőbeli adattárak közötti adatmozgatás teljesítményének javítása.  
- Ha valamilyen okból leáll valamelyik csomópont, más csomópontok továbbra is elérhetők az adatok áthelyezéséhez. 
- Ha az egyik csomópontot karbantartás miatt offline állapotba kell helyezni, más csomópontok továbbra is elérhetők az adatok áthelyezéséhez.

A csomóponton futtatható **egyidejű adatmozgatási feladatok** számának konfigurálása is konfigurálható a helyszíni és a felhőbeli adattárak közötti adatáthelyezési képesség ének méretezéséhez. 

Az Azure Portal használatával figyelheti ezeknek a csomópontoknak az állapotát, amely segít eldönteni, hogy hozzáadjon-e vagy eltávolítson-e egy csomópontot a logikai átjáróból. 

## <a name="architecture"></a>Architektúra 
Az alábbi ábra az adatkezelési átjáró méretezhetőségi és rendelkezésre állási funkciójának architektúráját ismerteti: 

![Adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logikai átjáró** az az átjáró, amelyet az Azure Portalon egy adatgyárhoz ad hozzá. Korábban csak egy helyszíni Windows-gépet társítható egy logikai átjáróval telepített Adatkezelési átjáróval. Ezt a helyszíni átjárógépet csomópontnak nevezzük. Most legfeljebb négy **fizikai csomópontot** társíthat egy logikai átjáróhoz. A több csomópontos logikai átjárót **többcsomópontos átjárónak**nevezzük.  

Ezek a csomópontok **mind**aktívak. Ezek mindegyike feldolgozhatja az adatok mozgatási feladatok at adatok között a helyszíni és a felhőbeli adattárak. Az egyik csomópont diszpécserként és dolgozóként is jár el. A csoportok más csomópontjai a dolgozói csomópontok. A **diszpécser** csomópont lekéri az adatmozgatási feladatokat/feladatokat a felhőszolgáltatásból, és elküldi azokat a munkavégző csomópontoknak (beleértve magát is). A **munkavégző** csomópont adatáthelyezési feladatokat hajt végre az adatok helyszíni és felhőbeli adattárak közötti áthelyezéséhez. Minden csomópont dolgozó. Csak egy csomópont lehet diszpécser és dolgozó is.    

Általában egy csomón elindulhat, és **horizontális felskálázással** további csomópontokat adhat hozzá, mivel a meglévő csomópontok túlterheltek az adatmozgás terhelésével. Az átjárócsomópont adatmozgatási képességét is **felskálázhatja** a csomóponton futtatható egyidejű feladatok számának növelésével. Ez a funkció egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség i és rendelkezésre állási szolgáltatás nincs engedélyezve). 

A több csomókat tartalmazó átjáró az adattár hitelesítő adatait szinkronizálja az összes csomópont között. Ha csomópont-csomópont kapcsolati probléma merül fel, előfordulhat, hogy a hitelesítő adatok nincsenek szinkronban. Amikor hitelesítő adatokat állít be egy átjárót használó helyszíni adattárhoz, a hitelesítő adatokat a diszpécser/feldolgozó csomóponton menti. A diszpécser csomópont szinkronizálja a többi munkavégző csomópontdal. Ezt a folyamatot **hitelesítő adatok szinkronizálásának nevezik.** A csomópontok közötti kommunikációs csatorna nyilvános SSL/TLS tanúsítvánnyal **titkosítható.** 

## <a name="set-up-a-multi-node-gateway"></a>Többcsomópontos átjáró beállítása
Ez a szakasz feltételezi, hogy a következő két cikken ment keresztül, vagy ismeri az alábbi cikkek ben található fogalmakat: 

- [Adatkezelési átjáró](data-factory-data-management-gateway.md) – részletes áttekintést nyújt az átjáróról.
- [Adatok áthelyezése a helyszíni és a felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) – részletes útmutatót tartalmaz az átjáró egyetlen csomópontos használatával kapcsolatos részletes útmutatóval.  

> [!NOTE]
> Mielőtt adatkezelési átjárót telepítene egy helyszíni Windows-számítógépen, olvassa el [a cikk](data-factory-data-management-gateway.md#prerequisites)előfeltételeit.

1. A [forgatókönyvben](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)logikai átjáró létrehozása közben engedélyezze a **Magas rendelkezésre állású & méretezhetőség** i. 

    ![Adatkezelési átjáró – magas rendelkezésre állás és méretezhetőség lehetővé tétele](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. A **Konfigurálás** lapon az **Express telepítő** vagy a **Kézi beállítás** hivatkozással telepítsen egy átjárót az első csomópontra (egy helyszíni Windows-gépen).

    ![Adatkezelési átjáró – expressz vagy manuális beállítás](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Ha az expressz beállítási lehetőséget használja, a csomópont-csomópont kommunikáció titkosítás nélkül történik. A csomópont neve megegyezik a gép nevével. Kézi beállítás, ha a csomópont-csomópont kommunikációt titkosítani kell, vagy meg szeretne adni egy választott csomópontnevet. A csomópontnevek később nem szerkeszthetők.
3. Ha expressz beállítás t **választja**
    1. Az átjáró sikeres telepítése után a következő üzenet jelenik meg:

        ![Adatkezelési átjáró - a beállítás immár a sikeres beállítási](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Indítsa el az adatkezelési konfigurációkezelőt az átjáróhoz az [alábbi utasításokat](data-factory-data-management-gateway.md#configuration-manager)követve. Megjelenik az átjáró neve, csomópontneve, állapota stb.

        ![Adatkezelési átjáró - a telepítés sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Ha a kézi beállítás lehetőséget **választja:**
    1. Töltse le a telepítőcsomagot a Microsoft letöltőközpontjából, és futtassa az átjáró telepítéséhez a számítógépre.
    2. Az átjáró regisztrálásához használja a **Konfigurálás** lap **hitelesítési kulcsát.**
    
        ![Adatkezelési átjáró - a telepítés sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Az **Új átjárócsomópont** lapon egyéni **nevet** adhat meg az átjárócsomópontnak. Alapértelmezés szerint a csomópont neve megegyezik a számítógép nevével.    

        ![Adatkezelési átjáró – adja meg a nevét](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. A következő lapon megadhatja, hogy **engedélyezze-e a titkosítást a csomópontok közötti kommunikációhoz.** A titkosítás letiltásához kattintson a **Kihagyás** gombra (alapértelmezett).

        ![Adatkezelési átjáró - titkosítás engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > A titkosítási mód módosítása csak akkor támogatott, ha egyetlen átjárócsomópont van a logikai átjáróban. Ha módosítani szeretné a titkosítási módot, ha egy átjáró több csomóponton is rendelkezik, tegye a következő lépéseket: törölje az összes csomópontot egy csomópont kivételével, módosítsa a titkosítási módot, majd adja hozzá újra a csomópontokat.
        > 
        > A [TLS/SSL-tanúsítvány](#tlsssl-certificate-requirements) használatára vonatkozó követelmények listáját lásd a TLS/SSL-tanúsítványok használatára vonatkozó követelményekről. 
    5. Az átjáró sikeres telepítése után kattintson a Konfigurációkezelő indítása gombra:
    
        ![Kézi beállítás - indítási konfigurációkezelő](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. az Adatkezelési átjáró konfigurációkezelője a csomóponton (helyszíni Windows-gépen) látható, amely a kapcsolat állapotát, **az átjáró nevét**és a csomópont nevét jeleníti **meg.**  

        ![Adatkezelési átjáró - a telepítés sikeres](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Ha az átjárót egy Azure virtuális gépen építi ki, használhatja [ezt az Azure Resource Manager-sablont.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway) Ez a parancsfájl logikai átjárót hoz létre, virtuális gépeket állít be a Data Management Gateway szoftverrel, és regisztrálja őket a logikai átjáróval. 
6. Az Azure Portalon indítsa el az **Átjáró** lapot: 
    1. A portál adatgyárának kezdőlapján kattintson a **Csatolt szolgáltatások**elemre.
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. válassza ki az **átjárót** az **Átjáró** lap megtekintéséhez:
    
        ![Data factory kezdőlap](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Megjelenik az **Átjáró** lap:   

        ![Átjáró egycsomópontos nézetben](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kattintson az eszköztár **Csomópont hozzáadása** gombjára, ha csomópontot szeretne hozzáadni a logikai átjáróhoz. Ha expressz telepítést kíván használni, ezt a lépést a helyszíni gépről, amely csomópontként kerül hozzáadásra az átjáróhoz. 

    ![Adatkezelési átjáró - csomópont menü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. A lépések hasonlóak az első csomópont beállításához. A Configuration Manager felhasználói felülete lehetővé teszi a csomópont nevének beállítását, ha a kézi telepítési lehetőséget választja: 

    ![Configuration Manager - második átjáró telepítése](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Az átjáró sikeres telepítése után a Configuration Manager eszköz a következő képernyőt jeleníti meg:  

    ![Configuration Manager - a második átjáró telepítése sikeres](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Ha megnyitja az **Átjáró** lapot a portálon, most két átjárócsomópont jelenik meg: 

    ![Átjáró két csomón van a portálon](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Átjárócsomópont törléséhez kattintson az eszköztár **Csomópont törlése** gombjára, jelölje ki a törölni kívánt csomópontot, majd kattintson az eszköztár **Törlés** gombjára. Ez a művelet törli a kijelölt csomópontot a csoportból. Vegye figyelembe, hogy ez a művelet nem távolítja el az adatkezelési átjáró szoftvert a csomópontról (helyszíni Windows-gép). Az átjáró eltávolításához használja a vezérlőpult on-premise **programok telepítése vagy eltávolítása** segédprogramját. Amikor eltávolítja az átjárót a csomópontról, az automatikusan törlődik a portálon.   

## <a name="upgrade-an-existing-gateway"></a>Meglévő átjáró frissítése
Egy meglévő átjáró frissítéséhez használhatja a magas rendelkezésre állás és méretezhetőség i. Ez a szolgáltatás csak olyan csomópontokkal működik, amelyek >= 2.12.xxxx verzió adatkezelési átjárójával rendelkeznek. A számítógépre telepített adatkezelési átjáró verzióját az Adatkezelési átjáró konfigurációkezelőjének **Súgó** lapján láthatja. 

1. Frissítse a helyszíni számítógép átjáróját a legújabb verzióra a [microsoftos letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717)származó MSI telepítőcsomag letöltésével és futtatásával. A részleteket lásd a [telepítési](data-factory-data-management-gateway.md#installation) szakaszban.  
2. Lépjen az Azure Portalra. Indítsa el a **Data Factory oldalt** az adatgyárhoz. Kattintson a Csatolt szolgáltatások csempe elemre a **csatolt szolgáltatások lap**elindításához. Válassza ki az átjárólap elindításához az **átjárót.** Kattintson az **Előnézeti funkció elemre,** és engedélyezze az alábbi képen látható módon: 

    ![Adatkezelési átjáró – előzetes verzió engedélyezése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Ha az előnézeti funkció engedélyezve van a portálon, zárja be az összes oldalt. Nyissa meg újra az **átjárólapot** az új előzetes felhasználói felület (UI) megtekintéséhez.
 
    ![Adatkezelési átjáró – az előzetes verziós funkciók sikerességének lehetővé tétele](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Adatkezelési átjáró – előzetes felhasználói felület](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > A frissítés során az első csomópont neve a gép neve. 
3. Most adjon hozzá egy csomópontot. Az **Átjáró** lapon kattintson a **Csomópont hozzáadása gombra.**  

    ![Adatkezelési átjáró - csomópont menü hozzáadása](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    A csomópont beállításához kövesse az előző szakasz utasításait. 

### <a name="installation-best-practices"></a>Gyakorlati tanácsok a telepítéshez

- Konfigurálja az energiasémát a gazdagépen az átjáróhoz, hogy a számítógép ne hibernáljon. Ha a gazdagép hibernált állapotban van, az átjáró nem válaszol az adatkérésekre.
- Biztonsági másolatot kell adni az átjáróhoz társított tanúsítványról.
- Győződjön meg arról, hogy minden csomópont hasonló konfigurációjú (ajánlott) az ideális teljesítmény érdekében. 
- Adjon hozzá legalább két csomópontot a magas rendelkezésre állás biztosítása érdekében.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL tanúsítványkövetelmények
Az integrációs futásidejű csomópontok közötti kommunikáció biztosításához használt TLS/SSL-tanúsítvány követelményei a következők:

- A tanúsítványnak nyilvánosan megbízható X509 v3 tanúsítványnak kell lennie. Azt javasoljuk, hogy olyan tanúsítványokat használjon, amelyeket nyilvános (harmadik féltől származó) hitelesítésszolgáltató (CA) állít ki.
- Minden egyes integrációs futtatócsomópontnak meg kell bíznia ebben a tanúsítványban, valamint a hitelesítő adatkezelő alkalmazást futtató ügyfélgépben. 
  > [!NOTE]
  > A Hitelesítő adatok kezelője alkalmazás a Másolás varázslóból/Azure Portalról származó hitelesítő adatok biztonságos beállítása közben használatos. És ez lehet kilőni bármely gépen ugyanazon a hálózaton belül, mint a helyszíni / privát adattár.
- A helyettesítő karakteres tanúsítványok támogatottak. Ha a teljes tartománynév **node1.domain.contoso.com,** a ***.domain.contoso.com** használható a tanúsítvány tulajdonosneveként.
- A SAN-tanúsítványok nem ajánlottak, mivel a tulajdonosi alternatív nevek nek csak az utolsó elemét használja a rendszer, és az összes többi tanúsítványt figyelmen kívül hagyja a jelenlegi korlátozás miatt. Például olyan SAN-tanúsítvánnyal rendelkezik, amelynek **SAN node1.domain.contoso.com** és **node2.domain.contoso.com**, ezt a tanúsítványt csak olyan gépen használhatja, amelynek teljes tartományneve **node2.domain.contoso.com**.
- A Windows Server 2012 R2 által TLS-tanúsítványokhoz támogatott bármely kulcsméretet támogatja.
- A CNG-kulcsokat használó tanúsítványok nem támogatottak.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>GYIK: Mikor nem engedélyezném ezt a titkosítást?
A titkosítás engedélyezése bizonyos költségeket adhat az infrastruktúrához (nyilvános tanúsítvány takarásában), ezért az alábbi esetekben kihagyhatja a titkosítás engedélyezését:
- Ha az integrációs futtatótime megbízható hálózaton vagy átlátszó titkosítással, például IP/SEC-titkosítással rendelkező hálózaton fut. Mivel ez a csatornakommunikáció csak korlátozott a megbízható hálózaton belül, előfordulhat, hogy nincs szükség további titkosításra.
- Ha az integrációs futtatókörnyezet nem éles környezetben fut. Ez segíthet a TLS/SSL tanúsítvány költségének csökkentésében.


## <a name="monitor-a-multi-node-gateway"></a>Többcsomópontos átjáró figyelése
### <a name="multi-node-gateway-monitoring"></a>Többcsomópontos átjáró figyelése
Az Azure Portalon megtekintheti az erőforrás-kihasználtság (CPU, memória, hálózat(be/ki) közel valós idejű pillanatképét az átjárócsomópontok állapotaival együtt. 

![Adatkezelési átjáró – több csomópont figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Az **átjáró** lapon engedélyezheti a **Speciális beállításokat,** hogy olyan speciális metrikákat lásson, mint **a Hálózat**(be/ki), a szerepkör & a hitelesítő adatok **állapota**, ami hasznos az átjáróproblémák hibakeresésében, és az **egyidejű feladatok** (Futó/ Korlát), amelyek a teljesítmény hangolása során ennek megfelelően módosíthatók/ módosíthatók. Az alábbi táblázat az átjárócsomópontok listájának **oszlopait** tartalmazza:  

Figyelési tulajdonság | Leírás
:------------------ | :---------- 
Név | Az átjáróhoz társított logikai átjáró és csomópontok neve.  
status | A logikai átjáró és az átjárócsomópontok állapota. Példa: Online/Offline/Limited/etc. Ezekről az állapotokról az [Átjáró állapota](#gateway-status) című szakaszban talál további információt. 
Verzió | A logikai átjáró és az egyes átjárócsomópontok verzióját jeleníti meg. A logikai átjáró verziója a csoport ban lévő csomópontok többségének verziója alapján kerül meghatározásra. Ha a logikai átjáró beállításában különböző verziójú csomópontok vannak, csak a logikai átjáróval azonos verziószámmal rendelkező csomópontok működnek megfelelően. Mások a korlátozott módban, és manuálisan kell frissíteni (csak abban az esetben, ha az automatikus frissítés sikertelen). 
Igénybe vehető memória | Az átjárócsomóponton rendelkezésre álló memória. Ez az érték egy közel valós idejű pillanatkép. 
Processzorhasználat | Egy átjárócsomópont processzorkihasználtsága. Ez az érték egy közel valós idejű pillanatkép. 
Hálózatkezelés (be/ki) | Átjárócsomópont hálózati kihasználtsága. Ez az érték egy közel valós idejű pillanatkép. 
Egyidejű feladatok (futó/korlát) | Az egyes csomópontokon futó feladatok vagy feladatok száma. Ez az érték egy közel valós idejű pillanatkép. A korlát az egyes csomópontok maximális egyidejű feladatait jelenti. Ez az érték a gép mérete alapján van meghatározva. Növelheti a korlátot az egyidejű feladat-végrehajtás ütemezéséhez speciális forgatókönyvekben, ahol a CPU/ memória/ hálózat kihasználatlan, de a tevékenységek időtúllépés nélkül jelennek meg. Ez a funkció egycsomópontos átjáróval is elérhető (még akkor is, ha a méretezhetőség i és rendelkezésre állási szolgáltatás nincs engedélyezve). További információt a [Méretezési szempontok című](#scale-considerations) részben talál. 
Szerepkör | Kétféle szerepkör létezik : diszpécser és dolgozó. Minden csomópont dolgozó, ami azt jelenti, hogy mind használható feladatok végrehajtásához. Csak egy diszpécser csomópont van, amely feladatok/feladatok lekérése a felhőszolgáltatásokból, és különböző munkavégző csomópontokba (beleértve magát is) történő elküldésére szolgál. 

![Adatkezelési átjáró – több csomópont fejlett figyelése](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Átjáró állapota

Az alábbi táblázat egy **átjárócsomópont**lehetséges állapotát tartalmazza: 

status  | Megjegyzések/forgatókönyvek
:------- | :------------------
Online | A Data Factory szolgáltatáshoz csatlakoztatott csomópont.
Offline | A csomópont offline állapotban van.
Frissítés | A csomópont automatikus frissítése folyamatban van.
Korlátozott | A kapcsolódási probléma miatt. A HTTP-port 8050-es problémája, a szolgáltatásbusz-kapcsolat problémája vagy a hitelesítő adatok szinkronizálása miatt fordulhat elő. 
Inaktív | A csomópont más többségi csomópontok konfigurációjától eltérő konfigurációban van.<br/><br/> Egy csomópont inaktív lehet, ha nem tud csatlakozni más csomópontokhoz. 


Az alábbi táblázat a **logikai átjáró**lehetséges állapotát tartalmazza. Az átjáró állapota az átjárócsomópontok állapotától függ. 

status | Megjegyzések
:----- | :-------
Regisztrációt igényel | Ehhez a logikai átjáróhoz még nincs csomópont regisztrálva.
Online | Az átjárócsomópontok online állapotban vannak
Offline | Nincs csomópont online állapotban.
Korlátozott | Az átjáró nem minden csomópontja kifogástalan állapotban van. Ez az állapot egy figyelmeztetés, hogy néhány csomópont lehet, hogy nem! <br/><br/>Lehet, hogy a hitelesítő adatok szinkronizálási problémája a diszpécser/feldolgozó csomóponton. 

### <a name="pipeline-activities-monitoring"></a>Csővezeték/ tevékenységek figyelése
Az Azure Portal részletes csomópontszint-részletekkel biztosít folyamatfigyelési élményt. Például azt mutatja, hogy mely tevékenységek futottak, amelyeken a csomópont. Ez az információ hasznos lehet egy adott csomópont teljesítményproblémáinak megértésében, például a hálózati szabályozás miatt. 

![Adatkezelési átjáró – több csomópont figyelése folyamatokhoz](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Adatkezelési átjáró - folyamat részletei](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Méretezési szempontok

### <a name="scale-out"></a>Horizontális felskálázás
Ha a **rendelkezésre álló memória alacsony,** és a **CPU-használat magas,** egy új csomópont hozzáadása segít horizontális felskálázása a terhelés gépek között. Ha a tevékenységek sikertelenek az időtúloldalon vagy az átjárócsomópont offline állapotban, akkor segít, ha csomópontot ad hozzá az átjáróhoz.
 
### <a name="scale-up"></a>Vertikális felskálázás
Ha a rendelkezésre álló memória és a PROCESSZOR nem jól használja, de az alapjárati kapacitás 0, a csomóponton futtatható egyidejű feladatok számának növelésével kell felskáláznia. Előfordulhat, hogy a tevékenységek időtúllépés esetén is felskálázni szeretné, mert az átjáró túlterhelt. Ahogy az alábbi képen látható, növelheti a csomópont maximális kapacitását. Javasoljuk, hogy duplázza meg.  

![Adatkezelési átjáró – méretezési szempontok](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Ismert problémák/megszakító változások

- Jelenleg legfeljebb négy fizikai átjárócsomópontot hozhat létre egyetlen logikai átjáróhoz. Ha teljesítménybeli okokból négynél több csomópontra van [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)szüksége, küldjön e-mailt a rendszernek.
- Az átjárócsomópont ot nem regisztrálhatja újra a hitelesítési kulccsal egy másik logikai átjáróról az aktuális logikai átjáróról való váltáshoz. Az újbóli regisztráláshoz távolítsa el az átjárót a csomópontról, telepítse újra az átjárót, és regisztrálja a másik logikai átjáró hitelesítési kulcsával. 
- Ha az összes átjárócsomóponthoz HTTP-proxy szükséges, állítsa be a proxyt a diahost.exe.config és a diawp.exe.config fájlban, és a kiszolgálókezelő segítségével győződjön meg arról, hogy minden csomópont azonos diahost.exe.config és diawip.exe.config. A részleteket a [Proxybeállítások konfigurálása](data-factory-data-management-gateway.md#configure-proxy-server-settings) című szakaszban találja. 
- A csomópont-csomópont kommunikáció titkosítási módjának módosításához az Átjáró konfigurációkezelőjében törölje a portál összes csomópontját, kivéve egyet. Ezután adja hozzá a csomópontokat a titkosítási mód módosítása után.
- Ha úgy dönt, hogy titkosítja a csomópont-csomópont kommunikációs csatornát, használjon hivatalos TLS-tanúsítványt. Az önaláírt tanúsítvány kapcsolódási problémákat okozhat, mivel előfordulhat, hogy ugyanaz a tanúsítvány nem megbízható más gépek hitelesítésszolgáltatói listájában. 
- Nem regisztrálhat átjárócsomópontot logikai átjáróhoz, ha a csomópont verziója alacsonyabb, mint a logikai átjáró verziója. Törölje a logikai átjáró összes csomópontját a portálról, így regisztrálhat egy alacsonyabb verziójú csomópontot(visszalépést). Ha törli egy logikai átjáró összes csomópontját, manuálisan telepítse nésfel és regisztrálja az új csomópontokat az adott logikai átjáróra. Ebben az esetben az expressz beállítás nem támogatott.
- Az expressz telepítő nem használható csomópontok telepítésére egy meglévő logikai átjáróra, amely még mindig felhőalapú hitelesítő adatokat használ. A Beállítások lapon található Átjárókonfiguráció-kezelőben ellenőrizheti, hogy hol tárolja a hitelesítő adatokat.
- Az expressz telepítő nem használható csomópontok telepítésére egy meglévő logikai átjáróra, amelynek csomópont-csomópont titkosítása engedélyezve van. Mivel a titkosítási mód beállítása magában foglalja a tanúsítványok manuális hozzáadását, az expressz telepítés már nem lehetséges. 
- A helyszíni környezetből származó fájlmásolás esetén ne \\használjon többé localhost vagy C:\fájlokat, mivel előfordulhat, hogy a localhost vagy a helyi meghajtó nem érhető el az összes csomóponton keresztül. Ehelyett a \\Kiszolgálónév\fájlok segítségével adja meg a fájlok helyét.


## <a name="rolling-back-from-the-preview"></a>Visszaállítás az előnézetből 
Az előnézetből való visszaállításhoz egy csomóponton kívül törölje az összes csomópontot. Nem számít, hogy mely csomópontokat törli, de győződjön meg arról, hogy legalább egy csomópont a logikai átjáróban. Törölheti a csomópontot az átjáró eltávolításával a gépen vagy az Azure Portal használatával. Az Azure Portalon a **Data Factory** lapon kattintson a Csatolt szolgáltatások elemre a **Csatolt szolgáltatások** lap elindításához. Válassza ki az **átjárót** az átjáró elindításához. Az átjáró lapon láthatja az átjáróhoz társított csomópontokat. A lap lehetővé teszi egy csomópont törlését az átjáróból.
 
A törlés után kattintson az **előnézeti funkciók** ra ugyanazon az Azure Portal-lapon, és tiltsa le az előnézeti funkciót. Visszaállította az átjárót egy ga (általános elérhetőségi) átjáróra.


## <a name="next-steps"></a>További lépések
Tekintse át a következő cikkeket:
- [Adatkezelési átjáró](data-factory-data-management-gateway.md) – részletes áttekintést nyújt az átjáróról.
- [Adatok áthelyezése a helyszíni és a felhőbeli adattárak között](data-factory-move-data-between-onprem-and-cloud.md) – részletes útmutatót tartalmaz az átjáró egyetlen csomópontos használatával kapcsolatos részletes útmutatóval. 
