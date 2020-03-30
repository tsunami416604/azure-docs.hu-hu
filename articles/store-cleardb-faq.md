---
title: Gyakran feltett kérdések a ClearDB MySql-adatbázisokról az Azure App Service szolgáltatással kapcsolatban
description: Válaszok a ClearDB MySQL-adatbázisok Azure App Service-szolgáltatással való használatával kapcsolatos gyakori kérdésekre.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979915"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Gyakran feltett kérdések a ClearDB MySql-adatbázisokról az Azure App Service szolgáltatással kapcsolatban
Ez a gyIK választ ad a ClearDB MySQL-adatbázisok Azure Web Apps-hez való használatával és megvásárlásával kapcsolatos gyakori kérdésekre.

> [!IMPORTANT]
> 2018. június 13-tól a ClearDB átváltotta a Microsoft által jelenleg számlázott Azure-alapú ügyfeleket egy közvetlen számlázási modellre a ClearDB-vel. A cikkben szereplő információk már elavultak. A továbbiakban nem hozhat létre és nem frissíthet ClearDB-adatbázist, amelyet az Azure-ban hoztak létre.
>
> További részleteket és a következő lépéseket a [ClearDB szolgáltatási csomagok módosításai (ClearDB szolgáltatáscsomagok módosítása) (Módosítások a) csomagban talál.](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/)

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Milyen lehetőségeim vannak a MySQL-re az Azure-ban?
A szolgáltatással kapcsolatos legfrissebb információkért lásd a [ClearDB-t.](https://w2.cleardb.net/) ClearDB egy MySQL hosting szolgáltatás és kezeli a MySQL infrastruktúra az Ön számára. 

Számos más lehetősége van a MySQL azure-beli üzemeltetésére:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Azure-beli virtuális gépen futó MySQL-fürt](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Az Azure-beli virtuális gépen futó MySQL egyetlen példánya](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Szükségem van hitelkártyára a webalkalmazás + MySQL sablonhoz az Azure Piactéren?
Ez a használt előfizetés típusától függ. Íme néhány általánosan használt előfizetési típus:

* [Fizessen, ahogy van:](https://azure.microsoft.com/offers/ms-azr-0003p/)Hitelkártyát igényel, és amikor fizetett MySQL adatbázist vásárol, a hitelkártyáját terhelik.
* [Ingyenes próbaverzió:](https://azure.microsoft.com/pricing/free-trial/)A Microsoft Azure-szolgáltatásokkal való használatra szánt krediteket tartalmazza, de nem teszi lehetővé külső erőforrások vásárlását. Harmadik fél szolgáltatásainak vagy fizetős MySQL-adatbázisnak a megvásárlásához hitelkártya-kompatibilis előfizetést kell használnia. Web alkalmazások hozhatja létre egy ingyenes ClearDB MySQL adatbázis.
* [MSDN-előfizetés](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) és **MSDN fejlesztői tesztfizetés menet közben:** Az ingyenes próbaverzióhoz hasonlóan az MSDN-előfizetéshez is szükség van egy hitelkártyára, hogy fizetős MySQL-megoldást vásároljon a ClearDB-től.
* [Nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Az EA-ügyfelek minden negyedévben az Azure Marketplace-en (harmadik féltől származó) vásárlások után külön, összevont számlán kerülnek számlázásra. A számlázás a pénzbeli kötelezettségvállaláson kívül történik a piactéri vásárlásokért. Kérjük, vegye figyelembe, hogy az Azure Store jelenleg nem érhető el az Azerbajdzsánban, Horvátországban, Norvégiában és Puerto Ricóban regisztrált ügyfelek számára. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Miért kellett 3,50 USD-t fizetnem egy webalkalmazásért + MySQL-ért az Azure Piactéren?
Az alapértelmezett adatbázis-beállítás a Titan, amely 3,50 000 000 000.000$. Az adatbázis létrehozása során nem jelenítjük meg a költségeket, és előfordulhat, hogy véletlenül olyan adatbázist vásárol, amelyet nem kívánt. Igyekszünk megtalálni a módját, hogy javítsa a felhasználói élményt, de addig ellenőriznie kell az összes kiválasztott tarifacsomagok webalkalmazás és adatbázis, mielőtt a **Létrehozása** és az erőforrások üzembe helyezésének megkezdése.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>A MySQL-t a saját Azure virtuális gépemen futtatom. Csatlakoztathatom az Azure-webalkalmazásomat az adatbázisomhoz?
Igen. A webalkalmazást mindaddig csatlakoztathatja az adatbázishoz, amíg az Azure virtuális gép e távoli hozzáférést adott a webalkalmazáshoz. További információ: [A MySQL telepítése virtuális gépen](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Mely országokban/régiókban támogatottak a ClearDB Premium MySQL-fürtök?
A ClearDB Premium MySQL-fürtök világszerte az Azure minden régiójában elérhetők, Kivéve Indiát, Ausztráliát, Dél-Brazíliát és Kínát.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Létrehozhatok új fürtöt, mielőtt létrehoznék egy adatbázist cleardb prémium szintű fürtmegoldással?
Nem, az üres ClearDB-fürtök létrehozása nem támogatott. Az Azure portal lehetővé teszi, hogy adatbázisokat hozzon létre egy fürtben, amely egyidejűleg új fürtöt hozhat létre.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Figyelmeztetnek, ha megpróbálom törölni az egyik alkalmazásom által használt ClearDB MySQL adatbázist?
Nem, az Azure nem figyelmezteti, ha töröl egy piactéri vásárlást, amelytől az alkalmazás függ.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Mely régiókban hozhatok létre ClearDB adatbázisokat?
Az Azure Marketplace nem érhető el azerbajdzsáni, horvátországi, norvégiai vagy Puerto Ricó-i regisztrált ügyfelek számára. A ClearDB nem érhető el ezekben a régiókban.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Milyen tarifacsomagot válasszak egy éles webalkalmazáshoz és adatbázishoz?
Használja az alapszintű vagy magasabb tarifacsomag a webalkalmazások. A ClearDB esetében szaturnuszi vagy Jupiter-tervet ajánlunk. Tekintse át az egyes alkalmazáscsomagok [és](https://azure.microsoft.com/pricing/details/app-service/) a [ClearDB MySQL-adatbázisok](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) egyes tarifacsomagjainak funkcióit & korlátait, és válassza ki az igényeinek megfelelőt.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hogyan frissíthetem a ClearDB-adatbázisomat egyik csomagról a másikra?
Az [Azure Portalon](https://portal.azure.com)egy ClearDB megosztott üzemeltetési adatbázis t. Olvassa el ezt a [cikket,](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) hogy többet. Jelenleg nem támogatjuk a ClearDB Premium-fürtök frissítését az Azure Portalon.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nem látom a ClearDB-adatbázisomat az Azure Portalon?
Ha klasszikus módban hozott létre ClearDB-adatbázist, nem fogja látni az adatbázist az [Azure Portalon.](https://portal.azure.com) Ehhez a forgatókönyvhöz nincs megoldás.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Kihez fordulhatok segítségért, ha az adatbázisom nem áll meg?
Az adatbázissal kapcsolatos problémák esetén forduljon a [ClearDB támogatási szolgálatához.](https://www.cleardb.com/developers/help/support) Készüljön fel arra, hogy megadja nekik az Azure-előfizetési adatait.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Létrehozhatok további felhasználókat a ClearDB MySQL adatbázis-fürtmegoldásomhoz?
Nem. Nem hozhat létre további felhasználókat, de további adatbázisokat is létrehozhat a ClearDB adatbázis-fürtön.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Lehet-e az Alap/ Pro sorozatú adatbázisokat helyben frissíteni, hasonlóan a Planetáris tervekhez ma a ClearDB portálon?
Igen, az alapszintű sorozatadatbázisok helyben frissíthetők (Basic 60–Basic 500). Pro sorozat bővíthető helyben (Pro 125 keresztül Pro 1000), kivéve a Pro 60. Jelenleg nem támogatjuk a Pro 60 adatbázis frissítését. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Amikor áttelepítem az erőforrásokat az egyik előfizetésből a másikba, a ClearDB MySQL-adatbázisom is áttelepül?
Amikor az előfizetések között hajt végre erőforrás-áttelepítést, bizonyos [korlátozások](azure-resource-manager/management/move-support-resources.md) érvényesek. A ClearDB MySQL-adatbázis egy külső szolgáltatás, ezért nem lesz áttelepítés az Azure-előfizetések áttelepítése során. Ha az Azure-erőforrások áttelepítése előtt nem kezeli a MySQL-adatbázis áttelepítését, a ClearDB MySQL-adatbázisok letilthatók. Először manuálisan migrálja át az adatbázisokat, majd hajtsa végre az Azure-előfizetések áttelepítését a webalkalmazáshoz. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Elértem az előfizetésem költési korlátját. Eltávolítottam a korlátot, és az App Service online állapotban van, de az adatbázis nem érhető el. Hogyan engedélyezhetem újra a ClearDB adatbázist?
Az adatbázis újbóli engedélyezéséhez lépjen kapcsolatba a [ClearDB ügyfélszolgálatával.](https://www.cleardb.com/developers/help/support) Adja meg nekik az Azure-előfizetés adatait és az adatbázis nevét.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Átvihetek ClearDB-adatbázist egy hitelkártya-előfizetésből egy Nagy- és Nagy- és Nagy- és Szolgáltatás-előfizetésbe?
A meglévő ClearDB adatbázisok a meglévő előfizetésekhez társított hitelkártyát használják. Az EA-előfizetés használatához át kell telepítenie az adatokat egy új adatbázisba:

* Vásároljon új ClearDB adatbázist az EA-előfizetésével.
* Telepítse át az adatokat az új adatbázisba.
* Frissítse az alkalmazást az új adatbázis használatához.
* Törölje a régi ClearDB adatbázist.

Amikor új webalkalmazást hoz létre a MySQL (ClearDB) segítségével, vagy létrehoz egy MySQL adatbázist (ClearDB), a kiválasztott előfizetés határozza meg, hogyan fog fizetni a szolgáltatásért. EA-előfizetéssel nem fogjuk letiltani a külső szolgáltatások, például a ClearDB beszerzését az Azure Portalon. Az EA-előfizetések számlázása a monetáris kötelezettségvállaláson kívül történik, és negyedévente és hátralékban kerülnek számlázásra. Az EA-ügyfélnek be kell állítania egy fizetési módot, például egy hitelkártyát, hogy kifizethes minden harmadik fél piactéri szolgáltatásáért.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Hol láthatom a ClearDB-erőforrások díjait egy Nagy-aba-előfizetésben?
A közvetlen Nagyvállalati Szerződéssel foglalkozó ügyfelek számára az Azure Marketplace díjai láthatók az Enterprise Portalon. Vegye figyelembe, hogy minden piactéri vásárlás és fogyasztás számlázása a monetáris kötelezettségvállaláson kívül történik, és negyedévente és hátralékban van. Az EA ügyfeleinek közvetlenül a harmadik fél szolgáltatóknak kell fizetniük, és ezt úgy tehetik meg, hogy engedélyezik a fizetési módot, például egy hitelkártyát az EA-fiókjukkal.

A közvetett Nagyvállalati Szerződés-ügyfelek az Enterprise Portal **Előfizetések kezelése** lapján megtalálhatják Azure Marketplace-előfizetéseiket, de az árak rejtettek. A Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz kell fordulniuk.

Az Azure Marketplace-hez való hozzáférést külső szolgáltatásokhoz az EA Azure-regisztrációs rendszergazdái is kezelhetik. Letilthatják vagy újra engedélyezhetik a hozzáférést a külső vásárlásokhoz az Áruházból a Fiókok és előfizetések kezelése területen az Enterprise Portal Fiókok szakaszában.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kihez fordulhatok a ClearDB-szolgáltatásokkal kapcsolatos számlámmal kapcsolatos kérdéseimért az EA-előfizetésemben?
Lépjen kapcsolatba [a nagyvállalati ügyfélszolgálattal](https://aka.ms/AzureEntSupport) a nagyvállalati szerződéssel való regisztrációval kapcsolatban. Az EA portál támogatási csapata válaszol a kérdésére, vagy segít megoldani a problémát.

## <a name="more-information"></a>További információ
[Azure Marketplace – Gyakori kérdések](https://azure.microsoft.com/marketplace/faq/)

