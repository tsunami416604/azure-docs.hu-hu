---
title: "Az Azure verem szoftverfejlesztői készlet telepítésének előfeltételei |} Microsoft Docs"
description: "A környezetére és hardverére követelményei Azure verem szoftverfejlesztői készlet (felhő üzemeltetője) megtekintéséhez."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jeffgilb
ms.openlocfilehash: 8a0d23e14ef50034d5f9595cf154c3513a09c464
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Azure Stack üzembehelyezési előfeltételek

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Központi telepítése előtt [Azure verem szoftverfejlesztői készlet](azure-stack-poc.md), győződjön meg arról, hogy a számítógép megfelel-e a következő követelményeknek:


## <a name="hardware"></a>Hardver
| Összetevő | Minimális | Ajánlott |
| --- | --- | --- |
| Lemezmeghajtók: Operációs rendszer |1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |
| Lemezmeghajtók: általános development kit adatok * |4 lemez. Mindegyik lemez legalább 140 GB tárhellyel (SSD vagy HDD). Az összes elérhető lemez használatban lesz. |4 lemez. Mindegyik lemez legalább 250 GB tárhellyel (SSD vagy HDD). Az összes elérhető lemez használatban lesz. |
| Számítási igény: CPU |Kettős foglalat: 12 fizikai mag (összesen) |Kettős foglalat: 16 fizikai mag (összesen) |
| Számítási igény: Memória |96 GB RAM |128 GB RAM (Ez az a minimális erőforrás-szolgáltató PaaS támogatásához.)|
| Számítási igény: BIOS |Hyper-V engedélyezve (SLAT-támogatással) |Hyper-V engedélyezve (SLAT-támogatással) |
| Hálózat: NIC |Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek |Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek |
| Hardveres tanúsítványembléma |[A Windows Server 2012 R2 hitelesített](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[A Windows Server 2012 R2 hitelesített](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Kell több, mint a kapacitás ez ajánlott, ha azt tervezi, számos hozzáadásával a [Piactéri elemek](azure-stack-download-azure-marketplace-item.md) az Azure-ból.

**Adatok lemezmeghajtó konfigurációs:** meghajtók azonos típusú (összes SAS, minden SATA vagy összes NVMe) és kapacitást kell lennie. Ha SAS-lemezmeghajtókat használ, egyetlen elérési úttal kell őket összekapcsolnia (az MPIO nem engedélyezett, a többutas működés támogatása biztosított).

**HBA-konfigurációs beállítások**

* (Előnyben részesített) Egyszerű HBA
* RAID HBA – Az adaptert „átmenő” módba kell beállítani
* RAID HBA – A lemezeket egyszeres lemezként kell konfigurálni, RAID-0 használatával

**Támogatott busz és adathordozó típusa kombinációk**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (ha az adathordozó típusa nem meghatározott/ismeretlen\*)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

\*Csatlakoztatott lehetőséggel nem RAID-vezérlők nem ismeri fel az adathordozó típusát. Az ilyen vezérlők a HDD és SSD meghajtókat „nem meghatározottként” jelölik meg. Ebben az esetben az SSD állandó tárolóként lesz használva gyorsítótáreszköz helyett. Ezért a szoftverfejlesztői készlet e SSD meghajtókon is telepíthet.

**Példa HBA-kra:** LSI 9207-8i, LSI-9300-8i vagy LSI-9265-8i átmenő módban

OEM mintakonfigurációk is elérhetők.

## <a name="operating-system"></a>Operációs rendszer
|  | **Követelmények** |
| --- | --- |
| **Operációs rendszer verziója** |Windows Server 2012 R2 vagy újabb. Az operációs rendszer verzióját nem kritikus a telepítés megkezdése előtt, a virtuális Merevlemezt, amely megtalálható a Azure verem telepítés lesz indul a számítógép. Az operációs rendszer és a szükséges javításokat már integrálva a lemezképpel. Ne használjon bármely kulcsok aktiválásához bármely a csomagban használt Windows Server-példányokat. |

## <a name="deployment-requirements-check-tool"></a>Telepítési követelményeinek ellenőrzése eszköz
Az operációs rendszer telepítése után is használhatja a [telepítési ellenőrző Azure verem](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) annak ellenőrzéséhez, hogy a hardver megfelel a követelményeknek.

## <a name="account-requirements"></a>Fiókra vonatkozó követelmények
A csomag általában az internetkapcsolattal, amelyen keresztül csatlakozhat a Microsoft Azure telepít. Ebben az esetben konfigurálnia kell egy Azure Active Directory (Azure AD) fiókot a csomag telepítéséhez.

Ha a környezet nem kapcsolódik az internethez, vagy nem kívánja használni az Azure AD, Azure verem telepítheti az Active Directory összevonási szolgáltatások (AD FS) használatával. A csomag tartalmazza a saját AD FS és az Active Directory tartományi szolgáltatások példányok. Használatával kíván üzembe helyezni ezt a beállítást, ha nincs fiók időben beállítása.

>[!NOTE]
Ha az AD FS-beállítás használatával telepít, újra kell telepítenie az Azure verem váltson át az Azure AD.

### <a name="azure-active-directory-accounts"></a>Az Azure Active Directory-fiókok
Azure verem telepíteni az Azure AD-fiókot, elő kell készítenie az Azure AD-fiókot a központi telepítés PowerShell-parancsfájl futtatása előtt. Ezt a fiókot az Azure AD-bérlő számára a globális rendszergazdai válik. Szeretnék telepíteni és az alkalmazások és az összes Azure verem szolgáltatáshoz Azure Active Directory és a Graph API együttműködő szolgáltatásnevekről szolgál. Az alapértelmezett szolgáltató előfizetés (amely később módosítható) tulajdonosaként is használható. Bejelentkezhet az Azure-verem rendszer felügyeleti portálra a fiók használatával.

1. Hozzon létre egy Azure AD-fiókot, amely legalább egy Azure ad a directory-rendszergazda. Ha már rendelkezik ilyen fiókkal, használhatja azt. Ellenkező esetben létrehozhat egy ingyenes [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (kínai, látogasson el a <http://go.microsoft.com/fwlink/?LinkID=717821> helyett). Ha azt tervezi, hogy később [Azure verem regisztrálni Azure](azure-stack-register.md), rendelkeznie kell előfizetéssel a az újonnan létrehozott fiók.
   
    6. lépésében menteni ezeket a hitelesítő adatokat [a csomag telepítése](azure-stack-run-powershell-script.md#deploy-the-development-kit). Ez a *szolgáltatás-rendszergazdai* fiók konfigurálhat és kezelhet erőforrásfelhőket, felhasználói fiókokat, bérlői csomagokat, kvótákat és díjszabást. A portálon létrehozhatnak felhőket webhelyhez, magánfelhőket virtuális géphez, csomagokat, és kezelhetik a felhasználói előfizetéseket.
2. [Hozzon létre](azure-stack-add-new-user-aad.md) legalább egy fiókot, hogy a development Kit hez bérlőként bejelentkezhet.
   
   | **Azure Active Directory-fiók** | **Támogatott?** |
   | --- | --- |
   | Munkahelyi vagy iskolai fiók érvényes nyilvános Azure-előfizetéssel |Igen |
   | Microsoft-fiók érvényes Public Azure-előfizetéssel |Igen |
   | Munkahelyi vagy iskolai fiókkal, Kína érvényes Azure-előfizetéssel |Igen |
   | Munkahelyi vagy iskolai fiók érvényes US Government Azure-előfizetés |Igen |

## <a name="network"></a>Network (Hálózat)
### <a name="switch"></a>Kapcsoló
Egy rendelkezésre álló portot a kapcsolón a development kit gép.  

A fejlesztői csomag machine kapcsolóport hozzáférés vagy trönk porthoz való csatlakozást támogatja. A kapcsoló esetében nem szükségesek speciális jellemzők. Ha trönkportot használ, vagy ha egy VLAN-azonosítót kell beállítania, üzembehelyezési paraméterként kell megadnia a VLAN-azonosítót. A példa látható a [üzembe helyezéshez megadott paraméterek listáját](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Alhálózat
A következő alhálózatokban ne csatlakoztassa a fejlesztési csomag machine:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Ezek alhálózatok development kit környezetben belső hálózatok számára vannak fenntartva.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Kizárólag az IPv4 használata támogatott. Nem hozhat létre IPv6-hálózatokat.

### <a name="dhcp"></a>DHCP
Győződjön meg róla, hogy egy DHCP-kiszolgáló elérhető azon a hálózaton, amelyhez a NIC csatlakozik. Ha a DHCP nem elérhető el, egy kiegészítő, az állomás által használt melletti, statikus IPv4-hálózatot kell előkészítenie. Üzembehelyezési paraméterként meg kell adnia azt az IP-címet és átjárót. A példa látható a [üzembe helyezéshez megadott paraméterek listáját](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Internetelérés
Azure verem internet-hozzáférésre van szüksége, közvetlenül vagy transzparens proxyra. Azure verem nem támogatja az Internet-hozzáférés engedélyezése a WebProxy beállításait. A gazdagép IP, mind az új IP-cím (amelyet a DHCP vagy statikus IP-cím) a MAS BGPNAT01 rendelt érhessék el az Internet kell lennie. A graph.windows.net és login.microsoftonline.com tartományok 80-as és 443-as portot használják.

## <a name="telemetry"></a>Telemetria

Telemetria segít nekünk alakul Azure verem jövőbeli verzióiban. Lehetővé teszi, gyorsan képesek reagálni az visszajelzései alapján, tartalmaz új funkciókat, valamint minőségének javításában. A Microsoft Azure verem tartalmazza a Windows Server 2016-os és az SQL Server 2014. Ezeket a termékeket egyike sem módosulnak az alapértelmezett beállításról, és mindkét által a Microsoft nagyvállalati adatvédelmi nyilatkozat ismerteti. Az Azure verem is tartalmaz, nyílt forráskódú szoftver, amely nem lett módosítva telemetriai adatokat küldeni a Microsoftnak. Az alábbiakban néhány olyan Azure verem telemetriai adatokat:

- központi telepítés regisztrációs adatait
- Ha riasztás megnyitása és bezárása
- a hálózati erőforrások számát

Telemetriai adatok folyamat támogatásához port 443-as (HTTPS) nyissa meg a hálózaton kell lennie. Az ügyfélvégpont a következő: https://vortex-win.data.microsoft.com.

Ha nem szeretne biztosítani a telemetriai adatok Azure verem, kikapcsolhatja azt a development kit állomás és az infrastruktúra virtuális gépek, ahogy az alábbi leírásban.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Kapcsolja ki a development kit gazdagépen telemetriai (nem kötelező)

>[!NOTE]
Telemetria kikapcsolása az development kit állomás szeretnénk, ha az üzembe helyezési parancsfájl futtatása előtt kell megtenni.

Mielőtt [a asdk-installer.ps1 parancsprogram futtatása]() a development kit gazdagép telepítése, a CloudBuilder.vhdx indul, és egy emelt szintű PowerShell-ablakban futtassa az alábbi parancsfájlt:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Beállítás **AllowTelemetry** telemetriai 0 kapcsolja ki a központi Windows- és Azure verem számára. Csak az operációs rendszer kritikus fontosságú biztonsági események küldése. A beállítás szabályozza a Windows telemetriai összes gazdagép és virtuális gépek infrastruktúra, és a program újra alkalmazza az új csomópontok vagy virtuális gépek kibővített műveletek esetén.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Kapcsolja ki az infrastruktúra virtuális gépek telemetriai adatokat (nem kötelező)

A telepítés befejezését követően futtassa a következő parancsfájl egy emelt szintű PowerShell-ablakban (AzureStack\AzureStackAdmin felhasználóként) fejlesztési kit gazdagépen:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

SQL Server telemetriai konfigurálásához lásd: [konfigurálása az SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Használati jelentésben

A regisztráció Azure verem is konfigurált előre használati adatokat az Azure-bA. Használati jelentésben szabályozott egymástól függetlenül a telemetriai adatokból. Mikor reporting használati kikapcsolható [regisztrálása](azure-stack-register.md) a Githubon a parancsfájl használatával. Állítson be a **$reportUsage** paramétert **$false**.

Használati adatok formátuma részletezett a [jelentés Azure verem használati adatokat az Azure-bA](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). Az Azure verem szoftverfejlesztői készlet felhasználók ténylegesen nem van szó. Ez a funkció tartalmazza a csomagban, hogy azt is ellenőrizze, hogy használatai jelentések működéséről. 


## <a name="next-steps"></a>Következő lépések
[Az Azure verem development kit központi telepítési csomag](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Azure verem szoftverfejlesztői készlet telepítése](azure-stack-run-powershell-script.md)

