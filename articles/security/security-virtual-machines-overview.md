---
title: Az Azure virtuális gépekkel használt Azure biztonsági funkciók |} Microsoft Docs
description: Ez a cikk a core áttekintést nyújt az Azure biztonsági funkciók használható Azure virtuális gépekkel.
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
ms.openlocfilehash: 7d31a434d4ead6dd8f8a13a08d368389904b5b4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364233"
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure virtuális gépek biztonsági áttekintése
Azure virtuális gépek segítségével számos különféle számítógépes megoldások üzembe egy gyors módja. A szolgáltatás támogatja a Microsoft Windows, Linux, a Microsoft SQL Server, Oracle, IBM, SAP, és Azure BizTalk szolgáltatások. Ezért bármilyen munkaterhelést és szinte bármilyen operációs rendszeren bármilyen nyelvet is telepíthet.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. Hozza létre, és az alkalmazásokat, hogy az adatok védett és biztonságos rendkívül biztonságos adatközpontokban-e benne.

Az Azure-hozhat létre biztonságos, megfelelő megoldás, amely:

* A virtuális gépek védelmére a vírusok és kártevő vonatkozásában.
* A bizalmas adatok titkosítására.
* Hálózati forgalmának biztonságossá tétele.
* Határozza meg és észleli a veszélyeket.
* A megfelelőségi követelményeknek.

Ez a cikk célja az alapvető áttekintést nyújtanak a virtuális gépekkel használható az Azure biztonsági funkciók. Így további cikkek hivatkozásainak adja meg az egyes szolgáltatások adatait.  

## <a name="antimalware"></a>Kártevőirtó
Az Azure-ral például Microsoft Symantec, Trend Micro vagy Kaspersky biztonsági szállítóktól származó kártevőirtó szoftverek is használhatja. Ez a szoftver megvédi a virtuális gépek a rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben.

Azure Cloud Services és a virtuális gépek Microsoft Antimalware a valós idejű védelem képessége, amelyik segít azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek.  A Microsoft Antimalware az Azure biztosít konfigurálható riasztást küld, ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni magukat az Azure rendszeren.

Az Azure-hoz a Microsoft Antimalware egy olyan single-ügynök megoldás az alkalmazások és a bérlői környezetekben. Célja az, hogy fut a háttérben, emberi beavatkozás nélkül. Telepítheti a védelmi alapú az alkalmazások és szolgáltatások, vagy alapvető biztonságos--alapértelmezés szerint a rendszer igényeinek megfelelően, vagy speciális egyéni konfiguráció – beleértve a kártevőirtó-figyelés.

Ha központilag telepíti, és engedélyezze a Microsoft Antimalware az Azure-ba, a következő alapvető funkciók érhetők el:

* **A valós idejű védelem**: a Felhőszolgáltatások és virtuális gépeken történő észleli és blokkolja a kártevő szoftverek végrehajtási tevékenység figyeli.
* **Ütemezett ellenőrzés**: rendszeres időközönként a kártevő szoftverek, beleértve, aktívan futó programok észlelését célzott vizsgálatát.
* **Kártevő szoftver eltávolításának**: automatikusan hajt végre műveletet a kártevő, például törlése vagy a karanténba helyezés kártevő fájlok és a rosszindulatú beállításjegyzék-bejegyzések törlése.
* **Aláírás frissítések**: automatikusan telepíti a legújabb definíciók (vírus-definíciók) védelem naprakész egy előre meghatározott gyakoriságáról biztosítása érdekében.
* **Kártevőirtó motor frissítéseit**: automatikusan frissíti a Microsoft Antimalware Azure motor.
* **Kártevőirtó platform frissítések**: automatikusan frissíti a Microsoft Antimalware Azure platform.
* **Aktív védelem**: jelentések telemetriai metaadatainak Azure gyors válasz fenyegetésről és gyanús erőforrásokat. Lehetővé teszi a valós idejű szinkron aláírás használatával a Microsoft Active Protection rendszer (MAPS).
* **– A jelentési minták**: biztosít, és jelenti a mintákat a Microsoft Antimalware Azure Service finomíthatja a szolgáltatást, és engedélyezze a hibaelhárítás érdekében.
* **Kizárások**: lehetővé teszi az alkalmazás és szolgáltatás-rendszergazdák konfigurálása bizonyos fájlokat, a folyamatokat, és fokozza a kizárása védelmi és a teljesítmény- és más okok miatt vizsgálatát.
* **Kártevőirtó eseménygyűjtés**: kártevőirtó szolgáltatás állapotát, a gyanús tevékenységek és elvégzett szervizelési műveleteket hajtja végre operációs rendszer-eseménynaplójában rögzíti és gyűjti azokat az Azure-tárfiókot.

Ismerje meg a kártevőirtó szoftver, a virtuális gépek védelme érdekében:

* [A Microsoft Antimalware Azure Felhőszolgáltatások és virtuális gépek](azure-security-antimalware.md)
* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Hogyan kell telepíteni, és a Trend Micro mély biztonságának konfigurálása a Windows virtuális gép szolgáltatásként](../virtual-machines/windows/classic/install-trend.md)
* [Hogyan kell telepíteni, és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Biztonsági megoldásokat az Azure piactéren](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Hardveres biztonsági modul
Kulcs biztonságának növelése javíthatja a titkosítás és hitelesítés védelmet. A felügyeleti és a kritikus titkos kulcsok és a kulcsok biztonsági tárolja őket az Azure Key Vault egyszerűbbé teheti. 

A Key Vault segítségével a kulcsokat a 2-es szintű FIPS 140-2 szabványnak megfelelő hardveres biztonsági modulokban (HSM) őrizheti. Az SQL Server titkosítási kulcsok biztonsági mentésre vagy [átlátható adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx) összes tárolhatja Key Vault kulcsok és titkos kulcsokat a alkalmazásokból. Engedélyek és a következő védett elemeknek használatával kezelhető [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

További információ:

* [Mi az Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Az Azure Key Vault blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemeztitkosítás
Az Azure Disk Encryption egy új funkció a Windows és Linux rendszerű virtuális gépek lemezeit titkosítására. Az Azure Disk Encryption használja a szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows-szolgáltatás és a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít.

A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és a lemez titkosítási kulcsok és titkos kulcsainak kulcstároló-előfizetése kezeléséhez. Ez biztosítja, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek az Azure tárolás közben.

További információ:

* [Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [A Linux és a Windows virtuális gépek az Azure Disk Encryption](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [A virtuális gép titkosítása](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
Az Azure biztonsági mentés méretezhető megoldás, amely segít megvédeni az alkalmazás adatait a tőkebefektetés szükségessége nélkül és minimális üzemeltetési költségek. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védelmének.

További információ:

* [Mi az az Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Az Azure biztonsági mentési képzési terv](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Az Azure Backup szolgáltatás – gyakori kérdések](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezete BCDR-stratégia egyik fontos része az tudja, hogyan kell fenntartani a vállalati számítási feladatok és futó tervezett alkalmazások és a nem tervezett leállások történik. Az Azure Site Recovery segítségével levezényelni a replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások úgy, hogy azok elérhetők a másodlagos helyről, ha az elsődleges hely leáll.

Helyreállítás:

* **Egyszerűbbé teszi a BCDR-stratégia**: a Site Recovery megkönnyíti, hogy a replikáció, feladatátvétel és helyreállítási több üzleti számítási feladatok és alkalmazások egyetlen helyről. A Site Recovery koordinálja a replikációt és feladatátvételt, de nem az alkalmazásadatok intercept vagy nem rendelkezik semmilyen információval.
* **Rugalmas replikáció biztosít**: a Site Recovery segítségével replikálhatja a Hyper-V virtuális gépek, a VMware virtuális gépek és a windowsos/Linuxos fizikai kiszolgálókon futó számítási feladatok.
* **Támogatja a feladatátvételi és helyreállítási**: a Site Recovery vészhelyreállítások részletezésének támogatásához anélkül, hogy befolyásolná a termelési környezetben feladatátvételi teszteket biztosít. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel után is sikertelen vissza az elsődleges helyeken a. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Másodlagos adatközpont kiküszöböli**: egy másodlagos helyszíni helyre, vagy az Azure-bA replikálhatja. Az Azure célhelye vész-helyreállítási megszünteti a költségek és a másodlagos hely karbantartásának. A replikált adatok Azure Storage tárolja.
* **Integrálható a meglévő BCDR-technológiákkal**: Site Recovery együttműködik az egyéb alkalmazások BCDR-funkcióival. A Site Recovery segítségével például az SQL Server háttéralkalmazását vállalati munkaterhelések védelme érdekében. Ez magában foglalja a natív támogatást az SQL Server Always On rendelkezésre állási csoportok feladatátvételének kezelésében.

További információ:

* [Mi az Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hogyan működik az Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Milyen számítási feladatokat az Azure Site Recovery által védett?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuális hálózat
Virtuális gépek hálózati kapcsolattal kell. Ez a követelmény kielégítése érdekében az Azure-nak egy Azure virtuális hálózatra kell csatlakoztatni a virtuális gépek. 

Egy Azure virtuális hálózatra egy logikai szerkezet, a fizikai Azure hálózati háló platformra épül. Minden logikai Azure virtuális hálózat összes többi Azure virtuális hálózat elkülönül. Elkülönítés segítségével biztosítja, hogy a hálózati forgalmat a központi telepítés nem érhető el más Microsoft Azure-ügyfelek.

További információ:

* [Az Azure biztonsági áttekintése](security-network-overview.md)
* [A Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)
* [Hálózati szolgáltatásait és partnerségei vállalati forgatókönyvek esetén](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés
Az Azure Security Center segítséget nyújt a megakadályozása, észlelésében és kezelésében fenyegetések ellen. A Security Center által biztosított, láthatósága nőtt és felett, Azure-erőforrások biztonsági ellenőrzést. Biztosít integrált biztonsági monitorozást és az Azure-előfizetések. Ez segítséget nyújt az szolgáló szabályzatkezelést, és számos biztonsági megoldással együttműködik.

A Security Center segítségével optimalizálása, és figyelheti a virtuális gépek által a biztonsági:

* Így [biztonsági javaslatok](../security-center/security-center-recommendations.md) a virtuális gépekhez. Példa ajánlások a következők: rendszer frissítéseinek alkalmazása, hozzáférés-vezérlési listák végpontok konfigurálása, kártevőirtó engedélyezése, engedélyezze a hálózati biztonsági csoportok és lemeztitkosítás alkalmazni.
* A virtuális gépek állapotának figyelését.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)
* [Az Azure Security Center gyakran ismételt kérdések](../security-center/security-center-faq.md)
* [Azure Security Center tervezéséhez és műveletek](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Megfelelőség
Az Azure virtuális gépek FISMA, a FedRAMP, a HIPAA, a PCI DSS szintjét 1 és az egyéb kulcs megfelelőségi alkalmazásokhoz minősítéssel. A hitelesítésszolgáltató megkönnyíti a saját Azure-alkalmazások a megfelelőségi követelményeknek és az üzleti számos különböző hazai és nemzetközi szabályozási követelmények teljesítésére.

További információ:

* [A Microsoft biztonsági és adatkezelési központ: megfelelőségi](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Megbízható felhő: Microsoft Azure biztonsági, adatvédelmi és megfelelőségi](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
