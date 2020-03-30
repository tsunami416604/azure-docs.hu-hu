---
title: Az Azure Firewall Manager megbízható biztonsági partnerének telepítése
description: Ismerje meg, hogyan telepíthet egy Azure Firewall Manager megbízható biztonságot az Azure Portalon.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931308"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Megbízható biztonsági partner üzembe helyezése (előzetes verzió)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Az* Azure Firewall Manager megbízható biztonsági partnerei lehetővé teszik, hogy az ismerős, legjobb formában lévő, harmadik féltől származó, szolgáltatásként (SECaaS) származó biztonsági ajánlatait használja a felhasználók internet-hozzáférésének védelme érdekében.

Ha többet szeretne megtudni a támogatott forgatókönyvekről és az ajánlott eljárásokra vonatkozó irányelvekről, olvassa el [a Mik azok a megbízható biztonsági partnerek (előzetes verzió).](trusted-security-partners.md)

A támogatott biztonsági partnerek **zscaler** és **iboss** ehhez az előzetes verzióhoz. A támogatott régiók a WestCentralUS, a NorthCentralUS, a WestUS, a WestUS2 és az EastUS.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az Azure Firewall Manager előzetes verzióját `Register-AzProviderFeature` explicit módon engedélyezni kell a PowerShell paranccsal.

A PowerShell parancssorából futtassa a következő parancsokat:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
A szolgáltatás regisztrációjának befejezése akár 30 percet is igénybe vesz. Futtassa a következő parancsot a regisztrációs állapot ellenőrzéséhez:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Külső biztonsági szolgáltató telepítése új hubon

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. A **Keresés mezőbe**írja be a Firewall **Manager** parancsot, és válassza ki a **Szolgáltatások csoportban.**
3. Keresse meg az **Első lépések ( Első lépések ) ( Első lépések**) ( Első lépések ) Válassza **a Biztonságos virtuális elosztó létrehozása**lehetőséget. 
4. Adja meg az előfizetést és az erőforráscsoportot, válasszon ki egy támogatott régiót, és adja hozzá a hub és a virtuális WAN-információkat. 
5. **A VPN-átjáró telepítése** alapértelmezés szerint engedélyezve van. A MEGBÍZHATÓ biztonsági partner központi telepítéséhez VPN-átjáró szükséges. 
6. Válassza a **Következő lehetőséget: Azure tűzfal**
   > [!NOTE]
   > A megbízható biztonsági partnerek VPN-átjáróalagutak kal csatlakoznak a hubhoz. Ha törli a VPN-átjárót, a megbízható biztonsági partnerekkel létesített kapcsolatok elvesznek.
7. Ha azt szeretné, hogy az Azure Firewall a magánforgalom szűrésére külső szolgáltatóval együtt az internetes forgalom szűréséhez, válasszon egy szabályzatot az Azure Firewall számára. Tekintse meg a [támogatott forgatókönyveket.](trusted-security-partners.md#key-scenarios)
8. Ha csak egy külső biztonsági szolgáltatót szeretne telepíteni a hubon, válassza az **Azure Tűzfal: Engedélyezve/letiltva** lehetőséget a **Letiltva**beállításhoz. 
9. Válassza a **Tovább: Megbízható biztonsági partnerek**lehetőséget.
10. Válassza **a Megbízható biztonsági partner** lehetőséget, ha engedélyezni **szeretné.** Válasszon partnert. 
11. Válassza a **Tovább lehetőséget.** 
12. Tekintse át a tartalmat, és válassza a **Létrehozás lehetőséget.**

A VPN-átjáró telepítése több mint 30 percet is igénybe vehet.

Győződjön meg arról, hogy a hub létrejött, keresse meg az Azure Firewall Manager->biztonságos hubok. Válassza ki a központi >áttekintése lapot a partner nevének és a Függőben lévő biztonsági kapcsolat állapotának **megjelenítéséhez.**

A hub létrehozása és a biztonsági partner beállítása után folytassa a biztonsági szolgáltató csatlakoztatásával a hubhoz.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Külső biztonsági szolgáltató telepítése meglévő hubon

Kiválaszthat egy meglévő elosztót is egy virtuális WAN-ban, és ezt *biztonságos virtuális központtá*alakíthatja.

1. Az **első lépések csoportban**válassza **a Meglévő elosztók konvertálása**lehetőséget.
2. Válasszon ki egy előfizetést és egy meglévő központot. A további lépéseket követve telepítsen egy külső szolgáltatót egy új hubon.

Ne feledje, hogy egy VPN-átjárót kell telepíteni ahhoz, hogy egy meglévő hubot külső szolgáltatókkal biztonságos hubká alakítson át.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Külső biztonsági szolgáltatók konfigurálása biztonságos elosztóhoz való csatlakozáshoz

A virtuális központ VPN-átjárójához való alagutak beállításához a külső szolgáltatóknak hozzáférési jogokra van szükségük a hubhoz. Ehhez társítson egy egyszerű szolgáltatást az előfizetéshez vagy az erőforráscsoporthoz, és adjon hozzáférési jogokat. Ezután meg kell adnia ezeket a hitelesítő adatokat a portálhasználatával a harmadik félnek.

1. Hozzon létre Az Azure Active Directory (AD) szolgáltatás egyszerű: kihagyhatja az átirányítási URL-címet. 

   [Útmutató: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Adja hozzá a hozzáférési jogokat és a hatókört a szolgáltatásnévhez.
   [Útmutató: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > A részletesebb vezérlés érdekében csak az erőforráscsoportra korlátozhatja a hozzáférést.
3. Kövesse a [ZScaler: Konfigurálása a Microsoft Azure virtuális WAN integrációs](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) utasításokat:

   - Jelentkezzen be a partnerportálra, és adja hozzá hitelesítő adatait, hogy a megbízható partner hozzáférhessen a biztonságos központhoz.
   - Szinkronizálja a virtuális központokat a partnerportálon, és állítsa be az alagutat a virtuális hubhoz. Ezt az Azure AD-hitelesítési hitelesítő adatainak érvényesítése után teheti meg.
   
4. Tekintse meg a bújtatás létrehozásának állapotát az Azure Virtuális WAN-portálon az Azure-ban. Miután az alagutak az Azure-on és a partnerportálon **is csatlakoztatva** jelennek meg, folytassa a következő lépésekkel az útvonalak beállításához, hogy melyik ágakat és virtuális hálózatokat küldjenek internetes forgalmat a partnernek.

## <a name="configure-route-settings"></a>Útvonalbeállítások konfigurálása

1. Tallózással keresse fel az Azure Firewall Manager -> a biztonságos elosztókat. 
2. Jelöljön ki egy elosztót. A Hub állapotnak most **a Kiépített** állapotnak kell megjelennie a **függőben lévő biztonsági kapcsolat**helyett.

   Győződjön meg arról, hogy a külső szolgáltató tud csatlakozni a hubhoz. A VPN-átjáró natalanaknak **csatlakoztatott** állapotban kell lennie. Ez az állapot jobban tükrözi a hub és a külső partner közötti kapcsolat állapotát a korábbi állapothoz képest.
3. Jelölje ki a központot, és keresse meg az **Útvonalbeállítások lapot.**

   Amikor egy külső szolgáltatót telepít a hubba, az a hubot *biztonságos virtuális központtá alakítja.* Ez biztosítja, hogy a külső szolgáltató 0.0.0/0 (alapértelmezett) útvonalat hirdet a hubra. A virtuális hálózatok és a hubhoz kapcsolódó helyek azonban csak akkor kapják meg ezt az útvonalat, ha engedélyezi, hogy mely kapcsolatok kapják meg ezt az alapértelmezett útvonalat.
4. Az **internetes forgalom**csoportban válassza **a Virtuális hálózat és internet** közötti vagy **a fiók-az internet hez** lehetőséget, vagy mindkettő, hogy az útvonalak a harmadik félen keresztül történő küldésre konfigurálva.

   Ez csak azt jelzi, hogy milyen típusú forgalmat kell irányítani a hubra, de ez még nem befolyásolja a virtuális hálózatok vagy ágak útvonalait. Ezek az útvonalak alapértelmezés szerint nem lesznek propagálva a hubhoz csatlakoztatott összes virtuális hálózatra/ágra.
5. Ki kell választania **a biztonságos kapcsolatokat,** és ki kell választania azokat a kapcsolatokat, amelyeken ezeket az útvonalakat be kell állítani. Ez azt jelzi, hogy mely virtuális hálózatok/ágak kezdhetik meg az internetes forgalom küldését a külső szolgáltatónak.
6. Az **Útvonal beállítások területen**válassza a Biztonságos **kapcsolatok** lehetőséget az internetes forgalom ban, majd válassza ki a biztonságos virtuális hálózatot vagy*ágakat (helyek* a Virtual WAN-ban). Válassza **az Internetes forgalom biztonságossá tétele lehetőséget.**
   ![Biztonságos internetes forgalom](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Lépjen vissza a hubok lapra. A hub **megbízható biztonsági partner** állapota mostantól **biztosítva**kell lennie.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Fiók- vagy virtuálishálózat-internetes forgalom külső szolgáltatáson keresztül

Ezután ellenőrizheti, hogy a virtuális hálózat virtuális gépei vagy a telephely hozzáférhetnek-e az internethez, és ellenőrizheti, hogy a forgalom a külső szolgáltatásba áramlik-e.

Az útvonalbeállítási lépések befejezése után a virtuális hálózat virtuális gépei, valamint a fiókhelyek 0/0-t küldenek a harmadik fél szolgáltatásútvonalának. Nem rdp vagy SSH ezekbe a virtuális gépekbe. A bejelentkezéshez üzembe helyezheti az [Azure Bastion](../bastion/bastion-overview.md) szolgáltatást egy társviszony-létesített virtuális hálózatban.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: A felhőhálózat biztonságossá tétele az Azure Firewall Manager előzetes verziójával az Azure Portal használatával](secure-cloud-network.md)




