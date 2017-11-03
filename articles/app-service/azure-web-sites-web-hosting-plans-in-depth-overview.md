---
title: App Service-csomagok az Azure App Service Web Apps |} Microsoft Docs
description: "Ismerje meg, hogyan App Service-csomagok Azure App Service munka, és hogyan azok előnyeit, a kezelhetőséget."
keywords: "app service, azure app service, méret, méretezhető, app service-csomag, app service ára"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: fb5b782f09bdd8c8a862eddfbd65b0f86ef8d08c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="app-service-plans-in-azure-app-service-web-apps"></a>Az Azure App Service Web Apps App Service-csomagokról

App Service-csomagokról az alkalmazások futtatásához használt fizikai erőforrások gyűjteményét képviseli.

Az App Service-csomagok a következőket határozzák meg:

- A régióban (USA nyugati régiója, USA keleti régiója, stb.)
- Méretezési száma (egy, két, három példányt, stb.)
- Példányméretének (Small, Medium, Large)
- Termékváltozat (szabad, megosztott, Basic, Standard, Premium, PremiumV2, elszigetelt)

Web Apps, Mobile Apps, az API Apps, függvény alkalmazások (vagy funkciók), a [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) összes futtassa az App Service-csomagot.  Az ugyanahhoz az előfizetéshez, és a régió alkalmazások oszthatnak meg az App Service-csomag. 

Hozzárendelt összes alkalmazást egy **App Service-csomag** a meghatározott erőforrások megosztása. A megosztás menti a pénz, esetén több alkalmazást az önálló App Service-csomag.

A **App Service-csomag** a méretezhető **szabad** és **megosztott** való tiers **alapvető**, **szabványos**,  **Prémium szintű**, és **elszigetelt** rétegek. Minden egyes magasabb szintű használható további erőforrások és szolgáltatások biztosít hozzáférést.

Ha az App Service-csomag **alapvető** szintjüket, vagy újabb rendszerre, majd megadhatja a **mérete** , a méretezés, a virtuális gépek száma.

Például, ha a terv két "kicsi" példánya használatára van konfigurálva a **szabványos** réteg, mindkét esetben futtassa, hogy a tervben szereplő összes alkalmazás. Alkalmazások hozzáféréssel is rendelkeznek a **szabványos** szolgáltatások réteg. Alkalmazások futnak terv példányok teljes körűen felügyelt, és magas rendelkezésre állású.

> [!IMPORTANT]
> Az App Service-csomag tarifacsomagját (SKU) határozza meg, a költség, és nem a benne tárolt alkalmazások száma.

Ez a cikk ismerteti, az App Service-csomag árképzési szinteket, és a skála, és hogyan működnek, kezelheti az alkalmazásokat például főbb jellemzőit.

## <a name="new-pricing-tier-premiumv2"></a>Új IP-címek: PremiumV2

Az új **PremiumV2** biztosít IP-címek [Dv2 sorozatú virtuális gépek](../virtual-machines/windows/sizes-general.md#dv2-series) gyorsabb processzorok, a SSD-re és a dupla memória-core arány képest **szabványos** réteg. **PremiumV2** is támogatja a nagyobb mértékű keresztül nagyobb a példányok száma így biztosít az összes speciális képességei a Standard csomag. A meglévő összes szolgáltatásához **prémium** réteg szerepelnek **PremiumV2**.

Hasonló más dedikált szintekre, három Virtuálisgép-méretek érhetők el a réteg:

- Kis (1 Processzormagok, 3.5-ös GiB memória) 
- Közepes (2 processzormag, 7 GiB memória) 
- Nagy (CPU 4 mag, 14 GiB memória)  

A **PremiumV2** díjszabási információkért lásd: [App Service szolgáltatás díjszabása](/pricing/details/app-service/).

Az új használatába **PremiumV2** IP-címek, lásd: [az App Service konfigurálása PremiumV2 réteg](app-service-configure-premium-tier.md).

## <a name="apps-and-app-service-plans"></a>Alkalmazások és az App Service-csomagokról

Lehet, hogy az App Service alkalmazás társítva van egy adott időpontban csak egy App Service-csomag.

Alkalmazások és csomagok is tartalmaznak egy **erőforráscsoport**. Erőforráscsoport minden szereplő erőforrásra életciklus határa funkcionál. Erőforráscsoportok segítségével együtt kezelheti az alkalmazás minden a helyére.

Egyetlen erőforráscsoportként működnek rendelkezhet több App Service-csomagokról, mert rendel a különböző alkalmazások különböző fizikai erőforrásokhoz.

Például elkülönítheti a erőforrások fejlesztői, tesztelési és éles környezetek között. Éles üzemi pontjának és fejlesztési és tesztelési célú külön környezeteket rendelkező lehetővé teszi az erőforrások elkülönítése. Ezzel a módszerrel szemben az alkalmazások új verziójának tesztelése betöltése nem "versenyeznek" az az erőforrásokhoz, az éles alkalmazásokat, amelyek valós ügyfelek szolgál.

Ha több csomagokról egyetlen erőforráscsoportként működnek, megadhatja a földrajzi régió átfogó alkalmazás is.

Például egy magas rendelkezésre állású két régiókban futó alkalmazást tartalmaz legalább két terveket, minden egyes régió egy és minden terv társított egy alkalmazást. Ilyen esetben az alkalmazás összes példányát egyetlen erőforráscsoportként működnek majd tartalmazza. Több csomagokban és több alkalmazás erőforráscsoport rendelkező egyszerűen kezelése, szabályozása és az alkalmazás állapotának megtekintéséhez.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Az App Service-csomag létrehozása vagy meglévő ütemezés használata

Egy új webalkalmazást az App Service létrehozásakor úgy, hogy az alkalmazás be egy meglévő App Service-csomag üzemeltetési erőforrásokat oszthat meg. Annak megállapításához, hogy az új alkalmazás rendelkeznek-e a szükséges erőforrásokat, a kapacitás, a meglévő App Service-csomagot, és a várható terhelést az új alkalmazás tisztában kell. Túlzott az erőforrások lefoglalása is okozhat a meglévő és új alkalmazások állásidő.

Javasoljuk, hogy az alkalmazás egy új App Service-be elkülönítése ütemezésének:

- Alkalmazás erőforrás-igényes áll.
- Az alkalmazás rendelkezik egy meglévő csomagban tárolt más alkalmazásokból különböző méretezési tényező.
- Erőforrás más földrajzi régióban kell alkalmazást.

Ezzel a módszerrel lehet új készletét erőforrásokat lefoglalni az alkalmazás és az alkalmazások nagyobb ellenőrzést.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha az App Service-környezetek, olvassa el [az App Service-csomag létrehozása az App Service-környezetek](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Létrehozhat egy üres App Service-csomag vagy alkalmazás létrehozásának részeként.

A a [Azure-portálon](https://portal.azure.com), kattintson a **új** > **Web + mobil**, majd válassza ki **webalkalmazás** vagy más App Service alkalmazás típusa.

![Alkalmazás létrehozása az Azure portálon.][createWebApp]

Ezután válassza ki vagy hozzon létre az új alkalmazás az App Service-csomag.

 ![Az App Service-csomag létrehozása.][createASP]

Az App Service-csomag létrehozásához kattintson a **[+] hozzon létre új**, típusa a **App Service-csomag** nevet, és válasszon egy megfelelő **hely**. Kattintson a **tarifacsomag**, majd válassza ki a megfelelő tarifacsomagot a szolgáltatáshoz. Válassza ki **összes** több, mint az beállítások árképzési nézetre **szabad** és **megosztott**. Miután kiválasztotta a tarifacsomagot, kattintson a **válasszon** gombra.

## <a name="move-an-app-to-a-different-app-service-plan"></a>Az alkalmazások áthelyezése egy másik App Service-csomag

Az alkalmazások áthelyezése egy másik App Service-csomag a a [Azure-portálon](https://portal.azure.com). Alkalmazások között áthelyezheti tervek mindaddig, amíg a tervek a _ugyanazt az erőforráscsoportot és földrajzi régió_.

Az alkalmazások áthelyezése másik terv:

- Nyissa meg az alkalmazást, amelynek át szeretné helyezni.
- Az a **menü**, keresse meg a **App Service-csomag** szakasz.
- Válassza ki **módosítás App Service-csomag** a folyamat elindításához.

**App Service-csomag módosítása** megnyitja a **App Service-csomag** választó. Ezen a ponton választhatja ki a meglévő séma ezzel az alkalmazással történő áthelyezése. A ugyanazt az erőforráscsoportot és régió csak tervek jelennek meg.

![App Service-csomag választó.][change]

Minden egyes tervnek a saját IP-címek. Például egy webhely áthelyezése egy ingyenes szint a Standard szintű, hogy lehetővé teszi, hogy a szolgáltatások és erőforrások Standard csomagra hozzárendelt összes alkalmazáshoz.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Egy alkalmazás egy másik App Service-csomagra klónozása

Ha azt szeretné, az alkalmazás áthelyezése egy másik régióban, egy alternatív-e az alkalmazás a Klónozás. A Klónozás teszi az alkalmazás másolatát egy új vagy meglévő App Service-csomag bármely régióban.

Található **Klónozott alkalmazás** a a **Fejlesztőeszközök** a menü részét.

> [!IMPORTANT]
> A Klónozás rendelkezik néhány címen olvashat [Azure App Service-alkalmazást a Klónozás](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Az App Service-csomag vertikális

A csomag skálázása három módja van:

- **Módosítsa a csomag csomagazonosítóját tarifacsomag**. Standard, és minden alkalmazás rendelt Standard csomagra szolgáltatásait is használni a csomag az alapszintű rétegben alakíthatók ki.
- **A csomag példányméretének módosítása**. Tegyük fel az alapszintű rétegben, amely használja a kisméretű példányok terv nagy példányok használandó módosítható. Minden alkalmazás társított terv most használhatja a további memória és a CPU-erőforrást, amely nagyobb példányméretének nyújt.
- **Módosítsa a terv példányszám**. Például a Standard tervet készíteni, amely három alkalmazáspéldányra horizontálisan is méretezhető 10 példányok. A prémium tervének kiterjeszthető 20 példányokhoz (rendelkezésre állási) feltételei érvényesek. Minden alkalmazás társított terv most használhatja a további memória és a Processzor-erőforrások kínál a nagyobb a példányok száma.

Az árképzési szint és a példány mérete kattintva módosíthatja a **vertikális Felskálázás** elem alatt az alkalmazás vagy az App Service-csomag beállításai. Változások az App Service-csomag vonatkoznak, és hatással rajta futó összes alkalmazásra.

 ![Méretezést kívánó alkalmazás értékeinek beállításához.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Az alkalmazásszolgáltatási csomag karbantartása

> [!IMPORTANT]
> **App Service-csomagok** , amelyek nem találhatók alkalmazások hozzájuk társított továbbra is függő díj terheli, mivel azok továbbra is számítási kapacitás lefoglalni.

Alapértelmezés szerint is törlődnek a elkerülése érdekében nem várt, ha az utolsó az App Service-csomag tárolva alkalmazást törlik, az eredményül kapott üres App Service-csomag.

## <a name="summary"></a>Összefoglalás

App Service-csomagokról funkciók és kapacitás szempontjából lehet megosztani az alkalmazások csoportját képviselik. App Service-csomagokról rugalmasan, a konkrét alkalmazásokat rendelhet erőforrások olyan készletét, és tovább optimalizálhatja az Azure erőforrás-használat. Ezzel a módszerrel pénzt takaríthatnak meg a tesztelési környezetben, a kívánt is megoszthatja a csomag több alkalmazás között. Több régiók és tervek skálázással azt az éles környezetben is kihasználhatja átviteli sebesség.

## <a name="whats-changed"></a>A változások

- A módosítás a websites szolgáltatásról az App Service-re, váltásról: [Azure App Service és a hatása a meglévő Azure-szolgáltatások](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
