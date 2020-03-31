---
title: DNS-nevek konfigurálása a Traffic Manager rel
description: Ismerje meg, hogyan konfigurálhat egyéni tartományt egy Azure App Service-alkalmazáshoz, amely integrálható a Traffic Managerrel a terheléselosztáshoz.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944120"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Egyéni tartománynév konfigurálása az Azure App Service szolgáltatásban a Traffic Manager-integrációval

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> A Felhőszolgáltatásokról az [Azure-felhőszolgáltatás egyéni tartománynevének konfigurálása](../cloud-services/cloud-services-custom-domain-name.md)című témakörben található.

Amikor [az Azure Traffic Manager](/azure/traffic-manager/) használatával tölti be az Azure App Service-be irányuló forgalom terheléselosztási forgalmat, az App Service alkalmazás a ** \<traffic-manager-endpoint>.trafficmanager.net**használatával érhető el. [Azure App Service](overview.md) Egyéni tartománynevet , például www\.contoso.com rendelhet hozzá az App Service alkalmazáshoz, hogy felismerhetőbb tartománynevet biztosítson a felhasználók számára.

Ez a cikk bemutatja, hogyan konfigurálhat egyéni tartománynevet a [Traffic Manager](../traffic-manager/traffic-manager-overview.md)programmal integrált App Service-alkalmazással.

> [!NOTE]
> Csak [a CNAME](https://en.wikipedia.org/wiki/CNAME_record) rekordok támogatottak, ha a Traffic Manager-végpont használatával konfigurál tartománynevet. Mivel az A rekordok nem támogatottak, a gyökértartomány-hozzárendelés, például a contoso.com szintén nem támogatott.
> 

## <a name="prepare-the-app"></a>Az alkalmazás előkészítése

Egyéni DNS-név hozzárendelése egy olyan alkalmazáshoz, amely integrálva van az Azure Traffic Managerrel, a webalkalmazás [App Service-csomagjának](https://azure.microsoft.com/pricing/details/app-service/) **standard** szintű vagy magasabb szintűnek kell lennie. Ebben a lépésben ellenőrzi, hogy az App Service-alkalmazás a támogatott tarifacsomagban van-e.

### <a name="check-the-pricing-tier"></a>A tarifacsomag ellenőrzése

Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki **az App Services**lehetőséget.

Az **App Services** lapon válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-custom-domain/select-app.png)

Az alkalmazáslap bal oldali navigációs sávján válassza a **Felskálázás (App Service-csomag) lehetőséget.**

![Vertikális felskálázás menü](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Az alkalmazás jelenlegi tarifacsomagja kék szegéllyel van kiemelve. Ellenőrizze, hogy az alkalmazás **standard** szintű vagy annál magasabb szintű-e van-e (az **Éles** vagy **az Elkülönített** kategória bármely szintje). Ha igen, zárja be a **Felskálázás** lapot, és ugorjon [a CNAME leképezés létrehozása gombra.](#create-the-cname-mapping)

![A tarifacsomag ellenőrzése](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Az App Service-csomag vertikális felskálázása

Ha fel kell skáláznia az alkalmazást, válassza ki bármelyik tarifacsomagot a **Termelés** kategóriában. További beállításokhoz kattintson a **További lehetőségek megjelenítése** lehetőségre.

Kattintson az **Alkalmaz** gombra.

## <a name="create-traffic-manager-endpoint"></a>Traffic Manager-végpont létrehozása

A [Végpontok hozzáadása vagy törlése](../traffic-manager/traffic-manager-endpoints.md)című lépés lépéseit követve adja hozzá az App Service-alkalmazást végpontként a Traffic Manager-profilhoz.

Miután az App Service-alkalmazás egy támogatott tarifacsomagban van, megjelenik az elérhető App Service-célok listájában a végpont hozzáadásakor. Ha az alkalmazás nem szerepel a listában, [ellenőrizze az alkalmazás tarifacsomagját.](#prepare-the-app)

## <a name="create-the-cname-mapping"></a>A CNAME leképezés létrehozása
> [!NOTE]
> A [megvásárolt App Service-tartomány](manage-custom-dns-buy-domain.md)konfigurálásához hagyja ki ezt a szakaszt, és nyissa meg az [Egyéni tartomány engedélyezése](#enable-custom-domain)lehetőséget.
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Bár az egyes tartományszolgáltatók sajátosságai eltérőek lehetnek, az egyéni *tartománynévtől* (például **a contoso.com)** az alkalmazásba integrált Traffic Manager tartománynévre **(contoso.trafficmanager.net)** kell leképeznie. *to*

> [!NOTE]
> Ha egy bejegyzés már használatban van, és az alkalmazásokat megelőzően hozzá kell kötnie, létrehozhat egy további CNAME rekordot. Ha például megelőzőjelleggel szeretné a **\.www contoso.com** az alkalmazáshoz kötni, hozzon létre egy CNAME rekordot az **awverify.www** webhelyről **contoso.trafficmanager.net.** Ezután hozzáadhatja\.a "www contoso.com" szót az alkalmazáshoz anélkül, hogy módosítania kellene a "www" CNAME rekordot. További információt az [Aktív DNS-név áttelepítése az Azure App Service szolgáltatásba című témakörben talál.](manage-custom-dns-migrate-domain.md)

Miután befejezte a DNS-rekordok hozzáadását vagy módosítását a tartományi szolgáltatónál, mentse a módosításokat.

## <a name="enable-custom-domain"></a>Egyéni tartomány engedélyezése
A tartománynév rekordjainak propagálása után a böngésző segítségével ellenőrizze, hogy az egyéni tartománynév feloldódik-e az App Service-alkalmazásra.

> [!NOTE]
> Eltarthat egy ideig, amíg a CNAME propagál a DNS-rendszeren keresztül. A CNAME szolgáltatás <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> segítségével ellenőrizheti, hogy a CNAME elérhető-e.
> 
> 

1. Ha a tartományfeloldás sikeres lesz, vissza kell lépnie az alkalmazásoldalára az [Azure Portalon](https://portal.azure.com)
2. A bal oldali navigációs sávon válassza az **Egyéni tartományok** > **állomásnév hozzáadása**lehetőséget.
4. Írja be a korábban leképezett egyéni tartománynevet, és válassza **az Érvényesítés lehetőséget.**
5. Győződjön meg arról, hogy a **Hostname rekordtípus** **\.CNAME (www example.com vagy bármely altartomány)** értékre van állítva.

6. Mivel az App Service-alkalmazás most már integrálva van egy Traffic Manager-végpontdal, a Traffic Manager tartománynevet a CNAME konfiguráció alatt kell **látnia.** Jelölje ki, és kattintson **az Egyéni tartomány hozzáadása**gombra.

    ![DNS-név hozzáadása az alkalmazáshoz](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni DNS-név védelme SSL-kötéssel az Azure App Service-ben](configure-ssl-bindings.md)
