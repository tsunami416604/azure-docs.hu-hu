---
title: Internetes tartomány Traffic Manager – Azure Traffic Manager
description: Ez a cikk segít átirányítani a vállalata tartománynevét egy Traffic Manager szolgáltatásbeli tartománynevére.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: 93a8076f12b8f006d600cbd32ce39169f4b47c67
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89392595"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Vállalati internetes tartomány átirányítása Azure Traffic Manager-tartományra

Amikor Traffic Manager-profilt hoz létre, az Azure automatikusan hozzárendel egy DNS-nevet a profilhoz. A DNS-zónájából való név felhasználásához hozzon létre egy CNAME DNS-rekordot, amely leképezi a Traffic Manager-profiljának tartománynevét. A Traffic Manager tartományneve a Traffic Manager-profil Konfiguráció lapjának **Általános** részében tekinthető meg.

Ha például a `www.contoso.com` nevet szeretné a Traffic Manager `contoso.trafficmanager.net` DNS-neve felé irányítani, az alábbi DNS-erőforrásrekordot kell létrehoznia:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

A *www- \. contoso.com* irányuló összes forgalmi kérelem a *contoso.trafficmanager.net*.

> [!IMPORTANT]
> A második szintű tartomány, például a *contoso.com*, nem irányítható á Traffic Manager-tartományra. A DNS-protokollszabványok nem engedélyezik a CNAME-rekordokat a másodlagos szintű tartománynevek esetében.

## <a name="next-steps"></a>Következő lépések

* [A Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)
* [Traffic Manager – profil letiltása, engedélyezése vagy törlése](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Végpont letiltása vagy engedélyezése](disable-or-enable-an-endpoint.md)
