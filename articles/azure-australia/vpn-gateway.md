---
title: Azure-VPN Gateway az Azure Ausztráliában
description: Az Azure Australia VPN Gateway megvalósítása az ISM-vel való megfelelőség és az ausztrál kormányzati szervek hatékony biztosítása érdekében
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575431"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure-VPN Gateway az Azure Ausztráliában

A nyilvános felhővel való kritikus szolgáltatás a felhőalapú erőforrások és szolgáltatások biztonságos összekapcsolása a meglévő helyszíni rendszerekkel. Az Azure-ban ezt a képességet biztosító szolgáltatás az Azure VPN Gateway. Ez a cikk azokat a kulcsfontosságú pontokat ismerteti, amelyeket figyelembe kell venni, amikor a VPN-átjárót úgy konfigurálja, hogy megfeleljen az ausztrál Signals Igazgatósága (ASD) [adatbiztonsági manuális (ISM) vezérlőinek](https://acsc.gov.au/infosec/ism/).

A VPN-átjárók titkosított forgalmat küldenek egy Azure-beli virtuális hálózat és egy másik hálózat között. A VPN-átjárók három forgatókönyvet érintenek:

- Helyek közötti (S2S)
- Pont – hely kapcsolat (P2S)
- Hálózatról hálózatra

Ez a cikk a S2S VPN-átjárókkal foglalkozik. Az 1. ábrán egy példa látható a S2S VPN-átjáró konfigurációjában.

![VPN Gateway többhelyes kapcsolatokkal](media/vpngateway-multisite-connection-diagram.png)

*1. ábra – Azure S2S VPN Gateway*

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

Az Azure-t az ausztráliai kormányzati ügyfeleknek három hálózati beállítással lehet összekapcsolni:

- IKON
- Azure ExpressRoute
- Nyilvános Internet

Az ausztrál Cyber Security Center [Azure fogyasztói útmutatója](https://servicetrust.microsoft.com/viewpage/Australia) azt javasolja, hogy a három hálózati beállítással együtt használja a VPN Gatewayt (vagy egy azzal egyenértékű védett, harmadik féltől származó szolgáltatást). Ez a javaslat annak biztosítására szolgál, hogy a kapcsolatok megfeleljenek az ISM-vezérlőknek a titkosításhoz és az integritáshoz.

### <a name="encryption-and-integrity"></a>Titkosítás és integritás

Alapértelmezés szerint a VPN egyezteti a titkosítási és sértetlenségi algoritmusokat és paramétereket a kapcsolat létrehozásakor az IKE-kézfogások részeként. Az IKE-kézfogás során a konfiguráció és a beállítások sorrendje attól függ, hogy a VPN-átjáró a kezdeményező vagy a válaszadó. Ez a megjelölés a VPN-eszközön keresztül vezérelhető. A kapcsolat végső konfigurációját a VPN-eszköz konfigurációja vezérli. További információ az ellenőrzött VPN-eszközökről és azok konfigurálásáról: [Tudnivalók a VPN-szolgáltatásokról](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

A VPN-átjárók a kapcsolaton Egyéni IPsec/IKE-házirendet konfigurálva vezérelhetik a titkosítást és az integritást.

### <a name="resource-operations"></a>Erőforrás-műveletek

A VPN-átjárók az Azure és a nem Azure-környezetek közötti kapcsolatot hoznak létre a nyilvános interneten keresztül. Az ISM olyan vezérlőket tartalmaz, amelyek a kapcsolatok explicit engedélyezéséhez kapcsolódnak. Alapértelmezés szerint a VPN-átjárók használatával jogosulatlan alagutak hozhatók létre biztonságos környezetekben. Fontos, hogy a szervezetek az Azure szerepköralapú hozzáférés-vezérlés (RBAC) segítségével szabályozzák, ki hozhat létre és módosíthat VPN-átjárókat és azok kapcsolatait. Az Azure nem rendelkezik beépített szerepkörrel a VPN-átjárók felügyeletéhez, ezért egyéni szerepkörre van szükség.

A tulajdonoshoz, Közreműködőhöz és hálózati közreműködő szerepkörökhöz való hozzáférés szigorúan szabályozott. Javasoljuk továbbá, hogy az Azure Active Directory Privileged Identity Management részletesebb hozzáférés-vezérléshez használja.

### <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure VPN Gateway-átjárók több kapcsolattal rendelkezhetnek, és több helyszíni VPN-eszközt is támogatnak ugyanazon a helyszíni környezetben. Lásd: 1. ábra.

Az Azure-beli virtuális hálózatok több VPN-átjáróval is rendelkezhetnek, amelyek független, aktív-passzív vagy aktív-aktív konfigurációkon helyezhetők üzembe.

Javasoljuk, hogy az összes VPN-átjárót egy [magasan elérhető konfigurációban](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)telepítse. Ilyen például két helyszíni VPN-eszköz, amely két VPN-átjáróhoz csatlakozik aktív-passzív vagy aktív-aktív módban. Lásd a 2. diagramot.

![VPN Gateway redundáns kapcsolatai](media/dual-redundancy.png)

*2. ábra – aktív-aktív VPN-átjárók és két VPN-eszköz*

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

A kényszerített bújtatás átirányítja vagy kényszeríti az internetes forgalmat a helyszíni környezetre a VPN-átjárón keresztül a vizsgálathoz és a naplózáshoz. A kényszerített bújtatás nélkül az Azure-beli virtuális gépekről érkező, az interneten keresztül kötött forgalom közvetlenül a nyilvános interneten keresztül halad át az Azure hálózati infrastruktúrán, a forgalom vizsgálatának vagy naplózásának lehetősége nélkül. A kényszerített bújtatás kritikus fontosságú, ha egy szervezetnek biztonságos internet-átjárót (SIG) kell használnia egy adott környezetben.

## <a name="detailed-configuration"></a>Részletes konfiguráció

### <a name="service-attributes"></a>Szolgáltatás attribútumai

Az ausztrál kormánnyal konfigurált S2S-kapcsolatok VPN-átjáróinak a következő attribútumokkal kell rendelkezniük:

|Attribútum | Kell|
|--- | --- |
|gatewayType | VPN-|
|

A védett ISM-vezérlők betartásához szükséges attribútum-beállítások a következők:

|Attribútum | Kell|
|--- |---|
|vpnType |Útvonalalapú|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Az Azure VPN-átjárók számos titkosítási algoritmust támogatnak az IPsec és az IKE protokoll szabványainak megfelelően. Az alapértelmezett házirend a maximális együttműködési képességet állítja be a külső VPN-eszközök széles körével. Ennek eredményeképpen előfordulhat, hogy az IKE-kézfogás során nem megfelelő konfigurációt lehet egyeztetni. Javasoljuk, hogy [egyéni IPSec/IKE-házirend](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) -paramétereket alkalmazzon a VPN-átjárók vpnClientConfiguration, hogy a kapcsolatok megfeleljenek az Azure-hoz készült helyszíni környezeti kapcsolatokhoz tartozó ISM-vezérlőknek. A legfontosabb attribútumok a következő táblázatban láthatók.

|Attribútum|Kell|Kell|
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

Az előző táblázatban szereplő dhGroup és pfsGroup esetében a ECP256 és a ECP384 is előnyben részesítettek, bár más beállítások is használhatók.

### <a name="related-services"></a>Kapcsolódó szolgáltatások

Az Azure VPN Gateway tervezése és konfigurálása során számos kapcsolódó szolgáltatásnak is léteznie kell, és konfigurálni kell.

|Szolgáltatás | Beavatkozás szükséges|
|--- | ---|
|Virtuális hálózat | A VPN-átjárók virtuális hálózathoz vannak csatlakoztatva. Hozzon létre egy virtuális hálózatot, mielőtt új VPN-átjárót hozna létre.|
|Nyilvános IP-cím | A S2S VPN-átjárók nyilvános IP-címmel kell rendelkezniük a helyszíni VPN-eszköz és a VPN-átjáró közötti kapcsolat létrehozásához. Hozzon létre egy nyilvános IP-címet, mielőtt S2S VPN-átjárót hozna létre.|
|Subnet | Hozza létre a virtuális hálózat alhálózatát a VPN-átjáróhoz.|
|

## <a name="implementation-steps-using-powershell"></a>Implementációs lépések a PowerShell használatával

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

1. Hozzon létre egy egyéni szerepkört. Ilyen például a virtualNetworkGateway közreműködő. Hozzon létre egy olyan szerepkört, amelyet a VPN-átjárók létrehozására és módosítására jogosult felhasználókhoz szeretne rendelni. Az egyéni szerepkörnek a következő műveleteket kell engedélyeznie:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/nyilvános IP/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft. Network/routeTables/*  

2. Adja hozzá az egyéni szerepkört azokhoz a felhasználókhoz, akik számára engedélyezett a VPN-átjárók és a helyi környezetek kapcsolatainak létrehozása és kezelése.

### <a name="create-a-vpn-gateway"></a>VPN-átjáró létrehozása

Ezek a lépések azt feltételezik, hogy már létrehozott egy virtuális hálózatot.

1. Hozzon létre egy új nyilvános IP-címet.
2. Hozzon létre egy VPN Gateway-alhálózatot.
3. Hozzon létre egy VPN Gateway IP-konfigurációs fájlt.
4. Hozzon létre egy VPN-átjárót.
5. Hozzon létre egy helyi hálózati átjárót a helyszíni VPN-eszközhöz.
6. Hozzon létre egy IPsec-házirendet. Ez a lépés azt feltételezi, hogy egyéni IPsec/IKE-házirendeket használ.
7. Hozzon létre kapcsolatot a VPN-átjáró és egy helyi hálózati átjáró között az IPsec-házirend használatával.

### <a name="enforce-tunneling"></a>Bújtatás betartatása

Ha kényszerített bújtatásra van szükség, a VPN-átjáró létrehozása előtt:

1. Hozzon létre egy útválasztási táblázatot és egy útválasztási szabályt.
2. Rendeljen hozzá egy útválasztási táblázatot a megfelelő alhálózatokhoz.

A VPN-átjáró létrehozása után:

- Állítsa be a GatewayDefaultSite a helyszíni környezetre a VPN-átjárón.

### <a name="example-powershell-script"></a>Példa PowerShell-parancsfájlra

Az Egyéni IPsec/IKE-szabályzatok létrehozásához használt PowerShell-szkript például megfelel az ausztráliai védett biztonsági besorolású ISM-vezérlőknek.

Feltételezi, hogy a virtuális hálózat, a VPN-átjáró és a helyi átjárók léteznek.

#### <a name="create-an-ipsecike-policy"></a>IPsec/IKE-szabályzat létrehozása

Az alábbi parancsfájl egy IPsec/IKE-házirendet hoz létre a következő algoritmusokkal és paraméterekkel:

- IKEv2 AES256, SHA256, DHGroup ECP256
- IPSec AES256, SHA256, PFS ECP256, SA élettartam 14 400 másodperc és 102 400 000 KB

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

Ez a cikk a VPN Gateway konkrét konfigurációját tárgyalta, hogy megfeleljenek az ausztrál kormány által védett adatok átvitel közbeni védelmének biztosításához szükséges információs biztonsági útmutatóban megadott követelményeknek. A VPN-átjáró konfigurálásának lépéseiért lásd:

- [Az Azure Virtual Network Gateway áttekintése](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Mi az VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a PowerShell használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [VPN-átjáró létrehozása és kezelése](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)