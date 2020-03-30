---
title: Az Azure virtuális gépeivel használt biztonsági funkciók
titleSuffix: Azure security
description: Ez a cikk áttekintést nyújt az Azure virtuális gépekkel használható alapvető Azure biztonsági funkciókról.
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
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657163"
---
# <a name="azure-virtual-machines-security-overview"></a>Az Azure Virtuális gépek biztonsága – áttekintés
Ez a cikk áttekintést nyújt az Azure virtuális gépekkel használható alapvető biztonsági funkcióiról.

Az Azure virtuális gépek segítségével agilis módon üzembe helyezhetszámítástechnikai megoldások széles körét. A szolgáltatás támogatja a Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP és Azure BizTalk szolgáltatásokat. Így bármilyen számítási feladatot és nyelvet telepíthet szinte bármilyen operációs rendszeren.

Az Azure virtuális gépek anélkül biztosítják a virtualizálás rugalmasságát, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. Az alkalmazásokat azzal a biztosítékkal hozhat létre és telepítheti, hogy adatai védettek és biztonságosak a rendkívül biztonságos adatközpontokban.

Az Azure-ral olyan, fokozott biztonságú, megfelelő megoldásokat hozhat létre, amelyek:

* Védje meg virtuális gépeit a vírusoktól és a rosszindulatú programoktól.
* Titkosítsa a bizalmas adatokat.
* Biztonságos hálózati forgalom.
* A fenyegetések azonosítása és észlelése.
* Feleljen meg a megfelelőségi követelményeknek.  

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ral olyan biztonsági gyártók tól származó kártevőirtó szoftvereket használhat, mint a Microsoft, a Symantec, a Trend Micro és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a rosszindulatú fájloktól, adware-től és más fenyegetésektől.

A Microsoft Antimalware for Azure Cloud Services and Virtual Machines egy valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb rosszindulatú szoftvereket.  Az Azure-hoz készült Microsoft Antimalware konfigurálható riasztásokat biztosít, ha ismert rosszindulatú vagy nemkívánatos szoftverek próbálnak saját magát telepíteni vagy futtatni az Azure-rendszereken.

A Microsoft Antimalware for Azure együgynökes megoldás alkalmazások hoz és a bérlői környezetekben. Úgy tervezték, hogy emberi beavatkozás nélkül fusson a háttérben. Az alkalmazásszámítási feladatok igényei alapján telepítheti a védelmet, akár alapszintű biztonságos, akár speciális egyéni konfigurációval, beleértve a kártevőirtó figyelést is.

További információ az [Azure-hoz való Microsoft Antimalware-ről](antimalware.md) és az elérhető alapvető funkciókról.

További információ a virtuális gépek védelmét szolgáló kártevőirtó szoftverekről:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Trend Micro Deep Security telepítése és konfigurálása szolgáltatásként Windows virtuális gépen](/azure/virtual-machines/windows/classic/install-trend)
* [A Symantec Endpoint Protection telepítése és konfigurálása Windows virtuális gépen](/azure/virtual-machines/windows/classic/install-symantec)
* [Biztonsági megoldások az Azure Piactéren](https://azure.microsoft.com/marketplace/?term=security)

A még hatékonyabb védelem érdekében fontolja meg a [Windows Defender komplex veszélyforrások elleni védelem használatát.](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection) A Windows Defender ATP segítségével a következőket kapja:

* [A támadási felület csökkentése](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Új generációs védelem](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Végpontok védelme és válasz](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Automatizált vizsgálat és kármentesítés](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Biztonságos pontszám](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [Fejlett vadászat](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Kezelés és API-k](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [A Microsoft veszélyforrások elleni védelme](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

További információ:

* [A WDATP – első lépések](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [A WDATP képességeinek áttekintése](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Hardveres biztonsági modul

A kulcsbiztonság javítása javíthatja a titkosítást és a hitelesítési védelmet. Egyszerűsítheti a kritikus fontosságú titkos kulcsok és kulcsok felügyeletét és biztonságát az Azure Key Vaultban való tárolásukkal.

A Key Vault lehetővé teszi a kulcsok tárolását a FIPS 140-2 Level 2 szabványoknak megfelelő hardveres biztonsági modulokban (HSM). Az SQL Server titkosítási kulcsai a biztonsági mentéshez vagy [az átlátszó adattitkosításhoz](https://msdn.microsoft.com/library/bb934049.aspx) mind tárolhatók a Key Vaultban az alkalmazások bármely kulcsával vagy titkával. A védett elemekhez való engedélyek és hozzáférés kezelése az [Azure Active Directoryn](https://azure.microsoft.com/documentation/services/active-directory/)keresztül történik.

További információ:

* [Mi az Azure Key Vault?](/azure/key-vault/key-vault-overview)
* [Az Azure Key Vault blogja](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Virtuális gép lemeztitkosítása

Az Azure Disk Encryption egy új képesség a Windows és linuxos virtuálisgép-lemezek titkosítására. Az Azure Disk Encryption a Windows szabványos [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) szolgáltatását és a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatását használja az operációs rendszer és az adatlemezek kötettitkosításának biztosításához.

A megoldás integrálva van az Azure Key Vault szolgáltatással, amely ekkel vezérelheti és kezelheti a kulcstároló-előfizetés lemeztitkosítási kulcsait és titkos kulcsait. Biztosítja, hogy a virtuális gép lemezein lévő összes adat inkultatra titkosítva legyen az Azure Storage-ban.

További információ:

* [Azure lemeztitkosítás iaas virtuális gépekhez](/azure/security/azure-security-disk-encryption-overview)
* [Rövid útmutató: Windows IaaS virtuális gép titkosítása az Azure PowerShell használatával](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése

Az Azure Backup egy méretezhető megoldás, amely segít megvédeni az alkalmazásadatait nulla tőkebefektetéssel és minimális működési költségekkel. Az alkalmazáshibák megsérülhetnek az adatokban, és az emberi hibák hibákat okozhatnak az alkalmazásokban. Az Azure Backup segítségével a Windows és Linux rendszert futtató virtuális gépek védettek.

További információ:

* [Mi az az Azure Backup?](/azure/backup/backup-introduction-to-azure-backup)
* [Gyakori kérdések az Azure Backup szolgáltatásról](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

A szervezet BCDR-stratégiájának fontos része, hogy kitalálja, hogyan tarthatja a vállalati munkaterheléseket és alkalmazásokat, ha tervezett és nem tervezett kimaradások lépnek fel. Az Azure Site Recovery segít a számítási feladatok és alkalmazások replikációjának, feladatátvételének és helyreállításának koordinálásában, hogy azok egy másodlagos helyről is elérhetők, ha az elsődleges hely leáll.

Hely helyreállítása:

* **Leegyszerűsíti a BCDR-stratégiát:** A Site Recovery megkönnyíti több üzleti számítási feladat és alkalmazás egyetlen helyről történő helyreállítását. A Site Recovery vezényli a replikációt és a feladatátvételt, de nem fogja el az alkalmazásadatait, és nem rendelkezik semmilyen információval.
* **Rugalmas replikációt biztosít:** A Site Recovery használatával replikálhatja a Hyper-V virtuális gépeken, a VMware virtuális gépeken és a Windows/Linux fizikai kiszolgálókon futó számítási feladatokat.
* **Feladatátvétel és helyreállítás támogatása:** A Site Recovery tesztfeladat-átvételeket biztosít a vész-helyreállítási gyakorlatok támogatásához az éles környezetek befolyásolása nélkül. Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. Feladatátvétel után, akkor a feladat-vissza az elsődleges helyekre. A Site Recovery olyan helyreállítási terveket biztosít, amelyek parancsfájlokat és Azure Automation-munkafüzeteket tartalmazhatnak, így testre szabhatja a többrétegű alkalmazások feladatátvételét és helyreállítását.
* **Másodlagos adatközpontok kiküszöbölése:** Replikálhatja egy másodlagos helyszíni hely, vagy az Azure-ba. Az Azure vész-helyreállítási célként való használata kiküszöböli a másodlagos hely karbantartásának költségeit és összetettségét. A replikált adatok at az Azure Storage tárolja.
* **Integrálható a meglévő BCDR technológiákkal:** A Site Recovery partnerek más alkalmazások BCDR szolgáltatásaival. A Site Recovery segítségével például megvédheti az SQL Server biztonsági részét a vállalati munkaterhelések. Ez magában foglalja az SQL Server Always On natív támogatását a rendelkezésre állási csoportok feladatátvételének kezeléséhez.

További információ:

* [Mi az Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Hogy működik az Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Milyen számítási feladatokat véd az Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Virtuális hálózat

A virtuális gépeknek hálózati kapcsolatra van szükségük. Ennek a követelménynek a támogatásához az Azure-nak virtuális gépeket kell csatlakoztatnia egy Azure virtuális hálózathoz.

Az Azure virtuális hálózat egy logikai konstrukció, amely a fizikai Azure hálózati hálóra épül. Minden logikai Azure virtuális hálózat el van különítve az összes többi Azure virtuális hálózatok. Ez az elkülönítés segít biztosítani, hogy a központi telepítések hálózati forgalma nem érhető el más Microsoft Azure-ügyfelek számára.

További információ:

* [Az Azure hálózati biztonságának áttekintése](network-overview.md)
* [Virtuális hálózat – áttekintés](/azure/virtual-network/virtual-networks-overview)
* [Hálózati funkciók és partnerségek vállalati forgatókönyvekhez](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Biztonsági házirend kezelése és jelentése

Az Azure Security Center lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. A Security Center nagyobb betekintést nyújt az Azure-erőforrások biztonságába, és annak szabályozását is. Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben. Segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

A Security Center a következők segítségével optimalizálhatja és figyelheti a virtuális gépek biztonságát:

* Biztonsági [javaslatok biztosítása](/azure/security-center/security-center-recommendations) a virtuális gépekhez. Példa javaslatok: rendszerfrissítések alkalmazása, ACL-végpontok konfigurálása, kártevőirtó engedélyezése, hálózati biztonsági csoportok engedélyezése és lemeztitkosítás alkalmazása.
* A virtuális gépek állapotának figyelése.

További információ:

* [Az Azure Security Center bemutatása](/azure/security-center/security-center-intro)
* [Az Azure Security Center gyakori kérdései](/azure/security-center/security-center-faq)
* [Az Azure Security Center tervezése és műveletei](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Megfelelőség

Az Azure Virtual Machines fisma, FedRAMP, HIPAA, PCI DSS Level 1 és más kulcsfontosságú megfelelőségi programok minősítéssel rendelkezik. Ez a tanúsítvány megkönnyíti a saját Azure-alkalmazások megfelelőségi követelményeknek való megfelelést, és a vállalkozás számára, hogy megfeleljen a hazai és nemzetközi szabályozási követelmények széles körének.

További információ:

* [Microsoft Adatvédelmi központ: Megfelelőség](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Megbízható felhő: Microsoft Azure biztonság, adatvédelem és megfelelőség](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Bizalmas számítástechnika

Bár a bizalmas számítástechnika technikailag nem része a virtuális gép biztonságának, a virtuális gép biztonságának témaköre a "számítási" biztonság magasabb szintű témájához tartozik. A bizalmas számítástechnika a "számítási" biztonság kategóriájába tartozik.

A bizalmas számítástechnika biztosítja, hogy amikor az adatok "tiszta", ami a hatékony feldolgozáshoz https://en.wikipedia.org/wiki/Trusted_execution_environment szükséges, az adatok védettek legyenek egy megbízható végrehajtási környezetben (TEE - más néven enklávé), amelynek példája az alábbi ábrán látható.  

A pólók biztosítják, hogy az adatokat vagy a belső műveleteket kívülről, még egy hibakeresővel sem lehet megtekinteni. Még azt is biztosítják, hogy csak az engedélyezett kód férhet hozzá az adatokhoz. Ha a kódot módosítják vagy módosítják, a műveletek et a program elutasítja, és a környezet le van tiltva. A TEE ezeket a védelmet a kód végrehajtása során érvényesíti.

További információ:

* [Az Azure bizalmas számítástechnikai bemutatkozása](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/)  
* [Az Azure bizalmas számítástechnikai szolgáltatása](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>További lépések

Ismerje meg a virtuális gépek és az operációs rendszerek [biztonsági gyakorlati tanácsait.](iaas.md)
