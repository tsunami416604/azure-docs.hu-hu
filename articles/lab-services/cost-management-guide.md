---
title: Cost Management útmutató a Azure Lab Services
description: Ismerje meg a labor-szolgáltatások költségeinek megtekintésére szolgáló különböző módszereket.
services: lab-services
author: rbest
ms.author: rbest
ms.date: 05/09/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: b71ccd55407f24fe672bc3a977a91bf1315e831f
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897375"
---
# <a name="cost-management-for-azure-lab-services"></a>Cost Management Azure Lab Services

A Cost Management két különböző területre bontható: költségbecslés és Cost Analysis.  A tesztkörnyezet beállításakor a rendszer megbecsüli a számítást, hogy a labor kezdeti szerkezete a várt Költségvetésen belül is elfér-e.  A költségek elemzése általában a hónap végén történik, hogy elemezze a költségeket, és határozza meg a szükséges műveleteket a következő hónapban.

## <a name="estimating-the-lab-costs"></a>A labor költségeinek becslése

Az egyes labor-irányítópultok **díja & számlázási** szakasz, amely egy durva becslést ad arra vonatkozóan, hogy a labor Milyen költségekkel jár a hónapban.  A költségbecslés összefoglalja az órás használatot a felhasználók maximális számával, a becsült költségek óránként.  Ahhoz, hogy a legpontosabb becslést állítsa be a laborba, beleértve az [ütemtervet](how-to-create-schedules.md), és az irányítópulton a becsült költségek is megjelennek.  

Ez a becslés nem lehet az összes lehetséges költség, néhány erőforrást nem tartalmaz.  A sablon-előkészítési költségek nem számítanak bele a költségbecslésba.  A sablon létrehozásához szükséges idő mennyisége jelentősen eltérhet. A sablon futtatásának díja megegyezik a labor óránkénti teljes díjával. A [megosztott](how-to-use-shared-image-gallery.md) képkatalógus költségei nem szerepelnek a labor irányítópultján, mivel a katalógus több labor között is megosztható.  Végül, ha a tesztkörnyezet létrehozója elindítja a gépet, a rendszer kizárja a becslésből származó órákat.

> [!div class="mx-imgBorder"]
> ![Irányítópult-költségbecslés](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Korábbi hónapok használatának elemzése

A Cost Analysis a korábbi hónapok használatának áttekintésére szolgál a laborban végzett módosítások meghatározásához.  A múltbeli költségek lebontása az [előfizetés költségeinek elemzésében](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)található.  A Azure Portal írja be az "előfizetések" kifejezést a felső Keresés mezőbe, majd válassza az előfizetések lehetőséget.  

> [!div class="mx-imgBorder"]
> ![Előfizetés keresése](./media/cost-management-guide/subscription-search.png)

Válassza ki az áttekinteni kívánt előfizetést.

> [!div class="mx-imgBorder"]
> ![Előfizetés kiválasztása](./media/cost-management-guide/subscription-select.png)

 Válassza a "Cost Analysis" lehetőséget a **Cost Management**alatti bal oldali ablaktáblán.

 > [!div class="mx-imgBorder"]
> ![Előfizetés költséghatékonyságának elemzése](./media/cost-management-guide/subscription-cost-analysis.png)

Ez az irányítópult lehetővé teszi a részletes költségadatok használatát, beleértve a különböző fájltípusok ütemezett exportálásának lehetőségét is.  A Cost Management számos képességgel rendelkezik további információért lásd: [Cost Management számlázási áttekintés](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Az erőforrástípus szerinti szűrés: `microsoft.labservices/labaccounts` csak a labor szolgáltatásokhoz kapcsolódó költségeket jeleníti meg.

## <a name="understanding-the-usage"></a>A használat megismerése

Alább látható egy példa a Cost Analysis szolgáltatásra.

> [!div class="mx-imgBorder"]
> ![Előfizetés költséghatékonyságának elemzése](./media/cost-management-guide/cost-analysis.png)

Alapértelmezés szerint hat oszlop van: erőforrás, erőforrás típusa, hely, erőforráscsoport neve, címkék, Cost.  Az erőforrás oszlop tartalmazza a labor-fiók, a labor neve és a virtuális gép adatait.  A labor Account/Lab nevű sorok/alapértelmezett érték a labor díja, amely a második és harmadik sorban is látható.  A használt virtuális gépek a labor fiók/labor neve/alapértelmezett/virtuális gép neve alatt lesznek felhasználva.  Ebben a példában az első sor második sorral való hozzáadásakor a "aaalab/dockerlab" kifejezéssel kezdődően a "dockerlab" labor teljes költsége jelenik meg a "aaalab" Lab-fiókban.

A megosztott képkatalógus adatainak beszerzéséhez módosítsa az erőforrás típusát a értékre `Microsoft.Compute/Galleries` , amely megadja a képkatalógus teljes költségeit.  Előfordulhat, hogy a megosztási képtárak nem jelennek meg a katalógus tárolási helyétől függően.

> [!NOTE]
> A megosztott képkatalógus a labor-fiókhoz van csatlakoztatva.  Ez azt jelenti, hogy több labor is használhatja ugyanazt a rendszerképet.

## <a name="separating-costs"></a>Költségek elválasztása

Egyes egyetemek a labor-fiókot és az erőforráscsoportot használták a különböző osztályok elkülönítésének módjaként.  Minden osztály saját labor-fiókkal és erőforráscsoporthoz fog rendelkezni. A Cost Analysis (költség elemzése) ablaktáblán adjon hozzá egy szűrőt az erőforráscsoport neve alapján az osztály megfelelő erőforráscsoport-nevével, és csak az adott osztály költségei lesznek láthatók.  Ez lehetővé teszi a különböző osztályok közötti világosabb leválasztást a költségek megtekintésekor.  A Cost Analysis [ütemezett exportálási](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) funkciója lehetővé teszi, hogy az egyes osztályok díjait külön fájlba töltse le.

## <a name="managing-costs"></a>Költségek kezelése

Az osztály típusától függően lehetőség van a költségek kezelésére, hogy csökkentse a virtuális gépek futtatását tanulók nélkül a gépet használva.

### <a name="auto-shutdown-on-disconnect"></a>Automatikus leállítás a kapcsolat bontásakor

A labor létrehozásakor a labor tulajdonosa a laborban állíthatja be a virtuális gépeket úgy, hogy [leállítsa a kapcsolatot, ha a virtuális gép RDP-kapcsolata le van választva](how-to-enable-shutdown-disconnect.md).  Ez a beállítás csökkenti azt a forgatókönyvet, amelyben a tanuló leválasztja a kapcsolatot, de elfelejti a virtuális gép leállítását.

### <a name="quota-vs-scheduled-time"></a>Kvóta vs ütemezett idő

A [kvóta](classroom-labs-concepts.md#quota) és az [ütemezett idő](classroom-labs-concepts.md#schedules) megismerése lehetővé teszi, hogy a labor tulajdonosa konfigurálja a labort, hogy jobban illeszkedjen a professzor és a tanulók igényeihez.  Az ütemezett időpont egy olyan beállított idő, amelyben az összes tanuló virtuális gép elindult, és elérhetők a csatlakozáshoz.  Általában ütemezve van használatban abban az esetben, ha az összes tanuló saját virtuális gépe lesz, és a professzor utasításait a nap folyamán, például órákban követik.  A hátránya, hogy az összes tanuló virtuális gép elindult, és a költségek merülnek fel, még akkor is, ha a tanuló nem jelentkezik be a virtuális gépre.  A kvóta ideje az egyes tanulók számára lefoglalt idő, amelyet saját belátása szerint használhatnak, és gyakran használják a független tanulók számára. A virtuális gépek nem indulnak el, amíg a tanuló el nem indítja a virtuális gépet.  

A labor a kvóta idejét, az ütemezett időt vagy a kettő kombinációját is használhatja. Ha egy osztálynak nincs szüksége az ütemezett időpontra, akkor csak a virtuális gépek leghatékonyabb használatára vonatkozó kvóta-időt használja.

### <a name="scheduled-event---stop-only"></a>Ütemezett esemény – csak leállítás

Az ütemtervben hozzáadhat egy leállítási esemény típusát, amely egy adott időpontban leállítja az összes gépet.  Egyes laborok tulajdonosai minden nap éjfélkor állítottak be egyetlen eseményt, hogy csökkentsék a költségeket és a kvóták használatát, amikor egy tanuló elfelejti leállítani a használt virtuális gépet.  Az ilyen típusú események hátránya, hogy az összes virtuális gép le lesz állítva, akkor is, ha a tanuló a virtuális gépet használja.

### <a name="other-costs-related-to-labs"></a>A laborokkal kapcsolatos egyéb költségek 

Vannak olyan költségek, amelyek nem a labor szolgáltatásba kerülnek, de a labor szolgáltatáshoz is köthetők.  A megosztott képkatalógusok csatlakozhatnak a laborokhoz, de nem jelennek meg a labor Services költségei között, és költségekkel rendelkeznek.  Az általános költségek csökkentése érdekében távolítsa el a katalógusban fel nem használt képeket, mivel a lemezképek öröklik a tárolási költségeket.  A laborok más Azure-erőforrásokhoz is csatlakozhatnak egy virtuális hálózaton (VNet), ha eltávolítanak egy Lab-t, el kell távolítania a VNet és a többi erőforrást.

## <a name="conclusion"></a>Összegzés

A fenti információk remélhetőleg jobban megértették a használati költségeket, és azt, hogy az eszközök hogyan használhatók a felesleges költségek csökkentése érdekében.
