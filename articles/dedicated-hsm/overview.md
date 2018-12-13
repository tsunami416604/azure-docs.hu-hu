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
ms.openlocfilehash: 1eeafa33c8c1cdbcd7d0e55e3860dda1b8d451fe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080078"
---
# <a name="what-is-azure-dedicated-hsm"></a>Mi az az Azure-beli dedikált HSM?

Az Azure, a dedikált HSM egy Azure-szolgáltatás, amely biztosítja a titkosítási kulcs tárolása az Azure-ban. Dedikált HSM megfelel-e a legszigorúbb biztonsági előírásokat. Az ideális megoldás az ügyfelek igénylő FIPS 140-2 3. szint érvényesítve eszközök és a HSM-készülék teljes és kizárólagos vezérlő. A hardveres biztonsági modulokhoz számos Azure-régióban globálisan üzembe, és egyszerűen két, az eszközök kiosztása és magas rendelkezésre állásúként konfigurálva. HSM-EK is előfordulhat, hogy kiosztott ahhoz, hogy biztosítsa elleni regionális szolgáltatásszint feladatátvételi régióban. A Microsoft kiadott, a dedikált HSM szolgáltatás használatával a [SafeNet Luna hálózati HSM 7 (modell A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) Gemalto készüléket. Ez az eszköz a legmagasabb szintű teljesítmény- és titkosítási integrációs lehetőségeket kínál. Üzembe helyezésekor, a HSM-EK közvetlenül az ügyfél virtuális hálózathoz csatlakozik, és a is fért hozzá a helyszíni alkalmazás és a felügyeleti eszközök a pont – hely vagy a helyek közötti VPN-kapcsolat konfigurálása. Ügyfelek szoftver- és a dokumentációt, konfigurálhatja és kezelheti a hardveres biztonsági modulokhoz Gemalto a támogatási portálról fogják beszerezni.

## <a name="why-use-azure-dedicated-hsm"></a>Miért érdemes használni az Azure dedikált HSM?

### <a name="fips-140-2-level-3-compliance"></a>A FIPS 140-2. szintű 3 megfelelőség

Számos vállalat rendelkezik olyan szigorú iparági előírások szabályozzák a titkosítási kulcs tárolása megfelel-e [3-es szintű FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) követelményeinek. A Microsoft több-bérlős Azure Key Vault szolgáltatás jelenleg csak biztosítja a FIPS 140-2 2. szintű tanúsítvánnyal. Az Azure, a dedikált HSM valós pénzügyi szolgáltatások, kormányzati szerveknek és mások számára követelményeknek kell megfelelnie a FIPS 140-2 3. szint szükség teljesíti.

### <a name="single-tenant-devices"></a>Egyetlen bérlő eszközök

A titkosítási tárolóeszköz egyetlen bérlős követelmény számos ügyfelünk rendelkezik. A dedikált HSM Azure-szolgáltatás lehetővé teszi a Microsoft globálisan elosztott adatközpontokban egyik fizikai eszközök üzembe helyezés. Kiépített ügyfél számára, ha csak az adott ügyfél tudják az eszköz elérésére.

### <a name="full-administrative-control"></a>Teljes körű felügyeletet

Egyetlen bérlő eszközök, valamint számos ügyfél teljes körű felügyeletet igényel, és az egyéni felügyeleti célú hozzáférést. Kiosztása után már csak az adott ügyfél hozzáfér minden olyan felügyeleti vagy alkalmazás szintű az eszköz. A Microsoft nem szabályozza, amely rendszergazdai lesz az ügyfél először hozzáférést, amely kell átállítani a jelszó módosítása után. Ettől a ponttól az ügyfél egy igaz egybérlős alkalmazás felügyeleti funkció és teljes körű felügyeletet. Egy figyelő szintű hozzáféréssel (nem rendszergazda szerepkörrel), a telemetriai adatok hardver figyelők, például a hőmérséklet, power ellátási egészségügyi és ventilátor egészségügyi kiterjedő sorosport-kapcsolaton keresztül a Microsoft biztosítja. Egy ingyenes, tiltsa le ezt, ha szükséges, de majd nem jelenít meg proaktív állapotriasztások a Microsofttól.

### <a name="high-performance"></a>Nagy teljesítmény

A Gemalto eszköz ezt a szolgáltatást, a titkosítási algoritmust, különböző támogatott operációs rendszerekről és széles körű API-támogatást széles köre miatt választotta. Az adott modell üzembe helyezett RSA 2048-bites 10 000 műveletek száma másodpercenként a kiváló teljesítményt nyújt. Támogatja a 10 partíció, amely egyedi az alkalmazáspéldányok is használható. Ez az egy alacsony késleltetésű, nagy kapacitású eszköz- és nagy átviteli sebességet.

### <a name="unique-cloud-based-offering"></a>Egyedi felhőalapú ajánlat

A Microsoft elismert egy adott igénynek, többek között egy egyedi beállítva, az ügyfelek és az egyetlen felhőszolgáltató, amely új ügyfeleket biztosít egy dedikált HSM-szolgáltatás, amely érvényes a FIPS 140-2 3. szint érvényesítve és kínál olyan mértékben, felhőalapú és helyszíni alkalmazás integráció.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Az Azure dedikált HSM-be a megfelelő választás az?

Az Azure, a dedikált HSM egy olyan speciális szolgáltatás egy adott típusú nagyméretű szervezet igazítva egyedi követelményeinek teljesítése. Ennek eredményeképpen várható, hogy az Azure-ügyfelek tömeges nem felelnek meg a profil, használja ezt a szolgáltatást. Számos megtalálja az Azure Key Vault szolgáltatás több megfelelő és költséghatékonyabb. Segít eldönteni, hogy a választás a követelmények megállapítottuk rendelkezik a következő feltételeknek.

### <a name="best-fit"></a>Legjobb illesztés

Leginkább megfelelő hardveres biztonsági modulokhoz történő közvetlen és kizárólagos hozzáférést igénylő "lift-and-shift" forgatókönyvek esetén. Példák erre vonatkozóan:

- Helyszíni áttelepítés alkalmazások az Azure Virtual Machines
- Az Amazon AWS EC2-é áttelepítése alkalmazások az Azure Virtual Machines, melyek az AWS Cloud HSM klasszikus szolgáltatást (Amazon nem biztosít a szolgáltatás az új ügyfelek számára)
- Shrink-wrapped szoftverek futtatását az Azure Virtual Machines például Apache/Ngnix SSL-alapú Kiszervezést, az Oracle TDE és ADCS

### <a name="not-a-fit"></a>Nem egy laphoz

Azure dedikált HSM-be nem integrált Microsoft-felhőszolgáltatásokhoz, az ügyfél által felügyelt kulcsokat (például az Azure Information Protection, az Azure Disk Encryption, az Azure Data Lake Store, az Azure Storage, Azure SQL, Office 365 Ügyfélkulcs) támogatja a titkosítást.

### <a name="it-depends"></a>Attól függ

Számos forgatókönyv követelményei és milyen feltörések is, vagy nem hajtható végre, a lehetséges összetett vegyesen függ. Például, a FIPS 140-2-3. szint követelmény, amely gyakran teljes kielégítését, és ezért dedikált HSM jelenleg az egyetlen lehetőség.  Ha ezeket felhatalmazott követelményeinek nem megfelelő, majd gyakran lenne között az Azure Key Vault és a dedikált HSM döntés alapján vegyesen követelmények felméréséhez. Példák erre vonatkozóan:

- Új kódot az ügyfél Azure virtuális gépek futtatása
- Az SQL Server TDE-beli virtuális gépen
- Az Azure Storage ügyféloldali titkosítás
- SQL Server és az Always Encrypted az Azure SQL DB

## <a name="next-steps"></a>További lépések

Ez a szolgáltatás rendkívül specializált jellegét, figyelembe véve ajánlott, hogy néhány fontosabb új fogalmat a dokumentációkészlet található teljes áttekintése, a díjszabás, támogatást és szolgáltatásiszint-szerződések teljes áttekintése, és ezután oktatóanyag elérhető egy meglévő virtuális hálózat környezetbe HSM-EK kiépítése megkönnyítése érdekében. [Gemalto integrációs útmutatóiban](https://safenet.gemalto.com/partners/microsoft/) és üzembe helyezési architektúrája meghatározásáért útmutatók szintén kiváló forrást.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Monitorozás](monitoring.md)
