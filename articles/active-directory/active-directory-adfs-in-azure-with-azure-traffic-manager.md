---
title: Magas rendelkezésre állású AD FS telepítése az Azure-ban Azure Traffic Managerrel | Microsoft Docs
description: Ebből a dokumentumból megtanulhatja, hogyan helyezze üzembe az AD FS szolgáltatást az Azure-ban, és biztosítson ezzel magas fokú rendelkezésre állást.
keywords: Ad FS és Azure Traffic Manager, adfs és Azure Traffic Manager, földrajzi, több adatközpont, földrajzi adatközpontok, több földrajzi adatközpont, AD FS telepítése az azure-ban, azure adfs telepítése, azure adfs, azure ad fs, adfs telepítése, ad fs telepítése, adfs az azure-ban, adfs telepítése az azure-ban, AD FS telepítése az azure-ban, adfs azure, AD FS bemutatása, Azure, AD FS az Azure-ban, iaas, ADFS, adfs áthelyezése az azure-ba
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: a14bc870-9fad-45ed-acd5-a90ccd432e54
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: anandy;billmath
ms.openlocfilehash: e984d3d590021e3dd9e46d0f12493889b2acc229
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26604779"
---
# <a name="high-availability-cross-geographic-ad-fs-deployment-in-azure-with-azure-traffic-manager"></a>Magas rendelkezésre állású AD FS telepítése az Azure-ban Azure Traffic Managerrel
Az [AD FS telepítése az Azure-ban](active-directory-aadconnect-azure-adfs.md) című cikk részletesen ismerteti egy egyszerű AD FS-infrastruktúra telepítésének lépéseit az Azure-ban a szervezete számára. A cikk bemutatja az AD FS az Azure-ba történő, földrajzi határokon átívelő telepítésének további lépéseit az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) használatával. Az Azure Traffic Managerrel egy földrajzilag kiterjedt, magas rendelkezésre állású és nagy teljesítményű AD FS-infrastruktúrát hozhat létre szervezete számára, amelyhez számos útválasztási módszer áll rendelkezésre a különböző igények infrastruktúrából történő kielégítésére.

A magas rendelkezésre állású, földrajzi határokon átívelő AD FS-infrastruktúra a következőket teszi lehetővé:

* **Az egypontos meghibásodás kockázatának kiküszöbölése:** Az Azure Traffic Manager feladatátvételi funkciójával egy magas rendelkezésre állású AD FS-infrastruktúrát hozhat létre, még akkor is, ha az egyik adatközpont leáll a világ valamely pontján.
* **Jobb teljesítmény:** A cikkben leírt ajánlott telepítés használatával magas rendelkezésre állású AD FS-infrastruktúrát tarthat fenn, amely segíthet a felhasználóknak a gyorsabb hitelesítésben. 

## <a name="design-principles"></a>Tervezési alapelvek
![Általános tervezési szempontok](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/blockdiagram.png)

Az alapvető tervezési alapelvek megegyeznek az AD FS telepítése az Azure-ban című cikkben felsorolt tervezési alapelvekkel. A fenti ábrán az alapszintű telepítés egy másik földrajzi régióba történő egyszerű kiterjesztése látható. A telepítés egy új földrajzi régióba való kiterjesztésekor érdemes figyelembe venni az alábbi pontokat

* **Virtuális hálózat:** Érdemes létrehozni egy új virtuális hálózatot abban a földrajzi régióban, ahová egy további AD FS-infrastruktúrát kíván telepíteni. A fenti ábrán az egyes földrajzi régiók Geo1 VNET és Geo2 VNET nevű virtuális hálózatai láthatók.
* **Tartományvezérlők és AD FS-kiszolgálók egy új földrajzi virtuális hálózatban:** Ajánlott tartományvezérlőket telepíteni az új földrajzi régióban, hogy az új régióban lévő AD FS-kiszolgálóknak ne kelljen egy másik, távoli hálózatban lévő tartományvezérlővel kapcsolatba lépniük egy hitelesítés elvégzése során. Ezáltal jelentősen növelhető a teljesítmény.
* **Tárfiókok:** A tárfiókok egy adott régióhoz tartoznak. Mivel egy új földrajzi régióba telepít gépeket, az adott régióban használható, új tárfiókokat is létre kell hoznia.  
* **Hálózati biztonsági csoportok:** A tárfiókokhoz hasonlóan az egy adott régióban létrehozott hálózati biztonsági csoportok sem használhatók egy másik földrajzi régióban. Ezért az első földrajzi régióban lévőkhöz hasonló, új hálózati biztonsági csoportokat kell létrehoznia az INT és DMZ alhálózatokhoz az új földrajzi régióban.
* **DNS-címkék a nyilvános IP-címekhez:** Az Azure Traffic Manager KIZÁRÓLAG DNS-címkéken keresztül tud végpontokra hivatkozni. Ezért DNS-címkéket kell létrehoznia a külső terheléselosztók nyilvános IP-címeihez.
* **Azure Traffic Manager:** A Microsoft Azure Traffic Managerrel szabályozható a világ különböző pontjain található adatközpontokban futó szolgáltatásvégpontokra érkező felhasználói forgalom elosztása. Az Azure Traffic Manager a DNS szintjén működik. DNS-válaszok használatával irányítja a végfelhasználói forgalmat a globálisan elosztott végpontok felé. Ezután az ügyfelek közvetlenül a végpontokhoz csatlakoznak. A különböző útválasztási beállításoknak (teljesítménycentrikus, súlyozott, prioritásos) köszönhetően könnyedén kiválaszthatja a szervezete igényeinek leginkább megfelelő megoldást. 
* **Virtuális hálózatok közötti kapcsolat két régió között:** A virtuális hálózatoknak nem kell kapcsolatban lenniük. Mivel minden virtuális hálózat hozzáfér a tartományvezérlőkhöz, valamint AD FS- és WAP-kiszolgálót is tartalmaz, a különböző régiókban található virtuális hálózatok egymással kialakított kapcsolat nélkül is működhetnek. 

## <a name="steps-to-integrate-azure-traffic-manager"></a>Az Azure Traffic Manager integrálásának lépései
### <a name="deploy-ad-fs-in-the-new-geographical-region"></a>Az AD FS telepítése az új földrajzi régióban
Kövesse az [AD FS telepítése az Azure-ban](active-directory-aadconnect-azure-adfs.md) című cikk lépéseit és irányelveit az azonos topológia az új földrajzi régióban történő telepítéséhez.

### <a name="dns-labels-for-public-ip-addresses-of-the-internet-facing-public-load-balancers"></a>Az internetkapcsolattal rendelkező (nyilvános) terheléselosztók nyilvános IP-címeinek DNS-címkéi
A korábban említetteknek megfelelően az Azure Traffic Manager csak végpontokként tud hivatkozni a DNS-címkékre, ezért fontos, hogy DNS-címkéket hozzon létre a külső terheléselosztók nyilvános IP-címeihez. Az alábbi képernyőfelvétel bemutatja, hogyan konfigurálhat DNS-címkét a nyilvános IP-címhez. 

![DNS-címke](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfabstsdnslabel.png)

### <a name="deploying-azure-traffic-manager"></a>Az Azure Traffic Manager telepítése
Kövesse az alábbi lépéseket a Traffic Manager-profil létrehozásához. További információkért lásd: [Az Azure Traffic Manager-profilok kezelése](../traffic-manager/traffic-manager-manage-profiles.md).

1. **Traffic Manager-profil létrehozása:** Lássa el a Traffic Manager-profilt egy egyedi névvel. A profil neve része a DNS nevének, továbbá a Traffic Manager tartománynév-címkéjének előtagjaként is szolgál. A Traffic Manager DNS-címkéjének létrehozásához a név/előtag lesz hozzáadva a .trafficmanager.net címhez. Az alábbi képernyőfelvételen a Traffic Manager DNS-előtagja „mysts”-ként lett megadva, így a létrejövő DNS-címke a mysts.trafficmanager.net lesz. 
   
    ![Traffic Manager-profil létrehozása](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/trafficmanager01.png)
2. **Forgalomirányítási módszer:** A Traffic Manager három útválasztási beállítást tartalmaz:
   
   * Prioritás 
   * Teljesítmény
   * Súlyozott
     
     A **Teljesítmény** a gyors válaszidejű AD FS-infrastruktúra eléréséhez ajánlott beállítás. Azonban ehelyett bármilyen másik útválasztási módszert is megadhat, ha az jobban megfelel a környezeti igényeknek. Az AD FS működését nem befolyásolja a kiválasztott útválasztási módszer. További információkért lásd: [A Traffic Manager forgalomirányítási módszerei](../traffic-manager/traffic-manager-routing-methods.md). A fenti, mintául szolgáló képernyőfelvételen a **Teljesítmény** módszer van kiválasztva.
3. **Végpontok konfigurálása:** A Traffic Manager oldalán kattintson a végpontokra, majd válassza a Hozzáadás elemet. Ezzel megnyit egy, az alábbi képernyőfelvételen láthatóhoz hasonló Végpont hozzáadása oldalt.
   
   ![Végpontok konfigurálása](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/eastfsendpoint.png)
   
   A különböző adatbevitelek esetében kövesse az alábbi útmutatást:
   
   **Típus:** Válassza az Azure-végpont lehetőséget, mivel egy nyilvános Azure IP-címre fogunk mutatni.
   
   **Név:** Adja meg a végponthoz rendelni kívánt nevet. Ez nem a DNS-név, és nincs hatással a DNS-rekordokra.
   
   **Célerőforrás típusa:** Ehhez a tulajdonsághoz válassza a Nyilvános IP-cím értéket. 
   
   **Célerőforrás:** Itt lehetősége lesz választani az előfizetéséhez elérhető különböző DNS-címkék közül. Válasszon a konfigurálni kívánt végpontnak megfelelő DNS-címkét.
   
   Adjon végpontot minden olyan földrajzi régióhoz, amelybe forgalmat kíván irányítani az Azure Traffic Manager által.
   A végpontok Traffic Managerben történő hozzáadásával és konfigurálásával kapcsolatos további információkért és a lépések részletes ismertetéséért lásd: [Végpontok felvétele, letiltása, engedélyezése és törlése](../traffic-manager/traffic-manager-endpoints.md).
4. **Mintavétel konfigurálása:** A Traffic Manager oldalán kattintson a Konfigurálás elemre. A konfigurációs oldalon módosítania kell a figyelési beállításokat, hogy a mintavétel a 80-as HTTP-portnál és az /adfs/probe relatív elérési úton történjen.
   
    ![Mintavétel konfigurálása](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/mystsconfig.png) 
   
   > [!NOTE]
   > **Győződjön meg arról, hogy a konfigurálás végeztével a végpontok állapota ONLINE legyen**. Ha minden végpont „csökkentett teljesítményű” állapotban van, az Azure Traffic Manager igyekszik a lehető legjobb módon irányítani a forgalmat, feltételezve, hogy a diagnosztika hibás, és minden végpont elérhető.
   > 
   > 
5. **Az Azure Traffic Manager DNS-rekordjainak módosítása:** Az összevonási szolgáltatásnak egy, az Azure Traffic Manager DNS-névre mutató CNAME-rekordnak kell lennie. Hozzon létre egy CNAME-rekordot a nyilvános DNS-rekordokban, hogy amikor valaki az összevonási szolgáltatást próbálja elérni, valójában az Azure Traffic Managert érje el.
   
    Például az fs.fabidentity.com összevonási szolgáltatás a Traffic Manager felé történő irányításához a következők szerint kellene frissítenie a DNS-rekordokat:
   
    <code>fs.fabidentity.com IN CNAME mysts.trafficmanager.net</code>

## <a name="test-the-routing-and-ad-fs-sign-in"></a>Az útválasztás és az AD FS-bejelentkezés tesztelése
### <a name="routing-test"></a>Útválasztási teszt
Az útválasztás tesztelésének egy igen alapvető módja lehet az összevonási szolgáltatás DNS-nevének pingelése az egyes földrajzi régiókban lévő gépekről. A kiválasztott útválasztási módszertől függően a ténylegesen pingelt végpont jelenik meg a művelet végeredményeként. Ha például a teljesítménycentrikus útválasztást állította be, akkor az ügyfél régiójához legközelebb eső végpont lesz visszaadva. Az alábbi képernyőfelvételen két ping látható két különböző régió ügyfelének gépéről (egy a kelet-ázsiai térségéből, egy pedig az USA nyugati régiójából). 

![Útválasztási teszt](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/pingtest.png)

### <a name="ad-fs-sign-in-test"></a>AD FS-bejelentkezési teszt
Az AD FS a legegyszerűbb módon az IdpInitiatedSignon.aspx oldal használatával tesztelhető. Ahhoz, hogy ez megvalósítható legyen, engedélyezze az IdpInitiatedSignOn tulajdonságot az AD FS tulajdonságaiban. Az AD FS beállításának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Az engedélyezéshez futtassa az alábbi parancsmagot az AD FS-kiszolgálón a PowerShell segítségével. 
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
2. Bármilyen külső gépről keresse fel a következő címet: https://<yourfederationservicedns>/adfs/ls/IdpInitiatedSignon.aspx
3. Az AD FS oldalának az alábbi módon kell megjelennie:
   
    ![ADFS-teszt – hitelesítő kérdés](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest1.png)
   
    a sikeres bejelentkezés után pedig az alább látható sikert jelző üzenetet kapja:
   
    ![ADFS-teszt – sikeres hitelesítés](./media/active-directory-adfs-in-azure-with-azure-traffic-manager/adfstest2.png)

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Alapszintű AD FS-telepítés az Azure-ban](active-directory-aadconnect-azure-adfs.md)
* [Microsoft Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [A Traffic Manager forgalomirányítási módszerei](../traffic-manager/traffic-manager-routing-methods.md)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Traffic Manager-profilok kezelése](../traffic-manager/traffic-manager-manage-profiles.md)
* [Végpontok felvétele, letiltása, engedélyezése és törlése](../traffic-manager/traffic-manager-endpoints.md) 

