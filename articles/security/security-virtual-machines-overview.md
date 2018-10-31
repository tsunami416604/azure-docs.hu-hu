---
title: Az Azure-beli virtuális gépek használt Azure biztonsági szolgáltatásait |} A Microsoft Docs
description: Ez a cikk az alapvető áttekintést nyújt az Azure biztonsági funkciókat, amelyeket az Azure Virtual Machines segítségével.
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
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: 631fe91bc72093cf5ad87779853df4901a27a068
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249117"
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure virtuális gépek biztonsági áttekintése

Azure Virtual Machines használatával a legkülönbözőbb számítástechnikai megoldások széles üzembe. A szolgáltatás támogatja a Microsoft Windows, Linux, a Microsoft SQL Server, Oracle, IBM, SAP és az Azure BizTalk Services. Telepíthet tehát bármilyen számítási feladatot és programnyelvtől függetlenül, szinte bármely operációs rendszeren.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. Hozhat létre, és üzembe helyezett az adatok nem védett és biztonságos a rendkívül biztonságos adatközpontjainkban.

Az Azure-ral hozhat létre biztonságos, előírásoknak megfelelő megoldások, amelyek:

* A virtuális gépek védelme a vírusokkal és kártevőkkel szemben.
* Bizalmas adatok titkosítása.
* Hálózati forgalmának biztonságossá tétele.
* Fenyegetések azonosítása és felderítése.
* Megfelelőségi követelmények teljesítése érdekében.

Ez a cikk célja az alapvető áttekintést biztosít az Azure biztonsági szolgáltatások virtuális gépekkel is használható. Cikkek hivatkozásainak egyes szolgáltatások részletei adjon meg, hogy többet is megtudhat.  

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ral biztonsági megoldásokkal foglalkozó forgalmazó, például a Microsoft, a Symantec, a Trend Micro és a Kaspersky kártevőirtó szoftvert is használhatja. Ez a szoftver segítségével, a virtuális gépek kártékony fájlokkal, hirdetőprogramokkal és egyéb fenyegetések védelme.

A Microsoft Antimalware az Azure Cloud Services és Virtual Machines a vírusok, kémprogramok és más kártevők azonosításában és segít a valós idejű védelem funkcióval.  A Microsoft Antimalware az Azure biztosít konfigurálható riasztást, ha ismert kártevő vagy nemkívánatos szoftver megkísérli a telepítést, vagy futtassa az Azure rendszeren.

A Microsoft Antimalware az Azure az alkalmazások és a bérlői környezetek single-ügynök megoldás is. Feladata, hogy fut a háttérben, emberi beavatkozás nélkül. Az alkalmazás-munkaterhelések, vagy alapszintű biztonságos alapértelmezés szerint az igényeinek megfelelően, vagy egyéni konfigurálására, beleértve a kártevőirtó-figyelés speciális védelem is telepítheti.

Amikor telepíti és engedélyezi a Microsoft Antimalware az Azure-hoz, a következő alapvető funkciók érhetők el:

* **Valós idejű védelem**: figyeli a tevékenységet, a Cloud Services és észleli és blokkolja a kártevő szoftver futtatása virtuális gépeken.
* **Keresés ütemezett**: a Microsoft rendszeresen végez célzott vizsgálatának például aktívan futó programok észlelését.
* **Kártevő szoftver eltávolításának**: automatikusan hajt végre műveletet a észlelt kártevőket, például törlése vagy rosszindulatú fájlok karanténba és rosszindulatú beállításjegyzék-bejegyzések törlése.
* **Aláírás-frissítések**: automatikusan telepíti a legújabb aláírások (vírus-definíciók), hogy protection naprakész-e az előre meghatározott gyakoriságát.
* **A kártevőirtó motor frissítéseit**: automatikusan frissíti a Microsoft Antimalware az Azure-motor.
* **A kártevőirtó platform frissítések**: a Microsoft Antimalware az Azure platform automatikusan frissíti.
* **Aktív védelem**: jelentések telemetriai metaadatait, az Azure-bA az észlelt fenyegetések és a gyanús erőforrások arról, hogy gyors választ. Lehetővé teszi a valós idejű szinkron aláírás kézbesítési keresztül a Microsoft Active Protection System (MAPS).
* **Jelentéskészítés – minták**: biztosít, és a minták a jelentéseket a Microsoft Antimalware az Azure-szolgáltatás, pontosítsa a szolgáltatást, és hibaelhárítást.
* **A kizárások**: lehetővé teszi az alkalmazás és szolgáltatás-rendszergazdák konfigurálása bizonyos fájlokat, a folyamatokat, és a kizárása védelmi és a teljesítmény és más okok miatt vizsgálatát meghajtókat.
* **Kártevőirtó eseménygyűjtés**: kártevőirtó szolgáltatás állapotát, a gyanús tevékenységeket és a szervizelési műveletek venni az operációs rendszer-eseménynaplóban rögzíti, és gyűjti azokat az Azure storage-fiókban.

További információ a kártevőirtó szoftver, a virtuális gépek védelme érdekében:

* [A Microsoft Antimalware az Azure Cloud Services és Virtual Machines](azure-security-antimalware.md)
* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Telepítése és konfigurálása a Trend Micro Deep Security szolgáltatásként, a Windows virtuális gép](../virtual-machines/windows/classic/install-trend.md)
* [Telepítése és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Biztonsági megoldások az Azure Marketplace-en](https://azure.microsoft.com/marketplace/?term=security)

Még hatékonyabb védelme érdekében fontolja meg [Windows Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). A Windows Defender ATP-ben a következőket kínálja:

* [Támadási felület csökkentése](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Következő generációs védelme](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Az Endpoint protection-válasz](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizált vizsgálati és javítási](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [biztonságos pontszám](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Speciális vadászat](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Felügyeleti és API-k](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

További információ: 

* [WDATP használatának első lépései](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/get-started)  
* [WDATP képességek áttekintése](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

Kulcs biztonságának növelése javíthatja a titkosítási és hitelesítési védelmet. A kezelését és a létfontosságú kulcsok és titkok biztonságos tárolása az Azure Key Vault egyszerűsítheti. 

A Key Vault segítségével a kulcsokat a 2-es szintű FIPS 140-2 szabványnak megfelelő hardveres biztonsági modulokban (HSM) őrizheti. Az SQL Server titkosítási kulcsok biztonsági mentése vagy [transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx) összes tárolható a Key Vault- és az alkalmazások titkos. Engedélyek és a hozzáférést ezekhez a védett elemekhez kezelhető [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

További információ:

* [Mi az Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md)
* [Az Azure Key Vault-blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemeztitkosítás

Az Azure Disk Encryption egy új funkció a Windows és Linux rendszerű virtuális gépek lemezeinek titkosításához. Az Azure Disk Encryption használja az iparági szabványnak megfelelő [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows szolgáltatása és a [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját.

A megoldás integrálva van az Azure Key Vault segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. Biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure Storage-ban.

További információ:

* [Az Azure Disk Encryption IaaS virtuális gépekhez](../security/azure-security-disk-encryption-overview.md)
* [Gyors útmutató: Az Azure PowerShell használatával Windows IaaS virtuális gép titkosítása](../security/quick-encrypt-vm-powershell.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése

Az Azure Backup egy méretezhető megoldás, amely segít megvédeni a nulla tőkebefektetés és minimális működési költségek mellett megőrizheti alkalmazásadatait. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védett.

További információ:

* [Mi az az Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Az Azure biztonsági mentési képzési terv](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Az Azure Backup szolgáltatás – gyakori kérdések](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Fontos része annak a szervezete BCDR-stratégia van foglalkozhatunk azzal, hogyan kell fenntartani a vállalati számítási feladatok és alkalmazások futására tervezett és nem tervezett leállások. Az Azure Site Recovery segítségével előkészíthető a replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások úgy, hogy a megjelenésük egy másodlagos helyre, ha az elsődleges hely leáll.

A Site Recovery:

* **Egyszerűbbé teszi a BCDR-stratégia**: a Site Recovery megkönnyíti a replikáció, feladatátvétel és helyreállítás több üzleti számítási feladatok és alkalmazások egyetlen helyről kezelésére. A Site Recovery koordinálja a replikációt és a feladatátvételt, de nem az alkalmazásadatok intercept vagy nem rendelkezik semmilyen információval.
* **Rugalmas replikáció biztosít**: a Site Recovery segítségével replikálhatja a Hyper-V virtuális gépek, VMware virtuális gépek és fizikai Windows/Linux-kiszolgálókon futó számítási feladatokat.
* **Támogatja a feladatátvétel és helyreállítás**: a Site Recovery feladatátvételi teszteket a vészhelyreállítási próbák támogatására anélkül, hogy ez hatással lenne az éles környezetet biztosít. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvételt követően is visszaadja a feladatokat az elsődleges helyeken. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Kiküszöböli a másodlagos adatközpontok**: egy másodlagos helyszíni helyre, vagy az Azure-bA replikálhatja. Az Azure-t egy cél vész-helyreállítási kiküszöböli a járó bonyodalmak és költségek fenntartása másodlagos helyre. A replikált adatokat az Azure Storage szolgáltatásban tárolt.
* **A meglévő BCDR-technológiákkal integrálható**: Site Recovery együttműködik az egyéb alkalmazások általi BCDR-funkciókkal. A Site Recovery használatával például az SQL Server háttéralkalmazását vállalati számítási feladatok védelme érdekében. Ez magában foglalja a natív módon támogatja a SQL Server Always On rendelkezésre állási csoportok feladatátvételének kezelésében.

További információ:

* [Mi az Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Hogyan működik az Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Milyen számítási feladatokat az Azure Site Recovery által védett?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Virtuális hálózat

Virtuális gépek hálózati kapcsolat szükséges. Ezt a követelményt támogatása érdekében az Azure-beli virtuális hálózathoz csatlakoztatni kívánt virtuális gépek van szüksége. 

Azure-beli virtuális hálózathoz egy logikai szerkezet, a fizikai az Azure hálózati háló épülnek. Minden logikai Azure virtuális hálózat el különítve a minden más Azure virtuális hálózatokkal. Elkülönítés segítségével biztosítja, hogy a központi telepítések a hálózati forgalom nem érhető el a más Microsoft Azure-ügyfelek.

További információ:

* [Azure-hálózat biztonsági áttekintése](security-network-overview.md)
* [A Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)
* [A hálózati szolgáltatásokkal és partneri kapcsolatoknak vonatkozó vállalati forgatókönyvek](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirend kezelése és jelentéskészítés

Az Azure Security Center segít megelőzését, észlelését és háríthatja el a fenyegetéseket. A Security Center lehetővé teszi, nagyobb betekintést és szabályozhatóbbá, az Azure-erőforrások biztonságát. Integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetések között. Segít a fenyegetések szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni.

A Security Center segítségével optimalizálásához, figyeléséhez, a virtuális gépek biztonságát:

* Így [biztonsági javaslatok](../security-center/security-center-recommendations.md) a virtuális gépek. Példa javaslatok: rendszerfrissítések alkalmazása, hozzáférés-vezérlési listák végpontok konfigurálása, kártevőirtó engedélyezéséhez, a hálózati biztonsági csoportok engedélyezése és a lemeztitkosítás alkalmazása.
* A virtuális gépek állapotának figyelését.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)
* [Az Azure Security Center – gyakori kérdések](../security-center/security-center-faq.md)
* [Az Azure Security Center tervezéséhez és](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Megfelelőség

Az Azure Virtual Machines a FISMA, a FedRAMP, a HIPAA, a PCI DSS Level 1 és más programok minősítésével megkapta a minősítést. A minősítési megkönnyíti a saját Azure-megfelelőségi követelmények teljesítése érdekében az alkalmazások és a cége számára hazai és nemzetközi szabályozások széles skáláját.

További információ:

* [A Microsoft adatvédelmi központ: megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance)
* [A megbízható felhő: Microsoft Azure biztonsági, adatvédelmi és megfelelőségi](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Bizalmas számítástechnika

Bizalmas nem része technikailag virtuális gép biztonsági számítástechnika, amíg a témakör a virtuális gép biztonsági "compute" biztonsági magasabb szintű tárgya tartozik. Bizalmas számítási "compute" biztonsági kategóriájába tartozik. 

Bizalmas számítási biztosítja, hogy "az a titkosítatlan" adatok esetén azért szükség a hatékony feldolgozás, az adatok védelmét egy megbízható végrehajtási környezetet https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE – más néven az enklávé), az alábbi ábrán látható egy példa .  

T-idomok győződjön meg arról, nem lehet adatokat vagy a művelet kívülről, még a hibakereső megtekintéséhez. Ezek még akkor is győződjön meg arról, hogy csak akkor engedélyezett, ha a kód számára engedélyezett hozzáférési adatokat. Ha a kódot módosítva vagy illetéktelen módosítás, a műveleteket a rendszer megtagadja, és a környezet le van tiltva. A TEE kikényszeríti a védelem a benne található kód végrehajtása során. 

További információ:

* [Introducing Azure bizalmas számítástechnika](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Az Azure bizalmas számítástechnika](https://azure.microsoft.com/blog/azure-confidential-computing/)  

