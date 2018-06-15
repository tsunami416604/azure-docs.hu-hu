---
title: Az infrastruktúra biztonsági másolat szolgáltatással Azure verem végzetes adatvesztés helyreállíthatók |} Microsoft Docs
description: Ha egy végzetes hiba miatt sikertelen, az Ön Azure-verem az infrastruktúra adatok vissza, ha az Azure Alkalmazásveremben üzembe visszaállítása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 7ca3945dd3768ac71e0a962417f0a621aa83be1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075720"
---
# <a name="recover-from-catastrophic-data-loss"></a>Végzetes adatvesztés helyreállítása

*A következőkre vonatkozik: Azure verem integrált rendszerek.*

Azure verem Azure szolgáltatást futtat, az adatközpontban található. Az Azure-verem mérete négy csomópont egyetlen szekrényben telepítve legyen a környezetben is futtatható. Ezzel szemben Azure fut, legfeljebb 40 régiókban több adatközpontot és minden régióban több zóna. Felhasználói erőforrásokat is kiterjedhetnek több kiszolgálók, például rackszekrények, adatközpontok és régiókban. Az Azure-vermet jelenleg csak választhat a központi telepítése a teljes körű felhőalapú egyetlen rögzítve. Ez mutatja a felhőbe, a datacenter vagy a hiba oka, hogy nagyobb hibák katasztrofális események kockázatát. Ha egy olyan vészhelyzet esetén éri, az Azure-verem példány offline állapotba kerül. Az összes adat potenciálisan helyreállíthatatlan.

Attól függően, hogy az adatok elvesztését okozza szükség lehet egy egyetlen infrastruktúra-szolgáltatás javítsa vagy állítsa vissza a teljes Azure verem-példány. Akkor is szükség lehet ugyanazon a helyen, vagy egy másik helyen lévő másik hardverre visszaállítása.

A forgatókönyv címek helyreállítása a teljes telepítési hiba esetén a készülék és a magánfelhő újbóli telepítését.

| Forgatókönyv                                                           | Adatvesztés                            | Megfontolandó szempontok                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Végzetes adatvesztés katasztrófa vagy termék hiba miatt helyreállítása | Az összes infrastruktúra és a felhasználó és az alkalmazás adatokat | Felhasználói alkalmazás és az adatok védett külön infrastruktúra adatokból |

## <a name="workflows"></a>Munkafolyamatok

Az Azure Start védelmének út kezdődik-e adatok biztonsági mentése az infrastruktúra- és alkalmazás/bérlői külön-külön. Ez a dokumentum bemutatja, hogyan adhat az infrastruktúra védelméhez. 

![Első üzembe Azure verem](media\azure-stack-backup\azure-stack-backup-workflow1.png)

A legrosszabb esetben forgatókönyvekben, ahol minden adat elveszik Azure verem helyreállítása során a rendszer az egyedi infrastruktúra adatok visszaállítása Azure verem és az összes felhasználói adatot telepítésének. 

![Telepítse újra az Azure verem](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Visszaállítás

Ha nincs végzetes adatvesztés, de továbbra is használható a hardver, Azure verem újratelepítés megadása kötelező. Során újbóli üzembe helyezése megadhatja a tárolási helyét és a biztonsági másolatok eléréséhez szükséges hitelesítő adatokat. Ebben a módban nincs szükség adhatja meg a szolgáltatásokat, amelyeket vissza kell állítani. Biztonsági mentés vezérlő infrastruktúra esetében a telepítési munkafolyamat részeként vezérlősík vezérlőhivatkozás.

Ha egy olyan vészhelyzet esetén, hogy ez a beállítás a hardver nem használható, újbóli üzembe helyezése csak kiszolgálón lehetőség új hardverre. Újratelepítés hetet is igénybe vehet néhány helyettesítő hardver van rendezve, és az adatközponton belül érkezik. Bármikor vezérlő vezérlősík adatainak helyreállítását lehetőség. Visszaállítás azonban nem támogatott, ha az újratelepített példányának verziószáma nagyobb, mint a legutóbbi biztonsági mentés használt verzió több verziója. 

| A telepítési módban | Kezdőpont | Végpont                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tiszta telepítés   | Alapkonfiguráció build | OEM Azure verem telepíti, és a legújabb támogatott verzióra frissíti.                                                                                                                                          |
| Helyreállítási módban   | Alapkonfiguráció build | OEM Azure verem helyreállítási módban telepíti, és kezeli a rendelkezésre álló legutóbbi biztonsági követelmények megfelelő verziója. Az eredeti Berendezésgyártók által támogatott legújabb verzióra történő frissítése a központi telepítés befejeződik. |

## <a name="data-in-backups"></a>Adatok biztonsági mentései

Azure verem támogatja a felhő helyreállítási módban nevű központi telepítési típus. Ebben az üzemmódban csak akkor, ha úgy dönt, hogy katasztrófa utáni helyreállításhoz. Azure-verem vagy termék hiba kerül a megoldás helyreállíthatatlan szolgál. Ebben a telepítési módban nem tudja elhárítani a megoldásban tárolt felhasználói adatok. Ebben a telepítési módban a hatóköre korlátozva visszaállítása a következő adatokat:

 - Központi telepítés bemenetek
 - Belső identitáskezelő rendszerekkel
 - Összevont konfigurációs (leválasztott központi telepítésekhez) azonosítása
 - Belső hitelesítésszolgáltató által használt legfelső szintű tanúsítványok
 - Az Azure erőforrás-kezelő konfigurációs felhasználói adatok, például előfizetések, tervek, ajánlatok és kvóták tárolási, hálózati és számítási erőforrásokat
 - KeyVault titkok és tárolók
 - Az RBAC házirend-hozzárendelések és a szerepkör-hozzárendelések 

A felhasználó infrastruktúra (IaaS) szolgáltatásként vagy Platform (PaaS) szolgáltatás-erőforrások egyike helyreállítás üzembe helyezése során. Vagyis IaaS virtuális gépeket, storage-fiókok, blobok, táblák, hálózati konfigurációt, és így tovább, elvesznek. Felhőbeli helyreállítási célja annak biztosítása érdekében a kezelők és a felhasználók a portálon ismét be tud jelentkezni a telepítés befejezése után. Naplózás vissza a felhasználók nem fogják látni az erőforrásokat. Felhasználók saját vissza előfizetéssel rendelkezik, és együtt, amely az eredeti terveket, és a rendszergazda által meghatározott olyan szabályzatot kínál. A felhasználók bejelentkezés a rendszer az eredeti megoldásban előtt a katasztrófa miatt szükséges azonos korlátozások alatt működik. Felhő helyreállítás után kézzel is helyreállíthatja az operátor hozzáadott értéket és a külső RPs, és a hozzájuk kapcsolódó adatok.

## <a name="next-steps"></a>További lépések

 - További információk az ajánlott eljárások [az infrastruktúra biztonsági másolat szolgáltatással](azure-stack-backup-best-practices.md).
