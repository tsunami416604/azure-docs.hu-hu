---
title: "A ClearDB MySql-adatbázisok – gyakori kérdések az Azure App Service |} Microsoft Docs"
description: "Az Azure App Service ClearDB MySQL-adatbázisok használatával kapcsolatos gyakori kérdésekre adott válaszok."
documentationcenter: php
services: 
author: sunbuild
manager: yochayk
editor: 
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: 0d1e3c826e61b8dee264e1c01fd2d54ba7ebb636
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>ClearDB MySQL-adatbázisok használata az Azure App Service szolgáltatásban – gyakori kérdések
Ez a GYIK kapcsolatos kérdésekre ad közös használatával, és az Azure Web Apps adatbázisok beszerzési a ClearDB MySQL.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Milyen lehetőségek állnak rendelkezésre az Azure-beli MySQL?
Több lehetőség közül választhat:

* [ClearDB megosztott MySQL-adatbázis](/marketplace/partners/cleardb/databases/)
* [ClearDB MySQL prémium fürtök](/marketplace/partners/cleardb-clusters/cluster/)
* [Egy Azure virtuális Gépen futó MySQL-fürt](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Egyetlen példány futhat MySQL az Azure virtuális gépen](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB egy MySQL szolgáltatást futtató és a MySQL-infrastruktúrával kezeli az Ön. Futtatásakor a saját MySQL-fürt vagy az adatbázis egy Azure virtuális gépen, ki kell a MySQL-kiszolgáló beállítása és őrizniük azok javítást frissül.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Kell hitelkártya a webes alkalmazás + az Azure piactéren MySQL-sablon?
Ez függ az előfizetés használ. Íme néhány gyakran használt előfizetés típusa:

* [Használatalapú fizetés](/offers/ms-azr-0003p/): hitelkártya, valamint a hitelkártya díjfizetéssel fizetős MySQL-adatbázis megvásárolt igényel.
* [Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/): használja a Microsoft Azure-szolgáltatások, de nem engedélyezi a külső erőforrások beszerzési kreditek tartalmazza. Harmadik féltől származó szolgáltatással vagy egy fizetős MySQL-adatbázis egy engedélyezett hitelkártya-előfizetés használatára kell vásárolnia. A Web Apps szabad ClearDB MySQL-adatbázis is létrehozhat.
* [MSDN-előfizetés](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) és **MSDN Dev teszt használatalapú**: ingyenes hasonló, az MSDN-előfizetés szükséges hozzá egy hitelkártyának ClearDB MySQL fizetős megoldás vásárolhat.
* [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Nagyvállalati ügyfelek számlázása a EA szemben az összes Azure piactéren (külső) vásárolt külön, összevont számlán negyedévenként. A pénzügyi kötelezettségvállalást a piactér beszerzésének kívül kell fizetni. Vegye figyelembe, hogy a jelenleg, Azure-tároló nem regisztrált Azerbajdzsán, horvát, norvég és Puerto Rico az ügyfél számára érhető el. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): a Web Apps csak szabad ClearDB adatbázist hozhat létre. Létrehozhat szabad ClearDB MySQL-adatbázisok száma korlátozva van. Vegye figyelembe, hogy ingyenes adatbázist a rendszer nem használja termelési web Apps, mivel ez a szolgáltatás csak próbaverzió szól.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Miért kell fizetnem a webes alkalmazás + MySQL az Azure piactérről 3.50 $?
Az alapértelmezett adatbázis-beállítás $3.50 program Titan. Az adatbázis létrehozása során nem megmutatjuk, a költség, és előfordulhat, hogy véletlenül vásárol egy adatbázist, amelyeket nem állt szándékában. Keressük élményének növelése érdekében úgy találja, de addig ellenőrizni kell az összes a kijelölt árképzési szinteket a web app és az adatbázis való kattintás előtt **létrehozása** és a források a telepítés elindítása.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>MySQL használom a saját Azure virtuális gépen. Képes kapcsolódni az Azure-webalkalmazásban adatbázis?
Igen. Mindaddig, amíg az Azure virtuális gép távoli hozzáférést adott a webalkalmazás a webalkalmazás tud kapcsolódni az adatbázishoz. További információkért lásd: [MySQL telepítése virtuális gépen](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Ahol országok, amelyek a ClearDB prémium MySQL-fürt támogatott?
[ClearDB prémium MySQL fürtök](/marketplace/partners/cleardb-clusters/cluster/) érhetők el az összes Azure-régiók világszerte India, Ausztrália, Dél-Brazília és Kína kivételével.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Hozhat létre egy új fürt előtt ClearDB prémium fürtmegoldás rendelkező adatbázis létrehozását?
Üres ClearDB fürtök létrehozására nem, nem támogatott. Az Azure-portálon adatbázisok létrehozására az a fürt, amely előfordulhat, hogy hozzon létre egy új fürtöt egy időben teszi lehetővé.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>A rendszer I figyelmezteti Ha meg törölni a ClearDB MySQL-adatbázis, amely használatban van egy saját alkalmazásaihoz?
Nem, Azure nem figyelmeztet, ha törli a piactér beszerzési, amely az alkalmazás függ.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Mely régiókat hozható létre a ClearDB adatbázist?
Az Azure piactér nincs regisztrálva Azerbajdzsán, horvát, Norvégia vagy Puerto Rico az ügyfél számára érhető el. ClearDB nem érhető el ezeken a területeken.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Milyen tarifacsomag kell kiválasztani a egy éles web app és az adatbázis?
A Web Apps használja a Basic vagy magasabb szintű tarifacsomagban használható. A ClearDB ajánlott Szaturnusz vagy Jupiter terv. Tekintse át a funkciók & mindkét egyes tarifacsomagok korlátaival [webalkalmazások](https://azure.microsoft.com/pricing/details/app-service/) és [ClearDB MySQL-adatbázisok](/marketplace/partners/cleardb/databases/) választania azt, amelyik megfelel az igényeinek.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hogyan lehet frissíteni a ClearDB adatbázist egy tervet a másikra?
Az a [Azure-portálon](https://portal.azure.com), ClearDB megosztott üzemeltetési adatbázis költenie. Olvassa el ezt [cikk](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) további. Jelenleg nem támogatjuk frissítés ClearDB prémium fürtök az Azure portálon.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nem látom az Azure-portálon a ClearDB adatbázist?
Ha létrehozott egy ClearDB adatbázist a classic, csak akkor láthatók az adatbázis a [Azure Portal](https://portal.azure.com). Nincs nincs munkahelyi körül ehhez a forgatókönyvhöz.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Aki tegye I támogatás kérése, amikor az adatbázis nem működik?
Ügyfél [ClearDB támogatási](https://www.cleardb.com/developers/help/support) az esetleges adatbázis-kapcsolatos probléma. Készüljön fel biztosítani nekik az Azure-előfizetési adatokat.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Létrehozhatók további felhasználók számára a ClearDB MySQL-adatbázis fürtmegoldás?
Nem. További felhasználók nem hozható létre, de további adatbázisok a ClearDB adatbázist fürtöt hozhat létre.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Tudja Basic/Pro sorozat adatbázisok kell frissíteni a helyi hasonló ma planetáris tervek ClearDB portálon?
Igen, adatbázisok lehet alapvető adatsorozat frissítése helyi (alapvető 500 keresztül alapvető 60). Pro adatsorozat lehet frissíteni a helyi (és Pro 1000 Pro 125) kivételével Pro 60. Pro 60 adatbázis frissítése jelenleg nem támogatott. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Ha az erőforrások egy előfizetésből, másik telepítek, nem a ClearDB MySQL-adatbázis települnek át is?
Végrehajtásakor erőforrásmigrálás előfizetésekhez, néhány [korlátozások](app-service/app-service-move-resources.md) alkalmazni. A ClearDB MySQL-adatbázis egy külső szolgáltatás, és ezért nem települnek át az Azure-előfizetés az áttelepítés során. Ha nem kezelheti az Azure-erőforrások áttelepítése előtt a MySQL-adatbázis áttelepítése, a ClearDB MySQL-adatbázisok letiltható. Manuálisan telepítse át először az adatbázisokat, és utána végezze el az Azure-előfizetés áttelepítése a webalkalmazás. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Az előfizetésem az I elérte a költségkeret maximumát. A korlát eltávolított, és az App Service érhető el, de az adatbázis nem érhető el. Hogyan újból engedélyezhető a ClearDB adatbázist?
Ügyfél [ClearDB támogatási](https://www.cleardb.com/developers/help/support) az adatbázis újbóli engedélyezéséhez. Biztosítani nekik a Azure-előfizetés információkat és az adatbázis nevét.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Átvihető egy ClearDB adatbázist hitelkártya előfizetésből EA előfizetéshez?
Meglévő ClearDB adatbázist használja a meglévő előfizetések társított bankkártyát. Egy EA előfizetés használatára kell áttelepíteni az adatokat egy új adatbázist:

* Vásárolja meg a Nagyvállalati előfizetéssel rendelkező új ClearDB adatbázist.
* Adatok áttelepítése az új adatbázishoz.
* Az alkalmazás használhatja az új adatbázis frissítéséhez.
* Törölje a régi ClearDB adatbázist.

Hozzon létre egy új webalkalmazást MySQL (ClearDB), vagy hozzon létre egy MySQL-adatbázis (ClearDB), az előfizetésben határozza meg, hogyan fogja kell fizetnie a szolgáltatást. Az EA előfizetői azt nem blokkolja a harmadik fél szolgáltatások ClearDB például az Azure-portálon az beszerzése. EA előfizetések pénzügyi kötelezettségvállalást a kívül vannak számlázva, és negyedéves és utólag számlázása. A Nagyvállalati ügyfelek kellene a például a hitelkártya fizetési mód beállítása a külső piactér szolgáltatások után kell fizetnie.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Ahol láthatja a díjak ClearDB erőforrások egy EA előfizetésben?
A közvetlen EA ügyfelek Azure piactér díjak jelennek meg a vállalati portálon. Ne feledje, hogy piactér vásárlásokat és a fogyasztás kívül pénzügyi kötelezettségvállalást a számlázása számlázása negyedévente, és az elmaradt. Nagyvállalati ügyfelek közvetlenül a külső szolgáltatók fizetnie kell, és ehhez a például a EA fiókjukkal hitelkártya fizetési mód engedélyezése.

Közvetett EA ügyfelek megtalálható az Azure piactér előfizetések a **előfizetések kezelése oldalt** az Enterprise Portal oldalon, de árképzési rejtett. Ügyfelek azok LSP piactér költségekkel kapcsolatos segítségért.

Hozzáférés az Azure piactéren külső-szolgáltatások a EA Azure regisztrációs rendszergazdák által kezelhető. Ezek tiltsa le, vagy újra hozzáférés engedélyezése a 3. fél vásárlás fiókok kezelése és előfizetések az áruházból a vállalati portál fiókok szakaszban.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Ki tegye lehet kapcsolatba lépni kérdések kapcsolatban a ClearDB szolgáltatások saját EA az előfizetést?
Ügyfél [vállalati ügyfélszolgálathoz](http://aka.ms/AzureEntSupport) jelenítik számlázási a EA regisztráció alatt. A EA Portal támogatási csoport lesz a kérdése megválaszolásában, vagy a probléma megoldása érdekében.

## <a name="more-information"></a>További információ
[Az Azure piactér – gyakori kérdések](/marketplace/faq/)

