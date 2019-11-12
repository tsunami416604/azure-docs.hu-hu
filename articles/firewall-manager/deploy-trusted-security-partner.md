---
title: Azure Firewall Manager megbízható biztonsági partner üzembe helyezése
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Firewall Manager megbízható biztonságot a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931308"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Megbízható biztonsági partner üzembe helyezése (előzetes verzió)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Azure Firewall Managerben található *megbízható biztonsági partnerek* lehetővé teszik, hogy ismerős, legjobb, harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) kínálja a felhasználók internet-hozzáférésének védelmére.

További információ a támogatott forgatókönyvekről és az ajánlott eljárásokról: [Mik azok a megbízható biztonsági partnerek (előzetes verzió)?](trusted-security-partners.md).

A támogatott biztonsági partnerek a **ZScaler** és a **iboss** az előzetes verzióhoz. A támogatott régiók a következők: WestCentralUS, NorthCentralUS, WestUS, WestUS2 és EastUS.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure Firewall Manager előzetes verzióját explicit módon engedélyezni kell a `Register-AzProviderFeature` PowerShell-paranccsal.

Futtassa a következő parancsokat egy PowerShell-parancssorból:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztrációjának befejezéséhez akár 30 percet is igénybe vehet. A regisztráció állapotának megtekintéséhez futtassa a következő parancsot:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Külső gyártótól származó biztonsági szolgáltató üzembe helyezése új központban

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **Search (keresés**) mezőbe írja be a **Firewall Manager** kifejezést, majd válassza ki a **szolgáltatások**területen.
3. Navigáljon **első lépések**. Válassza **a biztonságos virtuális központ létrehozása**lehetőséget. 
4. Adja meg az előfizetést és az erőforráscsoportot, válasszon ki egy támogatott régiót, és adja hozzá a hub és a virtuális WAN-információkat. 
5. A **VPN-átjáró üzembe helyezése** alapértelmezés szerint engedélyezve van. Egy VPN Gateway szükséges egy megbízható biztonsági partner üzembe helyezéséhez a központban. 
6. Válassza a Next (tovább) lehetőséget **: Azure Firewall**
   > [!NOTE]
   > A megbízható biztonsági partnerek VPN Gateway alagutak használatával csatlakoznak a hubhoz. Ha törli a VPN Gateway, a megbízható biztonsági partnerekkel létesített kapcsolatok elvesznek.
7. Ha Azure Firewall szeretné központilag telepíteni a privát forgalmat, valamint a külső szolgáltatót az internetes forgalom szűrésére, válassza ki a szabályzatot a Azure Firewallhoz. Tekintse meg a [támogatott forgatókönyveket](trusted-security-partners.md#key-scenarios).
8. Ha csak egy külső gyártótól származó biztonsági szolgáltatót szeretne üzembe helyezni a központban, válassza a **Azure Firewall: engedélyezve/letiltva** lehetőséget a **Letiltva**beállításhoz. 
9. Válassza a Next (tovább) lehetőséget **: megbízható biztonsági partnerek**.
10. Válassza ki a **megbízható biztonsági partner** beállítást az **engedélyezéshez**. Válasszon partnert. 
11. Kattintson a **Tovább** gombra. 
12. Tekintse át a tartalmat, majd válassza a **Létrehozás**lehetőséget.

A VPN-átjáró üzembe helyezése több mint 30 percet is igénybe vehet.

Annak ellenőrzéséhez, hogy létrejött-e a hub, navigáljon Azure Firewall Manager-> biztonságos hubokhoz. Válassza ki a hub-> áttekintő lapot a partner nevének és az állapotnak a **függőben lévő biztonsági kapcsolatként**való megjelenítéséhez.

Miután létrejött a hub, és a biztonsági partner be van állítva, folytassa a szolgáltatást a biztonsági szolgáltató hubhoz való összekapcsolásához.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Külső gyártótól származó biztonsági szolgáltató üzembe helyezése meglévő központban

Kiválaszthat egy meglévő hubot egy virtuális WAN-ban, és átalakíthatja azt egy *biztonságos virtuális hubhoz*.

1. A **első lépések**területen válassza a **meglévő hubok konvertálása**lehetőséget.
2. Válasszon egy előfizetést és egy meglévő hubot. A külső szolgáltatók új központban való üzembe helyezéséhez kövesse a további lépéseket.

Ne feledje, hogy a VPN-átjárót központilag kell telepíteni ahhoz, hogy egy meglévő hubot a külső szolgáltatóktól származó biztonságos hubhoz lehessen alakítani.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Külső gyártótól származó biztonsági szolgáltatók konfigurálása biztonságos hubhoz való kapcsolódáshoz

A virtuális központ VPN Gatewayhoz tartozó alagutak beállításához a harmadik féltől származó szolgáltatók hozzáférési jogokkal kell rendelkezniük a központhoz. Ehhez társítson egy egyszerű szolgáltatást az előfizetéséhez vagy az erőforráscsoporthoz, és adja meg a hozzáférési jogosultságokat. Ezeket a hitelesítő adatokat a portálon keresztül kell megadnia a harmadik féltől.

1. Azure Active Directory (AD) egyszerű szolgáltatás létrehozása: kihagyhatja az átirányítási URL-címet. 

   [Útmutató: a portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adja meg az egyszerű szolgáltatásnév hozzáférési jogosultságait és hatókörét.
   [Útmutató: a portál használatával létrehozhat egy Azure AD-alkalmazást és egy egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > A részletesebb szabályozás érdekében korlátozhatja a hozzáférést csak az erőforráscsoporthoz.
3. Kövesse a [ZScaler: Microsoft Azure virtuális WAN-integrációs utasítások konfigurálása](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) a következőhöz:

   - Jelentkezzen be a partner portálra, és adja meg a hitelesítő adatait, és adja meg a megbízható partner hozzáférését a biztonságos hubhoz.
   - Szinkronizálja a virtuális hubokat a partner portálon, és állítsa be az alagutat a virtuális hubhoz. Ezt az Azure AD-hitelesítési hitelesítő adatok érvényesítése után teheti meg.
   
4. Az Azure-beli Azure Virtual WAN-portálon megtekintheti az alagút létrehozási állapotát. Miután az alagutak az Azure-ban és a partner portálon **is megjelennek, folytassa a következő** lépésekkel, hogy beállítsa az útvonalakat annak kiválasztásához, hogy mely ágakat és virtuális hálózatok kell elküldeni az internetes forgalomnak a partnernek.

## <a name="configure-route-settings"></a>Útvonal-beállítások konfigurálása

1. Keresse meg a Azure Firewall Manager-> biztonságos hubokat. 
2. Válasszon egy hubot. A hub állapotának most már a **függőben lévő biztonsági kapcsolat**helyett **kiépítve** kell lennie.

   Győződjön meg arról, hogy a külső szolgáltató tud csatlakozni az elosztóhoz. A VPN-átjárón lévő alagutaknak **csatlakoztatott** állapotban kell lenniük. Ez az állapot jobban tükrözi a hub és a külső partner közötti kapcsolat állapotát az előző állapothoz képest.
3. Válassza ki a hubot, és navigáljon az **útvonal beállításai**elemre.

   Ha harmadik féltől származó szolgáltatót helyez üzembe a központba, az a hubot egy *biztonságos virtuális hubhoz*alakítja át. Ez biztosítja, hogy a harmadik féltől származó szolgáltató egy 0.0.0.0/0 (alapértelmezett) útvonalat Hirdessen a hubhoz. Az VNet-kapcsolatok és a központhoz csatlakoztatott helyek azonban nem kapják meg ezt az útvonalat, kivéve, ha nem engedélyezte, hogy mely kapcsolatokhoz kell ezt az alapértelmezett útvonalat választania.
4. Az **internetes forgalom**területen válassza a **VNet** vagy az **ág – Internet** lehetőséget, vagy mindkettőt, hogy az útvonalak konfigurálva legyenek a harmadik féltől.

   Ez csak azt jelzi, hogy milyen típusú forgalmat kell átirányítani a központba, de nem befolyásolja az útvonalakat a virtuális hálózatok vagy ágakon. Ezeket az útvonalakat a rendszer alapértelmezés szerint nem továbbítja a hubhoz csatolt összes virtuális hálózatok/ág számára.
5. Ki kell választania a **biztonságos kapcsolatokat** , és ki kell választania azokat a kapcsolatokat, amelyeken ezeket az útvonalakat be kell állítani. Ez azt jelzi, hogy mely virtuális hálózatok/ágak indíthatják el az internetes forgalmat a külső szolgáltató felé.
6. Az **útvonal beállításai**területen válassza a **biztonságos kapcsolatok** az internetes forgalom alatt lehetőséget, majd válassza ki a VNet vagy az ágakat (virtuális WAN-*helyeket* ) a biztonság érdekében. Válassza a **biztonságos internetes forgalom**lehetőséget.
   ![biztonságos internetes forgalom](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Váltson vissza a hubok lapra. A hub **megbízható biztonsági partnerének** állapota most már **védett**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Harmadik féltől származó szolgáltatással történő internetes forgalom ág-vagy VNet

Ezt követően ellenőrizheti, hogy a VNet virtuális gépek vagy a fiókirodák hozzáférhetnek-e az internethez, és ellenőrizhetik, hogy a forgalom a külső szolgáltatás felé áramlik-e.

Az útvonal-beállítás lépéseinek befejezése után a VNet virtuális gépek és a fiókirodák a 0/0-as vagy harmadik féltől származó szolgáltatási útvonalra kerülnek. Ezeket a virtuális gépeket nem lehet RDP-vagy SSH-kapcsolat. A bejelentkezéshez az [Azure Bastion](../bastion/bastion-overview.md) szolgáltatást egy társ VNet helyezheti üzembe.

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Manager előzetes verziójával a Azure Portal használatával](secure-cloud-network.md)




