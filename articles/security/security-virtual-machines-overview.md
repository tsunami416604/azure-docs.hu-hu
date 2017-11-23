---
title: "Az Azure virtuális gépekkel használt Azure biztonsági funkciók |} Microsoft Docs"
description: " Az Azure biztonsági alapszolgáltatások használható az Azure virtuális gépekkel áttekintése. Azure virtuális gépek a munkakörnyezetnek a virtualization kellene vásárolnia és karbantartania a fizikai hardver a virtuális Gépet futtató nélkül. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9d10b9fde99867a218c4368f0be6bee00bc44dc1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure virtuális gépek biztonsági áttekintése
Az Azure Virtual Machines rugalmas környezetet biztosít a legkülönbözőbb számítástechnikai megoldások üzembe helyezéséhez. Számítási feladatait a Windows-, Linux-, SQL Server-, Oracle-, IBM-, SAP- és Azure BizTalk Services-támogatás révén mérettől és programnyelvtől függetlenül, szinte bármely operációs rendszerben üzembe helyezheti.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania.  Hozza létre, és a benne, hogy az adatok-e a védett és biztonságos rendkívül biztonságos adatközpontjainkba alkalmazások központi telepítését.

Az Azure-hozhat létre biztonságos, megfelelő megoldás, amely:

* Virtuális gépek védelme a vírusokkal és kártevőkkel szemben
* Bizalmas adatok titkosítása
* Biztonságos hálózati adatforgalom
* Fenyegetések azonosítása és felderítése
* A megfelelőségi követelmények teljesítése

Ez a cikk célja az alapvető áttekintést nyújtanak a virtuális gépekkel használható az Azure biztonsági funkciók. Azt adja meg egyes szolgáltatások az adatait, így további hivatkozásokat is adja meg.  

Az Azure virtuális gép biztonsági alapképességek ebben a cikkben nem vonatkozik:

* Kártevőirtó
* Hardveres biztonsági modul
* Virtuális gép lemeztitkosítás
* Virtuális gép biztonsági mentése
* Azure Site Recovery
* Virtuális hálózat
* Biztonsági házirendek kezelése és jelentéskészítés
* Megfelelőség

## <a name="antimalware"></a>Kártevőirtó
Az Azure-ral például Microsoft Symantec, Trend Micro vagy Kaspersky biztonsági szállítóktól származó kártevőirtó szoftver segítségével a virtuális gépek védelmét a rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben. Című témakör további tudnivalók az alábbi cikkek partnernél keresse meg a megoldást.

Azure Cloud Services és a virtuális gépek Microsoft Antimalware a valós idejű védelem képessége, amelyik segít azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek.  A Microsoft Antimalware biztosít konfigurálható riasztást küld, ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni magukat az Azure rendszeren.

A Microsoft Antimalware egy olyan ügynök egyetlen megoldás az alkalmazások és a bérlői környezetekben, emberi beavatkozás nélkül a háttérben futnak. Telepítheti a védelmi alapú az alkalmazások és szolgáltatások, vagy alapvető biztonságos--alapértelmezés szerint a rendszer igényeinek megfelelően, vagy speciális egyéni konfiguráció – beleértve a kártevőirtó-figyelés.

Ha központi telepítése a Microsoft Antimalware engedélyezi, a következő alapvető funkciók érhetők el:

* A valós idejű védelem - figyelők tevékenység a Felhőszolgáltatások és virtuális gépeken történő észleli és blokkolja a kártevő szoftverek végrehajtása.
* Ütemezett ellenőrzés - rendszeres időközönként kártevőket, például a aktívan futó programok észlelését célzott vizsgálatát.
* Kártevő szoftver eltávolításának - automatikusan hajt végre műveletet a kártevő, például törlése vagy a karanténba helyezés kártevő fájlok és a rosszindulatú beállításjegyzék-bejegyzések törlése.
* Aláírás frissítések - automatikusan telepíti a legújabb definíciók (vírus-definíciók) védelmének biztosítása kerüljön a az előre meghatározott gyakoriságát.
* Kártevőirtó motor-frissítések – automatikusan frissíti a Microsoft Antimalware-motort.
* Kártevőirtó Platform frissítései – automatikusan frissíti a Microsoft Antimalware platform.
* Aktív védelem - jelentés Azure telemetriai metaadatok fenyegetésről és a gyanús erőforrásokról gyors válasz biztosításához, és lehetővé teszi a valós idejű szinkron aláírás használatával a Microsoft Active Protection rendszer (MAPS).
* Minták reporting - biztosít, és jelenti a mintákat a Microsoft Antimalware szolgáltatás finomíthatja a szolgáltatást, és engedélyezze a hibaelhárítás érdekében.
* Kizárások – lehetővé teszi az alkalmazás és szolgáltatás-rendszergazdák konfigurálása bizonyos fájlokat, a folyamatokat, és fokozza a kizárása védelmi és a teljesítmény- és más okok miatt vizsgálatát.
* Eseménygyűjtés kártevőirtó - a kártevőirtó szolgáltatás állapota, a gyanús tevékenységek és az elvégzett szervizelési műveleteket hajtja végre operációs rendszer-eseménynaplójában rögzíti, és gyűjti azokat az ügyfél Azure Storage figyelembe.

További: a virtuális gépek védelmére szolgáló kártevőirtó szoftver kapcsolatos további információkért lásd:

* [A Microsoft Antimalware Azure Felhőszolgáltatások és virtuális gépek](azure-security-antimalware.md)
* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Hogyan kell telepíteni, és a Trend Micro mély biztonságának konfigurálása a Windows virtuális gép szolgáltatásként](../virtual-machines/windows/classic/install-trend.md)
* [Hogyan kell telepíteni, és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Biztonsági megoldásokat az Azure piactéren](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Hardveres biztonsági modul
Titkosítás és hitelesítés védelmét azáltal, hogy a kulcs biztonsági javítja fejleszthető. A felügyeleti és a kritikus titkos kulcsok és a kulcsok biztonsági tárolja őket az Azure Key Vault egyszerűbbé teheti. A Key Vault segítségével a kulcsokat a 2-es szintű FIPS 140-2 szabványnak megfelelő hardveres biztonsági modulokban (HSM) őrizheti. Az SQL Server titkosítási kulcsok biztonsági mentésre vagy [átlátható adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx) összes tárolhatja Key Vault kulcsok és titkos kulcsokat a alkalmazásokból. Engedélyek és a következő védett elemeknek használatával kezelhető [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

További információ:

* [Mi az Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Az Azure Key Vault blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemeztitkosítás
Az Azure Disk Encryption egy új képesség, amely lehetővé teszi a Windows és Linux Azure virtuális gépek lemezeit titkosításához. Az Azure Disk Encryption használja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows-szolgáltatás és a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít.

A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és kezelheti a lemez titkosítási kulcsok és titkos kulcsokat a kulcstartót az előfizetést, biztosítva, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek-e az Azure tárolás közben.

További információ:

* [Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [A Linux és a Windows virtuális gépek az Azure Disk Encryption](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [A virtuális gép titkosítása](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
Az Azure Backup egy méretezhető megoldás, amellyel megvédheti, és tőkebefektetés nélkül, minimális működési költségek mellett megőrizheti alkalmazásadatait. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védelmének.

További információ:

* [Mi az az Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Az Azure biztonsági mentési képzési terv](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure biztonsági mentési szolgáltatás – gyakori kérdések](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezete BCDR-stratégiájának egyik fontos része, hogy tudja, hogyan kell fenntartani a vállalati munkaterheléseket és alkalmazásokat, illetve azok futtatását tervezett és nem tervezett leállások esetén. Az Azure Site Recovery segítségével levezényelni a replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások, így azok elérhetők a másodlagos helyről, ha az elsődleges hely leáll.

Helyreállítás:

* **Egyszerűbbé teszi a BCDR-stratégia** – a Site Recovery megkönnyíti, hogy a replikáció, feladatátvétel és helyreállítási több üzleti számítási feladatok és alkalmazások egyetlen helyről. A Site Recovery koordinálja a replikációt és a feladatátvételt, de nem fér hozzá az alkalmazás adataihoz, és semmilyen arra vonatkozó információval nem rendelkezik.
* **Rugalmas replikáció biztosít** – a Site Recovery segítségével replikálhatja a Hyper-V virtuális gépek, a VMware virtuális gépek és a windowsos/Linuxos fizikai kiszolgálókon futó számítási feladatok.
* **Támogatja a feladatátvételi és helyreállítási** – a Site Recovery vészhelyreállítások részletezésének támogatásához anélkül, hogy befolyásolná a termelési környezetben feladatátvételi teszteket biztosít. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel befejezése után visszaadhatja a feladatokat az elsődleges helyeknek. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Megszünteti a másodlagos adatközpontba** – egy másodlagos helyszíni helyre, vagy az Azure-bA replikálhatja. Az Azure célhelye vész-helyreállítási megszünteti a költségek és a másodlagos hely karbantartásának. A replikált adatok Azure Storage tárolja.
* **Integrálható a meglévő BCDR-technológiákkal** – Site Recovery együttműködik az alkalmazás más BCDR-funkcióival. A Site Recovery segítségével például az SQL Server háttéralkalmazását vállalati munkaterhelések védelmét. Ez magában foglalja a natív támogatást az SQL Server AlwaysOn rendelkezésre állási csoportok feladatátvételének kezelésében.

További információ:

* [Mi az Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hogyan működik az Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Mi az Azure Site Recovery által védett munkaterhelések?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuális hálózat
Virtuális gépek hálózati kapcsolattal kell. Ez a követelmény kielégítése érdekében az Azure-nak egy Azure virtuális hálózatra kell csatlakoztatni a virtuális gépek. Egy Azure virtuális hálózatra egy logikai szerkezet, a fizikai Azure hálózati háló platformra épül. Minden logikai Azure Virtual Network el különítve a többi Azure virtuális hálózatok. Elkülönítés segítségével biztosítja, hogy a hálózati forgalmat a központi telepítés nem érhető el más Microsoft Azure-ügyfelek.

További információ:

* [Az Azure biztonsági áttekintése](security-network-overview.md)
* [Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)
* [Hálózati szolgáltatásait és partnerségei vállalati forgatókönyvek esetén](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés
Az Azure Security Center segít a megakadályozása, észlelésében és kezelésében fenyegetések, és biztosítja, hogy lássák, és szabályozhatják, az Azure-erőforrások biztonságának nőtt. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Azure Security Center segítségével optimalizálása és figyelheti a virtuális gép biztonsági megoldást:

* Így a virtuális gép [biztonsági javaslatok](../security-center/security-center-recommendations.md) ilyen, a rendszer frissítések alkalmazása, hozzáférés-vezérlési listák végpontok konfigurálása, kártevőirtó engedélyezése, engedélyezze a hálózati biztonsági csoportok és lemeztitkosítás alkalmazni.
* A virtuális gépek állapotának figyelése

További információ:

* [Azure Security Center bemutatása](../security-center/security-center-intro.md)
* [Az Azure Security Center gyakran ismételt kérdések](../security-center/security-center-faq.md)
* [Azure Security Center tervezéséhez és műveletek](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Megfelelőség
Az Azure virtuális gépek FISMA, a FedRAMP, a HIPAA, a PCI DSS szintjét 1 és az egyéb kulcs megfelelőségi alkalmazásokhoz minősítéssel. A hitelesítésszolgáltató megkönnyíti a saját Azure-alkalmazások a megfelelőségi követelményeknek és az üzleti számos különböző hazai és nemzetközi szabályozási követelmények teljesítésére.

További információ:

* [A Microsoft biztonsági és adatkezelési központ: megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Megbízható felhő: Microsoft Azure biztonsági, adatvédelmi és megfelelőségi](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
