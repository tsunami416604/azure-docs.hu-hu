---
title: Azure-VPN Gateway az Azure Ausztráliában
description: Az Azure Australia VPN Gateway megvalósítása az ISM-vel való megfelelőség és az ausztrál kormányzati szervek hatékony biztosítása érdekében
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571847"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure-VPN Gateway az Azure Ausztráliában

A nyilvános felhővel való kritikus szolgáltatás a felhőalapú erőforrások és szolgáltatások biztonságos összekapcsolása a meglévő helyszíni rendszerekkel.  Az Azure-ban az Azure VPN Gateway (VPN Gateway) szolgáltatás biztosítja ezt a funkciót. Ez a cikk azokat a legfontosabb szempontokat ismerteti, amelyekkel a VPN Gateway konfigurálható, hogy megfeleljenek az Australian Signals Igazgatósága (ASD) [Information Security kézi vezérlőinek](https://acsc.gov.au/infosec/ism/) (ISM).

A VPN Gateway az Azure-beli virtuális hálózatok és egy másik hálózat közötti titkosított forgalom küldésére szolgál.  A VPN-átjárók három forgatókönyvet céloznak meg:

- **Helyek közötti** S2S
- **Pont – hely** kapcsolat P2S
- **VNet-to-VNet**

Ez a cikk a S2S VPN-átjárókkal foglalkozik. Az 1. ábra egy példát mutat be a helyek közötti VPN-átjáró konfigurálására.

![VPN Gateway többhelyes kapcsolatokkal](media/vpngateway-multisite-connection-diagram.png)

*1. ábra – Azure-helyek közötti VPN Gateway*

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

Az Azure-t az ausztráliai kormányzati ügyfeleknek három hálózati beállítással lehet összekapcsolni:

- **IKON**
- **ExpressRoute**
- **Nyilvános Internet**

Az ausztrál Cyber Security Center Azure-beli [fogyasztói útmutatója](https://servicetrust.microsoft.com/viewpage/Australia) azt javasolja, hogy a két hálózati beállítással együtt használja a VPN Gateway (vagy azzal egyenértékű védelemmel ellátott harmadik féltől származó szolgáltatást), amely biztosítja, hogy a kapcsolatok megfeleljenek a A titkosításra és az integritásra vonatkozó ISM-vezérlők.

### <a name="encryption-and-integrity"></a>Titkosítás és integritás

Alapértelmezés szerint a VPN egyezteti a titkosítási és sértetlenségi algoritmusokat és paramétereket a kapcsolat létrehozásakor az IKE-kézfogások részeként.  Az IKE-kézfogás során a konfiguráció és a preferencia sorrendje attól függ, hogy a VPN Gateway a kezdeményező vagy a válaszadó (Megjegyzés: Ez a VPN-eszközön keresztül van szabályozva).  A kapcsolat végső konfigurációját a VPN-eszköz konfigurációja vezérli.  Az ellenőrzött VPN-eszközök és azok konfigurációjának részleteiért lásd itt: [Tudnivalók a VPN-eszközökről](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

A VPN-átjárók a kapcsolaton Egyéni IPsec/IKE-házirendet konfigurálva vezérelhetik a titkosítást és az integritást.

### <a name="resource-operations"></a>Erőforrás-műveletek

A VPN-átjárók az Azure és a nem Azure-környezetek közötti kapcsolatot hoznak létre a nyilvános interneten keresztül.  Az ISM olyan vezérlőket tartalmaz, amelyek a kapcsolatok explicit engedélyezéséhez kapcsolódnak.  Alapértelmezés szerint a VPN-átjárók használatával jogosulatlan alagutak hozhatók létre biztonságos környezetekben.  Ezért fontos, hogy a szervezetek az Azure szerepköralapú Access Control (RBAC) használatával szabályozzák, ki hozhat létre és módosíthat VPN-átjárókat és azok kapcsolatait.  Az Azure nem rendelkezik "beépített" szerepkörrel a VPN-átjárók kezeléséhez, ezért ehhez egyéni szerepkörre lesz szükség.

A "tulajdonos", a "közreműködő" és a "hálózati közreműködő" szerepkörökhöz való hozzáférés szigorúan szabályozva van.  Javasoljuk továbbá, hogy az Azure AD Privileged Identity Management részletesebb hozzáférés-vezérléshez használja.

### <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure VPN-átjárók több kapcsolattal rendelkezhetnek (lásd az 1. ábrát), és több helyszíni VPN-eszközt is támogatnak ugyanazon a helyszíni környezetben.  

Az Azure-beli virtuális hálózatok több VPN-átjáróval is rendelkezhetnek, amelyek független, aktív-passzív vagy aktív-aktív konfigurációkon helyezhetők üzembe.

Azt javasoljuk, hogy az összes VPN-átjárót egy [nagyon elérhető konfigurációban](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)telepítse: például két helyszíni VPN-eszköz, amely két VPN-átjáróhoz csatlakozik aktív-passzív vagy aktív-aktív módban (lásd a 2. ábrát).

![Redundáns kapcsolatok VPN Gateway](media/dual-redundancy.png)

*2. ábra – aktív-aktív VPN-átjárók és két VPN-eszköz*

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

A kényszerített bújtatás átirányítja vagy "kényszeríti" az internetre irányuló forgalmat a helyszíni környezetre a VPN Gateway a vizsgálat és a naplózás érdekében. A kényszerített bújtatás nélkül az Azure-beli virtuális gépekről érkező, az interneten keresztül kötött forgalom közvetlenül a nyilvános interneten keresztül halad át az Azure hálózati infrastruktúrán, a forgalom vizsgálatának vagy naplózásának lehetősége nélkül.  Ez kritikus fontosságú, ha a szervezetnek biztonságos internet-átjárót (SIG) kell használnia egy adott környezetben.

## <a name="detailed-configuration"></a>Részletes konfiguráció

### <a name="service-attributes"></a>Szolgáltatás attribútumai

Az ausztrál kormánnyal konfigurált S2S-kapcsolatok VPN-átjáróinak a következő attribútumokkal kell rendelkezniük:

|Attribútum | KELL|
|--- | --- |
|gatewayType | VPN-|
|

A védett ISM-vezérlők betartásához szükséges attribútum-beállítások a következők:

|Attribútum | KELL|
|--- |---|
|vpnType |Útvonalalapú|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Az Azure VPN-átjárók számos titkosítási algoritmust támogatnak az IPsec és az IKE protokoll szabványainak megfelelően.  Az alapértelmezett házirend-készletek maximalizálják az együttműködést a külső VPN-eszközök széles körével.  Ennek eredményeképpen lehetséges, hogy az IKE-kézfogás során nem megfelelő konfiguráció van egyeztetve.  Ezért erősen ajánlott az [egyéni IPSec/IKE-házirend](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) paramétereinek alkalmazása a VPN-átjárók vpnClientConfiguration, így biztosítva, hogy a kapcsolatok megfeleljenek az Azure-hoz helyszíni környezeti kapcsolatokhoz tartozó ISM-vezérlőknek.  A legfontosabb attribútumok a következők:

|Attribútum|KELL|KELL|
|---|---|---|
|saLifeTimeSeconds|< 14400 (mp)|300 mp >|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256 – GCMAES256|
|ipsecIntegrity| |SHA256 – GCMAES256|
|ikeEncryption| |AES256 – GCMAES256|
|ikeIntegrity| |SHA256 – GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

*A fenti táblázatban található dhGroup és pfsGroup a ECP256 és a ECP384 előnyben részesítettek, bár más beállítások is használhatók*

### <a name="related-services"></a>Kapcsolódó szolgáltatások

Az Azure-VPN Gateway tervezése és konfigurálása során számos kapcsolódó szolgáltatásnak is léteznie kell, és konfigurálni kell:

|Szolgáltatás | Beavatkozás szükséges|
|--- | ---|
|Virtuális hálózat | A VPN-átjárók virtuális hálózathoz vannak csatlakoztatva.  Új VPN Gateway létrehozása előtt létre kell hozni egy virtuális hálózatot.|
|Nyilvános IP-cím | A S2S VPN-átjárók nyilvános IP-címet igényelnek a helyszíni VPN-eszköz és a VPN Gateway közötti kapcsolat létesítéséhez.  A S2S VPN Gateway létrehozása előtt létre kell hozni egy nyilvános IP-címet.|
|Subnet | A VPN Gatewayhoz létre kell hozni a virtuális hálózat alhálózatát.|
|

## <a name="implementation-steps-using-powershell"></a>Implementációs lépések a PowerShell használatával

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

1. Hozzon létre egyéni szerepkört (például virtualNetworkGateway közreműködői).  Hozzon létre egy olyan szerepkört, amelyet a VPN-átjárók létrehozására és módosítására jogosult felhasználókhoz szeretne rendelni. Az egyéni szerepkörnek a következő műveleteket kell engedélyeznie:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/nyilvános IP/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Vegyen fel egyéni szerepkört azokhoz a felhasználókhoz, akik számára engedélyezett a VPN-átjárók és a helyi környezetek kapcsolatainak létrehozása és kezelése.

### <a name="create-vpn-gateway"></a>VPN Gateway létrehozása

*Ezek a lépések azt feltételezik, hogy egy virtuális hálózat már létrejött*

1. Új nyilvános IP-cím létrehozása
2. VPN Gateway alhálózat létrehozása
3. VPN Gateway IP-konfiguráció létrehozása
4. VPN Gateway átjáró létrehozása
5. Helyi hálózati átjáró létrehozása a helyszíni VPN-eszközhöz
6. IPsec-házirend létrehozása (feltételezve, hogy egyéni IPsec/IKE-házirendeket használ)
7. Kapcsolat létrehozása a VPN Gateway és a helyi hálózati átjáró között az IPsec-házirend használatával

### <a name="enforce-tunneling"></a>Bújtatás betartatása

Ha kényszerített bújtatásra van szükség, a VPN Gateway létrehozása előtt:

1. Útválasztási táblázat és útválasztási szabály (ok) létrehozása
2. Útválasztási táblázat hozzárendelése a megfelelő alhálózatokhoz

A VPN Gateway létrehozása után:

1. A GatewayDefaultSite beállítása a helyszíni környezetre a VPN Gateway

### <a name="example-powershell-script"></a>Példa PowerShell-parancsfájlra

Példa PowerShell-parancsfájlra egy olyan egyéni IPSEC/IKE-házirend létrehozásához, amely megfelel az ausztrál védelemmel ellátott biztonsági besorolású ISM-vezérlőknek.

Feltételezi, hogy a virtuális hálózat, a VPN Gateway és a helyi átjárók léteznek.

#### <a name="create-an-ipsecike-policy"></a>IPsec/IKE-szabályzat létrehozása

Az alábbi parancsfájl egy IPsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:

- IKEv2 AES256, SHA256, DHGroup ECP256
- IPSec AES256, SHA256, PFS ECP256, SA élettartama 14 400 másodperc & 102400000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>S2S VPN-kapcsolat létrehozása az Egyéni IPsec/IKE-házirenddel

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>További lépések

Ez a cikk a VPN Gateway konkrét konfigurációját tárgyalta, hogy megfeleljenek az ausztrál kormány által védett adatok átvitel közbeni védelmének biztosításához szükséges biztonsági kézikönyv (ISM) követelményeinek. A VPN Gateway konfigurálásának részletes lépései:

- [Az Azure Virtual Network Gateway áttekintése](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Mi az VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [VNet létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [VPN-átjáró létrehozása és kezelése](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)