---
title: "Kapacitásának tervezése az Azure App Service kiszolgálói szerepkörök Azure verem |} Microsoft Docs"
description: "Kapacitástervezési Azure verem Azure App Service kiszolgálói szerepköre tekintetében"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 8926955d5e0260b5971e07b6988bb21df9980847
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Kapacitástervezési Azure verem Azure App Service kiszolgálói szerepköre tekintetében
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure App Service Azure veremben készen éles környezet kiépítéséhez, figyelembe kell vennie a kapacitás, a rendszer támogatja a várt.  Ez a példányok és számítási kell használnia minden éles környezet termékváltozat minimális száma útmutatást.

Az App Service kapacitás stratégia használja ezeket az irányelveket is tervezhet. Azure verem jövőbeli verzióiban az App Service magas rendelkezésre állású lehetőségeket kínál.

| App Service-kiszolgálói szerepkör | Ajánlott a példányok száma | Ajánlott számítási Termékváltozat|
| --- | --- | --- |
| Vezérlő | 2 | A1 |
| Előtér | 2 | A1 |
| Kezelés | 2 | A3 |
| Közzétevő | 2 | A1 |
| Webes munkavállalók - megosztott | 2 | A1 |
| Webes munkavállalók - dedikált | 2 / réteg | A1 |

## <a name="controller-role"></a>Tartományvezérlő szerepkör

**Ajánlott minimális**: A1 standard két példánya

Az Azure App Service Controller általában teljesen alacsony használat Processzor, memória és hálózati erőforrásokat. Azonban a magas rendelkezésre állás, rendelkeznie kell két vezérlő. Két vezérlő megtalálhatók a tartományvezérlők engedélyezett maximális számát. Létrehozhat a második webhelyek vezérlő közvetlen a telepítő a telepítés során.

## <a name="front-end-role"></a>Első szerep

**Ajánlott minimális**: A1 standard két példánya

Az előtér kérelmeket továbbítja a webes munkavállalók attól függően, hogy a webes munkavégző rendelkezésre állási. A magas rendelkezésre állás érdekében egynél több előtér kell lennie, és akkor is meghaladja a kettőt. Kapacitástervezési célból fontolja meg, hogy minden egyes core kezelni tud a körülbelül 100 kérések száma másodpercenként.

## <a name="management-role"></a>Kezelés szerepköre

**Ajánlott minimális**: A3 két példánya

Az App Service Azure Resource Manager és API-végpontokon portál extensions (admin, tenant, Functions portálon) és a szolgáltatás felelős az Azure App Service felügyeleti szerepkört. A felügyeleti kiszolgálói szerepkör általában csak a 4 GB RAM, éles környezetben kapcsolatos van szükség. Azonban ez problémákat tapasztalhat a Processzor magas szintű (például a webhely létrehozása) számos felügyeleti feladatok végrehajtásakor. A magas rendelkezésre állás ehhez a szerepkörhöz rendelt egynél több kiszolgálón kell lennie, és legalább két processzormag kiszolgálónként.

## <a name="publisher-role"></a>A Publisher szerepkör

**Ajánlott minimális**: A1 két példánya

Ha sok felhasználó tesz közzé egy időben, a közzétevő szerepkört problémákat tapasztalhat a nagy CPU-felhasználást. A magas rendelkezésre állás szabadítson fel több közzétevő szerepkört.  A közzétevő kizárólag az FTP-/ FTPS-forgalmat kezeli.

## <a name="web-worker-role"></a>Webes feldolgozói szerepkör

**Ajánlott minimális**: A1 két példánya

A magas rendelkezésre állás érdekében legalább négy webes feldolgozói szerepköröket kell rendelkeznie, kettőt csak a megosztott webhelyet üzemmód és két minden speciális feldolgozó réteg azt tervezi, hogy kínálnak. A megosztott és dedikált számítási módok szolgáltatás különböző szinteken biztosít a bérlők számára. További webalkalmazás munkavállalók lehet szükség, ha sok ügyfél van:
 - dedikált számítási mód munkavégző rétegek (amelyek erőforrás-igényes)
 - megosztott számítási módban fut.

Miután a felhasználó hozott létre egy App Service-csomag egy dedikált számítási mód Termékváltozat, a webes dolgozniuk abban, hogy az App Service-csomag már nem lesz elérhető a felhasználók számára megadott számát.

Adja meg az Azure Functions a felhasználók számára a fogyasztás terv modellben, megosztott webes munkavállalók kell telepítenie.

Amikor eldönti, a megosztott webes feldolgozói szerepkörök számának használatához, tekintse át ezeket a szempontokat:

- **Memória**: memória az a kritikus fontosságú erőforrás egy webes feldolgozói szerepkör esetében. Nincs elég memória hatások webhely teljesítményét az alábbi virtuális memória lemezről van cserélve. Minden egyes kiszolgáló 1,2 GB RAM-MAL az operációs rendszer igényel. A küszöbérték fölött RAM webhelyek futtatásához használható.
- **Az aktív webhelyek százalékos**: alkalmazások az Azure App Service üzemelő Azure verem körülbelül 5 százalékát általában aktív. Azonban a kérelmek százalékos arányát, amely egy adott időpontban aktív magasabb vagy alacsonyabb lehet. 5 százalékkal aktív alkalmazás sebesség, az alkalmazások az Azure App Service az Azure Alkalmazásveremben üzembe helyezendő maximális száma nem lehet kisebb, mint:
    - 20 alkalommal aktív webhelyek (5 x 20 = 100) száma.
- **Átlagos memóriaigény**: az átlagos memóriaigény éles környezetekben megfigyelt alkalmazások pedig körülbelül 70 MB. Ezért az összes webes feldolgozói szerepkörben működő számítógép vagy virtuális gépek között lefoglalt memória kerülhet sor, az alábbiak szerint:

    *Kiépítve alkalmazások száma * 70 MB * 5 % - (száma a webszolgáltatás feldolgozói szerepkörök * 1044 MB)*

   Például ha 10 webes munkavégző szerepkört futtató környezetben 5000 alkalmazások, minden webes feldolgozói szerepkör VM 7060 MB RAM-ot kell rendelkezniük:

   5000 * 70 * 0,05 – (10 * 1044) = 7060 (= 7 GB-os)

   Több worker-példányok hozzáadásával kapcsolatos további információkért lásd: [további feldolgozói szerepkörök hozzáadása](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Fájlkiszolgálói szerepkör

A Fájlkiszolgáló szerepkör használható önálló fájlkiszolgáló fejlesztési és tesztelési, például amikor üzembe helyezése az Azure verem szoftverfejlesztői készlet az Azure App Service használhatja ezt a sablont - https://aka.ms/appsvconmasdkfstemplate. Élesben való használat esetén érdemes használni a előre beállított Windows-fájlkiszolgálón, vagy egy előre megadott-Windows fájlkiszolgálókon.

Éles környezetben a Fájlkiszolgáló szerepkör intenzív lemez i/o észlel. Mert Kezelőkód összes tartalom és alkalmazás fájlt a felhasználó-webhelyekhez, előre, konfigurálnia kell a szerepkör a következőket:
- a Windows File Server
- Fájlkiszolgálófürt
- a-Windows fájlkiszolgáló
- fájlkiszolgálófürt
- További információt NAS (hálózati csatolt tároló) eszköz lásd [kiépíteni egy fájlkiszolgáló](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>További lépések

[Az App Service Azure veremben megkezdése előtt](azure-stack-app-service-before-you-get-started.md)
