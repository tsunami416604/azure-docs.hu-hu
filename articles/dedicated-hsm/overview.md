---
title: Mit jelent a dedikált HSM? -Az azure dedikált HSM |} A Microsoft Docs
description: Azure dedikált HSM-be, e rövid áttekintés révén kulcstároló-képességeket, amely megfelel a FIPS Azure-ban 140-2-3. szint tanúsítvánnyal
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 503c6d124d6a67c39d9a88100e5ad35dc787fb7b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975593"
---
# <a name="what-is-azure-dedicated-hsm"></a>Mi az az Azure-beli dedikált HSM?

Az Azure, a dedikált HSM egy Azure-szolgáltatás, amely biztosítja a titkosítási kulcs tárolása az Azure-ban. Dedikált HSM megfelel-e a legszigorúbb biztonsági előírásokat. Az ideális megoldás az ügyfeleink számára, akiknek a FIPS 140-2 szintű 3 érvényesített eszközökhöz és a HSM-készülék teljes és kizárólagos vezérlő. 

 Hardveres biztonsági modulokhoz globálisan több Azure-régióban vannak telepítve. Ezek egyszerűen két, az eszközök kiosztása és magas rendelkezésre állásúként konfigurálva. Hardveres biztonsági modulokhoz is bővítheti ahhoz, hogy biztosítsa elleni regionális szintű feladatátvételi régióban. A Microsoft biztosít a dedikált HSM-szolgáltatás használatával a [SafeNet Luna hálózati HSM 7 (modell A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) Gemalto készüléket. Ez az eszköz a legmagasabb szintű teljesítmény- és titkosítási integrációs lehetőségeket kínál. 

Miután azok üzemelnek, hardveres biztonsági modulokhoz közvetlenül egy ügyfél virtuális hálózatán csatlakoznak. Azok is elérhető a helyszíni alkalmazás és a felügyeleti eszközök a pont – hely vagy a helyek közötti VPN-kapcsolat konfigurálásakor. Ügyfeleinknek minél több a szoftverek és a dokumentációt, konfigurálhatja és kezelheti a hardveres biztonsági modulokhoz Gemalto a támogatási portálról.

## <a name="why-use-azure-dedicated-hsm"></a>Miért érdemes használni az Azure dedikált HSM-be?

### <a name="fips-140-2-level-3-compliance"></a>A FIPS 140-2-3. szint megfelelőség

Számos vállalat rendelkezik olyan szigorú iparági szabályozások a Diktálás, amely megfelel a titkosítási kulcs tárolása [FIPS 140-2-3. szint](https://csrc.nist.gov/publications/detail/fips/140/2/final) követelményeinek. A Microsoft több-bérlős Azure Key Vault szolgáltatás jelenleg csak biztosítja a FIPS 140-2 2. szintű tanúsítvánnyal. Dedikált HSM az Azure teljesíti a pénzügyi szolgáltatások iparágát kormányzati szerveknek és mások számára követelményeknek kell megfelelnie a FIPS 140-2-3. szint valós szüksége.

### <a name="single-tenant-devices"></a>Egybérlős eszközök

A titkosítási tárolóeszköz egyetlen bérlős követelmény számos ügyfelünk rendelkezik. A dedikált HSM Azure-szolgáltatás lehetővé teszi, hogy a Microsoft globálisan elosztott adatközpontokban egyik fizikai eszköz kiépítése. Miután egy ügyfél üzembe csak, hogy az ügyfél hozzáférhet az eszközhöz.

### <a name="full-administrative-control"></a>Teljes körű felügyeletet

Számos ügyfél igényel teljes körű felügyeletet és az egyéni eszköz adminisztrációs célból való hozzáférést. Miután egy eszköz ki van építve, csak az ügyfél rendelkezik az eszköz felügyeleti vagy alkalmazásszintű hozzáférést.

 A Microsoft nem rendszergazdai felügyelet rendelkezik, miután az ügyfél hozzáfér az eszköz első alkalommal, amikor az ügyfél megváltoztatja a jelszót. Ettől a ponttól az ügyfél egy igaz egybérlős teljes körű felügyeletet és alkalmazás-kezelési lehetőséget. A Microsoft biztosítja a figyelő szintű hozzáférést (nem rendszergazda szerepkörrel) telemetriai sorosport-kapcsolaton keresztül. Ezt a hozzáférést a hardver figyelők, például a hőmérséklet, power ellátási egészségügyi és ventilátor egészségügyi ismerteti. 
 
 Az ügyfél letilthatja, a monitorozás szükséges díjmentes. Azonban elkezdődnek, proaktív állapotriasztások a Microsoft nem kapnak.

### <a name="high-performance"></a>Nagy teljesítmény

A Gemalto eszköz ehhez a szolgáltatáshoz, különböző okok miatt választotta. Titkosítási algoritmus támogatást, a támogatott operációs rendszerek és a széles körű API-támogatás különféle széles körének biztosít. A modell üzembe helyezett RSA 2048-bites 10 000 műveletek száma másodpercenként a kiváló teljesítményt nyújt. Támogatja a 10 partíció, amely egyedi az alkalmazáspéldányok is használható. Ez az eszköz, egy alacsony késleltetésű, nagy kapacitású eszköz- és nagy átviteli sebességet.

### <a name="unique-cloud-based-offering"></a>Egyedi felhőalapú ajánlat

A Microsoft az ügyfelek egyedi készletének egy adott igénynek ismerhető fel. Legyen az egyetlen felhőszolgáltató, amely egy dedikált HSM-szolgáltatás, amely szint FIPS 140-2, 3 ellenőrzött és kínál olyan mértékben, felhőalapú és helyszíni alkalmazásintegráció kínál az új ügyfeleknek.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Az Azure dedikált HSM-be a megfelelő választás az?

Azure dedikált HSM olyan speciális szolgáltatás, amely egy adott típusú nagyméretű szervezet egyedi igényeinek. Ennek eredményeképpen várható, hogy az Azure-ügyfelek tömeges nem felelnek meg a profil, használja ezt a szolgáltatást. Számos megtalálja az Azure Key Vault szolgáltatás megfelelő, és költséghatékony. Eldöntheti, hogy megfelel a követelményeknek, azonosítottunk a következő feltételeknek.

### <a name="best-fit"></a>Legjobb illesztés

Az Azure, a dedikált HSM leginkább megfelelő hardveres biztonsági modulokhoz történő közvetlen és kizárólagos hozzáférést igénylő "lift-and-shift" forgatókönyvek esetén. Példák erre vonatkozóan:

- Helyszíni áttelepítés alkalmazások az Azure Virtual Machines
- Az Amazon AWS EC2-é az AWS Cloud HSM klasszikus szolgáltatásfelügyeleti (Amazon nem biztosít a szolgáltatás új ügyfeleknek) használó virtuális gépek áttelepítése alkalmazások
- Például az Apache/Ngnix SSL-alapú Kiszervezést, Oracle TDE és az Azure Virtual machines gépeken ADCS shrink-wrapped szoftverek futtatását 

### <a name="not-a-fit"></a>Nem egy laphoz

Az Azure, a dedikált HSM nem jó megoldás lehet a következő típusú forgatókönyvet: A Microsoft cloud services –, hogy a titkosítás támogatása az ügyfél által felügyelt kulcsokat (például az Azure Information Protection, az Azure Disk Encryption, az Azure Data Lake Store, Azure Storage, Azure SQL Database és az Office 365-höz Ügyfélkulcs), amelyek nem integrálhatók Az Azure dedikált HSM.

### <a name="it-depends"></a>Attól függ

E Azure dedikált HSM-be, használhatja a attól függ, hogy feltörések, vagy nem hajtható végre, és a követelmények meglehetősen összetett vegyesen. Ilyen például, a FIPS 140-2-3. szint követelménynek. Ez a követelmény nem gyakori, és dedikált HSM jelenleg az egyetlen lehetőség, az értekezlet vonatkozó. Ha ezeket felhatalmazott követelményeinek nem megfelelő, majd gyakran, az Azure Key Vault és a dedikált HSM közötti választást. A követelmények elemzése alapján hoz döntést elvégzése előtt.

Olyan helyzetekben, amelyben kell tenniük a lehetőségek a következők: 

- Az ügyfél az Azure virtuális gépen futó új kódot
- Az SQL Server TDE-beli virtuális gépen
- Az Azure Storage ügyféloldali titkosítás
- SQL Server és az Always Encrypted az Azure SQL DB

## <a name="next-steps"></a>További lépések

Ez a rendkívül specializált szolgáltatásként. Ezért azt javasoljuk, hogy megértette a dokumentációkészlet, beleértve a díjszabás, a támogatást és szolgáltatásiszint-szerződések kapcsolatos főbb fogalmakat. 

A [Gemalto integrációs útmutatóiban](https://safenet.gemalto.com/partners/microsoft/) segítséget megkönnyítik a HSM-EK kiépítésének egy meglévő virtuális hálózati környezetben. Nincsenek is segít abban, megismerheti, hogyan állíthatja be az üzembe helyezési architektúra útmutatókat.

* [Magas rendelkezésre állás](high-availability.md)
* [fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Monitorozás](monitoring.md)
