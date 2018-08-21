---
title: Azure Stack érvényesítési szolgáltatás hibaelhárítása |} A Microsoft Docs
description: Szolgáltatását az Azure Stack érvényesítési hibák elhárítása
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4372da2f1057c06761dd6abaf18c26df6e33640e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234950"
---
# <a name="troubleshoot-validation-as-a-service"></a>Érvényesítési szolgáltatás hibaelhárítása

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az alábbiakban gyakori problémák elhárítása és a megoldásaikat, egymástól független.

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>A portál megjeleníti a helyi ügynök hibakeresési módban

Ennek oka valószínűleg az ügynök nem tud szívverést küldeni a szolgáltatás egy nem stabil hálózati kapcsolat miatt nem sikerült. Szívverés küldi át 5 percenként. Ha a szolgáltatás nem kap szívverés 15 percig, a szolgáltatás az ügynök inaktívnak tekinti, és vizsgálatokat, már nem lesz ütemezve. A hibaüzenetben a *Agenthost.log* fájl a könyvtárban található, az ügynök elindítása.

> [!Note] 
> Már fut az ügynökön lévő bármely tesztet is tovább fut, a teszt vége előtt visszaállítva, de ha a szívverés nem, akkor az ügynök nem fogja tudni a vizsgálat állapotának frissítése vagy naplók feltöltése. A teszt mindig jelennek meg **futó** és kell szakítható meg.

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Az ügynök folyamata a gép le lett állítva, teszt végrehajtása közben. Mi várható?

Az ügynök folyamata leállt ungracefully például, ha a gép újraindul, leállított folyamat (CTRL + C billentyűkombinációt az ügynök ablakban tekinthető szabályos leállítást) a rajta futó vizsgálat folytatódik állapotúként **futó**. Ha az ügynök újraindítása után, akkor az ügynök frissül, hogy a teszt állapota **meg lett szakítva**. Ha nem indítja újra az ügynököt, akkor jelenik meg a teszt **futó** és manuálisan törölnie kell a teszt

> [!Note] 
> Ütemezett vizsgálatok egy munkafolyamaton belül egymás után futnak. **Függőben lévő** tesztek nem lekérése amíg a tesztek végrehajtása a **futó** állapot teljes ugyanabban a munkafolyamatban.

## <a name="handle-slow-network-connectivity"></a>Lassú hálózati kapcsolat kezeléséhez

Letöltheti a PIR kép megosztásra a helyi adatközpontban. És ezután már ellenőrizhető a lemezképet.

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Töltse le a PIR rendszerkép helyi megosztás esetén a lassú hálózati forgalom

1. Töltse le az AzCopy: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. AzCopy.zip csomagolja ki, és arra a könyvtárra, amelyben AzCopy.exe módosítása

3. Nyissa meg a Windows Powershellt egy rendszergazda jogú parancssorból. Futtassa az alábbi parancsot:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare, a megosztás elérési útját vagy a helyi elérési útja.

### <a name="verifying-pir-image-file-hash-value"></a>PIR lemezkép fájl kivonata érték ellenőrzés alatt áll

Használhat **Get-HashFile** parancsmaggal a kivonatolt érték beolvasható a letöltött nyilvános lemezképtárban képfájlok, a képek sértetlenségének ellenőrzéséhez.

| Fájlnév | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
