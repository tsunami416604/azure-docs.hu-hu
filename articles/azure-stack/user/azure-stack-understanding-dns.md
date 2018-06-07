---
title: Azure-készletben ismertetése IDN |} Microsoft Docs
description: Az IDN-szolgáltatásokat és képességeket Azure verem ismertetése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 9123160f42adea57c28dff265bd5b5dbbcbb7918
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724258"
---
# <a name="introducing-idns-for-azure-stack"></a>Azure verem IDN bemutatása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

IDN az Azure verem hálózati szolgáltatása: lehetővé teszi a külső DNS-nevek feloldására (például http://www.bing.com.) is lehetővé teszi a belső virtuális hálózati nevek regisztrálni. Ennek végrehajtásával oldhatja meg virtuális gépek azonos virtuális hálózaton IP-címet, hanem nevét. Ez a megközelítés eltávolítja a kell megadnia az egyéni DNS-kiszolgálóbejegyzéseik. DNS szolgáltatással kapcsolatos további információkért lásd: a [Azure DNS áttekintése](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Mire IDN?

IDN Azure-készletben akkor az alábbi képességeket egyéni DNS-kiszolgálóbejegyzéseik megadása nélkül beolvasása:

- Megosztott DNS névfeloldási szolgáltatást bérlői munkaterheléseket.
- A névfeloldás és a DNS-regisztráció a bérlői virtuális hálózaton belül mérvadó DNS-szolgáltatás.
- A rekurzív DNS-szolgáltatás a bérlői virtuális gépek Internet-nevek feloldását. Bérlők már nem kell adnia az egyéni DNS-bejegyzések Internet névfeloldás (például www.bing.com.)

Továbbra is a saját DNS állapotba, és egyéni DNS-kiszolgálók használata. Azonban IDN formátumú tartománynevek használatával oldható fel internetes DNS-nevek, és az azonos virtuális hálózatban lévő más virtuális gépek csatlakozni, nem kell egyéni DNS-bejegyzéseket létrehozni.

## <a name="what-doesnt-idns-do"></a>Mit nem IDN?

A rendszer milyen IDN nem teszi lehetővé a teendő, hozzon létre egy DNS-rekordot a nevet, amely a virtuális hálózaton kívül feloldhatók legyenek.

Az Azure lehetősége van egy DNS-névcímke társított egy nyilvános IP-cím megadásával. Választhat, hogy a címke (előtag), de Azure úgy dönt, az a régió, ahol a nyilvános IP-cím létrehozása alapuló előtagot.

![A DNS-névcímke – példa](media/azure-stack-understanding-dns-in-tp2/image3.png)

Ahogy az előző ábrán látható, Azure létrehoz egy "A" rekord a DNS-ben a DNS-névcímke, a zóna alatt megadott **westus.cloudapp.azure.com**. Az előtag és az utótag együttes írása egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), amely feloldható a bárhol a nyilvános interneten.

Az Azure verem csak támogatja belső névregisztráció, IDN, így nem tegye a következőket:

- Egy meglévő üzemeltetett DNS-zóna (például local.azurestack.external.) a DNS-rekord létrehozása
- Hozzon létre egy DNS-zóna (például Contoso.com.)
- A saját egyéni DNS-zóna rekordot kell létrehozni.
- A tartománynevek vásárlásának támogatja.

## <a name="next-steps"></a>További lépések

[Az Azure-készletben a DNS-sel](azure-stack-dns.md)
