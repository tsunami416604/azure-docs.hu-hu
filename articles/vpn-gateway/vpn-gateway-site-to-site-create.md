<properties
   pageTitle="Virtuális hálózat létrehozása helyek közötti VPN Gateway-kapcsolattal a klasszikus Azure portál használatával | Microsoft Azure"
   description="Hozzon létre S2S VPN Gateway-kapcsolattal rendelkező virtuális hálózatot létesítmények közötti és hibrid konfigurációkhoz a klasszikus üzemi modell használatával."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# Virtuális hálózat létrehozása helyek közötti kapcsolattal a klasszikus Azure portál használatával

> [AZURE.SELECTOR]
- [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)

Ez a cikk részletesen bemutatja, hogyan hozható létre egy virtuális hálózat és egy helyek közötti VPN-kapcsolat a helyszíni hálózathoz a **klasszikus üzemi modell** és a klasszikus portál használatával. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók. Jelenleg nem hozható létre végpontok közötti helyek közötti konfiguráció a klasszikus portálon a klasszikus üzemi modell esetén.

![Helyek közötti diagram](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")


### Üzembe helyezési modellek és eszközök a helyek közötti kapcsolatokhoz

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Ha csatlakoztatni szeretné egymáshoz a virtuális hálózatokat, tekintse meg a [Configure a VNet-to-VNet connection for the classic deployment model](virtual-networks-configure-vnet-to-vnet-connection.md) (Virtuális hálózatok közötti kapcsolat konfigurálása a klasszikus üzemi modellhez) című témakört. 
 
## Előkészületek

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következő elemekkel.

- Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.

- Egy kifelé irányuló, nyilvános IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.

- Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## A virtuális hálózat létrehozása

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com/).

2. Kattintson a képernyő bal alsó sarkában található **Új** gombra. A navigációs ablakban kattintson a **Hálózati szolgáltatások**, majd a **Virtuális hálózat** lehetőségre. Kattintson az **Egyéni létrehozás** lehetőségre a konfigurációs varázsló elindításához.

3. A virtuális hálózat létrehozásához adja meg a konfigurációs beállításokat a következő lapokon:

## Virtuális hálózat részletei lap

Adja meg a következő információkat:

- **Név**: Nevezze el a virtuális hálózatát. Például *EastUSVNet*. Ezt a virtuálishálózat-nevet fogja használni a virtuálisgép- és PaaS-példányok üzembe helyezésekor, ezért nem ajánlott túl bonyolult nevet választani.
- **Hely**: A hely közvetlenül kapcsolódik ahhoz a fizikai helyhez (régiót), ahol az erőforrásokat (virtuális gépeket) el szeretné helyezni. Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezni kívánt virtuális gépek az *USA keleti régiójában* helyezkedjenek el, válassza ki azt helyként. A virtuális hálózat létrehozása után az ahhoz tartozó régiót nem lehet módosítani.

## DNS-kiszolgálók és VPN-kapcsolatok lap

Adja meg a következő információkat, és a továbblépéshez kattintson a jobb alsó Tovább nyílra.

- **DNS-kiszolgálók**: Adja meg a DNS-kiszolgáló nevét és IP-címét, vagy válasszon egy korábban regisztrált DNS-kiszolgálót a helyi menüből. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.
- **Helyek közötti VPN konfigurálása**: Jelölje be a **Helyek közötti VPN konfigurálása** jelölőnégyzetet.
- **Helyi hálózat**: A helyi hálózat a helyszíni fizikai helyet jelenti. Választhat egy korábban létrehozott helyi hálózatot, vagy létrehozhat egy új helyi hálózatot. Ha azonban egy korábban létrehozott helyi hálózat használata mellett dönt, lépjen a **Helyi hálózatok** konfigurációs lapra, és ellenőrizze, hogy a VPN-eszköz IP-címe (nyilvános IPv4-cím) helyes-e.

## Helyek közötti kapcsolat lap

Ha egy új helyi hálózatot hoz létre, megjelenik a **Helyek közötti kapcsolat** lap. Ha egy korábban létrehozott helyi hálózatot kíván használni, ez a lap nem jelenik meg a varázslóban, így továbbléphet a következő szakaszra.

Adja meg az alábbi információkat, és kattintson a Tovább nyílra.

-   **Név**: A helyi (helyszíni) hálózati hely számára választott név.
-   **VPN-eszköz IP-címe**: A helyszíni VPN-eszköz nyilvános Ipv4-címe, amelyet az Azure-hoz való csatlakozáshoz használ. A VPN-eszköz nem lehet NAT mögött.
-   **Címtér**: Adja meg a kezdő IP-címet és a CIDR-t (a címek számát). Meg kell adni a virtuális hálózati átjárón keresztül a helyszíni helyre átküldeni kívánt címteret/címtereket. Ha egy cél-IP-cím az itt megadott tartományokon belülre esik, a rendszer a virtuális hálózati átjárón keresztül irányítja át.
-   **Címtér hozzáadása**: Ha több címteret kíván átküldeni a virtuális hálózati átjárón, adja meg a további címtereket. A **Helyi hálózat** lapon később is hozzáadhat vagy eltávolíthat tartományokat.

## Virtuális hálózat címterei lap

Adja meg a virtuális hálózatához használni kívánt címteret. Ezek a dinamikus IP-címek (DIP-k) lesznek a virtuális gépekhez és az egyéb ezen a virtuális hálózaton üzembe helyezett szerepkörpéldányokhoz rendelve.

Rendkívül fontos, hogy olyan tartományt válasszon, amely nincs átfedésben a helyszíni hálózatához használt egyéb tartományokkal. Egyeztessen a hálózati rendszergazdával. A hálózati rendszergazdának esetlegesen ki kell jelölnie egy IP-címtartományt a helyszíni hálózati címtérből a virtuális hálózaton való használatra.

Adja meg az alábbi információkat, és kattintson a jobb alsó pipára a hálózat konfigurálásához.

- **Címtér**: Adja meg a kezdő IP-címet és a címek számát. Ellenőrizze, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel.
- **Alhálózat hozzáadása**: Adja meg a kezdő IP-címet és a címek számát. További alhálózatok nem szükségesek, de célszerű lehet egy külön alhálózat létrehozása a statikus DIP-kkel rendelkező virtuális gépekhez. Esetleg dönthet úgy is, hogy a virtuális gépeit egy, a többi szerepkörpéldányait tartalmazótól eltérő alhálózaton helyezi el.
- **Átjáróalhálózat hozzáadása**: Kattintson ide az átjáróalhálózat hozzáadásához. Az átjáróalhálózat kizárólag a virtuális hálózati átjáróhoz használatos, és kötelező ehhez a konfigurációhoz.

Kattintson a lap alján található pipára. Ekkor megkezdődik a virtuális hálózat létrehozása. Ha ez befejeződik, megjelenik a **Létrehozva** elem a klasszikus Azure portál **Hálózatok** lapján az **Állapot** listában. Miután létrejött a virtuális hálózat, konfigurálhatja a virtuális hálózati átjárót.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## A virtuális hálózati átjáró konfigurálása

Konfigurálja a virtuális hálózati átjárót a biztonságos helyek közötti kapcsolat létrehozásához. Lásd: [Virtuális hálózati átjáró konfigurálása a klasszikus Azure portálon](vpn-gateway-configure-vpn-gateway-mp.md).

## Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd a [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)-dokumentációt.



<!--HONumber=Sep16_HO4-->


