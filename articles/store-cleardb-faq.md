---
title: A ClearDB MySql-adatbázisok az Azure App Service – GYIK |} A Microsoft Docs
description: A ClearDB MySQL-adatbázisok Azure App Service-ben való használatával kapcsolatos gyakori kérdésekre adott válaszok.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.workload: data-management
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: cce6555c71829110781a6dd13757078b105dbb41
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230631"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>ClearDB MySQL-adatbázisok használata az Azure App Service szolgáltatásban – gyakori kérdések
Ez a GYIK használatával, és az Azure Web Appshez az adatbázisok a ClearDB MySQL vásárlásával kapcsolatos gyakori kérdésekre ad választ.

> [!IMPORTANT]
> 2018. június 13. a ClearDB továbbítjuk jelenleg a számlázás alapját a Microsoft a ClearDB közvetlen számlázási modellt az Azure-alapú ügyfelek. Ebben a cikkben található információk már elavult. Már nem sikerült létrehozni vagy frissíteni a ClearDB-adatbázis, amely az Azure-ban lett létrehozva.
>
> További részletek és a következő lépések: [átvált a ClearDB-service-csomagok](http://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).


## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Milyen lehetőségek vannak az Azure MySQL-hez?
Több lehetősége van:

* [Megosztott MySQL a ClearDB-adatbázis](/marketplace/partners/cleardb/databases/)
* [A ClearDB MySQL prémium fürtök](/marketplace/partners/cleardb-clusters/cluster/)
* [MySQL-fürt az Azure virtuális gépen](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [MySQL-beli virtuális gépen futó egyetlen példánya](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

A ClearDB egy MySQL-üzemeltetési szolgáltatás, és a MySQL-infrastruktúrával kezeli az Ön számára. Amikor futtatja a saját MySQL-fürt vagy az adatbázis egy Azure virtuális gépen, akkor állítsa be a MySQL-kiszolgálót, és folyamatosan frissíteni a javítások.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>A webalkalmazás és MySQL az Azure Marketplace-sablon esetében van szükségem hitelkártyával?
Ez az előfizetés típusától függ. Íme néhány gyakran használt előfizetés típusa:

* [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/): hitelkártya, valamint való megvásárlása után kell fizetni a megadott bankkártyára fizetős MySQL-adatbázis szükséges.
* [Az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/): krediteket tartalmaz, használja a Microsoft Azure-szolgáltatások, de nem teszi lehetővé a külső erőforrások beszerzési. Vásárolja meg a harmadik féltől származó szolgáltatásokkal vagy egy fizetős MySQL-adatbázist kell hitelkártyát használnia engedélyezve van az előfizetésben. A Web Apps létrehozhat egy ingyenes a ClearDB MySQL-adatbázishoz.
* [MSDN-nel](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) és **MSDN Dev-Test használatalapú**: hasonlóan az ingyenes próbaverzióban, MSDN-előfizetés szükséges hozzá, arra, hogy fizetős megoldás MySQL a ClearDB hitelkártya.
* [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): nagyvállalati szerződéssel rendelkező ügyfelek számlázása a nagyvállalati szerződés terhére negyedévente összes az Azure Marketplace-en (külső) vásárlások külön, összevont számlán. A bármelyik piactéren történő vásárlások pénzügyi kötelezettségvállalást kívül számítjuk fel. Vegye figyelembe, hogy jelenleg Azure Store nem érhető el az ügyfelek számára a regisztrált Azerbajdzsán, Horvátország, Norvégia és Puerto Ricóban. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Miért érdemes fizetni egy webalkalmazás és MySQL az Azure Marketplace-ről a 3.50 $?
Az alapértelmezett adatbázis-beállítás Titan $3.50. Hogy ne jelenjen meg a költségek az adatbázis létrehozása során, és előfordulhat, hogy véletlenül vásárol egy adatbázis nem szeretett volna. Szeretne megtalálni a élmény javításához, de addig gombra kattintás előtt kell jelölnie minden a kiválasztott tarifacsomag webalkalmazáshoz és adatbázishoz **létrehozás** és az erőforrások a telepítés elindítása.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>MySQL futtatok saját Azure virtuális gépen. Csatlakozhatok az Azure webalkalmazás adatbázisom?
Igen. Mindaddig, amíg az Azure virtuális gép távoli hozzáférést adott a webalkalmazásra a webalkalmazás képes kapcsolódni az adatbázishoz. További információkért lásd: [MySQL telepítése virtuális gépen](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Országban, amelyben támogatott prémium szintű MySQL a ClearDB-fürtök, amelyek?
[Prémium szintű MySQL a ClearDB-fürtök](/marketplace/partners/cleardb-clusters/cluster/) érhetők el az összes Azure-régiókban világszerte India, Ausztrália, Dél-Brazília és Kína kivételével.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>A prémium szintű megoldást a ClearDB-adatbázis létrehozása előtt egy új fürtöt hozhat létre?
Üres a ClearDB-fürtök létrehozása során nem, nem támogatott. Az Azure portal adatbázisok létrehozása egy fürtben, amely előfordulhat, hogy hozzon létre egy új fürtöt egyszerre teszi lehetővé.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Tudom a rendszer figyelmezteti Ha meg törölni a ClearDB MySQL-adatbázis, amely használatban van egy másik alkalmazásom?
Nem, az Azure nem figyelmezteti, ha törli a Marketplace-beli vásárlás, hogy az alkalmazása függ.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Mely régiók hozható létre a ClearDB-adatbázis?
Az Azure Marketplace-en regisztrálva Azerbajdzsán, Horvátország, Norvégia és Puerto Rico az ügyfelek számára nem érhető el. A ClearDB nem érhető el ezekben a régiókban.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Milyen tarifacsomag kell választani egy éles webes alkalmazások és az adatbázis?
Web Apps alapszintű vagy egy magasabb díjszabási csomagot használja. A ClearDB javasoljuk, hogy a Szaturnusz vagy a Jupiter tervet. Tekintse át a funkciók és korlátozások minden tarifacsomaghoz mindkét [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) és [ClearDB MySQL-adatbázisok](/marketplace/partners/cleardb/databases/) , válassza ki azt, amelyik a legjobban az igényeinek.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Hogyan frissíthetek ClearDB-adatbázis a csomag egy másikra?
Az a [az Azure portal](https://portal.azure.com), megosztott üzemeltetési adatbázis a ClearDB vertikálisan felskálázhatja. Olvassa el ezt [cikk](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) további. Jelenleg nem támogatjuk frissítés ClearDB prémium fürtök az Azure Portalon.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nem látom a ClearDB-adatbázis Azure Portalon?
A ClearDB-adatbázis a klasszikus hozta létre, ha nem lesz megtekinthető az adatbázis a [az Azure Portal](https://portal.azure.com). Nincs nincs megkerülő ehhez a forgatókönyvhöz.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Kihez fordulhatok támogatás, ha nem működik az adatbázis?
Kapcsolattartó [ClearDB támogatási](https://www.cleardb.com/developers/help/support) kapcsolódó problémák adatbázisok mindegyike esetében. Készüljön fel szolgálnak az Azure-előfizetés adatait.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Létrehozhatok további felhasználók számára a ClearDB MySQL database megoldást?
Nem. További felhasználók nem hozható létre, de további adatbázist a ClearDB-adatbázis fürtön is létrehozhat.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Alapszintű/Pro sorozat adatbázisok kell frissítése is helyben hasonló planetáris csomagok még ma a ClearDB-portálon?
Igen, alapszintű sorozat adatbázisok is lehet frissíteni a helyben (alapszintű 60 alapszintű 500 keresztül). Haladó sorozat lehet helyben (és Pro 1000 Pro 125) kivételével Pro 60 frissítve. Pro 60 adatbázis frissítése jelenleg nem támogatott. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Át tudom telepíteni az erőforrások egy előfizetésből egy másikba, ha nem a ClearDB MySQL-adatbázis települnek át is?
Végrehajtásakor erőforrás-migrálás, előfizetések között bizonyos [korlátozások](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) vonatkoznak. A ClearDB MySQL-adatbázis egy külső szolgáltatás, és ezért nem települnek át az Azure-előfizetés áttelepítése során. Ha nem kezeli az Azure-erőforrások áttelepítése előtt a MySQL-adatbázis áttelepítése, a ClearDB MySQL-adatbázisok letiltható. Manuálisan át először adatbázisait, és hajtsa végre az Azure-előfizetés áttelepítése a webalkalmazás. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>A költségkeret kattintsunk az előfizetéshez. Ha eltávolítottam a korlát, és az App Service-ben online állapotban, de az adatbázis nem érhető el. Hogyan engedélyezhetem újból a ClearDB-adatbázis?
Kapcsolattartó [ClearDB támogatási](https://www.cleardb.com/developers/help/support) engedélyezheti újra az adatbázist. Adja meg azokat a saját Azure-előfizetés információkat és az adatbázis nevével.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Áthelyezhetők-e a ClearDB-adatbázis hitelkártya előfizetésből EA-előfizetéshez?
Meglévő ClearDB-adatbázis használata a meglévő előfizetésekhez kapcsolódó hitelkártyát. A nagyvállalati szerződéses előfizetésében kell migrálnia az adatokat egy új adatbázisra használata:

* Vásároljon egy új, a nagyvállalati szerződéses előfizetésében a ClearDB-adatbázis.
* Telepítse át az adatokat az új adatbázis.
* Az alkalmazás használhatja az új adatbázis frissítéséhez.
* Törölje a régi ClearDB-adatbázis.

Hozzon létre egy új webalkalmazást a MySQL (ClearDB), vagy hozzon létre egy MySQL-adatbázis (ClearDB), ha úgy dönt, az előfizetés határozza meg, hogyan ki kell fizetnie a szolgáltatás. EA-előfizetéssel hogy nem blokkolja a beszerzés, a külső szolgáltatások, például a ClearDB az Azure Portalon. EA-előfizetések számlázása a pénzügyi kötelezettségvállalás kívül, és a negyedéves és utólag számítunk fel. A nagyvállalati szerződéssel rendelkező ügyfelek állítsa be a fizetési például hitelkártya bármely harmadik felek marketplace-szolgáltatások fizetnie kell.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Hol tekinthetem meg egy nagyvállalati szerződéses előfizetésében a ClearDB-erőforrások díja?
A közvetlen nagyvállalati szerződéssel rendelkező ügyfelek az Azure piactér-díjak láthatók a vállalati portál. Vegye figyelembe, hogy minden piactéren történő vásárlások és felhasználási kívül vállalt számlázása negyedévente és utólag számítunk fel. Nagyvállalati szerződéssel rendelkező ügyfelek közvetlenül a külső szolgáltatók kell fizetnem, és teheti meg fizetési módot, például a nagyvállalati szerződéssel rendelkező fiókkal bankkártya engedélyezésével.

Közvetett nagyvállalati szerződéssel rendelkező ügyfelek az Azure piactér-beli előfizetések találhatja meg a **előfizetések kezelése** a vállalati portál, de a díjszabás rejtett. Ügyfeleink forduljanak a LSP piactér-díjak tájékoztatást.

Harmadik féltől származó szolgáltatásokkal az Azure Marketplace-en való hozzáférést az EA Azure regisztrációs rendszergazdák által kezelhető. Letilthatja, vagy újra engedélyezze a hozzáférést a 3. külső vásárlásokra a fiókok kezelése és az előfizetések a Store a fiókok szakaszában, a vállalati portálon.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kihez fordulhatok kérdései vannak a ClearDB-services-számlámon kapcsolatos az EA-előfizetésemben?
Kapcsolattartó [vállalati ügyfél-támogatási](https://aka.ms/AzureEntSupport) tartományállapot számlázás mellett az EA-regisztrációhoz. Az EA Portalon ügyfélszolgálathoz fog ad választ kérdésére, vagy a probléma megoldása érdekében.

## <a name="more-information"></a>További információ
[Az Azure piactér – gyakori kérdések](https://azure.microsoft.com/marketplace/faq/)

