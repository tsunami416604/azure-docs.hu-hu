---
title: Mi az a Dedicated HSM? - Az Azure dedikált HSM | Microsoft dokumentumok
description: Az Azure dedikált HSM-jének áttekintése olyan kulcsfontosságú tárolási lehetőségeket kínál az Azure-ban, amelyek megfelelnek a FIPS 140-2 Level 3 minősítésnek
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: dd5ce117645ef2b368bbf8f0e441770d6e746b5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "70881082"
---
# <a name="what-is-azure-dedicated-hsm"></a>Mi az az Azure Dedicated HSM?

Az Azure dedikált HSM egy Azure-szolgáltatás, amely kriptográfiai kulcstárolást biztosít az Azure-ban. A dedikált HSM megfelel a legszigorúbb biztonsági követelményeknek. Ez az ideális megoldás azoknak az ügyfeleknek, akikfips 140-2 Level 3-validált eszközöket, valamint a HSM készülék teljes és kizárólagos vezérlését igénylik. 

 A HSM-eszközök globálisan vannak telepítve számos Azure-régióban. Ezek könnyen kiépíthető, mint egy pár eszköz, és magas rendelkezésre állásra konfigurálható. HSM-eszközök is kiépíthető régiók között, hogy biztosítsa a regionális szintű feladatátvétel ellen. A Microsoft a dedikált HSM szolgáltatást a [Gemalto SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) készülékével nyújtja. Ez az eszköz a legmagasabb szintű teljesítményt és kriptográfiai integrációs lehetőségeket kínálja. 

Kiépítésük után a HSM-eszközök közvetlenül kapcsolódnak az ügyfél virtuális hálózatához. A helyszíni alkalmazás- és felügyeleti eszközökkel is elérhetők, amikor pont-hely vagy helyek közötti VPN-kapcsolatot konfigurál. Az ügyfelek a Gemalto támogatási portáljáról kapják meg a HSM-eszközök konfigurálásához és kezeléséhez szükséges szoftvert és dokumentációt.

## <a name="why-use-azure-dedicated-hsm"></a>Miért érdemes használni az Azure Dedikált HSM-et?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 3.

Számos szervezet szigorú iparági előírásokkal rendelkezik, amelyek előírják, hogy a kriptográfiai kulcstárolás megfelel a [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) követelményeinek. A Microsoft több-bérlős Azure Key Vault szolgáltatás jelenleg csak fips 140-2 Level-2 minősítést biztosít. Az Azure Dedikált HSM valós igényét elégíti ki a pénzügyi szolgáltatási ágazatra, a kormányzati szervekre és másokra, akiknek meg kell felelniük a FIPS 140-2 Level-3 követelményeinek.

### <a name="single-tenant-devices"></a>Egybérlős eszközök

Sok ügyfelünknek követelménye van a kriptográfiai tárolóeszköz egyszeri bérbearától. Az Azure dedikált HSM-szolgáltatás lehetővé teszi számukra, hogy egy fizikai eszköz a Microsoft globálisan elosztott adatközpontok egyik. Miután kivan építve egy ügyfél, csak az ügyfél férhet hozzá az eszközhöz.

### <a name="full-administrative-control"></a>Teljes körű adminisztratív ellenőrzés

Sok ügyfél teljes körű felügyeleti ellenőrzést és kizárólagos hozzáférést igényel az eszközéhez adminisztratív célokra. Az eszköz kiépítése után csak az ügyfél rendelkezik felügyeleti vagy alkalmazásszintű hozzáféréssel az eszközhöz.

 A Microsoft nem rendelkezik rendszergazdai felügyelettel, miután az ügyfél először fért hozzá az eszközhöz, ekkor az ügyfél megváltoztatja a jelszót. Ettől a ponttól kezdve az ügyfél egy valódi egybérlős, teljes felügyeleti felügyelettel és alkalmazás-felügyeleti képességgel. A Microsoft a telemetriai adatokhoz a soros portkapcsolaton keresztül is fenntartja a monitorszintű hozzáférést (nem rendszergazdai szerepkört). Ez a hozzáférés olyan hardvermonitorokra vonatkozik, mint a hőmérséklet, a tápegység állapota és a ventilátorok egészsége. 
 
 Az ügyfél szabadon letilthatja a szükséges figyelést. Ha azonban letiltják, nem kapnak proaktív állapotriasztásokat a Microsofttól.

### <a name="high-performance"></a>Nagy teljesítmény

A Gemalto eszközt különböző okok miatt választották ki erre a szolgáltatásra. Széles körű kriptográfiai algoritmustámogatást, számos támogatott operációs rendszert és széles körű API-támogatást kínál. Az üzembe helyezett modell kiváló teljesítményt nyújt 10 000 másodpercenkénti művelettel az RSA-2048 számára. Támogatja a 10 partíciókat, amelyek egyedi alkalmazáspéldányok használható. Ez az eszköz alacsony késésű, nagy kapacitású és nagy átviteli sebességű eszköz.

### <a name="unique-cloud-based-offering"></a>Egyedi felhőalapú ajánlat

A Microsoft felismerte, hogy különleges ügyfelekre van szükség. Ez az egyetlen felhőszolgáltató, amely új ügyfeleknek dedikált HSM szolgáltatást kínál, amely FIPS 140-2 Level 3-validált, és ilyen mértékű felhőalapú és helyszíni alkalmazásintegrációt kínál.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Megfelelő az Azure dedikált HSM-je?

Az Azure dedikált HSM egy speciális szolgáltatás, amely egy adott típusú nagyméretű szervezet egyedi követelményeit elégíti ki. Ennek eredményeképpen várható, hogy az Azure-ügyfelek nagy része nem felel meg a szolgáltatás használati profiljának. Sokan az Azure Key Vault szolgáltatás megfelelőbbnek és költséghatékonyabbnak fogják találni. Annak érdekében, hogy eldönthesse, megfelel-e az Ön igényeinek, az alábbi feltételeket határoztuk meg.

### <a name="best-fit"></a>A legjobb illeszkedés

Az Azure dedikált HSM a legalkalmasabb a "lift-and-shift" forgatókönyvek, amelyek közvetlen és kizárólagos hozzáférést igényelnek a HSM-eszközökhöz. Példák erre vonatkozóan:

- Alkalmazások áttelepítése a helyszíni eszközökről az Azure virtuális gépekre
- Alkalmazások áttelepítése az Amazon AWS EC2-ről az AWS Cloud HSM Classic szolgáltatást használó virtuális gépekre (az Amazon nem kínálja ezt a szolgáltatást új ügyfeleknek)
- Zsugorfóliázott szoftverek, például Apache/Ngnix SSL tehermentesítés, Oracle TDE és ADCS futtatása az Azure virtuális gépeken 

### <a name="not-a-fit"></a>Nem illik

Az Azure dedikált HSM nem felel meg a következő típusú forgatókönyvnek: Olyan Microsoft felhőszolgáltatások, amelyek támogatják a titkosítást az ügyfelek által felügyelt kulcsokkal (például Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Adatbázis és ügyfélkulcs az Office 365-höz), amelyek nincsenek integrálva az Azure dedikált HSM-jével.

### <a name="it-depends"></a>Attól függ

Az, hogy az Azure dedikált HSM-e működni fog-e, a követelmények és a kompromisszumok potenciálisan összetett kombinációjától függ, amelyeket nem tud vagy nem tud végrehajtani. Ilyen például a FIPS 140-2 Level 3 követelmény. Ez a követelmény gyakori, és a dedikált HSM jelenleg az egyetlen lehetőség a teljesítéséhez. Ha ezek a kötelező követelmények nem relevánsak, akkor gyakran az Azure Key Vault és a dedikált HSM közötti választás. A döntés meghozatala előtt mérje fel a követelményeket.

Helyzetek, amelyekben meg kell mérlegelni a lehetőségeket a következők: 

- Új kód fut az ügyfél Azure virtuális gépén
- SQL Server TDE egy Azure virtuális gépen
- Azure Storage ügyféloldali titkosítása
- SQL Server és Az Azure SQL DB mindig titkosított

## <a name="next-steps"></a>További lépések

Ez egy nagyon speciális szolgáltatás. Ezért azt javasoljuk, hogy teljes mértékben ismerje meg a dokumentációs készlet legfontosabb fogalmait, beleértve az árképzést, a támogatást és a szolgáltatásiszint-szerződéseket. 

A [Gemalto integrációs útmutatók](https://safenet.gemalto.com/partners/microsoft/) segítségével megkönnyítheti a HSM-ek kiépítését egy meglévő virtuális hálózati környezetbe. A telepítési architektúra beállításának meghatározásához útmutatóútmutatók is tartoznak.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Megfigyelő](monitoring.md)
