---
title: "Azure verem biztonsági vezérlők megértése |} Microsoft Docs"
description: "Szolgáltatás-rendszergazdaként Azure verem biztonsági vezérlők megismerése"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1d92f8f2ed9e8ab504afc65bab861e1f7bb3689
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="azure-stack-infrastructure-security-posture"></a>Az Azure verem infrastruktúra biztonságot

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Biztonsági szempontok és a megfelelőségi szabályzat közé tartoznak, a hibrid felhő használata fő illesztőprogramjait. Azure verem forgatókönyvek esetén úgy lett kialakítva, és a intézkedések már ismerni Azure verem elfogadásakor.

Azure-készletben az alábbi két egyszerre használható biztonsági előírások réteg. Az első réteget a Azure verem infrastruktúrában, ami a hardverösszetevők egészen akár az Azure erőforrás-kezelő ki, és a rendszergazda és a bérlői portál is magában foglalja. A második réteg bérlők létrehozni, telepíteni és kezelni a munkaterhelések áll, és magában foglalja többek között a virtuális gépek és alkalmazásszolgáltatások webhelyeket.  

## <a name="security-approach"></a>Biztonsági módszer
Azure verem úgy lett kialakítva, a biztonságot a modern fenyegetésekkel szembeni védelmét, és a a fő megfelelőségi szabványok követelményeinek teljesítéséhez lett létrehozva. Ennek eredményeképpen a biztonságot az Azure-verem infrastruktúra két oszlopok épül:

 - **Tegyük fel megszegésre kerül.**  
Azt feltételezi, hogy a rendszer már nem tettek kezdve összpontosítani *észlelésére, és a behatolás hatásának korlátozása* és a támadások megelőzése érdekében csak közben. 
 - **Alapértelmezés szerint megerősítve.**  
Az infrastruktúra futó jól meghatározott hardvereket és szoftvereket, mivel azt *engedélyezése, konfigurálása és ellenőrzése minden biztonsági szolgáltatás* alapértelmezés szerint.



Azure verem egy integrált rendszert, a rendszer, mert a biztonságot az Azure-verem infrastruktúra Microsoft határozzák meg. Csakúgy, mint az Azure bérlők felelősek meghatározása a bérlői terhelések biztonsági állapotát. Ez a dokumentum a biztonságot az Azure-verem infrastruktúra eligazodást Tudásbázis biztosít.

## <a name="data-at-rest-encryption"></a>Adatok inaktív adatok titkosítása
Minden Azure verem az infrastrukturális és bérlői adatok titkosítása a Bitlocker használatával. A titkosítási fizikai elvész vagy ellopják az Azure-verem tárolási összetevőinek véd. 

## <a name="data-in-transit-encryption"></a>Adatok adatátvitel közbeni titkosítás
A verem Azure infrastruktúra-összetevőihez kommunikálni a TLS 1.2 titkosított csatornákon keresztül. Titkosítási tanúsítványok önálló kezeli az infrastruktúra. 

Külső infrastrukturális végpontjai, például a REST-végpontok vagy a verem Azure portal TLS 1.2-es támogatja a biztonságos kommunikációhoz. Titkosítási tanúsítványok, a harmadik fél vagy a vállalati hitelesítésszolgáltató, ezekre a végpontokra meg kell adni. 

Önaláírt tanúsítványokat külső végpontokkal is használható, amíg a Microsoft nyomatékosan tesz, azok használatának mellőzését. 

## <a name="secret-management"></a>Titkos kezelése
Azure-verem infrastruktúra titkos adatait, például jelszavak, számos működéséhez használja. Legtöbbjük a program automatikusan elforgatja gyakran, mivel azok csoport által felügyelt szolgáltatásfiókok, amelyek elforgatása 24 óránként.

A fennmaradó titkos kulcsok, amelyek nincsenek csoport által felügyelt szolgáltatásfiókok forgatható el manuálisan a kiemelt végpont parancsfájllal.

## <a name="code-integrity"></a>A kódintegritás
Az Azure verem alkalmazza a legújabb Windows Server 2016 funkciókat. Az egyik legyen a Windows Defender Eszközvédelem, amely alkalmazások engedélyezése biztosít, és biztosítja, hogy csak hitelesített kód futtatása az Azure-verem infrastruktúráján belül. 

Microsoft vagy az OEM partner által aláírt engedélyezett kódot, és megtalálható a Microsoft által megadott házirend megadott engedélyezett szoftverek listáját. Ez azt jelenti csak az Azure-verem infrastruktúra futtatását jóváhagyott szoftverek hajtható végre. Jogosulatlan kódot próbál le van tiltva, és naplózási jön létre.

A Eszközvédelem házirend is megakadályozza, hogy külső ügynökeinek és szoftverének az Azure-verem infrastruktúrában működő.

## <a name="credential-guard"></a>Hitelesítőadat-emelés
Egy másik Windows Server 2016-os biztonsági szolgáltatás Azure verem a Windows Defender hitelesítőadat-őrfeltétellel az önmagukat Azure verem infrastruktúra hitelesítő adatokat a Pass-the-Hash és Pass-the-Ticket támadások elleni védelméhez használt.

## <a name="antimalware"></a>Kártevőirtó
A Windows Defender víruskereső minden összetevő Azure verem (Hyper-V-gazdagépek és virtuális gépek) védett.

## <a name="constrained-administration-model"></a>Korlátozott adminisztrációs modell
Azure verem felügyeleti szabályozza az egy adott célhoz három belépési pontok használatával: 
1. A [adminisztrációs portálhoz](azure-stack-manage-portals.md) pont kattintson élményt nyújt a napi felügyeleti műveleteket.
2. Az Azure Resource Manager tesz elérhetővé a felügyeleti portál, a REST API-n keresztül, PowerShell és az Azure CLI által használt összes felügyeleti műveletet. 
3. Az alacsony szintű műveleteket, például data center integrációs vagy forgatókönyvek támogatása, Azure verem közzétesz egy PowerShell-végpont nevű [kiemelt végpont](azure-stack-privileged-endpoint.md). Ehhez a végponthoz tesz elérhetővé, csak szerepel az engedélyezési listán parancsmagokat, és fokozottan ellenőrzi.

## <a name="network-controls"></a>Hálózati vezérlő
Hálózati hozzáférés-vezérlési List(ACL) többrétegű Azure verem infrastruktúra tartalmaz. A hozzáférés-vezérlési jogosulatlan hozzáférés elkerülése érdekében az infrastruktúra-összetevőihez, és csak az elérési utakat a működéséhez szükséges infrastruktúra kommunikációt korlátozza. 

Hálózati hozzáférés-vezérlési listákat a három réteg lépnek érvénybe:
1.  Állvány kapcsolók tetejéhez
2.  A szoftveralapú hálózati
3.  Gazdagép és virtuális gép operációs rendszer tűzfal 


