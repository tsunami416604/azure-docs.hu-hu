---
title: Azure Spring Cloud geo – vész-helyreállítás | Microsoft Docs
description: Ismerje meg, hogyan védhető a Spring Cloud-alkalmazás a regionális kimaradásokból
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jeconnoc
ms.openlocfilehash: 4752cba6081d376bd006406a154b9b182c6a3a72
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164964"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud vész-helyreállítás

Ez a cikk azokat a stratégiákat ismerteti, amelyekkel biztosíthatja, hogy az Azure Spring Cloud-alkalmazásai a leállás során is védve legyenek.  Előfordulhat, hogy bármely régió vagy adatközpont a regionális katasztrófák által okozott állásidőt tapasztalhatja, de a gondos tervezés csökkentheti az ügyfelekre gyakorolt hatást.

## <a name="plan-your-application-deployment"></a>Az alkalmazás központi telepítésének megtervezése

Az Azure Spring Cloud-alkalmazások egy adott régióban futnak.  Az Azure világszerte több földrajzi régión működik. Az Azure földrajz a világ egy meghatározott területe, amely legalább egy Azure-régiót tartalmaz. Az Azure-régió egy földrajzon belüli terület, amely egy vagy több adatközpontot tartalmaz.  Minden egyes Azure-régió párosítva van egy másik régióval, amely ugyanabban a földrajzi helyen található, és regionális párokat alkot. Az Azure szerializálja a platform frissítéseit (tervezett karbantartás) a regionális párok között, így biztosítva, hogy egyszerre csak egy régió legyen frissítve az egyes párokban. Ha egy leállás több régiót érint, akkor az egyes párokban legalább egy régiót előnyben részesíti a helyreállítás.

A magas rendelkezésre állás és a katasztrófák elleni védelem biztosításához szükséges, hogy a Spring Cloud-alkalmazásokat több régióra telepítse.  Az Azure a [párosított régiók](../best-practices-availability-paired-regions.md) listáját jeleníti meg, hogy a tavaszi Felhőbeli üzemelő példányokat a regionális párokra tervezze.  Javasoljuk, hogy a mikro-szolgáltatás architektúrájának tervezésekor három fő tényezőt vegye figyelembe: a régiók elérhetőségét, az Azure párosított régiókat és a szolgáltatás rendelkezésre állását.

*  Régió elérhetősége: válasszon ki egy földrajzi területet a felhasználók számára a hálózati késés és az átviteli idő minimalizálásához.
*  Azure párosított régiók: ha szükséges, a kiválasztott földrajzi területen válassza a párosított régiók lehetőséget az összehangolt platform-frissítések és a rangsorolt helyreállítási erőfeszítések biztosításához.
*  Szolgáltatás rendelkezésre állása: döntse el, hogy a párosított régióknak meleg/gyakori, meleg/meleg vagy meleg/hidegen kell futniuk.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Az Azure Traffic Manager használata a forgalom irányításához

Az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) a DNS-alapú forgalom terheléselosztását biztosítja, és több régióban is terjesztheti a hálózati forgalmat.  Az Azure Traffic Manager használatával irányíthatja az ügyfeleket a legközelebbi Azure Spring Cloud Service-példányra.  A legjobb teljesítmény és redundancia érdekében az Azure Traffic Manageron keresztül az összes alkalmazás forgalmát az Azure Spring Cloud Service-be való elküldés előtt irányítsa.

Ha több régióban is rendelkezik Azure Spring Cloud-alkalmazásokkal, az Azure Traffic Manager segítségével szabályozhatja az alkalmazások forgalmának áramlását az egyes régiókban.  Definiáljon egy Azure Traffic Manager végpontot az egyes szolgáltatásokhoz a szolgáltatás IP-címének használatával. Az ügyfeleknek csatlakozniuk kell egy Azure Traffic Manager DNS-névhez, amely az Azure Spring Cloud szolgáltatásra mutat.  Az Azure Traffic Manager terheléselosztási forgalmat a meghatározott végpontok között.  Ha egy katasztrófa egy adatközpontot üt ki, akkor az Azure Traffic Manager az adott régiótól a párja felé irányítja a forgalmat, így biztosítva a szolgáltatás folytonosságát.