---
title: "A TRAFFIC Manager-végpont típusú |} Microsoft Docs"
description: "Ez a cikk ismerteti a különböző használható az Azure Traffic Manager-végpontok"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-végpontok
A Microsoft Azure Traffic Manager lehetővé teszi, hogy hogyan a hálózati forgalom különböző adatközpontokban futó alkalmazások telepítése esetén elosztott vezérlését. Minden alkalmazás központi telepítésének egy végpontként a Traffic Manager konfigurálása. Ha a Traffic Manager DNS-kérelmet kap, térjen vissza a DNS-válasz egy elérhető végpontot úgy dönt. A TRAFFIC manager végpont aktuális állapota és a forgalom-útválasztási módszer választása alapjait. További információkért lásd: [Traffic Manager működése](traffic-manager-how-traffic-manager-works.md).

Traffic Manager által támogatott végpont három típusa van:
* **Azure-végpontok** Azure-ban üzemeltetett szolgáltatásokhoz tartoznak.
* **Külső végpontok száma** a szolgáltatások üzemeltetett külső Azure, a helyi vagy egy másik szolgáltató használata.
* **A beágyazott végpontok** kombinálhatja a Traffic Manager-profilok létrehozása rugalmasabb forgalom-útválasztási rendszerek nagyobb, összetettebb telepítések igényeinek támogatására használhatók.

Hogyan van összevonva különböző típusú végpontok egy Traffic Manager-profil nincs korlátozva van. Az egyes profilok típusú végpontok esetében bármilyen kombinációját is tartalmazhat.

A következő szakaszok ismertetik az egyes alaposabb típusú végpont.

## <a name="azure-endpoints"></a>Azure-végpontok

Azure-végpontok Azure-alapú szolgáltatásokhoz a Traffic Manager tartoznak. A következő Azure erőforráscsoport-típusok támogatottak:

* A felhőalapú szolgáltatások "Klasszikus" infrastruktúra-szolgáltatási virtuális gépeknél és PaaS.
* Web Apps
* Nyilvános erőforrásokat (virtuális gépek is csatlakoztathatók közvetlenül, vagy egy Azure Load Balancer). A nyilvános rendelkeznie kell egy DNS-nevet hozzárendelni a Traffic Manager-profil használható.

Nyilvános erőforrásokhoz olyan erőforrások, Azure Resource Manager. A klasszikus üzembe helyezési modellel azok nem léteznek. Így azok csak a támogatott Traffic Manager Azure Resource Manager lép. A más típusú végpontok esetében az erőforrás-kezelő és a klasszikus telepítési modellel keresztül támogatottak.

Azure-végpontok használata esetén a Traffic Manager észleli, ha a "Klasszikus" IaaS virtuális gépek, a felhőalapú szolgáltatás vagy a webes alkalmazás leáll, majd elindítani. Ez az állapot a végpont állapota is megjelenik. Lásd: [Traffic Manager-végpont figyelési](traffic-manager-monitoring.md#endpoint-and-profile-status) részleteiről. Az alapul szolgáló szolgáltatás leáll, amikor a Traffic Manager nem végez végpont állapotellenőrzést vagy a végpont közvetlen forgalmat. A leállított példány számlázási Traffic Manager-események nem fordulhat elő. A szolgáltatás újraindításakor számlázási folytatása és a a végpont nem jogosult forgalom fogadására. Az észlelés nem vonatkozik a nyilvános végpontok.

## <a name="external-endpoints"></a>Külső végpontok száma

Külső végpontok száma a Azure-on kívüli szolgáltatáshoz használt. Például egy szolgáltatást a helyben tárolt vagy másik szolgáltatóhoz. Külső végpontok száma külön-külön használja, vagy együtt az Azure-végpontok a Traffic Manager-profilt. Külső végpontok száma az Azure-végpontok kombinálásával lehetővé teszi, hogy a különböző forgatókönyvekben:

* Vagy az aktív-aktív vagy aktív-passzív feladatátvevő modell használható Azure ad egy meglévő nagyobb redundancia helyszíni alkalmazás megadására.
* A felhasználók számára a világ különböző alkalmazás késés csökkentése érdekében terjesztheti ki egy meglévő helyszíni alkalmazást további földrajzi helyek, az Azure-ban. További információkért lásd: ["Teljesítmény" Traffic Manager forgalom-útválasztás](traffic-manager-routing-methods.md#performance).
* A további kapacitás egy létező használata Azure a helyszíni alkalmazás, folyamatosan vagy egy "kapacitásnövelés felhő" megoldás egy csúcs az igények kielégítéséhez.

Bizonyos esetekben célszerű hivatkozhasson rá az Azure-szolgáltatások használatához a külső végpontok száma (példákért lásd a [gyakran ismételt kérdések](traffic-manager-faqs.md#traffic-manager-endpoints)). Állapot-ellenőrzési eredményeire számlázása ebben az esetben az Azure-végpontok díj nem külső végpontok száma másodpercenként. Azonban eltérően az Azure-végpontok, leállítása vagy törlése a mögöttes szolgáltatás állapotának ellenőrzése számlázási továbbra is fennáll, addig, amíg letiltása, vagy törölni a Traffic Manager-végpontot.

## <a name="nested-endpoints"></a>Beágyazott végpontok

Beágyazott végpontok több Traffic Manager-profilok létrehozása a rugalmas forgalom-útválasztási sémák, és kielégítse az összetett, nagyobb méretű telepítések össze. Beágyazott végpontokon "child" profil profilhoz van adva, a végpont egy "parent". A gyermek és szülő profilok tartalmazhat bármilyen típusú, beleértve a más beágyazott profilok végpontja. További információkért lásd: [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps végpontként

További vegye figyelembe a következőket: a Traffic Manager végpontként webalkalmazások konfigurálásakor:

1. Csak a "Standard" SKU vagy meghaladja a webalkalmazások jogosultak a Traffic Managerrel történő használathoz. A webes alkalmazás egy alacsonyabb metódust felvételére irányuló kísérletek sikertelenek. A Traffic Manager már nem olyan adatforgalmat küldenie, hogy a webes alkalmazás alacsonyabb verziójúra változtatása egy már meglévő webalkalmazás Termékváltozata eredménye.
2. Amikor a végpont egy HTTP-kérelem érkezik, az a "" állomásfejléc a kérelemben meghatározásához mely webalkalmazás kell feldolgozni a kérelmet. Az állomásfejléc tartalmazza a DNS-név, amelyről a kérést, például "contosoapp.azurewebsites.net". A webalkalmazás egy másik DNS-név használatához regisztrálni kell a DNS-nevet az alkalmazás az egyéni tartomány nevét. Egy webalkalmazás végpont Azure végpontjaként hozzáadásakor a Traffic Manager-profil DNS-neve automatikusan regisztrálja az alkalmazást. Ez a regisztráció automatikusan törlődik, amikor a végpontja törölve.
3. Minden egyes Traffic Manager-profil legfeljebb egy webalkalmazás-végpontot rendelkezhet minden Azure-régióban. Az ennél a határértéknél megoldása érdekében webalkalmazás is létrehozható, amely egy külső végpont. További információkért lásd: a [gyakran ismételt kérdések](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Engedélyezése és letiltása a végpontok száma

A Traffic Manager végpont letiltása traffic ideiglenesen eltávolítása a karbantartási mód vagy újratelepítés alatt álló végpontok hasznos lehet. Ha a végpont újra fut, újra engedélyezni lehet.

Végpontok engedélyezve van, és le van tiltva a Traffic Manager-portál, PowerShell, a parancssori felületen vagy a REST API-t olyan erőforrás-kezelő és a klasszikus üzembe helyezési modellel is támogatottak.

> [!NOTE]
> Az Azure a végpont letiltása rendelkezik köze az Azure rendszerbeli üzembe helyezési állapotát. Az Azure-szolgáltatások (például egy virtuális gép vagy a webes alkalmazás továbbra is fut, és akkor is, ha le van tiltva a Traffic Manager forgalom fogadására alkalmasak. Forgalom is kezelhetők, közvetlenül a szolgáltatáspéldány számára, nem pedig a Traffic Manager-profil DNS-név használatával. További információkért lásd: [Traffic Manager működése](traffic-manager-how-traffic-manager-works.md).

Az aktuális való jogosultságát, mindegyik végpont forgalom fogadására a következő tényezőktől függ:

* A profil állapota (engedélyezett vagy letiltott)
* A végpont állapota (engedélyezett vagy letiltott)
* Az eredmények a rendszerállapot ellenőrzi, hogy a végpont

További információkért lásd: [Traffic Manager-végpont figyelési](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> A Traffic Manager DNS szintjén működik, mert nem tudja befolyásolni a tetszőleges végpontot meglévő kapcsolatok. A végpont nem érhető el, ha a Traffic Manager irányítja új kapcsolatot egy másik elérhető végpontot. A gazdagép a letiltott vagy nem megfelelő végpont mögött azonban továbbra is meglévő kapcsolatokon keresztül forgalom fogadására, amíg a munkamenetek leállítása van. Alkalmazások a munkamenet időtartama a meglévő kapcsolatok kiürítésére forgalom engedélyezésére kell korlátoznia.

Ha egy profil végpontjai le vannak tiltva, vagy ha a profil le van tiltva, majd a Traffic Manager "NXDOMAIN" választ küld egy új DNS-lekérdezést.


## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [Traffic Manager működése](traffic-manager-how-traffic-manager-works.md).
* Ismerje meg a Traffic Manager [végpont figyelési és automatikus feladatátvételt](traffic-manager-monitoring.md).
* Ismerje meg a Traffic Manager [forgalom-útválasztási módszerei](traffic-manager-routing-methods.md).
