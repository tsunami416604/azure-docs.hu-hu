---
title: "Az Azure-készletben DNS ismertetése |} Microsoft Docs"
description: "DNS-szolgáltatásokat és képességeket Azure verem ismertetése"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 8c023eda179ace41a082bf4a4fadc281c14db7ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-idns-for-azure-stack"></a>Azure verem IDN bemutatása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

IDN egy olyan szolgáltatás, amely lehetővé teszi, hogy a külső DNS-nevek (pl. http://www.bing.com) Azure-készletben.
Lehetővé teszi a belső virtuális hálózati nevek regisztrálni. Ennek végrehajtásával oldhatja virtuális gépek azonos virtuális hálózaton IP-címet, hanem nevét egyéni DNS-kiszolgálóbejegyzéseik megadása nélkül.

Amelyet mindig az, hogy létezik az Azure-ban, de akkor érhető el a Windows Server 2016 és Azure verem túl.

## <a name="what-does-idns-do"></a>Mire IDN?
IDN Azure-készletben akkor az alábbi képességeket egyéni DNS-kiszolgálóbejegyzéseik megadása nélkül beolvasása.

* Megosztott DNS névfeloldási szolgáltatást bérlői munkaterheléseket.
* A névfeloldás és a DNS-regisztráció a bérlői virtuális hálózaton belül mérvadó DNS-szolgáltatás.
* A rekurzív DNS-szolgáltatás a bérlői virtuális gépek Internet-nevek feloldását. Bérlők már nem kell Internet névfeloldás (például www.bing.com) egyéni DNS-bejegyzéseit adja meg.

Kapcsolja a saját DNS is, és egyéni DNS-kiszolgálók használata, ha azt szeretné. Azonban, ha most kívánja kell tudni oldania az internetes DNS-nevek, és az azonos virtuális hálózatban lévő másik virtuális gép csatlakozni, adja meg, semmit nem kell, és csak akkor fog működni.

## <a name="what-does-idns-not-do"></a>Mire IDN nem?
Milyen IDN nem teszi lehetővé a teendő, hozzon létre egy DNS-rekordot a nevet, amely a virtuális hálózaton kívül feloldhatók legyenek.

Az Azure lehetősége van egy DNS-névcímke, amely társítva van egy nyilvános IP-cím megadásával. Választhat, hogy a címke (előtag), de Azure úgy dönt, az a régió, ahol a nyilvános IP-cím létrehozása alapuló előtagot.

![Képernyőfelvétel a DNS-névcímke](media/azure-stack-understanding-dns-in-tp2/image3.png)

A fenti kép Azure létrehoz egy "A" rekord a DNS-ben a DNS-névcímke, a zóna alatt megadott **westus.cloudapp.azure.com**. Az előtag és a együtt utótag állítható össze a teljes tartomány neve (FQDN), amelyeket bárhol lehet feloldani a nyilvános interneten.

Az Azure verem csak támogatja belső névregisztráció, IDN, így nem tegye a következőket.

* Hozzon létre egy meglévő üzemeltetett DNS-zóna (például local.azurestack.external) a DNS-rekord.
* Hozzon létre egy DNS-zóna (például Contoso.com).
* A saját egyéni DNS-zóna rekordot kell létrehozni.
* A tartománynevek vásárlásának támogatja.

