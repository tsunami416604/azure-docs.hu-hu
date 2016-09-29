<properties 
   pageTitle="Információk az Azure Virtual Network hálózatokhoz használható, helyek közötti VPN Gateway-kapcsolatok VPN-eszközeiről | Microsoft Azure"
   description="Ez a cikk ismerteti a helyek közötti S2S VPN Gateway-kapcsolatok VPN-eszközeit és IPsec paramétereit, valamint hivatkozásokat tartalmaz a konfigurációs utasításokhoz és mintákhoz."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />


# Információk a helyek közötti VPN Gateway-kapcsolatok VPN-eszközeiről

Helyek közötti (S2S) VPN-kapcsolat konfigurálásához egy VPN-eszközre van szükség. A helyek közötti kapcsolat segítségével hibrid megoldást hozhat létre, illetve biztonságos kapcsolatot tesz lehetővé a helyszíni és a virtuális hálózat között. Jelen cikk a kompatibilis VPN-eszközöket és azok konfigurációs paramétereit tárgyalja. 

>[AZURE.NOTE] Helyek közötti kapcsolat konfigurálásakor a VPN-eszköz számára egy nyilvános IPv4 IP-címre van szükség.                                                                                                                                                                               

Ha az adott eszköz nem szerepel az [Ellenőrzött VPN-eszközök](#devicetable) táblában, tekintse meg a jelen cikk [Nem ellenőrzött VPN-eszközök](#additionaldevices) című szakaszát. Lehetséges, hogy az eszköz mégis kompatibilis az Azure-ral. A VPN-eszközök támogatásával kapcsolatban lépjen kapcsolatba az eszköze gyártójával.

**A táblák megtekintésekor figyelembe veendő elemek:**

- A statikus és a dinamikus útválasztás esetében terminológiai változás történt. Nagy valószínűséggel mindkét kifejezéssel találkozni fog. A funkció nem, csak a nevek változtak.
    - Statikus útválasztás = Házirendalapú
    - Dinamikus útválasztás = Útvonalalapú
- A Nagy teljesítményű és az útvonalalapú VPN-átjárók specifikációi azonosak, hacsak a szöveg másként nem jelzi. Például az útvonalalapú VPN-átjárókkal kompatibilis, ellenőrzött VPN-eszközök az Azure Nagy teljesítményű VPN-átjárókkal is kompatibilisek lesznek. 


## <a name="devicetable"></a>Ellenőrzött VPN-eszközök 

Eszközszállítói partnereinkkel különböző standard VPN-eszközöket ellenőriztünk. Az alábbi listában szereplő eszközcsaládokban megtalálható összes eszköz kompatibilis az Azure VPN-átjárókkal. A konfigurálni kívánt megoldáshoz létrehozandó átjárótípus ellenőrzésének lépéseit az [Információk a VPN Gateway-ről](vpn-gateway-about-vpngateways.md) című cikk ismerteti. 

A VPN-eszköz konfigurálásához kövesse a megfelelő eszközcsaládhoz tartozó hivatkozásokat. A VPN-eszközök támogatásával kapcsolatban lépjen kapcsolatba az eszköze gyártójával.



| **Szállító**                      | **Eszközcsalád**                                        | **Operációs rendszer minimális verziója**                             | **Házirendalapú**                                                                                                                                                                                                             | **Útvonalalapú**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | AR sorozatú VPN-útválasztók                                    | 2.9.2                                              | Hamarosan elérhető                                                                                                                                                                                                                                          | Nem kompatibilis                                                                                                                                                                                               |
| Barracuda Networks, Inc.        | Barracuda NextGen tűzfal, F sorozat             | Házirendalapú: 5.4.3, Útvonalalapú: 6.2.0  | [Konfigurációs utasítások](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Konfigurációs utasítások](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda Networks, Inc.        |  Barracuda NextGen tűzfal, X sorozat                 | Barracuda tűzfal, 6.5-ös verzió | [Barracuda tűzfal](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | Nem kompatibilis                                                                                                                                                                                               |
| Brocade                         | Vyatta 5400 vRouter                                      | Virtual Router 6.6R3 GA                            | [Konfigurációs utasítások](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | Nem kompatibilis                                                                                                                                                                                               |
| Ellenőrzőpont                     | Biztonsági átjáró                                         | R75.40, R75.40VS                                     | [Konfigurációs utasítások](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Konfigurációs utasítások](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Cisco-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | Nem kompatibilis                                                                                                                                                                                               |
| Cisco                           | ASR                                                      | IOS 15.1 (házirendalapú), IOS 15.2 (útvonalalapú)                | [Cisco-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Cisco-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (házirendalapú), IOS 15.1 (útvonalalapú*)               | [Cisco-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Cisco-minták*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1-es vagy újabb verzió                                           | [Integrációs utasítások](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | Nem kompatibilis                                                                                                                                                                                               |
| Dell SonicWALL                  | TZ sorozat, NSA sorozat, SuperMassive sorozat, E-Class NSA sorozat | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Utasítások – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Utasítások – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Utasítások – SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Utasítások – SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | BIG-IP sorozat                                 |           12.0                                            | [Konfigurációs utasítások](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Konfigurációs utasítások](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Konfigurációs utasítások](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Konfigurációs utasítások](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Internet Initiative Japan (IIJ) | SEIL sorozat                                              | SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20            | [Konfigurációs utasítások](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | Nem kompatibilis                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (házirendalapú), JunOS 11.4 (útvonalalapú)            | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | J sorozat                                                 | JunOS 10.4r9 (házirendalapú), JunOS 11.4 (útvonalalapú)          | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (házirendalapú és útvonalalapú)                  | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (házirendalapú és útvonalalapú)                  | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Juniper-minták](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Útválasztás és távelérés szolgáltatás                        | Windows Server 2012                                | Nem kompatibilis                                                                                                                                                                                                                                       | [Microsoft-minták](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| Open Systems AG | Mission Control biztonsági átjáró | N/A | [Telepítési útmutató](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Telepítési útmutató](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Hamarosan elérhető)                                                                                                                                                                                                                                        | Nem kompatibilis                                                                                                                                                                                               |
| Palo Alto Networks              | Az összes PAN-OS rendszert futtató eszköz     | PAN-OS 6.1.5 vagy újabb (házirendalapú), PAN-OS 7.0.5 vagy újabb (útvonalalapú)       | [Konfigurációs utasítások]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Konfigurációs utasítások](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| Watchguard                      | Összes                                                      | Fireware XTM v11.x                                 | [Konfigurációs utasítások](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | Nem kompatibilis                                                                                                                                                                                               |

(*) Az ISR 7200 sorozatba tartozó útválasztók csak a házirendalapú VPN-eket támogatják.

## <a name="additionaldevices"></a>Nem ellenőrzött VPN-eszközök

Ha nem látja az eszközt a fenti Ellenőrzött VPN-eszközök táblában, az ennek ellenére képes lehet helyek közötti kapcsolat létesítésére. Ellenőrizze, hogy a VPN-eszköz teljesíti-e az [Információk a VPN-átjárókról](vpn-gateway-about-vpngateways.md#gateway-requirements) című cikk Az átjáróra vonatkozó követelmények szakaszában megadott minimális követelményeket. A minimális rendszerkövetelményeknek megfelelő eszközök is működnek a VPN-átjárókkal. További támogatásért és konfigurációs útmutatásért lépjen kapcsolatba az eszköze gyártójával.


## Az eszköz konfigurációs mintáinak szerkesztése

A megadott VPN-eszközkonfigurációs minta letöltését követően egyes értékeket a környezeti beállításoknak megfelelően le kell cserélni. 

**A minta szerkesztéséhez tegye a következőket:**

1. Nyissa meg a mintát a Jegyzettömb alkalmazásban. 
1. Keresse meg és cserélje le az összes <*szöveges*> karakterláncot a környezeti beállításokhoz tartozó értékekre. Győződjön meg arról, hogy szerepelnek benne < és > karakterek. A név megadásakor a kiválasztott névnek egyedinek kell lennie. Ha egy parancs nem működik, tekintse meg az eszköz gyártói dokumentációját.

| **Szövegminta**                | **Módosítsa a következőre:**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Az objektum választott neve. Például: myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Az objektum választott neve. Például: myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Az objektum választott neve. Például: myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Az objektum választott neve. Például: myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Az objektum választott neve. Például: myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Adja meg a tartományt. Példa: 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Adja meg az alhálózati maszkot. Példa: 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Adja meg a helyszíni tartományt. Példa: 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Adja meg a helyszíni alhálózati maszkot. Példa: 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Ez az információ kifejezetten az Ön virtuális hálózatára vonatkozik, és a felügyeleti portálon az **átjáró IP-címe** név alatt található meg. |
| &lt;SP_PresharedKey&gt;                | Ez az információ kifejezetten az Ön virtuális hálózatára vonatkozik, és a felügyeleti portálon a Kulcskezelés cím alatt található meg.          |



## IPsec paraméterek

>[AZURE.NOTE] Bár az Azure VPN Gateway támogatja az alábbi táblázatban felsorolt értékeket, adott kombinációk megadására és kiválasztására jelenleg nincs lehetőség az Azure VPN Gateway átjárón. Az esetleges korlátozásokat a helyszíni VPN-eszközről kell megadni. Ezenfelül az MSS korlátozását 1350-re kell állítani.

### IKE – az 1. fázis beállítása

| **Tulajdonság**                                       | **Házirendalapú** | **Útvonalalapú és standard vagy nagy teljesítményű VPN Gateway** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE verziószám                                        | IKEv1                          | IKEv2                                                            |
| Diffie-Hellman Group                               | 2. csoport (1024 bites)             | 2. csoport (1024 bites)                                               |
| Hitelesítési módszer                              | Előre megosztott kulcs                 | Előre megosztott kulcs                                                   |
| Titkosítási algoritmusok                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Kivonatoló algoritmus                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| 1. fázisú biztonsági társítás (SA) Élettartam (idő) | 28 800 másodperc                 | 10 800 másodperc                                                   |


### IKE – a 2. fázis beállítása

| **Tulajdonság**                                                             | **Házirendalapú**                 | **Útvonalalapú és standard vagy nagy teljesítményű VPN Gateway**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE verziószám                                                              | IKEv1                                          | IKEv2                                                              |
| Kivonatoló algoritmus                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| 2. fázisú biztonsági társítás (SA) Élettartam (idő)                        | 3 600 másodperc                                  | 3 600 másodperc                                                                  |
| 2. fázisú biztonsági társítás (SA) Élettartam (teljesítmény)                  | 102 400 000 kB                                 | -                                                                  |
| IPsec SA titkosítási és hitelesítési ajánlatok (sorrendben) | 1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A | Lásd: *Útvonalalapú átjárókra vonatkozó IPsec-biztonsági társítási (SA) ajánlatok* (alább) |
| Sérülés utáni titkosságvédelem (PFS)                                            | Nem                                             | Nem (*)|
| Kapcsolat megszakadásának észlelése                                                      | Nem támogatott                                  | Támogatott                                                          |

(*) az IKE-válaszadóként szolgáló Azure-átjáró az 1., 2., 5., 14., 24. PFS DH-csoportokat fogadja el.

### Útvonalalapú átjárókra vonatkozó IPsec-biztonsági társítási (SA) ajánlatok

Az alábbi táblázat felsorolja az IPsec SA titkosítási és hitelesítési ajánlatait. Az ajánlatok prioritási sorrendben vannak felsorolva a választáshoz.

| **IPsec SA titkosítási és hitelesítési ajánlatok** | **Azure-átjáró, mint kezdeményező**                               | **Azure-átjáró, mint válaszadó**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | AH SHA1, ESP AES_128-cal, null értékű HMAC-val                      |
| 5                                                 | AH SHA1, ESP AES_256-tal, null értékű HMAC-val                      | AH SHA1, ESP 3_DES-sel, null értékű HMAC-val                        |
| 6                                                 | AH SHA1, ESP AES_128-cal, null értékű HMAC-val                      | AH MD5, ESP 3_DES-sel, null értékű HMAC-val, nincs javasolt élettartam |
| 7                                                 | AH SHA1, ESP 3_DES-sel, null értékű HMAC-val                        | AH SHA1, ESP 3_DES SHA1-gyel, nincs élettartam                    |
| 8                                                 | AH MD5, ESP 3_DES-sel, null értékű HMAC-val, nincs javasolt élettartam | AH MD5, ESP 3_DES MD5-tel, nincs élettartam                     |
| 9                                                 | AH SHA1, ESP 3_DES SHA1-gyel, nincs élettartam                    | ESP DES MD5                                                  |
| 10                                                | AH MD5, ESP 3_DES MD5-tel, nincs élettartam                     | ESP DES SHA1, nincs élettartam                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1, ESP DES-sel, null értékű HMAC-val, nincs javasolt élettartam        |
| 12                                                | ESP DES SHA1, nincs élettartam                                   | AH MD5, ESP DES-sel, null értékű HMAC-val, nincs javasolt élettartam        |
| 13                                                | AH SHA1, ESP DES-sel, null értékű HMAC-val, nincs javasolt élettartam        | AH SHA1, ESP DES SHA1-gyel, nincs élettartam                      |
| 14                                                | AH MD5, ESP DES-sel, null értékű HMAC-val, nincs javasolt élettartam        | AH MD5, ESP DES MD5-tel, nincs élettartam                       |
| 15                                                | AH SHA1, ESP DES SHA1-gyel, nincs élettartam                      | ESP SHA, nincs élettartam                                        |
| 16                                                | AH MD5, ESP DES MD5-tel, nincs élettartam                       | ESP MD5, nincs élettartam                                        |
| 17                                                | -                                                            | AH SHA, nincs élettartam                                         |
| 18                                                | -                                                            | AH MD5, nincs élettartam                                        |


- Az IPsec ESP NULL titkosítás útvonalalapú és Nagy teljesítményű VPN-átjárók segítségével adható meg. A nullalapú titkosítás nem biztosít védelmet az adatok számára az átvitel során, ezért használata csak abban az esetben indokolt, ha maximális átviteli sebességre és minimális késleltetésre van szükség.  Az ügyfelek virtuális hálózatok közötti kapcsolatoknál dönthetnek ennek használata mellett, vagy ha más helyen a rendszer titkosítást alkalmaz.

- A létesítmények közötti internetes kapcsolat esetében az alapértelmezett Azure VPN-átjáróbeállításokat a fenti táblákban található titkosítási és kivonatolási algoritmusokkal használja a kritikus fontosságú kommunikáció biztonságának megteremtéséhez.








<!--HONumber=Sep16_HO4-->


