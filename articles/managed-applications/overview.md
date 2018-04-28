---
title: A kezelt alkalmazások Azure áttekintése |} Microsoft Docs
description: A fogalmakat ismerteti az Azure által felügyelt alkalmazások
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 35e30700270fb5757a80c4a6b8f231042f7640f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-managed-applications-overview"></a>Az Azure által felügyelt alkalmazások áttekintése

Azure kezelt alkalmazások lehetővé teszik a fogyasztók üzembe helyezéséhez és üzemeltetéséhez könnyen felhő-megoldásokat nyújtsanak. Az infrastruktúra implementálása, és adja meg a folyamatban lévő támogatási. Felszabadításához kezelt alkalmazás az összes ügyfél számára tegye közzé az Azure piactéren. Tegye elérhetővé a szervezeti felhasználók, tegye közzé azt egy belső katalógust. 

Kezelt alkalmazás hasonlít a piactéren, és egy fő különbség a megoldássablon. A kezelt alkalmazás az erőforrások létrehozni, hogy az alkalmazás kiadójának által kezelt erőforráscsoport. Az erőforráscsoport a fogyasztó előfizetésben, de a közzétevő-bérlőben identitás hozzáfér az erőforráscsoportot. A közzétevő megadja a megoldás folyamatban lévő támogatási költsége.

## <a name="advantages-of-managed-applications"></a>Felügyelt alkalmazások előnyei

Felügyelt alkalmazások korlátok csökkentheti a fogyasztók számára a megoldások segítségével. Nincs szükségük a megoldást kíván használni a felhőalapú infrastruktúrában szakértői. A fogyasztók a fontos erőforrásokhoz való hozzáférés korlátozott. Ezeket nem kell foglalkoznia így hiba, ha azt kezelése. 

Kezelt alkalmazások lehetővé teszik a felhasználók létrehozni a folyamatos kapcsolatot jelent. Megadhatja az alkalmazás kezelése feltételei, és díjak megfizetése Azure számlázás keresztül történik.

Bár az ügyfelek ezeket az előfizetéseket a kezelt alkalmazások telepítéséhez, karbantartásához, frissítéséhez vagy szolgáltatás őket nincs. Biztosíthatja, hogy minden használják az ügyfelek a jóváhagyott verziók. Az ügyfelek nem rendelkezik a fejlesztéséhez és alkalmazásspecifikus tartomány Tudásbázis ezeket az alkalmazásokat kezeléséhez. Az ügyfelek automatikusan megszerezze alkalmazást nem kell foglalkoznia a hibaelhárítás és az alkalmazásokkal kapcsolatos problémák diagnosztizálásához. 

Az informatikai csapata hozza létre a kezelt alkalmazások lehetővé teszik előre jóváhagyott-megoldásokat nyújtsanak a szervezetben lévő felhasználók részére. Biztosíthatja, hogy ezek a megoldások amelyek megfelelnek a vállalati szabványoknak.

## <a name="types-of-managed-applications"></a>Felügyelt alkalmazások

A felügyelt alkalmazást is közzétehet, külső vagy belső.

![Belső vagy külső közzététele](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>szolgáltatáskatalógus

A szolgáltatáskatalógus olyan egy belső katalógust a szervezet felhasználói jóváhagyott megoldások. A katalógus segítségével biztosítsa az egyes szervezeti szabványok során ezek a megoldások biztosít a szervezetek számára. Az alkalmazottak a katalógus segítségével könnyen felfedezheti az alkalmazásokat, amelyek az informatikai részleg által jóváhagyott és ajánlott széles skáláját. Akkor jelenik meg a kezelt alkalmazások, amelyek a szervezet más munkatársainak velük.

További információ a szolgáltatáskatalógus kezelt alkalmazás közzététele: [katalógus szolgáltatásalkalmazás létrehozása](publish-service-catalog-app.md).

### <a name="marketplace"></a>Piactér

A szolgáltatások számlázási kívánó szállítók elérhetővé teheti a kezelt alkalmazás az Azure piactéren keresztül. Miután a szállító közzétesz egy alkalmazást, érhető el a szervezeten kívüli felhasználók számára. A megközelítést, a felügyelt szolgáltató (MSPs), a független szoftvergyártók (ISV-k) és a rendszer kiegészítők (SIs) is a megoldásokat nyújtsanak az összes Azure-ügyfél számára.

További információ a piactéren kezelt alkalmazás közzététele: [piactér-alkalmazás létrehozása](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Erőforráscsoportok kezelt alkalmazások

A kezelt alkalmazás erőforrásait általában két erőforráscsoport találhatók. A fogyasztó egy erőforráscsoport, valamint kezelésére a közzétevő a másik erőforráscsoportban. A kezelt alkalmazás meghatározásakor a közzétevő a hozzáférési szintet határozza meg. A következő kép bemutatja egy olyan forgatókönyvet, ahol a közzétevő kéri a tulajdonosi szerepkört, a felügyelt erőforráscsoport. A közzétevő Ez az erőforráscsoport a fogyasztó számára olvasási zárolást helyezni. A közzétevő-azonosságok, amelyek hozzáférést kapnak a felügyelt erőforráscsoporthoz nem érvényes a zárolás.

![Erőforrás csoport hozzáférése](./media/overview/access.png)

### <a name="application-resource-group"></a>Alkalmazás erőforráscsoport

Ez az erőforráscsoport tárolja a kezelt alkalmazás-példány. Ez az erőforráscsoport neve csak tartalmazhat egy erőforrást. Az erőforrás típusa a kezelt alkalmazás **Microsoft.Solutions/applications**.

A fogyasztó az erőforráscsoport teljes hozzáféréssel rendelkezik, és használja a kezelt alkalmazás életciklusának kezelését.

### <a name="managed-resource-group"></a>Felügyelt erőforráscsoport

Ez az erőforráscsoport tárolja a kezelt alkalmazás által igényelt összes erőforrást. Például az erőforráscsoport a virtuális gépeket, a storage-fiókok és a virtuális hálózatok a megoldáshoz. A fogyasztó csak korlátozott hozzáféréssel rendelkezik az erőforráscsoporthoz, mert az ügyfél nem tudja kezelni az egyéni erőforrásokat a felügyelt alkalmazás. Ez az erőforráscsoport a közzétevő eléréséhez a kezelt alkalmazás-definícióban meghatározott szerepkör felel meg. Például a közzétevő kérhetnek a tulajdonos vagy közreműködő szerepkört az erőforráscsoport.

Ha a fogyasztó töröl a kezelt alkalmazás, a felügyelt erőforráscsoportot is törlődik.

## <a name="next-steps"></a>További lépések

* Bevezető meghatározása, és a kezelt alkalmazás telepítése, lásd: [létrehozása és telepítése az Azure által felügyelt alkalmazás Azure parancssori felülettel](managed-apps-quickstart-cli.md)
* További információ a belső alkalmazás közzététele: [katalógus szolgáltatásalkalmazás létrehozása](publish-service-catalog-app.md).
* Felügyelt alkalmazások közzétételéhez a piactéren kapcsolatos információkért lásd: [piactér-alkalmazás létrehozása](publish-marketplace-app.md).
