---
title: Az Azure verem szoftverfejlesztői készlet (ASDK) telepítésének előfeltételei |} Microsoft Docs
description: Tekintse át a környezetére és hardverére Azure verem Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f4b55bb3287f67792b3257c3f62256437f5625ca
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-deployment-planning-considerations"></a>Az Azure-verem telepítés tervezési szempontok
Az Azure verem Development Kit (ASDK) telepítése, előtt győződjön meg arról, hogy a fejlesztési kit állomás a számítógép megfelel a jelen cikkben ismertetett.


## <a name="hardware"></a>Hardver
| Összetevő | Minimális | Ajánlott |
| --- | --- | --- |
| Lemezmeghajtók: Operációs rendszer |1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |
| Lemezmeghajtók: általános development kit adatok<sup>*</sup>  |4 lemez. Mindegyik lemez legalább 140 GB tárhellyel (SSD vagy HDD). Az összes rendelkezésre álló lemezek használhatók. |4 lemez. Mindegyik lemez legalább 250 GB tárhellyel (SSD vagy HDD). Az összes rendelkezésre álló lemezek használhatók. |
| Számítási igény: CPU |Kettős foglalat: 12 fizikai mag (összesen) |Kettős foglalat: 16 fizikai mag (összesen) |
| Számítási igény: Memória |96 GB RAM |128 GB RAM (Ez az a minimális erőforrás-szolgáltató PaaS támogatásához.)|
| Számítási igény: BIOS |Hyper-V engedélyezve (SLAT-támogatással) |Hyper-V engedélyezve (SLAT-támogatással) |
| Hálózat: NIC |Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek |Windows Server 2012 R2-tanúsítvány szükséges a NIC-hez; speciális jellemzők nem szükségesek |
| Hardveres tanúsítványembléma |[A Windows Server 2012 R2 hitelesített](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[A Windows Server 2016 hitelesített](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Kell több, mint a kapacitás ez ajánlott, ha azt tervezi, számos hozzáadásával a [Piactéri elemek](asdk-marketplace-item.md) az Azure-ból.

**Adatok lemezmeghajtó konfigurációs:** meghajtók azonos típusú (összes SAS, minden SATA vagy összes NVMe) és kapacitást kell lennie. Ha SAS-lemezmeghajtókat használ, egyetlen elérési úttal kell őket összekapcsolnia (az MPIO nem engedélyezett, a többutas működés támogatása biztosított).

**HBA-konfigurációs beállítások**

* (Előnyben részesített) Egyszerű HBA
* RAID HBA – Az adaptert „átmenő” módba kell beállítani
* RAID HBA – A lemezeket egyszeres lemezként kell konfigurálni, RAID-0 használatával

**Támogatott busz és adathordozó típusa kombinációk**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (ha az adathordozó-típus nincs megadva/ismeretlen<sup>*</sup>)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> Csatlakoztatott lehetőséggel nem RAID-vezérlők nem ismeri fel az adathordozó típusát. Az ilyen tartományvezérlők HDD és a SSD megjelölése nem meghatározott. Ebben az esetben az SSD helyett eszközök gyorsítótár állandó tárolóként szolgál. Ezért a szoftverfejlesztői készlet e SSD meghajtókon is telepíthet.

**Példa HBA-kra:** LSI 9207-8i, LSI-9300-8i vagy LSI-9265-8i átmenő módban

OEM mintakonfigurációk is elérhetők.

## <a name="operating-system"></a>Operációs rendszer
|  | **Követelmények** |
| --- | --- |
| **Operációs rendszer verziója** |Windows Server 2012 R2 vagy újabb. Az operációs rendszer verzióját nem kritikus a telepítés megkezdése előtt, a virtuális Merevlemezt, amely megtalálható a Azure verem telepítés lesz indul a számítógép. Az operációs rendszer és a szükséges javításokat már integrálva a lemezképpel. Ne használjon bármely kulcsok aktiválásához bármely a csomagban használt Windows Server-példányokat. |

> [!TIP]
> Az operációs rendszer telepítése után is használhatja a [telepítési ellenőrző Azure verem](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) annak ellenőrzéséhez, hogy a hardver megfelel a követelményeknek.

## <a name="account-requirements"></a>Fiókra vonatkozó követelmények
A csomag általában az internetkapcsolattal, amelyen keresztül csatlakozhat a Microsoft Azure telepít. Ebben az esetben konfigurálnia kell egy Azure Active Directory (Azure AD) fiókot a csomag telepítéséhez.

Ha a környezet nem kapcsolódik az internethez, vagy nem kívánja használni az Azure AD, Azure verem telepítheti az Active Directory összevonási szolgáltatások (AD FS) használatával. A csomag tartalmazza a saját AD FS és az Active Directory tartományi szolgáltatások példányok. Használatával kíván üzembe helyezni ezt a beállítást, ha nincs fiók időben beállítása.

>[!NOTE]
Ha az AD FS-beállítás használatával telepít, újra kell telepítenie az Azure verem váltson át az Azure AD.

### <a name="azure-active-directory-accounts"></a>Az Azure Active Directory-fiókok
Azure verem telepíteni az Azure AD-fiókot, elő kell készítenie az Azure AD-fiókot a központi telepítés PowerShell-parancsfájl futtatása előtt. Ezt a fiókot az Azure AD-bérlő számára a globális rendszergazdai válik. Szeretnék telepíteni és az alkalmazások és az összes Azure verem szolgáltatáshoz Azure Active Directory és a Graph API együttműködő szolgáltatásnevekről szolgál. Az alapértelmezett szolgáltató előfizetés (amely később módosítható) tulajdonosaként is használható. Bejelentkezhet az Azure-verem rendszer felügyeleti portálra a fiók használatával.

1. Hozzon létre egy Azure AD-fiókot, amely legalább egy Azure ad a directory-rendszergazda. Ha már rendelkezik ilyen fiókkal, használhatja azt. Ellenkező esetben létrehozhat egy ingyenes [ https://azure.microsoft.com/free/ ](http://azure.microsoft.com/pricing/free/) (kínai, látogasson el a <http://go.microsoft.com/fwlink/?LinkID=717821> helyett). Ha azt tervezi, hogy később [Azure verem regisztrálni Azure](asdk-register.md), rendelkeznie kell előfizetéssel a az újonnan létrehozott fiók.
   
    Mentse ezeket a hitelesítő adatokat a szolgáltatás rendszergazdájával. Ez a fiók konfigurálható és kezelhető az erőforrás-felhők, a felhasználói fiókok, a bérlői tervek, a kvóták és a díjszabás. A portálon létrehozhatnak felhőket webhelyhez, magánfelhőket virtuális géphez, csomagokat, és kezelhetik a felhasználói előfizetéseket.
1. Hozzon létre legalább egy felhasználói fiókot az Azure AD-ben, így hez bérlőként development Kit bejelentkezhet.
   
   | **Azure Active Directory-fiók** | **Támogatott?** |
   | --- | --- |
   | Munkahelyi vagy iskolai fiók érvényes nyilvános Azure-előfizetéssel |Igen |
   | Microsoft-fiók érvényes Public Azure-előfizetéssel |Igen |
   | Munkahelyi vagy iskolai fiókkal, Kína érvényes Azure-előfizetéssel |Igen |
   | Munkahelyi vagy iskolai fiók érvényes US Government Azure-előfizetés |Igen |

## <a name="network"></a>Network (Hálózat)
### <a name="switch"></a>Kapcsoló
Egy rendelkezésre álló portot a kapcsolón a development kit gép.  

A fejlesztői csomag machine kapcsolóport hozzáférés vagy trönk porthoz való csatlakozást támogatja. A kapcsoló esetében nem szükségesek speciális jellemzők. Ha trönkportot használ, vagy ha egy VLAN-azonosítót kell beállítania, üzembehelyezési paraméterként kell megadnia a VLAN-azonosítót.

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
Győződjön meg róla, hogy egy DHCP-kiszolgáló elérhető azon a hálózaton, amelyhez a NIC csatlakozik. Ha a DHCP nem elérhető el, egy kiegészítő, az állomás által használt melletti, statikus IPv4-hálózatot kell előkészítenie. Üzembehelyezési paraméterként meg kell adnia azt az IP-címet és átjárót.

### <a name="internet-access"></a>Internetelérés
Azure verem internet-hozzáférésre van szüksége, közvetlenül vagy transzparens proxyra. Azure verem nem támogatja az Internet-hozzáférés engedélyezése a WebProxy beállításait. A gazdagép IP, mind az új IP-cím (amelyet a DHCP vagy statikus IP-cím) a MAS BGPNAT01 rendelt érhessék el az Internet kell lennie. A graph.windows.net és login.microsoftonline.com tartományok 80-as és 443-as portot használják.


## <a name="next-steps"></a>További lépések
[A ASDK központi telepítési csomag](asdk-download.md)
