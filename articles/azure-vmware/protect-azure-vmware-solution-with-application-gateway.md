---
title: Az Azure Application Gateway használatával biztosíthatja webalkalmazásait az Azure VMware-megoldásban
description: Az Azure Application Gateway konfigurálása az Azure VMware-megoldáson futó webalkalmazások biztonságos megjelenítéséhez.
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: ad7cd36d77da41d75ea9dcc18a51d0ffc5540d2a
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579816"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Az Azure Application Gateway használatával biztosíthatja webalkalmazásait az Azure VMware-megoldásban

Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) egy 7. rétegbeli webes forgalmi terheléselosztó, amely lehetővé teszi a webes alkalmazások forgalmának kezelését. Számos képességet kínál: a cookie-alapú munkamenet-affinitást, az URL-alapú útválasztást és a webalkalmazási tűzfalat (WAF), hogy néhányat említsünk. (A szolgáltatások teljes listájáért lásd: az [Azure Application Gateway funkciói](../application-gateway/features.md).) A szolgáltatás két verzióban, a v1-ben és a v2-ben is elérhető. Mindkettő tesztelték az Azure VMware-megoldáson futó webalkalmazásokkal.

Ebben a cikkben egy olyan gyakori forgatókönyvet ismertetünk, amely a webkiszolgáló-farmok előtt Application Gatewayt használ egy olyan konfigurációval és javaslatokkal, amelyekkel az Azure VMware-megoldáson futó webalkalmazások védhetők. 

## <a name="topology"></a>Topológia
Ahogy az az alábbi ábrán is látható, Application Gateway használható az Azure IaaS Virtual Machines, az Azure Virtual Machine Scale sets vagy a helyszíni kiszolgálók elleni védelemhez. Az Azure VMware-megoldás virtuális gépei Application Gateway alapján lesznek kezelve helyszíni kiszolgálóként.

![Application Gateway védi az Azure VMware-megoldás virtuális gépeket.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> Az Azure Application Gateway jelenleg az egyetlen támogatott módszer az Azure VMware megoldású virtuális gépeken futó webalkalmazások elérhetővé tétele érdekében.

Az alábbi ábrán az Azure VMware Solution web Applications használatával végzett Application Gateway ellenőrzéséhez használt tesztelési forgatókönyv látható.

![Application Gateway integráció a Web Apps szolgáltatást futtató Azure VMware-megoldással.](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-avs-scenario.png)

A Application Gateway példány egy dedikált alhálózaton van üzembe helyezve a központban. Azure-beli nyilvános IP-címmel rendelkezik; ajánlott a virtuális hálózat szabványos DDoS-védelmének aktiválása. A webkiszolgáló a NSX T0 és T1 útválasztók mögötti Azure VMware-megoldásban található. Az Azure VMware-megoldás [ExpressRoute Global REACH](../expressroute/expressroute-global-reach.md) használ az elosztóval és a helyszíni rendszerekkel való kommunikáció engedélyezésére.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók.
- Azure VMware-megoldás saját felhő üzembe helyezése és futtatása.

## <a name="deployment-and-configuration"></a>Üzembe helyezés és konfigurálás

1. A Azure Portal keresse meg **Application Gateway** és válassza az **Application Gateway létrehozása**lehetőséget.

2. Adja meg az alapszintű részleteket az alábbi ábrán látható módon: Ezután válassza a **Tovább: frontendek>**. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Application Gateway létrehozása":::

3. Válassza ki az előtérbeli IP-cím típusát. Nyilvános esetén válasszon egy meglévő nyilvános IP-címet, vagy hozzon létre egy újat. Kattintson a **Next (tovább) gombra:>háttérrendszer **.

    > [!NOTE]
    > Privát frontendek esetében csak a standard és a Web Application Firewall (WAF) SKU-ket támogatja a rendszer.

4. Ezután vegyen fel egy háttér-készletet, amely az alkalmazás vagy szolgáltatás részét képező példányokat ismerteti (ebben az esetben az Azure VMware megoldás-infrastruktúrán futó virtuális gépeket). Adja meg az Azure VMware-megoldás saját felhőben futó webkiszolgálók részleteit, és válassza a **Hozzáadás**lehetőséget. Ezután válassza a **következő: Configuration>**.

1. A **konfiguráció** lapon válassza az **útválasztási szabály hozzáadása**elemet.

6. A **figyelő** lapon adja meg a figyelő részleteit. Ha a HTTPS lehetőséget választja, meg kell adni egy tanúsítványt, vagy egy PFX-fájlból vagy egy meglévő, Azure Key Vaultból származó tanúsítványból. 

7. Válassza a **háttérbeli célok** fület, és válassza ki a korábban létrehozott háttér-készletet. A **http-beállítások** mezőben válassza az **új hozzáadása**elemet.

8. Konfigurálja a HTTP-beállítások paramétereit. Válassza a **Hozzáadás** lehetőséget.

9. Ha elérésiút-alapú szabályokat szeretne konfigurálni, válassza **a több cél hozzáadása lehetőséget egy elérésiút-alapú szabály létrehozásához**. 

10. Adjon hozzá egy elérésiút-alapú szabályt, és válassza a **Hozzáadás**lehetőséget. További elérésiút-alapú szabályok hozzáadásához ismételje meg a műveletet. 

11. Ha befejezte az elérésiút-alapú szabályok hozzáadását, válassza a **Hozzáadás** újra lehetőséget. Ezután válassza a **Tovább: címkék>** elemet. 

12. Adja hozzá a kívánt címkéket. Válassza a **Tovább: Áttekintés +>létrehozása **elemet.

13. Az érvényesítés a Application Gatewayon fog futni; Ha a művelet sikeres, válassza a **Létrehozás** elemet a telepítéshez.

## <a name="configuration-examples"></a>Konfigurációs példák

Ebből a szakaszból megtudhatja, hogyan konfigurálhatja a Application Gateway az Azure VMware megoldás virtuális gépei használatával a következő felhasználási esetekben: 

- [Több webhely üzemeltetése](#hosting-multiple-sites)
- [Útválasztás URL-cím szerint](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Több webhely üzemeltetése

Az alkalmazás-átjáró létrehozásakor a Azure Portal használatával több webhely üzemeltetését is konfigurálhatja. Ebben az oktatóanyagban a háttérbeli címkészlet meghatározása egy Azure VMware-megoldásban futó virtuális gépek használatával egy meglévő Application Gateway-en. Az Application Gateway egy hub virtuális hálózat része, amely az [Azure VMware-megoldás integrálása egy központba és küllős architektúrába](concepts-hub-and-spoke.md)című részben leírtak szerint. Ez az oktatóanyag feltételezi, hogy a saját több tartománya van, és példákat használ a www.contoso.com és a www.fabrikam.com.

1. Hozza létre a virtuális gépeket. Az Azure VMware-megoldás saját felhőben hozzon létre két különböző készletet a virtuális gépeken; az egyik a Contosot és a második Fabrikam-t jelöli. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs.png" alt-text="Application Gateway létrehozása":::

    Ennek az oktatóanyagnak a szemléltetéséhez a Windows Server 2016-et telepítette Internet Information Services (IIS) szerepkörrel. Miután telepítette a virtuális gépeket, futtassa az alábbi PowerShell-parancsokat az IIS konfigurálásához az egyes virtuális gépeken. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Adja hozzá a háttér-készleteket. Egy meglévő Application Gateway-példányban válassza ki a bal oldali menüben a **háttér-készletek** elemet, válassza a  **Hozzáadás**lehetőséget, majd adja meg az új készletek részleteit. A jobb oldali ablaktáblán válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png" alt-text="Application Gateway létrehozása" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-02.png":::

3. A **figyelők** szakaszban hozzon létre egy új figyelőt az egyes webhelyekhez. Adja meg az egyes figyelők részleteit, és válassza a **Hozzáadás**lehetőséget.

4. A bal oldali navigációs sávon válassza a **http-beállítások** elemet, és válassza a **Hozzáadás** lehetőséget a bal oldali ablaktáblán. A részletek kitöltésével hozzon létre egy új HTTP-beállítást, és kattintson a **Mentés**gombra.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png" alt-text="Application Gateway létrehozása" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-03.png":::

5. Hozza létre a szabályokat a bal oldali menü **szabályok** szakaszában. Társítsa az egyes szabályokat a megfelelő figyelőhöz. Válassza a **Hozzáadás** lehetőséget.

6. Konfigurálja a megfelelő háttér-készletet és a HTTP-beállításokat. Válassza a **Hozzáadás** lehetőséget.

7. A kapcsolatok tesztelése. Nyissa meg az előnyben részesített böngészőt, és navigáljon az Azure VMware-megoldási környezetében üzemeltetett különböző webhelyekre, például: http://www.fabrikam.com .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-avs-07.png" alt-text="Application Gateway létrehozása":::

### <a name="routing-by-url"></a>Útválasztás URL-cím szerint

Az Azure Application Gateway az URL-elérésiút-alapú útválasztási szabályok konfigurálására használható. Ebben az oktatóanyagban a háttérbeli címkészlet meghatározása egy Azure VMware-megoldásban futó virtuális gépek használatával egy meglévő Application Gateway-en. Az Application Gateway egy hub virtuális hálózat része, az Azure [VMware megoldás Azure natív integrációs dokumentációjában](concepts-hub-and-spoke.md)leírtak szerint. Ezután olyan útválasztási szabályokat hozhat létre, amelyek gondoskodnak arról, hogy a webes forgalom a készletekben lévő megfelelő kiszolgálókon érkezzen.

1. Hozza létre a virtuális gépeket. Az Azure VMware-megoldás saját felhőben hozzon létre egy virtuális gépek készletét, amely a webfarmt jelképezi. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs.png" alt-text="Application Gateway létrehozása"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Adja hozzá a háttér-készleteket. Három új háttér-készletet kell hozzáadnia egy meglévő Application Gateway-példányban. Válassza ki a **háttér-készletek** elemet a bal oldali menüben. Válassza a **Hozzáadás** lehetőséget, és adja meg a **contoso-web**első készletének részleteit. Adjon hozzá egy virtuális gépet célként. Válassza a **Hozzáadás** lehetőséget. Ismételje meg ezt a folyamatot a **contoso-images** és a **contoso-video**esetében, és adjon hozzá egy egyedi virtuális gépet az egyes célhoz. 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Application Gateway létrehozása" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-02.png":::

3. A **figyelők** szakaszban hozzon létre egy alapszintű új figyelőt az 8080-es port használatával.

4. A bal oldali navigációs sávon válassza a **http-beállítások** elemet, és válassza a **Hozzáadás** lehetőséget a bal oldali ablaktáblán. A részletek kitöltésével hozzon létre egy új HTTP-beállítást, és kattintson a **Mentés**gombra.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Application Gateway létrehozása":::

5. Hozza létre a szabályokat a bal oldali menü **szabályok** szakaszában. Társítsa az egyes szabályokat a korábban létrehozott figyelőhöz. Ezután konfigurálja a fő háttér-készletet és a HTTP-beállításokat. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Application Gateway létrehozása":::

6. Tesztelje a konfigurációt. Nyissa meg az Application Gatewayt a Azure Portalon, és az **Áttekintés** szakaszban másolja a nyilvános IP-címet. Ezután nyisson meg egy új böngészőablakot, és írja be az URL-címet `http://<app-gw-ip-address>:8080` . 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Application Gateway létrehozása":::

    Módosítsa az URL-címet a következőre: `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Application Gateway létrehozása":::

    Módosítsa újra az URL-címet a következőre: `http://<app-gw-ip-address>:8080/video/test.htm` .

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Application Gateway létrehozása":::

## <a name="next-steps"></a>Következő lépések

További konfigurációs példákért tekintse át az [Azure Application Gateway dokumentációját](https://docs.microsoft.com/azure/application-gateway/) .
