---
title: Az Azure Virtual Machines szolgáltatással használt biztonsági funkciók – Azure Security | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure-Virtual Machines használható alapvető Azure-beli biztonsági funkciókról.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: 6c01df071f263f7080f6c89b539d9a40aeff282f
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792614"
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure Virtual Machines biztonsági áttekintése
Ez a cikk áttekintést nyújt a virtuális gépekkel használható alapvető Azure-beli biztonsági funkciókról.

Az Azure Virtual Machines számos számítástechnikai megoldás üzembe helyezését teszi lehetővé agilis módon. A szolgáltatás támogatja a Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP és Azure BizTalk Services használatát. Így szinte bármilyen operációs rendszeren üzembe helyezheti a számítási feladatokat és bármely nyelvet.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. Az alkalmazásait felépítheti és üzembe helyezheti azzal a garanciával, hogy az Ön adatai védettek és biztonságosak a nagyon biztonságos adatközpontokban.

Az Azure-ban olyan fokozott biztonságú, megfelelő megoldásokat hozhat létre, amelyek:

* A virtuális gépeket vírusok és kártevő szoftverek védik.
* Titkosítsa a bizalmas adatokat.
* Biztonságos hálózati forgalom.
* Fenyegetések azonosítása és észlelése.
* Megfeleljen a megfelelőségi követelményeknek.  

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ban olyan biztonsági gyártóktól származó kártevő szoftvereket használhat, mint a Microsoft, a Symantec, a Trend Micro és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben.

A Microsoft antimalware for Azure Cloud Services és Virtual Machines egy valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket.  Az Azure-hoz készült Microsoft antimalware konfigurálható riasztásokat biztosít, ha az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken.

Az Azure-hoz készült Microsoft antimalware egyetlen ügynökből álló megoldás az alkalmazások és a bérlői környezetek számára. Úgy tervezték, hogy az emberi beavatkozás nélkül fusson a háttérben. A védelmet az alkalmazás számítási feladatainak igényei szerint helyezheti üzembe, amely alapszintű biztonsági vagy speciális konfigurációval rendelkezik, beleértve a kártevő szoftverek figyelését is.

Tudjon meg többet az Azure-hoz készült [Microsoft antimalware](antimalware.md) szolgáltatásról és a rendelkezésre álló alapvető funkciókról.

További információ az antimalware szoftverről a virtuális gépek védelméhez:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Trend Micro Deep Security telepítése és konfigurálása Windows rendszerű virtuális gépen](/azure/virtual-machines/windows/classic/install-trend)
* [Symantec-Endpoint Protection telepítése és konfigurálása Windowsos virtuális gépen](/azure/virtual-machines/windows/classic/install-symantec)
* [Biztonsági megoldások az Azure piactéren](https://azure.microsoft.com/marketplace/?term=security)

A fokozottan hatékony védelem érdekében érdemes lehet a [Windows Defender komplex veszélyforrások elleni védelmet](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)használni. A Windows Defender ATP-vel a következőket kapja:

* [Támadási felület csökkentése](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Következő generációs védelem](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Endpoint Protection és Response](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizált vizsgálat és szervizelés](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Biztonságos pontszám](/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Speciális vadászat](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Felügyelet és API-k](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Részletek:

* [Ismerkedés a WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [A WDATP képességeinek áttekintése](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A kulcsfontosságú biztonság javítása növelheti a titkosítást és a hitelesítési védelmet. A kritikus fontosságú titkok és kulcsok felügyeletét és biztonságát leegyszerűsítheti, ha Azure Key Vault tárolja őket.

A Key Vault segítségével a kulcsokat a 2-es szintű FIPS 140-2 szabványnak megfelelő hardveres biztonsági modulokban (HSM) őrizheti. A biztonsági mentéshez vagy [transzparens adattitkosításhoz](https://msdn.microsoft.com/library/bb934049.aspx) használt SQL Server titkosítási kulcsainak mindegyike Key Vault tárolható az alkalmazásokból származó kulcsokkal vagy titkos kulcsok használatával. Az engedélyeket és a védett elemek elérését [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)kezelik.

Részletek:

* [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Azure Key Vault blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemezének titkosítása

A Azure Disk Encryption egy új képesség a Windows és a Linux rendszerű virtuális gépek lemezének titkosítására. Azure Disk Encryption a Windows iparági szabványnak megfelelő [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) FUNKCIÓJÁT használja az operációs rendszer és az adatlemezek mennyiségi titkosításának biztosításához.

A megoldás integrálva van Azure Key Vault a Key Vault-előfizetésben lévő lemezes titkosítási kulcsok és titkos kódok szabályozásához és kezeléséhez. Biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az Azure Storage-ban.

Részletek:

* [IaaS virtuális gépek Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview)
* [Gyors útmutató: Windows IaaS virtuális gép titkosítása Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése

A Azure Backup méretezhető megoldás, amely segít az alkalmazásadatok nulla tőkebefektetéssel és minimális működési költségekkel szembeni védelme terén. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek.

Részletek:

* [Mi az az Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Azure Backup szolgáltatás – gyakori kérdések](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

A szervezet BCDR-stratégiájának fontos részét képezi, hogyan tarthatja naprakészen a vállalati munkaterheléseket és alkalmazásokat a tervezett és nem tervezett leállások esetén. Azure Site Recovery segíti a munkaterhelések és alkalmazások replikálásának, feladatátvételének és helyreállításának összehangolását, hogy azok másodlagos helyről is elérhetők legyenek, ha az elsődleges hely leáll.

Site Recovery:

* **Egyszerűsíti a BCDR stratégiát: a**site Recovery megkönnyíti a több üzleti számítási feladat és alkalmazás replikálásának, feladatátvételének és helyreállításának kezelését egyetlen helyről. Site Recovery összehangolja a replikációt és a feladatátvételt, de nem veszi fel az alkalmazásadatok adatait, vagy nem rendelkezik vele kapcsolatos információkkal.
* **Rugalmas replikációt biztosít**: site Recovery használatával replikálhatja a Hyper-V virtuális gépeken, a VMWare virtuális gépeken és a Windows/Linux rendszerű fizikai kiszolgálókon futó számítási feladatokat.
* **Támogatja a feladatátvételt és a helyreállítást**: a site Recovery teszt feladatátvételeket biztosít a vész-helyreállítási gyakorlatok támogatásához anélkül, hogy ez hatással lenne az éles környezetekre. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel után visszatérhet az elsődleges helyekhez. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Másodlagos adatközpontok**kihagyása: replikálhat egy másodlagos helyszíni helyre vagy az Azure-ba. Ha az Azure-t használja a vész-helyreállítási célhelyként, a másodlagos hely fenntartásának költségeit és összetettségét kizárja. A replikált adattárolók tárolása az Azure Storage szolgáltatásban történik.
* **Integrálható a meglévő BCDR-technológiákkal**: site Recovery partnereket más alkalmazásokkal, BCDR funkciókkal. A Site Recovery segítségével például megvédheti a vállalati munkaterhelések SQL Server háttérrendszer-végpontját. Ez magában foglalja a rendelkezésre állási csoportok feladatátvételének kezeléséhez szükséges SQL Server natív támogatását.

Részletek:

* [Mi az Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hogyan működik Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Milyen számítási feladatokat véd a Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuális hálózat

A virtuális gépeknek hálózati kapcsolatra van szükségük. Ezen követelmény támogatásához az Azure megköveteli, hogy a virtuális gépek egy Azure-beli virtuális hálózathoz kapcsolódjanak.

Az Azure-beli virtuális hálózatok a fizikai Azure hálózati hálóra épülő logikai konstrukciók. Minden egyes logikai Azure-beli virtuális hálózat el van különítve az összes többi Azure-beli virtuális hálózattól. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma ne legyen elérhető más Microsoft Azure ügyfelek számára.

Részletek:

* [Az Azure hálózati biztonság áttekintése](network-overview.md)
* [A Virtual Network áttekintése](/azure/virtual-network/virtual-networks-overview)
* [Hálózati funkciók és partnerkapcsolatok nagyvállalati forgatókönyvekhez](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés

Azure Security Center segít a fenyegetések megelőzésében, észlelésében és megválaszolásában. Security Center az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. Segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

A Security Center segítségével optimalizálhatja és figyelheti a virtuális gépek biztonságát:

* [Biztonsági javaslatok](/azure/security-center/security-center-recommendations) nyújtása a virtuális gépekhez. A javaslatok közé tartoznak például a rendszerfrissítések alkalmazása, az ACL-végpontok konfigurálása, az antimalware engedélyezése, a hálózati biztonsági csoportok engedélyezése és a lemez titkosításának alkalmazása.
* A virtuális gépek állapotának figyelése.

Részletek:

* [Az Azure Security Center bemutatása](/azure/security-center/security-center-intro)
* [Azure Security Center gyakori kérdések](/azure/security-center/security-center-faq)
* [Azure Security Center tervezés és műveletek](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Megfelelőség

Az Azure Virtual Machines tanúsítvánnyal rendelkezik az FISMA, a FedRAMP, a HIPAA, a PCI DSS 1. és más kulcsfontosságú megfelelőségi programok számára. Ez a minősítés megkönnyíti a saját Azure-alkalmazásai számára a megfelelőségi követelmények teljesítését, és a vállalata számára a hazai és nemzetközi szabályozási követelmények széles körének kezelésére.

Részletek:

* [Microsoft adatvédelmi központ: megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Megbízható felhő: Microsoft Azure biztonság, adatvédelem és megfelelőség](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Bizalmas számítástechnika

Habár a bizalmas számítástechnika nem része a virtuális gépek biztonságának, a virtuális gép biztonságának témája a "számítási" biztonság magasabb szintű tárgya. A bizalmas számítástechnika a "számítási" biztonság kategóriába tartozik.

A bizalmas számítástechnika biztosítja, hogy ha az adatok "egyértelmű" formában vannak megkövetelve a hatékony feldolgozáshoz, az adatok védelme megbízható végrehajtási környezetben történik https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE-ként is ismert), például az alábbi ábrán látható.  

A pólók biztosítják, hogy az adatok vagy a műveletek kívülről, még hibakeresővel is megtekinthetők. Azt is biztosítják, hogy csak az engedélyezett kódok férhessenek hozzá az adateléréshez. Ha a kód megváltozott vagy módosítva van, a rendszer megtagadja a műveleteket, és letiltja a környezetet. A póló a kód végrehajtása során kikényszeríti ezeket a védelmet.

Részletek:

* [Az Azure bizalmas számítástechnika bemutatása](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure bizalmas számítástechnika](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Következő lépések

A virtuális gépek és operációs rendszerek [biztonsági eljárásainak](iaas.md) megismerése.
