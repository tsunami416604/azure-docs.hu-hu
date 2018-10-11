---
title: Kapacitás megtervezése a az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben |} A Microsoft Docs
description: Az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben kapacitástervezése
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 7cdcd8b7e9814c206255077fae0af2029fab6583
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078113"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Az Azure App Service-ben kiszolgálói szerepkörök az Azure Stackben kapacitástervezése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Állítsa be az Azure App Service az Azure Stacken kész éles környezet, meg kell terveznie a kapacitás, a rendszer támogatja a várt.  

Ez a cikk nyújt útmutatást a számítási példányok és számítási éles rendszerek üzembe használjon SKU-k minimális száma.

Az App Service-ben kapacitás stratégia használatával ezeket az irányelveket is tervezhet. Azure Stack későbbi verzióiban biztosít magas rendelkezésre állási lehetőségek az App Service-ben.

| Az App Service-kiszolgálói szerepkör | Legrégebbi ajánlott példányok száma | Javasolt számítási Termékváltozat|
| --- | --- | --- |
| Vezérlő | 2 | A1 |
| Előtér | 2 | A1 |
| Kezelés | 2 | A3 |
| Közzétevő | 2 | A1 |
| Webes feldolgozó – megosztott | 2 | A1 |
| Webes feldolgozó – dedikált | 2 / csomag | A1 |

## <a name="controller-role"></a>Vezérlői szerepkör

**Ajánlott minimális**: A1 Standard két példánya

Az Azure App Service Controller általában teljesen alacsony felhasználás a Processzor, memória és hálózati erőforrásokat. Ugyanakkor a magas rendelkezésre állás érdekében rendelkeznie kell két vezérlőn. Két vezérlőn is rendelkezésre állnak a tartományvezérlők engedélyezett maximális számát. Hozhat létre a második Web Sites vezérlő közvetlen a telepítő a telepítés során.

## <a name="front-end-role"></a>Az előtérbeli szerep

**Ajánlott minimális**: A1 Standard két példánya

Az előtér felé irányítja a kérelmeket a webes feldolgozók webes feldolgozó rendelkezésre állás. A magas rendelkezésre állás érdekében egynél több előtér rendelkeznie kell, és legfeljebb két rendelkezhet. Kapacitástervezési célból fontolja meg, hogy egyes maghoz kezelhetik a körülbelül 100 vonatkozó kérelmek másodpercenkénti száma.

## <a name="management-role"></a>Felügyeleti szerepkör

**Ajánlott minimális**: A3 Standard két példánya

Az Azure App Service felügyeleti szerepkör az App Service az Azure Resource Manager és az API-végpontokat, portál extensions (felügyeleti, a bérlő, a Functions portálon.) és az adatszolgáltatás felelős. A felügyeleti kiszolgálói szerepkör általában csak a 4 GB RAM, éles környezetben kapcsolatos van szükség. Azonban ez tapasztalhat magas CPU-szintű (például a webhely létrehozása) számos felügyeleti feladatot el kell végezni. A magas rendelkezésre állás érdekében kell rendelkeznie ehhez a szerepkörhöz hozzárendelt egynél több kiszolgálón, és legalább két kiszolgáló magok.

## <a name="publisher-role"></a>Közzétevői szerepkör

**Ajánlott minimális**: A1 Standard két példánya

Hány felhasználó egyszerre tesz közzé, ha a közzétevői szerepkör tapasztalhat a nagy CPU-használat. A magas rendelkezésre állás érdekében győződjön meg arról, egynél több közzétevői szerepkör érhető el.  A kiadó csak kezeli az FTP-/ FTPS-forgalmat.

## <a name="web-worker-role"></a>Webes feldolgozó szerepkör

**Ajánlott minimális**: A1 Standard két példánya

A magas rendelkezésre állás érdekében legalább négy webes feldolgozói szerepkörök van szükség, két megosztott webhely-üzemmód és két egyes dedikált feldolgozói réteg csomagot az ajánlathoz. A megosztott és dedikált számítási mód szolgáltatás különböző szinteken biztosít a bérlők számára. Ha az ügyfelek számos további a webes feldolgozók lehet szükség:

- Dedikált számítási mód feldolgozói rétegek (amelyek erőforrás-igényes.) használatával
- A megosztott számítási üzemmódban fut.

Miután a felhasználó által létrehozott App Service-csomag számára egy dedikált számítási üzemmódra Termékváltozat, a megadott App Service-csomag már nem lesz elérhető a felhasználók számára, hogy webes feldolgozót száma.

Adja meg az Azure Functions a használatalapú csomag modell a felhasználók számára, a webes feldolgozók megosztott kell telepíteni.

Amikor eldönti, megosztott webes feldolgozói szerepkörök számának használatához, tekintse át ezeket a szempontokat:

- **Memória**: memória mérete a kritikus fontosságú erőforrás egy webes feldolgozói szerepkör esetében. Nincs elég memória a virtuális memória lemezről átváltásakor hatással van a webhely teljesítményét. Minden kiszolgálón az operációs rendszer 1,2 GB RAM szükséges. A küszöbérték fölött RAM segítségével webhelyeket.
- **Aktív webhelyek százaléka**: általában az Azure Stack üzembe helyezés az Azure App Service-alkalmazások körülbelül 5 %-os aktívak. Azonban, amely egy adott időpontban aktív kérelmek aránya magasabb vagy alacsonyabb lehet. Egy aktív alkalmazás arány 5 %-os, helyezze el az Azure Stack üzemelő példányon, egy Azure App Service-alkalmazások maximális számát lehet kisebb, mint:
  - 20 alkalommal aktív webhelyek (5 x 20 = 100) száma.
- **Átlagos memória-erőforrás-igényű**: az alkalmazások éles környezetben megfigyelt átlagos memóriaigénye körülbelül 70 MB. Az erőforrás-igényű használja, minden webes feldolgozói szerepkörben működő számítógép vagy virtuális gépek számára fenntartott memória mérete számítható ki a következő:

    *Kiépített alkalmazások száma * 70 MB * 5 % - (szám a webes feldolgozói szerepkörök * 1044 MB)*

   Például ha a környezetben, 10 webes feldolgozói szerepkörök 5000 alkalmazások is vannak, egyes webes feldolgozói szerepkörök VM 7060 MB RAM-MAL kell rendelkeznie:

   5 000 * 70 * 0,05 – (10 * 1044) = 7060 (= 7 GB-os)

   Több feldolgozói példányból hozzáadásával kapcsolatos további információkért lásd: [további feldolgozói szerepkörök hozzáadása](azure-stack-app-service-add-worker-roles.md).

## <a name="file-server-role"></a>Fájlkiszolgálói szerepkör

A Fájlkiszolgáló szerepkör használható önálló fájlkiszolgáló fejlesztési és tesztelési, például amikor az Azure Stack Development Kit futó Azure App Service üzembe helyezése használhatja a sablon - <https://aka.ms/appsvconmasdkfstemplate>. Termelési célra egy előre konfigurált Windows-fájlkiszolgáló, vagy egy előre konfigurált nem Windows fájlkiszolgáló kell használnia.

Éles környezetben a Fájlkiszolgáló szerepkör teljesen intenzív lemez i/o. Kezelőkód összes tartalom és alkalmazás fájlt a felhasználó-webhelyekhez, mert előre konfigurálnia kell ezt a szerepkört az alábbiak egyikét:

- Windows fájlkiszolgáló
- a Windows fájlkiszolgáló fürt
- nem Windows fájlkiszolgáló
- a nem Windows-fájlkiszolgáló fürt
- (hálózati tárolóeszközök) NAS-eszközök

További információkért lásd: [fájlkiszolgáló üzembe helyezése](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>További lépések

[Mielőtt elkezdené, az Azure Stack App Service-szel](azure-stack-app-service-before-you-get-started.md)
