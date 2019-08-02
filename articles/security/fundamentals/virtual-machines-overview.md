---
title: Az Azure Virtual Machines szolgáltatással használt biztonsági funkciók – Azure Security | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure-Virtual Machines használható alapvető Azure-beli biztonsági funkciókról.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 186a0b0d4025f7d8a8888a9a0d2d0bd8832cf2d9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726497"
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

Az Azure-hoz készült Microsoft antimalware üzembe helyezése és engedélyezése esetén a következő alapvető funkciók érhetők el:

* **Valós idejű védelem**: Figyeli a Cloud Services és a Virtual Machines tevékenységeit a kártevők végrehajtásának észleléséhez és letiltásához.
* **Ütemezett vizsgálat**: A rendszeres időközönként a kártevők észlelése, beleértve az aktívan futó programokat is.
* **Kártevő-szervizelés**: A automatikusan végrehajtja az észlelt kártevők műveleteit, például a rosszindulatú fájlok törlését vagy karanténba helyezését, valamint a kártékony beállításjegyzék-bejegyzések tisztítását.
* **Aláírás-frissítések**: A automatikusan telepíti a legújabb védelmi aláírásokat (vírus-definíciókat), így biztosítva, hogy a védelem előre meghatározott gyakorisággal naprakész legyen.
* **Antimalware-motor frissítései**: Automatikusan frissíti az Azure Engine-hez készült Microsoft antimalware-t.
* **Kártevők elleni platform frissítései**: Automatikusan frissíti a Microsoft antimalware for Azure platformot.
* **Aktív védelem**: A az észlelt fenyegetésekkel és a gyanús erőforrásokkal kapcsolatos metaadatokat telemetria az Azure-ban a gyors reagálás érdekében. Lehetővé teszi a valós idejű szinkron aláírások továbbítását a Microsoft Active Protection-rendszeren (MAPS).
* **Példák**a jelentésekre: Mintákat biztosít a Microsoft antimalware for Azure szolgáltatás számára, és lehetővé teszi a szolgáltatások pontosítását és a hibaelhárítást.
* **Kizárások**: Lehetővé teszi, hogy az alkalmazás-és szolgáltatás-rendszergazdák bizonyos fájlokat, folyamatokat és meghajtókat konfiguráljanak, hogy kizárják őket a védelemből és a teljesítmény és egyéb okok miatt.
* **Antimalware-események gyűjteménye**: A kártevő szolgáltatás állapotát, a gyanús tevékenységeket, valamint az operációs rendszer eseménynaplójában végrehajtott szervizelési műveleteket rögzíti, és az Azure Storage-fiókban gyűjti azokat.

További információ az antimalware szoftverről a virtuális gépek védelméhez:

* [Microsoft antimalware az Azure Cloud Services és Virtual Machines](antimalware.md)
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

További információ:

* [Ismerkedés a WDATP](/windows/security/threat-protection/windows-defender-atp/get-started)  
* [A WDATP képességeinek áttekintése](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A kulcsfontosságú biztonság javítása növelheti a titkosítást és a hitelesítési védelmet. A kritikus fontosságú titkok és kulcsok felügyeletét és biztonságát leegyszerűsítheti, ha Azure Key Vault tárolja őket.

A Key Vault segítségével a kulcsokat a 2-es szintű FIPS 140-2 szabványnak megfelelő hardveres biztonsági modulokban (HSM) őrizheti. A biztonsági mentéshez vagy [transzparens](https://msdn.microsoft.com/library/bb934049.aspx) adattitkosításhoz használt SQL Server titkosítási kulcsainak mindegyike Key Vault tárolható az alkalmazásokból származó kulcsokkal vagy titkos kulcsok használatával. Az engedélyeket és a védett elemek elérését [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)kezelik.

További információ:

* [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Azure Key Vault blog](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemezének titkosítása

A Azure Disk Encryption egy új képesség a Windows és a Linux rendszerű virtuális gépek lemezének titkosítására. Azure Disk Encryption a Windows iparági szabványnak megfelelő [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) FUNKCIÓJÁT használja az operációs rendszer és az adatlemezek mennyiségi titkosításának biztosításához.

A megoldás integrálva van Azure Key Vault a Key Vault-előfizetésben lévő lemezes titkosítási kulcsok és titkos kódok szabályozásához és kezeléséhez. Biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatok titkosítva legyenek az Azure Storage-ban.

További információ:

* [IaaS virtuális gépek Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview)
* [Rövid útmutató: Windows IaaS virtuális gép titkosítása Azure PowerShell](../azure-disk-encryption-linux-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése

A Azure Backup méretezhető megoldás, amely segít az alkalmazásadatok nulla tőkebefektetéssel és minimális működési költségekkel szembeni védelme terén. Az alkalmazáshibák adatai sérülését okozhatják, az emberi hibák pedig az alkalmazások meghibásodásához vezethetnek. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek.

További információ:

* [Mi az az Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Azure Backup szolgáltatás – gyakori kérdések](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

A szervezet BCDR-stratégiájának fontos részét képezi, hogyan tarthatja naprakészen a vállalati munkaterheléseket és alkalmazásokat a tervezett és nem tervezett leállások esetén. Azure Site Recovery segíti a munkaterhelések és alkalmazások replikálásának, feladatátvételének és helyreállításának összehangolását, hogy azok másodlagos helyről is elérhetők legyenek, ha az elsődleges hely leáll.

Site Recovery:

* **Leegyszerűsíti a BCDR stratégiát**: A Site Recovery megkönnyíti a több üzleti számítási feladat és alkalmazás replikálásának, feladatátvételének és helyreállításának kezelését egyetlen helyről. Site Recovery összehangolja a replikációt és a feladatátvételt, de nem veszi fel az alkalmazásadatok adatait, vagy nem rendelkezik vele kapcsolatos információkkal.
* **Rugalmas replikációt biztosít**: Site Recovery használatával a Hyper-V virtuális gépeken, a VMware virtuális gépeken és a Windows-/Linux-alapú fizikai kiszolgálókon futó számítási feladatok replikálhatók.
* **Támogatja a feladatátvételt és a helyreállítást**: Site Recovery feladatátvételi teszteket biztosít a vész-helyreállítási gyakorlatok támogatásához anélkül, hogy ez hatással lenne az éles környezetekre. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. A feladatátvétel után visszatérhet az elsődleges helyekhez. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Kiküszöböli a másodlagos**adatközpontokat: A replikálást egy másodlagos helyszíni helyre vagy az Azure-ba végezheti el. Ha az Azure-t használja a vész-helyreállítási célhelyként, a másodlagos hely fenntartásának költségeit és összetettségét kizárja. A replikált adattárolók tárolása az Azure Storage szolgáltatásban történik.
* **Integrálható a meglévő BCDR**-technológiákkal: Site Recovery partnereket más alkalmazásokkal a BCDR szolgáltatásaival. A Site Recovery segítségével például megvédheti a vállalati munkaterhelések SQL Server háttérrendszer-végpontját. Ez magában foglalja a rendelkezésre állási csoportok feladatátvételének kezeléséhez szükséges SQL Server natív támogatását.

További információ:

* [Mi az Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hogyan működik Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Milyen számítási feladatokat véd a Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuális hálózat

A virtuális gépeknek hálózati kapcsolatra van szükségük. Ezen követelmény támogatásához az Azure megköveteli, hogy a virtuális gépek egy Azure-beli virtuális hálózathoz kapcsolódjanak.

Az Azure-beli virtuális hálózatok a fizikai Azure hálózati hálóra épülő logikai konstrukciók. Minden egyes logikai Azure-beli virtuális hálózat el van különítve az összes többi Azure-beli virtuális hálózattól. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma ne legyen elérhető más Microsoft Azure ügyfelek számára.

További információ:

* [Az Azure hálózati biztonság áttekintése](network-overview.md)
* [A Virtual Network áttekintése](/azure/virtual-network/virtual-networks-overview)
* [Hálózati funkciók és partnerkapcsolatok nagyvállalati forgatókönyvekhez](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés

Azure Security Center segít a fenyegetések megelőzésében, észlelésében és megválaszolásában. Security Center az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. Segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

A Security Center segítségével optimalizálhatja és figyelheti a virtuális gépek biztonságát:

* [Biztonsági javaslatok](/azure/security-center/security-center-recommendations) nyújtása a virtuális gépekhez. A javaslatok közé tartoznak például a rendszerfrissítések alkalmazása, az ACL-végpontok konfigurálása, az antimalware engedélyezése, a hálózati biztonsági csoportok engedélyezése és a lemez titkosításának alkalmazása.
* A virtuális gépek állapotának figyelése.

További információ:

* [Az Azure Security Center bemutatása](/azure/security-center/security-center-intro)
* [Azure Security Center gyakori kérdések](/azure/security-center/security-center-faq)
* [Azure Security Center tervezés és műveletek](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Megfelelőség

Az Azure Virtual Machines tanúsítvánnyal rendelkezik az FISMA, a FedRAMP, a HIPAA, a PCI DSS 1. és más kulcsfontosságú megfelelőségi programok számára. Ez a minősítés megkönnyíti a saját Azure-alkalmazásai számára a megfelelőségi követelmények teljesítését, és a vállalata számára a hazai és nemzetközi szabályozási követelmények széles körének kezelésére.

További információ:

* [Microsoft adatvédelmi központ: Betartásának](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Megbízható felhő: Microsoft Azure biztonság, adatvédelem és megfelelőség](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Bizalmas számítástechnika

Habár a bizalmas számítástechnika nem része a virtuális gépek biztonságának, a virtuális gép biztonságának témája a "számítási" biztonság magasabb szintű tárgya. A bizalmas számítástechnika a "számítási" biztonság kategóriába tartozik.

A bizalmas számítástechnika biztosítja, hogy ha az adatok "egyértelmű" formában vannak, és a hatékony feldolgozáshoz szükségesek, az adatok védelme megbízható végrehajtási környezetben https://en.wikipedia.org/wiki/Trusted_execution_environment (Tee – más néven enklávéban) történik, például az alábbi ábrán látható módon. .  

A pólók biztosítják, hogy az adatok vagy a műveletek kívülről, még hibakeresővel is megtekinthetők. Azt is biztosítják, hogy csak az engedélyezett kódok férhessenek hozzá az adateléréshez. Ha a kód megváltozott vagy módosítva van, a rendszer megtagadja a műveleteket, és letiltja a környezetet. A póló a kód végrehajtása során kikényszeríti ezeket a védelmet.

További információ:

* [Az Azure bizalmas számítástechnika bemutatása](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Azure bizalmas számítástechnika](https://azure.microsoft.com/blog/azure-confidential-computing/)  
