<properties
    pageTitle="Az Azure Stack POC üzembe helyezése előtt | Microsoft Azure"
    description="Tekintse meg az Azure Stack POC-ra (szolgáltatás-rendszergazda) vonatkozó környezeti és hardverkövetelményeket."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Azure Stack üzembehelyezési előfeltételek

Az Azure Stack POC üzembe helyezése előtt [(a koncepció igazolása](azure-stack-poc.md)) győződjön meg róla, hogy a számítógépe megfelel a következő követelményeknek.
Ezek a követelmények csak az Azure Stack POC-re vonatkoznak, és a jövőbeli kiadásoknál megváltozhatnak.

A következő üzembehelyezési videó megtekintése is hasznos lehet:

[AZURE.VIDEO microsoft-azure-stack-tp1-poc-deployment-tutorial]

## Hardver

| Összetevő | Minimális  | Ajánlott |
|---|---|---|
| Lemezmeghajtók: Operációs rendszer | 1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) | 1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |
| Lemezmeghajtók: Általános Azure Stack POC-adatok | 4 lemez. Mindegyik lemez legalább 140 GB tárhellyel (SSD vagy HDD). Az összes elérhető lemez használatban lesz. | 4 lemez. Mindegyik lemez legalább 250 GB tárhellyel (SSD vagy HDD). Az összes elérhető lemez használatban lesz.|
| Számítási igény: CPU | Kettős foglalat: 12 fizikai mag (összesen)  | Kettős foglalat: 16 fizikai mag (összesen) |
| Számítási igény: Memória | 96 GB RAM  | 128 GB RAM |
| Számítási igény: BIOS | Hyper-V engedélyezve (SLAT-támogatással)  | Hyper-V engedélyezve (SLAT-támogatással) |
| Hálózat: NIC | Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek | Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek |
| Hardveres tanúsítványembléma | [Windows Server 2012 R2-tanúsítvány](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Windows Server 2012 R2-tanúsítvány](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

Használhatja a [Deployment Checker for Azure Stack Technical Preview 1](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) szolgáltatást a követelmények megerősítéséhez.

**Adatlemez-meghajtó konfigurációja:** Minden adatmeghajtónak ugyanolyan típusúnak (csak SAS vagy csak SATA) és méretűnek kell lennie. Ha SAS-lemezmeghajtókat használ, egyetlen elérési úttal kell őket összekapcsolnia (az MPIO nem engedélyezett, a többutas működés támogatása biztosított).

**HBA-konfigurációs lehetőségek**
 
- (Előnyben részesített) Egyszerű HBA
- RAID HBA – Az adaptert „átmenő” módba kell beállítani
- RAID HBA – A lemezeket egyszeres lemezként kell konfigurálni, RAID-0 használatával

**Támogatott busz- és adathordozó-típuskombinációk**

-   SATA HDD

-   SAS HDD

-   RAID HDD

-   RAID SSD (ha az adathordozó típusa nem meghatározott/ismeretlen\*)

-   SATA SSD + SATA HDD

-   SAS SSD + SAS HDD

\* Az átmenő képesség nélküli RAID-vezérlők nem ismerik fel az adathordozó-típust. Az ilyen vezérlők a HDD és SSD meghajtókat „nem meghatározottként” jelölik meg. Ebben az esetben az SSD állandó tárolóként lesz használva gyorsítótáreszköz helyett. Ezért helyezheti üzembe a Microsoft Azure Stack POC-t ezeken az SSD meghajtókon.

**Példa HBA-kra:** LSI 9207-8i, LSI-9300-8i vagy LSI-9265-8i átmenő módban

OEM mintakonfigurációk is elérhetők.




## Operációs rendszer

| | **Követelmények**  |
|---|---|
| **Operációs rendszer verziója** | Windows Server 2016 Datacenter Edition **Technical Preview 4** a legújabb fontos frissítésekkel telepítve. A WindowsServer2016Datacenter.vhdx a letöltési csomag részét képezi. Indíthatja a rendszert ebben a VHDX-ben, majd használhatja alap operációs rendszerként az Azure Stack POC üzembe helyezéséhez.|
| **Telepítési módszer** | Tiszta telepítés. Használhatja az üzembehelyezési csomagban található WindowsServer2016Datacenter.vhdx fájlt az operációs rendszer az Azure Stack POC gépre történő gyors telepítéséhez. |
| **Tartományhoz csatlakoztatva?** | Nem. |


## Microsoft Azure Active Directory-fiókok

1. Olyan Azure AD-fiókot kell létrehoznia, amely a címtár-rendszergazdája legalább egy Azure Active Directorynak. Ha már rendelkezik ilyen fiókkal, használhatja azt. Egyéb esetben ingyenesen létrehozhat egyet a következő webhelyen: [http://azure.microsoft.com/hu-hu/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (Kínából a következő címet kell felkeresnie: <http://go.microsoft.com/fwlink/?LinkID=717821>).

    Mentse ezeket a hitelesítő adatokat a [PowerShell üzembehelyezési szkript futtatása](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script) 6. lépésében történő használathoz. Ez a *szolgáltatás-rendszergazdai* fiók konfigurálhat és kezelhet erőforrásfelhőket, felhasználói fiókokat, bérlői csomagokat, kvótákat és díjszabást. A portálon létrehozhatnak felhőket webhelyhez, magánfelhőket virtuális géphez, csomagokat, és kezelhetik a felhasználói előfizetéseket.

2. [Hozzon létre](azure-stack-add-new-user-aad.md) legalább egy fiókot, így bérlőként bejelentkezhet az Azure Stack POC-ba.

  	| **Azure Active Directory-fiók**  | **Támogatott?** |
  	|---|---| 
  	| Szervezeti azonosító érvényes Public Azure-előfizetéssel  | Igen |
  	| Microsoft-fiók érvényes Public Azure-előfizetéssel  | Igen |
  	| Szervezeti azonosító érvényes China Azure-előfizetéssel  | Igen |
  	| Szervezeti azonosító érvényes US Government Azure-előfizetéssel  | Nem |

>[AZURE.NOTE] Az Azure Stack POC kizárólag az Azure Active Directory-hitelesítést támogatja.


## Network (Hálózat)

### Kapcsoló

Egy elérhető port a kapcsolón a POC-géphez.  

Az Azure Stack POC gépe támogatja a csatlakozást egy kapcsoló hozzáférési portjához vagy trönkportjához. A kapcsoló esetében nem szükségesek speciális jellemzők. Ha trönkportot használ, vagy ha egy VLAN-azonosítót kell beállítania, üzembehelyezési paraméterként kell megadnia a VLAN-azonosítót. Példa:

    DeployAzureStack.ps1 –Verbose –PublicVLan 305

Ennek a paraméternek a megadása a VLAN-azonosítót csak az állomáshoz és NATVM-hez állítja be.

### Alhálózat

Ne csatlakoztassa a POC-gépet a következő alhálózatokhoz: 192.168.200.0/24, 192.168.100.0/24 vagy 192.168.133.0/24. Ezek belső hálózatok számára vannak lefoglalva a Microsoft Azure Stack POC-környezeten belül.

### IPv4/IPv6

Kizárólag az IPv4 használata támogatott. Nem hozhat létre IPv6-hálózatokat.

### DHCP

Győződjön meg róla, hogy egy DHCP-kiszolgáló elérhető azon a hálózaton, amelyhez a NIC csatlakozik. Ha a DHCP nem elérhető el, egy kiegészítő, az állomás által használt melletti, statikus IPv4-hálózatot kell előkészítenie. Üzembehelyezési paraméterként meg kell adnia azt az IP-címet és átjárót. Példa:

    DeployAzureStack.ps1 -Verbose -NATVMStaticIP 10.10.10.10/24 -NATVMStaticGateway 10.10.10.1

### Internetelérés

Győződjön meg róla, hogy a NIC tud csatlakozni az internethez. Mind az állomás IP-címe és a NATVM-hez (DHCP vagy statikus IP-cím által) rendelt új IP-címnek képesnek kell lennie az internet elérésére. A 80-as és 443-as portok a graph.windows.net és login.windows.net tartományok alatt vannak használatban.

### Proxy

Ha proxyra van szükség a környezetében, adja meg a proxykiszolgáló címét és portját üzembehelyezési paraméterként. Példa:

    DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080

Az Azure Stack POC nem támogatja a proxyhitelesítést. 

### Telemetria

A 443-as portnak (HTTPS) nyitva kell lennie a hálózathoz. Az ügyfélvégpont a következő: https://vortex-win.data.microsoft.com.


## Következő lépések

[Az Azure Stack POC üzembehelyezési csomag letöltése](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Az Azure Stack POC üzembe helyezése](azure-stack-run-powershell-script.md)



<!--HONumber=Sep16_HO3-->


