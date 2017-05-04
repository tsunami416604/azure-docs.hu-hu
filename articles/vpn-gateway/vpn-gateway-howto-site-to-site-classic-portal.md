---
title: "A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: (klasszikus) portál | Microsoft Docs"
description: "A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a portál segítségével."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e5dcf957ea88175be02bce21929c43151417d0e3
ms.lasthandoff: 05/02/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Helyek közötti kapcsolat létrehozása az Azure Portal használatával (klasszikus)

Ez a cikk bemutatja, hogyan használhatja az Azure Portalt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a klasszikus üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)
> 
>

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)


A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a klasszikus üzemi modellt kívánja használni. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Egy kifelé irányuló, nyilvános IPv4 IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván.
* Jelenleg a megosztott kulcs megadásához és a VPN Gateway-kapcsolat létrehozásához a PowerShellre van szükség. Telepítse az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál. Ahhoz, hogy ezt a konfigurációt elvégezhesse, a PowerShellt rendszergazdaként kell futtatnia. 

### <a name="values"></a>Konfigurációs mintaértékek ehhez a gyakorlathoz

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

* **Virtuális hálózat neve:** TestVNet1
* **Címtér:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (nem kötelező ehhez a gyakorlathoz)
* **Alhálózatok:**
  * Előtér: 10.11.0.0/24
  * Háttér: 10.12.0.0/24 (nem kötelező ehhez a gyakorlathoz)
* **Átjáróalhálózat:** 10.11.255.0/27
* **Erőforráscsoport:** TestRG1
* **Hely:** az USA keleti régiója
* **DNS-kiszolgáló:** 8.8.8.8 (nem kötelező ehhez a gyakorlathoz)
* **Helyi hely neve:** Site2

## <a name="CreatVNet"></a>1. Virtuális hálózat létrehozása

Amikor helyek közötti használatra hoz létre virtuális hálózatot, gondoskodnia kell róla, hogy a megadott címterek ne legyenek átfedésben az azon helyi helyekhez tartozó ügyfélcímterekkel, amelyekhez csatlakozni szeretne. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni.

* Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. 

* Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.

    ![Virtuális hálózat keresése panel](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. A Virtuális hálózat panel alján, az **Üzemi modell kiválasztása** listában válassza ki a **Klasszikus** elemet, majd kattintson a **Létrehozás** elemre.

    ![Üzemi modell kiválasztása](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. A **Virtuális hálózat létrehozása** panelen konfigurálja a virtuális hálózat beállításait. Ezen a panelen adhatja hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Virtuális hálózat létrehozása panel")
5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg az **Előfizetés** résznél. Az előfizetéseket a legördülő menüben módosíthatja.
6. Kattintson az **Erőforráscsoport** elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
7. Ezután válassza ki a virtuális hálózatához tartozó **Hely** beállításokat. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
8. Ha szeretné könnyen megtalálni virtuális hálózatát az irányítópulton, akkor válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.

    ![Rögzítés az irányítópulton](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Rögzítés az irányítópulton")
9. A Létrehozás gombra kattintva létrejön egy csempe az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

    ![Virtuális hálózat létrehozása csempe](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Virtuális hálózat létrehozása")

Miután létrejött a virtuális hálózata, megjelenik a **Létrehozva** elem a klasszikus Azure-portál Hálózatok lapján az **Állapot** listában.

## <a name="additionaladdress"></a>2. További címterek felvétele

Miután létrehozta a virtuális hálózatot, hozzáadhat további címtereket. A további címterek felvétele nem kötelező lépése az S2S-konfigurációnak, de ha több címtérre van szüksége, kövesse az alábbi lépéseket:

1. Keresse meg a virtuális hálózatokat a portálon.
2. A virtuális hálózat paneljének **Beállítások** szakasza alatt kattintson a **Címtér** elemre.
3. A Címtér panelen kattintson a **+Hozzáadás** gombra, és adjon meg egy címteret.
 
## <a name="dns"></a>3. DNS-kiszolgáló megadása
A DNS-beállítások megadása nem kötelező lépése az S2S-konfigurációnak, de a névfeloldáshoz szükség van DNS-re.

Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. Nyissa meg a virtuális hálózat beállításait, kattintson a DNS-kiszolgálókra, majd adja hozzá a névfeloldáshoz használni kívánt DNS-kiszolgáló IP-címét. A beállítás nem hoz létre DNS-kiszolgálót. A például szolgáló beállításokban egy nyilvános DNS-kiszolgálót használunk. Általában érdemesebb egy privát DNS-kiszolgálót használni. Ügyeljen arra, hogy olyan DNS-kiszolgálókat adjon hozzá, amelyekkel az erőforrások kommunikálni tudnak.

1. Keresse meg a virtuális hálózatokat a portálon.
2. A virtuális hálózat paneljének **Beállítások** szakasza alatt kattintson a **DNS-kiszolgálók** elemre.
3. Adjon meg egy DNS-kiszolgálót.
4. A beállítások mentéséhez kattintson a lap tetején található **Mentés** gombra.
 
## <a name="localsite"></a>4. A helyi hely konfigurálása

A helyi hely általában a használat helyszínét jelenti. Tartalmazza azon VPN-eszköz IP-címét, amelyhez kapcsolatot szeretne létesíteni, valamint azokat az IP-címtartományokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy átjárót.
2. A virtuális hálózat paneljén, az **Áttekintés** panel VPN-kapcsolatok szakaszában kattintson az **Átjáró** elemre az **Új VPN-kapcsolat** panel megnyitásához.

    ![Kattintson az átjáró beállításainak konfigurálásához](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Kattintson az átjáró beállításainak konfigurálásához")
3. Az **Új VPN-kapcsolat** panelen válassza a **Helyek közötti** beállítást.

    ![Kattintson a helyek közötti lehetőségre](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "helyek közötti")
4. A **Helyi hely** panel megnyitásához kattintson a **Helyi hely – Kötelező beállítások megadása** elemre. Konfigurálja a beállításokat, majd kattintson az **OK** gombra a beállítások mentéséhez.
    - **Név**: Adjon olyan nevet a helyi helynek, amely alapján könnyen azonosíthatja.
    - **VPN-átjáró IP-címe**: Ez a helyszíni hálózaton található VPN-eszköz nyilvános IP-címe. A VPN-eszköznek nyilvános IPv4 IP-címmel kell rendelkeznie. Adjon meg egy érvényes nyilvános IP-címet azon VPN-eszköznek, amelyhez csatlakozni kíván. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.
    - **Ügyfélcímtér**: A helyszíni hálózatra ezen az átjárón keresztül átirányítani kívánt IP-címtartományok listája. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott tartományok ne legyenek átfedésben olyan egyéb hálózatok tartományaival, amelyekhez a virtuális hálózat csatlakozik, illetve magának a virtuális hálózatnak a címtartományaival.

    ![Helyi hely](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Helyi hely konfigurálása")

## <a name="gatewaysubnet"></a>5. Az átjáróalhálózat konfigurálása

Létre kell hoznia egy átjáróalhálózatot a VPN-átjáróhoz. Az átjáróalhálózat tartalmazza a VPN-átjáró szolgáltatásai által használt IP-címeket.

1. Az **Új VPN-kapcsolat** panelen jelölje be az **Átjáró azonnali létrehozása** jelölőnégyzetet. Megjelenik az „Átjáró opcionális konfigurálása” panel. Ha nem jelöli be a jelölőnégyzetet, nem jelenik meg az átjáróalhálózat konfigurálását lehetővé tevő panel.

    ![Átjáró konfigurálása – Alhálózat, méret, útválasztási típus](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Átjáró konfigurálása – Alhálózat, méret, útválasztási típus")
2. Kattintson az **Átjáró konfigurálása – Alhálózat, méret és útválasztási típus** elemre az **Átjáró konfigurációja** panel megnyitásához.
3. Az **Átjáró konfigurációja** panelen kattintson az **Alhálózat – Kötelező beállítások konfigurálása** elemre az **Alhálózat hozzáadása** panel megnyitásához.

    ![Átjáró konfigurációja – átjáróalhálózat](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Átjáró konfigurációja – átjáró-alhálózat")
4. Az **Alhálózat hozzáadása** panelen adja meg az átjáróalhálózatot. Az Ön által megadott átjáróalhálózat mérete a létrehozni kívánt VPN-átjárókonfigurációtól függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: /27-eset vagy /28-asat. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

    ![Átjáróalhálózat hozzáadása](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Átjáróalhálózat hozzáadása")

## <a name="sku"></a>6. A termékváltozat és a VPN típusának megadása
1. Válassza ki az átjáró **méretét**. Ez az átjáró a virtuális hálózati átjáró létrehozásához használt termékváltozata. A portálon az alapértelmezett termékváltozat az **Alapszintű**. További információk a Gateway termékváltozatokról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![A termékváltozat és a VPN típusának kiválasztása](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "A termékváltozat és a VPN típusának kiválasztása")
2. Válassza ki az átjáró **útválasztási típusát**. Ez VPN-típus néven is ismert. Fontos a megfelelő átjárótípus kiválasztása, mert azt később nem lehet módosítani. A VPN-eszköznek kompatibilisnek kell lennie a kiválasztott útválasztási típussal. További, a VPN-típusokra vonatkozó információkért lásd [a VPN-átjárók beállításaival](vpn-gateway-about-vpn-gateway-settings.md#vpntype) foglalkozó témakört. A „RouteBased” és „PolicyBased” VPN-típusokkal további cikkek is foglalkozhatnak. A „Dynamic” (Dinamikus) a „RouteBased”, a „Static” (Statikus) a „PolicyBased” típusra utal.
3. Kattintson az **OK** gombra a beállítások mentéséhez.
4. A **New VPN Connection** (Új VPN-kapcsolat) panel alján kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásához. Ennek végrehajtása 45 percig is eltarthat.

## <a name="vpndevice"></a>7. VPN-eszköz konfigurálása

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. A kapcsolat létrehozása
Ebben a lépésben beállítja a megosztott kulcsot, és létrehozza a kapcsolatot. A beállított kulcsnak meg kell egyeznie a VPN-eszköz konfigurációjához használt kulccsal.

> [!NOTE]
> Ez a lépés jelenleg az Azure Portalon nem érhető el. A műveletekhez az Azure PowerShell-parancsmagjainak Service Management (SM) verzióját kell használnia.
>

### <a name="step-1-connect-to-your-azure-account"></a>1. lépés Csatlakozás az Azure-fiókhoz

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```powershell
  Login-AzureRmAccount
  ```
2. Keresse meg a fiókot az előfizetésekben.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Adja hozzá a PowerShell-parancsmagok SM-verzióját.

  ```powershell
  Add-AzureAccount
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>2. lépés A megosztott kulcs beállítása és a kapcsolat létrehozása

A PowerShell és a klasszikus üzemi modell használatakor a portálon található erőforrások neve néha nem egyezik azzal, amelyet az Azure elvár a PowerShell használatakor. Az alábbi lépések segítségével exportálhatja a hálózati konfigurációs fájlt a nevek pontos értékeinek lekéréséhez.

1. Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Nyissa meg a hálózati konfigurációs fájlt egy xml-szerkesztővel, majd ellenőrizze a „LocalNetworkSite name” és a „VirtualNetworkSite name” értékeit. Módosítsa úgy a példát, hogy tükrözze ezeket az értékeket. Amikor szóközt tartalmazó nevet ad meg, zárja az értéket szimpla idézőjelek közé.

3. Állítsa be a megosztott kulcsot, és hozza létre a kapcsolatot. A „-SharedKey” egy Ön által létrehozott és megadott érték. A példában az „abc123” értéket használtuk, de érdemesebb egy ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
Amikor létrejött a kapcsolat, az eredmény **Status: Successful** (Állapot: Sikeres) lesz.

## <a name="verify"></a>9. A kapcsolat ellenőrzése

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


